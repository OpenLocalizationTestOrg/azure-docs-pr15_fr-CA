<properties
    pageTitle="Mise en route avec Azure Mobile Engagement pour iOS objectif c | Microsoft Azure"
    description="Découvrez comment utiliser Azure Mobile Engagement avec analytique et l’envoi de notifications pour les applications d’e/s."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/05/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Mise en route avec Azure Mobile Engagement pour applications iOS en C d’objectif

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous indique comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et d’envoyer des notifications de type pousser à segmenté d’utilisateurs à une application iOS.
Dans ce didacticiel, vous créez une application iOS vide qui collecte les données de base et qui reçoit des notifications de type pousser à l’aide d’Apple Push Notification système (APNS).

Ce didacticiel requiert les éléments suivants :

+ 8 XCode, que vous pouvez installer à partir de votre MAC App Store
+ l' [Engagement de Mobile iOS SDK]

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels Mobile Engagement pour les applications d’e/s.

> [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).

##<a id="setup-azme"></a>Engagement de Mobile le programme d’installation pour votre application iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application à la principale mission de Mobile

Ce didacticiel présente une « intégration de base », qui est l’ensemble minimal requis pour collecter des données et d’envoyer une notification de transmission. Vous trouverez la documentation sur l’intégration complète dans l' [intégration de kit de développement logiciel de Mobile Engagement iOS](mobile-engagement-ios-sdk-overview.md)

Nous allons créer une application de base avec XCode pour illustrer l’intégration.

###<a name="create-a-new-ios-project"></a>Créez un nouveau projet d’e/s

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connecter votre application à la principale mission de Mobile

1. Télécharger l' [Engagement de Mobile iOS SDK].
2. Extraire le. tar.gz des fichiers dans un dossier de votre ordinateur.
3. Droit sur le projet et sélectionnez **Ajouter les fichiers au**.

    ![][1]

4. Accédez au dossier où vous avez extrait le Kit de développement logiciel, sélectionnez le `EngagementSDK` dossier et appuyez sur **OK**.

    ![][2]

5. Ouvrir l’onglet **Créer des Phases** et dans le menu **Binaire avec des bibliothèques de liens** , ajoutez les infrastructures comme indiqué ci-dessous :

    ![][3]

6. Revenez au portail Azure dans la page **d’Informations sur la connexion** de votre application et copier la chaîne de connexion.

    ![][4]

7. Ajoutez la ligne de code suivante dans votre fichier **AppDelegate.m** .

        #import "EngagementAgent.h"

8. Collez maintenant la chaîne de connexion dans le `didFinishLaunchingWithOptions` déléguer.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
        }

9. `setTestLogEnabled`est une instruction facultative qui permet les journaux SDK vous permet d’identifier les problèmes. 

##<a id="monitor"></a>Activer l’analyse en temps réel

Avant de commencer l’envoi de données et de veiller à ce que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) sur le serveur principal d’Engagement de Mobile.

1. Ouvrez le fichier **ViewController.h** et importer **EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. Remplaçons maintenant la super classe de l’interface **ViewController** par `EngagementViewController`:

    `@interface ViewController : EngagementViewController`

##<a id="monitor"></a>Application de se connecter grâce à une surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activer les notifications de transmission et de la messagerie dans l’application

Engagement de Mobile vous permet d’interagir avec les utilisateurs et atteindre avec les notifications de transmission et de la messagerie dans le cadre de campagnes dans l’application. Ce module est appelé la portée dans le portail Mobile Engagement.
Les sections suivantes, configurées votre application pour les recevoir.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Activer votre application recevoir des Notifications Push en mode silencieux

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]  

### <a name="add-the-reach-library-to-your-project"></a>Ajouter la bibliothèque de portée à votre projet

1. Cliquez droit sur votre projet.
2. Sélectionnez **Ajouter un nouveau fichier**.
3. Accédez au dossier où vous avez extrait le Kit de développement logiciel.
4. Sélectionnez le `EngagementReach` dossier.
5. Cliquez sur **Ajouter**.

### <a name="modify-your-application-delegate"></a>Modifiez votre Application de délégué

1. Dans **AppDeletegate.m** , fichier, importer le module de parvenir à Engagement.

        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>

2. À l’intérieur de la `application:didFinishLaunchingWithOptions` méthode, créez un module de portée et passer à la ligne d’initialisation Engagement existant :

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Activer votre application de recevoir des Notifications Push de APNS

1. Ajoutez la ligne suivante à la `application:didFinishLaunchingWithOptions` méthode :

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

2. Ajouter le `application:didRegisterForRemoteNotificationsWithDeviceToken` méthode comme suit :

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }

3. Ajouter le `didFailToRegisterForRemoteNotificationsWithError` méthode comme suit :

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           
           NSLog(@"Failed to get token, error: %@", error);
        }

4. Ajouter le `didReceiveRemoteNotification:fetchCompletionHandler` méthode comme suit :

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Engagement de Mobile iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

