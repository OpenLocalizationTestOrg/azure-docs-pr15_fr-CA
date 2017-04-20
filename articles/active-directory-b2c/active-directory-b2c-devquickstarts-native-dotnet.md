<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Comment générer une application de bureau Windows qui inclut signe, d’abonnement, et la gestion des profils à l’aide d’Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C : Créer une application de bureau Windows

À l’aide de B2C d’Azure Active Directory (AD Azure), vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissant à votre application de bureau en quelques étapes. Cet article vous explique comment créer une application de « liste de tâches » .NET Windows Presentation Foundation (WPF) qui comprend l’utilisateur, inscription, inscription et gestion des profils. L’application inclura une prise en charge de l’inscription et à la connexion à l’aide d’un nom d’utilisateur ou d’un e-mail. Il comprendra également prise en charge de l’inscription et à la connexion à l’aide de comptes sociaux tels que Facebook et Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire, ou client.  Un répertoire est un conteneur pour tous vos utilisateurs, applications, groupes et bien plus encore. Si vous n’avez pas encore, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de poursuivre dans ce guide.

## <a name="create-an-application"></a>Création d’une application

Ensuite, vous devez créer une application dans votre répertoire B2C. Ainsi, Azure informations dont il a besoin pour communiquer en toute sécurité avec votre application. Pour créer une application, suivez [les instructions](active-directory-b2c-app-registration.md).  Veillez à :

- Inclure un **client natif** dans l’application.
- Copier le **Rediriger URI** `urn:ietf:wg:oauth:2.0:oob`. Il s’agit de l’URL par défaut pour cet exemple de code.
- Copiez l' **ID de l’Application** qui est assigné à votre application. Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure B2C d’Active Directory, chaque expérience de l’utilisateur est défini par une [stratégie](active-directory-b2c-reference-policies.md). Cet exemple de code contient trois expériences d’identité : vous inscrire, vous connecter et modifier le profil. Vous devez créer une stratégie pour chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lorsque vous créez les trois stratégies, veillez à :

- Choisissez **L’ID utilisateur d’abonnement** ou **E-mail d’inscription** dans la lame de fournisseurs d’identité.
- Choisissez le **nom d’affichage** et d’autres attributs d’abonnement dans votre stratégie d’inscription.
- Choisissez les revendications de **nom complet** et **l’ID d’objet** en tant que revendications de l’application pour chaque stratégie. Vous pouvez choisir les autres déclarations.
- Copier le **nom** de chaque stratégie après que l’avoir créé. Il doit avoir le préfixe `b2c_1_`.  Vous devez ensuite ces noms de la stratégie.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois que vous avez créé les trois stratégies, vous êtes prêt à créer votre application.

## <a name="download-the-code"></a>Télécharger le code

