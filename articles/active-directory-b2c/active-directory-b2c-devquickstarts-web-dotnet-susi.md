<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Comment créer une application web qui a une inscription, la connexion et le mot de passe réinitialiser à l’aide d’Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-sign-up--sign-in-in-a-aspnet-web-app"></a>Azure AD B2C : Inscription & connexion dans une application Web d’ASP.NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

À l’aide de B2C d’Azure Active Directory (AD Azure), vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissant pour votre application web en quelques étapes. Cet article traite de la création d’une application web ASP.NET qui inclut des utilisateur, inscription, inscription, et de réinitialisation de mot de passe. L’application inclut la prise en charge de l’inscription et à la connexion à l’aide d’un nom d’utilisateur ou d’un courrier électronique et en utilisant des comptes sociaux tels que Facebook et Google.

Ce didacticiel est différente de [nos autre didacticiel du web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) qu’il utilise un [Inscrivez-vous ou ouvrez une session stratégie](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy) pour fournir l’enregistrement de l’utilisateur et reconnectez-vous à l’aide d’un seul bouton, au lieu de deux (une pour inscription et l’autre pour la connexion).  En bref, une inscription ou signe dans la stratégie permet aux utilisateurs de se connecter avec un compte existant si elles ont un ou créer un autre s’il s’agit de leur première fois à l’aide de l’application.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire, ou client. Un répertoire est un conteneur pour tous vos utilisateurs, applications, groupes et bien plus encore.  Si vous n’avez pas encore, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de poursuivre dans ce guide.

## <a name="create-an-application"></a>Création d’une application

Ensuite, vous devez créer une application dans votre répertoire B2C. Ainsi, Azure informations dont il a besoin pour communiquer en toute sécurité avec votre application. Pour créer une application, suivez [les instructions](active-directory-b2c-app-registration.md).  Veillez à :

- Inclure un **site web app API** dans l’application.
- Entrez `https://localhost:44316/` sous la forme d’un **URI de redirection**. Il s’agit de l’URL par défaut pour cet exemple de code.
- Copiez l' **ID de l’Application** qui est assigné à votre application.  Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure B2C d’Active Directory, chaque expérience de l’utilisateur est défini par une [stratégie](active-directory-b2c-reference-policies.md). Cet exemple de code contient deux expériences d’identité : **inscription et ouverture de session**et **réinitialisation du mot de passe**.  Vous devez créer une stratégie de chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md). Lorsque vous créez les deux stratégies, veillez à :

- Choisissez **L’ID utilisateur d’abonnement** ou **E-mail d’inscription** dans la lame de fournisseurs d’identité.
- Cliquez sur le **nom d’affichage** et autres attributs d’abonnement dans votre stratégie d’inscription et de connexion.
- Choisissez la revendication de **nom complet** sous la forme d’une application demande dans chaque stratégie. Vous pouvez choisir les autres déclarations.
- Copier le **nom** de chaque stratégie après que l’avoir créé. Vous devez ensuite ces noms de la stratégie.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Après avoir créé les deux stratégies, vous êtes prêt à créer votre application.

## <a name="download-the-code-and-configure-authentication"></a>Télécharger le code et la configuration de l’authentification

Le code de cet exemple [est conservée sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI). Pour générer l’exemple à mesure que vous progressez, vous pouvez [Télécharger le squelette de projet sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/skeleton.zip). Vous pouvez également cloner le squelette :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

L’exemple complet est également [disponible sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip) ou sur le `complete` branche du même référentiel.

Après avoir téléchargé l’exemple de code, ouvrez le fichier .sln Visual Studio pour commencer.

Votre application communique avec Azure AD B2C en envoyant des requêtes d’authentification HTTP qui spécifient la stratégie qu’il souhaite exécuter dans le cadre de la demande. Pour les applications web .NET, vous pouvez utiliser la bibliothèque OWIN de Microsoft pour envoyer les demandes d’authentification OpenID de se connecter, d’exécuter des stratégies, de gérer les sessions utilisateur et bien plus encore.

Pour commencer, ajoutez les packages NuGet de middleware OWIN au projet à l’aide de la Console de Gestionnaire de package de Visual Studio.

