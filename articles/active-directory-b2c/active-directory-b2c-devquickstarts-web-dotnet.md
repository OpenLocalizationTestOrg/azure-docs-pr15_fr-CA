<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Comment créer une application web qui a sign-in, d’abonnement, et la gestion des profils à l’aide d’Azure Active Directory B2C."
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
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-build-a-net-web-app"></a>Azure AD B2C : Créer une application web de .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

À l’aide de B2C d’Azure Active Directory (AD Azure), vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissant pour votre application web en quelques étapes. Cet article présentent la création d’une application web .NET Model-View-Controller (MVC) qui inclut l’utilisateur, inscription, inscription et la gestion des profils. L’application inclut la prise en charge de l’inscription et à la connexion à l’aide d’un nom d’utilisateur ou d’un courrier électronique et en utilisant des comptes sociaux tels que Facebook et Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire, ou client. Un répertoire est un conteneur pour tous vos utilisateurs, applications, groupes et bien plus encore.  Si vous n’avez pas encore, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de poursuivre dans ce guide.

## <a name="create-an-application"></a>Création d’une application

Ensuite, vous devez créer une application dans votre répertoire B2C. Ainsi, Azure informations dont il a besoin pour communiquer en toute sécurité avec votre application. Pour créer une application, suivez [les instructions](active-directory-b2c-app-registration.md).  Veillez à :

- Inclure un **site web app API** dans l’application.
- Entrez `https://localhost:44316/` sous la forme d’un **URI de redirection**. Il s’agit de l’URL par défaut pour cet exemple de code.
- Copiez l' **ID de l’Application** qui est assigné à votre application.  Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure B2C d’Active Directory, chaque expérience de l’utilisateur est défini par une [stratégie](active-directory-b2c-reference-policies.md). Cet exemple de code contient trois expériences d’identité : vous inscrire, vous connecter et modifier le profil. Vous devez créer une stratégie de chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).

>[AZURE.NOTE] Azure AD B2C prend également en charge un combiné, inscrivez-vous ou dans la stratégie qui n’est pas inclu dans ce didacticiel.  L’inscription ou le signe dans la stratégie dans [ce didacticiel équivalent](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

Lorsque vous créez les trois stratégies, veillez à :

- Choisissez **L’ID utilisateur d’abonnement** ou **E-mail d’inscription** dans la lame de fournisseurs d’identité.
- Cliquez sur le **nom d’affichage** et autres attributs d’abonnement dans votre stratégie d’inscription.
- Choisissez la revendication de **nom complet** sous la forme d’une application demande dans chaque stratégie. Vous pouvez choisir les autres déclarations.
- Copier le **nom** de chaque stratégie après que l’avoir créé. Vous devez ensuite ces noms de la stratégie.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Après avoir créé vos stratégies de trois, vous êtes prêt à créer votre application.  

## <a name="download-the-code-and-configure-authentication"></a>Télécharger le code et la configuration de l’authentification

Le code de cet exemple [est conservée sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Pour générer l’exemple à mesure que vous progressez, vous pouvez [Télécharger le squelette de projet sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip). Vous pouvez également cloner le squelette :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

L’exemple complet est également [disponible sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) ou sur le `complete` branche du même référentiel.

Après avoir téléchargé l’exemple de code, ouvrez le fichier .sln Visual Studio pour commencer.

Votre application communique avec Azure AD B2C en envoyant des messages d’authentification qui spécifient la stratégie que souhaite exécuter dans le cadre de la demande HTTP. Pour les applications web .NET, vous pouvez utiliser le middleware OWIN de Microsoft pour envoyer les demandes d’authentification OpenID de se connecter, d’exécuter des stratégies, de gérer les sessions utilisateur et bien plus encore.

Pour commencer, ajoutez les packages NuGet de middleware OWIN au projet à l’aide de la Console de Gestionnaire de package de Visual Studio.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Ensuite, ouvrez le `web.config` de fichiers dans la racine du projet et entrez des valeurs de configuration de votre application dans le `<appSettings>` section, en remplaçant les valeurs existantes.

```
<configuration>
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
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ensuite, ajoutez une classe de démarrage OWIN au projet appelé `Startup.cs`. Avec le bouton droit sur le projet, sélectionnez **Ajouter** , **Nouvel élément**et recherchez « OWIN ». **Veillez à modifier la déclaration de classe pour `public partial class Startup` **. Nous avons implémenté la partie de cette classe pour vous dans un autre fichier. Le middleware OWIN appelle la `Configuration(...)` méthode au démarrage de votre application. Dans cette méthode, effectuez un appel à `ConfigureAuth(...)`, où vous devez définir l’authentification pour votre application.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Ouvrez le fichier `App_Start\Startup.Auth.cs` et mettre en œuvre la `ConfigureAuth(...)` méthode.  Les paramètres que vous fournissez dans `OpenIdConnectAuthenticationOptions` servent de coordonnées pour votre application pour communiquer avec Active Directory Azure. Vous devez également configurer l’authentification des cookies. Le middleware OpenID connecter utilise des cookies pour gérer les sessions utilisateur, entre autres choses.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignUpPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(ProfilePolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignInPolicyId));
    }

    // Used for avoiding yellow-screen-of-death
    private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        notification.HandleResponse();
        if (notification.Exception.Message == "access_denied")
        {
            notification.Response.Redirect("/");
        }
        else
        {
            notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
        }

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
```

## <a name="send-authentication-requests-to-azure-ad"></a>Envoyer les demandes d’authentification à Active Directory Azure
Votre application est désormais correctement configurée pour communiquer avec Azure AD B2C en utilisant le protocole d’authentification OpenID de se connecter.  OWIN a prise en charge de tous les détails de l’élaboration de messages d’authentification, la validation de jetons à partir d’AD Azure et gestion de session de l’utilisateur.  Il ne reste qu’à lancer les flux de chaque utilisateur.

Lorsqu’un utilisateur sélectionne les **inscrire**, **vous connecter**ou **Modifier le profil** dans l’application web, l’action associée est appelée dans `Controllers\AccountController.cs`. Dans chaque cas, vous pouvez utiliser les méthodes OWIN intégrées pour déclencher la stratégie de droite :

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties () { RedirectUri = "/" }, Startup.SignInPolicyId);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SignUpPolicyId);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.ProfilePolicyId);
    }
}
```

Vous pouvez également utiliser un `[Authorize]` balises dans vos contrôleurs qui requiert l’exécution d’une stratégie spécifique, si l’utilisateur n’est pas connecté. Ouvrir `Controllers\HomeController.cs` et ajouter la `[Authorize]` balise au contrôleur de revendications.  Sélectionne la dernière stratégie configurée pour exécuter lorsque la balise autoriser est appelée OWIN.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a policy if the user is not already signed in the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

Vous pouvez également utiliser OWIN pour vous déconnecter l’utilisateur de l’application. In `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>Afficher les informations utilisateur
Lorsque vous authentifiez les utilisateurs à l’aide de OpenID de se connecter, Azure AD renvoie un jeton ID à l’application qui contient des **déclarations**. Il s’agit des assertions sur l’utilisateur. Vous pouvez utiliser des revendications pour personnaliser votre application.  

