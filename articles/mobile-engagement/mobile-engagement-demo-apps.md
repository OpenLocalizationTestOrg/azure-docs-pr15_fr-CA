<properties
    pageTitle="App de démo Mobile Engagement Azure | Microsoft Azure"
    description="Décrit l’emplacement de téléchargement, l’utilisation et les avantages de l’utilisation d’app de démo Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/10/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-demo-app"></a>App de démo Mobile Engagement Azure

Nous avons publié une application de démonstration Azure Mobile Engagement pour **iOS**, **Android**et les plates-formes **Windows** pour vous aider à trouver des ressources utiles et en savoir plus sur l’Engagement de Mobile.

L’application vous aide à :

- Trouvez facilement des liens utiles vers les ressources Mobile Engagement comme des vidéos, la documentation, le forum de support et où aller pour déclencher des demandes de fonctionnalités.
- Notifications d’exemple expérience qui sont pris en charge par l’Engagement Mobile vous donner des idées pour vos propres applications mobiles.
- Utilisez une implémentation de référence à étudier l’implémentation de Mobile Engagement dans votre application. Vous pouvez apprendre à :

    - Collecter des données d’analytique.
    - Implémenter des scénarios de notification avancée de types *plein écran INTERSTITIEL* ou *pop-up*.
    - Mettre en œuvre des enquêtes et sondages.
    - Mettre en œuvre des scénarios de données et de diffusion envoyé en mode silencieux.   

## <a name="app-installation"></a>Installation de l’application
Cette application est disponible dans les magasins d’applications suivantes :

- **App de démo universel de Windows**:

    - Télécharger l’application au niveau de l' [application Windows stocker](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
    - L’application a été développée sous la forme d’une application Windows 10 universel. Le code source est disponible sur [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).

