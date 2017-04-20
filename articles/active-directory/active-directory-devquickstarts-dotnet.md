<properties
    pageTitle=".NET AD Azure route | Microsoft Azure"
    description="Comment générer une application de bureau Windows de .NET qui s’intègre avec Azure AD pour l’ouverture de session et appelle Azure AD protégé d’API utilisant OAuth."
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


# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Intégrer AD Azure dans une application WPF de bureau de Windows

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si vous développez une application de bureau, AD Azure rend simple et directe pour vous authentifier vos utilisateurs avec leurs comptes Active Directory.  Il permet également à votre application d’utiliser en toute sécurité de n’importe quel web API protégé par AD Azure, telles que les API d’Office 365 ou l’API d’Azure.

Pour .NET natifs clients qui doivent accéder aux ressources protégées, AD Azure fournit la bibliothèque de l’authentification Active Directory ou ADAL.  Seul but de ADAL dans la vie est de faciliter votre application obtenir les jetons d’accès.  Pour illustrer simplement combien il est facile, ici, nous créerons une application de liste de tâches de WPF .NET qui :

-   Obtient accès jetons pour l’appel de l’API d’Azure AD graphique utilisant le [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Recherche dans un répertoire pour les utilisateurs disposant d’un alias donné.
-   Signe les utilisateurs.

Pour générer l’application complète de travail, vous devez :

2. Inscrire l’application dans Azure AD.
3. Installer et configurer les ADAL.
5. Utilisez ADAL pour obtenir des jetons à partir d’AD Azure.

Pour démarrer, [Téléchargez le squelette d’application](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Vous aurez également besoin d’un locataire AD Azure dans lequel vous pouvez créer des utilisateurs et enregistrer une application.  Si vous ne disposez pas d’un client, à [savoir comment en obtenir un](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. enregistrer l’Application DirectorySearcher*
Pour permettre à votre application obtenir des jetons, vous devrez tout d’abord l’enregistrer dans votre client AD Azure et de lui accorder l’autorisation d’accéder à l’API Azure AD graphique :

-   Signe dans le portail de gestion Azure
-   Dans nav de la main gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lequel l’application.
-   Cliquez sur l’onglet **Applications** , cliquez sur **Ajouter** dans le bac d’alimentation inférieur.
-   Suivez les invites et créez une nouvelle **Application Client d’origine**.
    -   Le **nom** de l’application décrit votre application aux utilisateurs finals
    -   L' **Uri de redirection** est une combinaison de schéma et de chaîne AD Azure permet de renvoyer les réponses de jetons.  Permet d’entrer une valeur spécifique à votre application, par exemple, `http://DirectorySearcher`.
-   Une fois l’enregistrement terminé, DAS affecter à votre application un identificateur de client unique.  Vous aurez besoin de cette valeur dans les sections suivantes, donc copier à partir de l’onglet **configurer** .
- Également dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ».  Pour l’application « Azure Active Directory », ajoutez l’autorisation de **l’annuaire de l’organisation de votre accès** sous **Autorisations d’accès délégué**.  Cela permettra à votre application à l’API de graphique pour les utilisateurs de la requête.

## <a name="2-install--configure-adal"></a>*2. installer et configurer ADAL*
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer ADAL et écrire votre code liées à l’identité.  Pour ADAL être en mesure de communiquer avec Active Directory Azure, vous devez lui fournir des informations sur l’inscription de l’application.
-   Commencez par ajouter ADAL au projet à l’aide de la Console du Gestionnaire de package de DirectorySearcher.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   Dans le projet de DirectorySearcher, ouvrez `app.config`.  Remplacez les valeurs des éléments dans les `<appSettings>` section selon les valeurs que vous entrez dans le portail Azure.  Votre code font référence à ces valeurs lorsqu’elle utilise ADAL.
    -   Le `ida:Tenant` est le domaine de vos clients AD Azure, par exemple : contoso.onmicrosoft.com
    -   Le `ida:ClientId` est l’identifiant du client de votre application, vous avez copié à partir du portail.
    -   Le `ida:RedirectUri` est la redirection url que vous avez enregistré dans le portail.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Utilisez ADAL pour obtenir les jetons de DAS*
Le principe de base derrière l’ADAL est que chaque fois que votre application a besoin d’un jeton d’accès, il appelle simplement `authContext.AcquireTokenAsync(...)`, et ADAL s’occupe du reste.  

-   Dans le `DirectorySearcher` ouvert le projet, `MainWindow.xaml.cs` et recherchez la `MainWindow()` méthode.  La première étape consiste à initialiser votre application `AuthenticationContext` -ADAL de la classe principale.  Il s’agit d’où vous passez ADAL les coordonnées, il doit communiquer avec Active Directory Azure et lui indiquer comment mettre en cache des jetons.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

- Recherchez maintenant la `Search(...)` méthode qui sera appelée lorsque le bouton des cliks utilisateur la « recherche » dans l’interface utilisateur de l’application.  Cette méthode effectue une demande GET à l’API de Azure AD graphique pour la requête pour les utilisateurs dont UPN commence par le terme de recherche donné.  Mais pour l’API de graphique d’une requête, vous devez inclure un access_token dans les `Authorization` en-tête de la demande - c’est là qu’intervient ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- Lorsque votre application demande un jeton en appelant `AcquireTokenAsync(...)`, ADAL tente de retourner un jeton sans demander à l’utilisateur des informations d’identification.  Si ADAL détermine que l’utilisateur doit se connecter afin d’obtenir un jeton, s’afficher une boîte de dialogue d’ouverture de session, collecter les informations d’identification de l’utilisateur et retourne un jeton que si l’authentification aboutit.  Si ADAL ne peut pas retourner un jeton pour une raison quelconque, elle génère une `AdalException`.
- Notez que la `AuthenticationResult` objet contient un `UserInfo` objet qui peut être utilisé pour collecter des informations de votre application peut nécessiter.  Dans le composant DirectorySearcher, `UserInfo` est utilisé pour personnaliser l’interface utilisateur de l’application avec l’id de l’utilisateur.

- Lorsque l’utilisateur clique sur le bouton « Déconnecter », nous souhaitons vous assurer que le prochain appel à `AcquireTokenAsync(...)` demande à l’utilisateur pour vous connecter.  Avec ADAL, c’est aussi simple que d’effacer le cache de jetons :

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- Toutefois, si l’utilisateur ne clique pas le bouton « Déconnecter », vous devez maintenir la session de l’utilisateur pour la prochaine fois qu’ils exécutent le composant DirectorySearcher.  Lorsque l’application est lancée, vous pouvez vérifier le cache de jetons de ADAL pour un jeton existant et la mise à jour de l’interface utilisateur en conséquence.  Dans le `CheckForCachedToken()` méthode, effectuer un autre appel à `AcquireTokenAsync(...)`, cette fois en passant dans le `PromptBehavior.Never` paramètre.  `PromptBehavior.Never`indiquera ADAL que l’utilisateur ne doit pas être invité pour ouvrir une session et ADAL doivent à la place une exception si elle ne peut pas retourner un jeton.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Félicitations ! Vous avez une application WPF de .NET qui a la capacité d’authentifier les utilisateurs, d’appeler en toute sécurité les API Web à l’aide de OAuth 2.0 de travail et obtenez des informations de base sur l’utilisateur.  Si vous ne l’avez pas déjà, il est temps de remplir vos clients avec certains utilisateurs.  Exécutez votre application DirectorySearcher et vous connecter avec un de ces utilisateurs.  Rechercher d’autres utilisateurs en fonction de leur UPN.  Fermez l’application et exécutez de nouveau.  Notez comment la session de l’utilisateur reste intacte.  Vous déconnecter et vous reconnecter en tant qu’un autre utilisateur.

ADAL facilite l’intégration de toutes ces fonctionnalités d’identité commune dans votre application.  Il s’occupe de tout le travail pour vous - gestion du cache, la prise en charge de protocoles d’OAuth, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation de jetons a expiré et bien plus encore.  Tout ce que vous devez vraiment savoir est un seul appel d’API, `authContext.AcquireTokenAsync(...)`.

Pour référence, l’exemple complet (sans les valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Vous pouvez désormais déplacer d’autres scénarios.  Vous souhaiterez peut-être essayer :

[Sécuriser un site Web .NET API avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
