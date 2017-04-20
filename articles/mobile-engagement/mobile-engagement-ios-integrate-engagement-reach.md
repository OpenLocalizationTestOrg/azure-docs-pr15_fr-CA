<properties
    pageTitle="Azure Mobile Engagement iOS intégration d’atteindre SDK | Microsoft Azure"
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

#<a name="how-to-integrate-engagement-reach-on-ios"></a>Comment intégrer l’atteindre Engagement sur iOS

Vous devez suivre la procédure d’intégration décrite dans [comment intégrer l’Engagement sur le document de l’e/s](mobile-engagement-ios-integrate-engagement.md) avant de suivre ce guide.

Cette documentation nécessite XCode 8. Si vous dépendez vraiment XCode 7 vous pouvez utiliser l' [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Il existe un bogue connu sur cette version précédente lors de l’exécution sur des périphériques d’e/s 10 : notifications système n’étaient pas activées. Pour résoudre le problème vous devrez implémenter l’API déconseillée `application:didReceiveRemoteNotification:` dans votre application délégué comme suit :

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Nous ne recommandons pas cette solution de contournement** en tant que ce problème peut changer dans toute mise à niveau de la version à venir iOS (même mineur) car cette iOS API est déconseillée. Vous devez passer XCode 8 dès que possible.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Activer votre application recevoir des Notifications Push en mode silencieux

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

##<a name="integration-steps"></a>Étapes d’intégration

### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Incorporer le SDK atteindre d’Engagement dans votre projet d’e/s

-   Ajouter le Kit de développement de portée de votre projet Xcode. Dans Xcode, accédez à **projet \> ajouter au projet** et cliquez sur le `EngagementReach` dossier.

### <a name="modify-your-application-delegate"></a>Modifiez votre Application de délégué

-   En haut de votre fichier de mise en œuvre, importer le module Engagement atteindre :

        [...]
        #import "AEReachModule.h"

-   Dans la méthode `applicationDidFinishLaunching:` ou `application:didFinishLaunchingWithOptions:`, créez un module de portée et passer à la ligne d’initialisation Engagement existant :

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
          AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
          [...]

          return YES;
        }

-   Modifier la chaîne **« icon.png »** avec le nom d’image que vous souhaitez placer votre icône de notification.
-   Si vous souhaitez utiliser l’option *mise à jour la valeur du badge* dans les campagnes de la portée ou si vous souhaitez utiliser les push de native \<API de SaaS/portée de campagne/format/Native Push\> campagnes, vous devez laisser la portée de module gérer l’icône badge lui-même (il efface automatiquement le badge de l’application et également réinitialiser la valeur stockée par l’Engagement, chaque fois que l’application est démarrée ou foregrounded). Pour ce faire, vous devez ajouter la ligne suivante après l’initialisation de module de portée :

        [reach setAutoBadgeEnabled:YES];

-   Si vous souhaitez gérer la transmission des données de portée, vous devez laisser votre délégué de l’Application sont conformes à la `AEReachDataPushDelegate` protocole. Ajoutez la ligne suivante après l’initialisation de module de portée :

        [reach setDataPushDelegate:self];

-   Puis vous pouvez implémenter les méthodes `onDataPushStringReceived:` et `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` dans votre délégué de l’application :

        -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
        {
           NSLog(@"String data push message with category <%@> received: %@", category, body);
           return YES;
        }

        -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
        {
           NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
           // Do something useful with decodedBody like updating an image view
           return YES;
        }

### <a name="category"></a>Catégorie

Le paramètre category est facultatif lorsque vous créez une campagne de Push de données et permet la qu'exécute un push de vous permet de filtrer les données. Cette option est utile si vous souhaitez distribuer les différents types de `Base64` données et que vous souhaitez pour identifier leur type avant leur analyse.

**Votre application est maintenant prête à recevoir et afficher le contenu de la portée.**

##<a name="how-to-receive-announcements-and-polls-at-any-time"></a>Comment recevoir des annonces et des interrogations à tout moment

