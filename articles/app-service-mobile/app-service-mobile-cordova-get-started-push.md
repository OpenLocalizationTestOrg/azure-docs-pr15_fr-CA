<properties
    pageTitle="Ajouter des Notifications de type pousser à Apache Cordova application Azure applications mobiles | Service d’application Azure"
    description="Apprenez à utiliser les applications Azure Mobile pour envoyer des notifications de type pousser à votre application d’Apache Cordova."
    services="app-service\mobile"
    documentationCenter="javascript"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Ajouter des notifications de type pousser à votre application d’Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d’ensemble

Dans ce didacticiel, vous ajoutez les notifications de transmission au projet [rapide à Apache Cordova démarrer] afin qu’une notification de transmission est envoyée au périphérique chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez le progiciel d’extension de notification push. Pour plus d’informations, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel décrit une application Apache Cordova développée avec Visual Studio 2015 qui s’exécute sur l’émulateur Android de Google, un appareil Android, un périphérique Windows et un périphérique iOS.

Pour terminer ce didacticiel, vous devez :

* Un ordinateur avec [Visual Studio 2015 communautaire] d’ou versions ultérieures.
* [Visual Studio Tools pour Apache Cordova].
* Un [compte Azure actif](https://azure.microsoft.com/pricing/free-trial/).
* Un projet de [Cordova Apache démarrage rapide] terminé.
* (Android) Un [compte Google] avec une adresse email vérifié.
* (e/s) Un abonnement Apple Developer Program et un périphérique iOS (iOS Simulator ne gère pas les push).
* (Windows) Un compte de développeur Windows Store et un périphérique Windows 10.

##<a name="configure-hub"></a>Configurer un concentrateur de notification

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Regardez une vidéo illustrant les étapes de cette section](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##<a name="update-the-server-project-to-send-push-notifications"></a>Mettre à jour le projet serveur pour envoyer des notifications de type Pousser

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>Modifier votre application Cordova pour recevoir des notifications de type Pousser

Vous devez vous assurer que votre projet d’application de Cordova d’Apache est prêt à traiter les notifications de type Pousser en installant le plug-in de transmission Cordova ainsi que des services spécifiques à la plateforme push.

#### <a name="update-the-cordova-version-in-your-project"></a>Mettre à jour la version de Cordova dans votre projet.

Nous vous recommandons de mettre à jour le projet client à Cordova 6.1.1 si votre projet est configuré à l’aide d’une version antérieure. Pour mettre à jour le projet, cliquez sur config.xml pour ouvrir le Concepteur de la configuration. Sélectionnez l’onglet plates-formes et choisissez 6.1.1 dans la zone de texte **Cordova CLI** .

Cliquez sur **créer**, puis sur **Générer la Solution** pour mettre à jour le projet.

#### <a name="install-the-push-plugin"></a>Installer le plug-in de push

En mode natif, Apache Cordova applications ne gèrent pas les fonctionnalités de réseau ou de périphérique.  Ces fonctionnalités sont fournies par les plug-ins qui sont publiés sur [npm](https://www.npmjs.com/) ou sur GitHub.  Le `phonegap-plugin-push` plug-in est utilisé pour gérer les notifications de transmission réseau.

Vous pouvez installer le plug-in de push dans une des manières suivantes :

**À partir de l’invite de commande :**

Exécutez la commande suivante :

    cordova plugin add phonegap-plugin-push

**À partir de Visual Studio :**

1.  Dans l’Explorateur de solutions, ouvrez le `config.xml` fichier **plug-ins**, cliquez sur > **personnalisé**, sélectionnez **Git** comme la source d’installation, puis entrez `https://github.com/phonegap/phonegap-plugin-push` comme source.

    ![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  Cliquez sur la flèche en regard de la source d’installation.

3. Dans **SENDER_ID**, si vous disposez déjà d’un ID numérique de projet pour le projet de Console de développement Google, vous pouvez l’ajouter ici. Sinon, entrez une valeur d’espace réservé, comme 777777, et si vous ciblez Android vous pouvez mettre à jour cette valeur dans le fichier config.xml ultérieurement.

4. Cliquez sur **Ajouter**.

Le plug-in de push est maintenant installé.

####<a name="install-the-device-plugin"></a>Installer le plug-in du périphérique

Suivez la même procédure que vous avez utilisé pour installer le plug-in de push, mais vous trouverez le plug-in de périphérique dans la liste de plug-ins de base (cliquez sur **plug-ins** > **Core** pour le trouver). Vous avez besoin de ce plug-in pour obtenir le nom de la plateforme (`device.platform`).

#### <a name="register-your-device-for-push-on-start-up"></a>Inscrire votre appareil pour la distribution au démarrage

Initialement, nous inclurons un code minimal pour Android. Par la suite, nous rendra quelques petites modifications sur iOS ou Windows 10.

1. Ajoutez un appel à **registerForPushNotifications** pendant le rappel pour le processus de connexion, ou à la fin de la méthode **onDeviceReady** :

        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    Cet exemple illustre l’appel de **registerForPushNotifications** après l’authentification réussit, qui est recommandé lors de l’utilisation des notifications de transmission et de l’authentification dans votre application.

2. Ajoutez la nouvelle méthode **registerForPushNotifications** comme suit :

        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });

        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.            
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });

        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
        }

