<properties
    pageTitle="Mise en route avec les concentrateurs de Notification pour les applications Xamarin.Android | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type pousser à une application d’Android de Xamarin."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter="xamarin"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>Mise en route avec les concentrateurs de Notification avec Xamarin pour Android

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel vous montre comment utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type pousser à une application Xamarin.Android.
Vous allez créer une application vide Xamarin.Android qui reçoit des notifications de type pousser à l’aide de la messagerie de nuage Google (GCM). Lorsque vous avez terminé, vous serez en mesure d’utiliser votre concentrateur de notification à la diffusion des notifications de type pousser à tous les périphériques de l’exécution de votre application. Le code est terminé est disponible dans l' [application de NotificationHubs] [ GitHub] exemple.

Ce didacticiel illustre le scénario de diffusion simple à l’aide de concentrateurs de Notification.


## <a name="before-you-begin"></a>Avant de commencer

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Le code complété pour ce didacticiel, vous pouvez trouver sur GitHub [ici](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid).



##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ Visual Studio avec Xamarin sur Windows ou Xamarin Studio sur des instructions d’installation complètes de Mac OS x sont sur [l’installation et l’installer pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ Compte actif de Google
+ [Composant de messagerie de Azure]
+ [Composant de Client de messagerie de nuage de Google]

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels de concentrateurs de Notification pour les applications Xamarin.Android.

> [AZURE.IMPORTANT] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##<a name="enable-google-cloud-messaging"></a>Activer le Cloud Google messagerie

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-your-notification-hub"></a>Configurer votre concentrateur de notification

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>Cliquez sur l’onglet <b>configurer</b> , en haut, entrez la valeur de <b>Clé d’API</b> que vous avez obtenu dans la section précédente, puis cliquez sur <b>Enregistrer</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)


Votre concentrateur de notification est maintenant configuré pour fonctionner avec le GCM, et vous présentent les chaînes de connexion à la fois à votre application de recevoir des notifications et d’envoyer des notifications de type Pousser.

##<a name="connect-your-app-to-the-notification-hub"></a>Votre application de se connecter au concentrateur de notification

###<a name="create-a-new-project"></a>Créez un nouveau projet

1. Dans Xamarin Studio, cliquez sur la **Nouvelle Solution**, cliquez sur **Application Android**et cliquez sur **suivant**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. Entrez votre **Nom de l’application** et un **identificateur**. Cliquez sur la **Cible les plateformes** que vous souhaitez prendre en charge et puis cliquez sur **suivant** et **créer**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)


    Cette opération crée un nouveau projet Android.

2. Ouvrez les propriétés du projet en cliquant votre nouveau projet dans l’affichage de la Solution et en choisissant les **Options**. Sélectionnez l’élément de **l’Application d’Android** dans la section **Générer** .

    Assurez-vous que la première lettre du **nom de Package** est en minuscules.

    > [AZURE.IMPORTANT] La première lettre du nom du package doit être en minuscule. Dans le cas contraire, vous recevrez des erreurs manifeste d’application lorsque vous vous inscrivez votre **BroadcastReceiver** et un **IntentFilter** pour les notifications de type Pousser ci-dessous.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)


3. Définissez éventuellement la **version Minimum Android** à un autre niveau de l’API.

4. Définissez éventuellement la **cible Android version** à l’autre version de l’API que vous souhaitez cibler (doit être au niveau des API 8 ou version ultérieure).

Cliquez sur **OK** et fermez la boîte de dialogue Options du projet.


###<a name="add-the-required-components-to-your-project"></a>Ajouter les composants nécessaires à votre projet

Le Google Cloud Client de messagerie disponibles dans le magasin de composants Xamarin simplifie le processus de prise en charge des notifications de type Pousser dans Xamarin.Android.

1. Cliquez sur le dossier composants de l’application de Xamarin.Android et choisissez **Obtenir les composants plus**.

2. Recherchez le composant **De messagerie Azure** et l’ajouter au projet.

