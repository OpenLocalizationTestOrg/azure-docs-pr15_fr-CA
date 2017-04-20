<properties
    pageTitle="Concentrateurs de Notification Azure sécurisés Push"
    description="Apprenez à envoyer des notifications de transmission sécurisée pour une application iOS dans Azure. Échantillons de code rédigés dans Objective-C et C#."
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>Concentrateurs de Notification Azure sécurisés Push

> [AZURE.SELECTOR]
- [Universel de Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [e/s](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>Vue d’ensemble

Prise en charge des notifications push dans Microsoft Azure vous permet d’accéder à une infrastructure push facile à utiliser, multi-plateformes, à grande échelle, ce qui simplifie considérablement l’implémentation des notifications de type Pousser pour les consommateurs et les entreprise des applications pour les plates-formes mobiles.

En raison des réglementations ou des contraintes de sécurité, une application peut parfois inclure quelque chose dans la notification ne peut pas être transmise par le biais de l’infrastructure de notification de transmission standard. Ce didacticiel explique comment réaliser la même expérience en envoyant des informations sensibles via une connexion sécurisée et authentifiée entre le périphérique client et le serveur principal chargé de l’application.

À un niveau élevé, le flux est la suivante :

1. Application back-end :
    - Magasins transport de données sécurisé dans la base de données back-end.
    - Renvoie l’ID de la notification au périphérique (aucune information sécurisée n’est envoyée).
2. L’application sur le périphérique, lors de la réception de la notification :
    - Le périphérique contacte back-end demandant le transport de données sécurisé.
    - L’application peut afficher la charge utile sous la forme d’une notification sur le périphérique.

Il est important de noter que dans le flux précédent (et, dans ce didacticiel), nous partons du principe que l’appareil stocke un jeton d’authentification dans le stockage local, une fois que l’utilisateur se connecte. Cela garantit une expérience complètement transparente, comme le périphérique peut récupérer de données sécurisé de la notification à l’aide de ce jeton. Si votre application ne stocke pas les jetons d’authentification sur le périphérique, ou si ces jetons peuvent être expirés, l’application de périphérique, lors de la réception de la notification doit afficher une notification générique, demander à l’utilisateur pour lancer l’application. Ensuite, l’application authentifie l’utilisateur et indique la charge utile de notification.

Ce didacticiel de Push de sécuriser montre comment envoyer une notification de transmission en toute sécurité. Le didacticiel s’appuie sur le didacticiel [d’Avertir les utilisateurs](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , afin que vous devez effectuer tout d’abord les étapes dans ce didacticiel.

> [AZURE.NOTE] Ce didacticiel suppose que vous avez créé et configuré votre concentrateur de notification, comme décrit dans [Mise en route avec les concentrateurs de Notification (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modifier le projet d’e/s

Maintenant que vous avez modifié votre back-end app pour envoyer simplement l' *id* de la notification, vous devez modifier votre application iOS pour gérer cette notification et de rappel de vos principaux pour récupérer le message de sécurité s’affiche.

Pour atteindre cet objectif, nous devons écrire la logique pour récupérer le contenu sécurisé à partir de l’application back-end.

1. Dans **AppDelegate.m**, assurez-vous que les registres d’application pour les notifications en mode silencieux, donc il traite l’id de notification envoyés par le serveur principal. Ajoutez l’option **UIRemoteNotificationTypeNewsstandContentAvailability** dans didFinishLaunchingWithOptions :

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. Votre **AppDelegate.m** d' ajouter une section de mise en œuvre en haut avec la déclaration suivante :

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

3. Puis ajoutez le code suivant, en remplaçant l’espace réservé dans la section implémentation `{back-end endpoint}` avec le point de terminaison de votre back-end obtenu précédemment :

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

4. Nous avons gérer la notification entrante et utilisez la méthode ci-dessus pour récupérer le contenu à afficher. Tout d’abord, nous devons activer votre application iOS pour s’exécuter en arrière-plan lors de la réception d’une notification de transmission. Dans **XCode**, sélectionnez votre projet d’application dans le volet gauche, puis cliquez sur cible de votre application principale dans la section **cibles** à partir du volet central.

5. Cliquez sur l’onglet **fonctionnalités** en haut de votre volet central, puis cochez la case **Notifications à distance** .

    ![][IOS1]


6. Dans **AppDelegate.m** , ajoutez la méthode suivante pour gérer les notifications de transmission :

        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);

            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];

        }

    Notez qu’il est préférable de traiter les cas de rejet ou de propriété d’en-tête d’authentification manquants en back-end. Le traitement spécifique de ces cas dépend essentiellement de votre expérience de l’utilisateur cible. Une option consiste à afficher une notification avec un message générique à l’utilisateur de s’authentifier à récupérer de la notification.

## <a name="run-the-application"></a>Exécution de l’Application

Pour exécuter l’application, effectuez les opérations suivantes :

1. Dans XCode, exécutez l’application sur un périphérique d’e/s physiques (envoi de notifications ne fonctionnent pas dans le simulateur).

2. Dans l’interface utilisateur d’application iOS, entrez un nom d’utilisateur et le mot de passe. Ce peuvent être n’importe quelle chaîne, mais ils doivent être de la même valeur.

3. Dans l’interface utilisateur d’application iOS, cliquez sur **se connecter**. Cliquez ensuite sur **Envoyer push**. Vous devriez voir la notification sécurisée s’affiche dans votre centre de notification.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
