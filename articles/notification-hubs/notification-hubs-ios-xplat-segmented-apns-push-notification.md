<properties
    pageTitle="Concentrateurs de notification rupture News didacticiel - iOS"
    description="Apprenez à utiliser des concentrateurs de Notification de Bus de Service Azure pour envoyer des notifications de news de rupture aux périphériques d’e/s."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Concentrateurs de Notification permet d’envoyer des informations de dernière minute

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous indique comment utiliser des concentrateurs de Notification Azure pour diffuser des notifications de news de rupture pour une application iOS. Lorsque vous avez terminé, vous pourrez inscrire pour casser des catégories d’informations que qui ne vous intéressent et recevoir les notifications de transmission uniquement pour ces catégories. Ce scénario est un modèle courant pour nombreuses applications où les notifications doivent être envoyées à des groupes d’utilisateurs que vous ont déclaré précédemment d’intérêt, par exemple, le lecteur RSS, applications pour les passionnés de musique, etc..

Scénarios de diffusion sont activés en incluant une ou plusieurs _balises_ lors de la création d’un enregistrement dans le concentrateur de notification. Lorsque les notifications sont envoyées à une balise, tous les périphériques qui ont inscrit pour la balise recevra la notification. Étant donné que les balises sont simplement des chaînes, ils n’ont pas à être mis en service à l’avance. Pour plus d’informations sur les balises, consultez [routage des concentrateurs de Notification et les Expressions de la balise](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Conditions préalables

Cette rubrique s’appuie sur l’application que vous avez créé dans la [mise en route de concentrateurs de Notification][get-started]. Avant de commencer ce didacticiel, vous devez déjà avoir effectué [mise en route de concentrateurs de Notification][get-started].

##<a name="add-category-selection-to-the-app"></a>Ajouter la sélection de la catégorie à l’application

La première étape consiste à ajouter des éléments d’interface utilisateur à votre table de montage séquentiel existante qui permettent à l’utilisateur de sélectionner les catégories à inscrire. Les catégories sélectionnées par un utilisateur sont stockées sur le périphérique. Lorsque l’application démarre, un enregistrement de périphérique est créé dans votre centre de notification avec les catégories sélectionnées sous forme de balises.

1. Dans votre MainStoryboard_iPhone.storyboard ajoutez les composants suivants à partir de la bibliothèque d’objets :
    + Une étiquette avec le texte « Flash Info »
    + Des étiquettes avec des textes de catégorie « Monde », « Politique », « Business », « Technologie », « Sciences », « Sports »,
    + Six commutateurs, un par catégorie, définissez chaque commutateur **état** **désactivé** par défaut.
    + Un bouton intitulé « S’abonner »

    Votre table de montage séquentiel doit se présenter comme suit :

    ![][3]

2. Dans l’éditeur de l’assistant, créer des points de vente pour tous les commutateurs et les appeler « WorldSwitch », « PoliticsSwitch », « BusinessSwitch », « TechnologySwitch », « ScienceSwitch », « SportsSwitch »


3. Créer une Action pour le bouton appelé « s’abonner ». Votre ViewController.h doit contenir les éléments suivants :

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4. Créer une nouvelle **Classe de cacao Touch** `Notifications`. Copiez le code suivant dans la section de l’interface du fichier Notifications.h :

        @property NSData* deviceToken;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;

        - (NSSet*)retrieveCategories;

        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. Ajoutez la directive d’importation suivantes à Notifications.m :

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Copiez le code suivant dans la section de mise en oeuvre du fichier Notifications.m.

        SBNotificationHub* hub;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

            hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                        notificationHubPath:hubName];

            return self;
        }

        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

            [self subscribeWithCategories:categories completion:completion];
        }


        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }


        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
        {
           //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

            NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
                jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
        }



    Cette classe utilise le stockage local pour stocker et récupérer les catégories de news qui recevra de ce périphérique. Également, il contient une méthode pour vous inscrire à ces catégories à l’aide d’un enregistrement de [modèle](notification-hubs-templates-cross-platform-push-messages.md) .

7. Dans le fichier AppDelegate.h, ajoutez une instruction import pour Notifications.h et une propriété d’une instance de la classe de Notifications :

        #import "Notifications.h"

        @property (nonatomic) Notifications* notifications;
    

8. Dans la méthode **didFinishLaunchingWithOptions** dans AppDelegate.m, ajoutez le code pour initialiser l’instance de notifications au début de la méthode.  
 
    `HUBNAME`et `HUBLISTENACCESS` (défini dans hubinfo.h) doit déjà être le `<hub name>` et `<connection string with listen access>` des espaces réservés est remplacé par votre nom de concentrateur de notification et de la chaîne de connexion pour *DefaultListenSharedAccessSignature* que vous avez obtenu précédemment

        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];

    > [AZURE.NOTE] Étant donné que les informations d’identification qui sont distribuées avec une application client ne sont pas généralement sécurisées, vous devez distribuer la clé pour l’accès d’écoute avec votre application cliente. Écouter access Active votre application à s’inscrire pour les notifications, mais les enregistrements existants ne peuvent pas être modifiée et notifications ne peuvent pas être envoyées. La clé d’accès complet est utilisée dans un service back-end sécurisé pour l’envoi de notifications et de modifier des enregistrements existants.


