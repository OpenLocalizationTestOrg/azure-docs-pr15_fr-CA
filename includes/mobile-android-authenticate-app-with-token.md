
L’exemple précédent a montré une norme de connexion qui requiert le client de contacter le fournisseur d’identité et le back-end Azure service chaque fois que l’application démarre. Cette méthode est non seulement inefficace, que vous pouvez exécuter dans les problèmes liés à l’utilisation de nombreux clients est conseillé vous démarrer l’application en même temps. Une meilleure approche consiste à mettre en cache le jeton d’autorisations retourné par le service Azure et essayez d’utiliser ce premier avant d’utiliser une fournisseur sign-in. 

>[AZURE.NOTE]Vous pouvez mettre en cache le jeton émis par le serveur principal service Azure que vous utilisiez l’authentification client gérés ou gérés par le service. Ce didacticiel utilise une authentification de service géré.


1. Ouvrez le fichier ToDoActivity.java et ajoutez les instructions d’importation suivantes :

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. Ajouter les membres suivants à la `ToDoActivity` classe.

        public static final String SHAREDPREFFILE = "temp"; 
        public static final String USERIDPREF = "uid";  
        public static final String TOKENPREF = "tkn";   


3. Dans le fichier ToDoActivity.java, ajoutez le la définition suivante pour la `cacheUserToken` méthode.
 
        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }   
  
    Cette méthode stocke l’id utilisateur et un jeton dans un fichier de préférences qui est marqué comme privé. Cela doit protéger l’accès au cache afin que les autres applications sur le périphérique n’ont pas accès au jeton, car la préférence est isolée pour l’application. Cependant, si quelqu'un parvient à accéder au périphérique, il est possible qu’ils peuvent accéder au cache de jeton par d’autres moyens. 

    >[AZURE.NOTE]Vous pouvez protéger encore davantage le jeton avec cryptage si le jeton accès à vos données est considéré comme très sensible et un utilisateur peut accéder au périphérique. Toutefois, une solution totalement sécurisée est au-delà de la portée de ce didacticiel et en fonction de vos besoins en matière de sécurité.


4. Dans le fichier ToDoActivity.java, ajoutez le la définition suivante pour la `loadUserTokenCache` méthode.

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null); 
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null); 
            if (token == null)
                return false;
                
            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);
                
            return true;
        }



5. Dans le fichier *ToDoActivity.java* , remplacez le `authenticate` méthode avec la méthode suivante, qui utilise un cache de jetons. Modifier le fournisseur de connexions si vous souhaitez utiliser un autre compte que Google.

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
        
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();  
                    }
                });
            }
        }

6. Générer l’authentification de l’application et de test à l’aide d’un compte valide. Au moins deux fois l’exécuter. Au cours de la première exécution, vous recevrez une invite de commandes de connexion et de créer le cache de jetons. Après cela, chaque exécution va tenter de charger le cache de jetons pour l’authentification, et vous ne devez pas être nécessaire à la connexion.