3. (Android) Dans le code ci-dessus, remplacez `Your_Project_ID` avec le numérique ID du projet pour votre application à partir de la [Console de développeur de Google].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Facultatif) Configurer et exécuter l’application sur Android

Cette tâche pour activer les notifications de type Pousser pour Android.

####<a name="enable-gcm"></a>Activer la Firebase de messagerie en nuage

Puisque nous ciblons initialement la plate-forme Android de Google, vous devez activer la messagerie de nuage Firebase. De la même façon, si vous ont été ciblant les périphériques Microsoft Windows, vous devriez autoriser la prise en charge WNS.

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

####<a name="configure-backend"></a>Configurer le serveur principal chargé de l’application Mobile pour envoyer des demandes de transmission à l’aide de FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####<a name="configure-your-cordova-app-for-android"></a>Configurer votre application de Cordova pour Android

Dans votre application de Cordova, ouvrez config.xml et remplacez `Your_Project_ID` avec le numérique ID du projet pour votre application à partir de la [Console de développeur de Google].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Ouvrez index.js et mettre à jour le code pour utiliser votre code de projet numérique.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

####<a name="configure-device"></a>Configurer votre appareil Android pour débogage USB

Avant de déployer votre application sur votre appareil Android, vous devez activer le débogage USB.  Sur votre téléphone Android, effectuez les opérations suivantes :

1. Accédez à **paramètres** > **sur le téléphone**, puis cliquez sur le **numéro de Build** jusqu'à ce que le mode développeur est activé (environ 7 fois).

2. Dans les **paramètres** > **Developer Options** activer le **débogage USB**, puis connectez votre téléphone Android pour le développement de votre PC à l’aide d’un câble USB.

Nous avons testé cette utilisant un dispositif de X Google Nexus 5 Android 6.0 (guimauve) en cours d’exécution.  Toutefois, les techniques sont communes à toutes les versions de Android modernes.

#### <a name="install-google-play-services"></a>Installer des Services de lecture de Google

Le plug-in de push repose sur Android Google lire des Services pour les notifications de transmission.  

1.  Dans **Visual Studio**, cliquez sur **Outils** > **Android** > **Android SDK Manager**, développez le dossier **Extras** et la case à cocher pour vous assurer que chacun des kits de développement logiciel suivants est installé.
    * Android 2.3 ou supérieur
    * Révision du référentiel de Google 27 ou supérieure
    * Services de lecture de Google 9.0.2 ou version ultérieure

2.  Cliquez sur **Installer des Packages** et attendre pour terminer l’installation.

En cours requis bibliothèques sont répertoriés dans la [documentation d’installation du plug-in phonegap-push].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Notifications de type Pousser de test de l’application sur Android

Vous pouvez les notifications de type Pousser du test maintenant en exécutant l’application et l’insertion d’éléments dans la table TodoItem. Cela à partir du même périphérique ou à partir d’un second périphérique, dans la mesure où vous utilisez le même arrière-plan. Testez votre application de Cordova sur la plate-forme Android dans une des manières suivantes :

- **Sur un périphérique physique :**  
Joindre votre appareil Android à votre ordinateur de développement avec un câble USB.  Au lieu de l' **Émulateur Android de Google**, sélectionnez le **périphérique**. Visual Studio va déployer l’application sur le périphérique et l’exécuter.  Vous pouvez alors interagir avec l’application sur le périphérique.  
Améliorer votre expérience de développement.  Écran de partage d’applications telles que les [Mobizen] peuvent vous aider dans le développement d’une application d’Android en projetant l’écran Android à un navigateur web sur votre ordinateur.

