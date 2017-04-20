<properties
    pageTitle="Azure Mobile Engagement iOS procédure de mise à jour de SDK | Microsoft Azure"
    description="Dernières mises à jour et les procédures pour iOS SDK pour Azure Mobile Engagement"
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
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="upgrade-procedures"></a>Procédures de mise à niveau

Si vous avez déjà intégré une version plus ancienne de l’Engagement dans votre application, vous devez considérer les points suivants lors de la mise à niveau de la SDK.

Pour chaque nouvelle version du Kit de développement vous devez d’abord remplacer (supprimer et réimporter dans xcode) les dossiers EngagementSDK et EngagementReach.

##<a name="from-300-to-400"></a>À partir de 3.0.0 à 4.0.0

### <a name="xcode-8"></a>XCode 8
XCode 8 est obligatoire à partir de la version 4.0.0 du SDK.

> [AZURE.NOTE] Si vous dépendez vraiment XCode 7 vous pouvez utiliser l' [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Il existe un bogue connu sur le module de la portée de cette version précédente lors de l’exécution sur des périphériques d’e/s 10 : notifications système n’étaient pas activées. Pour résoudre le problème vous devrez implémenter l’API déconseillée `application:didReceiveRemoteNotification:` dans votre application délégué comme suit :

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Nous ne recommandons pas cette solution de contournement** en tant que ce problème peut changer dans toute mise à niveau de la version à venir iOS (même mineur) car cette iOS API est déconseillée. Vous devez passer XCode 8 dès que possible.

### <a name="usernotifications-framework"></a>Cadre de UserNotifications
Vous devez ajouter la `UserNotifications` framework dans vos Phases de génération.

dans l’Explorateur de projets, ouvrez le volet des projets et sélectionnez la bonne cible. Ensuite, ouvrez l’onglet **« Build phases »** et dans le menu **« binaire avec des bibliothèques de liens »** , ajoutez framework `UserNotifications.framework` -le lien en tant que`Optional`

### <a name="application-push-capability"></a>« Pousser » application
XCode 8 peut réinitialiser votre application pousser la capacité, veuillez le vérifier double le `capability` l’onglet de votre cible sélectionné.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Ajoutez le nouveau code d’enregistrement de notification des e/s 10
L’extrait de code antérieur à l’enregistrement de l’application aux notifications fonctionne toujours mais utilise les API à éviter lors de l’exécution sur iOS 10.

Importer le `User Notification` framework :

        #import <UserNotifications/UserNotifications.h> 

Dans votre délégué d’application `application:didFinishLaunchingWithOptions` méthode remplacer :

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

par :

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

### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Si vous avez déjà votre propre implémentation de UNUserNotificationCenterDelegate

Le Kit de développement a également sa propre implémentation du protocole UNUserNotificationCenterDelegate. Il est utilisé par le Kit de développement pour contrôler le cycle de vie des notifications d’Engagement sur des appareils exécutant sur iOS 10 ou supérieur. Si le Kit de développement logiciel détecte votre délégué, il n’utilisera pas sa propre implémentation, car il peut y avoir qu’un seul délégué UNUserNotificationCenter par l’application. Cela signifie que vous devrez ajouter la logique de l’Engagement à votre propre délégué.

Il existe deux manières de procéder.

Simplement par transférer votre délégué appelle le Kit de développement SDK :

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Ou en héritant de la `AEUserNotificationHandler` classe

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [AZURE.NOTE] Vous pouvez déterminer si une notification émane de l’Engagement ou ne pas en passant son `userInfo` dictionnaire pour l’Agent de `isEngagementPushPayload:` méthode de classe.

##<a name="from-200-to-300"></a>À partir de 2.0.0 à 3.0.0
Supprimer la prise en charge des e/s 4.X. À partir de cette version de la cible de déploiement de votre application doit être d’au moins 6 d’iOS.

Si vous utilisez la portée de votre application, vous devez ajouter `remote-notification` de valeur pour le `UIBackgroundModes` tableau dans votre fichier Info.plist pour recevoir des notifications à distance.

La méthode `application:didReceiveRemoteNotification:` doit être remplacé par `application:didReceiveRemoteNotification:fetchCompletionHandler:` dans votre délégué de l’application.

« AEPushDelegate.h » est désapprouvé interface et que vous devez supprimer toutes les références. Cela inclut la suppression `[[EngagementAgent shared] setPushDelegate:self]` et les méthodes du délégué de votre délégué de l’application :

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##<a name="from-1160-to-200"></a>À partir de 1.16.0 à 2.0.0
Voici comment migrer une intégration SDK à partir du service de Capptain offert par Capptain SAS dans une application grâce à Azure Mobile Engagement.
Si vous migrez à partir d’une version antérieure, veuillez consulter le site web Capptain pour effectuer la migration à la question 1.16 tout d’abord, puis appliquer la procédure suivante.

>[AZURE.IMPORTANT] Capptain et l’Engagement de Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous ne souligne que la migration de l’application cliente. Le Kit de développement de l’application de la migration ne migre pas vos données à partir des serveurs Capptain vers les serveurs Mobile Engagement

### <a name="agent"></a>Agent

La méthode `registerApp:` a été remplacée par la nouvelle méthode `init:`. Votre délégué de l’application doivent être mis à jour en conséquence et utiliser la chaîne de connexion :

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

Suivi de SmartAd a été supprimé du SDK, il vous suffit de supprimer toutes les instances de `AETrackModule` classe

### <a name="class-name-changes"></a>Changements de nom de classe

Dans le cadre de la redésignation, il y a deux noms de classe/fichier qui doivent être modifiés.

Le préfixe « CP » de toutes les classes sont renommés avec le préfixe de « AE ».

Exemple :

-   `CPModule.h`est renommé en `AEModule.h`.

Le préfixe « Capptain » de toutes les classes sont renommés avec le préfixe de « Engagement ».

Exemples :

-   La classe `CapptainAgent` est renommé en `EngagementAgent`.
-   La classe `CapptainTableViewController` est renommé en `EngagementTableViewController`.
-   La classe `CapptainUtils` est renommé en `EngagementUtils`.
-   La classe `CapptainViewController` est renommé en `EngagementViewController`.
