<properties
    pageTitle="Mise en route avec Azure Engagement Mobile pour Xamarin.iOS"
    description="Découvrez comment utiliser Azure Mobile Engagement avec Analytique et d’envoyer des Notifications pour les applications Xamarin.iOS."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Mise en route avec Azure Engagement Mobile pour des applications Xamarin.iOS

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous indique comment Azure Mobile Engagement permet de comprendre l’utilisation de votre application et d’envoyer des notifications de type Pousser aux utilisateurs segmentés dans une application Xamarin.iOS.
Dans ce didacticiel, vous créez une application de Xamarin.iOS vide qui collecte les données de base et qui reçoit des notifications de type pousser à l’aide d’Apple Push Notification système (APNS).

Ce didacticiel requiert les éléments suivants :

+ [Xamarin Studio](http://xamarin.com/studio). Ce didacticiel utilise Xamarin Studio, mais vous pouvez également utiliser Visual Studio avec Xamarin. Pour les instructions d’installation, consultez [le programme d’installation et installer pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
+ [Kit de développement logiciel de Mobile Engagement Xamarin](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).

##<a id="setup-azme"></a>Engagement de Mobile le programme d’installation pour votre application iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application à la principale mission de Mobile

Ce didacticiel présente une « intégration de base » qui est le minimum requis pour collecter des données et d’envoyer une notification de transmission.

Nous allons créer une application de base avec Xamarin pour illustrer l’intégration :

###<a name="create-a-new-xamarinios-project"></a>Créez un nouveau projet Xamarin.iOS

1. Lancez Xamarin Studio. Accédez au **fichier** -> **Nouveau** -> **Solution** 

    ![][1]

2. Sélectionnez **l’Application d’affichage unique**, assurez-vous que la langue sélectionnée est **C#** et puis cliquez sur **suivant**.

    ![][2]

3. Renseignez le **Nom de l’application** et l' **Identificateur d’organisation** , puis cliquez sur **suivant**. 

    ![][3]

    > [AZURE.IMPORTANT] Assurez-vous que le profil de publication que vous utilisez finalement pour déployer votre application iOS utilise un ID d’application qui correspond exactement avec l’identificateur de groupe que vous avez ici. 

4. Mettre à jour le **Nom du projet**, le **Nom de la Solution** et **l’emplacement** si nécessaire et cliquez sur **créer**.

    ![][4]
 
Xamarin Studio crée l’application de démonstration dans lequel nous intégrera Mobile Engagement. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Votre application de se connecter au back-end de l’Engagement de Mobile

1. Cliquez avec le bouton droit sur le dossier **Packages** dans les fenêtres de la Solution et sélectionnez **Ajouter des Packages...**

    ![][5]

2. Rechercher le **Kit de développement logiciel Microsoft Azure Mobile Engagement Xamarin** et l’ajouter à votre solution.  

    ![][6]
   
3. Ouvrez **AppDelegate.cs** et ajoutez l’instruction using :

        using Microsoft.Azure.Engagement.Xamarin;

4. Dans la méthode **FinishedLaunching** , ajoutez la ligne suivante pour initialiser la connexion avec le serveur principal Mobile Engagement. Assurez-vous d’ajouter votre **ConnectionString**. Ce code utilise également un mannequin **NotificationIcon** qui est ajoutée par le Kit de développement Engagement Mobile peut vouloir remplacer. 

        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

##<a id="monitor"></a>Activation de l’analyse en temps réel

Avant de commencer l’envoi de données et d’assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran à la principale mission de Mobile.

1. Ouvrez **ViewController.cs** et ajoutez l’instruction using :

        using Microsoft.Azure.Engagement.Xamarin;

2. Remplacez la classe de laquelle `ViewController` hérite de `UIViewController` à `EngagementViewController`. 

##<a id="monitor"></a>Application de se connecter grâce à une surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activer les notifications de transmission et de la messagerie dans l’application

Engagement de Mobile vous permet d’interagir avec les utilisateurs et atteindre avec les notifications de transmission et de la messagerie dans le cadre de campagnes dans l’application. Ce module est appelé la portée dans le portail Mobile Engagement.
Les sections suivantes, configurées votre application pour les recevoir.

### <a name="modify-your-application-delegate"></a>Modifiez votre Application de délégué

1. Ouvrez **AppDelegate.cs** et ajoutez le code suivant à l’aide d’instruction :

        using System; 

2. Maintenant à l’intérieur de la `FinishedLaunching` méthode, ajoutez le code suivant pour enregistrer les messages envoyés après`EngagementAgent.init(...)`

        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. Enfin - mettre à jour ou ajoutez les méthodes suivantes :

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. Dans votre fichier **Info.plist** dans la solution, vérifiez que l' **Identificateur de groupe** correspond à l' **ID de l’application** que vous avez dans votre profil de déploiement dans le centre de développement d’Apple. 

    ![][7]

5. Dans le même fichier **Info.plist** , assurez-vous d’avoir activé **l’Activer les Modes de fond** et des **Notifications à distance**. 

    ![][8]

6. Exécutez l’application sur le périphérique que vous avez associé à ce profil de publication. 

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
