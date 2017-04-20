<properties
    pageTitle=".NET AD Azure route | Microsoft Azure"
    description="Comment créer une application Web MVC .NET qui s’intègre avec Azure AD pour l’ouverture de session."
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

# <a name="aspnet-web-app-sign-in--sign-out-with-azure-ad"></a>ASP.NET Web application connexion et déconnexion avec Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

AD Azure rend simple et directe sous-traiter la gestion d’identité de votre application web, fournissant la même connexion et de déconnexion avec seulement quelques lignes de code.  Dans les applications web Asp.NET, vous pouvez le faire à l’aide de l’implémentation Microsoft de la Communauté OWIN logiciels intermédiaires inclus dans.NET Framework 4.5.  Ici, nous allons utiliser OWIN pour :
-   Connecter l’utilisateur à l’application à l’aide d’Active Directory Azure comme le fournisseur d’identité.
-   Afficher des informations sur l’utilisateur.
-   Connexion de l’utilisateur de l’application.

Pour ce faire, vous devez :

1. Inscrire une application avec Azure AD
2. Configurez votre application pour utiliser le pipeline de l’authentification OWIN.
3. OWIN permet d’émettre des demandes de connexion et de déconnexion à AD Azure.
4. Imprimer les données relatives à l’utilisateur.

Pour démarrer, [Téléchargez le squelette d’application](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  Vous aurez également besoin d’un locataire AD Azure dans lequel vous souhaitez enregistrer votre application.  Si vous n’en avez pas déjà fait, [Apprenez à en obtenir un](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>*1. inscription d’une Application avec Azure AD*
Pour permettre à votre application authentifier les utilisateurs, vous devez tout d’abord enregistrer une nouvelle application dans votre client.

- Signe dans le portail de gestion Azure.
- Dans nav de la main gauche, cliquez sur **Active Directory**.
- Sélectionnez le client où vous souhaitez enregistrer l’application.
- Cliquez sur l’onglet **Applications** , puis cliquez sur Ajouter dans le bac d’alimentation inférieur.
- Suivez les invites et créer une nouvelle **Application Web et/ou WebAPI**.
    - Le **nom** de l’application décrit votre application aux utilisateurs finals
    -   L' **Ouverture de session d’URL** est l’URL de base de votre application.  Valeur par défaut du squelette est `https://localhost:44320/`.
    - L' **URI d’application ID** est un identificateur unique pour votre application.  La convention consiste à utiliser `https://<tenant-domain>/<app-name>`, par exemple :`https://contoso.onmicrosoft.com/my-first-aad-app`
- Une fois l’enregistrement terminé, DAS affecter à votre application un identificateur de client unique.  Vous aurez besoin de cette valeur dans les sections suivantes, donc copier à partir de l’onglet Configurer.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurer votre application pour utiliser le pipeline de l’authentification OWIN*
Ici, nous configure le middleware OWIN pour utiliser le protocole d’authentification OpenID de se connecter.  OWIN sera utilisé pour émettre des demandes de connexion et de déconnexion, la gestion de session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

-   Pour commencer, ajoutez les packages NuGet de middleware OWIN au projet à l’aide de la Console du Gestionnaire de package.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Ajouter une classe de démarrage de OWIN au projet appelé `Startup.cs` droite cliquez sur le projet--> **Ajouter** --> **Un nouvel élément** --> Recherchez « OWIN ».  Le middleware OWIN appelle la `Configuration(...)` méthode au démarrage de votre application.
-   Modifiez la déclaration de classe pour `public partial class Startup` -nous avons déjà mis en œuvre faisant partie de cette classe pour vous dans un autre fichier.  Dans le `Configuration(...)` méthode, effectuer un appel à ConfgureAuth(...) pour configurer l’authentification pour votre application web  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Ouvrez le fichier `App_Start\Startup.Auth.cs` et mettre en œuvre la `ConfigureAuth(...)` méthode.  Les paramètres que vous fournissez dans `OpenIDConnectAuthenticationOptions` servira de coordonnées de votre application pour communiquer avec Active Directory Azure.  Vous devez également configurer l’authentification par Cookie - le middleware OpenID connecter utilise des cookies derrière cette interface.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-   Enfin, ouvrez le `web.config` de fichiers dans la racine du projet et entrez vos valeurs de configuration de le `<appSettings>` section.
    -   Votre application `ida:ClientId` est le Guid que vous avez copié à partir du portail Azure à l’étape 1.
    -   Le `ida:Tenant` est le nom de vos clients AD Azure, par exemple, « contoso.onmicrosoft.com ».
    -   Votre `ida:PostLogoutRedirectUri` indique à AD Azure où un utilisateur doit être redirigé après une demande de déconnexion s’est terminée correctement.

## <a name="3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>*3. Utilisez OWIN pour émettre des demandes de connexion et de déconnexion pour Azure AD*
Votre application est désormais correctement configurée pour communiquer avec Azure AD en utilisant le protocole d’authentification OpenID de se connecter.  OWIN a prise en charge de tous les détails insupportable d’élaborer des messages d’authentification, la validation de jetons à partir d’AD Azure et gestion de session de l’utilisateur.  Il ne reste qu’à permettre à vos utilisateurs de se connecter et se déconnecter.

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
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-   À présent, ouvrez `Views\Shared\_LoginPartial.cshtml`.  Il s’agit d’où vous allez afficher des liens de connexion et de déconnexion de votre application de l’utilisateur et imprimer le nom d’utilisateur dans un affichage.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
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

## <a name="4--display-user-information"></a>*4. afficher les informations utilisateur*
Lors de l’authentification des utilisateurs avec OpenID Connect, AD Azure retourne un id_token à l’application qui contient « créances » ou des assertions sur l’utilisateur.  Vous pouvez utiliser ces déclarations pour personnaliser votre application :

- Ouvrir le `Controllers\HomeController.cs` fichier.  Vous pouvez accéder aux demandes de l’utilisateur dans vos contrôleurs via la `ClaimsPrincipal.Current` objet entité de sécurité.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Enfin, générez et exécutez votre application !  Si vous ne l’avez pas déjà, il est temps de créer un nouvel utilisateur dans vos clients avec une *. onmicrosoft.com domaine.  Connectez-vous à l’aide de cet utilisateur et notez l’identité de l’utilisateur est reflétée dans la barre de navigation supérieure.  Vous déconnecter et vous reconnecter en tant qu’un autre utilisateur de votre client.  Si vous sentez que quelque chose particulièrement ambitieux, enregistrer et exécuter une autre instance de cette application (avec son propre clientId) et Espion Voir authentification unique sur en action.

Pour référence, l’exemple complet (sans les valeurs de configuration) [est fourni ici](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  

Vous pouvez désormais déplacer sur des sujets plus avancés.  Vous souhaiterez peut-être essayer :

[Sécuriser un site Web API avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