Le code de ce didacticiel [est conservée sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Pour générer l’exemple à mesure que vous progressez, vous pouvez [télécharger un squelette de projet sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Vous pouvez également cloner le squelette :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

L’application terminée est également [disponible sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) ou sur le `complete` branche du même référentiel.

Après avoir téléchargé l’exemple de code, ouvrez le fichier .sln Visual Studio pour commencer. Le `TaskClient` projet est l’application de bureau WPF avec lequel l’utilisateur interagit. Pour les besoins de ce didacticiel, il s’appelle un web dorsal tâche API, hébergé dans Azure, qui stocke la liste des tâches de chaque utilisateur.  Vous n’avez pas besoin de générer l’API web, que vous l’avez déjà en cours d’exécution pour vous.

Pour savoir comment une API web authentifie en toute sécurité les demandes à l’aide d’Azure AD B2C, découvrez l' [API web mise en route l’article](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Exécuter les politiques
Votre application communique avec Azure AD B2C en envoyant des messages d’authentification qui spécifient la stratégie que souhaite exécuter dans le cadre de la demande HTTP. Pour les applications .NET, vous pouvez utiliser l’aperçu de la bibliothèque de l’authentification de Microsoft (MSAL) pour envoyer des messages d’authentification OAuth 2.0, exécuter les politiques et obtenir des jetons qui appellent les API de web.

### <a name="install-msal"></a>Installation de MSAL
Ajouter MSAL à la `TaskClient` projet à l’aide de la Console de Gestionnaire de package de Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Entrez les détails de votre B2C
Ouvrez le fichier `Globals.cs` et remplacer les valeurs de propriété par votre propre. Cette classe est utilisée dans l’ensemble de `TaskClient` aux valeurs de référence utilisé.

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### <a name="create-the-publicclientapplication"></a>Créer la PublicClientApplication
La classe principale de MSAL est `PublicClientApplication`. Cette classe représente votre application dans le système d’Azure AD B2C. Lorsque l’application initialise, créez une instance de `PublicClientApplication` dans `MainWindow.xaml.cs`. Cela peut être utilisé tout au long de la fenêtre.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };
    
    ...
```

### <a name="initiate-a-sign-up-flow"></a>Lancer un flux d’abonnement
Lorsqu’un utilisateur choisit de signes de, vous souhaitez lancer un flux d’abonnement qui utilise la stratégie d’inscription que vous avez créé. À l’aide de MSAL, vous appelez simplement `pca.AcquireTokenAsync(...)`. Les paramètres que vous passez à `AcquireTokenAsync(...)` déterminer quel jeton s’affiche, la stratégie utilisée dans la demande d’authentification et plus encore.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user 
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>Lancer un flux de connexion
Vous pouvez lancer un flux de connexion de la même façon que vous initiez un abonnement à un flux. Lorsqu’un utilisateur se connecte, effectuez le même appel à MSAL, cette fois à l’aide de votre stratégie de connexion :

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Lancer un flux de modifier le profil
Là encore, vous pouvez exécuter une stratégie modifier le profil de la même manière :

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

Dans tous ces cas, MSAL renvoie un jeton dans le `AuthenticationResult` ou lève une exception. Chaque fois que vous obtenez un jeton à partir de MSAL, vous pouvez utiliser la `AuthenticationResult.User` objet de mettre à jour les données de l’utilisateur dans l’application, telles que l’interface utilisateur. ADAL met également en cache le jeton pour une utilisation dans d’autres parties de l’application.


### <a name="check-for-tokens-on-app-start"></a>Vérifier les jetons sur le lancement de l’application
Vous pouvez également utiliser MSAL pour garder une trace des état de connexion de l’utilisateur.  Dans cette application, nous souhaitons l’utilisateur de rester connecté même après leur fermer l’application et ouvrez à nouveau.  Dans le `OnInitialized` remplacer, utilisez du MSAL `AcquireTokenSilent` mise en cache de méthode pour vérifier des jetons :

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>Appeler l’API de la tâche
Vous avez maintenant utilisé MSAL pour exécuter les politiques et d’obtenir les jetons.  Lorsque vous souhaitez utiliser l’une ces jetons pour appeler le API de la tâche, vous pouvez à nouveau utiliser de MSAL `AcquireTokenSilent` mise en cache de méthode pour vérifier des jetons :

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

Lors de l’appel à `AcquireTokenSilentAsync(...)` réussit et un jeton est trouvé dans le cache, vous pouvez ajouter le jeton pour le `Authorization` en-tête de la demande HTTP. L’API de web de tâche utilisera cet en-tête pour authentifier la demande de lire la liste des actions de l’utilisateur :

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Déconnexion de l’utilisateur
Enfin, vous pouvez utiliser MSAL pour mettre fin à une session utilisateur avec l’application lorsque l’utilisateur sélectionne la **déconnexion**.  Lorsque vous utilisez MSAL, cela en désactivant tous les jetons dans le cache de jetons :

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Exécutez l’exemple d’application

Enfin, générez et exécutez l’exemple.  Inscrivez-vous pour l’application à l’aide d’un nom d’utilisateur ou une adresse e-mail. Déconnectez-vous, puis reconnectez-vous en tant que le même utilisateur. Modifier le profil de cet utilisateur. Se déconnecter et de vous inscrire à l’aide d’un autre utilisateur.

## <a name="add-social-idps"></a>Ajouter IDPs sociales

Actuellement, l’application prend en charge uniquement inscription de l’utilisateur et à la connexion qui utilisent des **comptes locaux**. Il s’agit de comptes stockées dans votre répertoire B2C qui utilisent un nom d’utilisateur et le mot de passe. Grâce à Azure AD B2C, vous pouvez ajouter la prise en charge d’autres fournisseurs d’identité (IDPs) sans modifier votre code.

Pour ajouter des IDPs sociales à votre application, commencez en suivant les instructions détaillées de ces articles. Pour chaque IDP que vous souhaitez prendre en charge, vous devez enregistrer une application de ce système et obtenir un ID de client.

- [Configurer Facebook comme un IDP](active-directory-b2c-setup-fb-app.md)
- [Définir Google comme un IDP](active-directory-b2c-setup-goog-app.md)
- [Définir comme un IDP Amazon](active-directory-b2c-setup-amzn-app.md)
- [Définir comme un IDP LinkedIn](active-directory-b2c-setup-li-app.md)

Après avoir ajouté les fournisseurs d’identité dans votre répertoire B2C, vous devez modifier chacune de vos trois stratégies pour inclure les nouveau IDPs, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md). Après avoir enregistré vos stratégies, exécutez de nouveau l’application. Vous devriez voir les nouveau IDPs ajoutés en tant que connexion et rencontre des options d’abonnement dans chacune de vos identités.

Vous pouvez faire des essais avec vos stratégies et observer les effets sur votre exemple d’application. Ajouter ou supprimer IDPs, manipuler les revendications de l’application ou modifier les attributs d’abonnement. Faites des essais jusqu'à ce que vous pouvez voir comment lient ensemble les stratégies, les demandes d’authentification et les MSAL.

Pour référence, l’exemple complet [est fourni sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Vous pouvez également cloner GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
