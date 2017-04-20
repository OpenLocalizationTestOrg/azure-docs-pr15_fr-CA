<properties
    pageTitle="Azure AD Xamarin mise en route | Microsoft Azure"
    description="Comment générer une application Xamarin s’intègre avec Azure AD pour l’ouverture de session et appelle AD Azure protégé d’API utilisant OAuth."
    services="active-directory"
    documentationCenter="xamarin"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-xamarin-app"></a>Intégrer AD Azure dans une application Xamarin

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin vous permet d’écrire des applications mobiles dans C# qui peut s’exécuter sur iOS, Android et Windows (ordinateurs et périphériques mobiles). Si vous créez une application à l’aide de Xamarin, AD Azure rend simple et directe pour vous authentifier vos utilisateurs avec leurs comptes Active Directory. Il permet également à votre application d’utiliser en toute sécurité de n’importe quel web API protégé par AD Azure, telles que les API d’Office 365 ou l’API d’Azure.

Pour les applications Xamarin qui a besoin d’accéder aux ressources protégées, AD Azure fournit la bibliothèque de l’authentification Active Directory ou ADAL. Seul but de ADAL dans la vie est de faciliter votre application obtenir les jetons d’accès. Pour illustrer simplement combien il est facile, ici, nous créerons une application « Recherche de répertoire » qui :

-   S’exécute sur iOS, Android, Windows Desktop, Windows Phone et Windows Store.
- Utilise une bibliothèque de classe portable unique (PCL) pour authentifier les utilisateurs et d’obtenir des jetons pour l’API Azure AD graphique
-   Recherche dans un répertoire pour les utilisateurs avec un nom UPN donné.

Pour générer l’application complète de travail, vous devez :

2. Configurer votre environnement de développement Xamarin
2. Inscrire l’application dans Azure AD.
3. Installer et configurer les ADAL.
5. Utilisez ADAL pour obtenir des jetons à partir d’AD Azure.

