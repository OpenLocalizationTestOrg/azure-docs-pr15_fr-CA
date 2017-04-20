<properties
    pageTitle="Notification concentrateurs localisé Breaking News didacticiel pour iOS"
    description="Apprenez à utiliser des concentrateurs de Notification de Bus de Service Azure pour envoyer des notifications de news rupture localisée (iOS)."
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
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>Concentrateurs de Notification permet d’envoyer des informations de dernière minute localisé aux périphériques d’e/s

> [AZURE.SELECTOR]
- [Magasin de Windows C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification)
- [e/s](notification-hubs-ios-xplat-localized-apns-push-notification)


##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous indique comment utiliser la fonctionnalité [modèles](notification-hubs-templates-cross-platform-push-messages.md) de concentrateurs de Notification Azure pour diffuser les notifications de news avec rupture qui ont été localisées par langue et par périphérique. Dans ce didacticiel, vous démarrez avec l’application iOS créée dans [Utilisation des concentrateurs de Notification pour envoyer des informations de dernière minute]. Lorsque vous avez terminé, vous ne pourrez pas enregistrer de catégories qui que vous intéressez, spécifiez un langage dans lequel vous souhaitez recevoir des notifications et de recevoir des notifications de transmission uniquement pour les catégories sélectionnées dans cette langue.


Il existe deux parties de ce scénario :

- application iOS permet aux périphériques afin de spécifier une langue et de s’abonner à la rupture de différentes catégories d’actualités ; clients

- back-end diffuse les notifications, à l’aide de la **balise** et le **modèle** fondements de concentrateurs de Notification Azure.



##<a name="prerequisites"></a>Conditions préalables

Vous devez déjà avoir effectué le didacticiel [Utilisation des concentrateurs de Notification pour envoyer des informations de dernière minute] et que le code n’est disponible, car ce didacticiel s’appuie directement sur ce code.

Visual Studio 2012 ou une version ultérieure est facultative.



##<a name="template-concepts"></a>Concepts du modèle

Dans [Utiliser des concentrateurs de Notification pour envoyer des informations de dernière minute] , vous avez créé une application qui permet de s’abonner à des notifications pour les catégories d’informations différentes **balises** .
De nombreuses applications, toutefois, ciblent plusieurs marchés et à localiser. Cela signifie que le contenu des notifications eux-mêmes doivent être localisés et remis à l’ensemble de périphériques correct.
Dans cette rubrique, nous apprendrons à utiliser la fonctionnalité de **modèle** de concentrateurs de Notification pour livrer des notifications de news de rupture localisée de facilement.

Remarque : un pour envoyer des notifications localisées consiste à créer plusieurs versions de chaque balise. Par exemple, pour prendre en charge le Mandarin, anglais et Français, il nous faudrait trois balises différentes pour dépêches : « world_en », « world_fr » et « world_ch ». Il faut envoyer une version localisée de l’actualité mondiale à chacune de ces balises. Dans cette rubrique, nous utilisons des modèles afin d’éviter la prolifération des balises et la nécessité de l’envoi de plusieurs messages.

À un niveau élevé, les modèles sont un moyen de spécifier comment un périphérique spécifique doit recevoir une notification. Le modèle spécifie le format de charge utile exact en faisant référence à des propriétés qui font partie du message envoyé par votre application back-end. Dans notre cas, nous envoie un message agnostique en termes de paramètres régionaux contenant toutes les langues :

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Ensuite, nous nous assurons que les périphériques enregistrent avec un modèle qui fait référence à la propriété appropriée. Par exemple, une application iOS qui veut inscrire les nouvelles Français enregistre les éléments suivants :

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Les modèles sont une fonctionnalité très puissante que vous pouvez apprendre dans notre article de [modèles](notification-hubs-templates-cross-platform-push-messages.md) .

##<a name="the-app-user-interface"></a>Interface utilisateur de l’application

Nous allons maintenant modifier l’application Breaking News que vous avez créé dans la rubrique [Utilisation des concentrateurs de Notification pour envoyer des informations de dernière minute] pour envoyer des actualités localisées à l’aide de modèles.


Dans votre MainStoryboard_iPhone.storyboard, ajoutez un contrôle segmenté avec trois langages qui nous prend en charge : Mandarin, anglais et Français.

![][13]

Veillez à ajouter un type de IBOutlet dans votre ViewController.h comme indiqué ci-dessous :

![][14]

##<a name="building-the-ios-app"></a>Concevoir l’application iOS


1. Dans votre Notification.h ajoutez la méthode *retrieveLocale* et modifier la banque et vous abonner des méthodes comme indiqué ci-dessous :

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

        - (NSSet*) retrieveCategories;

        - (int) retrieveLocale;

    Dans votre Notification.m, modifiez la méthode *storeCategoriesAndSubscribe* , en ajoutant le paramètre paramètres régionaux et son stockage dans les paramètres par défaut de l’utilisateur :

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];

            [self subscribeWithLocale: locale categories:categories completion:completion];
        }

    Ensuite, modifiez la méthode de *s’abonner* pour inclure les paramètres régionaux :

        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }

            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }

    Notez comment nous utilisons maintenant la méthode *registerTemplateWithDeviceToken*, au lieu de *registerNativeWithDeviceToken*. Lors de l’enregistrement d’un modèle que nous avons fournir le modèle json et également un nom pour le modèle (comme notre application souhaiterez enregistrer des modèles différents). Veillez à enregistrer vos catégories en tant que balises, comme nous voulons vous assurer de recevoir les notifciations pour les news.

    Ajoutez une méthode pour extraire les paramètres régionaux à partir des paramètres utilisateur par défaut :

        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            int locale = [defaults integerForKey:@"BreakingNewsLocale"];

            return locale < 0?0:locale;
        }

2. Maintenant que nous avons modifié notre classe de Notifications, nous devons vérifier que notre ViewController rend l’utilisation de la UISegmentControl de nouveau. Ajoutez la ligne suivante dans la méthode *viewDidLoad* pour vous assurer qu’afficher les paramètres régionaux actuellement sélectionné :

        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

    Puis, dans la méthode *subscribe* , modifier votre appel à la *storeCategoriesAndSubscribe* à ce qui suit :

        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

3. Enfin, vous devez mettre à jour de la méthode *didRegisterForRemoteNotificationsWithDeviceToken* dans votre AppDelegate.m, afin que vous puissiez actualiser correctement de votre inscription au démarrage de votre application. Modifiez votre appel à la méthode de *vous abonner* , des notifications par le texte suivant :

        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="optional-send-localized-template-notifications-from-net-console-app"></a>(facultatif) Permet d’envoyer des notifications de modèle localisé à partir de l’application de console .NET.

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]



##<a name="optional-send-localized-template-notifications-from-the-device"></a>(facultatif) Envoyer des notifications de modèle localisé à partir du périphérique

Si vous n’ont accès à Visual Studio, ou souhaitez juste tester l’envoi de notifications modèle localisé directement à partir de l’application sur le périphérique.  Vous pouvez simple ajouter les paramètres de modèle localisé à le `SendNotificationRESTAPI` méthode que vous avez définie dans le didacticiel précédent.

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
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

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




## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des modèles, voir :

- [Avertir les utilisateurs avec des concentrateurs de Notification : ASP.NET]
- [Avertir les utilisateurs avec des concentrateurs de Notification : Services mobiles]






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Concentrateurs de Notification permet d’envoyer des informations de dernière minute]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Avertir les utilisateurs avec des concentrateurs de Notification : ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Avertir les utilisateurs avec des concentrateurs de Notification : Services mobiles]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