Engagement peut envoyer des notifications de portée pour les utilisateurs finaux à tout moment en utilisant le Service de Notification de pousser Apple.

Pour activer cette fonctionnalité, vous devrez préparer votre application pour les notifications de type Pousser Apple et de modifier votre délégué de l’application.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Préparer votre application pour les notifications de type Pousser Apple

Suivez le guide : [comment préparer votre Application pour les Notifications Push d’Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Ajoutez le code de client nécessaire

*À ce stade votre application doit avoir un certificat d’émetteur Apple enregistré dans le site Web frontal de l’Engagement.*

Si elle ne le n'est pas déjà fait, vous devez inscrire votre application de recevoir des notifications de type Pousser.

* Importer le `User Notification` framework :

        #import <UserNotifications/UserNotifications.h>

* Ajoutez la ligne suivante au démarrage de votre application (en général en `application:didFinishLaunchingWithOptions:`) :

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

Ensuite, vous devez fournir à l’Engagement, le jeton de périphérique renvoyé par les serveurs d’Apple. Cela est effectué dans la méthode nommée `application:didRegisterForRemoteNotificationsWithDeviceToken:` dans votre délégué de l’application :

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Enfin, vous devez informer le SDK de l’Engagement, lorsque votre application reçoit une notification à distance. Pour ce faire, appelez la méthode `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` dans votre délégué de l’application :

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [AZURE.NOTE] La méthode ci-dessus est introduite dans iOS 7. Si vous ciblez iOS < 7, veillez à mettre en œuvre la méthode `application:didReceiveRemoteNotification:` dans votre délégué de l’application et l’appel de `applicationDidReceiveRemoteNotification` sur la EngagementAgent en passant de zéro au lieu de la `handler` argument :

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] Par défaut, l’Engagement atteindre contrôle le completionHandler. Si vous souhaitez répondre manuellement à la `handler` bloquer dans votre code, vous pouvez passer zéro le `handler` argument et contrôle l’exécution de bloc vous-même. Consultez le `UIBackgroundFetchResult` type pour obtenir une liste des valeurs possibles.


### <a name="full-example"></a>Exemple complet

Voici un exemple complet d’intégration :

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="if-you-have-your-own-unusernotificationcenterdelegate-implementation"></a>Si vous disposez de votre propre implémentation de UNUserNotificationCenterDelegate

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

##<a name="how-to-customize-campaigns"></a>Comment personnaliser des campagnes

### <a name="notifications"></a>Notifications

Il existe deux types de notifications : notifications système et dans l’application.

Les notifications système sont gérées par les e/s et ne peuvent pas être personnalisées.

Les notifications dans l’application sont constituées d’une vue qui est dynamiquement ajoutée à la fenêtre d’application actuelle. Il s’agit d’une superposition de notification. Les superpositions de notification sont pour une intégration rapide car ils ne nécessite pas de modifier n’importe quel affichage dans votre application.

#### <a name="layout"></a>Mise en page

Pour modifier l’aspect de vos notifications dans l’application, il suffit de modifier le fichier `AENotificationView.xib` à vos besoins, tant que vous gardez les valeurs des balises et les types des sous-vues existants.

Par défaut, les notifications dans l’application sont présentées en bas de l’écran. Si vous souhaitez les afficher en haut de l’écran, modifier le `AENotificationView.xib` et modifiez le `AutoSizing` propriété de l’affichage principal afin qu’il peut être conservé dans le haut de son superview.

#### <a name="categories"></a>Catégories

Lorsque vous modifiez la disposition fournie, vous modifiez l’aspect de toutes vos notifications. Les catégories vous permettent de définir recherche ciblée différentes (et éventuellement des comportements) pour les notifications. Une catégorie peut être spécifiée lorsque vous créez une campagne de portée. Gardez à l’esprit que catégories vous permettent également de personnaliser les annonces et les sondages, qui est décrit plus loin dans ce document.

