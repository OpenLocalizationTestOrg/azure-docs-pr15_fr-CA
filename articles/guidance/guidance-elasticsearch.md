
<properties
   pageTitle="Elasticsearch sur les conseils Azure | Microsoft Azure"
   description="Elasticsearch sur les conseils d’Azure."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>

# <a name="elasticsearch-on-azure-guidance"></a>Elasticsearch sur les conseils d’Azure 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Elasticsearch est un moteur de recherche d’open source hautement évolutive et une base de données. Il est utile dans les situations qui nécessitent une analyse rapide et la détection des informations contenues dans des groupes de données volumineux. Ce guide examine certains aspects essentiels à prendre en compte lors de la création d’un cluster de Elasticsearch, en mettant l’accent sur les manières d’optimiser et de tester votre configuration.

> [AZURE.NOTE]Ce guide suppose une connaissance de base avec Elasticsearch. Pour plus d’informations, visitez le [site Web de Elasticsearch](https://www.elastic.co/products/elasticsearch). 

- **[Exécution de Elasticsearch sur Azure][]** fournit une brève présentation de la structure générale de Elasticsearch et explique comment implémenter un cluster Elasticsearch à l’aide d’Azure. 
- **[Performances de réception de données de réglage pour Elasticsearch sur Azure][]** décrit le déploiement d’un cluster d’Elasticsearch et fournit une analyse approfondie des différentes options de configuration à prendre en compte lorsque vous attendez un fort taux de réception des données.
- **[Agrégation des données de réglage et les performances des requêtes pour Elasticsearch sur Azure][]** fournit une analyse détaillée des options à prendre en compte lorsque vous décidez comment optimiser votre système pour des performances de requête et de recherche.
- **[Configuration de résilience et récupération sur Elasticsearch sur Azure][]** décrit quelques-unes des fonctionnalités importantes d’un cluster d’Elasticsearch qui peut aider à réduire les risques de perte de données et de temps de récupération de données étendues.
- **[Création d’un environnement de test de performances de Elasticsearch sur Azure][]** décrit comment configurer un environnement de test de la réception des données de performances et les charges de travail de requête dans un cluster de Elasticsearch. 
- **[Mise en œuvre d’un JMeter de tester le plan de Elasticsearch][]** résume implémentées à l’aide de plans de test de JMeter avec le code Java incorporée en tant que JUnit test pour effectuer des tâches telles que le téléchargement de données dans le cluster Elasticsearch les tests de performances en cours d’exécution.
- **[Déploiement d’un échantillon de JMeter JUnit pour le test de performances de Elasticsearch][]** décrit comment créer et utiliser un échantillon de JUnit qui peut générer et télécharger des données sur un cluster Elasticsearch. Cette approche très souple pour le test de charge qui peuvent générer de grandes quantités de données de test sans en fonction des fichiers de données externes. 
- **[Exécution des tests de résistance Elasticsearch automatisées][]** résume comment exécuter les tests de résistance utilisés dans cette série. 
- **[Exécution des tests de performances Elasticsearch automatiques][]** résume comment exécuter les tests de performances utilisés dans cette série.


[Elasticsearch en cours d’exécution sur Azure]: guidance-elasticsearch-running-on-azure.md
[Réglage des performances d’acquisition de données pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Création d’un environnement de test pour Elasticsearch sur Azure de performances]: guidance-elasticsearch-creating-performance-testing-environment.md
[Mise en œuvre d’un Plan de Test de JMeter pour Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Déploiement d’un échantillon de JMeter JUnit pour le test de performances de Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Réglage d’agrégation des données et des performances des requêtes pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configuration de résilience et récupération sur Elasticsearch sur Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Exécution des Tests de résistance Elasticsearch automatique]: guidance-elasticsearch-running-automated-resilience-tests.md
[Exécutez les Tests de performances de Elasticsearch automatique]: guidance-elasticsearch-running-automated-performance-tests.md
