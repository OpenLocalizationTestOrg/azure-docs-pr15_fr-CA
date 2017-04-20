<properties
    pageTitle="Ajouter des notifications de type pousser à votre application Xamarin.Forms | Microsoft Azure"
    description="Apprenez à utiliser les services Azure pour envoyer des notifications de type Pousser les multi-plateformes à vos applications Xamarin.Forms."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Ajouter des notifications de type pousser à votre application Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Vue d’ensemble

Dans ce didacticiel, vous ajoutez push notifications à tous les projets résultant de la [Xamarin.Forms rapide démarrer](app-service-mobile-xamarin-forms-get-started.md) afin qu’une notification de transmission est envoyée à tous les clients multiplates-formes chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez le progiciel d’extension de notification push. Pour plus d’informations, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Conditions préalables

* Pour iOS, vous devez une [adhésion au programme de développement de Apple](https://developer.apple.com/programs/ios/) et un périphérique d’e/s physiques étant donné que le [simulateur d’e/s ne gère pas les notifications de transmission](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

##<a name="configure-hub"></a>Configurer un concentrateur de Notification

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Mettre à jour le projet serveur pour envoyer des notifications de type Pousser

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##<a name="optional-configure-and-run-the-android-project"></a>(Facultatif) Configurer et exécuter le projet Android

Cette tâche pour activer les notifications de transmission pour le projet de Xamarin.Forms Droid pour Android.


###<a name="enable-firebase-cloud-messaging-fcm"></a>Activer le nuage Firebase Messaging (FCM)

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

###<a name="configure-the-mobile-app-backend-to-send-push-requests-using-fcm"></a>Configurer le serveur principal chargé de l’application Mobile pour envoyer des demandes de transmission à l’aide de FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###<a name="add-push-notifications-to-the-android-project"></a>Ajouter des notifications de type Pousser au projet Android

Avec le serveur principal configuré avec FCM, nous pouvons ajouter des composants et codes au client d’enregistrer avec FCM, s’inscrire pour les notifications de transmission avec des concentrateurs de Notification Azure via le back-end de l’application mobile et recevoir des notifications.

1. Dans le projet **Droid** , droit sur le dossier **composants** , cliquez sur **Obtenir plus des composants...**, rechercher le composant **Client de messagerie de nuage de Google** et l’ajouter au projet. Ce composant prend en charge les notifications de transmission d’un projet Xamarin Android.


2. Ouvrir le fichier de projet MainActivity.cs et ajoutez l’instruction using en haut du fichier :

        using Gcm.Client;

3. Ajoutez le code suivant à la méthode **OnCreate** après l’appel à **LoadApplication**:

        try
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }


4. Ajoutez une nouvelle méthode d’assistance **CreateAndShowDialog** , comme suit :

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }


5. Ajoutez le code suivant à la classe **MainActivity** :

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    Cela expose l’instance en cours de **MainActivity** afin de nous pouvons exécuter sur le thread d’interface utilisateur principal.

6. Initialiser le `instance`, variable au début de la méthode **OnCreate** , comme suit.

        // Set the current instance of MainActivity.
        instance = this;

2. Ajouter un nouveau fichier de classe au projet nommé **Droid** `GcmService.cs`et assurez-vous que les instructions **using** suivantes sont présentes en haut du fichier :

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;


9. Ajouter les demandes d’autorisation suivantes en haut du fichier, après les instructions **using** et avant la déclaration de **l’espace de noms** .

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Ajoutez la définition de classe suivante à l’espace de noms. 

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
        }

    >[AZURE.NOTE]Remplacez **< numero_projet >** par votre numéro de projet que vous avez noté précédemment.   

11. Remplacez la classe **GcmService** vide par le code suivant, qui utilise le nouveau récepteur de diffusion :

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


