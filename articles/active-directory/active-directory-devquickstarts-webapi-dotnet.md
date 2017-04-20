<properties
    pageTitle=".NET AD Azure route | Microsoft Azure"
    description="La création d’une API Web MVC .NET qui s’intègre avec Azure AD pour l’authentification et l’autorisation."
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


# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>Protéger une API Web à l’aide des jetons porteur d’Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si vous créez une application qui fournit l’accès à des ressources protégées, vous devrez savoir comment protéger ces ressources contre tout accès injustifiés.
Annonce Azure rend très simple protéger un site web à l’aide des jetons d’accès OAuth PORTEUR 2.0 avec seulement quelques lignes de code des API.

Dans les applications web Asp.NET, vous pouvez le faire à l’aide de l’implémentation Microsoft de la Communauté OWIN logiciels intermédiaires inclus dans.NET Framework 4.5.  Ici, nous allons utiliser OWIN pour créer un site web « Liste des tâches » API qui :
-   Désigne les API est protégés.
-   Vérifie que les appels de l’API Web contient un jeton d’accès valide.

Pour ce faire, vous devez :

1. Inscrire une application avec Azure AD
2. Configurez votre application pour utiliser le pipeline de l’authentification OWIN.
3. Configurer une application cliente pour appeler l’à faire liste Web API