Ouvrir le `Controllers\HomeController.cs` fichier. Vous pouvez accéder aux demandes de l’utilisateur dans vos contrôleurs via la `ClaimsPrincipal.Current` objet entité de sécurité.

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Vous pouvez accéder à toute demande que reçoit votre application de la même manière.  Une liste de toutes les créances que reçoit de l’application est disponible pour vous sur la page **demandes de remboursement** .

## <a name="run-the-sample-app"></a>Exécutez l’exemple d’application

Enfin, vous pouvez générer et exécuter votre application. Inscrivez-vous pour l’application à l’aide d’un nom d’utilisateur ou une adresse e-mail. Déconnectez-vous, puis reconnectez-vous en tant que le même utilisateur. Modifier le profil de cet utilisateur. Se déconnecter et de vous inscrire en tant qu’autre utilisateur. Notez que les informations affichées sous l’onglet **demandes** correspondant aux informations que vous avez configurés sur vos stratégies.

## <a name="add-social-idps"></a>Ajouter IDPs sociales

Actuellement, l’application prend en charge uniquement l’utilisateur, inscription et connexion à l’aide de **comptes locaux**. Il s’agit de comptes stockées dans votre répertoire B2C qui utilisent un nom d’utilisateur et le mot de passe. À l’aide d’Azure AD B2C, vous pouvez ajouter la prise en charge pour d’autres **fournisseurs d’identité** (IDPs) sans modifier votre code.

Pour ajouter des IDPs sociales à votre application, commencez en suivant les instructions détaillées de ces articles. Pour chaque IDP que vous souhaitez prendre en charge, vous devez enregistrer une application de ce système et obtenir un ID de client.

- [Configurer Facebook comme un IDP](active-directory-b2c-setup-fb-app.md)
- [Définir Google comme un IDP](active-directory-b2c-setup-goog-app.md)
- [Définir comme un IDP Amazon](active-directory-b2c-setup-amzn-app.md)
- [Définir comme un IDP LinkedIn](active-directory-b2c-setup-li-app.md)

Après avoir ajouté les fournisseurs d’identité dans votre répertoire B2C, vous devez modifier chacune de vos trois stratégies pour inclure les nouveau IDPs, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md). Après avoir enregistré vos stratégies, exécutez de nouveau l’application.  Vous devriez voir les nouveau IDPs ajoutés en tant que connexion et rencontre des options d’abonnement dans chacune de vos identités.

Vous pouvez faire des essais avec vos stratégies et observez l’effet sur votre exemple d’application. Ajouter ou supprimer IDPs, manipuler les revendications de l’application ou modifier les attributs d’abonnement. Faites des essais jusqu'à ce que vous pouvez voir comment lient ensemble les stratégies, les demandes d’authentification et les OWIN.

Pour référence, l’exemple complet (sans les valeurs de configuration) [est fourni sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Vous pouvez également cloner GitHub :

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