- **application de démonstration des e/s**:

    - Télécharger l’application aux [stocker d’Apple](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
    - L’application a été développée dans iOS Swift. Le code source est disponible sur [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).

- **Application de démonstration android**:

    - Télécharger l’application à la [lecture de Google stocker](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
    - Le code source est disponible sur [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![App de démo Windows universel][1]

![application de démonstration des e/s][2]
![app démo Android][3]


## <a name="usage"></a>Utilisation de

Vous pouvez utiliser cette application de différentes façons :

**Téléchargez l’application sur le périphérique à partir de l’application magasin est liée (fournies plus haut) :**

>[AZURE.IMPORTANT] Vous n’avez pas besoin un compte Azure ou le besoin de connecter l’application à un back-end. L’application fonctionne de manière indépendante.

- Une fois que l’application sur votre périphérique, vous pouvez accéder via les liens dans le menu de gauche pour trouver les ressources utiles à propos de l’Engagement de Mobile.
- Nous avons ajouté le [flux RSS de service](https://aka.ms/azmerssfeed) dans cette application afin que vous soyez toujours mis à jour sur les dernières mises à jour de produit.
- Vous pouvez également faire défiler les exemples de scénarios de notification pour découvrir le type de notifications qui sont pris en charge par Mobile Engagement pour chaque plate-forme. Ces notifications peuvent être rencontrées localement--qui est, vous pouvez cliquer sur les boutons sur les écrans pour vous montrer l’expérience des notifications, qui est identique à l’envoi de notifications à partir de la plate-forme Mobile Engagement.

![Menu d’application pour Windows][4]

![Menu d’application pour les e/s][5]
![menu App pour Android][6]

**Télécharger le code source à partir de liens GitHub (fournis plus haut) :**

- Une fois que vous avez téléchargé le code source, l’ouvrir dans l’environnement de développement respectifs de XCode pour iOS, Android Studio pour Android et Visual Studio pour Windows.
- Vous devez ensuite suivre notre [base étapes d’intégration de kit de développement logiciel](mobile-engagement-windows-store-dotnet-get-started.md) pour que vous soyez en mesure de connecter cette application à sa propre instance de back-end Mobile Engagement.
    - Vous devez configurer une chaîne de connexion dans l’application.
    - Vous devez également configurer la plate-forme notification pour votre application.
- Vous remarquerez que l’application elle-même est instrumentée avec Engagement de Mobile. Par conséquent, lorsque vous ouvrez l’application après la connexion au serveur principal, vous serez en mesure de voir la session de l’utilisateur, activités, événements et ainsi de suite, dans l’onglet **Moniteur** .
- Vous serez également en mesure d’envoyer des notifications à cette application à partir de votre propre instance Mobile Engagement, au lieu d’utiliser des notifications locales.
    - Ici, vous pouvez ajouter votre périphérique comme un périphérique de test à l’aide de l’élément de menu **obtenir l’ID de périphérique** dans l’application. Cela vous donne un ID de périphérique que vous enregistrez ensuite sous la forme d’un dispositif de test à votre instance de back-end de plate-forme.

    ![ID de périphérique sous Windows][7]

    ![ID de périphérique sur iOS][8]
    ![ID de périphérique sur Android][9]

## <a name="key-features-of-the-demo-app"></a>Principales fonctionnalités de l’application de démonstration

- Comme mentionné précédemment, avec cette application, vous avez toutes les ressources clés pour mission de Mobile dans votre main. Vous pouvez faire défiler les liens dans le menu de gauche.

- Vous pouvez rencontrer des notifications out-de-app pour chaque plate-forme. Ces notifications peuvent être remises en tant que **Notification seulement**, où cliquer sur la notification ouvre simplement un écran natif de l’application (en utilisant le **lien profond**)--ou une **annonce de Web**, où vous pouvez fournir un contenu HTML supplémentaire à partir de l’Engagement de Mobile back-end à afficher lorsqu’un clic est effectué sur la notification.

    ![Notifications de hors-de-app][29]

- Dans iOS, vous devez fermer l’application pour afficher les notifications de type Pousser out-de-app ou système. Vous pouvez consulter la mise en oeuvre ici pour ajouter des **boutons d’Action**, tels que ceux qui est ajoutés à cette notification hors-de-app de *commentaires* et de *partage* (de sorte que l’utilisateur peut prendre le droit d’action à partir de la notification elle-même).

    ![Notifications de hors-de-app sur iOS][11] ![Affichage de notification de hors-de-app sur iOS][14]

- Sur Android, les options qui sont prises en charge sont ajoute un texte multiligne (**Texte volumineux**) ou une notification (**Grande image**) pour la notification, ainsi que les **boutons d’Action** (pris en charge par les e/s).

    ![Notifications de hors-de-app sur Android][12] ![Affichage de notification de hors-de-app sur Android][15]

- Sur Windows 10, vous pouvez voir l’aspect des notifications sur le PC. Cette notification apparaît également dans le **Centre de Notification**de Windows 10. Il n’y a aucune prise en charge pour l’ajout de **boutons d’Action** pour le moment dans le SDK Windows.

    ![Notifications de hors-de-app sous Windows][10] ![Affichage de la sortie-de-app sous Windows][13]

- Vous pouvez rencontrer des notifications de « dans l’application » par défaut pour chaque plate-forme. Il s’agit d’une expérience en deux étapes où une fenêtre de **Notification** est affichée en premier. Lorsque vous cliquez dessus, elle ouvre un plein écran **annonce**, tel qu’affiché dans la capture d’écran suivante. Le contenu de cette annonce vient de votre instance de back-end Mobile Engagement. Le SDK contient les modèles pour les notifications et les annonces. Vous pouvez facilement les personnaliser, comme indiqué dans cette application de démonstration avec l’ajout de notre logo et de coloration.  

    ![Notifications dans l’application sous Windows][16]

    ![Notifications dans l’application sur iOS][17]  ![Notifications dans l’application sur Android][18]

    **iOS**, **Android**

- Vous pouvez également utiliser la fonction de **catégorie** de l’Engagement Mobile pour personnaliser l’expérience par défaut. Dans l’application de démonstration, nous avons démontré pour modifier l’expérience des notifications de deux façons. Notez que la fonction de la catégorie n’est pas encore reconnue dans le SDK Windows.

    **Plein écran INTERSTITIELLE :**

    ![Dans l’application notification - catégorie interstitiel][30]

    ![Catégorie INTERSTITIELLE sur iOS][21]  ![Catégorie INTERSTITIELLE sur Android][22]

    **Notification de fenêtres pop-up :**

    ![Dans l’application notification - catégorie de fenêtres pop-up][31]

    ![Notification de fenêtres pop-up sur iOS][19]   ![Notification de fenêtres pop-up sur Android][20]

**iOS**, **Android**

- Engagement de Mobile prend également en charge un type spécialisé de notification dans l’application appelée **sondages**. Cela vous permet d’envoyer des enquêtes rapides à vos utilisateurs app segmenté. Vous pouvez ajouter des questions et des options pour chaque question, comme dans la capture d’écran suivante. Ce sera ensuite s’affichent sous la forme d’une notification dans l’application à l’utilisateur de l’application.   

    ![Notifications de sondage][32]

    ![Enquête sur Windows][26]

    ![Enquête sur les e/s][27]   ![Enquête sur Android][28]

**iOS**, **Android**

- Engagement de Mobile prend également en charge l’envoi de notifications de **Données Push** en mode silencieux. Avec ces notifications, vous pouvez envoyer des données à partir de votre service (comme les données JSON dans l’exemple suivant), que vous pouvez gérer dans votre application et prendre des mesures. C’est un exemple comment nous passons le prix d’un article sélective à l’aide de notifications de transmission de données.

    ![Notification de transmission de données][33]

    ![Notification de transmission de données sous Windows][23]

    ![Notification de transmission de données sur iOS][24]  ![Notification de transmission de données sur Android][25]

**iOS**, **Android**

> [AZURE.NOTE] Vous pouvez afficher des instructions détaillées étape par étape pour une de ces notifications en cliquant sur **Cliquez ici pour obtenir des instructions sur l’envoi de ces notifications à partir de la plate-forme de l’Engagement de Mobile** sur n’importe quel écran de notification d’exemple.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
