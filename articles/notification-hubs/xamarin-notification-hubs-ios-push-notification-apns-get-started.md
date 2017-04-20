<properties
    pageTitle="iOS pousser des Notifications avec des concentrateurs de Notification pour les applications Xamarin | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type pousser à une application d’e/s Xamarin."
    services="notification-hubs"
    keywords="iOS pousser des notifications, des messages de type Pousser, notifications de transmission, pousser le message"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="ios-push-notifications-with-notification-hubs-for-xamarin-apps"></a>iOS pousser des Notifications avec des concentrateurs de Notification pour les applications de Xamarin

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble
> [AZURE.IMPORTANT] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Ce didacticiel vous montre comment utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type pousser à une application iOS.
Vous allez créer une application vide Xamarin.iOS qui reçoit des notifications de type Pousser en utilisant le [Service de Notification de transmission Apple (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). Lorsque vous avez terminé, vous serez en mesure d’utiliser votre concentrateur de notification à la diffusion des notifications de type pousser à tous les périphériques de l’exécution de votre application. Le code est terminé est disponible dans l' [application de NotificationHubs] [ GitHub] exemple.

Ce didacticiel illustre le scénario de diffusion du message de transmission simple avec des concentrateurs de Notification.

##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ [Xcode 6.0][Install Xcode]
+ Une e/s 7.0 (ou version ultérieure) avec un appareil compatible
+ l’adhésion au programme de développement d’iOS
+ [Xamarin Studio]

   > [AZURE.NOTE] En raison de la configuration requise pour iOS pousser des notifications, vous devez déployer et tester l’exemple d’application sur un périphérique d’e/s physiques (iPhone ou iPad) au lieu de dans le simulateur.

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels de concentrateurs de Notification pour les applications d’e/s Xamarin.


[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##<a name="configure-your-notification-hub"></a>Configurer votre concentrateur de notification

Cette section vous guide à travers la création d’un nouveau concentrateur de notification et de configuration de l’authentification avec APNS en utilisant le certificat d’émission **.p12** que vous avez créé. Si vous souhaitez utiliser un concentrateur de notification que vous avez déjà créé, vous pouvez passer à l’étape 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>Nous souhaitons configurer la connexion APNS, dans le portail d’Azure, ouvrir les paramètres de Notification concentrateur, ande cliquez sur <b>Notification Services</b>, puis cliquez sur l’élément <b>Apple (APNS)</b> dans la liste. Une fois terminé, cliquez sur <b>Télécharger un certificat</b> et sélectionnez le certificat de <b>.p12</b> que vous avez exportés plus haut, ainsi que le mot de passe pour le certificat.</p>
<p>Veillez à sélectionner le mode <b>Sandbox</b> dans la mesure où vous allez envoyer des messages de transmission dans un environnement de développement. Utilisez uniquement le paramètre de <b>Production</b> si vous souhaitez envoyer des notifications de type Pousser aux utilisateurs qui ont déjà acheté votre application à partir du magasin.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)


Votre concentrateur de notification est maintenant configuré pour fonctionner avec APNS, et que les chaînes de connexion pour enregistrer votre application et d’envoyer des notifications de type Pousser.


##<a name="connect-your-app-to-the-notification-hub"></a>Votre application de se connecter au concentrateur de notification

#### <a name="create-a-new-project"></a>Créez un nouveau projet

1. Dans Xamarin Studio, créez un nouveau projet d’e/s et sélectionnez l' **Unifiée des API** > modèle**d’Application vue unique** .

    ![Xamarin Studio - Type de la sélection d’une Application][31]

2. Ajoutez une référence au composant de messagerie Azure. Dans la vue de la Solution, cliquez sur le dossier **composants** pour votre projet et choisissez **Obtenir les composants plus**. Recherchez le composant **De messagerie Azure** et ajoutez le composant à votre projet.

3. Dans **AppDelegate.cs**, ajoutez le code suivant à l’aide d’instruction :

        using WindowsAzure.Messaging;

4. Déclarez une instance de **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

5. Créez une classe de **Constants.cs** avec les variables suivantes :

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. Dans **AppDelegate.cs**, mise à jour **FinishedLaunching()** pour faire correspondre les éléments suivants :

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }

            return true;
        }