12. Ajoutez le code suivant à la classe **GcmService** qui remplace le Gestionnaire d’événements **OnRegistered** et implémente une méthode **d’inscription** .

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

        Note that this code uses the `messageParam` parameter in the template registration. 

13. Ajoutez le code suivant qui implémente **OnMessage**: 

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    Il gère les notifications entrantes et les envoyer vers le Gestionnaire de notification s’affiche.

14. **GcmServiceBase** vous impose également implémenter les méthodes de gestionnaire **OnUnRegistered** et **OnError** , vous pouvez procédez comme suit :

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Vous êtes maintenant prêt le test des notifications de type Pousser dans l’application en cours d’exécution sur l’émulateur ou sur un appareil Android.

###<a name="test-push-notifications-in-your-android-app"></a>Notifications de type Pousser de test dans votre application d’Android

Les deux premières étapes sont nécessaires uniquement lorsque le test sur un émulateur.

1. Assurez-vous que vous déployez à ou le débogage sur un périphérique virtuel qui a des APIs de Google défini comme cible, comme indiqué dans le Gestionnaire de périphérique virtuel Android (AVD).

2. Ajouter un compte Google Android périphérique en cliquant sur **applications** > **paramètres** > **Ajouter un compte**, puis suivez les invites pour utiliser Ajouter un compte Google existant sur le périphérique pour créer une nouvelle.

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **Droid** et cliquez sur **définir comme projet de démarrage**.

2. Appuyez sur le bouton **exécuter** pour générer le projet et démarrer l’application sur votre appareil Android ou l’émulateur.

3. Dans l’application, entrez une tâche, puis cliquez sur le signe plus (**+**) icône.

4. Vérifiez que réception d’une notification lorsqu’un élément est ajouté.


##<a name="optional-configure-and-run-the-ios-project"></a>(Facultatif) Configurer et exécuter le projet d’e/s

Cette section est pour le projet d’e/s de Xamarin pour les périphériques d’e/s en cours d’exécution. Vous pouvez ignorer cette section si vous ne travaillez pas avec les périphériques d’e/s.

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]


####<a name="configure-the-notification-hub-for-apns"></a>Configurer le concentrateur de notification pour APNS

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Ensuite, vous allez configurer le paramètre du projet iOS dans Xamarin Studio ou Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####<a name="add-push-notifications-to-your-ios-app"></a>Ajouter des notifications de type pousser à votre application d’iOS

1. AppDelegate.cs d’ouvrir dans le projet **d’e/s** , ajoutez l’instruction **using** suivante en haut du fichier de code.

        using Newtonsoft.Json.Linq;