- **Sur un émulateur Android :**  
Il existe des étapes de configuration supplémentaires requises lors de l’exécution sur un émulateur.

    Assurez-vous que vous déployez à ou le débogage sur un périphérique virtuel qui a des APIs de Google défini comme cible, comme indiqué dans le Gestionnaire de périphérique virtuel Android (AVD).

    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Si vous souhaitez utiliser un x86 plus rapide émulateur, vous [installez le pilote HAXM](https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM) et configurer l’émulateur de l’utiliser.

    Ajouter un compte Google Android périphérique en cliquant sur **applications** > **paramètres** > **Ajouter un compte**, puis suivez les invites pour ajouter un Google existante compte au périphérique (nous recommandons à l’aide d’un compte existant plutôt que d’en créer un).

    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Exécutez l’application de la liste des tâches comme avant et insérer un nouvel élément todo. Cette fois-ci, une icône de notification s’affiche dans la zone de notification. Vous pouvez ouvrir le tiroir de notification pour afficher le texte intégral de la notification.

    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##<a name="optional-configure-and-run-on-ios"></a>(Facultatif) Configurer et exécuter des e/s

Cette section est pour l’exécution du projet Cordova sur les périphériques d’e/s. Vous pouvez ignorer cette section si vous ne travaillez pas avec les périphériques d’e/s.

####<a name="install-and-run-the-ios-remotebuild-agent-on-a-mac-or-cloud-service"></a>Installez et exécutez l’agent de remotebuild des e/s sur un Mac ou cloud service

