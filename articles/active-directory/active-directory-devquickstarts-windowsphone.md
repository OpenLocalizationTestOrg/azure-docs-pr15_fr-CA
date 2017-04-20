<properties
    pageTitle="Azure AD Windows Phone en route | Microsoft Azure"
    description="Comment générer une application Windows Phone qui s’intègre avec Azure AD pour l’ouverture de session et appelle AD Azure protégé d’API utilisant OAuth."
    services="active-directory"
    documentationCenter="windows"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>



# <a name="integrate-azure-ad-with-a-windows-phone-app"></a>Intégrer AD Azure avec une application de Windows Phone

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si vous développez une application Windows Phone 8.1, AD Azure rend simple et directe pour vous authentifier vos utilisateurs avec leurs comptes Active Directory.  Il permet également à votre application d’utiliser en toute sécurité de n’importe quel web API protégé par AD Azure, telles que les API d’Office 365 ou l’API d’Azure.

> [AZURE.NOTE] Cet exemple de code utilise la version 2.0 ADAL.  Pour la toute dernière technologie, vous souhaiterez peut-être plutôt essayer notre [didacticiel universel de Windows à l’aide de ADAL v3.0](active-directory-devquickstarts-windowsstore.md).  Si vous générez en fait une application pour Windows Phone 8.1, il s’agit de l’emplacement.  ADAL v2.0 est toujours entièrement compatible avec, et est la méthode recommandée de développement d’applications agianst Windows Phone 8.1 à l’aide de AD Azure.

Pour .NET natifs clients qui doivent accéder aux ressources protégées, AD Azure fournit la bibliothèque de l’authentification Active Directory ou ADAL.  Seul but de ADAL dans la vie est de faciliter votre application obtenir les jetons d’accès.  Pour illustrer simplement combien il est facile, ici, nous créerons un Windows Phone 8.1 app « répertoire Searcher » qui :

