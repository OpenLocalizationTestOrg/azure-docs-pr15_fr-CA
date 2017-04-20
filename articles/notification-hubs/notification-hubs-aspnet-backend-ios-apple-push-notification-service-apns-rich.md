<properties
    pageTitle="Notification d’Azure concentrateurs enrichi Push"
    description="Apprenez à envoyer des notifications de type Pousser riche pour une application iOS dans Azure. Échantillons de code rédigés dans Objective-C et C#."
    documentationCenter="ios"
    services="notification-hubs"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-rich-push"></a>Notification d’Azure concentrateurs enrichi Push


##<a name="overview"></a>Vue d’ensemble

Pour engager les utilisateurs avec les contenus riches instantanées, une application peut vouloir distribuer au-delà de texte brut. Ces notifications promeuvent les interactions de l’utilisateur et de présenter le contenu comme URL, sons, images/bons de réduction et bien plus encore. Ce didacticiel s’appuie sur la rubrique [d’Avertir les utilisateurs](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) et montre comment envoyer des notifications de type Pousser qui incorporent des charges utiles (par exemple, image).


Ce didacticiel est compatible avec les e/s, 7 et 8.

  ![][IOS1]

À un niveau élevé :

1. Le back-end de l’application :
    - Stocke la charge utile enrichie (dans ce cas, l’image) dans le stockage de base de données/local back-end
    - Envoie l’ID de cette notification enrichie au périphérique
2. Application sur le périphérique :
    - Le serveur principal qui demande la charge enrichie avec l’ID qu’il reçoit des contacts
    - Envoie les notifications aux utilisateurs sur le périphérique lors de la récupération des données est terminée et indique la charge utile immédiatement lorsque les utilisateurs cliquez pour en savoir plus


## <a name="webapi-project"></a>WebAPI projet