Pour inscrire un gestionnaire de catégorie pour les notifications, vous devez ajouter un appel une fois que la portée module est initialisé.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier`doit être une instance d’un objet qui est conforme au protocole de `AENotifier`.

Vous pouvez implémenter les méthodes de protocole par vous-même, ou vous pouvez choisir d’implémenter de nouveau la classe existante `AEDefaultNotifier` qui exécute déjà la plupart du travail.

Par exemple, si vous souhaitez redéfinir le mode de notification pour une catégorie spécifique, vous pouvez suivre cet exemple :

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Cet exemple simple de la catégorie que vous possédez un fichier nommé `MyNotificationView.xib` dans l’ensemble de votre application principale. Si la méthode n’est pas en mesure de trouver un correspondant `.xib`, la notification ne s’affichera pas et Engagement aura pour résultat un message dans la console.

Le fichier fourni Plume doit respecter les règles suivantes :

-   Il ne doit contenir qu’une seule vue.
-   Sous-vues doivent être les mêmes types que ceux qui sont à l’intérieur du fichier fourni Plume nommées`AENotificationView.xib`
-   Sous-vues doivent avoir les mêmes balises que celles à l’intérieur de l’élément fourni fichier Plume nommé`AENotificationView.xib`

> [AZURE.TIP] Copier simplement le fichier fourni Plume, nommé `AENotificationView.xib`et commencez à travailler à partir de là. Mais attention, la vue à l’intérieur de ce fichier de plume est associée à la classe `AENotificationView`. Cette classe de redéfinir la méthode `layoutSubViews` pour déplacer et redimensionner les sous-vues en fonction du contexte. Vous souhaiterez peut-être remplacer avec une `UIView` ou la classe d’affichage personnalisé pour vous.

Si vous avez besoin d’une personnalisation plus approfondie de vos notifications (si vous souhaitez par exemple charger votre vue directement à partir du code), il est recommandé d’examiner la documentation de classe et de code source fournie de `Protocol ReferencesDefaultNotifier` et `AENotifier`.

Notez que vous pouvez utiliser l’utilitaire de notification même pour plusieurs catégories.

Vous pouvez également redéfini le notificateur par défaut comme suit :

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Gestion de la notification

Lorsque vous utilisez la catégorie par défaut, certaines méthodes de cycle de vie sont appelées sur le `AEReachContent` objet de rapports statistiques et mettre à jour l’état de la campagne :

-   Lorsque la notification est affichée dans l’application, le `displayNotification` méthode est appelée (rapports statistiques) par `AEReachModule` si `handleNotification:` renvoie `YES`.
-   Si la notification est fermée, le `exitNotification` méthode est appelée, la statistique est signalée et campagnes suivant peuvent maintenant être traitées.
-   Si la notification est activée, `actionNotification` est appelée, statistique est signalée et l’action associée est effectuée.

Si votre implémentation de `AENotifier` contourne le comportement par défaut, vous devez appeler ces méthodes de cycle de vie par vous-même. Les exemples suivants illustrent certains cas où le comportement par défaut est ignoré :

-   Vous ne dépassent pas `AEDefaultNotifier`, par exemple, vous avez implémenté gestion de catégorie à partir de zéro.
-   Vous a substitué `prepareNotificationView:forContent:`, veillez à mapper au moins `onNotificationActioned` ou `onNotificationExited` pour un de vos U.I des contrôles.

> [AZURE.WARNING] Si `handleNotification:` lève une exception, le contenu est supprimée et `drop` est appelée, ceci est signalé dans les statistiques et les campagnes suivant peuvent maintenant être traitées.

#### <a name="include-notification-as-part-of-an-existing-view"></a>Inclure la notification dans le cadre d’une vue existante

Superpositions sont importants pour une intégration rapide mais peuvent parfois ne pas être pratiques ou peuvent avoir des effets secondaires indésirables.

Si vous n’êtes pas satisfait par le système de superposition de certains de vos affichages, vous pouvez le personnaliser pour ces vues.

Vous pouvez décider d’inclure notre disposition notification dans vos vues existantes. Pour ce faire, il existe deux styles de mise en œuvre :

1.  Ajouter l’affichage de notification à l’aide du Générateur d’interface

    -   Ouvrir le *Générateur d’Interface*
    -   Placez un 320 x 60 (ou si vous êtes sur un iPad 768 x 60) `UIView` où vous souhaitez que la notification s’affiche
    -   Définir la valeur de la balise de cet affichage à : **36822491**

2.  Ajouter par programme à l’affichage de la notification. Il suffit d’ajouter le code suivant lors de l’initialisation de l’affichage :

        UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
        notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
        [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG`macro se trouve dans `AEDefaultNotifier.h`.

