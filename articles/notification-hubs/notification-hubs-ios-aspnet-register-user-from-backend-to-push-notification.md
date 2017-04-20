<properties
    pageTitle="Inscrire l’utilisateur actuel pour les notifications de transmission à l’aide des API de Web | Microsoft Azure"
    description="Découvrez comment demander l’enregistrement de notification de transmission dans une application iOS avec Azure Notification concentrateurs lorsque registeration est effectué par les API de Web ASP.NET."
    services="notification-hubs"
    documentationCenter="ios"
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

# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Inscrire l’utilisateur actuel pour les notifications de transmission à l’aide d’ASP.NET

> [AZURE.SELECTOR]
- [e/s](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)



##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous indique comment demander l’enregistrement de notification de transmission avec Azure Notification concentrateurs lorsque l’enregistrement est effectué par les API de Web ASP.NET. Cette rubrique étend le didacticiel de [notifier les utilisateurs avec des concentrateurs de Notification]. Vous devez déjà avoir effectué les étapes requises dans ce didacticiel pour créer le service mobile authentifié. Pour plus d’informations sur le scénario d’utilisateurs notify, consultez [notifier les utilisateurs avec des concentrateurs de Notification].

##<a name="update-your-app"></a>Mise à jour de votre application  

1. Dans votre MainStoryboard_iPhone.storyboard, ajoutez les composants suivants à partir de la bibliothèque d’objets :

    + **Étiquette**: « Pousser à utilisateur avec Notification concentrateurs »
    + **Étiquette**: « ID »
    + **Étiquette**: « Utilisateur »
    + **Champ de texte**: « Utilisateur »
    + **Étiquette**: « Password »
    + **Champ de texte**: « Password »
    + **Bouton**: « Connexion »

    À ce stade, votre table de montage séquentiel se présente comme suit :

    ![][0]

2. Dans l’éditeur de l’assistant, créer des débouchés pour tous les contrôles commutés et appeler les connecter les champs de texte avec le contrôleur de la vue (délégué) et créer une **Action** pour le bouton de **connexion** .

    ![][1]

    Votre BreakingNewsViewController.h fichier doit désormais contenir le code suivant :

        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;

        - (IBAction)login:(id)sender;

5. Créez une classe nommée **DeviceInfo**et copiez le code suivant dans la section de l’interface du fichier DeviceInfo.h :

        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;

6. Copiez le code suivant dans la section de mise en oeuvre du fichier DeviceInfo.m :

            @synthesize installationId = _installationId;

            - (id)init {
                if (!(self = [super init]))
                    return nil;

                NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
                _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
                if(!_installationId) {
                    CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
                    _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
                    CFRelease(newUUID);

                    //store the install ID so we don't generate a new one next time
                    [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
                    [defaults synchronize];
                }

                return self;
            }

            - (NSString*)getDeviceTokenInHex {
                const unsigned *tokenBytes = [[self deviceToken] bytes];
                NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                      ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                      ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                      ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                return hexToken;
            }

7. Dans PushToUserAppDelegate.h, ajoutez le singleton de propriété suivants :

        @property (strong, nonatomic) DeviceInfo* deviceInfo;

8. Dans la méthode **didFinishLaunchingWithOptions** dans PushToUserAppDelegate.m, ajoutez le code suivant :

        self.deviceInfo = [[DeviceInfo alloc] init];

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    La première ligne initialise le singleton **DeviceInfo** . La deuxième ligne commence l’enregistrement pour push notifications, qui est déjà présentent est que vous avez déjà effectué le didacticiel [Mise en route de concentrateurs de Notification] .

9. Dans PushToUserAppDelegate.m, implémentez la méthode **didRegisterForRemoteNotificationsWithDeviceToken** dans votre AppDelegate et ajoutez le code suivant :

        self.deviceInfo.deviceToken = deviceToken;

    Cette valeur définit le jeton de périphérique pour la demande.

    > [AZURE.NOTE] À ce stade, il ne doit pas être tout autre code dans cette méthode. Si vous disposez déjà d’un appel à la méthode **registerNativeWithDeviceToken** qui a été ajouté lorsque vous terminé le didacticiel [Mise en route de concentrateurs de Notification](/manage/services/notification-hubs/get-started-notification-hubs-ios/) , vous devez commenter les ou supprimer cet appel.

10. Dans le fichier PushToUserAppDelegate.m, ajoutez la méthode suivante du gestionnaire :

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

     Cette méthode affiche une alerte dans l’interface utilisateur lorsque votre application reçoit les notifications en cours d’exécution.

9. Ouvrez le fichier PushToUserViewController.m et retourner le clavier dans l’implémentation suivante :

        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }

9. Dans la méthode **viewDidLoad** dans le fichier PushToUserViewController.m, initialiser l’étiquette d’ID comme suit :

        DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
        Self.installationId.text = deviceInfo.installationId;

10. Dans l’interface de PushToUserViewController.m, ajoutez les propriétés suivantes :

        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;

11. Ensuite, ajoutez l’implémentation suivante :

            - (NSOperationQueue *)downloadQueue {
                if (!_downloadQueue) {
                    _downloadQueue = [[NSOperationQueue alloc] init];
                    _downloadQueue.name = @"Download Queue";
                    _downloadQueue.maxConcurrentOperationCount = 1;
                }
                return _downloadQueue;
            }

            // base64 encoding
            - (NSString*)base64forData:(NSData*)theData
            {
                const uint8_t* input = (const uint8_t*)[theData bytes];
                NSInteger length = [theData length];

                static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

                NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
                uint8_t* output = (uint8_t*)data.mutableBytes;

                NSInteger i;
                for (i=0; i < length; i += 3) {
                    NSInteger value = 0;
                    NSInteger j;
                    for (j = i; j < (i + 3); j++) {
                        value <<= 8;

                        if (j < length) {
                            value |= (0xFF & input[j]);
                        }
                    }

                    NSInteger theIndex = (i / 3) * 4;
                    output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
                    output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
                    output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
                    output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
                }

                return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
            }


12. Copiez le code suivant dans la méthode de gestionnaire de **connexion** créée par XCode :

            DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

            // build JSON
            NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

            // build auth string
            NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
            [request setHTTPMethod:@"POST"];
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
            [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

            // connect with POST
            [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
                // add UIAlert depending on response.
                if (error != nil) {
                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if ([httpResponse statusCode] == 200) {
                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    } else {
                        NSLog(@"status: %ld", (long)[httpResponse statusCode]);
                    }
                } else {
                    NSLog(@"error: %@", error);
                }
            }];

    Cette méthode obtient un ID d’installation et le canal pour les notifications de transmission et l’envoie, avec le type de périphérique, à la méthode API Web authentifiée qui crée un enregistrement dans les concentrateurs de Notification. Cette API Web a été définie dans la [notification des utilisateurs avec les concentrateurs de la Notification].

Maintenant que l’application cliente a été mis à jour, revenez à [avertir les utilisateurs avec des concentrateurs de Notification] et du service mobile pour envoyer des notifications à l’aide de concentrateurs de Notification de mise à jour.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Avertir les utilisateurs avec des concentrateurs de Notification]: /manage/services/notification-hubs/notify-users-aspnet

[Mise en route de concentrateurs de Notification]: /manage/services/notification-hubs/get-started-notification-hubs-ios
