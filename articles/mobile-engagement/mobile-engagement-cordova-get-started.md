<properties
    pageTitle="Mise en route de la mission de Mobile Azure pour Cordova/Phonegap"
    description="Découvrez comment utiliser Azure Mobile Engagement avec Analytique et d’envoyer des Notifications pour les applications de Cordova/Phonegap."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="js"
    ms.topic="hero-article" 
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Mise en route de la mission de Mobile Azure pour Cordova/Phonegap

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous indique comment Azure Mobile Engagement permet de comprendre l’utilisation de votre application et d’envoyer des notifications de type Pousser aux utilisateurs segmentés pour une application mobile développé avec Cordova.

Dans ce didacticiel, nous va créer une application vide de Cordova à l’aide de Mac, puis intégrer le Kit de développement Mobile Engagement. Il collecte les données de base analytique et reçoit des notifications de type pousser à l’aide d’Apple Push Notification système (APNS) pour les e/s et de messagerie de nuage Google (GCM) pour Android. Nous déploierons cette à une e/s ou d’un appareil Android pour le test. 

> [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).

Ce didacticiel requiert les éléments suivants :

+ XCode, que vous pouvez installer à partir de Mac App Store (pour le déploiement d’e/s)
+ [Émulateur & SDK android](http://developer.android.com/sdk/installing/index.html) (pour le déploiement pour Android)
+ Certificat de notification push (.p12) que vous pouvez obtenir à partir du centre de développement d’Apple pour APNS
+ Numéro de projet de GCM que vous pouvez obtenir à partir de votre Console de développement Google pour GCM
+ [Plug-in d’Engagement mobile Cordova](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE] Vous pouvez trouver le code source et le fichier Lisezmoi pour le plug-in de Cordova sur [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Le programme d’installation de Mobile Engagement pour votre application Cordova

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connexion de votre application sur le serveur principal d’Engagement de Mobile

Ce didacticiel présente une « intégration de base » qui est le minimum requis pour collecter des données et d’envoyer une notification de transmission. 

Nous allons créer une application de base avec Cordova pour illustrer l’intégration :

###<a name="create-a-new-cordova-project"></a>Créez un nouveau projet Cordova

1. Lancer la fenêtre de *Terminal Server* sur votre ordinateur Mac et tapez le texte suivant, qui crée un nouveau projet Cordova à partir du modèle par défaut. Assurez-vous que le profil de publication que vous utilisez finalement pour déployer votre application iOS utilise « com.mycompany.myapp » comme application ID. 

        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova

2. Exécutez la commande suivante pour configurer votre projet pour **iOS** et l’exécuter dans le simulateur d’iOS :

        $ cordova platform add ios 
        $ cordova run ios

3. Exécutez la commande suivante pour configurer votre projet pour **Android** et l’exécuter dans l’émulateur Android. Assurez-vous que vos paramètres de l’émulateur de SDK Android ont sa cible en tant que APIs de Google (Google Inc.) avec le processeur / ABI comme Google APIs ARM.  

        $ cordova platform add android
        $ cordova run android

4.  Ajouter le plug-in de la Console de Cordova. 

        $ cordova plugin add cordova-plugin-console 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Votre application de se connecter au back-end de l’Engagement de Mobile

1. Installer le plug-in de Cordova d’Engagement Azure Mobile tout en fournissant des valeurs de la variable pour configurer le plug-in :

        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
            --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Icône d’atteindre Android* : doit être le nom de la ressource sans toute extension, ni préfixe drawable (ex : mynotificationicon), et le fichier icône doit être copié dans votre projet android (plates-formes/android/res/drawable)

*iOS atteindre une icône* : doit être le nom de la ressource avec l’extension (ex : mynotificationicon.png), et le fichier icône doit être ajouté dans votre projet d’e/s avec XCode (en utilisant le Menu ajouter de fichiers)

##<a id="monitor"></a>Activation de l’analyse en temps réel

1. Dans le projet Cordova - modifier la **www/js/index.js** pour ajouter l’appel à Engagement Mobile pour déclarer une nouvelle activité une fois l’événement *deviceReady* est reçu.

         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }

2. Exécutez l’application :
        
    - **Pour iOS**
    
        Dans `Terminal` fenêtre lancer votre application dans une nouvelle instance de simulateur en exécutant le code suivant :

            cordova run ios

    - **Pour Android**
        
        Dans `Terminal` fenêtre lancer votre application dans une nouvelle instance de l’émulateur en exécutant le code suivant :

            cordova run android

3. Vous pouvez voir les éléments suivants dans les journaux de la console :

        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>Application de se connecter grâce à une surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>L’activation de pousser des Notifications et dans l’application de messagerie

Engagement de Mobile vous permet d’interagir avec vos utilisateurs à l’aide de pousser les Notifications et les échanges dans le cadre de campagnes dans l’application. Ce module est appelé la portée dans le portail Mobile Engagement.
Les sections suivantes va configurer votre application pour les recevoir.

###<a name="configure-push-credentials-for-mobile-engagement"></a>Configurer les informations d’identification de l’émetteur pour l’Engagement de Mobile

Pour permettre l’Engagement de Mobile envoyer des Notifications de pousser à votre place, vous devez lui accorder l’accès à votre certificat de Apple iOS ou la clé de l’API serveur GCM. 
    
1. Accédez à votre portail Mobile Engagement. Assurez-vous que vous êtes dans l’application, nous sommes à l’aide de ce projet et puis cliquez sur le bouton de **l’engagement** en bas :
    
    ![][1]
    
2. Vous « atterrit » dans la page Paramètres dans votre portail de projet. À partir de là, cliquez sur la section **Push natif** :
    
    ![][2]

3. Configurer des e/s de clé de l’API serveur de certificat/GCM

    **[e]**

    une barre d’outils. Sélectionnez votre .p12, téléchargez-le et tapez votre mot de passe :
    
    ![][3]

    **[Android]**

    une barre d’outils. Cliquez sur l’icône Modifier en regard de la **Clé de l’API** dans la section Paramètres de GCM et dans le menu contextuel qui s’affiche, collez la clé GCM du serveur et cliquez sur **OK**. 
        
    ![][4]

###<a name="enable-push-notifications-in-the-cordova-app"></a>Activer les notifications de type Pousser dans l’application de Cordova

Modifiez **www/js/index.js** pour ajouter l’appel à Engagement Mobile pour demander des notifications de transmission et de déclarer un gestionnaire :

     onDeviceReady: function() {
           Engagement.initializeReach(  
                // on OpenUrl  
                function(_url) {   
                alert(_url);   
                });  
            Engagement.startActivity("myPage",{});  
        }

###<a name="run-the-app"></a>Exécuter l’application

**[e]**

1. Nous allons utiliser XCode pour générer et déployer l’application sur le périphérique pour tester les notifications de type Pousser depuis l’iOS n’autorise que les notifications de type pousser à une unité réelle. Accédez à l’emplacement dans lequel votre projet Cordova est créé et accédez à l’emplacement de **...\platforms\ios** . Ouvrez le fichier .xcodeproj natif dans XCode. 
    
2. Générer et déployer l’application de Cordova sur le périphérique d’e/s en utilisant le compte qui possède le profil mise en service qui contient le certificat que vous venez téléchargé vers le portail Mobile Engagement et de l’Id de l’application qui correspond à celle vous avez fourni lors de la création de l’application Cordova. Vous pouvez extraire l' *identificateur de groupe* de votre **ressources\*-info.plist** fichier dans XCode pour faire correspondre. 

3. Vous verrez la fenêtre contextuelle d’e/s standard sur votre appareil, indiquant que l’application demande l’autorisation d’envoyer des notifications. Accorder l’autorisation. 

**[Android]**

Vous pouvez simplement utiliser l’émulateur pour exécuter l’application Android notifications de GCM sont pris en charge sur l’émulateur Android. 

    cordova run android

##<a id="send"></a>Envoyer une notification à votre application

Nous allons maintenant créer une campagne de Notification de transmission simple qui envoie une transmission à votre application en cours d’exécution sur le périphérique :

1. Accédez à l’onglet **Atteindre** dans votre portail Mobile Engagement

2. Cliquez sur **Une nouvelle annonce** pour créer votre campagne de push

    ![][6]

3. Fournir des entrées pour créer votre campagne **[Android]**
    
    - Fournir un **nom** pour votre campagne. 
    - Sélectionnez le **Type de livraison** de *notification système* *Simple*
    - Sélectionnez le **délai de livraison** en tant que *« n’importe quel temps »*
    - Fournir un **titre** pour votre notification qui sera la première ligne de la commande.
    - Fournir un **Message** de votre notification qui sert de corps du message. 

    ![][11]

4. Fournir des entrées pour créer votre campagne **[e]**

    - Fournir un **nom** pour votre campagne. 
    - Sélectionnez le **délai de livraison** en tant que *« de l’application »*
    - Fournir un **titre** pour votre notification qui sera la première ligne de la commande.
    - Fournir un **Message** de votre notification qui sert de corps du message. 
 
    ![][12]

5. Défilement vers le bas et, dans la section contenu Sélectionnez **Notification uniquement**

    ![][8]

6. [Facultatif] Vous pouvez également fournir des URL d’Action. Assurez-vous qu’il utilise un schéma d’URL fourni lors de la configuration du plug-in **AZME\_redirection\_URL** variable, par exemple *myapp://test*.  

7. Vous avez terminé définir la campagne la plus basique possible. Maintenant, faites défiler à nouveau et cliquez sur le bouton **créer** pour enregistrer votre campagne.

8. Enfin **Activer** votre campagne
    
    ![][10]

9. Vous devez maintenant voir une notification de transmission sur votre périphérique ou l’émulateur dans le cadre de cette campagne. 

##<a id="next-steps"></a>Étapes suivantes
[Vue d’ensemble de toutes les méthodes disponibles avec le Kit de développement logiciel Cordova Mobile Engagement](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

