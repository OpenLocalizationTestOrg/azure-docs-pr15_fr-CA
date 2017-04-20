<properties
    pageTitle="Mise en route de l’authentification pour les applications mobiles dans Xamarin Android"
    description="Apprenez à utiliser les applications Mobile pour authentifier les utilisateurs de votre application d’Android de Xamarin avec un éventail de fournisseurs d’identité, y compris les DAS, Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinandroid-app"></a>Ajouter l’authentification à votre application Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Cette rubrique vous indique comment authentifier les utilisateurs d’une application Mobile à partir de votre application cliente. Dans ce didacticiel, vous ajoutez l’authentification pour le projet de démarrage rapide à l’aide d’un fournisseur d’identité qui est pris en charge par les applications Azure Mobile. Après avoir correctement authentifié et autorisé dans l’application Mobile, la valeur de l’ID utilisateur est affichée.

Ce didacticiel est basé sur le démarrage rapide du Mobile App. Vous devez également terminer le didacticiel de [Création d’une application Xamarin.Android]. Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register"></a>Enregistrer votre application pour l’authentification et configurer les Services d’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un périphérique ou un émulateur. Vérifiez qu’une exception non gérée avec un code d’état de 401 (non autorisé) est déclenchée après le démarrage de l’application. Cela se produit, car l’application essaie d’accéder à votre back-end de l’application Mobile comme un utilisateur non authentifié. La table *TodoItem* requiert l’authentification.

Ensuite, vous mettra à jour l’application client pour demander des ressources à partir du serveur principal chargé de l’application Mobile avec un utilisateur authentifié.

##<a name="add-authentication"></a>Ajouter une authentification à l’application

L’application est mise à jour pour obliger les utilisateurs à cliquer sur le bouton **se connecter** et s’authentifier avant que les données sont affichées.

1. Ajoutez le code suivant à la classe **TodoActivity** :

        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");

                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    Cela crée une nouvelle méthode pour authentifier un utilisateur et un gestionnaire de méthode pour un nouveau bouton de **connexion** . L’utilisateur de l’exemple de code ci-dessus est authentifié à l’aide d’une connexion à Facebook. Une boîte de dialogue permet d’afficher l’ID de l’utilisateur authentifié une fois.

    > [AZURE.NOTE] Si vous utilisez un fournisseur d’identité que Facebook, modifiez la valeur passée à **LoginAsync** ci-dessus pour une des valeurs suivantes : _MicrosoftAccount_, _Twitter_, _Google_ou _WindowsAzureActiveDirectory_.

3. Dans la méthode **OnCreate** , supprimer ou commenter les la ligne de code suivante :

        OnRefreshItemsSelected ();

4. Dans le fichier Activity_To_Do.axml, ajoutez la définition suivante de bouton *LoginUser* avant le bouton *AddItem* existant :

        <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. Ajoutez l’élément suivant dans le fichier de ressources Strings.xml :

        <string name="login_button_text">Sign in</string>

6. Dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un périphérique ou un émulateur et vous connecter avec votre fournisseur d’identité choisie.

    Lorsque vous êtes correctement connecté dans, l’application affiche votre ID de connexion et la liste d’éléments todo, et vous pouvez mettre à jour les données.


<!-- URLs. -->
[Créer une application de Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
