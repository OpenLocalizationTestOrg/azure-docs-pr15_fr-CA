
1. Dans la Solution affichage (ou de **L’Explorateur de solutions** dans Visual Studio), droit sur le dossier **composants** , cliquez sur **Obtenir plus des composants...**, rechercher le composant **Client de messagerie de nuage de Google** et l’ajouter au projet.

2. Ouvrez le fichier de projet ToDoActivity.cs et ajoutez le code suivant à l’aide de l’instruction à la classe :

        using Gcm.Client;

3. Dans la classe **ToDoActivity** , ajoutez le nouveau code suivant : 

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

    Cela vous permet d’accéder à l’instance de client mobile depuis le processus de service de gestionnaire de transmission.

4.  Ajoutez le code suivant à la méthode **OnCreate** , après la création de la **MobileServiceClient** :

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Votre **ToDoActivity** est maintenant prêt pour ajouter des notifications de type Pousser.