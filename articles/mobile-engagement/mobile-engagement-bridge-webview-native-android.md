<properties 
    pageTitle="WebView Android de pont avec natif Mobile Engagement Android SDK" 
    description="Décrit comment créer un pont entre WebView Javascript et le SDK de Android d’Engagement Mobile native en cours d’exécution"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-android" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>WebView Android de pont avec natif Mobile Engagement Android SDK

> [AZURE.SELECTOR]
- [Pont de Android](mobile-engagement-bridge-webview-native-android.md)
- [Pont d’e/s](mobile-engagement-bridge-webview-native-ios.md)

Certaines applications mobiles sont conçues sous la forme d’une application hybride dans lequel l’application elle-même est développée à l’aide de développement Android natif mais certains ou même tous les écrans sont restitués dans un affichage en mode Web Android. Vous pouvez toujours utiliser Mobile Engagement Android SDK au sein de ces applications, et ce didacticiel explique comment accéder à ce propos. L’exemple de code ci-dessous est basé sur la documentation Android [ici](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Il décrit comment utiliser cette approche documentée pour mettre en œuvre le même pour les méthodes couramment utilisées Mobile Engagement Android du Kit de développement, telle qu’un affichage Web à partir d’une application hybride peut également lancer des demandes à suivre les événements, les tâches, les erreurs, les app-info lors de leur tuyauterie via notre kit de développement logiciel Android. 

1. Tout d’abord, vous devez vous assurer que vous avez parcouru notre [didacticiel de mise en route](mobile-engagement-android-get-started.md) pour intégrer l’Engagement Android Kit de développement Mobile dans votre application hybride. Cela fait, votre `OnCreate` méthode ressemble à ce qui suit.  
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }

2. Assurez-vous que votre application hybride qu’un écran avec un affichage en mode Web sur celui-ci. Le code pour qu’il soit similaire à suivantes où nous chargeons un HTML local du fichier **Sample.html** dans l’affichage Web dans les `onCreate` méthode de votre écran. 

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }

        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }

3. Maintenant créer un fichier de pont appelé **WebAppInterface** , qui crée un wrapper sur certaines méthodes Mobile Engagement Android SDK couramment utilisées à l’aide de la `@JavascriptInterface` approche décrite dans la [documentation Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):

        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
        
        import com.microsoft.azure.engagement.EngagementAgent;
        
        import org.json.JSONArray;
        import org.json.JSONObject;
        
        public class WebAppInterface {
            Context mContext;
        
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
        
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
        
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
        
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
        
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  

4. Une fois que nous avons créé le fichier de pont ci-dessus, nous avons besoin pour vous assurer qu’il est associé à notre Webview. Pour ce faire, vous devez modifier votre `SetWebview` méthode afin qu’elle ressemble à ceci :

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }

5. Dans l’extrait de code ci-dessus, nous avons appelé `addJavascriptInterface` pour associer notre classe de pont avec notre Webview et également créé un handle appelé **EngagementJs** pour appeler les méthodes à partir du fichier de pont. 

6. Maintenant, créez le fichier suivant appelé **Sample.html** dans votre projet dans un dossier appelé **actifs** qui est chargé dans l’affichage en mode Web et où nous appelons les méthodes à partir du fichier de pont.

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
                      log('window.onerror: ' + err);
                    }
        
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
        
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
        
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
        
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
        
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
        
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
        
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
        
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>

8. Notez les points suivants concernant le fichier HTML ci-dessus :

    -   Il contient un ensemble de zones de saisie dans lequel vous pouvez fournir des données à utiliser en tant que noms pour vos événements, le travail, erreur, AppInfo. Lorsque vous cliquez sur le bouton en regard de celle-ci, un appel est effectué vers le code Javascript qui appelle les méthodes à partir du fichier de pont pour passer cet appel à l’Engagement de Mobile SDK Android. 
    -   Nous sommes le balisage sur des informations statiques supplémentaires pour les événements, les tâches et les erreurs même pour montrer comment cette opération peut être effectuée. Cette information supplémentaire est envoyée en tant que chaîne d’un JSON qui, si vous regardez dans le `WebAppInterface` de fichiers, est analysé et mis dans un Android `Bundle` et est transmise avec l’envoi d’événements, les tâches, les erreurs. 
    -   Une tâche d’Engagement Mobile est lancée avec le nom que vous spécifiez dans la zone de saisie, exécutez pendant 10 secondes et arrêter. 
    -   Un Engagement de Mobile appinfo ou la balise est passé avec 'customer_name' comme clé statique et de la valeur que vous avez entré dans l’entrée comme valeur de la balise. 
 
9. Exécutez l’application et vous verrez le texte suivant. Maintenant fournir un nom pour un événement de test comme suit, puis cliquez sur **Envoyer** ci-dessous. 

    ![][1]

10. Maintenant si vous accédez à l’onglet **Analyseur** de votre application et un aspect sous **événements -> Détails**, vous verrez cet événement s’affichent en même temps que l’application-info statique vous envoyer. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
