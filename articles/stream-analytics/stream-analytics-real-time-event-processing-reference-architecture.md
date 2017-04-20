<properties 
    pageTitle="Traitement avec le traitement des événements Analytique du flux d’événements en temps réel | Microsoft Azure" 
    description="Découvrez l’interopérabilité d’un ensemble de services Azure d’activation analytique et traitement des événements en temps réel." 
    keywords="traitement en temps réel, le traitement de l’événement, architecture de référence"
    services="stream-analytics,event-hubs,storage,sql-database" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="stream-analytics" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architecture de référence : traitement avec Microsoft Azure flux Analytique des événements en temps réel

L’architecture de référence pour les événements en temps réel en traitement Analytique de flux Azure vise à fournir un plan générique pour le déploiement d’une plate-forme en temps réel en tant que service (PaaS) traitement des flux solution avec Microsoft Azure.

## <a name="summary"></a>Résumé

Traditionnellement, les solutions d’analytique ont été basées sur des fonctionnalités telles que ETL (extract, transform, load) et entreposage de données, où sont stockées les données avant l’analyse. Évolution des besoins, y compris les données plus rapidement arriver, poussez ce modèle existant à la limite. La capacité à analyser des données au sein du déplacement du flux de données avant le stockage est une solution, et s’il n’est pas une nouvelle fonctionnalité, l’approche n’a pas été largement adoptée dans tous les secteurs de l’industrie. 

Microsoft Azure fournit un catalogue complet de technologies analytique qui sont capables de prendre en charge d’un tableau de différents scénarios de solution et les exigences. Sélectionner les services Azure pour déployer une solution de bout en bout peut être difficile étant donné la diversité des offres. Ce document est conçu pour décrire les fonctionnalités et l’interopérabilité des différents services Azure qui prennent en charge d’une solution de flux d’événements. Il décrit également certains des scénarios dans lesquels les clients bénéficient de ce type d’approche.

## <a name="contents"></a>Contenu

- Rapport de synthèse
- Présentation Analytique en temps réel
- Proposition de valeur de données en temps réel dans Azure
- Scénarios courants d’Analytique en temps réel
- Architecture et composants
    - Sources de données
    - Couche d’intégration des données
    - Couche d’Analytique en temps réel
    - Couche de stockage de données
    - Présentation / couche de consommation
- Conclusion

**Auteur :** Centre de conseils de données Charles Feddersen, architecte de la Solution, d’Excellence, Microsoft Corporation

**Publié :** Janvier 2015

**Révision :** 1.0

**Télécharger :** [Traitement avec Microsoft Azure flux Analytique des événements en temps réel](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)

 