7. Substituez la méthode **RegisteredForRemoteNotifications()** dans **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. Substituez la méthode **ReceivedRemoteNotification()** dans **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Créer la méthode suivante de **ProcessNotification()** dans **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] Vous pouvez choisir de substituer **FailedToRegisterForRemoteNotifications()** pour gérer les situations comme aucune connexion réseau. Ceci est particulièrement important où l’utilisateur peut démarrer votre application en mode hors connexion (par exemple, avion) et que vous souhaitez gérer les push de scénarios spécifiques à votre application de messagerie.


10. Exécutez l’application sur un périphérique.


## <a name="sending-push-notifications"></a>Envoi de Notifications de type Pousser


Vous pouvez tester la réception des notifications de type Pousser dans votre application en envoyant des notifications dans le [Portail Azure] via la capacité de **Tester Envoyer** dans l’ensemble d’outils de **résolution des problèmes** , directement dans la page de concentrateur de notification, comme indiqué dans l’écran ci-dessous.

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

Les notifications de transmission sont généralement envoyées via un service back-end, tels que les Services mobiles ou ASP.NET à l’aide d’une bibliothèque compatible. Vous pouvez également utiliser l’API REST directement à envoyer des messages de type Pousser si une bibliothèque n’est pas disponible dans votre scénario. 

Dans ce didacticiel, nous faire simple et simplement démontrer votre application cliente de test en envoyant des notifications à l’aide du Kit de développement .NET pour moyeux de notification dans une application de console au lieu d’un service back-end. Nous vous recommandons le didacticiel [Utilisation des concentrateurs de Notification pour les notifications de type Pousser aux utilisateurs](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) que l’étape suivante pour l’envoi de notifications à partir d’un serveur principal d’ASP.NET. Toutefois, les approches suivantes peuvent être utilisés pour envoyer des notifications :

* **Interface REST**: vous pouvez bénéficier de notification de transmission sur n’importe quelle plate-forme de serveur principal à l’aide de l' [interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Kit de développement logiciel Microsoft Azure Notification concentrateurs .NET**: dans le Nuget Gestionnaire de package de Visual Studio, exécutez [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : [l’utilisation de concentrateurs de Notification de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

**Applications Mobile**: pour obtenir un exemple de la façon d’envoyer des notifications à partir d’un back-end le Service Azure App Apps Mobile qui est intégré avec les concentrateurs de Notification, reportez-vous à la section [Ajouter des notifications de type pousser à votre application mobile](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

* **Java / PHP**: pour obtenir un exemple de l’envoi de notifications de type pousser à l’aide de l’API reste, reportez-vous à la section « Comment faire pour utiliser des concentrateurs de Notification à partir de Java/PHP » ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).


####<a name="optional-send-push-notifications-from-a-net-console-app"></a>(Facultatif) Envoyer des Notifications de transmission à partir d’une application de Console .NET

Dans cette section, nous vous enverrons des notifications de type pousser à l’aide d’une simple application de console .NET. Pour les besoins de cet exemple, nous allons la transformer dans un environnement de développement de Windows qui a déjà installée de Visual Studio.

1. Dans Visual Studio, créez une nouvelle application de console Visual C# :

    ![Visual Studio - créez une nouvelle application de console][213]

2. Dans Visual Studio, cliquez sur **Outils**, cliquez sur **Gestionnaire de package de NuGet**, puis cliquez sur **Console de Gestionnaire de package**.

    La console du Gestionnaire de package doit sont ancrée au bas de votre espace de travail Visual Studio.

