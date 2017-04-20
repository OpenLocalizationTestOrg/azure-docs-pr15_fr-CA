<properties
    pageTitle="Envoi de notifications de type Pousser aux e/s avec des concentrateurs de Notification Azure | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type pousser à une application iOS."
    services="notification-hubs"
    documentationCenter="ios"
    keywords="Pousser la notification, les notifications de type Pousser, e/s des notifications de transmission"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-ios-with-azure-notification-hubs"></a>Envoi de notifications de type Pousser aux e/s avec des concentrateurs de Notification Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

> [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

Ce didacticiel vous montre comment utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type pousser à une application iOS. Vous allez créer une application vide iOS qui reçoit des notifications de type Pousser en utilisant le [service de Notification de transmission Apple (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). 

Lorsque vous avez terminé, vous serez en mesure d’utiliser votre concentrateur de notification à la diffusion des notifications de type pousser à tous les périphériques de l’exécution de votre application.

## <a name="before-you-begin"></a>Avant de commencer

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Vous trouverez le code complet pour ce didacticiel [sur GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ [Services mobiles iOS SDK version 1.2.4]
+ Version la plus récente de [Xcode]
+ Une e/s 8 (ou version ultérieure)-périphérique
+ Membre du [Programme de développeur d’Apple](https://developer.apple.com/programs/) .

   > [AZURE.NOTE] En raison de la configuration requise pour les notifications de transmission, vous devez déployer et tester les notifications de type Pousser sur un périphérique d’e/s physiques (iPhone ou iPad) au lieu de l’iOS du simulateur.

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels de concentrateurs de Notification pour les applications d’e/s.

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##<a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurer votre concentrateur de Notification pour les notifications de transmission des e/s

Cette section vous guide à travers la création d’un nouveau concentrateur de notification et de configuration de l’authentification avec APNS en utilisant le certificat d’émission **.p12** que vous avez créé. Si vous souhaitez utiliser un concentrateur de notification que vous avez déjà créé, vous pouvez passer à l’étape 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li>
<p>Cliquez sur le bouton <b>De Notification Services</b> dans la lame de <b>paramètres</b> , puis sélectionnez <b>Apple (APNS)</b>. Cliquez sur <b>Télécharger un certificat</b> et sélectionnez le fichier <b>.p12</b> que vous avez exportés plus haut. Assurez-vous que vous spécifiez également le mot de passe.</p>
<p>Veillez à sélectionner le mode <b>Sandbox</b> dans la mesure où il s’agit de développement. Utiliser uniquement la <b>Production</b> si vous souhaitez envoyer des notifications de type Pousser aux utilisateurs qui ont acheté votre application à partir du magasin.</p>
</li>
</ol>
&emsp;&emsp;![Configurer APNS dans Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![Configurer la certification de APNS dans Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)



Votre concentrateur de notification est maintenant configuré pour fonctionner avec APNS, et que les chaînes de connexion pour enregistrer votre application et d’envoyer des notifications de type Pousser.

##<a name="connect-your-ios-app-to-notification-hubs"></a>Connectez votre application iOS à des concentrateurs de Notification

1. Dans Xcode, créez un nouveau projet d’e/s et sélectionnez le modèle **d’Application vue unique** .

    ![Xcode - Application de vue unique][8]

2. Lorsque vous définissez les options pour votre nouveau projet, assurez-vous d’utiliser le même **Nom** et l' **Identificateur d’organisation** que vous avez utilisé lorsque vous définissez précédemment l’ID de l’offre sur le portail des développeurs d’Apple.

    ![Xcode - options du projet][11]

3. Sous **cibles**, cliquez sur le nom de votre projet, cliquez sur l’onglet **Paramètres de génération** et développez **l’Identité de signature de Code**et puis sous **débogage**, définissez votre identité de signature de code. Activer/désactiver les **niveaux** de **base** pour **toutes les**et définissez le **Profil de mise en service** sur le profil de configuration que vous avez créé précédemment.

    Si vous ne voyez pas le nouveau profil de configuration que vous avez créé dans Xcode, essayez d’actualiser les profils pour votre identité de signature. Cliquez sur **Xcode** sur la barre de menus, cliquez sur **Préférences**, cliquez sur l’onglet **compte** , cliquez sur le bouton **Afficher les détails** , cliquez sur votre identité de signature et puis cliquez sur le bouton Actualiser dans le coin inférieur droit.

    ![Xcode - profil de mise en service][9]

4. Téléchargez les [Services mobiles iOS SDK version 1.2.4] et décompressez le fichier. Dans Xcode, droit sur votre projet et cliquez sur l’option **Ajouter des fichiers pour** ajouter le dossier **WindowsAzureMessaging.framework** à votre projet Xcode. Sélectionnez **copier des éléments, si nécessaire**, puis cliquez sur **Ajouter**.

    >[AZURE.NOTE] Les concentrateurs de notification SDK ne pas prend en charge bitcode sur Xcode 7.  Vous devez définir **Bitcode d’activer** à **No** dans les **Options de génération** de votre projet.

    ![Décompressez Azure SDK][10]

5. Ajouter un nouveau fichier d’en-tête à votre projet nommé `HubInfo.h`. Ce fichier contiendra l’une des constantes pour votre concentrateur de notification.  Ajoutez les définitions suivantes et remplacer les espaces réservés de chaîne littérale avec votre *nom de concentrateur* et de l' *DefaultListenSharedAccessSignature* que vous avez notée précédemment.

        #ifndef HubInfo_h
        #define HubInfo_h
        
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
        
        #endif /* HubInfo_h */

6. Ouvrir votre `AppDelegate.h` fichier ajouter les directives d’importation suivantes :

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
        
7. Dans votre `AppDelegate.m file`, ajoutez le code suivant dans le `didFinishLaunchingWithOptions` méthode en fonction de votre version d’e/s. Ce code inscrit votre descripteur de périphérique avec APNs :

    Pour les e/s 8 :

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    Pour les e/s des versions antérieures à 8 :

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


8. Dans le même fichier, ajoutez les méthodes suivantes. Ce code connecte le concentrateur de notification en utilisant les informations de connexion que vous avez spécifié dans HubInfo.h. Il donne alors le jeton de périphérique pour le concentrateur de notification afin que le concentrateur de notification peut envoyer des notifications :

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }


9. Dans le même fichier, ajoutez la méthode suivante pour afficher un **UIAlert** si cette notification est reçue pendant que l’application est active :


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

10. Générez et exécutez l’application sur votre périphérique pour vérifier qu’il n’y a aucune défaillance.

## <a name="send-test-push-notifications"></a>Envoyer des notifications de type Pousser de test


Vous pouvez tester la réception de notifications dans votre application en envoyant des notifications de type Pousser le [Portail Azure] via la section **dépannage** de la lame de concentrateur (utilisez l’option *Tester Envoyer* ).

![Azure Portal - envoi de Test][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="optional-send-push-notifications-from-the-app"></a>(Facultatif) Envoyer des notifications de type pousser à partir de l’application

>[AZURE.IMPORTANT] Cet exemple de l’envoi de notifications à partir de l’application cliente est fourni uniquement à des fins pédagogiques. Dans la mesure où cette opération nécessite le `DefaultFullSharedAccessSignature` pour être présent dans l’application cliente, il expose votre concentrateur de notification pour le risque qu’un utilisateur peut accéder pour envoyer des notifications non autorisées à vos clients.

Si vous souhaitez envoyer des notifications de type pousser à partir d’une application, cette section fournit un exemple de cette procédure à l’aide de l’interface REST.

1. Dans Xcode, ouvrez `Main.storyboard` et ajoutez les composants suivants de l’interface utilisateur à partir de la bibliothèque d’objets pour permettre à l’utilisateur d’envoyer des notifications de type Pousser dans l’application :

    - Une étiquette avec le texte d’étiquette. Elle servira à signaler des erreurs lors de l’envoi de notifications. La propriété de **lignes** doit être définie sur **0** afin qu’il sera automatiquement taille limitée vers la droite et les marges de gauche et le haut de la vue.
    - Un champ de texte contenant un **espace réservé** au **Message de Notification permet d’entrer**la valeur. Limiter le champ juste en dessous de l’étiquette, comme illustré ci-dessous. Définir le contrôleur de la vue en tant que délégué de la prise de courant.
    - Un bouton intitulé **Envoyer une Notification de** contrainte juste en dessous de la zone de texte et dans le Centre horizontal.

    La vue doit se présenter comme suit :

    ![Concepteur de Xcode][32]


2. [Ajouter des prises de courant](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) pour le champ de texte et d’étiquette connecté votre vue et mettre à jour votre `interface` définition pour prendre en charge `UITextFieldDelegate` et `NSXMLParserDelegate`. Ajoutez les déclarations de trois propriété ci-dessous afin de prendre en charge l’appel de l’API REST et l’analyse de la réponse.

    Votre fichier ViewController.h doit se présenter comme suit :

        #import <UIKit/UIKit.h>

        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }

        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;

        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;

        @end

3. Ouvrir `HubInfo.h` et ajoutez les constantes suivantes qui seront utilisés pour envoyer des notifications à votre concentrateur. Remplacez le littéral de chaîne espace réservé par votre chaîne de connexion *DefaultFullSharedAccessSignature* réel.

        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. Ajoutez le code suivant `#import` instructions à votre `ViewController.h` fichier.

        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"

5. Dans `ViewController.m` ajoutez le code suivant à l’implémentation de l’interface. Ce code analysera votre chaîne de connexion *DefaultFullSharedAccessSignature* . Comme mentionné dans la [référence de l’API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx), ces informations analysées servira à générer un jeton SaS pour l’en-tête de la demande **d’autorisation** .

        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;

        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;

            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];

                @throw parseException;
            }

            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }

6. Dans `ViewController.m`, mettre à jour la `viewDidLoad` méthode pour analyser la chaîne de connexion lors du charge de la vue. Également ajouter les méthodes utilitaires, illustrés ci-dessous, à l’implémentation de l’interface.  


        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





7. Dans `ViewController.m`, ajoutez le code suivant à l’implémentation de l’interface à générer le jeton d’autorisation SaS qui sera fourni dans l’en-tête **d’autorisation** , comme indiqué dans la [Référence de l’API reste](http://msdn.microsoft.com/library/azure/dn495627.aspx).

        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;

            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }

            return token;
        }


