<properties 
    pageTitle="Analytique - le puissant outil de recherche d’idées d’Application | Microsoft Azure" 
    description="Vue d’ensemble d’Analytique, l’outil de recherche de diagnostic puissant de perspectives de l’Application. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/26/2016" 
    ms.author="awills"/>


# <a name="analytics-in-application-insights"></a>Analytique dans les perspectives de l’Application


[Analytique](app-insights-analytics.md) est une fonctionnalité puissante de recherche [d’Idées d’Application](app-insights-overview.md). Ces pages décrivent le lanquage de requête Analytique. 

* **[Visionnez la vidéo d’introduction](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Lecteur de test Analytique sur nos données simulées](https://analytics.applicationinsights.io/demo)** si votre application n’est pas envoyer des données aux analyses d’Application encore.

## <a name="queries-in-analytics"></a>Requêtes dans Analytique
 
Une requête typique est une table de *source de* suivi d’une série d' *opérateurs* séparés par `|`. 

Par exemple, nous allons savoir à quelle heure de la journée, les citoyens de Hyderabad Essayez notre application web. Et alors que nous sommes présents, nous allons voir les codes de résultats sont renvoyés à leurs demandes HTTP. 

```AIQL

    requests      // Table of events that log HTTP requests.
  	| where timestamp > ago(7d) and client_City == "Hyderabad"
  	| summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
  	| extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Nous compter les adresses IP de client distinct, en les groupant par l’heure de la journée au cours des 7 derniers jours. 

Nous allons afficher les résultats avec la présentation du graphique à barres, choisissez Empiler les résultats à partir des codes de réponse différente :

![Sélectionnez le graphique à barres, les x et les axes des ordonnées, puis segmentation](./media/app-insights-analytics/020.png)

L’apparence de notre application est plus populaires à déjeuner et lit-heure à Hyderabad. (Et nous devons étudier ces 500 codes).


Il existe également des opérations statistiques puissantes :

![](./media/app-insights-analytics/025.png)


Le langage possède de nombreuses fonctionnalités intéressantes :

* [Filtre](app-insights-analytics-reference.md#where-operator) votre télémétrie app brut par tous les champs, y compris vos propriétés personnalisées et les mesures.
* [Joindre](app-insights-analytics-reference.md#join-operator) plusieurs tables – demandes de mettre en corrélation avec les vues de page, les appels de dépendance, exceptions et journal des traces.
* Statistiques [des agrégations](app-insights-analytics-reference.md#aggregations)puissant.
* Aussi puissant que SQL, mais il est beaucoup plus facile pour les requêtes complexes : au lieu d’imbrication d’instructions, vous canaliser les données à partir d’une seule opération élémentaire à l’autre.
* Visualisation immédiate et puissante.







## <a name="connect-to-your-application-insights-data"></a>Se connecter à vos données d’idées d’Application


Ouvrez Analytique à partir de votre application [lame de vue d’ensemble](app-insights-dashboards.md) dans les perspectives de l’Application : 

![Ouvrez portal.azure.com, ouvrir votre ressource de perspectives de l’Application et cliquez sur Analytique.](./media/app-insights-analytics/001.png)


## <a name="limits"></a>Limites

À l’heure actuelle, les résultats de la requête sont limités à juste plus d’une semaine de données anciennes.



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


## <a name="next-steps"></a>Étapes suivantes


* Nous vous recommandons de que commencer avec la [visite guidée du langage](app-insights-analytics-tour.md).