<properties
    pageTitle="Mise en route de l’authentification pour les applications Mobile dans l’application de Xamarin.Forms | Microsoft Azure"
    description="Apprenez à utiliser les applications Mobile pour authentifier les utilisateurs de votre application de formulaires de le Xamarin via un grand nombre de fournisseurs d’identité, y compris les DAS, Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinforms-app"></a>Ajouter l’authentification à votre application Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous indique comment authentifier les utilisateurs d’un Service d’application Mobile App depuis votre application cliente. Dans ce didacticiel, vous ajoutez l’authentification pour le projet de démarrage rapide de Xamarin.Forms à l’aide d’un fournisseur d’identité qui est pris en charge par le Service de l’application. Une fois correctement authentifié et autorisé par votre application Mobile, la valeur de l’ID utilisateur s’affiche et vous ne pourrez pas accéder aux données de la table limité.

##<a name="prerequisites"></a>Conditions préalables

Pour obtenir les meilleurs résultats avec ce didacticiel, nous vous recommandons d’effectuer tout d’abord le didacticiel de [Création d’une application Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md) . Après avoir terminé ce didacticiel, vous aurez un projet de Xamarin.Forms qui est une application de liste des tâches multi-plateforme.

Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Enregistrer votre application pour l’authentification et configurer les Services d’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Restreindre les autorisations aux utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##<a name="add-authentication-to-the-portable-class-library"></a>Ajouter l’authentification à la bibliothèque de classes portable

Applications Mobile utilise la méthode d’extension [LoginAsync] sur la [MobileServiceClient] à la connexion utilisateur avec l’authentification du Service de l’application. Cet exemple utilise un flux géré par le serveur de l’authentification qui affiche l’interface du fournisseur de connexion dans l’application. Pour plus d’informations, consultez [authentification de géré par le serveur](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow). Pour fournir une meilleure expérience utilisateur dans votre application de production, vous pouvez envisager utilisant à la place de [l’authentification de Client géré](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow). 

Pour s’authentifier auprès d’un projet Xamarin.Forms, vous définissez une interface **IAuthenticate** dans la bibliothèque de classes Portable pour l’application. Vous également mettre à jour l’interface utilisateur définie dans la bibliothèque de classes Portable pour ajouter un bouton **ouverture de session** , l’utilisateur doit cliquer pour lancer l’authentification. Après une authentification réussie, les données sont chargées à partir du serveur principal chargé de l’application mobile.

Vous devez implémenter l’interface **IAuthenticate** pour chaque plate-forme prise en charge par votre application.


1. Dans Visual Studio ou Xamarin Studio, App.cs ouvert à partir du projet avec le **Portable** dans le nom, qui est un projet de bibliothèque de classes Portable, ajoutez ce qui suit `using` instruction :

        using System.Threading.Tasks;

2. Dans App.cs, ajoutez le code suivant `IAuthenticate` définition d’interface immédiatement avant la `App` définition de classe.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }

3. Ajoutez les membres statiques suivantes à la classe **App** pour initialiser l’interface avec une implémentation spécifique de la plate-forme.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }

4. Ouvrez TodoList.xaml à partir du projet bibliothèque de classes Portable, ajoutez l’élément de **bouton** suivant dans l’élément de mise en page *buttonsPanel* , après le bouton existant : 

        <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
            Clicked="loginButton_Clicked"/>

    Ce bouton déclenche géré par le serveur pour l’authentification avec le serveur principal votre application mobile.

5. Ouvrez TodoList.xaml.cs à partir du projet bibliothèque de classes Portable, puis ajoutez le champ suivant à la `TodoList` classe :

        // Track whether the user has authenticated. 
        bool authenticated = false;


6. Remplacez la méthode **OnAppearing** par le code suivant :

        protected override async void OnAppearing()
        {
            base.OnAppearing();
    
            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data 
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Cela permet de garantir que les données sont uniquement actualisées à partir du service une fois que l’utilisateur a été authentifié.

7. Ajoutez le gestionnaire suivant pour l’événement **Clicked** à la classe de la **liste des tâches** :

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

8. Enregistrez vos modifications et régénérez le projet de bibliothèque de classes Portable vérification sans erreurs.


##<a name="add-authentication-to-the-android-app"></a>Ajouter une authentification à l’application d’Android

Cette section montre comment implémenter l’interface **IAuthenticate** dans le projet d’application Android. Ignorer cette section si vous ne prennent pas en charge des appareils Android.

1. Dans Visual Studio ou un Studio de Xamarin, cliquez droit sur le projet **droid** , puis le **définir comme projet de démarrage**.

2. Appuyez sur F5 pour démarrer le projet dans le débogueur, puis vérifiez qu’une exception non gérée avec un code d’état de 401 (non autorisé) est déclenchée après le démarrage de l’application. Cela se produit car l’accès sur le serveur principal est limité aux utilisateurs autorisés uniquement.

3. Ouvrez MainActivity.cs dans le projet Android et ajoutez le code suivant `using` instructions :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Mise à jour de la classe **MainActivity** pour implémenter l’interface **IAuthenticate** , comme suit :

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Mise à jour de la classe **MainActivity** en ajoutant un champ de **MobileServiceUser** et d’une méthode **d’authentification** , ce qui est requis par l’interface **IAuthenticate** , comme suit :

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }


    Si vous utilisez un fournisseur d’identité que Facebook, choisissez une autre valeur pour [MobileServiceAuthenticationProvider].

