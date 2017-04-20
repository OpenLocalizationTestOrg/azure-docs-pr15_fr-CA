<properties
   pageTitle="Travailler avec des identités basée sur les revendications dans des applications mutualisées | Microsoft Azure"
   description="Comment une utilisation prétend pour l’autorisation et la validation de l’émetteur"
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
   ms.date="05/23/2016"
   ms.author="mwasson"/>

# <a name="working-with-claims-based-identities-in-multitenant-applications"></a>Travail avec des identités dans les applications mutualisées basée sur les revendications

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article fait [partie d’une série]. Il existe également un [exemple d’application] complète qui accompagne cette série.

## <a name="claims-in-azure-ad"></a>Revendications dans Azure AD

Lorsqu’un utilisateur se connecte, AD Azure envoie un jeton d’identification qui contient un ensemble de revendications relatives à l’utilisateur. Une revendication est simplement un élément d’information, exprimé sous la forme d’une paire clé/valeur. Par exemple, `email` = `bob@contoso.com`.  Revendications ont un émetteur &mdash; dans ce cas, Azure AD &mdash; qui est l’entité qui authentifie l’utilisateur et crée les revendications. Vous approuvez les revendications dans la mesure où vous faites confiance à l’émetteur. (À l’inverse, si vous ne faites confiance à l’émetteur, n’approuvez pas les revendications !)

À un niveau élevé :

1.  L’utilisateur s’authentifie.
2.  Le IDP envoie une série de déclarations.
3.  L’application normalise ou augmente les revendications (facultatives).
4.  L’application utilise les revendications pour prendre des décisions d’autorisation.

Dans OpenID vous connecter, la série de déclarations que vous obtenez est contrôlée par le [paramètre de l’étendue] de la demande d’authentification. Toutefois, AD Azure émet un ensemble limité de déclarations au moyen de OpenID se connecter ; consultez [prise en charge de jeton et les Types de revendications]. Si vous souhaitez plus d’informations sur l’utilisateur, vous devrez utiliser l’API Azure AD graphique.

Voici quelques-uns des créances de DAS qui une application peut généralement s’intéressent :

Type de revendication dans le jeton d’ID |    Description
-----------------------|--------------
AUD | Qui a émis le jeton pour. Il s’agit de l’ID de client. application En règle générale, vous devrez ne doit pas vous inquiéter à propos de cette demande, car le middleware valide automatiquement. Exemple :`"91464657-d17a-4327-91f3-2ed99386406f"`
groupes   | Une liste de groupes de DAS dont l’utilisateur est membre. Exemple :`["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
ISS  | L' [émetteur] du jeton OIDC. Exemple :`https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
nom    | Nom complet de l’utilisateur. Exemple :`"Alice A."`
OID | L’identificateur d’objet pour l’utilisateur dans un DAS. Cette valeur est l’identificateur immuable et non récupérable de l’utilisateur. Utilisez cette valeur, le pas de courrier électronique, sous la forme d’un identificateur unique pour les utilisateurs ; les adresses e-mail peuvent changer. Si vous utilisez l’API Azure AD graphique dans votre application, ID d’objet est la valeur utilisée pour interroger les informations de profil. Exemple :`"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
rôles   | Liste des rôles d’application pour l’utilisateur. Exemple :`["SurveyCreator"]`
TID | ID de client. Cette valeur est un identificateur unique pour le locataire dans Azure AD. Exemple :`"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
nom_unique | Un homme lisible surnom de l’utilisateur. Exemple :`"alice@contoso.com"`
UPN | Nom d’utilisateur principal. Exemple :`"alice@contoso.com"`

Ce tableau répertorie les types de revendications tels qu’ils apparaissent dans le jeton d’ID. Dans ASP.NET 1.0 de base, le middleware OpenID connecter convertit certains des types de revendication lorsqu’elle remplit la collection des demandes de l’utilisateur principal :

-   OID >`http://schemas.microsoft.com/identity/claims/objectidentifier`
-   TID >`http://schemas.microsoft.com/identity/claims/tenantid`
-   nom_unique >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-   UPN >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## <a name="claims-transformations"></a>Transformations de déclarations

Pendant le flux d’authentification, vous pouvez souhaiter modifier les déclarations que vous obtenez à partir de la IDP. Dans ASP.NET 1.0 de base, vous pouvez effectuer la transformation des déclarations à l’intérieur de l’événement **AuthenticationValidated** dans le middleware OpenID se connecter. (Voir les [événements d’authentification]).

Toute réclamation que vous ajoutez lors de **AuthenticationValidated** est stockées dans le cookie d’authentification de session. Ils n’obtenir déplacées vers Azure AD.

Voici quelques exemples de transformation de déclarations :

