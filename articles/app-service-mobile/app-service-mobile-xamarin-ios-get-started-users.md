<properties
    pageTitle="Mise en route de l’authentification pour les applications mobiles dans iOS de Xamarin"
    description="Apprenez à utiliser les applications Mobile pour authentifier les utilisateurs de votre application d’e/s Xamarin via un grand nombre de fournisseurs d’identité, y compris les DAS, Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinios-app"></a>Ajouter l’authentification à votre application Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Cette rubrique vous indique comment authentifier les utilisateurs d’un Service d’application Mobile App depuis votre application cliente. Dans ce didacticiel, vous ajoutez l’authentification pour le projet de démarrage rapide de Xamarin.iOS à l’aide d’un fournisseur d’identité qui est pris en charge par le Service de l’application. Une fois correctement authentifié et autorisé par votre application Mobile, la valeur de l’ID utilisateur s’affiche et vous ne pourrez pas accéder aux données de la table limité.

Vous devez tout d’abord terminer le didacticiel de [Création d’une application Xamarin.iOS]. Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Enregistrer votre application pour l’authentification et configurer les Services d’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Restreindre les autorisations aux utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un périphérique ou un émulateur. Vérifiez qu’une exception non gérée avec un code d’état de 401 (non autorisé) est déclenchée après le démarrage de l’application. L’échec est consigné sur la console du débogueur. Ainsi, dans Visual Studio, vous devez voir l’échec dans la fenêtre Sortie.

&nbsp;&nbsp;Cet échec non autorisé se produit, car l’application essaie d’accéder à votre back-end de l’application Mobile comme un utilisateur non authentifié. La table *TodoItem* requiert l’authentification.

Ensuite, vous mettra à jour l’application client pour demander des ressources à partir du serveur principal chargé de l’application Mobile avec un utilisateur authentifié.

##<a name="add-authentication-to-the-app"></a>Ajouter une authentification à l’application

Dans cette section, vous allez modifier l’application pour afficher un écran de connexion avant d’afficher les données. Lorsque l’application démarre, il ne sera pas pas se connecter à votre application de Service et n’affichera pas les données. La première fois que l’utilisateur exécute le mouvement d’actualisation, l’écran de connexion s’affiche ; la liste d’éléments todo s’affichera après l’ouverture de session réussie.

1. Dans le projet client, ouvrez le fichier **QSTodoService.cs** et ajoutez le code suivant à l’aide d’instruction et `MobileServiceUser` avec un accesseur à la classe QSTodoService :

    ```
        using UIKit;
    ```

        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

2. Ajoutez la nouvelle méthode appelée **authentification** à **QSTodoService** avec la définition suivante :


        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Si vous utilisez un fournisseur d’identité autre qu’un Facebook, modifiez la valeur passée à **LoginAsync** ci-dessus pour une des valeurs suivantes : _MicrosoftAccount_, _Twitter_, _Google_ou _WindowsAzureActiveDirectory_.

3. Ouvrez **QSTodoListViewController.cs**. Modifiez la définition de la méthode de **ViewDidLoad** en supprimant l’appel à **RefreshAsync()** à la fin :

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();
        }


4. Modifiez la méthode **RefreshAsync** pour l’authentification si la propriété de **l’utilisateur** est null. Ajoutez le code suivant en haut de la définition de méthode :

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5. Dans Visual Studio ou Xamarin Studio connecté à votre hôte de Build Xamarin sur votre Mac, exécutez le projet client ciblage d’un périphérique ou un émulateur. Vérifiez que l’application n’affiche aucune donnée.

    Effectuez le mouvement d’actualisation en tirant vers le bas de la liste d’éléments, ce qui provoquera l’écran de connexion s’affiche. Une fois que vous avez entré les informations d’identification valides, l’application affiche la liste des tâches, et vous pouvez mettre à jour les données.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Créer une application de Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
