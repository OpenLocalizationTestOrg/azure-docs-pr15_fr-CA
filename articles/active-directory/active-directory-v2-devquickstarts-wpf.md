<properties
pageTitle="Application Native du .NET v2.0 Azure Active Directory | Microsoft Azure"
description="Comment créer une application native de .NET qui signe les comptes d’utilisateurs avec les deux Account Microsoft et travail ou l’école."
services="active-directory"
documentationCenter=""
authors="dstrockis"
manager="mbaldwin"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="dotnet"
ms.topic="article"
ms.date="07/30/2016"
ms.author="dastrock; vittorib"/>

# <a name="add-sign-in-to-a-windows-desktop-app"></a>Ajoutez connexion à une application de bureau Windows

Avec le point de terminaison v2.0, vous pouvez rapidement ajouter l’authentification pour vos applications de bureau avec prise en charge pour les deux comptes personnels de Microsoft et les comptes de travail ou l’école.  Il permet également de votre application à communiquer de manière sécurisée avec un site web d’arrière-guichet api, ainsi que [Le graphique Microsoft](https://graph.microsoft.io) et quelques-uns des [API unifiée d’Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE] Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory (AD) sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

Pour [.NET des applications natives qui s’exécutent sur un périphérique](active-directory-v2-flows.md#mobile-and-native-apps), AD Azure fournit la bibliothèque d’authentification d’identité, ou MSAL.  Seul but du MSAL dans la vie est de faciliter votre application obtenir des jetons pour appeler des services web.  Pour illustrer simplement combien il est facile, ici, nous créerons une application de liste de tâches de WPF .NET qui :

- L’utilisateur s’inscrit en & obtient accès jetons en utilisant le [protocole d’authentification OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow).
- En toute sécurité appelle un service web de liste de tâches, qui est également garanti par OAuth 2.0 de back-end.
- Déconnecte l’utilisateur.

## <a name="download-sample-code"></a>Télécharger les exemples de code

Le code de ce didacticiel est maintenue [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Pour suivre l’exemple, vous pouvez [Télécharger le squelette de l’application sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) ou cloner le squelette :

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

L’application terminée est fournie à la fin de ce didacticiel ainsi.

## <a name="register-an-app"></a>Enregistrer une application
Créer une nouvelle application à [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Copie de l' **Id de l’Application** affectée à votre application, vous en aurez besoin plus rapidement.
- Ajouter la plate-forme **Mobile** pour votre application.

## <a name="install--configure-msal"></a>Installer et configurer MSAL
Maintenant que vous avez une application qui inscrite auprès de Microsoft, vous pouvez installer MSAL et écrire votre code liées à l’identité.  Pour MSAL être en mesure de communiquer le point de terminaison v2.0, vous devez lui fournir des informations sur l’inscription de l’application.

-   Commencez par ajouter MSAL au projet TodoListClient à l’aide de la Console du Gestionnaire de package.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

-   Dans le projet TodoListClient, ouvrez `app.config`.  Remplacez les valeurs des éléments dans le `<appSettings>` section reflète les valeurs que vous entrez dans le portail de l’enregistrement d’app.  Votre code font référence à ces valeurs lorsqu’elle utilise MSAL.
    -   Le `ida:ClientId` est l' **Id de l’Application** de votre application, vous avez copié à partir du portail.

- Dans le projet de liste des tâches-Service, ouvrez `web.config` dans la racine du projet.  
    - Remplacer le `ida:Audience` valeur avec le même **Id de l’Application** à partir du portail.

## <a name="use-msal-to-get-tokens"></a>MSAL permet d’obtenir des jetons
Le principe de base derrière le MSAL est que chaque fois que votre application a besoin d’un jeton d’accès, vous appelez simplement `app.AcquireToken(...)`, et MSAL s’occupe du reste.  

-   Dans le `TodoListClient` ouvert le projet, `MainWindow.xaml.cs` et recherchez la `OnInitialized(...)` méthode.  La première étape consiste à initialiser votre application `PublicClientApplication` -classe principale de MSAL représentant les applications natives.  Il s’agit d’où vous passez MSAL, les coordonnées dont il a besoin pour communiquer avec Active Directory Azure et lui indiquer comment mettre en cache des jetons.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

- Lorsque l’application démarre, nous voulons vérifier et voir si l’utilisateur est déjà connecté à l’application.  Toutefois, nous ne voulons pas appeler une interface de connexion encore : nous allons faire de l’utilisateur cliquez sur « connexion » pour le faire.  Également dans le `OnInitialized(...)` méthode :

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.
    
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

- Si l’utilisateur n’est pas connecté et ils cliquent sur le bouton « Sign In », nous voulons appeler une interface utilisateur de connexion et que l’utilisateur à entrer leurs informations d’identification.  Implémenter le Gestionnaire de bouton ouverture de session :

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

- Si l’utilisateur a des signes de MSAL recevra et mettre en cache un jeton pour vous et vous pouvez continuer à appeler les `GetTodoList()` méthode en toute confiance.  Tout ce qui reste pour obtenir les tâches d’un utilisateur consiste à implémenter le `GetTodoList()` méthode.

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Exécuter

Félicitations ! Vous disposez maintenant d’une application WPF de .NET de travail qui a la capacité d’authentifier les utilisateurs et d’appeler en toute sécurité les API Web à l’aide de OAuth 2.0.  Exécuter à la fois vos projets et vous connecter avec un compte Microsoft personnel ou un compte de travail ou l’école.  Ajouter des tâches à la liste des actions de cet utilisateur.  Vous déconnecter et vous reconnecter en tant qu’un autre utilisateur pour afficher la liste des tâches.  Fermez l’application et exécutez de nouveau.  Notez la façon dont la session utilisateur reste intacte, c’est parce que l’application met en cache les jetons dans un fichier local.

MSAL facilite à incorporer des fonctionnalités d’identité commune dans votre application, à l’aide de comptes personnelles et professionnelle.  Il s’occupe de tout le travail pour vous - gestion du cache, la prise en charge de protocoles d’OAuth, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation de jetons a expiré et bien plus encore.  Tout ce que vous devez vraiment savoir est un seul appel d’API, `app.AcquireTokenAsync(...)`.

Pour référence, l’exemple complet (sans les valeurs de configuration) [est fourni sous la forme d’un fichier zip ici](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), ou vous pouvez le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais déplacer sur des sujets plus avancés.  Vous souhaiterez peut-être essayer :

- [Sécurisation de l’API Web de TodoListService avec le point de terminaison v2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Pour obtenir des ressources supplémentaires, consultez :  

- [Le guide du développeur v2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow « msal » balise >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous encourageons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et de s’abonner à l’alerte de sécurité.