Avant de pouvoir exécuter une application de Cordova sur les e/s à l’aide de Visual Studio, accédez à travers les étapes dans les [e/s Guide de configuration](http://taco.visualstudio.com/en-us/docs/ios-guide/) pour installer et exécuter l’agent remotebuild.

Vérifiez que vous pouvez générer l’application pour iOS. Les étapes du guide de configuration sont nécessaires pour créer des e/s à partir de Visual Studio. Si vous ne disposez pas d’un Mac, vous pouvez créer pour les e/s à l’aide de l’agent remotebuild sur un service tel que MacInCloud. Pour plus d’informations, voir [exécuter votre application iOS dans le nuage](http://taco.visualstudio.com/en-us/docs/build_ios_cloud/).

>[AZURE.NOTE] XCode 7 ou version ultérieure est requis pour utiliser le plug-in de push sur iOS.

####<a name="find-the-id-to-use-as-your-app-id"></a>Rechercher l’ID à utiliser en tant que votre ID d’application

Avant d’enregistrer votre application pour les notifications de type Pousser config.xml ouvert dans votre application de Cordova, trouver la `id` valeur dans l’élément de widget d’attribut et le copier pour une utilisation ultérieure. Dans le code XML suivant, l’ID est `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
        version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Par la suite, utilisez cet identificateur lorsque vous créez un ID de l’application sur le portail des développeurs d’Apple. (Si vous créez un autre ID d’application sur le portail des développeurs et à utiliser qui, vous devrez effectuer quelques étapes supplémentaires plus loin dans ce didacticiel pour modifier ce code dans le fichier config.xml. L’ID de l’élément de widget doit correspondre à l’ID de l’application sur le portail de développement.)

####<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Enregistrement de l’application de notifications de type Pousser sur le portail des développeurs d’Apple

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Regardez une vidéo illustrant les étapes similaires](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####<a name="configure-azure-to-send-push-notifications"></a>Configurer Azure pour envoyer des notifications de type Pousser

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

####<a name="verify-that-your-app-id-matches-your-cordova-app"></a>Vérifiez que votre ID App correspond à votre application Cordova

Si l’ID d’application déjà créé dans votre compte de développeur Apple correspond à l’ID de l’élément dans le fichier config.xml widget, vous pouvez ignorer cette étape. Toutefois, si les codes ne correspondent pas, procédez comme suit :

1. Supprimez le dossier de plates-formes à partir de votre projet.

2. Supprimer le dossier Plug-ins de votre projet.

3. Supprimez le dossier node_modules de votre projet.

4. Mettre à jour l’attribut id de l’élément de widget dans le fichier config.xml pour utiliser l’ID de l’application que vous avez créé dans votre compte de développeur d’Apple.

5. Régénérez votre projet.

#####<a name="test-push-notifications-in-your-ios-app"></a>Notifications de type Pousser de test dans votre application iOS

1. Dans Visual Studio, assurez-vous **iOS** est sélectionné comme cible du déploiement, puis choisissez le **périphérique** sur votre périphérique iOS connecté.

    Vous pouvez exécuter sur un périphérique iOS connecté à votre ordinateur à l’aide d’iTunes. L’iOS Simulator ne gère pas les notifications de transmission.

2. Appuyez sur le bouton **exécuter** ou sur **F5** dans Visual Studio pour générer le projet et démarrer l’application dans un dispositif d’e/s, puis cliquez sur **OK** pour accepter les notifications de transmission.

    >[AZURE.NOTE] Vous devez explicitement accepter les notifications de type pousser à partir de votre application. Cette demande ne se produit que la première fois que l’application s’exécute.

3. Dans l’application, entrez une tâche, puis cliquez sur le signe plus (+) icône.

4. Vérifiez qu’une notification est reçue, puis cliquez sur OK pour fermer la notification.

##<a name="optional-configure-and-run-on-windows"></a>(Facultatif) Configurer et exécuter sur Windows

Cette section est pour le projet d’application Apache Cordova en cours d’exécution sur les périphériques Windows 10 (le plug-in de PhoneGap push est pris en charge sur Windows 10). Vous pouvez ignorer cette section si vous ne travaillez pas avec des périphériques Windows.

####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Inscrire votre application Windows pour les notifications de transmission avec WNS

Pour utiliser les options de mémorisation dans Visual Studio, sélectionnez une cible de Windows à partir de la liste des plateformes de Solution, tel que **Windows-x64** ou **Windows-x86** (éviter de **Windows-AnyCPU** pour les notifications de type Pousser).

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Regardez une vidéo illustrant les étapes similaires](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####<a name="configure-the-notification-hub-for-wns"></a>WNS configurer le concentrateur de notification

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####<a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configurer votre application Cordova pour prendre en charge les notifications de type Pousser de Windows

Ouvrez le Concepteur de la configuration (avec le bouton droit sur les fichiers config.xml et sélectionnez **Concepteur de vues**), sélectionnez l’onglet **Windows** et choisissez **Windows 10** sous **Windows Version de cible**.

>[AZURE.NOTE] Si vous utilisez une version de Cordova antérieure à Cordova 5.1.1 (6.1.1 recommandé), vous devez également définir l’indicateur Capable de Toast à true dans le fichier config.xml.

Pour prendre en charge la transmission notifications dans par défaut (debug) les builds, build.json Ouvrir fichier. Copiez la configuration de « release » à la configuration de débogage.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

Après la mise à jour, le code précédent doit se présenter comme suit.

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

Générer l’application et vérifiez que vous ne disposez d’aucune erreur. Application de client vous doit maintenant enregistrer pour les notifications à partir du serveur principal chargé de l’application Mobile. Répétez cette section pour chaque projet de Windows dans votre solution.

####<a name="test-push-notifications-in-your-windows-app"></a>Notifications de type Pousser de test dans votre application Windows

Dans Visual Studio, assurez-vous qu’une plate-forme Windows est sélectionnée comme cible de déploiement, tel que **Windows-x64** ou **Windows-x86**. Pour exécuter l’application sur un PC de 10 Windows hébergeant de Visual Studio, cliquez sur **Ordinateur Local**.

Appuyez sur le bouton Exécuter pour générer le projet et démarrer l’application.

Dans l’application, tapez un nom pour un nouveau todoitem, puis cliquez sur le signe plus (+) icône pour l’ajouter.

Vérifiez que réception d’une notification lorsque l’élément est ajouté.

##<a name="next-steps"></a>Étapes suivantes

* Découvrez [Les concentrateurs de Notification] pour en savoir plus sur les notifications de transmission.
* Si vous ne le n'avez pas déjà fait, continuer le didacticiel par [Ajout de l’authentification] pour votre application Apache Cordova.

Apprenez à utiliser les kits de développement logiciel.

* [Kit de développement logiciel Apache Cordova]
* [ASP.NET Server (SDK)]
* [Node.js Server (SDK)]

<!-- URLs -->
[Ajout de l’authentification]: app-service-mobile-cordova-get-started-users.md
[Démarrage rapide d’Apache Cordova]: app-service-mobile-cordova-get-started.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Compte Google]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Console de développeur de Google]: https://console.developers.google.com/home/dashboard
[documentation d’installation du plug-in PhoneGap-push]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Communauté 2015]: http://www.visualstudio.com/
[Visual Studio Tools pour Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Concentrateurs de notification]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Kit de développement logiciel Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server (SDK)]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server (SDK)]: app-service-mobile-node-backend-how-to-use-server-sdk.md
