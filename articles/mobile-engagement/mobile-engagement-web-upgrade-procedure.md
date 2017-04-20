<properties
    pageTitle="Les procédures de mise à niveau de kit de développement Web Mobile Engagement Azure | Microsoft Azure"
    description="Les dernières mises à jour et des procédures pour le Kit de développement Web pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Procédures de mise à niveau de kit de développement Web Mobile Engagement Azure

Si vous avez déjà intégré à une version antérieure du SDK Azure Mobile Engagement Web dans votre application web, vous devez considérer les points suivants lorsque vous mettez à niveau le Kit de développement.

Si vous avez ignoré plusieurs versions de l’Engagement de Mobile Web SDK, vous devrez peut-être effectuer plusieurs procédures pendant le processus de mise à niveau. Par exemple, si vous migrez à partir de 1.4.0 vers 1.6.0, suivre les procédures de mise à niveau à partir de 1.4.0 vers 1.5.0. Ensuite, suivez les procédures de mise à niveau à partir de 1.5.0 vers 1.6.0.

La version mise à niveau à partir, remplacer une version antérieure de l’azure-engagement.js de fichier avec la dernière version du fichier.

## <a name="upgrade-from-121-to-200"></a>Mise à niveau à partir de 1.2.1 à 2.0.0

Cette section décrit comment migrer une intégration du Kit de développement Web Mobile Engagement à partir du service de Capptain, offert par Capptain SAS, à une application Azure Mobile Engagement. Si vous migrez à partir d’une version antérieure, veuillez consulter le site Web de Capptain pour commencer par migrer vers 1.2.1 et ensuite appliquer les procédures suivantes.

Cette version de l’Engagement de Mobile Web SDK ne prennent en charge Samsung Smart TV, TV d’Opera, webOS ou la fonction de la portée.

>[AZURE.IMPORTANT] Capptain et Azure Mobile Engagement ne sont pas le même service. La procédure suivante met en évidence seulement la migration de l’application cliente. Le Kit de développement Web de mission Mobile dans l’application la migration ne migre pas vos données à partir d’un serveur Capptain vers un serveur Mobile Engagement.

### <a name="javascript-files"></a>Fichiers JavaScript

Remplacer le fichier capptain-sdk.js avec le fichier azure-engagement.js et mettez à jour votre script les importations en conséquence.

### <a name="remove-capptain-reach"></a>Supprimer la portée de Capptain

Cette version du Kit de développement Web de mission Mobile ne prend pas en charge la fonctionnalité de portée. Si vous avez intégré à portée de Capptain dans votre application, vous devez le supprimer.

Supprimer l’importation de CSS d’atteindre à partir de votre page et supprimer le fichier .css connexes (capptain-reach.css, par défaut).

Supprimer les ressources de portée suivantes : l’image fermer (capptain-close.png, par défaut) et l’icône de marque (capptain--icône de notification, par défaut).

Supprimer l’interface utilisateur d’atteindre pour les notifications dans l’application. La disposition par défaut ressemble à ceci :

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Supprimer l’interface utilisateur de joindre pour le texte et les annonces du web et les sondages. La disposition par défaut ressemble à ceci :

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Supprimer le `reach` objet de votre configuration, si elle existe. Il ressemble à ceci :

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Supprimez toute autre personnalisation de portée, comme les catégories.

### <a name="remove-deprecated-apis"></a>Supprimer API

Certaines API de Capptain est déconseillées dans le Kit de développement logiciel de Mobile Engagement Web.

Supprimez les appels à l’API suivantes : `agent.connect`, `agent.disconnect`, `agent.pause`, et `agent.sendMessageToDevice`.

Supprimer toutes les instances des rappels suivants de votre configuration de Capptain : `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, et `onPushMessageReceived`.

### <a name="configuration"></a>Configuration de

Engagement de Mobile utilise une chaîne de connexion pour configurer des identificateurs SDK, par exemple, l’identificateur d’application.

Remplacez le code de l’application avec votre chaîne de connexion. Notez que l’objet global pour la configuration du Kit de développement logiciel passe de `capptain` à `azureEngagement`.

Avant la migration :

    window.capptain = {
      appId: ...,
      [...]
    };

Après la migration :

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

La chaîne de connexion de votre application s’affiche dans le portail Azure.

### <a name="javascript-apis"></a>API JavaScript

L’objet JavaScript global `window.capptain` a été renommé `window.azureEngagement` , mais vous pouvez utiliser la `window.engagement` alias pour les appels API. Vous ne pouvez pas utiliser l’alias pour définir la configuration du Kit de développement logiciel.

Par exemple, `capptain.deviceId` devient `engagement.deviceId`, `capptain.agent.startActivity` devient `engagement.agent.startActivity`, et ainsi de suite.