> [AZURE.NOTE] L’utilitaire de notification par défaut détecte automatiquement que la présentation de la notification est incluse dans cette vue et n’ajoute pas une superposition pour lui.

### <a name="announcements-and-polls"></a>Annonces et sondages

#### <a name="layouts"></a>Mises en page

Vous pouvez modifier les fichiers `AEDefaultAnnouncementView.xib` et `AEDefaultPollView.xib` tant que vous gardez les valeurs des balises et les types des sous-vues existants.

#### <a name="categories"></a>Catégories

##### <a name="alternate-layouts"></a>Autres dispositions

Telles que les notifications, la catégorie de la campagne peut servir pour que vos annonces et les interrogations des formes différentes.

Pour créer une catégorie pour une annonce, vous devez étendre le **AEAnnouncementViewController** et l’enregistrer une fois le module de portée a été initialisé :

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] Chaque fois qu’un utilisateur clique sur une notification pour une annonce avec la catégorie « Mes\_catégorie », votre contrôleur d’affichage inscrits (dans ce cas `MyCustomAnnouncementViewController`) sera initialisé en appelant la méthode `initWithAnnouncement:` et la vue sera ajoutée à la fenêtre d’application actuelle.

Dans votre implémentation de la `AEAnnouncementViewController` vous devrez lire la propriété de classe `announcement` pour initialiser votre sous-vues. Prenons l’exemple ci-dessous, où deux étiquettes sont initialisées à l’aide de `title` et `body` propriétés de la `AEReachAnnouncement` classe :

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Si vous ne souhaitez pas charger vos affichages par vous-même, mais vous souhaitez réutiliser la mise en page par défaut annonce affichage uniquement, vous suffit d’en faire votre contrôleur d’affichage personnalisé étend la classe `AEDefaultAnnouncementViewController`. Dans ce cas, dupliquer le fichier Plume `AEDefaultAnnouncementView.xib` et le renommer peut être chargé par votre contrôleur de la vue personnalisée (pour un contrôleur nommé `CustomAnnouncementViewController`, vous devez appeler votre fichier Plume `CustomAnnouncementView.xib`).

Pour remplacer la catégorie par défaut des annonces, il enregistre simplement votre contrôleur d’affichage personnalisé pour la catégorie définie dans `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Sondages peuvent être personnalisés de la même manière :

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Cette fois, à la condition `MyCustomPollViewController` devez étendre `AEPollViewController`. Ou vous pouvez choisir d’étendre à partir du contrôleur par défaut : `AEDefaultPollViewController`.

> [AZURE.IMPORTANT] N’oubliez pas d’appeler `action` (`submitAnswers:` pour les contrôleurs de la vue personnalisée de sondage) ou `exit` méthode avant que le contrôleur de la vue est fermé. Sinon, les statistiques ne seront pas envoyées (c'est-à-dire aucun analytique sur la campagne) et autres campagnes important suivant ne seront pas avertis qu’après le redémarrage du processus de l’application.

##### <a name="implementation-example"></a>Exemple de mise en œuvre

Dans cette implémentation, l’affichage de l’annonce personnalisée est chargée à partir d’un fichier xib externe.

Comme pour la personnalisation de notification avancée, il est recommandé d’examiner le code source de la mise en oeuvre standard.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