3. La valeur **par défaut du projet de** votre projet d’application console dans la fenêtre de la Console du Gestionnaire de package et puis dans la fenêtre de la console, exécutez la commande suivante :

        Install-Package Microsoft.Azure.NotificationHubs

    Cette opération ajoute une référence à la Notification de concentrateurs Azure SDK à l’aide du <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">package Microsoft.Azure.Notification concentrateurs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Ouvrir le `Program.cs` de fichiers et ajoutez le code suivant `using` instruction, veiller à ce que nous pouvons utiliser les classes Azure et des fonctions dans votre classe principale :

        using Microsoft.Azure.NotificationHubs;

3. Dans votre `Program` de classe, ajoutez la méthode suivante (n’oubliez pas de remplacer la **chaîne de connexion** et le **nom de concentrateur**) :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Ajoutez les lignes suivantes dans votre `Main` méthode :

         SendNotificationAsync();
         Console.ReadLine();

5. Appuyez sur la touche F5 pour exécuter l’application. Quelques secondes, une notification de transmission doit s’afficher sur votre périphérique. Si vous utilisez le Wi-Fi ou à un réseau de données cellulaires, assurez-vous que vous disposez d’une connexion internet active sur le périphérique.

Vous pouvez trouver toutes les charges possibles dans Apple [Local et Guide de programmation de Notification Push].

####<a name="optional-send-notifications-from-a-mobile-service"></a>(Facultatif) Envoyer des Notifications à partir d’un Service Mobile

Dans cette section, nous vous enverrons des notifications de type pousser à l’aide d’un service mobile via un script de nœud.

Pour envoyer une notification à l’aide d’un service mobile, suivez la [mise en route avec les Services mobiles], puis :

1. Connectez-vous au [Portail classique d’Azure]et sélectionnez votre service mobile.

2. Sélectionnez l’onglet **Planificateur** dans la partie supérieure.

    ![Azure Portal classique - planificateur][215]

3. Créer une nouvelle tâche planifiée, insérer un nom et sélectionnez **à la demande**.

    ![Azure classique Portal - créer un nouveau travail][216]

4. Lors de la création du travail, cliquez sur le nom du travail. Cliquez ensuite sur l’onglet **Script** de la barre supérieure.

5. Insérer le script suivant à l’intérieur de la fonction de planificateur. Veillez à remplacer les espaces réservés avec votre nom de concentrateur de notification et de la chaîne de connexion pour *DefaultFullSharedAccessSignature* que vous avez obtenu précédemment. Cliquez sur **Enregistrer**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );


6. Cliquez sur **Exécuter une fois** sur la barre du bas. Vous devriez recevoir une alerte sur votre périphérique.

##<a name="next-steps"></a>Étapes suivantes

Dans cet exemple simple, vous envoyé des notifications de type pousser à tous vos périphériques d’e/s. Pour cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation des concentrateurs de Notification pour les notifications de type Pousser aux utilisateurs]. Si vous souhaitez que segmenter les utilisateurs en groupes d’intérêt, vous pouvez lire les [Concentrateurs de Notification utilisé pour envoyer des informations de dernière minute]. Pour en savoir plus sur l’utilisation de concentrateurs de Notification dans le [Guide de concentrateurs de Notification] et dans la [Notification concentrateurs sur les procédures pour iOS].


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Mise en route de Services mobiles]: /develop/mobile/tutorials/get-started-xamarin-ios
[Azure Portal classique]: https://manage.windowsazure.com/
[Conseils de concentrateurs de notification]: http://msdn.microsoft.com/library/jj927170.aspx
[Procédures de concentrateurs notification pour iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Utiliser des concentrateurs de Notification pour les notifications de type Pousser aux utilisateurs]: /manage/services/notification-hubs/notify-users-aspnet
[Concentrateurs de Notification permet d’envoyer des informations de dernière minute]: /manage/services/notification-hubs/breaking-news-dotnet

[Local et poussez le Guide de programmation de Notification]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Azure Portal]: https://portal.azure.com
