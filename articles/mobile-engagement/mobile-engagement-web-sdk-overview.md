<properties
    pageTitle="Vue d’ensemble du Kit de développement logiciel Azure Engagement Mobile Web | Microsoft Azure"
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
    ms.date="10/18/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk"></a>Azure Engagement Mobile Web SDK

Commencez ici pour tous les détails sur la façon d’intégrer Azure Mobile Engagement dans une application web. Si vous souhaitez essayer avant la mise en route avec votre application web, consultez notre [didacticiel de 15 minutes](mobile-engagement-web-app-get-started.md).

## <a name="integration-procedures"></a>Procédures d’intégration
1. Découvrez [comment intégrer l’Engagement Mobile dans votre application web](mobile-engagement-web-integrate-engagement.md).

2. Pour la mise en oeuvre du plan de balise, Découvrez [comment utiliser l’Engagement Mobile avancée balisage API dans votre application web](mobile-engagement-web-use-engagement-api.md).

## <a name="release-notes"></a>Notes de publication

### <a name="202-10182016"></a>2.0.2 (18/10/2016)

-   Fixe d’incidents de navigation privée (Safari).
-   Fixe d’incident sur des navigateurs avec les cookies désactivés.

Pour toutes les versions, veuillez consulter les [notes de version complète](mobile-engagement-web-release-notes.md).

## <a name="upgrade-procedures"></a>Procédures de mise à niveau

### <a name="upgrade-from-121-to-200"></a>Mise à niveau à partir de 1.2.1 à 2.0.0

Les sections suivantes décrivent comment migrer une intégration du Kit de développement Web Mobile Engagement à partir du service de Capptain, offert par Capptain SAS, à une application Azure Mobile Engagement. Si vous migrez à partir d’une version antérieure à 1.2.1, veuillez consulter le site Web de Capptain pour effectuer une migration vers 1.2.1 tout d’abord et ensuite appliquer les procédures suivantes.

Cette version de l’Engagement de Mobile Web SDK ne prennent en charge Samsung Smart TV, TV d’Opera, webOS ou la fonction de la portée.

>[AZURE.IMPORTANT] Capptain et Azure Mobile Engagement ne sont pas le même service, et les procédures suivantes en surbrillance uniquement la migration de l’application cliente. Le Kit de développement Web de mission Mobile dans l’application la migration ne migre pas vos données à partir d’un serveur Capptain vers un serveur Mobile Engagement.

#### <a name="javascript-files"></a>Fichiers JavaScript

Remplacer le fichier capptain-sdk.js avec le fichier azure-engagement.js et mettez à jour votre script les importations en conséquence.

#### <a name="remove-capptain-reach"></a>Supprimer la portée de Capptain

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

#### <a name="remove-deprecated-apis"></a>Supprimer API

Certaines API de Capptain est déconseillées dans le Kit de développement logiciel de Mobile Engagement Web.

Supprimez les appels à l’API suivantes : `agent.connect`, `agent.disconnect`, `agent.pause`, et `agent.sendMessageToDevice`.

Supprimer les rappels suivants de votre configuration de Capptain : `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, et `onPushMessageReceived`.

#### <a name="configuration"></a>Configuration de

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

#### <a name="javascript-apis"></a>API JavaScript

L’objet JavaScript global `window.capptain` a été renommé `window.azureEngagement`, mais vous pouvez utiliser la `window.engagement` alias pour les appels API. Vous ne pouvez pas utiliser cet alias pour définir la configuration du Kit de développement logiciel.

Par exemple, `capptain.deviceId` devient `engagement.deviceId`, `capptain.agent.startActivity` devient `engagement.agent.startActivity`, et ainsi de suite.

Si vous avez déjà intégré à une version antérieure du SDK Azure Mobile Engagement Web dans votre application, lisez sur [les procédures de mise à niveau](mobile-engagement-web-upgrade-procedure.md).
