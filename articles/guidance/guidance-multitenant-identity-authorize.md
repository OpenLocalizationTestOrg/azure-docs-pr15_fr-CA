<properties
   pageTitle="Autorisation dans les applications mutualisées | Microsoft Azure"
   description="Comment faire pour exécuter une autorisation dans une application partagée"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="role-based-and-resource-based-authorization-in-multitenant-applications"></a>Autorisation basée sur les rôles et basée sur les ressources dans les applications mutualisées

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article fait [partie d’une série]. Il existe également un [exemple d’application] complète qui accompagne cette série.

Notre [implémentation de référence] est une application ASP.NET Core 1.0. Dans cet article nous examinerons deux approches générales pour l’autorisation, à l’aide de l’API fournies dans ASP.NET Core 1.0 d’autorisation.

-   **Autorisation basée sur les rôles**. Autoriser une action basée sur les rôles attribués à un utilisateur. Par exemple, certaines actions nécessitent un rôle d’administrateur.
-   **Autorisation basée sur les ressources**. Autoriser une action basée sur une ressource particulière. Par exemple, chaque ressource a un propriétaire. Le propriétaire peut supprimer la ressource ; autres utilisateurs ne peuvent pas.

Une application classique emploie un mélange des deux. Par exemple, pour supprimer une ressource, l’utilisateur doit être la ressource propriétaire _ou_ un administrateur.


## <a name="role-based-authorization"></a>Autorisation basée sur les rôles

Les [Enquêtes de Tailspin] [ Tailspin] application définit les rôles suivants :

- Administrateur. Peut effectuer toutes les opérations CRUD sur toute enquête appartenant à ce client.
- Créateur. Peut créer de nouvelles enquêtes
- Lecteur. Peut lire des enquêtes qui appartiennent à ce client.

Rôles s’appliquent aux _utilisateurs_ de l’application. Dans l’application d’enquêtes, un utilisateur est un administrateur, créateur ou lecteur.

Pour une discussion sur la manière de définir et de gérer des rôles, consultez [rôles d’Application].

Quelle que soit la façon dont vous gérez les rôles, votre code d’autorisation est similaire. Noyau d’ASP.NET 1.0 introduit une abstraction que l'on appelée des [stratégies d’autorisation des][policies]. Grâce à cette fonctionnalité, vous définissez des stratégies d’autorisation dans le code et ensuite appliquez ces stratégies à des actions de contrôleur. La stratégie est dissociée du contrôleur.

### <a name="create-policies"></a>Créer des stratégies

Pour définir une stratégie, commencez par créer une classe qui implémente `IAuthorizationRequirement`. Il est plus facile de dériver à partir de `AuthorizationHandler`. Dans le `Handle` méthode, examinez les claim(s) pertinentes.

Voici un exemple de l’application Tailspin enquêtes :

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Voir [SurveyCreatorRequirement.cs]

Cette classe définit la spécification d’un utilisateur à créer une nouvelle enquête. L’utilisateur doit être dans le rôle de SurveyAdmin ou SurveyCreator.

Dans votre classe de démarrage, définir une stratégie nommée qui comprend une ou plusieurs spécifications. S’il existe plusieurs conditions, l’utilisateur doit respecter _toutes_ les exigences pour être autorisée. Le code suivant définit deux stratégies :

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Voir [Startup.cs]

Ce code définit également le schéma d’authentification, qui indique à ASP.NET le middleware d’authentification doit s’exécuter si l’autorisation échoue. Dans ce cas, nous spécifions le middleware cookie d’authentification, car le middleware de l’authentification par cookie peut rediriger l’utilisateur vers une page « Interdit ». L’emplacement de la page interdit est défini dans l’option AccessDeniedPath pour le middleware de cookie ; voir [configuration de l’authentification middleware].

### <a name="authorize-controller-actions"></a>Autorise les actions de contrôleur

Enfin, définissez la stratégie pour autoriser une action dans un contrôleur MVC, le `Authorize` attribut :

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

Dans les versions antérieures d’ASP.NET, vous définissez la propriété de **rôles** sur l’attribut :

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Il est toujours pris en charge dans ASP.NET Core 1.0, mais il présente certains inconvénients par rapport aux stratégies d’autorisation :

