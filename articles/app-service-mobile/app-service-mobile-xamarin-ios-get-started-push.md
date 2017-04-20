<properties
    pageTitle="Ajouter des notifications de type pousser à votre application de Xamarin.iOS avec le Service d’application Azure"
    description="Apprenez à utiliser le Service d’application Azure pour envoyer des notifications de type pousser à votre application Xamarin.iOS"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinios-app"></a>Ajouter des notifications de type pousser à votre application Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Vue d’ensemble

Dans ce didacticiel, vous ajoutez les notifications de transmission au projet [Xamarin.iOS rapide démarrer](app-service-mobile-xamarin-ios-get-started.md) afin qu’une notification de transmission est envoyée au périphérique chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez le progiciel d’extension de notification push. Pour plus d’informations, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Conditions préalables

* Utiliser le didacticiel de [démarrage rapide de Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) .

* Un périphérique d’e/s physiques. Les notifications de transmission ne sont pas pris en charge par le simulateur d’e/s.

##<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Enregistrement de l’application de notifications de type Pousser sur le portail des développeurs d’Apple

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

##<a name="configure-your-mobile-app-to-send-push-notifications"></a>Configurer votre application Mobile pour envoyer des notifications de type Pousser

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Mettre à jour le projet serveur pour envoyer des notifications de type Pousser

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="configure-your-xamarinios-project"></a>Configurer votre projet Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##<a name="add-push-notifications-to-your-app"></a>Ajouter des notifications de type pousser à votre application

1. Dans **QSTodoService**, ajoutez la propriété suivante afin **qu’AppDelegate** peut acquérir le client mobile :

            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Ajoutez le code suivant `using` en haut du fichier **AppDelegate.cs** .

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. Dans **AppDelegate**, substituez l’événement **FinishedLaunching** :

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. Dans le même fichier, substituez l’événement **RegisteredForRemoteNotifications** . Dans ce code est enregistré pour une notification de modèle simple qui est envoyée sur toutes les plates-formes prises en charge par le serveur.

    Pour plus d’informations sur les modèles de concentrateurs de Notification, reportez-vous à la section [modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


4. Ensuite, substituez l’événement **DidReceivedRemoteNotification** :

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Votre application est maintenant mise à jour pour prendre en charge les notifications de transmission.

## <a name="test"></a>Notifications de type Pousser de test dans votre application

1. Appuyez sur le bouton **exécuter** pour générer le projet et démarrer l’application dans un dispositif capable d’e/s, puis cliquez sur **OK** pour accepter les notifications de transmission.

    > [AZURE.NOTE] Vous devez explicitement accepter les notifications de type pousser à partir de votre application. Cette demande ne se produit que la première fois que l’application s’exécute.

2. Dans l’application, entrez une tâche, puis cliquez sur le signe plus (**+**) icône.

3. Vérifiez qu’une notification est reçue, puis cliquez sur **OK** pour fermer la notification.

4. Répétez l’étape 2 et immédiatement fermer l’application, puis vérifiez qu’une notification s’affiche.

Vous avez terminé ce didacticiel.

<!-- Images. -->

<!-- URLs. -->



