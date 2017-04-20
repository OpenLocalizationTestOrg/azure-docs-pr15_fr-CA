<properties
    pageTitle="Envoyer des notifications de type Pousser aux applications de Chrome avec des concentrateurs de Notification Azure | Microsoft Azure"
    description="Apprenez à utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type pousser à une application de Chrome."
    services="notification-hubs"
    keywords="les notifications de transmission mobiles, des notifications de type Pousser, pousser la notification, les notifications de transmission de chrome"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-chrome"
    ms.devlang="JavaScript"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Envoyer des notifications de type Pousser aux applications de Chrome avec Azure Notification concentrateurs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Cette rubrique vous indique comment utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type pousser à une application de Chrome qui sera affiché dans le contexte du navigateur Google Chrome. Dans ce didacticiel, nous allons créer une application de Chrome qui reçoit des notifications de type pousser à l’aide de [La messagerie de nuage Google (GCM)](https://developers.google.com/cloud-messaging/). 

>[AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

Le didacticiel vous guide tout au long des étapes de base suivantes pour activer les notifications de transmission :

* [Activer le Cloud Google messagerie](#register)
* [Configurer votre concentrateur de notification](#configure-hub)
* [Connexion de votre application de Chrome pour le concentrateur de notification](#connect-app)
* [Envoyer une notification de transmission à votre application de Chrome](#send)
* [Fonctions et fonctionnalités supplémentaires](#next-steps)

>[AZURE.NOTE] Notifications de type Pousser chrome app ne sont pas des notifications dans le navigateur génériques - elles sont spécifiques à l’extensibilité de navigateur modèle (voir la [Vue d’ensemble des applications de Chrome] pour plus de détails). Outre le navigateur de bureau, les applications de Chrome passent sur mobile (Android et iOS) Apache Cordova. Voir [Applications Chrome sur Mobile] pour en savoir plus.

Configuration de GCM et concentrateurs de Notification Azure est identique à la configuration pour Android, étant donné que [Google Cloud messagerie de Chrome] a été désapprouvée et le GCM même prend désormais en charge les appareils Android et instances de Chrome.

##<a id="register"></a>Activer le Cloud Google messagerie

1. Accédez au site Web de [Google Cloud Console] , connectez-vous avec vos informations d’identification du compte Google et puis cliquez sur le bouton **Créer un projet** . Fournir un **Nom de projet**, puis cliquez sur le bouton **créer** .

    ![Nuage de Google Console - permet de créer le projet][1]

2. Prenez note du **Numéro de projet** sur la page **projets** pour le projet que vous venez de créer. Vous utiliserez ce que **GCM Sender ID** dans l’application de Chrome à enregistrer avec le GCM.

    ![Console de Google Cloud - numéro de projet][2]

3. Dans le volet gauche, cliquez sur les **API et authentification**et puis faites défiler vers le bas et cliquez sur le bouton pour activer la **Messagerie de nuage de Google pour Android**. Vous n’êtes pas obligé d’activer la **Messagerie de nuage de Google pour le Chrome**.

    ![Console de Google Cloud - clé serveur][3]

4. Dans le volet gauche, cliquez sur **informations d’identification** > **Créer une nouvelle clé** > **Clé serveur** > **créer**.

    ![Nuage de Google Console - informations d’identification][4]

5. Prenez note de la **Clé de l’API**du serveur. Vous allez configurer cela dans votre centre de notification suivant, pour lui permettre d’envoyer des notifications de type pousser à GCM.

    ![Console de Google Cloud - clé API][5]

##<a id="configure-hub"></a>Configurer votre concentrateur de notification

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. dans la lame de **paramètres** , sélectionnez **Notification Services** , puis sur **Google (GCM)**. Entrez la clé d’API et d’enregistrer.

&emsp;&emsp;![Concentrateurs de Notification Azure - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

##<a id="connect-app"></a>Connexion de votre application de Chrome pour le concentrateur de notification

Votre concentrateur de notification est maintenant configuré pour fonctionner avec le GCM, et que les chaînes de connexion pour enregistrer votre application pour recevoir et envoyer des notifications de type Pousser. LK

###<a name="create-a-new-chrome-app"></a>Créer une nouvelle application de Chrome

L’exemple ci-dessous est basé sur l' [Exemple de Chrome App GCM] et utilise la méthode recommandée pour créer une application de Chrome. Nous mettrons en évidence les étapes spécifiquement liées à des concentrateurs de Notification Azure. 

>[AZURE.NOTE] Nous vous recommandons de télécharger la source pour cette application de Chrome à partir de [l’Exemple Notification Chrome App].

L’application de Chrome est créée via JavaScript, et vous pouvez utiliser une de vos éditeurs favoris word pour sa création. Voici l’aspect de cette application de Chrome.

![Application de Google Chrome][15]

1. Créez un dossier et nommez-le `ChromePushApp`. Bien entendu, le nom est arbitraire, si vous lui quelque chose de différent, assurez-vous que vous remplacez le chemin d’accès dans les segments de code requis.

2. Télécharger la [bibliothèque de crypto-js] dans le dossier que vous avez créé à la deuxième étape. Ce dossier contient deux sous-dossiers : `components` et `rollups`.

3. Créer un `manifest.json` fichier. Toutes les applications de Chrome sont soutenues par un fichier manifeste qui contient les métadonnées de l’application et, plus important encore, toutes les autorisations qui sont accordées à l’application lorsque l’utilisateur l’installe.

        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }

    Avis du `permissions` élément, qui spécifie que cette application Chrome sera en mesure de recevoir des notifications de type Pousser de GCM. Elle doit également spécifier l’URI de concentrateurs de Notification Azure où l’application de Chrome fera appel reste pour l’inscription.
    Notre exemple d’application utilise également un fichier icône, `gcm_128.png`, que vous trouverez à la source est réutilisée à partir de l’exemple GCM d’origine. Vous pouvez le substituer n’importe quelle image qui correspond aux [critères de l’icône](https://developer.chrome.com/apps/manifest/icons).

4. Créez un fichier appelé `background.js` par le code suivant :

        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }

        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.

          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);

          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }

        var registerWindowCreated = false;

        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {

            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }

        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);

        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);

    Ceci est le fichier qui s’affiche la fenêtre de l’application de Chrome HTML (**register.html**) et définit également le gestionnaire **messageReceived** pour gérer les notifications entrantes.

5. Créez un fichier appelé `register.html` -définit l’interface utilisateur de l’application de Chrome. 

   >[AZURE.NOTE] Cet exemple utilise **v3.1.2 de CryptoJS**. Si vous avez téléchargé une autre version de la bibliothèque, vérifiez que vous remplacez correctement la version dans le `src` chemin d’accès.

        <html>

        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>

        <body>

        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>

        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

        <br/>

        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

        <br/>
        <br/>

        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

        </body>

        </html>

6. Créez un fichier appelé `register.js` avec le code ci-dessous. Ce fichier indique le script derrière `register.html`. Applications de chrome n’autorisent pas l’exécution inline, donc vous devez créer un script de sauvegarde distinct pour votre interface utilisateur.

        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }

        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }

          document.getElementById("console").innerHTML = currentStatus  + status;
        }

        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);

          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }

        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;

          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }

          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;

          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }

        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }

        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";

          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });

          originalUri = endpoint + hubName;
        }

        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration

          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;

          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);

          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }

        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";

          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();

          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };

          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }

          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");

          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }

    Le script ci-dessus possède les paramètres de clé suivants :
    - **Window.OnLoad** définit les événements de clic de bouton de deux boutons dans l’interface utilisateur. Un inscrit avec GCM, et l’autre utilise l’ID de l’enregistrement qui est retournée après l’enregistrement avec GCM pour enregistrer avec Azure Notification concentrateurs.
    - **updateLog** est la fonction qui permet de gérer les fonctions d’enregistrement simple.
    - **registerWithGCM** est le premier gestionnaire de clic de bouton, qui rend le `chrome.gcm.register` appel à GCM pour inscrire l’instance en cours de Chrome App.
    - **registerCallback** est la fonction de rappel qui est appelée lorsque l’appel d’inscription GCM renvoie.
    - **registerWithNH** est le deuxième gestionnaire de clic de bouton, qui inscrit les concentrateurs de la Notification. Il obtient `hubName` et `connectionString` (sur lequel l’utilisateur a spécifié) et développe l’appel de l’API REST de Notification concentrateurs d’enregistrement.
    - **splitConnectionString** et **generateSaSToken** sont des Assistants qui représentent l’implémentation JavaScript d’un processus de création d’un jeton de SaS, qui doit être utilisé dans tous les appels de l’API REST. Pour plus d’informations, consultez [Concepts communs](http://msdn.microsoft.com/library/dn495627.aspx).
    - **sendNHRegistrationRequest** est la fonction qui appelle un reste de HTTP à des concentrateurs de Notification Azure.
    - **registrationPayload** définit la charge utile de l’inscription XML. Pour plus d’informations, consultez [Création d’API REST NH d’enregistrement]. Mise à jour de l’ID d’enregistrement avec ce que nous avons reçu de GCM.
    - **client** est une instance de **XMLHttpRequest** qui nous permet de faire la demande HTTP POST. Notez que nous mettons à jour la `Authorization` en-tête avec `sasToken`. Réussite de cet appel enregistre cette instance de l’application de Chrome avec Azure Notification concentrateurs.


La structure générale de dossier pour ce projet doit ressembler à ceci :     ![Google Chrome App - Structure de dossier][21]

###<a name="set-up-and-test-your-chrome-app"></a>Configurer et tester votre application de Chrome

1. Ouvrez votre navigateur Chrome. Ouvrir **les extensions de Chrome** et activer le **mode développeur**.

    ![Google Chrome - activer le Mode développeur][16]

2. Cliquez sur **charger l’extension décompressée** et naviguez jusqu’au dossier où vous avez créé les fichiers. Vous pouvez également utiliser le **Chrome applications et outils de développement d’Extensions**. Cet outil est une application de Chrome en soi (installé dans le magasin Web de Chrome) et fournit des fonctions avancées de débogage pour le développement de votre application de Chrome.

    ![Google Chrome - charger l’Extension décompressée][17]

3. Si l’application Chrome est créée sans erreur, puis vous verrez votre application de Chrome s’affichent.

    ![Google Chrome - Chrome App affichage][18]

4. Entrez le **Numéro de projet** que vous avez précédemment à partir de la **Console de nuage de Google** comme l’ID de l’expéditeur, puis cliquez sur **Enregistrer avec GCM**. Vous devez voir le message **l’inscription a réussi le GCM.**

    ![Google Chrome - personnalisation d’application de Chrome][19]

5. Entrez votre **Nom de concentrateur de Notification** et le **DefaultListenSharedAccessSignature** que vous avez obtenue à partir du portail, puis cliquez sur **inscrire avec concentrateur de Notification Azure**. Vous devez voir le message **enregistrement de concentrateur de Notification réussi !** et les détails de la réponse d’inscription, qui contient l’enregistrement de concentrateurs de Notification Azure ID.

    ![Google Chrome - spécifiez les détails de concentrateur de Notification][20]  

##<a name="send"></a>Envoyer une notification à votre application de Chrome

À des fins de test, nous vous enverrons application console de notifications de type Pousser de Chrome à l’aide de .NET. 

>[AZURE.NOTE] Vous pouvez envoyer des notifications de transmission avec Notification de concentrateurs à partir de n’importe quel serveur principal via notre public <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Consultez notre [portail de la documentation](https://azure.microsoft.com/documentation/services/notification-hubs/) pour plus d’exemples multiplates-formes.

1. Dans Visual Studio, dans le menu **fichier** , sélectionnez **Nouveau** , puis **projet**. Dans **Visual C#**, cliquez sur **Windows** et **Application Console**, puis cliquez sur **OK**.  Cette opération crée un nouveau projet d’application console.

2. Dans le menu **Outils** , cliquez sur **Gestionnaire de package de bibliothèque de** **Console du Gestionnaire de package**. Cette option affiche la Console du Gestionnaire de package.

3. Dans la fenêtre de la console, exécutez la commande suivante :

        Install-Package Microsoft.Azure.NotificationHubs

    Cette opération ajoute une référence pour le Bus de Service Azure SDK avec le <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">package NuGet de WindowsAzure.ServiceBus</a>.

4. Ouvrir `Program.cs` et ajoutez le code suivant `using` instruction :

        using Microsoft.Azure.NotificationHubs;

5. Dans le `Program` de classe, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

    Veillez à remplacer le `<hub name>` espace réservé avec le nom du concentrateur de notification qui s’affiche dans le [portail](https://portal.azure.com) de la blade de concentrateur de Notification. De même, remplacez l’espace réservé chaîne de connexion avec la chaîne de connexion nommée `DefaultFullSharedAccessSignature` que vous avez obtenu dans la section de configuration de concentrateur de notification.

    >[AZURE.NOTE] Assurez-vous que vous utilisez la chaîne de connexion avec un accès **complet** , ne pas **écouter** les accès. La chaîne de connexion access **écouter** n’accorde pas les autorisations pour envoyer des notifications de type Pousser.

5. Ajouter les appels suivants de la `Main` méthode :

         SendNotificationAsync();
         Console.ReadLine();
         
6. Assurez-vous que le Chrome est en cours d’exécution et exécutez l’application console.

7. Vous devriez voir la notification suivante de raccourcis sur le bureau.

    ![Google Chrome - Notification][13]

8. Vous pouvez également voir toutes vos notifications à l’aide de la fenêtre Notifications du Chrome dans la barre des tâches (sous Windows) lorsque le Chrome est en cours d’exécution.

    ![Google Chrome - liste des Notifications][14]

>[AZURE.NOTE] Vous n’avez pas besoin que l’exécution de l’application de Chrome ou d’ouvrir dans le navigateur (bien que du navigateur Chrome lui-même doit être en cours d’exécution). Vous obtenez également une vue consolidée de toutes vos notifications dans la fenêtre Notifications du Chrome.

## <a name="next-steps"> </a>Étapes suivantes

Pour en savoir plus sur la Notification des concentrateurs dans [Vue d’ensemble des concentrateurs de Notification].

Pour cibler des utilisateurs spécifiques, consultez le didacticiel [Azure Notification concentrateurs notifier utilisateurs] . 

Si vous souhaitez que segmenter les utilisateurs en groupes d’intérêt, vous pouvez suivre le didacticiel de [Concentrateurs de Notification Azure dernières actualités] .

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Exemple de concentrateur de chrome application Notification]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Console de nuage de Google]: http://cloud.google.com/console
[Azure Classic Portal]: https://manage.windowsazure.com/
[Vue d’ensemble des concentrateurs de notification]: notification-hubs-push-notification-overview.md
[Vue d’ensemble des applications de chrome]: https://developer.chrome.com/apps/about_apps
[Exemple d’application GCM de chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Applications de chrome sur Mobile]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Créer l’enregistrement NH reste API]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[bibliothèque de Crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Nuage de Google Chrome de messagerie]: https://developer.chrome.com/apps/cloudMessagingV1
[Les concentrateurs Azure Notification notifient les utilisateurs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Informations de dernière minute de concentrateurs de Notification Azure]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