4. Dans la classe **AppDelegate** , ajoutez une substitution pour enregistrer les notifications de l’événement **RegisteredForRemoteNotifications** :

        public override void RegisteredForRemoteNotifications(UIApplication application, 
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. Dans **AppDelegate**, également ajouter la substitution suivante pour le Gestionnaire d’événements **DidReceivedRemoteNotification** :

        public override void DidReceiveRemoteNotification(UIApplication application, 
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    Cette méthode gère les notifications entrantes pendant l’exécution de l’application.

2. Dans la classe **AppDelegate** , ajoutez le code suivant à la méthode **FinishedLaunching** : 

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Cela permet la prise en charge des notifications à distance et les requêtes push d’enregistrement.

Votre application est maintenant mise à jour pour prendre en charge les notifications de transmission.

####<a name="test-push-notifications-in-your-ios-app"></a>Notifications de type Pousser de test dans votre application iOS

1. Cliquez avec le bouton droit sur le projet d’e/s, puis cliquez sur **définir comme projet de StartPp**.

2. Appuyez sur le bouton **exécuter** ou sur **F5** dans Visual Studio pour générer le projet et démarrer l’application dans un dispositif d’e/s, puis cliquez sur **OK** pour accepter les notifications de transmission.

    > [AZURE.NOTE] Vous devez explicitement accepter les notifications de type pousser à partir de votre application. Cette demande ne se produit que la première fois que l’application s’exécute.

3. Dans l’application, entrez une tâche, puis cliquez sur le signe plus (**+**) icône.

4. Vérifiez qu’une notification est reçue, puis cliquez sur **OK** pour fermer la notification.


##<a name="optional-configure-and-run-the-windows-projects"></a>(Facultatif) Configurer et exécuter les projets Windows

Cette section est pour l’exécution de la Xamarin.Forms WinApp et WinPhone81 des projets pour les périphériques Windows. Ces étapes prennent également en charge les projets de plate-forme de Windows universel (UWP). Vous pouvez ignorer cette section si vous ne travaillez pas avec des périphériques Windows.


####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Inscrire votre application Windows pour les notifications de transmission avec WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####<a name="configure-the-notification-hub-for-wns"></a>WNS configurer le concentrateur de notification

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####<a name="add-push-notifications-to-your-windows-app"></a>Ajouter des notifications de type pousser à votre application Windows

1. Dans Visual Studio, ouvrez **App.xaml.cs** dans un projet Windows et ajoutez les instructions **using** suivantes.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Remplacer `<your_TodoItemManager_portable_class_namespace>` avec l’espace de noms de votre projet portable qui contient le `TodoItemManager` classe.
 

2. Dans App.xaml.cs, ajoutez la méthode suivante de **InitNotificationsAsync** : 

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    Cette méthode obtient le canal de notification push et enregistre un modèle pour recevoir des notifications de modèle à partir de votre centre de notification. Une notification de modèle qui prend en charge les *messageParam* est remise à ce client.

3. Dans App.xaml.cs, vous devez mettre à jour la définition de méthode du Gestionnaire d’événements de **OnLaunched** en ajoutant la `async` modificateur, puis ajoutez la ligne de code suivante à la fin de la méthode : 

        await InitNotificationsAsync();

    Cela permet de s’assurer que l’enregistrement de notification de transmission est créé ou actualisé chaque fois que l’application est lancée. Il est important de le faire pour vous assurer que le canal de transmission WNS est toujours actif.  

4. Dans l’Explorateur de solutions de Visual Studio, ouvrez le fichier **Package.appxmanifest** et la valeur **Capable de Toast** **Oui** sous **Notifications**.

5. Générer l’application et vérifiez que vous n’avez aucune erreur.  Application de client vous doit désormais s’enregistrer pour les notifications de modèle à partir du serveur principal chargé de l’application Mobile. Répétez cette section pour chaque projet de Windows dans votre solution.


####<a name="test-push-notifications-in-your-windows-app"></a>Notifications de type Pousser de test dans votre application Windows

1. Dans Visual Studio, cliquez avec le bouton droit sur un projet Windows et cliquez sur **définir comme projet de démarrage**.

2. Appuyez sur le bouton **exécuter** pour générer le projet et démarrer l’application.

3. Dans l’application, tapez un nom pour un nouveau todoitem, puis cliquez sur le signe plus (**+**) icône pour l’ajouter.

4. Vérifiez que réception d’une notification lorsque l’élément est ajouté.

##<a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les notifications de transmission :

* [Diagnostiquer les problèmes de notification de transmission](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Il existe diverses raisons pourquoi les notifications peuvent être perdues ou ne finissent pas sur les périphériques. Cette rubrique vous indique comment analyser et déterminer la cause des échecs de notification de transmission. 

Envisagez de passer à un des didacticiels suivants :

* [Ajouter l’authentification à votre application](app-service-mobile-xamarin-forms-get-started-users.md)  
Apprenez à authentifier les utilisateurs de votre application avec un fournisseur d’identité.

* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Découvrez comment ajouter la prise en charge en mode hors connexion de votre application à l’aide d’un back-end de l’application Mobile. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;affichage, ajout ou modification de données&mdash;même lorsqu’il n’y a pas de connexion réseau.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