-   **Normalisation des créances**ou des créances faisant cohérentes entre les utilisateurs. Ceci est particulièrement pertinent si vous obtenez des revendications à partir de plusieurs IDPs, qui peuvent utiliser les types de déclaration différents pour des informations similaires.
Par exemple, publicité Azure envoie une déclaration « upn » qui contient le courrier électronique de l’utilisateur. Autres IDPs peuvent envoyer une demande d’indemnisation « courrier électronique ». Le code suivant convertit la demande « upn » une revendication « courrier électronique » :

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- Ajouter des **valeurs par défaut des revendications** pour les créances qui ne sont pas présents &mdash; , par exemple, affecter un utilisateur à un rôle par défaut. Dans certains cas, cela peut simplifier une logique d’autorisation.
- Ajouter des **types de revendications personnalisées** avec des informations spécifiques sur l’utilisateur. Par exemple, vous pouvez stocker des informations sur l’utilisateur dans une base de données. Vous pouvez ajouter une revendication personnalisée grâce à ces informations au ticket d’authentification. La demande est stockée dans un cookie, vous devez uniquement pour l’obtenir à partir de la base de données une fois par session de connexion. En revanche, vous souhaitez également éviter la création de cookies trop volumineux, vous devez envisager le compromis entre la taille du cookie et les recherches dans la base de données.   

Une fois que le flux d’authentification est terminé, les revendications sont disponibles dans `HttpContext.User`. À ce stade, vous devez les traiter comme une collection en lecture seule &mdash; par exemple, les utiliser pour prendre des décisions d’autorisation.

## <a name="issuer-validation"></a>Validation de l’émetteur
Dans OpenID vous connecter, la revendication de l’émetteur (« iss ») identifie le IDP qui a émis le jeton d’ID. Partie du flux d’authentification OIDC est pour vérifier que la créance de l’émetteur correspond à l’émetteur réel. Le middleware OIDC gère cela pour vous.

Dans Azure AD, la valeur d’émetteur est unique par les clients AD (`https://sts.windows.net/<tenantID>`). Par conséquent, une application doit effectuer une vérification supplémentaire, pour vous assurer que l’émetteur représente un client qui est autorisé à se connecter à l’application.

Pour une application mono-utilisateur, vous pouvez simplement vérifier que l’émetteur vos propres clients. En fait, le middleware OIDC effectue cette opération automatiquement par défaut. Dans une application partagée, vous devez autoriser plusieurs émetteurs, les locataires différents correspondant. Voici une approche générale à utiliser :

-   Dans les options de middleware OIDC, définissez **ValidateIssuer** sur false. Cette option désactive la vérification automatique.
-   Lorsqu’un client se connecte, stocker le locataire et l’émetteur dans votre base de données de l’utilisateur.
-   Chaque fois qu’un utilisateur se connecte, recherchez l’émetteur dans la base de données. Si l’émetteur n’est pas trouvée, cela signifie que le locataire n’a pas inscrit (e). Vous pouvez les rediriger vers une page d’inscription.
-  Vous pouvez également liste de blocage certains locataires ; par exemple, pour les clients qui n’ont pas de payer leur abonnement.

Pour plus d’informations, consultez [d’abonnement et client arrivant dans une application mutualisée][signup].

## <a name="using-claims-for-authorization"></a>À l’aide de demandes d’autorisation

Avec les déclarations, identité d’un utilisateur n’est plus une entité monolithique. Par exemple, un utilisateur peut avoir une adresse e-mail numéro de téléphone, anniversaire, sexe, etc. Peut-être IDP l’utilisateur stocke toutes ces informations. Mais lorsque vous authentifiez l’utilisateur, vous obtiendrez généralement un sous-ensemble de ces créances. Dans ce modèle, l’identité de l’utilisateur est simplement un ensemble de revendications. Lorsque vous prenez des décisions d’autorisation concernant un utilisateur, vous recherchez des ensembles particuliers de revendications. En d’autres termes, la question « Peut effectuer des action Y utilisateur X » devient finalement le « revendique d’utilisateur X a Z ».

Voici quelques modèles de base de vérification des créances.

-  Pour vérifier que l’utilisateur dispose d’une demande de remboursement particulier avec une valeur particulière :

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    Ce code vérifie si l’utilisateur possède une déclaration de rôle avec la valeur « Administrateur ». Il gère correctement le cas où l’utilisateur a aucune déclaration de rôle ou de plusieurs demandes de rôle.

    La classe **ClaimTypes** définit des constantes pour les types de revendications de couramment utilisées. Toutefois, vous pouvez utiliser n’importe quelle valeur de chaîne pour le type de déclaration.

-   Pour obtenir une seule valeur pour un type de déclaration, lorsque vous prévoyez qu’au plus une valeur :
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-   Pour obtenir toutes les valeurs d’un type de demande :

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

Pour plus d’informations, consultez [autorisation basée sur les rôles et basée sur les ressources dans les applications mutualisées][authorization].

## <a name="next-steps"></a>Étapes suivantes

- Lire le prochain article de cette série : [d’abonnement et client arrivant dans une application mutualisée][signup]
- En savoir plus sur [l’autorisation basée sur les revendications] dans la documentation ASP.NET Core 1.0

<!-- Links -->
[partie d’une série]: guidance-multitenant-identity.md
[paramètre d’étendue]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[Jeton de prise en charge et les Types de revendications]: ../active-directory/active-directory-token-and-claims.md
[émetteur]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[Événements d’authentification]: guidance-multitenant-identity-authenticate.md#authentication-events
[signup]: guidance-multitenant-identity-signup.md
[Autorisation basée sur les revendications]: https://docs.asp.net/en/latest/security/authorization/claims.html
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[authorization]: guidance-multitenant-identity-authorize.md
