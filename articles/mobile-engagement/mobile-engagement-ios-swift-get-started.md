<properties
    pageTitle="Mise en route avec Azure Mobile Engagement pour iOS dans Swift | Microsoft Azure"
    description="Découvrez comment utiliser Azure Mobile Engagement avec Analytique et d’envoyer des Notifications pour les applications d’iOS."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Mise en route avec Azure Mobile Engagement pour iOS Apps dans Swift

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous indique comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et d’envoyer des notifications de type pousser à segmenté d’utilisateurs à une application iOS.
Dans ce didacticiel, vous créez une application iOS vide qui collecte les données de base et qui reçoit des notifications de type pousser à l’aide d’Apple Push Notification système (APNS).

Ce didacticiel requiert les éléments suivants :

+ 8 XCode, que vous pouvez installer à partir de votre MAC App Store
+ l' [Engagement de Mobile iOS SDK]
+ Certificat de notification push (.p12) que vous pouvez obtenir sur votre centre de développement de Apple

> [AZURE.NOTE] Ce didacticiel utilise Swift version 3.0. 

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels Mobile Engagement pour les applications d’e/s.

> [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).

##<a id="setup-azme"></a>Engagement de Mobile le programme d’installation pour votre application iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application à la principale mission de Mobile

Ce didacticiel présente une « intégration de base », qui est l’ensemble minimal requis pour collecter des données et d’envoyer une notification de transmission. Vous trouverez la documentation sur l’intégration complète dans l' [intégration de kit de développement logiciel de Mobile Engagement iOS](mobile-engagement-ios-sdk-overview.md)

Nous allons créer une application de base avec XCode pour illustrer l’intégration :

###<a name="create-a-new-ios-project"></a>Créez un nouveau projet d’e/s

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Votre application de se connecter au back-end de l’Engagement de Mobile

1. Télécharger l' [Engagement de Mobile iOS SDK]
2. Extraire le. tar.gz des fichiers dans un dossier de votre ordinateur
3. Cliquez avec le bouton droit sur le projet et sélectionnez « Ajouter des fichiers à... »

    ![][1]

4. Accédez au dossier où vous avez extrait le Kit de développement logiciel, puis sélectionnez le `EngagementSDK` dossier puis appuyez sur OK.

    ![][2]

5. Ouvrir le `Build Phases` onglet et dans le `Link Binary With Libraries` menu Ajouter les structures comme indiqué ci-dessous :

    ![][3]

8. Créer un en-tête de pontage pour être en mesure d’utiliser les API du Kit de développement objectif C en choisissant Fichier > Nouveau > fichier > iOS > Source > fichier d’en-tête.

    ![][4]

9. Modifier le fichier d’en-tête pontage pour exposer le code Mobile Engagement Objective-C à votre code Swift, ajoutez les importations ci-après :

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"

10. Sous paramètres de génération, assurez-vous que la génération de l’en-tête de pontage Objective-C sous compilateur Swift - génération du Code a un chemin d’accès à cet en-tête. Voici un exemple de chemin d’accès : **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (selon le chemin d’accès)**

    ![][6]

11. Revenez au portail Azure dans la page *d’Informations sur la connexion* de votre application et de copier la chaîne de connexion

    ![][5]

12. Collez maintenant la chaîne de connexion dans le `didFinishLaunchingWithOptions` déléguer

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>Activation de l’analyse en temps réel

Avant de commencer l’envoi de données et de veiller à ce que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) sur le serveur principal d’Engagement de Mobile.

1. Ouvrez le fichier **ViewController.swift** et remplacez la classe de base de **ViewController** à **EngagementViewController**:

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>Application de se connecter grâce à une surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>L’activation de pousser des Notifications et dans l’application de messagerie

Engagement de Mobile vous permet d’interagir et de parvenir à vos utilisateurs avec l’envoi de Notifications et de messagerie dans l’application dans le cadre de campagnes. Ce module est appelé la portée dans le portail Mobile Engagement.
Les sections suivantes va configurer votre application pour les recevoir.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Activer votre application recevoir des Notifications Push en mode silencieux

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Ajouter la bibliothèque de portée à votre projet

1. Cliquez avec le bouton droit sur votre projet.
2. Sélectionnez`Add file to ...`
3. Accédez au dossier où vous avez extrait le SDK
4. Sélectionnez le `EngagementReach` dossier
5. Cliquez sur Ajouter
6. Modifier le fichier d’en-tête pontage pour exposer les en-têtes d’atteindre de Mobile Engagement Objective-C et ajoutez les importations ci-après :

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Modifiez votre Application de délégué

1. À l’intérieur de la `didFinishLaunchingWithOptions` - créer un module de la portée et passer à la ligne d’initialisation Engagement existant :

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Activer votre application de recevoir des Notifications Push de APNS
1. Ajoutez la ligne suivante à la `didFinishLaunchingWithOptions` méthode :

        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }

2. Ajouter le `didRegisterForRemoteNotificationsWithDeviceToken` méthode comme suit :

        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. Ajouter le `didReceiveRemoteNotification:fetchCompletionHandler:` méthode comme suit :

        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Engagement de Mobile iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