9. Dans la méthode **didRegisterForRemoteNotificationsWithDeviceToken** dans AppDelegate.m, remplacez le code dans la méthode par le code suivant pour passer le jeton de périphérique à la classe de notifications. La classe de notifications effectue l’enregistrement pour les notifications avec les catégories. Si l’utilisateur modifie la sélection de la catégorie, nous appelons la `subscribeWithCategories` méthode en réponse au bouton **s’abonner** pour les mettre à jour.

    > [AZURE.NOTE] Parce qu’il est peut probable que le jeton de périphérique affecté par Apple Push Notification Service (APNS) à tout moment, vous devez vous inscrire pour les notifications fréquemment afin d’éviter les échecs de notification. Cet exemple s’inscrit pour la notification chaque fois que l’application démarre. Pour les applications qui sont exécutées fréquemment, plus d’une fois par jour, vous pouvez de probablement ignorer l’enregistrement pour préserver la bande passante si l’inscription précédente remonte à moins d’un jour.

        self.notifications.deviceToken = deviceToken;

        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.

        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];


    Notez qu’à ce stade il ne convient aucun autre code dans la méthode **didRegisterForRemoteNotificationsWithDeviceToken** .

10. Les méthodes suivantes doivent déjà être présents dans AppDelegate.m à partir de la fin de la [mise en route de concentrateurs de Notification] [ get-started] didacticiel.  Si ce n’est pas le cas, ajoutez-les.

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

    Cette méthode gère les notifications reçues lors de l’application est exécutée en affichant une simple **UIAlert**.

11. Dans ViewController.m, ajoutez une instruction import pour AppDelegate.h et copiez le code suivant dans la méthode XCode généré **s’abonner** . Ce code met à jour l’enregistrement de notification pour utiliser les nouvelles balises de catégorie, que l’utilisateur a choisi dans l’interface utilisateur.

        ```
        #import "Notifications.h"
        ```

        NSMutableArray* categories = [[NSMutableArray alloc] init];

        if (self.WorldSwitch.isOn) [categories addObject:@"World"];
        if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
        if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
        if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
        if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
        if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
            if (!error) {
                [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

    Cette méthode crée un **NSMutableArray** des catégories et utilise la classe de **Notifications** pour stocker la liste dans le stockage local et enregistre les balises correspondantes avec votre concentrateur de notification. Lorsque les catégories sont modifiées, l’enregistrement est recréé avec les nouvelles catégories.

12. Dans ViewController.m, ajoutez le code suivant dans la méthode **viewDidLoad** pour définir l’interface utilisateur basée sur les catégories précédemment enregistrés.


        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



L’application peut désormais stocker un ensemble de catégories dans le stockage local du périphérique utilisé pour enregistrer avec le concentrateur de notification à chaque démarrage de l’application.  L’utilisateur peut modifier la sélection de catégories en cours d’exécution et cliquez sur la méthode **d’abonnement** pour mettre à jour l’inscription pour le périphérique. Ensuite, vous mettra à jour l’application pour envoyer les notifications de nouvelles de dernière minute directement dans l’application elle-même.


##<a name="optional-sending-tagged-notifications"></a>(facultatif) Envoi des notifications

Si vous n’avez pas accès à Visual Studio, vous pouvez passer à la section suivante et envoyer des notifications à partir de l’application elle-même. Vous pouvez également envoyer la notification de modèle approprié à partir du [Portail classique d’Azure] à l’aide de l’onglet déboguer votre concentrateur de notification. 

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]


##<a name="optional-send-notifications-from-the-device"></a>(facultatif) Envoyer des notifications à partir du périphérique

Notifications seraient normalement envoyées par un service back-end, mais vous pouvez envoyer des notifications de news de rupture directement à partir de l’application. Pour cela, nous mettrons à jour la `SendNotificationRESTAPI` méthode que nous avons défini dans la [mise en route de concentrateurs de Notification] [ get-started] didacticiel.


1. Mise à jour de ViewController.m les `SendNotificationRESTAPI` méthode comme suit afin qu’elle accepte un paramètre pour la balise category et envoie la notification du bon [modèle](notification-hubs-templates-cross-platform-push-messages.md) .

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                    categoryTag, self.notificationMessage.text];

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }



2. ViewController.m mettre à jour l’action **d’Envoyer une Notification** comme indiqué dans le code qui suit. Afin qu’il envoie les notifications à l’aide de chaque balise individuellement et envoyer à plusieurs plates-formes.



        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }



3. Régénérez votre projet et assurez-vous que vous n’avez aucune erreur de génération.


##<a name="run-the-app-and-generate-notifications"></a>Exécutez l’application et générer des notifications

1. Appuyez sur le bouton Exécuter pour générer le projet et démarrer l’application. Sélectionnez des options de news de rupture s’abonner à et appuyez sur le bouton **s’abonner** . Vous devez voir une boîte de dialogue indiquant que les notifications sont abonnées.

    ![][1]

    Lorsque vous cliquez sur **s’abonner**, l’application convertit les catégories sélectionnées dans des balises et demande une nouvelle inscription de périphérique pour les balises sélectionnées à partir du concentrateur de notification.

2. Entrez un message pour être envoyées sous la forme d’informations de dernière minute, puis appuyez sur le bouton **Envoyer la Notification** . Vous pouvez également exécuter l’application de console .NET pour générer des notifications.

    ![][2]


3. Chaque périphérique d’abonné aux dernières actualités recevra les notifications de news avec rupture que vous venez d’envoyer.



## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons appris à diffuser des informations de dernière minute par catégorie. Pensez à la fin d’un des didacticiels suivants qui mettent en évidence d’autres scénarios avancés de concentrateurs de Notification :

+ **[Utiliser des concentrateurs de Notification pour diffuser des informations de dernière minute localisée]**

    Apprenez à développer l’application d’actualités rupture pour activer l’envoi de notifications localisée.





<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Utiliser des concentrateurs de Notification pour diffuser des informations de dernière minute localisée]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Azure Portal classique]: https://manage.windowsazure.com