8. CTRL + faire glisser à partir de la **Notification d’envoi** bouton à `ViewController.m` pour ajouter un objet action nommée **SendNotificationMessage** pour l’événement de **Touche vers le bas** . Mettre à jour la méthode par le code suivant pour envoyer la notification à l’aide de l’API REST.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }

        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];

            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];

            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }


9. Dans `ViewController.m`, ajoutez la méthode suivante de délégué pour prendre en charge la fermeture du clavier pour le champ de texte. CTRL + faire glisser à partir du champ de texte pour l’icône contrôleur de la vue dans le Concepteur de l’interface pour définir le contrôleur de la vue en tant que délégué de la prise de courant.

        //===[ Implement UITextFieldDelegate methods ]===

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }


10. Dans `ViewController.m`, ajoutez les méthodes suivantes de délégué pour prendre en charge de l’analyse de la réponse à l’aide de `NSXMLParser`.

        //===[ Implement NSXMLParserDelegate methods ]===

        -(void)parserDidStartDocument:(NSXMLParser *)parser
        {
            self.statusResult = @"";
        }

        -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
            namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
            attributes:(NSDictionary *)attributeDict
        {
            NSString * element = [elementName lowercaseString];
            NSLog(@"*** New element parsed : %@ ***",element);

            if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
            {
                self.currentElement = element;
            }
        }

        -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
        {
            self.statusResult = [self.statusResult stringByAppendingString:
                [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
        }

        -(void)parserDidEndDocument:(NSXMLParser *)parser
        {
            // Set the status label text on the UI thread
            dispatch_async(dispatch_get_main_queue(),
            ^{
                [self.sendResults setText:self.statusResult];
            });
        }



11. Générez le projet et vérifiez qu’il n’y a pas d’erreurs.


> [AZURE.NOTE] Si vous rencontrez une erreur de génération dans Xcode7 sur le support de bitcode, vous devez modifier les **Paramètres de génération** > **Activer Bitcode (ENABLE_BITCODE)** à **NO** dans Xcode. Le Kit de développement de concentrateurs de Notification ne prend pas en charge bitcode. 

Vous pouvez trouver toutes les charges possibles dans Apple [Local et Guide de programmation de Notification Push].


##<a name="checking-if-your-app-can-receive-push-notifications"></a>Vérifier si votre application peut recevoir des notifications de type Pousser

Pour tester les notifications de type Pousser sur iOS, vous devez déployer l’application sur un périphérique d’e/s physiques. Impossible d’envoyer des notifications de type Pousser Apple à l’aide du simulateur d’iOS.

1. Exécutez l’application et vérifiez que l’inscription a réussi et appuyez sur **OK**.

    ![Test de l’enregistrement de Notification App Push d’iOS][33]

2. Vous pouvez envoyer une notification de transmission de test à partir du [Portail Azure], comme décrit ci-dessus. Si vous avez ajouté le code pour l’envoi de notifications de type Pousser dans l’application, sélectionnez dans le champ de texte permet d’entrer un message de notification. Appuyez sur le bouton **Envoyer** sur le clavier ou le bouton **Envoyer une Notification** dans la vue pour envoyer le message de notification.

    ![iOS App Push Notification envoyer un Test][34]

3. La notification de transmission est envoyée à tous les périphériques qui sont inscrits pour recevoir des notifications à partir du concentrateur de Notification particulier.

    ![iOS App des Test réception d’une Notification de Push][35]


##<a name="next-steps"></a>Étapes suivantes

Dans cet exemple simple, vous envoyé des notifications de type pousser à tous vos périphériques iOS inscrits. Nous vous suggérons une étape suivante dans votre apprentissage que vous passez à didacticiel [Azure Notification concentrateurs avertir les utilisateurs pour iOS avec back-end de .NET] , qui vous guidera tout au long de la création d’un back-end pour envoyer des notifications de type pousser à l’aide de balises. 

Si vous souhaitez que segmenter les utilisateurs en groupes d’intérêt, vous pouvez en outre atteindre le didacticiel [Utilisation des concentrateurs de Notification pour envoyer des informations de dernière minute] . 

Pour obtenir des informations générales sur les concentrateurs de Notification, consultez le [Guide de concentrateurs de Notification].



<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Services mobiles iOS SDK version 1.2.4]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[Conseils de concentrateurs de notification]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Concentrateurs Azure Notification notifier des utilisateurs pour les e/s avec le serveur principal .NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Concentrateurs de Notification permet d’envoyer des informations de dernière minute]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local et poussez le Guide de programmation de Notification]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com