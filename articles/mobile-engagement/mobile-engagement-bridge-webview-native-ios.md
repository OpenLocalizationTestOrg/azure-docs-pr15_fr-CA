<properties 
    pageTitle="Pont iOS WebView avec iOS native de mission de Mobile SDK" 
    description="Décrit comment créer un pont entre WebView exécutant Javascript et iOS natif Engagement de Mobile SDK"      
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
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Pont iOS WebView avec iOS native de mission de Mobile SDK

> [AZURE.SELECTOR]
- [Pont de Android](mobile-engagement-bridge-webview-native-android.md)
- [Pont d’e/s](mobile-engagement-bridge-webview-native-ios.md)

Certaines applications mobiles sont conçues sous la forme d’une application hybride dans lequel l’application elle-même est développée en utilisant un développement natif iOS Objective-C mais certaines ou même tous les écrans sont restitués dans une e/s en mode Web. Vous pouvez toujours utiliser des Mobile Engagement iOS, Kit de développement logiciel au sein de ces applications, et ce didacticiel explique comment accéder à ce propos. 

Il existe deux approches pour ce faire, même si les deux ne sont pas documentées :

- Tout d’abord une est décrite sur ce [lien](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) , qui implique l’enregistrement d’une `UIWebViewDelegate` sur votre affichage web et catch-et-immédiatement-Annuler une modification de l’emplacement dans Javascript. 
- En second lieu un est basé sur cette [session de WWDC 2013](https://developer.apple.com/videos/play/wwdc2013/615), une approche plus NET que la première et qui sera suivie de ce guide. Notez que cette approche ne fonctionne que sur les iOS7 et les suivants. 

Suivez les étapes ci-dessous pour iOS pont exemple :

1. Tout d’abord, vous devez vous assurer que vous avez parcouru notre [didacticiel de mise en route](mobile-engagement-ios-get-started.md) pour l’intégration Mobile Engagement iOS SDK dans votre application hybride. Si vous le souhaitez, vous pouvez également activer la journalisation de test comme suit afin que vous pouvez voir les méthodes du Kit de développement logiciel que nous les déclencher l’affichage Web. 
    
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
            [EngagementAgent setTestLogEnabled:YES];
           ....
        }

2. Assurez-vous que votre application hybride qu’un écran avec un affichage en mode Web sur celui-ci. Vous pouvez l’ajouter à la `Main.storyboard` de l’application. 

3. Associer ce mode d’affichage Web avec votre **ViewController** en faisant glisser l’affichage Web à partir de la scène de contrôleur d’affichage pour le `ViewController.h` modifier écran, placer immédiatement sous la `@interface` ligne. 

4. Une fois cela fait, une boîte de dialogue s’affiche vous demandant un nom. Indiquez le nom en tant que **mode d’affichage Web**. Votre `ViewController.h` fichier doit ressembler à ce qui suit :

        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
        
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
        
        @end

5. Nous mettrons à jour la `ViewController.m` de fichiers ultérieurement, mais tout d’abord, nous allons créer le fichier de pont qui crée un wrapper sur certains iOS Mobile Engagement couramment utilisées des méthodes de kit de développement logiciel. Créer un nouveau fichier d’en-tête appelé **EngagementJsExports.h** qui utilise le `JSExport` mécanisme décrit dans la ladite [session](https://developer.apple.com/videos/play/wwdc2013/615) pour exposer les méthodes d’e/s native. 

        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
        
        @protocol EngagementJsExports <JSExport>
        
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
        
        @end

        @interface EngagementJs : NSObject <EngagementJsExports>

        @end

6. Vous créez ensuite la deuxième partie du fichier bridge. Créez un fichier nommé **EngagementJsExports.m** qui contient la mise en oeuvre création les wrappers réels en appelant les méthodes de kit de développement logiciel de Mobile Engagement iOS. Notez également que nous sommes l’analyse de la `extras` passé à partir de l’affichage en mode Web javascript et qui en plaçant une `NSMutableDictionary` objet d’être passé avec la méthode d’appelle SDK de l’Engagement.  

        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
        
        @implementation EngagementJs
        
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
        
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
        
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
        
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
        
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
        
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
           
           return extras;
        }
        
        @end

5. Maintenant, nous y revenir à la **ViewController.m** et mettre à jour avec le code suivant : 
        
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
        
        @interface ViewController ()
        
        @end
        
        @implementation ViewController
        
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
        
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
           
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
        
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
           
           context[@"EngagementJs"] = [EngagementJs class];
        }
        
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
        
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
        
        @end

6. Notez les points suivants concernant le fichier **ViewController.m** :

    - Dans le `loadWebView` méthode, nous allons charger un fichier HTML local appelé **LocalPage.html** dont nous allons ensuite le code. 
    - Dans le `webViewDidFinishLoad` méthode, nous allons saisir le `JsContext` et en associant notre classe wrapper. Ainsi, notre wrapper l’appel de méthodes du Kit de développement logiciel à l’aide de la poignée de **EngagementJs** à partir de l’affichage en mode Web. 

7. Créez un fichier nommé **LocalPage.html** avec le code suivant :

        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
               
               <script type="text/javascript">
               
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
               
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
                   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
                           
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
        
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
                           
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
               
           </head>
           <body>
               <h1>Bridge Tester</h1>
               
               <div id='engagement'>
                   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
                   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
                   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
                   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
               
               </div>
           </body>
        </html>

8. Notez les points suivants concernant le fichier HTML ci-dessus :

    -   Il contient un ensemble de zones de saisie dans lequel vous pouvez fournir des données à utiliser en tant que noms pour vos événements, le travail, erreur, AppInfo. Lorsque vous cliquez sur le bouton en regard de celle-ci, un appel est effectué pour le code Javascript qui appelle les méthodes à partir du fichier de pont pour passer cet appel à Mobile Engagement iOS SDK. 
    -   Nous sommes le balisage sur des informations statiques supplémentaires pour les événements, les tâches et les erreurs même pour montrer comment cette opération peut être effectuée. Cette information supplémentaire est envoyée en tant que chaîne d’un JSON qui, si vous regardez dans le `EngagementJsExports.m` de fichiers, qui est analysée et transmis avec l’envoi d’événements, les tâches, les erreurs. 
    -   Une tâche d’Engagement Mobile est lancée avec le nom que vous spécifiez dans la zone de saisie, exécutez pendant 10 secondes et arrêter. 
    -   Un Engagement de Mobile appinfo ou la balise est passé avec 'customer_name' comme clé statique et de la valeur que vous avez entré dans l’entrée comme valeur de la balise. 
 
9. Exécutez l’application et vous verrez le texte suivant. Maintenant fournir un nom pour un événement de test comme suit, puis cliquez sur **Envoyer** en regard de celui-ci. 

    ![][1]

10. Maintenant si vous accédez à l’onglet **Analyseur** de votre application et un aspect sous **événements -> Détails**, vous verrez cet événement s’affichent en même temps que l’application-info statique vous envoyer. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
