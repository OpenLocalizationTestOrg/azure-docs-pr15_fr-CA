<properties
    pageTitle="Azure AD B2C | Microsoft Azure"
    description="La génération d’une API de Web .NET à l’aide d’Azure Active Directory B2C, sécurisés à l’aide des jetons d’accès OAuth 2.0 pour l’authentification."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure B2C de répertoire actif : Génération d’une API de web .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Avec Azure Active Directory (AD Azure), B2C, vous pouvez sécuriser un site web API à l’aide des jetons d’accès OAuth 2.0. Ces jetons permettent à vos applications clientes qui utilisent Azure AD B2C pour s’authentifier auprès de l’API. Cet article vous montre comment créer une API .NET Model-View-Controller (MVC) « liste de tâches » qui permet aux utilisateurs des tâches CRUD. Le web API est sécurisé à l’aide d’Azure AD B2C et autorise uniquement les utilisateurs authentifiés à gérer leur liste de tâches.

## <a name="create-an-azure-ad-b2c-directory"></a>Créer un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire, ou client. Un répertoire est un conteneur pour tous vos utilisateurs, applications, groupes et bien plus encore. Si vous n’avez pas encore, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de poursuivre dans ce guide.

## <a name="create-an-application"></a>Création d’une application

Ensuite, vous devez créer une application dans votre répertoire B2C. Ainsi, Azure informations dont il a besoin pour communiquer en toute sécurité avec votre application. Pour créer une application, suivez [les instructions](active-directory-b2c-app-registration.md). Veillez à :

- Inclure une **application web** ou une **API web** dans l’application.
- Utiliser la **redirection d’identificateur de ressource uniforme** `https://localhost:44316/` pour l’application web. Il s’agit de l’emplacement par défaut du client web app pour cet exemple de code.
- Copiez l' **ID de l’application** qui est assigné à votre application. Vous en aurez besoin ultérieurement.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure B2C d’Active Directory, chaque expérience de l’utilisateur est défini par une [stratégie](active-directory-b2c-reference-policies.md). Le client dans cet exemple de code contient trois expériences d’identité : vous inscrire, vous connecter et modifier le profil. Vous devez créer une stratégie pour chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lorsque vous créez vos trois stratégies, veillez à :

- Choisissez **L’ID utilisateur d’abonnement** ou **E-mail d’inscription** dans la lame de fournisseurs d’identité.
- Choisissez le **nom d’affichage** et d’autres attributs d’abonnement dans votre stratégie d’inscription.
- Choisissez les revendications de **nom complet** et **l’ID d’objet** en tant que revendications de l’application pour chaque stratégie. Vous pouvez choisir les autres déclarations.
- Copier le **nom** de chaque stratégie après que l’avoir créé. Vous devez ensuite ces noms de la stratégie.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois que vous avez créé les trois stratégies, vous êtes prêt à créer votre application.

## <a name="download-the-code"></a>Télécharger le code

Le code de ce didacticiel [est conservée sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Pour générer l’exemple à mesure que vous progressez, vous pouvez [télécharger un squelette de projet sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Vous pouvez également cloner le squelette :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

L’application terminée est également [disponible sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) ou sur le `complete` branche du même référentiel.

Après avoir téléchargé l’exemple de code, ouvrez le fichier .sln Visual Studio pour commencer. Le fichier solution contient deux projets : `TaskWebApp` et `TaskService`. `TaskWebApp`est une application web MVC avec lequel l’utilisateur interagit. `TaskService`est le site web de back-end de l’application API qui stocke la liste des tâches de chaque utilisateur.

## <a name="configure-the-task-web-app"></a>Configurer l’application web de tâche

Lorsqu’un utilisateur interagit avec `TaskWebApp`, le client envoie des requêtes à Active Directory Azure et récupère les jetons qui peuvent être utilisés pour appeler le `TaskService` web API. Pour connecter l’utilisateur et obtenir des jetons, vous devez fournir `TaskWebApp` avec des informations sur votre application. Dans le `TaskWebApp` ouvert le projet, le `web.config` de fichiers dans la racine du projet et de remplacer les valeurs dans le `<appSettings>` section.  Vous pouvez laisser le `AadInstance`, `RedirectUri`, et `TaskServiceUrl` valeurs comme-est.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

Cet article ne couvre pas la construction du `TaskWebApp` client.  Pour savoir comment créer une application web à l’aide d’Azure AD B2C, consultez [notre didacticiel d’application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="secure-the-api"></a>Sécuriser l’API

Lorsque vous avez un client qui appelle l’API pour le compte des utilisateurs, vous pouvez sécuriser `TaskService` en utilisant des jetons de support OAuth 2.0. Votre API peut accepter et valider des jetons à l’aide Open Web Interface de Microsoft de bibliothèque du .NET (OWIN).

### <a name="install-owin"></a>Installation de OWIN
Commencez par installer le pipeline d’authentification OAuth de OWIN :

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>Entrez les détails de votre B2C
Ouvrir le `web.config` fichier à la racine de la `TaskService` de projet et de remplacer les valeurs dans le `<appSettings>` section. Ces valeurs serviront dans l’ensemble de la bibliothèque d’API et OWIN.  Vous pouvez laisser le `AadInstance` valeur inchangée.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>Ajouter une classe de démarrage OWIN
Ajouter une classe de démarrage OWIN à la `TaskService` projet appelé `Startup.cs`.  Avec le bouton droit sur le projet, sélectionnez **Ajouter** , **Nouvel élément**et puis recherchez OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Configurer l’authentification OAuth 2.0
Ouvrez le fichier `App_Start\Startup.Auth.cs`et mettre en œuvre la `ConfigureAuth(...)` méthode :

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>Sécuriser le contrôleur de la tâche
Une fois que l’application est configurée pour utiliser l’authentification OAuth 2.0, vous pouvez sécuriser votre site web API en ajoutant une `[Authorize]` balise sur le contrôleur de la tâche. C’est le contrôleur de manipulation de liste toutes les tâches lieu, donc vous devez sécuriser le contrôleur entière au niveau de la classe. Vous pouvez également ajouter la `[Authorize]` balise pour un contrôle plus précis des actions individuelles.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Obtenir des informations utilisateur à partir du jeton
`TasksController`stocke des tâches dans une base de données où chaque tâche a un utilisateur associé qui « possède » la tâche. Le propriétaire est identifié par l' **ID de l’objet**. (C’est pourquoi vous avez besoin d’ajouter l’ID d’objet en tant qu’application demande dans toutes vos stratégies.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>Exécutez l’exemple d’application

Enfin, générez et exécutez les deux `TaskWebApp` et `TaskService`. Inscrivez-vous pour l’application à l’aide d’une adresse de messagerie ou un nom d’utilisateur. Créer des tâches dans la liste des actions de l’utilisateur et remarquez comment ils sont rendus persistants dans l’API de même après l’arrêt et le redémarrage du client.

## <a name="edit-your-policies"></a>Modifier vos stratégies

Une fois que vous avez sécurisé d’une API à l’aide d’Azure AD B2C, vous pouvez faire des essais avec les stratégies de votre application et afficher les effets (ou leur absence) sur l’API. Vous pouvez manipuler les revendications de l’application dans les stratégies et modifier les informations utilisateur qui sont disponibles dans l’API de web. Toute réclamation que vous ajoutez sera disponible sur votre site web .NET MVC API dans le `ClaimsPrincipal` objet, comme décrit précédemment dans cet article.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->
