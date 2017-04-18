4. Créer une nouvelle classe dans le projet appelé `ToDoBroadcastReceiver`.

5. Ajoutez le code suivant à l’aide des instructions à la classe de **ToDoBroadcastReceiver** :

        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

6. Ajoutez les demandes d’autorisation suivant entre les instructions **d’utilisation** et la déclaration **d’espace de noms** :

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. Remplacez la définition de classe **ToDoBroadcastReceiver** existante par le suivant :
 
        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
        Categories = new string[] { "@PACKAGE_NAME@" })]
        public class ToDoBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            // Set the Google app ID.
            public static string[] senderIDs = new string[] { "<PROJECT_NUMBER>" };
        }

    Dans le code ci-dessus, vous devez remplacer _`<PROJECT_NUMBER>`_ avec le numéro de projet affecté par Google lors du déploiement de votre application dans le portail de développement Google. 

8. Dans le fichier de projet ToDoBroadcastReceiver.cs, ajoutez le code suivant qui définit la classe **PushHandlerService** :
 
        // The ServiceAttribute must be applied to the class.
        [Service] 
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
 
            public PushHandlerService() : base(ToDoBroadcastReceiver.senderIDs) { }
        }

    Notez que cette classe dérive de **GcmServiceBase** et que **l’attribut** doit être appliqué à cette classe.

    >[AZURE.NOTE]La classe **GcmServiceBase** implémente les **OnRegistered()**, les méthodes **OnUnRegistered()**, **OnMessage()** et **OnError()** . Vous devez substituer ces méthodes dans la classe **PushHandlerService** .

5. Ajoutez le code suivant à la classe **PushHandlerService** qui remplace le Gestionnaire d’événements **OnRegistered** . 

        protected override void OnRegistered(Context context, string registrationId)
        {
            System.Diagnostics.Debug.WriteLine("The device has been registered with GCM.", "Success!");

            // Get the MobileServiceClient from the current activity instance.
            MobileServiceClient client = ToDoActivity.CurrentActivity.CurrentClient;
            var push = client.GetPush();

            // Define a message body for GCM.
            const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

            // Define the template registration as JSON.
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
              {"body", templateBodyGCM }
            };

            try
            {
                // Make sure we run the registration on the same thread as the activity, 
                // to avoid threading errors.
                ToDoActivity.CurrentActivity.RunOnUiThread(

                    // Register the template with Notification Hubs.
                    async () => await push.RegisterAsync(registrationId, templates));
                
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Push Installation Id", push.InstallationId.ToString()));
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Error with Azure push registration: {0}", ex.Message));
            }
        }

    Cette méthode utilise l’ID de l’enregistrement retourné GCM pour enregistrer avec Azure pour les notifications de transmission. Les balises ne peuvent être ajoutés à l’enregistrement après sa création. Pour plus d’informations, consultez [Comment : ajouter des balises à l’installation d’un périphérique pour activer des balises de l’émission](../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).

10. Substituez la méthode **OnMessage** dans **PushHandlerService** par le code suivant :

        protected override void OnMessage(Context context, Intent intent)
        {          
            string message = string.Empty;

            // Extract the push notification message from the intent.
            if (intent.Extras.ContainsKey("message"))
            {
                message = intent.Extras.Get("message").ToString();
                var title = "New item added:";

                // Create a notification manager to send the notification.
                var notificationManager = 
                    GetSystemService(Context.NotificationService) as NotificationManager;

                // Create a new intent to show the notification in the UI. 
                PendingIntent contentIntent = 
                    PendingIntent.GetActivity(context, 0, 
                    new Intent(this, typeof(ToDoActivity)), 0);           

                // Create the notification using the builder.
                var builder = new Notification.Builder(context);
                builder.SetAutoCancel(true);
                builder.SetContentTitle(title);
                builder.SetContentText(message);
                builder.SetSmallIcon(Resource.Drawable.ic_launcher);
                builder.SetContentIntent(contentIntent);
                var notification = builder.Build();

                // Display the notification in the Notifications Area.
                notificationManager.Notify(1, notification);

            }
        }

12. Substituez les méthodes **OnUnRegistered()** et **OnError()** par le code suivant.

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            throw new NotImplementedException();
        }

        protected override void OnError(Context context, string errorId)
        {
            System.Diagnostics.Debug.WriteLine(
                string.Format("Error occurred in the notification: {0}.", errorId));
        }