-   Obtient accès jetons pour l’appel de l’API d’Azure AD graphique utilisant le [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Recherche dans un répertoire pour les utilisateurs avec un nom UPN donné.
-   Signe les utilisateurs.

Pour générer l’application complète de travail, vous devez :

2. Inscrire l’application dans Azure AD.
3. Installer et configurer les ADAL.
5. Utilisez ADAL pour obtenir des jetons à partir d’AD Azure.

Pour démarrer, [Téléchargez un squelette de projet](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Chacune est une solution Visual Studio 2013.  Vous aurez également besoin d’un locataire AD Azure dans lequel vous pouvez créer des utilisateurs et enregistrer une application.  Si vous ne disposez pas d’un client, à [savoir comment en obtenir un](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>*1. enregistrer l’Application de recherche de répertoire*
Pour permettre à votre application obtenir des jetons, vous devrez tout d’abord l’enregistrer dans votre client AD Azure et de lui accorder l’autorisation d’accéder à l’API Azure AD graphique :

-   Connexion au [portail de gestion Azure](https://manage.windowsazure.com)
-   Dans nav de la main gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lequel l’application.
-   Cliquez sur l’onglet **Applications** , cliquez sur **Ajouter** dans le bac d’alimentation inférieur.
-   Suivez les invites et créez une nouvelle **Application Client d’origine**.
    -   Le **nom** de l’application décrit votre application aux utilisateurs finals
    -   L' **Uri de redirection** est une combinaison de schéma et de chaîne AD Azure permet de renvoyer les réponses de jetons.  Permet d’entrer une valeur d’espace réservé pour le moment, par exemple, `http://DirectorySearcher`.  Nous allons remplacer cette valeur ultérieurement.
-   Une fois l’enregistrement terminé, DAS affecter à votre application un identificateur de client unique.  Vous aurez besoin de cette valeur dans les sections suivantes, donc copier à partir de l’onglet **configurer** .
- Également dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ».  Pour l’application « Azure Active Directory », ajoutez l’autorisation de **l’annuaire de l’organisation de votre accès** sous **Autorisations d’accès délégué**.  Cela permettra à votre application à l’API de graphique pour les utilisateurs de la requête.

## <a name="2-install--configure-adal"></a>*2. installer et configurer ADAL*
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer ADAL et écrire votre code liées à l’identité.  Pour ADAL être en mesure de communiquer avec Active Directory Azure, vous devez lui fournir des informations sur l’inscription de l’application.
-   Commencez par ajouter ADAL au projet à l’aide de la Console du Gestionnaire de package de DirectorySearcher.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   Dans le projet de DirectorySearcher, ouvrez `MainPage.xaml.cs`.  Remplacez les valeurs dans le `Config Values` région afin de refléter les valeurs que vous entrez dans le portail Azure.  Votre code font référence à ces valeurs lorsqu’elle utilise ADAL.
    -   Le `tenant` est le domaine de vos clients AD Azure, par exemple : contoso.onmicrosoft.com
    -   Le `clientId` est l’identifiant du client de votre application, vous avez copié à partir du portail.
-   Vous devez à présent découvrir l’uri de rappel pour votre application Windows Phone.  Définir un point d’arrêt sur cette ligne de la `MainPage` méthode :

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Exécutez l’application et copiez mis de côté la valeur de `redirectUri` lorsque le point d’arrêt est atteint.  Il doit ressembler à

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- Sur l’onglet de **configuration** de votre application dans le portail de gestion Azure, remplacez la valeur de la **RedirectUri** avec cette valeur.  

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Utilisez ADAL pour obtenir les jetons de DAS*
Le principe de base derrière l’ADAL est que chaque fois que votre application a besoin d’un jeton d’accès, il appelle simplement `authContext.AcquireToken(…)`, et ADAL s’occupe du reste.  

-   La première étape consiste à initialiser votre application `AuthenticationContext` -ADAL de la classe principale.  Il s’agit d’où vous passez ADAL les coordonnées, il doit communiquer avec Active Directory Azure et lui indiquer comment mettre en cache des jetons.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

- Recherchez maintenant la `Search(...)` méthode qui sera appelée lorsque le bouton des cliks utilisateur la « recherche » dans l’interface utilisateur de l’application.  Cette méthode effectue une demande GET à l’API de Azure AD graphique pour la requête pour les utilisateurs dont UPN commence par le terme de recherche donné.  Mais pour l’API de graphique d’une requête, vous devez inclure un access_token dans les `Authorization` en-tête de la demande - c’est là qu’intervient ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
- Si l’authentification interactive est nécessaire, ADAL utilisera du Windows Phone Web d’authentification Broker (WAB) et page de connexion de [modèle de continuation](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) pour afficher la publicité Azure.  Lorsque l’utilisateur se connecte, votre application doit passer ADAL les résultats de l’interaction du carnet d’adresses.  C’est aussi simple que la mise en œuvre de la `ContinueWebAuthentication` interface :

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

- Il est temps d’utiliser la `AuthenticationResult` qui ADAL retournée à votre application.  Dans le `QueryGraph(...)` de rappel, attacher l’access_token que vous avez acquis à la demande GET dans l’en-tête d’autorisation :

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
- Vous pouvez également utiliser le `AuthenticationResult` objet pour afficher des informations sur l’utilisateur dans votre application. Dans le `QueryGraph(...)` méthode, le résultat permet d’afficher l’id de l’utilisateur sur la page :

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Enfin, vous pouvez utiliser ADAL pour vous connecter à l’utilisateur de l’application ainsi.  Lorsque l’utilisateur clique sur le bouton « Déconnecter », nous souhaitons vous assurer que le prochain appel à `AcquireTokenSilentAsync(...)` échoue.  Avec ADAL, c’est aussi simple que d’effacer le cache de jetons :

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Félicitations ! Vous avez un application Windows Phone qui a la capacité d’authentifier les utilisateurs, d’appeler en toute sécurité les API Web à l’aide de OAuth 2.0 du travail et obtenez des informations de base sur l’utilisateur.  Si vous ne l’avez pas déjà, il est temps de remplir vos clients avec certains utilisateurs.  Exécutez votre application DirectorySearcher et vous connecter avec un de ces utilisateurs.  Rechercher d’autres utilisateurs en fonction de leur UPN.  Fermez l’application et exécutez de nouveau.  Notez comment la session de l’utilisateur reste intacte.  Vous déconnecter et vous reconnecter en tant qu’un autre utilisateur.

ADAL facilite l’intégration de toutes ces fonctionnalités d’identité commune dans votre application.  Il s’occupe de tout le travail pour vous - gestion du cache, la prise en charge de protocoles d’OAuth, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation de jetons a expiré et bien plus encore.  Tout ce que vous devez vraiment savoir est un seul appel d’API, `authContext.AcquireToken*(…)`.

Pour référence, l’exemple complet (sans les valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Vous pouvez désormais vous déplacer aux scénarios d’identité supplémentaire.  Vous souhaiterez peut-être essayer :

[Sécuriser un site Web .NET API avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]