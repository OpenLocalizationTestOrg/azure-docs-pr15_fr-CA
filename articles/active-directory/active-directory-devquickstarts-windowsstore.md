<properties
    pageTitle="Magasin de Windows Azure AD route | Microsoft Azure"
    description="Comment générer une application Windows Store qui s’intègre avec Azure AD pour l’ouverture de session et appelle AD Azure protégé d’API utilisant OAuth."
    services="active-directory"
    documentationCenter="windows"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-with-a-windows-store-app"></a>Intégrer AD Azure avec une application du Windows Store

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si vous développez une application pour le Windows Store, AD Azure rend simple et directe pour vous authentifier vos utilisateurs avec leurs comptes Active Directory.  Il permet également à votre application d’utiliser en toute sécurité de n’importe quel web API protégé par AD Azure, telles que les API d’Office 365 ou l’API d’Azure.

Pour les applications bureau qui ont besoin d’accéder aux ressources protégées, AD Azure fournit la bibliothèque de l’authentification Active Directory ou ADAL.  Seul but de ADAL dans la vie est de faciliter votre application obtenir les jetons d’accès.  Pour illustrer simplement combien il est facile, ici, nous créerons une application « Recherche de répertoire » Windows Store qui :

-   Obtient accès jetons pour l’appel de l’API d’Azure AD graphique utilisant le [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Recherche dans un répertoire pour les utilisateurs avec un nom UPN donné.
-   Signe les utilisateurs.

Pour générer l’application complète de travail, vous devez :

2. Inscrire l’application dans Azure AD.
3. Installer et configurer les ADAL.
5. Utilisez ADAL pour obtenir des jetons à partir d’AD Azure.

Pour démarrer, [Téléchargez un squelette de projet](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  Chacune est une solution Visual Studio 2015.  Vous aurez également besoin d’un locataire AD Azure dans lequel vous pouvez créer des utilisateurs et enregistrer une application.  Si vous ne disposez pas d’un client, à [savoir comment en obtenir un](active-directory-howto-tenant.md).

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
- Également dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ».  Pour l’application « Azure Active Directory », ajoutez l’autorisation **d’accès du répertoire que l’utilisateur connecté** sous **Autorisations d’accès délégué**.  Cela permettra à votre application à l’API de graphique pour les utilisateurs de la requête.

## <a name="2-install--configure-adal"></a>*2. installer et configurer ADAL*
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer ADAL et écrire votre code liées à l’identité.  Pour ADAL être en mesure de communiquer avec Active Directory Azure, vous devez lui fournir des informations sur l’inscription de l’application.
-   Commencez par ajouter ADAL au projet à l’aide de la Console du Gestionnaire de package de DirectorySearcher.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   Dans le projet de DirectorySearcher, ouvrez `MainPage.xaml.cs`.  Remplacez les valeurs dans le `Config Values` région afin de refléter les valeurs que vous entrez dans le portail Azure.  Votre code font référence à ces valeurs lorsqu’elle utilise ADAL.
    -   Le `tenant` est le domaine de vos clients AD Azure, par exemple : contoso.onmicrosoft.com
    -   Le `clientId` est l’identifiant du client de votre application, vous avez copié à partir du portail.
-   Vous devez à présent découvrir l’uri de rappel pour votre application du Windows Store.  Définir un point d’arrêt sur cette ligne de la `MainPage` méthode :

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Générez la solution, en veillant à ce que toutes les références du lot sont restaurés.  S’il manque des packages, ouvrez Configuration du Gestionnaire de package Nuget et restaurer les packages.
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

    authContext = new AuthenticationContext(authority);
}
```

- Recherchez maintenant la `Search(...)` méthode qui sera appelée lorsque l’utilisateur clique sur le bouton « Rechercher » dans l’interface utilisateur de l’application.  Cette méthode effectue une demande GET à l’API de Azure AD graphique pour la requête pour les utilisateurs dont UPN commence par le terme de recherche donné.  Mais pour l’API de graphique d’une requête, vous devez inclure un access_token dans les `Authorization` en-tête de la demande - c’est là qu’intervient ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
        }
        return;
    }
    ...
}
```
- Lorsque votre application demande un jeton en appelant `AcquireTokenAsync(...)`, ADAL tente de retourner un jeton sans demander à l’utilisateur des informations d’identification.  Si ADAL détermine que l’utilisateur doit se connecter afin d’obtenir un jeton, s’afficher une boîte de dialogue d’ouverture de session, collecter les informations d’identification de l’utilisateur et retourne un jeton que si l’authentification aboutit.  Si ADAL ne peut pas retourner un jeton pour une raison quelconque, le `AuthenticationResult` statut est une erreur.

- Il est temps d’utiliser le l’access_token que vous venez d’acquérir.  Également dans le `Search(...)` méthode, joignez le jeton à la demande d’obtenir des API de graphique dans l’en-tête d’autorisation :

```C#
// Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

```
- Vous pouvez également utiliser le `AuthenticationResult` objet pour afficher des informations sur l’utilisateur dans votre application, comme l’id de l’utilisateur :

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Enfin, vous pouvez utiliser ADAL pour vous connecter à l’utilisateur de l’application ainsi.  Lorsque l’utilisateur clique sur le bouton « Déconnecter », nous souhaitons vous assurer que le prochain appel à `AcquireTokenAsync(...)` affiche un signe dans la vue.  Avec ADAL, c’est aussi simple que d’effacer le cache de jetons :

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Félicitations ! Vous avez un application du Windows Store qui a la capacité d’authentifier les utilisateurs, d’appeler en toute sécurité les API Web à l’aide de OAuth 2.0 du travail et obtenez des informations de base sur l’utilisateur.  Si vous ne l’avez pas déjà, il est temps de remplir vos clients avec certains utilisateurs.  Exécutez votre application DirectorySearcher et vous connecter avec un de ces utilisateurs.  Rechercher d’autres utilisateurs en fonction de leur UPN.  Fermez l’application et exécutez de nouveau.  Notez comment la session de l’utilisateur reste intacte.  Se déconnecter (par un clic droit pour afficher la barre inférieure) et vous reconnecter en tant qu’un autre utilisateur.

ADAL facilite l’intégration de toutes ces fonctionnalités d’identité commune dans votre application.  Il s’occupe de tout le travail pour vous - gestion du cache, la prise en charge de protocoles d’OAuth, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation de jetons a expiré et bien plus encore.  Tout ce que vous devez vraiment savoir est un seul appel d’API, `authContext.AcquireToken*(…)`.

Pour référence, l’exemple complet (sans les valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  Vous pouvez désormais vous déplacer aux scénarios d’identité supplémentaire.  Vous souhaiterez peut-être essayer :

[Sécuriser un site Web .NET API avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
