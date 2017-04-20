<properties
    pageTitle="Annonce Azure v2.0 .NET Web App | Microsoft Azure"
    description="Comment créer une application Web MVC .NET qui signe les comptes d’utilisateurs avec les deux Account Microsoft et travail ou l’école."
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Ajoutez connexion à une application web de .NET MVC

Avec le point de terminaison version 2.0, vous pouvez rapidement ajouter une authentification à vos applications web avec la prise en charge pour les deux comptes personnels de Microsoft et les comptes de travail ou l’école.  Dans les applications web ASP.NET, cela à l’aide de middleware OWIN de Microsoft inclus dans.NET Framework 4.5.

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

 Ici, nous allons créer une application web qui utilise des OWIN pour l’utilisateur, afficher des informations sur l’utilisateur et de connexion l’utilisateur de l’application.
 
 ## <a name="download"></a>Télécharger
Le code de ce didacticiel est maintenue [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Pour suivre l’exemple, vous pouvez [Télécharger le squelette de l’application sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou cloner le squelette :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

L’application terminée est fournie à la fin de ce didacticiel ainsi.

## <a name="register-an-app"></a>Enregistrer une application
Créer une nouvelle application à [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Copie de l' **Id de l’Application** affectée à votre application, vous en aurez besoin plus rapidement.
- Ajouter la plate-forme **Web** pour votre application.
- Entrez l' **URI de redirection**. L’uri de redirection indique à AD Azure où les réponses d’authentification doivent être dirigés - la valeur par défaut pour ce didacticiel est `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Installer et configurer l’authentification OWIN
Ici, nous configure le middleware OWIN pour utiliser le protocole d’authentification OpenID de se connecter.  OWIN sera utilisé pour émettre des demandes de connexion et de déconnexion, la gestion de session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

-   Pour commencer, ouvrez le `web.config` de fichiers dans la racine du projet, puis entrez les valeurs de configuration de votre application dans le `<appSettings>` section.
    -   Le `ida:ClientId` est l' **Id de l’Application** affectée à votre application dans le portail de l’enregistrement.
    -   Le `ida:RedirectUri` est l' **Uri de redirection** saisis dans le portail.

-   Ensuite, ajoutez les packages NuGet de middleware OWIN au projet à l’aide de la Console du Gestionnaire de package.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Ajouter un « OWIN classe de démarrage » au projet appelé `Startup.cs` droite cliquez sur le projet--> **Ajouter** --> **Un nouvel élément** --> Recherchez « OWIN ».  Le middleware OWIN appelle la `Configuration(...)` méthode au démarrage de votre application.
-   Modifiez la déclaration de classe pour `public partial class Startup` -nous avons déjà mis en œuvre faisant partie de cette classe pour vous dans un autre fichier.  Dans le `Configuration(...)` méthode, effectuer un appel à ConfigureAuth(...) pour configurer l’authentification pour votre application web  

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
```

-   Ouvrez le fichier `App_Start\Startup.Auth.cs` et mettre en œuvre la `ConfigureAuth(...)` méthode.  Les paramètres que vous fournissez dans `OpenIdConnectAuthenticationOptions` servira de coordonnées de votre application pour communiquer avec Active Directory Azure.  Vous devez également configurer l’authentification par Cookie - le middleware OpenID connecter utilise des cookies derrière cette interface.

```C#
public void ConfigureAuth(IAppBuilder app)
             {
                     app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

                     app.UseCookieAuthentication(new CookieAuthenticationOptions());

                     app.UseOpenIdConnectAuthentication(
                             new OpenIdConnectAuthenticationOptions
                             {
                                     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0 
                                     // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                                     // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                                     ClientId = clientId,
                                     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                                     RedirectUri = redirectUri,
                                     Scope = "openid email profile",
                                     ResponseType = "id_token",
                                     PostLogoutRedirectUri = redirectUri,
                                     TokenValidationParameters = new TokenValidationParameters
                                     {
                                             ValidateIssuer = false,
                                     },
                                     Notifications = new OpenIdConnectAuthenticationNotifications
                                     {
                                             AuthenticationFailed = OnAuthenticationFailed,
                                     }
                             });
             }
```

## <a name="send-authentication-requests"></a>Envoyer les demandes d’authentification
Votre application est désormais correctement configurée pour communiquer avec le point de terminaison v2.0 en utilisant le protocole d’authentification OpenID de se connecter.  OWIN a prise en charge de tous les détails insupportable d’élaborer des messages d’authentification, la validation de jetons à partir d’AD Azure et gestion de session de l’utilisateur.  Il ne reste qu’à permettre à vos utilisateurs de se connecter et se déconnecter.

- Vous pouvez utiliser autorisent les balises dans vos contrôleurs pour cet utilisateur se connecte avant d’accéder à une page donnée.  Ouvrir `Controllers\HomeController.cs`et ajouter la `[Authorize]` le contrôleur propos balise.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-   Vous pouvez également utiliser OWIN pour soumettre directement des demandes d’authentification à partir de votre code.  Ouvrir `Controllers\AccountController.cs`.  Dans les actions SignIn() et SignOut(), numéro OpenID connecter le défi et demandes de déconnexion, respectivement.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-   À présent, ouvrez `Views\Shared\_LoginPartial.cshtml`.  Il s’agit d’où vous allez afficher des liens de connexion et de déconnexion de votre application de l’utilisateur et imprimer le nom d’utilisateur dans un affichage.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## <a name="display-user-information"></a>Afficher les informations utilisateur
Lors de l’authentification des utilisateurs avec OpenID de se connecter, le point de terminaison v2.0 retourne un id_token sur l’application qui contient les [demandes](active-directory-v2-tokens.md#id_tokens)ou les assertions relatives à l’utilisateur.  Vous pouvez utiliser ces déclarations pour personnaliser votre application :

- Ouvrir le `Controllers\HomeController.cs` fichier.  Vous pouvez accéder aux demandes de l’utilisateur dans vos contrôleurs via la `ClaimsPrincipal.Current` objet entité de sécurité.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

## <a name="run"></a>Exécuter

Enfin, générez et exécutez votre application !   Connectez-vous à l’aide d’un Account de Microsoft personnel ou un compte de travail ou de l’école et remarquez comment l’identité de l’utilisateur est reflétée dans la barre de navigation supérieure.  Vous disposez maintenant d’une application web sécurisée à l’aide de protocoles standard qui peuvent authentifier les utilisateurs avec leurs comptes personnels et de travail ou aux études.

Pour référence, l’exemple complet (sans les valeurs de configuration) [est fourni sous la forme d’un fichier zip ici](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip), ou vous pouvez le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais déplacer sur des sujets plus avancés.  Vous souhaiterez peut-être essayer :

[Sécuriser une API Web avec le point de terminaison v2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Pour obtenir des ressources supplémentaires, consultez :
- [Le guide du développeur v2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow « azure-active directory » balise >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous encourageons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et de s’abonner à l’alerte de sécurité.
