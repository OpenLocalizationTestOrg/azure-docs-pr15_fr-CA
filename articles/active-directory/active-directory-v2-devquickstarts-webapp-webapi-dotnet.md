<properties
    pageTitle="Annonce Azure v2.0 .NET Web App | Microsoft Azure"
    description="Comment créer une application Web de MVC .NET que web d’appels des services à l’aide de comptes Microsoft personnels et travail des comptes de l’établissement de connexion ou."
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
    ms.date="10/27/2016"
    ms.author="dastrock"/>

# <a name="calling-a-web-api-from-a-net-web-app"></a>L’appel d’une API de web à partir d’une application web de .NET

Avec le point de terminaison version 2.0, vous pouvez rapidement ajouter une authentification à vos applications web et web API avec prise en charge pour les deux comptes personnels de Microsoft et les comptes de travail ou l’école.  Ici, nous créerons une application web MVC qui signe les utilisateurs à l’aide de OpenID de se connecter, avec l’aide des middleware OWIN de Microsoft.  L’application web va obtenir des jetons d’accès OAuth 2.0 pour un site web api sécurisées par OAuth 2.0 qui permet de créer, lire et supprimer un utilisateur donné « liste de tâches ».

Ce didacticiel est principalement axée sur l’utilisation de MSAL pour acquérir et utiliser des jetons d’accès dans une application web, décrite dans complet [ici](active-directory-v2-flows.md#web-apps).  En tant que conditions préalables, vous pouvez souhaiter commencer par apprendre comment [Ajouter une authentification base dans une application web](active-directory-v2-devquickstarts-dotnet-web.md) ou comment [sécuriser un site web API](active-directory-v2-devquickstarts-dotnet-api.md).

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="download-sample-code"></a>Télécharger les exemples de code

Le code de ce didacticiel est maintenue [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Pour suivre l’exemple, vous pouvez [Télécharger le squelette de l’application sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) ou cloner le squelette :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Vous pouvez également [Télécharger l’application terminée, sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) ou cloner l’application terminée :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Enregistrer une application
Créer une nouvelle application à [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Copie de l' **Id de l’Application** affectée à votre application, vous en aurez besoin plus rapidement.
- Créer un **Secret de l’application** du type **mot de passe** et les copier vers le bas de sa valeur pour plus tard
- Ajouter la plate-forme **Web** pour votre application.
- Entrez l' **URI de redirection**. L’uri de redirection indique à AD Azure où les réponses d’authentification doivent être dirigés - la valeur par défaut pour ce didacticiel est `https://localhost:44326/`.


## <a name="install-owin"></a>Installation de OWIN
Ajouter des packages NuGet middleware OWIN à la `TodoList-WebApp` à l’aide de la Console du Gestionnaire de package de projet.  Le middleware OWIN sera utilisé pour émettre des demandes de connexion et de déconnexion, la gestion de session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Connexion de l’utilisateur
Maintenant configurer le middleware OWIN pour utiliser le [protocole d’authentification de connexion de OpenID](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  

-   Ouvrir le `web.config` fichier à la racine de la `TodoList-WebApp` de projet et entrez des valeurs de configuration de votre application dans le `<appSettings>` section.
    -   Le `ida:ClientId` est l' **Id de l’Application** affectée à votre application dans le portail de l’enregistrement.
    - Le `ida:ClientSecret` est le **Secret de l’application** , vous avez créé dans le portail d’inscription.
    -   Le `ida:RedirectUri` est l' **Uri de redirection** saisis dans le portail.
- Ouvrir le `web.config` fichier à la racine de la `TodoList-Service` du projet, puis remplacez le `ida:Audience` avec le même **Id de l’Application** , comme indiqué ci-dessus.


- Ouvrez le fichier `App_Start\Startup.Auth.cs` et ajoutez `using` instructions pour les bibliothèques à partir du haut.
- Dans le même fichier, vous devez implémenter le `ConfigureAuth(...)` méthode.  Les paramètres que vous fournissez dans `OpenIDConnectAuthenticationOptions` servira de coordonnées de votre application pour communiquer avec Active Directory Azure.

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
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>MSAL permet d’obtenir des jetons d’accès
Dans le `AuthorizationCodeReceived` notification, que nous souhaitons utiliser [2.0 OAuth en tandem avec OpenID se connecter](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) à échanger contre de l’authorization_code d’un jeton d’accès pour le Service de liste de tâches.  MSAL peut faciliter ce processus pour vous :

- Tout d’abord, installez la version d’évaluation de MSAL :

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```
- Et ajouter un autre `using` instruction à la `App_Start\Startup.Auth.cs` fichier de MSAL.
- Ajoutez maintenant une nouvelle méthode, les `OnAuthorizationCodeReceived` Gestionnaire d’événements.  Ce gestionnaire utilise MSAL pour acquérir un jeton d’accès à l’API de liste de tâches et stocke le jeton dans le cache de jetons de MSAL pour plus tard :

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

- Dans les applications web, MSAL a un cache de jeton extensible qui peut être utilisé pour stocker les jetons.  Cet exemple implémente le `NaiveSessionCache` qui utilise le stockage de session http aux jetons de cache.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Appeler l’API Web
Il est maintenant temps d’utiliser réellement l’access_token que vous avez acquis à l’étape 3.  Ouvrez l’application web `Controllers\TodoListController.cs` fichier, ce qui rend toutes les demandes CRUD à l’API de liste de tâches.

- Vous pouvez réutiliser MSAL ici pour extraire des access_tokens à partir du cache MSAL.  Tout d’abord, ajoutez un `using` instruction pour MSAL à ce fichier.

    `using Microsoft.Identity.Client;`

- Dans le `Index` , MSAL d’utiliser l’action `AcquireTokenSilentAsync` méthode pour obtenir un access_token qui peut être utilisé pour lire des données à partir du service de liste de tâches :

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

- L’exemple ajoute ensuite le jeton résultant pour la demande HTTP GET comme le `Authorization` en-tête, que le service de liste de tâches utilise pour authentifier la demande.
- Si le service de liste de tâches retourne un `401 Unauthorized` réponse, l’access_tokens dans MSAL sont devenues non valides pour une raison quelconque.  Dans ce cas, vous devez supprimer toute access_tokens à partir du cache MSAL et montrer à l’utilisateur un message qui leur sont nécessaires pour vous connecter à nouveau, qui va redémarrer le flux de jeton d’acquisition.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

- De même, si MSAL ne parvient pas à renvoyer une access_token pour une raison quelconque, vous demandez à l’utilisateur de se connecter à nouveau.  C’est aussi simple que l’interception d’une `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

- L’exacte même `AcquireTokenSilentAsync` appel est implementd dans les `Create` et `Delete` actions.  Dans les applications web, vous pouvez utiliser cette méthode MSAL pour obtenir access_tokens à chaque fois que vous en avez besoin dans votre application.  MSAL se charge de l’acquisition, la mise en cache et l’actualisation des jetons pour vous.

Enfin, générez et exécutez votre application !  Connectez-vous à l’aide d’un Account de Microsoft ou un compte Azure et remarquez comment l’identité de l’utilisateur est reflétée dans la barre de navigation supérieure.  Ajouter et supprimer des éléments à partir de la liste des actions de l’utilisateur de voir que le 2.0 OAuth sécurisé des appels d’API en action.  Vous disposez maintenant d’une application web & les API web, deux sécurisés à l’aide de protocoles standard, qui peuvent authentifier les utilisateurs avec leurs comptes personnels et de travail ou aux études.

Pour référence, l’exemple complet (sans les valeurs de configuration) [est fourni ici](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des ressources supplémentaires, consultez :
- [Le guide du développeur v2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow « azure-active directory » balise >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous encourageons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et de s’abonner à l’alerte de sécurité.