3. Recherchez le composant **Client de messagerie de nuage de Google** et l’ajouter au projet.


###<a name="set-up-notification-hubs-in-your-project"></a>Configuration des concentrateurs de notification dans votre projet

1. Rassemblez les informations suivantes pour votre application et la notification de supervision Android :

    - **GoogleProjectNumber**: obtenir cette valeur de numéro de projet à partir de la vue d’ensemble de votre application sur le portail de développement Google. Vous avez de cette valeur précédemment lorsque vous avez créé l’application sur le portail.
    - **Écouter la chaîne de connexion**: sur le tableau de bord dans le [Portail classique d’Azure], cliquez sur **les chaînes de connexion de vue**. Copier la chaîne de connexion *DefaultListenSharedAccessSignature* pour cette valeur.
    - **Nom du concentrateur**: c’est le nom de votre concentrateur à partir du [Portail classique d’Azure]. Par exemple, *mynotificationhub2*.

    Créer une classe **Constants.cs** pour votre projet Xamarin et définissez les valeurs de constantes suivantes dans la classe. Remplacez les espaces réservés par les valeurs.

        public static class Constants
        {
            public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
            public const string ListenConnectionString = "<Listen connection string>";
            public const string NotificationHubName = "<hub name>";
        }

2. Ajoutez le code suivant à l’aide d’instructions à **MainActivity.cs**:

        using Android.Util;
        using Gcm.Client;

3. Ajoutez une variable d’instance pour le `MainActivity` classe qui sera utilisé pour afficher une boîte de dialogue alerte lorsque l’application est en cours d’exécution :

        public static MainActivity instance;


3. Créer la méthode suivante dans la classe **MainActivity** :

        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. Dans le `OnCreate` l’initialisation de la méthode de **MainActivity.cs**, le `instance` variable et ajoutez un appel à `RegisterWithGCM`:

        protected override void OnCreate (Bundle bundle)
        {
            instance = this;

            base.OnCreate (bundle);

            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            RegisterWithGCM();
        }


4. Créer une nouvelle classe, **MyBroadcastReceiver**.

    > [AZURE.NOTE] Nous étudierons la création d’une classe de **BroadcastReceiver** de toutes pièces ci-dessous. Toutefois, une alternative rapide à la création manuelle de **MyBroadcastReceiver.cs** est pour désigner le fichier **GcmService.cs** dans l’exemple de projet Xamarin.Android inclus avec les [exemples de NotificationHubs][GitHub]. **GcmService.cs** de duplication et de modification des noms de classe peuvent être un bon point de départ ainsi.

5. Ajoutez le code suivant à l’aide d’instructions à **MyBroadcastReceiver.cs** (faisant référence à l’assembly que vous avez ajouté précédemment et le composant) :

        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;

5. Dans **MyBroadcastReceiver.cs**, ajoutez les demandes d’autorisation suivant entre les instructions **d’utilisation** et la déclaration **d’espace de noms** :

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. Dans **MyBroadcastReceiver.cs**, modifiez la classe **MyBroadcastReceiver** pour faire correspondre les éléments suivants :

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Ajouter une autre classe dans **MyBroadcastReceiver.cs** , nommé **PushHandlerService**, qui dérive de **GcmServiceBase**. Veillez à appliquer **l’attribut** à la classe :

        [Service] // Must use the service tag
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID)
            {
                Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
            }
        }


8. **GcmServiceBase** implémente des méthodes **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()**et **OnError()**. Notre classe d’implémentation **PushHandlerService** devez substituer ces méthodes et ces méthodes seront déclenchent en réponse à l’interaction avec le concentrateur de notification.


9. Substituez la méthode **OnRegistered()** dans **PushHandlerService** en utilisant le code suivant :

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...",
                                "The device has been Registered!");

            Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                        context);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }

            //var tags = new List<string>() { "falcons" }; // create tags if you want
            var tags = new List<string>() {};

            try
            {
                var hubRegistration = Hub.Register(registrationId, tags.ToArray());
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }
        }

    > [AZURE.NOTE] Dans le code **OnRegistered()** ci-dessus, vous devez noter la capacité de spécifier des balises pour vous inscrire à des canaux de messagerie spécifiques.