-   Il suppose qu’un type de demande de remboursement particulier. Les stratégies peuvent vérifier pour n’importe quel type de déclaration. Les rôles sont simplement un type de déclaration.
-   Le nom de rôle est codée en dur dans l’attribut. Les stratégies de la logique d’autorisation est tous dans un seul emplacement, qu’il soit plus facile à mise à jour ou de la même charge à partir des paramètres de configuration.
-   Les stratégies permettent des décisions d’autorisation plus complexes (par exemple, l’âge > = 21) qui ne peuvent pas être exprimées par l’appartenance au rôle de simple.

## <a name="resource-based-authorization"></a>Autorisation de ressource en fonction des

_Autorisation à base de ressource_ se produit à chaque fois que l’autorisation dépend d’une ressource spécifique qui est affectée par une opération. Dans l’application Tailspin enquêtes, chaque enquête a un propriétaire et contributeurs de zéro-à-plusieurs.

-   Le propriétaire peut lire, mettre à jour, supprimer, publier et annuler la publication de l’enquête.
-   Le propriétaire peut affecter des collaborateurs à l’enquête.
-   Les collaborateurs peuvent lire et mettre à jour de l’enquête.

Notez que « propriétaire » et « collaborateur » ne sont pas les rôles d’application ; ils sont stockés par l’enquête, dans la base de données de l’application. Pour vérifier si un utilisateur peut supprimer une enquête, par exemple, l’application vérifie si l’utilisateur est le propriétaire de cette enquête.

Dans ASP.NET 1.0 de Core, implémenter l’autorisation basée sur les ressources en dérivant de **AuthorizationHandler** et de substitution de la méthode de **gestion** .

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Notez que cette classe est fortement typée pour les objets de l’enquête.  Enregistrer la classe de DI au démarrage :

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Pour effectuer des vérifications d’autorisation, utilisez l’interface de **IAuthorizationService** , ce qui vous pouvez d’injecter dans vos contrôleurs. Le code suivant vérifie si un utilisateur peut lire une enquête :

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Parce que nous passons dans un `Survey` objet, cet appel va appeler le `SurveyAuthorizationHandler`.

Dans votre code d’autorisation, une bonne approche consiste à agréger toutes les autorisations basée sur les ressources et rôle de l’utilisateur, puis à cocher la valeur de l’agrégat par rapport à l’opération souhaitée.
Voici un exemple de l’application d’enquêtes. L’application définit plusieurs types d’autorisation :

- Admin
- Collaborateur
- Créateur
- Propriétaire
- Lecteur

L’application définit également un ensemble d’opérations possibles sur les enquêtes :

- Créer
- En lecture
- Mise à jour
- Supprimer
- Publier
- Unpublsh

Le code suivant crée une liste d’autorisations pour un utilisateur particulier et l’enquête. Notez que ce code examine les rôles d’application de l’utilisateur et les champs propriétaire/collaboration dans le cadre de l’enquête.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Voir [SurveyAuthorizationHandler.cs].

Dans une application partagée, vous devez vous assurer que les autorisations ne « échappent » pour les données d’un autre client. Dans l’application d’enquêtes, l’autorisation de collaborateurs est autorisée entre les locataires &mdash; vous pouvez affecter un utilisateur à partir d’un autre client sous la forme d’un contriubutor. Les autres types d’autorisations sont limitées aux ressources qui appartiennent à des clients de l’utilisateur. Pour appliquer cette exigence, le code vérifie le code de client avant d’accorder l’autorisation. (Le `TenantId` de champ comme attribué lors de la création de l’enquête.)

L’étape suivante consiste à vérifier l’opération (en lecture, mise à jour, suppression, etc.) contre les autorisations. L’application d’enquêtes implémente cette étape à l’aide d’une table de choix des fonctions :

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```


## <a name="next-steps"></a>Étapes suivantes

- Lire le prochain article de cette série : [sécurisation d’un back-end web API dans une application partagée][web-api]
- Pour en savoir plus sur l’autorisation en fonction des ressources dans ASP.NET 1.0 Core, reportez-vous à la section [D’autorisation en fonction de la ressource][rbac].

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[partie d’une série]: guidance-multitenant-identity.md
[Rôles d’application]: guidance-multitenant-identity-app-roles.md
[policies]: https://docs.asp.net/en/latest/security/authorization/policies.html
[rbac]: https://docs.asp.net/en/latest/security/authorization/resourcebased.html
[implémentation de référence]: guidance-multitenant-identity-tailspin.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Configuration de l’authentification middleware]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[web-api]: guidance-multitenant-identity-web-api.md
