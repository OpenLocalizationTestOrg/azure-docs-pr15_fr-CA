<properties
   pageTitle="Quel est le journal Analytique ? | Microsoft Azure"
   description="Analytique de journal est un service en opérations Management Suite (OMS) qui vous permet de collecter et d’analyser les données opérationnelles générées par les ressources de votre nuage et environnement sur site.  Cet article fournit une vue d’ensemble des différents composants de journal Analytique et liens vers le contenu détaillé."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />

# <a name="what-is-log-analytics"></a>Quel est le journal Analytique ?
Analytique de journal est un service de [Operations Management Suite \(OMS\) ](../operations-management-suite/operations-management-suite-overview.md) qui vous permet de collecter et d’analyser les données générées par les ressources de votre nuage et les environnements locaux. Il vous donne des idées en temps réel à l’aide de la recherche intégrée et tableaux de bord personnalisés facilement analyser des millions d’enregistrements sur tous vos charges de travail et les serveurs, quel que soit leur emplacement physique.


## <a name="log-analytics-components"></a>Composants du journal Analytique
Dans le centre de journal Analytique est le référentiel de l’OMS qui est hébergé dans le nuage Azure.  Collecte de données dans le référentiel à partir de sources connectées par l’ajout de solutions et de la configuration des sources de données à votre abonnement.  Solutions et sources de données chacun crée différents types d’enregistrements qui ont leur propre ensemble de propriétés, mais peuvent toujours être analysées conjointement dans le référentiel des requêtes.  Cela vous permet d’utiliser les mêmes outils et méthodes pour travailler avec différents types de données collectées par différentes sources.


![Référentiel de l’OMS](media/log-analytics-overview/overview.png)


Sources connectées sont les ordinateurs et autres ressources qui génèrent des données collectées par journal Analytique.  Cela peut inclure des agents installés sur les ordinateurs [Windows](log-analytics-windows-agents.md) et [Linux](log-analytics-linux-agents.md) qui se connectent directement ou dans un [groupe de gestion de System Center Operations Manager connecté](log-analytics-om-agents.md).  Analytique de journal peut également collecter des données de [stockage Azure](log-analytics-azure-storage.md).

[Sources de données](log-analytics-data-sources.md) sont les différents types de données collectées à partir de chaque source connecté.  Cela inclut les événements et [les données de performances](log-analytics-data-sources-performance-counters.md) de [Windows](log-analytics-data-sources-windows-events.md) et des agents de Linux en plus des sources telles que les [journaux IIS](log-analytics-data-sources-iis-logs.md)et les [journaux de texte personnalisé](log-analytics-data-sources-custom-logs.md).  Vous configurez chaque source de données que vous souhaitez collecter et la configuration est automatiquement remise à chaque source connecté.


## <a name="analyzing-log-analytics-data"></a>Analyse des données d’Analytique de journal
La plupart de vos interactions avec le journal Analytique sera via le portail de l’OMS qui s’exécute dans un navigateur et vous offre un accès aux paramètres de configuration et plusieurs outils pour analyser et traiter de sur les données collectées.  À partir du portail, vous pouvez tirer parti des [recherches de journaux](log-analytics-log-searches.md) où vous construisez des requêtes pour analyser les données collectées, les [tableaux de bord](log-analytics-dashboards.md) que vous pouvez personnaliser avec des affichages graphiques de vos recherches plus précieuses et les [solutions](log-analytics-add-solutions.md) qui fournissent des fonctionnalités supplémentaires et des outils d’analyse.

![Portail de l’OMS](media/log-analytics-overview/portal.png)


Analytique de journal fournit une syntaxe de requête pour récupérer rapidement et de consolider les données dans le référentiel.  Vous pouvez créer et enregistrer des [Recherches de journaux](log-analytics-log-searches.md) pour les analyser directement dans le portail de l’OMS ou recherches de journaux exécutés automatiquement pour créer une alerte si les résultats de la requête indiquent une condition importante.

![Recherche de journal](media/log-analytics-overview/log-search.png)

Pour donner un aperçu graphique de l’état de santé de votre environnement global, vous pouvez ajouter des visualisations pour les recherches de journal enregistré de votre [tableau de bord](log-analytics-dashboards.md).   

![Tableau de bord](media/log-analytics-overview/dashboard.png)

Pour analyser les données en dehors d’Analytique de journal, vous pouvez exporter les données à partir du référentiel de l’OMS dans des outils tels que [l’Alimentation BI](log-analytics-powerbi.md) ou Excel.  Vous pouvez également exploiter les [API de recherche de journal](log-analytics-log-search-api.md) pour créer des solutions personnalisées qui tirent parti des données d’Analytique de journal ou à intégrer avec d’autres systèmes.

## <a name="solutions"></a>Solutions
Solutions ajoutent des fonctionnalités à Analytique du journal.  Principalement, ils s’exécutent dans le nuage et fournissent une analyse des données collectées dans le référentiel de l’OMS. Ils peuvent également définir des nouveaux types d’enregistrements à collecter qui peuvent être analysés avec des recherches de journaux ou par l’interface utilisateur supplémentaire fournie par la solution dans le tableau de bord OMS.  

![Modifier la solution de suivi](media/log-analytics-overview/change-tracking.png)


Les solutions sont disponibles pour une variété de fonctions, et vous pouvez facilement parcourir les solutions disponibles et [Ajoutez-les à votre espace de travail de l’OMS](log-analytics-add-solutions.md) à partir de la galerie de Solutions.  Beaucoup seront automatiquement déployées et commencer à travailler immédiatement tandis que d’autres peuvent nécessiter une configuration.

![Galerie de solutions](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-architecture"></a>Architecture du journal Analytique
Les spécifications de déploiement de journal Analytique sont minimes dans la mesure où les composants centraux sont hébergées dans le cloud Azure.  Cela inclut le référentiel en plus des services qui vous permettent de mettre en corrélation et analyser les données collectées.  Le portail est accessible à partir de n’importe quel navigateur afin qu’il n’y a aucune exigence de logiciel client.

Vous devez installer les agents sur les ordinateurs [Windows](log-analytics-windows-agents.md) et [Linux](log-analytics-linux-agents.md) , mais il n’y a aucun agent supplémentaire requis pour les ordinateurs qui sont déjà membres d’un [groupe de gestion de SCOM connecté](log-analytics-om-agents.md).  Agents SCOM va continuer à communiquer avec les serveurs de gestion qui transmettent les données à Analytique du journal.  Certaines solutions mais nécessite que des agents de communiquer directement avec le journal Analytique.  La documentation de chaque solution spécifiera ses besoins de communication.

Lorsque vous [inscrire pour l’Analytique de journal](log-analytics-get-started.md), vous allez créer un espace de travail de l’OMS.  Vous pouvez considérer l’espace de travail sous la forme d’un unique environnement de OMS avec son propre référentiel de données, les sources de données et des solutions. Vous pouvez créer plusieurs espaces de travail de votre abonnement à la prise en charge de plusieurs environnements tels que la production et de test.

![Architecture du journal Analytique](media/log-analytics-overview/architecture.png)


## <a name="next-steps"></a>Étapes suivantes

- [S’inscrire pour un compte Analytique de journal gratuit](log-analytics-get-started.md) pour tester dans votre propre environnement.
- Permet d’afficher les différentes [Sources de données](log-analytics-data-sources.md) disponibles pour collecter les données dans le référentiel de l’OMS.
- Pour ajouter des fonctionnalités à Analytique du journal, [Recherchez les solutions disponibles dans la galerie de Solutions](log-analytics-add-solutions.md) .
