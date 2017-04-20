<properties
    pageTitle="Ajouter l’authentification à votre application de plate-forme de Windows universel (UWP) | Applications mobiles Azure"
    description="Apprenez à utiliser le Service Azure App Apps Mobile pour authentifier les utilisateurs de votre application de plate-forme de Windows universel (UWP) à l’aide d’une variété de fournisseurs d’identité, y compris : DAS, Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-windows-app"></a>Ajouter l’authentification à votre application Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Cette rubrique vous indique comment ajouter l’authentification basée sur le cloud pour votre application mobile. Dans ce didacticiel, vous ajoutez l’authentification pour le projet de démarrage rapide de plate-forme de Windows universel (UWP) pour les applications mobiles à l’aide d’un fournisseur d’identité qui est pris en charge par le Service d’application Azure. Après avoir correctement authentifié et autorisé par votre back-end de l’application Mobile, la valeur de l’ID utilisateur est affichée.

Ce didacticiel est basé sur le démarrage rapide pour les applications mobiles. Vous devez tout d’abord terminer le didacticiel [mise en route avec les applications mobiles](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Enregistrer votre application pour l’authentification et de configurer le Service de l’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Maintenant, vous pouvez vérifier que l’accès anonyme à votre serveur principal a été désactivé. Avec le projet d’application UWP définie comme projet de démarrage, déployer et exécute l’application ; Vérifiez qu’une exception non gérée avec un code d’état de 401 (non autorisé) est déclenchée après le démarrage de l’application. Cela se produit parce que l’application tente d’accéder à votre Code d’application Mobile comme un utilisateur non authentifié, mais la table de *TodoItem* requiert l’authentification.

Ensuite, vous mettra à jour l’application pour authentifier les utilisateurs avant de demander des ressources à partir de votre application de Service.

##<a name="add-authentication"></a>Ajouter une authentification à l’application

1. Dans la série UWP app MainPage.cs de fichier de projet et ajouter l’extrait de code suivant à la classe MainPage :
    
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    Ce code authentifie l’utilisateur avec une connexion à Facebook. Si vous utilisez un fournisseur d’identité que Facebook, modifier la valeur de **MobileServiceAuthenticationProvider** au-dessus de la valeur pour votre fournisseur.

3. Commenter les ou supprimez l’appel à la méthode **ButtonRefresh_Click** (ou la méthode **InitLocalStoreAsync** ) lors de la substitution de méthode **OnNavigatedTo** existante. Ainsi, les données en cours de chargement avant que l’utilisateur est authentifié. Ensuite, vous ajouterez un bouton **se connecter** à l’application qui déclenche l’authentification.

4. Ajouter l’extrait de code suivant à la classe MainPage :

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. Ouvrez le fichier de projet de fichier MainPage.xaml, recherchez l’élément qui définit le bouton **Enregistrer** et remplacez-le par le code suivant :

        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>

9. Appuyez sur la touche F5 pour exécuter l’application, cliquez sur le bouton **se connecter** et connectez-vous à l’application avec votre fournisseur d’identité choisie. Une fois que votre connexion est réussie, l’application s’exécute sans erreur et que vous êtes en mesure d’interroger votre back-end et de mettre à jour de données.


##<a name="tokens"></a>Stocker le jeton d’authentification sur le client

L’exemple précédent a montré une norme de connexion qui requiert le client de contacter le fournisseur de l’identité et le Service d’application chaque fois que l’application démarre. Non seulement c’est cette méthode inefficace, vous pouvez exécuter dans l’utilisation-concerne les problèmes de nombreux clients est conseillé vous démarrer l’application en même temps. Une meilleure approche consiste à mettre en cache le jeton d’autorisations retourné par votre Service d’application et essayez d’utiliser ce premier avant d’utiliser une fournisseur sign-in.

>[AZURE.NOTE]Vous pouvez mettre en cache le jeton émis par les Services de l’application que vous utilisiez l’authentification client gérés ou gérés par le service. Ce didacticiel utilise une authentification de service géré.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous terminé ce didacticiel de l’authentification de base, pensez à passer à un des didacticiels suivants :

+ [Ajouter des notifications de type pousser à votre application](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Découvrez comment ajouter push notifications prennent en charge à votre application et configurez votre back-end de l’application Mobile pour utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type Pousser.

+ [Activer la synchronisation hors connexion pour votre application](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Découvrez comment ajouter la prise en charge en mode hors connexion de votre application à l’aide d’un back-end de l’application Mobile. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;affichage, ajout ou modification de données&mdash;même lorsqu’il n’y a pas de connexion réseau.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md

