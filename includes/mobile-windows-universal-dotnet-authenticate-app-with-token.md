
1. Dans le fichier de projet MainPage.xaml.cs, ajoutez les instructions **using** suivantes :

        using System.Linq;      
        using Windows.Security.Credentials;

2. Remplacez la méthode **AuthenticateAsync** par le code suivant :

        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;

            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;

            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }

            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;

                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;

                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.

                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);

                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);

                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
            
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();

            return success;
        }

    Dans cette version de **AuthenticateAsync**, l’application tente d’utiliser les informations d’identification stockées dans le **PasswordVault** pour accéder au service. Une connexion régulière sign-in est également effectuée lorsqu’il n’existe aucune information d’identification stockée.

    >[AZURE.NOTE]Un jeton en cache peut être expiré et d’expiration du jeton peut également se produire après l’authentification, lorsque l’application est en cours d’utilisation. Pour savoir comment déterminer si un jeton est arrivé à expiration, consultez [vérifier les jetons d’authentification a expiré](http://aka.ms/jww5vp). Pour une solution de gestion des erreurs d’autorisation liés aux jetons arrivant à expiration, consultez le billet de [mise en cache et la gestion des jetons expirés dans Azure Mobile Services gérés SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 

3. Redémarrez l’application deux fois.

    Notez que sur le premier démarrage, la connexion avec le fournisseur est à nouveau requis. Toutefois, sur le deuxième redémarrage, les informations d’identification mises en cache sont utilisées et ouverture de session est ignorée. 