6. Ajoutez le code suivant à la méthode **OnCreate** de la classe **MainActivity** avant l’appel à `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Cela permet de s’assurer que l’authentificateur est initialisé avant le chargement de l’application.

7. Reconstruire l’application, exécutez-le, puis reconnectez-vous avec le fournisseur d’authentification, vous avez choisi et vérifiez que vous êtes en mesure d’accéder aux données qu’un utilisateur authentifié.

##<a name="add-authentication-to-the-ios-app"></a>Ajouter une authentification à l’application iOS

Cette section montre comment implémenter l’interface **IAuthenticate** dans le projet d’application iOS. Ignorer cette section si vous ne prennent pas en charge les périphériques e/s.

1. Dans Visual Studio ou un Studio de Xamarin, cliquez droit sur le projet **d’e/s** , puis **définir comme projet de démarrage**.

2. Appuyez sur F5 pour démarrer le projet dans le débogueur, puis vérifiez qu’une exception non gérée avec un code d’état de 401 (non autorisé) est déclenchée après le démarrage de l’application. Cela se produit car l’accès sur le serveur principal est limité aux utilisateurs autorisés uniquement.

4. Ouvrez AppDelegate.cs dans le projet d’e/s et ajoutez le code suivant `using` instructions :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Mise à jour de la classe **AppDelegate** pour implémenter l’interface **IAuthenticate** , comme suit :

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate

5. Mise à jour de la classe **AppDelegate** en ajoutant un champ de **MobileServiceUser** et d’une méthode **d’authentification** , ce qui est requis par l’interface **IAuthenticate** , comme suit :

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         

            return success;
        }

    Si vous utilisez un fournisseur d’identité que Facebook, choisissez une autre valeur pour [MobileServiceAuthenticationProvider].

6. Ajoutez la ligne de code suivante à la méthode **FinishedLaunching** avant l’appel à `LoadApplication()`: 

        App.Init(this);

    Cela permet de s’assurer que l’authentificateur est initialisé avant le chargement de l’application.

7. Reconstruire l’application, exécutez-le, puis reconnectez-vous avec le fournisseur d’authentification, vous avez choisi et vérifiez que vous êtes en mesure d’accéder aux données qu’un utilisateur authentifié.


##<a name="add-authentication-to-windows-app-projects"></a>Ajouter une authentification à des projets d’application Windows

Cette section montre comment implémenter l’interface **IAuthenticate** au 8.1 de Windows et les projets d’application Windows Phone 8.1. Les mêmes étapes s’appliquent pour les projets de plate-forme de Windows universel (UWP). Ignorer cette section si vous ne prennent pas en charge les périphériques Windows.

1. Dans Visual Studio, cliquez sur le **WinApp** , soit le projet **WinPhone81** , puis le **définir comme projet de démarrage**.

2. Appuyez sur F5 pour démarrer le projet dans le débogueur, puis vérifiez qu’une exception non gérée avec un code d’état de 401 (non autorisé) est déclenchée après le démarrage de l’application. Cela se produit car l’accès sur le serveur principal est limité aux utilisateurs autorisés uniquement.

3. Ouvrez MainPage.xaml.cs pour le projet d’application Windows et ajoutez le code suivant `using` instructions :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Remplacer `<your_Portable_Class_Library_namespace>` avec l’espace de noms pour votre bibliothèque de classes portable.

4. Mettre à jour la classe **MainPage** pour implémenter l’interface **IAuthenticate** , comme suit :

        public sealed partial class MainPage : IAuthenticate


5. Mettre à jour la classe **MainPage** en ajoutant un champ de **MobileServiceUser** et d’une méthode **d’authentification** , ce qui est requis par l’interface **IAuthenticate** , comme suit :

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
                
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }


    Si vous utilisez un fournisseur d’identité que Facebook, choisissez une autre valeur pour [MobileServiceAuthenticationProvider].

6. Ajoutez la ligne de code suivante dans le constructeur de la classe **MainPage** avant l’appel à `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
 
    Remplacer `<your_Portable_Class_Library_namespace>` avec l’espace de noms pour votre bibliothèque de classes portable.  
    Si c’est le projet WinApp, vous pouvez passer à l’étape 8. L’étape suivante s’applique uniquement au projet en WinPhone81, où vous devez effectuer le rappel de la connexion.

7. (Facultatif) Dans le projet d’application de **WinPhone81** , ouvrez App.xaml.cs et ajoutez le code suivant `using` instructions :

        using Microsoft.WindowsAzure.MobileServices;
        using <your_Portable_Class_Library_namespace>;

    Remplacer `<your_Portable_Class_Library_namespace>` avec l’espace de noms pour votre bibliothèque de classes portable.

8.  Ajoutez la substitution de la méthode **OnActivated** suivante à la classe **App** :

        protected override void OnActivated(IActivatedEventArgs args)
        {
            base.OnActivated(args);

            // We just need to handle activation that occurs after web authentication. 
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Get the client and call the LoginComplete method to complete authentication.
                var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
                client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
        }

    Lors de la substitution de la méthode existe déjà, ajoutez simplement le code conditionnel de l’extrait de code ci-dessus.

7. Reconstruire l’application, exécutez-le, puis reconnectez-vous avec le fournisseur d’authentification, vous avez choisi et vérifiez que vous êtes en mesure d’accéder aux données qu’un utilisateur authentifié.

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous terminé ce didacticiel de l’authentification de base, pensez à passer à un des didacticiels suivants :

+ [Ajouter des notifications de type pousser à votre application](app-service-mobile-xamarin-forms-get-started-push.md)  
  Découvrez comment ajouter push notifications prennent en charge à votre application et configurez votre back-end de l’application Mobile pour utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type Pousser.

+ [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Découvrez comment ajouter la prise en charge en mode hors connexion de votre application à l’aide d’un back-end de l’application Mobile. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;affichage, ajout ou modification de données&mdash;même lorsqu’il n’y a pas de connexion réseau.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

