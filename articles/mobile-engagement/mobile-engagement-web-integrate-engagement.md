<properties
    pageTitle="Intégration de kit de développement Web Mobile Engagement Azure | Microsoft Azure"
    description="Les dernières mises à jour et des procédures pour le Kit de développement Web de mission Mobile Azure"
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
    ms.date="02/29/2016"
    ms.author="piyushjo" />

#<a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Intégrer Azure Mobile Engagement dans une application web

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md)
- [e/s](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Les procédures décrites dans cet article décrivent la façon la plus simple pour activer l’analytique et les fonctions de surveillance dans Azure Mobile Engagement dans votre application web.

Suivez les étapes pour activer les rapports de journal qui sont nécessaires pour calculer les statistiques sur les utilisateurs, les sessions, les activités, les incidents et les technicals. Les statistiques dépendent de l’application, tels que les événements, les erreurs et les tâches, vous devez activer manuellement les rapports du journal à l’aide de l’API d’Engagement de Mobile Azure. Pour plus d’informations, Découvrez [comment utiliser l’Engagement Mobile avancée balisage API dans une application web](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introduction

[Télécharger le Kit de développement logiciel du Web Azure Engagement Mobile](http://aka.ms/P7b453).
L’Engagement de Mobile Web SDK est livré sous la forme d’un seul fichier JavaScript, azure-engagement.js, vous devez inclure dans chaque page de votre site ou une application web.

> [AZURE.IMPORTANT] Avant d’exécuter ce script, vous devez exécuter un extrait de code de script ou de code que vous écrivez pour configurer Mobile Engagement de votre application.

## <a name="browser-compatibility"></a>Compatibilité du navigateur

Le Kit de développement Web de mission Mobile utilise JSON natif de codage et de décodage, en plus de demandes d’AJAX inter-domaines (reposant sur la spécification W3C CORS). Il est compatible avec les navigateurs suivants :

* Microsoft Edge 12 +
* Internet Explorer 10 +
* Firefox 3.5 +
* Chrome 4 +
* Safari 6 +
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Configurer l’Engagement Mobile

Écrire un script qui crée un global `azureEngagement` objet de JavaScript, comme dans l’exemple suivant. Étant donné que votre site peut avoir les pages multiples, cet exemple suppose que ce script est inclus dans chaque page. Dans cet exemple, l’objet JavaScript nommé `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

Le `connectionString` la valeur de votre application s’affiche dans le portail Azure.

> [AZURE.NOTE] `appVersionName`et `appVersionCode` sont facultatifs. Toutefois, nous vous recommandons de les configurer afin qu’analytique peut traiter les informations de version.

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Inclure des scripts de l’Engagement de Mobile dans vos pages
Ajouter des scripts de l’Engagement de Mobile à vos pages dans une des manières suivantes :

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Ou cela :

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias

Une fois le script du Kit de développement Web Mobile Engagement est chargé, il crée l’alias de **l’engagement** pour accéder à l’API du Kit de développement logiciel. Vous ne pouvez pas utiliser cet alias pour définir la configuration du Kit de développement logiciel. Cet alias est utilisé comme référence dans cette documentation.

Notez que si l’alias par défaut entre en conflit avec une autre variable globale à partir de votre page, vous pouvez redéfinir il dans la configuration comme suit avant de charger l’Engagement de Mobile Web SDK :

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Création de rapports de base

Reporting de base lors de l’Engagement Mobile couvre les statistiques au niveau de la session, telles que les statistiques sur les utilisateurs, les sessions, les activités et les incidents.

### <a name="session-tracking"></a>Suivi de session

Une session d’Engagement de Mobile est divisée en une séquence d’activités, chacune étant identifiée par un nom.

Dans un site Web classique, il est recommandé que vous déclarez une activité différente sur chaque page de votre site. Pour une site Web ou une application web dans laquelle la page en cours change jamais, vous pouvez souhaiter suivre les activités sur une plus petite échelle, par exemple dans la page.

Quoi qu’il en soit, pour démarrer ou modifier l’activité de l’utilisateur actuel, appelez la `engagement.agent.startActivity` fonction. Par exemple :

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

Le serveur Mobile Engagement termine automatiquement à une ouverture de session dans les trois minutes après la fermeture de la page d’application.

Sinon, vous pouvez terminer une session manuellement en appelant `engagement.agent.endActivity`. Cette valeur définit l’activité utilisateur en cours à « Inactif ».  La session va se terminer 10 secondes plus tard, sauf si un nouvel appel à `engagement.agent.startActivity` reprend la session.

Vous pouvez configurer le délai de 10 secondes de l’objet de l’engagement global, comme suit :

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] Vous ne pouvez pas utiliser `engagement.agent.endActivity` dans les `onunload` rappel, car vous ne pouvez pas faire les appels AJAX à ce stade.

## <a name="advanced-reporting"></a>Reporting avancé

Éventuellement, si vous souhaitez signaler des travaux, des erreurs et des événements d’application, vous devez utiliser l’API d’Engagement Mobile. Vous accédez à l’API d’Engagement Mobile par le biais de la `engagement.agent` objet.

Vous pouvez accéder à toutes les fonctionnalités avancées lors de l’Engagement dans l’API d’Engagement Mobile Mobile. L’API est détaillée dans l’article [comment utiliser l’Engagement Mobile avancée balisage API dans une application web](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Personnaliser les URL utilisées pour les appels AJAX

Vous pouvez personnaliser les URL que le Kit de développement logiciel de Mobile Engagement Web utilise. Par exemple, pour redéfinir l’URL de connexion (point de terminaison du Kit de développement logiciel pour l’enregistrement), vous pouvez substituer la configuration comme suit :

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Si votre URL renvoient une chaîne qui commence par `/`, `//`, `http://`, ou `https://`, le modèle par défaut n’est pas utilisé. Par défaut, le `https://` schéma est utilisé pour les URL. Si vous souhaitez personnaliser le schéma par défaut, remplacer la configuration, comme suit :

    window.azureEngagement = {
      ...
      urls: {
        ...      
        scheme: '//'
      }
    };
