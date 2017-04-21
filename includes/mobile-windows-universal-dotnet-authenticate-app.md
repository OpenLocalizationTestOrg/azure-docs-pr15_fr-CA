
1. Ouvrez le fichier de projet partagé MainPage.cs et ajouter l’extrait de code suivant à la classe MainPage :
    
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

3. Commenter les ou supprimez l’appel à la méthode **RefreshTodoItems** lors de la substitution de méthode **OnNavigatedTo** existante.

    Ainsi, les données en cours de chargement avant que l’utilisateur est authentifié. Ensuite, vous ajouterez un bouton **se connecter** à l’application qui déclenche l’authentification.

4. Ajouter l’extrait de code suivant à la classe MainPage :

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. Dans le projet d’application Windows Store, ouvrez le fichier de projet MainPage.xaml et ajoutez l’élément de **bouton** suivant juste avant l’élément qui définit le bouton **Enregistrer** :

        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. Dans le projet d’application Windows Phone magasin, ajoutez l’élément de **bouton** suivant dans la **ContentPanel**, après l’élément de **zone de texte** :

        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>

8. Ouvrez le fichier de projet partagé App.xaml.cs et ajoutez le code suivant :

        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

    Si la méthode **OnActivated** existe déjà, il suffit d’ajouter le `#if...#endif` bloc de code.

9. Appuyez sur la touche F5 pour exécuter l’application Windows Store, cliquez sur le bouton **se connecter** et connectez-vous à l’application avec votre fournisseur d’identité choisi. 

    Lorsque vous êtes correctement connecté dans, l’application doit s’exécuter sans erreur et vous serez en mesure d’interroger votre back-end et de mettre à jour de données.

10. Cliquez sur le projet d’application Windows Phone magasin, cliquez sur **définir comme projet de démarrage**, puis répétez l’étape précédente pour vous assurer que le magasin de Windows Phone app fonctionne également correctement.  

 