1. Dans Visual Studio, ouvrez le projet **AppBackend** que vous avez créé dans le didacticiel [d’Avertir les utilisateurs](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Obtenir une image que vous souhaitez avertir les utilisateurs en et placez-la dans un dossier **img** dans votre répertoire de projet.
3. Cliquez sur **Afficher tous les fichiers** dans l’Explorateur de solutions, puis cliquez avec le bouton droit à **Inclure dans le projet**.
4. Avec l’image sélectionnée, modifier son Action de génération dans la fenêtre Propriétés de la **Ressource incorporée**.

    ![][IOS2]

5. Dans **Notifications.cs**, ajoutez le code suivant à l’aide d’instruction :

        using System.Reflection;

6. Mettre à jour l’ensemble de la classe **Notifications** par le code suivant. Veillez à remplacer les espaces réservés avec vos informations d’identification du concentrateur de notification et le nom du fichier image.

        public class Notification {
            public int Id { get; set; }
            // Initial notification message to display to users
            public string Message { get; set; }
            // Type of rich payload (developer-defined)
            public string RichType { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
            }

            public Notification CreateNotification(string message, string richType, string payload) {
                var notification = new Notification() {
                    Id = notifications.Count,
                    Message = message,
                    RichType = richType,
                    Payload = payload,
                    Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Stream ReadImage(int id) {
                var assembly = Assembly.GetExecutingAssembly();
                // Placeholder: image file name (for example, logo.png).
                return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
            }
        }

    >[AZURE.NOTE]  (facultatif) Pour plus d’informations sur la façon d’ajouter et d’obtenir des ressources de projet, voir [Comment faire pour incorporer et accéder aux ressources à l’aide de Visual C#](http://support.microsoft.com/kb/319292) .

7. Dans **NotificationsController.cs**, redéfinissez **NotificationsController** avec les extraits de code suivants. Cela envoie un code de notification initiale de riches en mode silencieux au périphérique et permet l’extraction du côté client de l’image :

        // Return http response with image binary
        public HttpResponseMessage Get(int id) {
            var stream = Notifications.Instance.ReadImage(id);

            var result = new HttpResponseMessage(HttpStatusCode.OK);
            result.Content = new StreamContent(stream);
            // Switch in your image extension for "png"
            result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

            return result;
        }

        // Create rich notification and send initial silent notification (containing id) to client
        public async Task<HttpResponseMessage> Post() {
            // Replace the placeholder with image file name
            var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

            // Silent notification with content available
            var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

8. Maintenant nous sera redéployer cette application sur un site Web d’Azure afin du pour rendre accessible à partir de tous les périphériques. Avec le bouton droit sur le projet **AppBackend** et sélectionnez **Publier**.

9. Sélectionnez site Web Azure comme cible à publier. Connectez-vous avec votre compte Azure et sélectionnez un site Web existant ou nouveau et prenez note de la propriété de **l’URL de destination** dans l’onglet **connexion** . Nous ferons référence à cette URL comme votre *point de terminaison de serveur principal* plus loin dans ce didacticiel. Cliquez sur **Publier**.

## <a name="modify-the-ios-project"></a>Modifier le projet d’e/s

Maintenant que vous avez modifié votre back-end de l’application pour envoyer simplement l' *id* de la notification, vous devez modifier votre application iOS pour gérer ce code et récupérer le message complet à partir de votre serveur principal.

1. Ouvrez votre projet iOS et activer les notifications à distance en accédant à votre cible principale application dans la section **cibles** .

2. Cliquez sur **fonctions**, activer **Les Modes de fond**et cochez la case **Notifications à distance** .

    ![][IOS3]

3. Accédez à **Main.storyboard**et vérifiez que vous disposez d’un contrôleur de vue (dénommées accueil View Controller dans ce didacticiel) à partir du didacticiel de [Notifier l’utilisateur](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .

4. Ajouter un **Contrôleur de Navigation** à votre table de montage séquentiel et faire glisser le contrôle à vue contrôleur Home pour faciliter l' **affichage de la racine** de navigation. Assurez-vous que le **Contrôleur d’affichage initiale est** dans l’inspecteur des attributs est sélectionné pour le contrôleur de Navigation uniquement.

5. Ajouter un **Contrôleur de la vue** pour la table de montage séquentiel et ajouter une **Vue de l’Image**. Il s’agit de la page que les utilisateurs verront une fois qu’ils le souhaitent en savoir plus en cliquant sur la notifiication. Votre table de montage séquentiel doit se présenter comme suit :

    ![][IOS4]

6. Cliquez sur le **Contrôleur de la vue personnelle** dans la table de montage séquentiel et vérifiez qu’il a **homeViewController** en tant que sa **Classe personnalisée** et **l’ID de la table de montage séquentiel** dans l’inspecteur de l’identité.

7. Procédez de même pour le contrôleur de la vue Image en tant que **imageViewController**.

8. Ensuite, créez une nouvelle classe de contrôleur d’affichage intitulée **imageViewController** pour gérer l’interface utilisateur que vous venez de créer.

9. Dans **imageViewController.h**, ajoutez le code suivant les déclarations d’interface du contrôleur. N’oubliez pas de contrôle, faites glisser l’affichage d’images de table de montage séquentiel à ces propriétés de lier les deux :

        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;

10. Dans **imageViewController.m**, ajoutez ce qui suit à la fin de **viewDidload**:

        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];

11. Dans **AppDelegate.m**, importer le contrôleur d’image que vous avez créé :

        #import "imageViewController.h"

12. Ajouter une section de l’interface avec la déclaration suivante :

        @interface AppDelegate ()

        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;

        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;

        @end

13. Dans **AppDelegate**, assurez-vous que votre application s’inscrit aux notifications en mode silencieux dans **application : didFinishLaunchingWithOptions**:

        // Software version
        self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

        // Register for remote notifications for iOS8 and previous versions
        if (self.iOS8) {
            NSLog(@"This device is running with iOS8.");

            // Action
            UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
            richPushAction.identifier = @"richPushMore";
            richPushAction.activationMode = UIUserNotificationActivationModeForeground;
            richPushAction.authenticationRequired = NO;
            richPushAction.title = @"More";

            // Notification category
            UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
            richPushCategory.identifier = @"richPush";
            [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

            // Notification categories
            NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];


            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                    UIUserNotificationTypeAlert |
                                                    UIUserNotificationTypeBadge
                                                                                     categories:richPushCategories];

            [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
            [[UIApplication sharedApplication] registerForRemoteNotifications];

        }
        else {
            // Previous iOS versions
            NSLog(@"This device is running with iOS7 or earlier versions.");

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
        }

        return YES;

14. Modifications d’interface utilisateur utilisateuret les substituer dans l’implémentation suivante de **l’application : didRegisterForRemoteNotificationsWithDeviceToken** de la table de montage séquentiel de prendre en compte :

        // Access navigation controller which is at the root of window
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        // Get home view controller from stack on navigation controller
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        hvc.deviceToken = deviceToken;

15. Ensuite, ajoutez les méthodes suivantes pour **AppDelegate.m** pour extraire l’image de votre point de terminaison et envoyer une notification locale lors de l’extraction est terminée. Veillez à remplacer l’espace réservé `{backend endpoint}` avec votre point de terminaison de serveur principal :

        NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

        // Helper: retrieve notification content from backend with rich notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
            UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
            homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
            NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
            // Check if authenticated
            if (!authenticationHeader) return;

            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];

            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200) {
                    // From NSData to UIImage
                    self.imagePayload = [UIImage imageWithData:data];

                    completion(nil);
                }
                else {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(error);
                    else {
                        completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        // Handle silent push notifications when id is sent from backend
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
            self.userInfo = userInfo;
            int richId = [[self.userInfo objectForKey:@"richId"] intValue];
            NSString* richType = [self.userInfo objectForKey:@"richType"];

            // Retrieve image data
            if ([richType isEqualToString:@"img"]) {  
                [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                    if (!error){
                        // Send local notification
                        UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                        // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                        localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                        localNotification.userInfo = self.userInfo;
                        localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                        localNotification.timeZone = [NSTimeZone defaultTimeZone];

                        // iOS8 categories
                        if (self.iOS8) {
                            localNotification.category = @"richPush";
                        }

                        [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                        handler(UIBackgroundFetchResultNewData);
                    }
                    else{
                        handler(UIBackgroundFetchResultFailed);
                    }
                }];
            }
            // Add "else if" here to handle more types of rich content such as url, sound files, etc.
        }

16. Gérez la notification locale ci-dessus en ouvrant le contrôleur d’affichage image dans **AppDelegate.m** avec les méthodes suivantes :

        // Helper: redirect users to image view controller
        - (void)redirectToImageViewWithImage: (UIImage *)img {
            UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
            UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                     bundle: nil];
            imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
            // Pass data/image to image view controller
            imgViewController.imagePayload = img;

            // Redirect
            [navigationController pushViewController:imgViewController animated:YES];
        }

        // Handle local notification sent above in didReceiveRemoteNotification
        - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
            if (application.applicationState == UIApplicationStateActive) {
                // Show in-app alert with an extra "more" button
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];

                [alert show];
            }
            // App becomes active from user's tap on notification
            else {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
        }

        // Handle buttons in in-app alerts and redirect with data/image
        - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
            // Handle "more" button
            if (buttonIndex == 1)
            {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            // Add "else if" here to handle more buttons
        }

        // Handle notification setting actions in iOS8
        - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
            // Handle richPush related buttons
            if ([identifier isEqualToString:@"richPushMore"]) {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            completionHandler();
        }

## <a name="run-the-application"></a>Exécution de l’Application

1. Dans XCode, exécutez l’application sur un périphérique d’e/s physiques (envoi de notifications ne fonctionnent pas dans le simulateur).

2. Dans l’interface utilisateur d’application iOS, entrez un nom d’utilisateur et le mot de passe de la même valeur pour l’authentification, puis cliquez sur **Se connecter**.

3. Cliquez sur **envoyer envoyé** et vous devriez voir une alerte dans l’application. Si vous cliquez sur **plus**, vous serez amené à l’image que vous avez choisi d’inclure dans votre back-end de l’application.

4. Vous pouvez également cliquer sur **Envoyer push** et appuyez immédiatement sur le bouton Accueil de votre périphérique. Dans quelques instants, vous recevrez une notification de transmission. Si vous cliquez dessus ou cliquez sur plus, vous serez amené à votre application et le contenu de l’image complète.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
