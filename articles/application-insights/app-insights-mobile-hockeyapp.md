<properties
    pageTitle="Analyse des performances pour les applications web mobiles avec Analytique de développeur | Microsoft Azure"
    description="Performances de l’application et l’analyse de l’utilisation pour les développeurs d’applications mobiles. , bureau, service web et les applications back-end avec des HockeyApp et des perspectives de l’Application."
    authors="alancameronwills"
    services="application-insights"
    documentationCenter=""
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="awills"/>

# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Analytique mobile avec HockeyApp et les perspectives d’Application

Surveiller les performances et l’utilisation des bêta-test et déployé des applications mobiles et de bureau avec [HockeyApp](https://hockeyapp.net/). Surveiller les applications de service et de back-end de web prise en charge avec les [Perspectives d’Application Visual Studio](app-insights-overview.md). Analyser les données à partir d’applications à la fois le client et le serveur Analytique et afficher les graphiques à côté de l’autre dans un tableau de bord Azure.

Analytique de développeur Microsoft propose deux solutions pour l’analyse des performances d’application :

* Applications **HockeyApp pour mobile** et de bureau.
 * Distribuer des versions de test aux utilisateurs sélectionnés.
 * Analyse des incidents.
 * Télémétrie personnalisé pour l’analyse de l’utilisation.
* Applications **d’Idées d’application pour les sites web** et les services back-end.
 * Les métriques de performances et d’utilisation et les alertes.
 * Rapports d’exceptions et de suivi de diagnostic.
 * Recherche de diagnostic avec filtrage des associés et des liens de télémétrie.

Les deux solutions s’offrent :

 * **[Langage de requête analytique](app-insights-analytics.md)** puissant pour l’analyse et de diagnostic.
 * **[Exporter des données](app-insights-export-telemetry.md)** à votre propre système de stockage.
 * Affichage du **tableau de bord intégré** des tables et des graphiques analytiques.

## <a name="monitor-your-app-components"></a>Surveiller vos composants d’application

Suivez les instructions fournies dans ces pages pour installer le Kit de développement logiciel dans votre code et de commencer le suivi de votre application.

### <a name="web-apps---application-insights"></a>Applications - aperçu de l’Application Web

* [ASP.NET web app](app-insights-asp-net.md) 
* [Application web de Java](app-insights-java-get-started.md)
* [Node.js web app](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Services en nuage Azure](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>Applications mobiles - HockeyApp

* [application iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Mac OS X app](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Application d’Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Application Windows universel](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Windows Phone 8 et 8.1 app](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation application](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Pour les applications de bureau Windows, nous vous recommandons de HockeyApp. Mais vous pouvez également [Envoyer des données télémétriques fournies par une application Windows pour les perspectives de l’Application](app-insights-windows-desktop.md). Vous pouvez souhaiter pour expérimenter les perspectives de l’Application.


## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Analytique et l’exportation de télémétrie de HockeyApp

Vous pouvez étudier HockeyApp personnalisé et connecter l’utilisation des fonctionnalités Analytique et exporter Continuous de perspectives de l’Application par la [configuration d’un pont](app-insights-hockeyapp-bridge-app.md)de télémétrie.