10. Substituez la méthode **OnMessage** dans **PushHandlerService** en utilisant le code suivant :

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }

11. Ajoutez les méthodes suivantes de le **createNotification** et de **dialogNotify** à **PushHandlerService** pour avertir les utilisateurs lorsqu’une notification est reçue.

    >[AZURE.NOTE] Conception de notification Android version 5.0 et ultérieure représente un écart important de celle des versions précédentes. Si vous testez cette sur Android 5.0 ou version ultérieure, l’application devez être en cours d’exécution pour recevoir la notification. Pour plus d’informations, consultez [Notifications Android](http://go.microsoft.com/fwlink/?LinkId=615880).

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }

        protected void dialogNotify(String title, String message)
        {

            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }


12. Substituer des membres abstraits, **OnUnRegistered()**, **OnRecoverableError()**et **OnError()** afin que votre code est compilé :

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }

        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

            return base.OnRecoverableError (context, errorId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }


##<a name="run-your-app-in-the-emulator"></a>Exécutez votre application dans l’émulateur

Si vous exécutez cette application dans l’émulateur, assurez-vous que vous utilisez un Android Virtual Device (AVD) qui prend en charge les APIs de Google.

> [AZURE.IMPORTANT] Pour recevoir des notifications de transmission, vous devez configurer un compte Google sur votre périphérique virtuel Android. (Dans l’émulateur, accédez aux **paramètres** et cliquez sur **Ajouter un compte**.) Assurez-vous également que l’émulateur est connecté à Internet.

>[AZURE.NOTE] Conception de notification Android version 5.0 et ultérieure représente un écart important de celle des versions précédentes. Pour plus d’informations, consultez [Notifications Android](http://go.microsoft.com/fwlink/?LinkId=615880).


1. À partir d' **Outils**, cliquez sur **Ouvrir le Gestionnaire d’émulateur Android**, sélectionnez votre périphérique, puis cliquez sur **Modifier**.

    ![][18]

2. Sélectionnez des **API Google** dans la **cible**, puis cliquez sur **OK**.

    ![][19]

3. Sur la barre d’outils supérieure, cliquez sur **exécuter**, puis sélectionnez votre application. Cela démarre l’émulateur et exécute l’application.

  L’application récupère les *attributs de registrationId* GCM et inscrit avec le concentrateur de notification.

##<a name="send-notifications-from-your-backend"></a>Envoyer des notifications à partir de votre back-end


Vous pouvez tester la réception des notifications dans votre application en envoyant des notifications dans le [Portail classique d’Azure] via l’onglet déboguer sur le concentrateur de notification, comme indiqué dans l’écran ci-dessous.

![][30]


Les notifications de transmission sont généralement envoyées dans un service back-end, tels que les Services mobiles ou ASP.NET via une bibliothèque compatible. Vous pouvez également utiliser l’API REST directement à envoyer des messages de notification si une bibliothèque n’est pas disponible pour votre serveur principal.

Voici une liste de certains autres didacticiels que vous pouvez consulter pour envoyer des notifications :

- ASP.NET : Reportez-vous à la section [Utilisation des concentrateurs de Notification pour les notifications de type Pousser aux utilisateurs].
- Java de concentrateurs de Notification Azure SDK : consultez [l’utilisation de concentrateurs de Notification à partir de Java](notification-hubs-java-push-notification-tutorial.md) pour l’envoi de notifications à partir de Java. Il a été testé dans Eclipse pour le développement Android.
- PHP : Consultez [l’utilisation de concentrateurs de Notification à partir de PHP](notification-hubs-php-push-notification-tutorial.md).


Dans les sous-sections suivante du didacticiel, vous envoyez des notifications à l’aide d’une application de console .NET et en utilisant un service mobile via un script de nœud.

####<a name="optional-send-notifications-by-using-a-net-app"></a>(Facultatif) Envoyer des notifications à l’aide d’une application .NET

Dans cette section, nous enverrons les notifications à l’aide d’une application de console .NET

1. Créez une nouvelle application de console Visual C# :

    ![][20]

2. Dans Visual Studio, cliquez sur **Outils**, cliquez sur **Gestionnaire de package de NuGet**, puis cliquez sur **Console de Gestionnaire de package**.

    Cela affiche la Console du Gestionnaire de package dans Visual Studio.

3. La valeur **par défaut du projet de** votre projet d’application console dans la fenêtre de la Console du Gestionnaire de package et puis dans la fenêtre de la console, exécutez la commande suivante :

        Install-Package Microsoft.Azure.NotificationHubs

    Cette opération ajoute une référence à la Notification de concentrateurs Azure SDK à l’aide du <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">package Microsoft.Azure.Notification concentrateurs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. Ouvrez le fichier Program.cs et ajoutez le code suivant `using` instruction :

        using Microsoft.Azure.NotificationHubs;

5. Dans votre `Program` de classe, ajoutez la méthode suivante. Mettre à jour le texte d’espace réservé avec votre *DefaultFullSharedAccessSignature* connexion chaîne et concentrateur le nom à partir du [Portail classique d’Azure].

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }

6. Ajoutez les lignes suivantes dans votre méthode **Main** :

         SendNotificationAsync();
         Console.ReadLine();

7. Appuyez sur la touche F5 pour exécuter l’application. Vous recevrez une notification dans l’application.

    ![][21]

####<a name="optional-send-notifications-by-using-a-mobile-service"></a>(Facultatif) Envoyer des notifications à l’aide d’un service mobile

1. Suivre la [mise en route de Services mobiles].

1. Connectez-vous au [Portail classique d’Azure]et sélectionnez votre service mobile.

2. Sélectionnez l’onglet **Planificateur** dans la partie supérieure.

    ![][22]

3. Créer une nouvelle tâche planifiée, insérer un nom et sélectionnez **à la demande**.

    ![][23]

4. Lors de la création du travail, cliquez sur le nom du travail. Cliquez ensuite sur l’onglet **Script** de la barre supérieure.

5. Insérer le script suivant à l’intérieur de la fonction de planificateur. Veillez à remplacer les espaces réservés avec votre nom de concentrateur de notification et de la chaîne de connexion pour *DefaultFullSharedAccessSignature* que vous avez obtenu précédemment. Cliquez sur **Enregistrer**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6. Cliquez sur **Exécuter une fois** sur la barre du bas. Vous recevrez une notification toast.

##<a name="next-steps"></a>Étapes suivantes

Dans cet exemple simple, vous envoyé des notifications à tous vos équipements Android. Pour cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation des concentrateurs de Notification pour les notifications de type Pousser aux utilisateurs]. Si vous souhaitez que segmenter les utilisateurs en groupes d’intérêt, vous pouvez lire les [Concentrateurs de Notification utilisé pour envoyer des informations de dernière minute]. Pour en savoir plus sur l’utilisation de concentrateurs de Notification dans le [Guide de concentrateurs de Notification] et dans la [Notification concentrateurs sur les procédures pour Android].

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Mise en route de Services mobiles]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure Portal classique]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Conseils de concentrateurs de notification]: http://msdn.microsoft.com/library/jj927170.aspx
[Procédures de concentrateurs de notification pour Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Utiliser des concentrateurs de Notification pour les notifications de type Pousser aux utilisateurs]: /manage/services/notification-hubs/notify-users-aspnet
[Concentrateurs de Notification permet d’envoyer des informations de dernière minute]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Composant de Client de messagerie de nuage de Google]: http://components.xamarin.com/view/GCMClient/
[Composant de messagerie de Azure]: http://components.xamarin.com/view/azure-messaging