```
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package System.IdentityModel.Tokens.Jwt
```

Ensuite, ouvrez le `web.config` de fichiers dans la racine du projet et entrez des valeurs de configuration de votre application dans le `<appSettings>` section, en remplaçant les valeurs ci-dessous avec votre propre.  Vous pouvez laisser la `ida:RedirectUri` et le `ida:AadInstance` valeurs, inchangée.

```
<configuration>
  <appSettings>

    ...

    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SusiPolicyId" value="b2c_1_susi" />
    <add key="ida:PasswordResetPolicyId" value="b2c_1_reset" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ensuite, ajoutez une classe de démarrage OWIN au projet appelé `Startup.cs`. Avec le bouton droit sur le projet, sélectionnez **Ajouter** , **Nouvel élément**et recherchez « OWIN ». Modifiez la déclaration de classe à `public partial class Startup`. Nous avons implémenté la partie de cette classe pour vous dans un autre fichier. Le middleware OWIN appelle la `Configuration(...)` méthode au démarrage de votre application. Dans cette méthode, effectuez un appel à `ConfigureAuth(...)`, où vous devez définir l’authentification pour votre application.

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
    public static string SusiPolicyId = ConfigurationManager.AppSettings["ida:SusiPolicyId"];
    public static string PasswordResetPolicyId = ConfigurationManager.AppSettings["ida:PasswordResetPolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(PasswordResetPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SusiPolicyId));

    }

    private Task OnSecurityTokenValidated(SecurityTokenValidatedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        // If you wanted to keep some local state in the app (like a db of signed up users),
        // you could use this notification to create the user record if it does not already
        // exist.

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
                SecurityTokenValidated = OnSecurityTokenValidated,
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
...
```

## <a name="send-authentication-requests-to-azure-ad"></a>Envoyer les demandes d’authentification à Active Directory Azure
Votre application est désormais correctement configurée pour communiquer avec Azure AD B2C en utilisant le protocole d’authentification OpenID de se connecter.  OWIN a prise en charge de tous les détails de l’élaboration de messages d’authentification, la validation de jetons à partir d’AD Azure et gestion de session de l’utilisateur.  Il ne reste qu’à lancer les flux de chaque utilisateur.

Lorsqu’un utilisateur sélectionne la **connexion** ou **mot de passe oublié ?** dans l’application web, l’action associée est appelée dans `Controllers\AccountController.cs`. Dans chaque cas, vous pouvez utiliser les méthodes OWIN intégrées pour déclencher la stratégie de droite :

```C#
// Controllers\AccountController.cs

public void Login()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SusiPolicyId);
    }
}

public void ResetPassword()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
        new AuthenticationProperties() { RedirectUri = "/" }, Startup.PasswordResetPolicyId);
    }
}
```

Lors de l’exécution de l’inscription ou de vous connecter dans la stratégie, l’utilisateur a la possibilité de cliquer sur un **votre mot de passe oublié ?** lien.  Dans ce cas, Azure AD B2C enverra à votre application un message d’erreur indiquant qu’il doit exécuter un mot de passe réinitialiser la stratégie.  Vous pouvez capturer cette erreur dans `Startup.Auth.cs` à l’aide de la `AuthenticationFailed` notification :

```C#
// Used for avoiding yellow-screen-of-death TODO
private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        // If the user canceled the sign in, redirect back to the home page
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```


En plus d’appeler explicitement une stratégie, vous pouvez utiliser un `[Authorize]` balises dans vos contrôleurs qui exécutera une politique si l’utilisateur n’est pas connecté. Ouvrir `Controllers\HomeController.cs` et ajouter la `[Authorize]` balise au contrôleur de revendications.  OWIN sélectionne la dernière stratégie configurée lorsque le `[Authorize]` balise est atteint.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

Vous pouvez également utiliser OWIN pour vous déconnecter l’utilisateur de l’application. In `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void Logout()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
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

Pour référence, l’exemple complet (sans les valeurs de configuration) [est fourni sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip). Vous pouvez également cloner GitHub :

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