Pour démarrer, [Téléchargez un squelette de projet](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Chacune est une solution Visual Studio 2013. Vous aurez également besoin d’un locataire AD Azure dans lequel vous pouvez créer des utilisateurs et enregistrer une application. Si vous ne disposez pas d’un client, à [savoir comment en obtenir un](active-directory-howto-tenant.md).

## <a name="0-set-up-your-xamarin-development-environment"></a>*0. configurer votre environnement de développement Xamarin*
Dans la mesure où ce didacticiel inclut des projets pour iOS, Android et Windows, vous aurez besoin de Visual Studio et Xamarin ensemble. Pour créer l’environnement nécessaire, suivez les instructions complètes sur [l’installation et l’installer pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) sur MSDN. Ces instructions incluent le matériel, que vous pouvez consulter pour plus d’informations sur Xamarin pendant que vous attendez pour les programmes d’installation terminer. 

Une fois vous avez terminé la configuration nécessaire, ouvrez la solution dans Visual Studio pour commencer. Vous trouverez les six projets : cinq projets propres à la plate-forme et une bibliothèque de classes portable qui est partagée sur toutes les plates-formes,`DirectorySearcher.cs`

## <a name="1-register-the-directory-searcher-application"></a>*1. enregistrer l’Application de recherche de répertoire*
Pour permettre à votre application obtenir des jetons, vous devrez tout d’abord l’enregistrer dans votre client AD Azure et de lui accorder l’autorisation d’accéder à l’API Azure AD graphique :

-   Connexion au [portail de gestion Azure](https://manage.windowsazure.com)
-   Dans nav de la main gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lequel l’application.
-   Cliquez sur l’onglet **Applications** , cliquez sur **Ajouter** dans le bac d’alimentation inférieur.
-   Suivez les invites et créez une nouvelle **Application Client d’origine**.
    -   Le **nom** de l’application décrit votre application aux utilisateurs finals
    -   L' **Uri de redirection** est une combinaison de schéma et de chaîne AD Azure permet de renvoyer les réponses de jetons. Entrez une valeur, par exemple, `http://DirectorySearcher`.
-   Une fois l’enregistrement terminé, DAS affecter à votre application un identificateur de client unique. Vous aurez besoin de cette valeur dans les sections suivantes, donc copier à partir de l’onglet **configurer** .
- Également dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ». Pour l’application « Azure Active Directory », ajoutez l’autorisation de **l’annuaire de l’organisation de votre accès** sous **Autorisations d’accès délégué**. Cela permettra à votre application à l’API de graphique pour les utilisateurs de la requête.

## <a name="2-install--configure-adal"></a>*2. installer et configurer ADAL*
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer ADAL et écrire votre code liées à l’identité. Pour ADAL être en mesure de communiquer avec Active Directory Azure, vous devez lui fournir des informations sur l’inscription de l’application.
-   Commencez par ajouter ADAL à chacun des projets de la solution à l’aide de la Console du Gestionnaire de package.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- Notez que les deux références de la bibliothèque sont ajoutés à chaque projet - la partie PCL de ADAL et une partie spécifique de la plate-forme.

-   Dans le projet DirectorySearcherLib, ouvrez `DirectorySearcher.cs`. Modifier les valeurs de membre de classe pour refléter les valeurs que vous entrez dans le portail Azure. Votre code font référence à ces valeurs lorsqu’elle utilise ADAL.
    -   Le `tenant` est le domaine de vos clients AD Azure, par exemple : contoso.onmicrosoft.com
    -   Le `clientId` est l’identifiant du client de votre application, vous avez copié à partir du portail.
    - Le `returnUri` est la redirectUri que vous avez entré dans le portail, par exemple, `http://DirectorySearcher`.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Utilisez ADAL pour obtenir les jetons de DAS*
*Apparemment* tout de la logique d’authentification de l’application se trouve dans `DirectorySearcher.SearchByAlias(...)`. Tout ce qui est nécessaire dans les projets propres à la plate-forme est de passer un paramètre contextuel à la `DirectorySearcher` PCL.

- Ouvrez tout d’abord, `DirectorySearcher.cs` et ajoutez un nouveau paramètre à la `SearchByAlias(...)` méthode. `IPlatformParameters`est le paramètre contextuel qui encapsule les objets propres à la plate-forme qui ADAL doit effectuer l’authentification.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-   Initialisez ensuite la `AuthenticationContext` -ADAL de la classe principale. Il s’agit d’où vous passez ADAL les coordonnées dont il a besoin pour communiquer avec Active Directory Azure. Appelez ensuite `AcquireTokenAsync(...)`, qui accepte la `IPlatformParameters` objet et appelle le flux d’authentification nécessaire pour renvoyer un jeton à l’application.

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)`tout d’abord essaiera de renvoyer un jeton pour la ressource demandée (dans ce cas, l’API de graphique) sans inviter l’utilisateur à entrer leurs informations d’identification (via la mise en cache ou l’actualisation des jetons anciens). Uniquement si c’est nécessaire, il affichera l’utilisateur le signe AD Azure dans la page avant d’acquérir le jeton demandé.


- Vous pouvez ensuite joindre le jeton d’accès à la demande de l’API de graphique dans l’en-tête d’autorisation :

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

C’est tout pour le `DirectorySearcher` PCL et votre application d’identité code.  Il ne reste qu’à appeler les `SearchByAlias(...)` méthode dans les affichages de chaque plate-forme, et si nécessaire ajouter du code pour la gestion du cycle de vie de l’interface utilisateur correcte.

####<a name="android"></a>Android :
- Dans `MainActivity.cs`, ajoutez un appel à `SearchByAlias(...)` sur le bouton Gestionnaire de clic :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- Vous devez également remplacer la `OnActivityResult` méthode du cycle de vie pour transférer toute authentification redirige vers la méthode appropriée.  ADAL fournit une méthode d’assistance pour ce dans Android :

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####<a name="windows-desktop"></a>Bureau de Windows :
- Dans `MainWindow.xaml.cs`, il vous suffit de faire un appel à `SearchByAlias(...)` en passant un `WindowInteropHelper` dans du bureau `PlatformParameters` objet :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="ios"></a>e/s :
- Dans `DirSearchClient_iOSViewController.cs`, l’e/s `PlatformParameters` objet prend simplement une référence au contrôleur de la vue :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="windows-universal"></a>Universel de Windows :
- Dans Windows universel, ouvrez `MainPage.xaml.cs` et mettre en œuvre la `Search` méthode, qui utilise une méthode d’assistance dans un projet partagé, mise à jour de l’interface utilisateur si nécessaire.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Félicitations ! Vous disposez maintenant d’une application de Xamarin qui a la capacité d’authentifier les utilisateurs et d’appeler en toute sécurité les API Web à l’aide de OAuth 2.0 sur cinq différentes plates-formes de travail. Si vous ne l’avez pas déjà, il est temps de remplir vos clients avec certains utilisateurs. Exécutez votre application DirectorySearcher et vous connecter avec un de ces utilisateurs. Rechercher d’autres utilisateurs en fonction de leur UPN.

ADAL permet d’incorporer des fonctionnalités d’identité commune dans votre application. Il s’occupe de tout le travail pour vous - gestion du cache, la prise en charge de protocoles d’OAuth, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation de jetons a expiré et bien plus encore. Tout ce que vous devez vraiment savoir est un seul appel d’API, `authContext.AcquireToken*(…)`.

Pour référence, l’exemple complet (sans les valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Vous pouvez désormais vous déplacer aux scénarios d’identité supplémentaire. Vous souhaiterez peut-être essayer :

[Sécuriser un site Web .NET API avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