Pour démarrer, [Téléchargez le squelette d’application](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Chacune est une solution Visual Studio 2013.  Vous aurez également besoin d’un locataire AD Azure dans lequel vous souhaitez enregistrer votre application.  Si vous n’en avez pas déjà fait, [Apprenez à en obtenir un](active-directory-howto-tenant.md).


## <a name="1--register-an-application-with-azure-ad"></a>*1. inscription d’une Application avec Azure AD*
Pour sécuriser votre application, vous devrez tout d’abord créer une application dans votre client et de fournir quelques informations essentielles Azure AD.

-   Connexion au [portail de gestion Azure](https://manage.windowsazure.com)
-   Dans nav de la main gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lequel l’application.
-   Cliquez sur l’onglet **Applications** , cliquez sur **Ajouter** dans le bac d’alimentation inférieur.
-   Suivez les invites et créer une nouvelle **Application Web et/ou WebAPI**.
    -   Le **nom** de l’application décrit votre application aux utilisateurs finals.  Entrez « Pour la liste des tâches Service ».
    -   L' **Uri de redirection** est une combinaison de schéma et de chaîne par AD Azure pour renvoyer des jetons votre application demandée. Entrez `https://localhost:44321/` pour cette valeur.
-   Une fois l’enregistrement terminé, accédez à l’onglet **configurer** et recherchez le champ **URI de ID App** .  Permet d’entrer un identificateur de clients spécifiques pour cette valeur, par exemple :`https://contoso.onmicrosoft.com/TodoListService`
- Enregistrer la configuration.  Laissez le portail ouvert - vous devez également enregistrer votre application cliente sous peu.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurer votre application pour utiliser le pipeline de l’authentification OWIN*

Maintenant que vous avez enregistré une application avec AD Azure, vous devez configurer votre application pour communiquer avec Active Directory Azure pour valider entrantes des demandes et des jetons.

-   Pour commencer, ouvrez la solution et ajouter les packages NuGet de middleware OWIN au projet TodoListService à l’aide de la Console du Gestionnaire de package.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-   Ajouter une classe de démarrage de OWIN du projet TodoListService appelé `Startup.cs`.  Droite cliquez sur le projet--> **Ajouter** --> **Un nouvel élément** --> Recherchez « OWIN ».  Le middleware OWIN appelle la `Configuration(…)` méthode au démarrage de votre application.
-   Modifiez la déclaration de classe pour `public partial class Startup` -nous avons déjà mis en œuvre faisant partie de cette classe pour vous dans un autre fichier.  Dans le `Configuration(…)` méthode, effectuer un appel à ConfgureAuth(...) pour configurer l’authentification pour votre application web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Ouvrez le fichier `App_Start\Startup.Auth.cs` et mettre en œuvre la `ConfigureAuth(…)` méthode.  Les paramètres que vous fournissez dans `WindowsAzureActiveDirectoryBearerAuthenticationOptions` servira de coordonnées de votre application pour communiquer avec Active Directory Azure.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-   Vous pouvez désormais utiliser `[Authorize]` attributs pour protéger vos contrôleurs et vos actions avec l’authentification de porteur JWT.  Décorer le `Controllers\TodoListController.cs` classe avec une balise d’autoriser.  Cela forcera l’utilisateur à se connecter avant d’accéder à cette page.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Quand un appelant autorisé avec succès appelle l’une de le `TodoListController` API, l’action avoir besoin d’accéder à des informations sur l’appelant.  OWIN fournit un accès aux revendications dans le jeton de support via le `ClaimsPrincpal` objet.  
- Une exigence courante pour les API de web est pour valider les présent dans le jeton « étendues », cela garantit que l’utilisateur final a consenti aux autorisations requises pour accéder au Service de la liste Todo :

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-   Enfin, ouvrez le `web.config` de fichiers dans la racine du projet TodoListService et entrez vos valeurs de configuration de le `<appSettings>` section.
  - Le `ida:Tenant` est le nom de vos clients AD Azure, par exemple, « contoso.onmicrosoft.com ».
  - Votre `ida:Audience` est l’URI d’application de l’ID de l’application que vous avez entré dans le portail Azure.

## <a name="3--configure-a-client-application--run-the-service"></a>*3. Configuration d’une application cliente et d’exécuter le service*
Avant de pouvoir afficher le Service de la liste Todo en action, vous devez configurer le Client de la liste Todo afin de pouvoir obtenir des jetons de DAS et effectuer des appels vers le service.

- Naviguer en arrière vers le [Portail de gestion Azure](https://manage.windowsazure.com)
- Créez une nouvelle application dans vos clients AD Azure et sélectionnez **l’Application de Client natif** dans l’invite qui en résulte.
    -   Le **nom** de l’application décrit votre application aux utilisateurs finals
    -   Entrez `http://TodoListClient/` de la valeur de **l’Uri de redirection** .
- Une fois l’enregistrement terminé, DAS affecter votre application unique **Id Client**. Vous aurez besoin de cette valeur dans les étapes suivantes, donc copier à partir de l’onglet Configurer.
- Également dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ». Cliquez sur « Ajouter l’Application ». Sélectionnez « Toutes les applications » dans la liste déroulante « Show », puis cliquez sur la case à cocher supérieure. Recherchez et cliquez sur votre à faire de liste Service et cliquez sur la coche en bas pour ajouter l’application. Sélectionnez « Accès à ne liste Service » dans la liste déroulante « Autorisations déléguées » et enregistrer la configuration.


- Dans Visual Studio, ouvrez `App.config` dans le TodoListClient de projet et entrez vos valeurs de configuration de le `<appSettings>` section.
  - Le `ida:Tenant` est le nom de vos clients AD Azure, par exemple, « contoso.onmicrosoft.com ».
  - Votre `ida:ClientId` ID de l’application vous avez copié à partir du portail Azure.
  - Votre `todo:TodoListResourceId` est l’URI d’application de l’ID de l’application à faire du Service de liste que vous avez entré dans le portail Azure.

Enfin, nettoyer, générez et exécutez chaque projet !  Si vous ne l’avez pas déjà, il est temps de créer un nouvel utilisateur dans vos clients avec une *. onmicrosoft.com domaine.  Connectez-vous au client de la liste des tâches associées à cet utilisateur et ajouter des tâches à liste l’utilisateur des tâches.

Pour référence, l’exemple complet (sans les valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Vous pouvez désormais déplacer d’autres scénarios d’identité supplémentaire.

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
