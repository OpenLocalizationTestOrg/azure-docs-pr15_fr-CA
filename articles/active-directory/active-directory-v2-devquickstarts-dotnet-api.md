<properties
    pageTitle="API Web .NET v2.0 de publicité Azure | Microsoft Azure"
    description="La génération d’une Api Web MVC .NET qui accepte des jetons de deux Account personnel de Microsoft et les comptes de travail ou l’école."
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
    ms.date="10/10/2016"
    ms.author="dastrock"/>

# <a name="secure-an-mvc-web-api"></a>Sécuriser un site web MVC API

Azure Active Directory du point de terminaison version 2.0, vous pouvez protéger une API Web à l’aide des jetons d’accès [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) , qui permet aux utilisateurs avec des comptes de Microsoft et travail de personnel ou de l’établissement des comptes pour accéder en toute sécurité à votre API Web.

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

Dans ASP.NET web API, vous pouvez faire à l’aide de middleware OWIN de Microsoft inclus dans.NET Framework 4.5.  Ici, nous allons utiliser OWIN pour générer une API Web MVC « Liste des tâches » qui permet aux clients de créer et de lire des tâches à partir de la liste des actions d’un utilisateur.  L’API de web valide que les requêtes entrantes contiennent un jeton d’accès valide et rejeter toutes les demandes qui ne passent pas de validation sur un itinéraire protégé.  Cet exemple a été construit à l’aide de Visual Studio 2015.

## <a name="download"></a>Télécharger
Le code de ce didacticiel est maintenue [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Pour suivre l’exemple, vous pouvez [Télécharger le squelette de l’application sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou cloner le squelette :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

L’application squelette inclut tout le code réutilisable pour une API simple, mais il manque toutes les pièces liées à l’identité. Si vous ne souhaitez pas suivre l’exemple, vous pouvez en revanche cloner ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Enregistrer une application
Créer une nouvelle application à [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Copie de l' **Id de l’Application** affectée à votre application, vous en aurez besoin plus rapidement.

Cette solution visual studio contient également un « TodoListClient », ce qui est une application WPF simple.  Le TodoListClient est utilisé pour montrer comment un signe de l’utilisateur et comment un client peut émettre des demandes à votre API Web.  Dans ce cas, à la fois le TodoListClient et le TodoListService sont représentées par la même application.  Pour configurer le TodoListClient, vous devez également :

- Ajouter la plate-forme **Mobile** pour votre application.


## <a name="install-owin"></a>Installation de OWIN

Maintenant que vous avez enregistré une application, vous devez configurer votre application pour communiquer avec le point de terminaison v2.0 pour valider entrantes des demandes et des jetons.

- Pour commencer, ouvrez la solution et ajouter les packages NuGet de middleware OWIN au projet TodoListService à l’aide de la Console du Gestionnaire de package.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Configurer l’authentification OAuth

- Ajouter une classe de démarrage de OWIN du projet TodoListService appelé `Startup.cs`.  Droite cliquez sur le projet--> **Ajouter** --> **Un nouvel élément** --> Recherchez « OWIN ».  Le middleware OWIN appelle la `Configuration(…)` méthode au démarrage de votre application.
- Modifiez la déclaration de classe pour `public partial class Startup` -nous avons déjà mis en œuvre faisant partie de cette classe pour vous dans un autre fichier.  Dans le `Configuration(…)` méthode, effectuer un appel à ConfgureAuth(...) pour configurer l’authentification pour votre application web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

- Ouvrez le fichier `App_Start\Startup.Auth.cs` et mettre en œuvre la `ConfigureAuth(…)` méthode qui configurera l’API Web d’accepter de jetons à partir de la version 2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

- Vous pouvez désormais utiliser `[Authorize]` attributs pour protéger vos contrôleurs et vos actions avec une authentification OAuth 2.0 au porteur.  Décorer le `Controllers\TodoListController.cs` classe avec une balise d’autoriser.  Cela forcera l’utilisateur à se connecter avant d’accéder à cette page.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Quand un appelant autorisé avec succès appelle l’une de le `TodoListController` API, l’action avoir besoin d’accéder à des informations sur l’appelant.  OWIN fournit un accès aux revendications dans le jeton de support via le `ClaimsPrincpal` objet.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-   Enfin, ouvrez le `web.config` de fichiers dans la racine du projet TodoListService et entrez vos valeurs de configuration de le `<appSettings>` section.
  - Votre `ida:Audience` est l' **Id de l’Application** de l’application que vous avez entré dans le portail.

## <a name="configure-the-client-app"></a>Configurer l’application cliente
Avant de pouvoir afficher le Service de la liste Todo en action, vous devez configurer le Client de la liste Todo afin de pouvoir obtenir des jetons à partir de la version 2.0 et effectuer des appels vers le service.

- Dans le projet TodoListClient, ouvrez `App.config` et entrez vos valeurs de configuration de le `<appSettings>` section.
  - Votre `ida:ClientId` Id de l’Application vous avez copié à partir du portail.

Enfin, nettoyer, générez et exécutez chaque projet !  Vous avez maintenant une API Web MVC .NET qui accepte des jetons des deux comptes personnels de Microsoft et les comptes de travail ou l’école.  Vous connecter à la TodoListClient et appeler votre site web api pour ajouter des tâches à la liste des actions de l’utilisateur.

Pour référence, l’exemple complet (sans les valeurs de configuration) [est fourni sous la forme d’un fichier zip ici](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), ou vous pouvez le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez désormais déplacer vers des rubriques supplémentaires.  Vous souhaiterez peut-être essayer :

[L’appel d’une API de Web à partir d’une application Web >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Pour obtenir des ressources supplémentaires, consultez :
- [Le guide du développeur v2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow « azure-active directory » balise >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous encourageons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et de s’abonner à l’alerte de sécurité.
