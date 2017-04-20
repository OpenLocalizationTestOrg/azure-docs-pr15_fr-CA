<properties
    pageTitle="Limites des ressources de base de données SQL Azure"
    description="Cette page décrit certaines limites communes de la ressource de base de données de SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="10/13/2016"
    ms.author="carlrab" />


# <a name="azure-sql-database-resource-limits"></a>Limites des ressources de base de données SQL Azure

## <a name="overview"></a>Vue d’ensemble

Une base de données SQL Azure gère les ressources disponibles pour une base de données à l’aide de deux mécanismes différents : **L’application de limites**et de la **Gouvernance de ressources** . Cette rubrique décrit ces deux domaines, la gestion des ressources.

## <a name="resource-governance"></a>Gouvernance de ressources
Un des objectifs des niveaux de service Basique, Standard et Premium, est pour une base de données de SQL Azure pour se comporter comme si la base de données est en cours d’exécution sur son propre ordinateur, complètement isolé à partir d’autres bases de données. La gouvernance de ressources émule ce comportement. Si l’utilisation des ressources globales atteint le disponible maximale de l’UC, la mémoire, d’e/s de journal et les ressources d’e/s de données affectés à la base de données, la gouvernance de ressources sera dans l’exécution des requêtes en file d’attente et affecter des ressources aux requêtes en file d’attente car ils libèrent.

En tant que sur un ordinateur dédié, utilisant toutes les ressources disponibles entraîne une exécution plus longue des requêtes, ce qui peuvent entraîner des délais d’expiration de commande sur le client en cours d’exécution. Applications avec la logique des nouvelles tentatives agressif et exécuter des requêtes sur la base de données avec une fréquence élevée peut rencontrer des messages d’erreur lorsque vous essayez d’exécuter des requêtes de nouveau lorsque la limite de demandes simultanées a été atteint.

### <a name="recommendations"></a>Recommandations :
Surveiller l’utilisation des ressources, ainsi que les temps de réponse moyen de requêtes lors de l’approche de l’utilisation maximale d’une base de données. Lorsqu’ils rencontrent des latences supérieures de requête vous disposez généralement de trois options :

1.  Réduire le volume de demandes entrantes à la base de données pour éviter le délai d’attente et des tas de demandes.

2.  Affecter un niveau de performances supérieur à la base de données.

3.  Optimiser les requêtes pour réduire l’utilisation des ressources de chaque requête. Pour plus d’informations, reportez-vous à la section Réglage de requête/Hinting dans l’article de conseils pour les performances de base de données SQL Azure.

## <a name="enforcement-of-limits"></a>Application des limites
Ressources autres que le processeur, la mémoire, d’e/s de journal et d’e/s de données sont appliquées en refusant les nouvelles demandes lorsque les limites sont atteintes. Les clients recevront un [message d’erreur](sql-database-develop-error-messages.md) en fonction de la limite a été atteinte.

Par exemple, le nombre de connexions à une base de données SQL, ainsi que le nombre de demandes simultanées qui peuvent être traitées est limité. Base de données de SQL permet le nombre de connexions à la base de données est supérieure au nombre de demandes simultanées pour prendre en charge le regroupement de connexions. Tandis que le nombre de connexions disponibles peut facilement être contrôlé par l’application, la quantité de requêtes parallèles est souvent plus difficile à estimer et à contrôler de temps. En particulier pendant les pics de charge lors de l’application soit envoie trop de demandes ou de la base de données atteint sa ressource limite et commence à empiler les threads de travail en raison d’une requêtes plus longues, erreurs peuvent être rencontrées.

## <a name="service-tiers-and-performance-levels"></a>Les niveaux de service et de performances

Il existe des niveaux de service et de performances pour les deux pools d’élastique et de base de données autonome.

### <a name="standalone-databases"></a>Bases de données autonome

Pour une base de données autonome, la limite d’une base de données est définie par le niveau de performances et de la couche de service de base de données. Le tableau suivant décrit les caractéristiques de base, Standard et Premium de bases de données à différents niveaux de performances.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>Pools élastiques

[Pools élastiques](sql-database-elastic-pool.md) partager les ressources entre les bases de données dans le pool. Le tableau suivant décrit les caractéristiques des pools d’élasticité de la base de données Basic, Standard et Premium.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Pour une définition de développé de chaque ressource répertorié dans les tableaux précédents, voir les descriptions dans les [limites et les capacités de niveau de Service](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Pour une vue d’ensemble des niveaux de service, consultez [les niveaux de performances et de niveaux de Service de base de données SQL Azure](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Autres limites de base de données SQL

| Zone | Limite de | Description |
|---|---|---|
| Bases de données à l’aide d’exportation automatique par abonnement | 10 | Exportation automatique vous permet de créer une planification personnalisée pour la sauvegarde de vos bases de données SQL. Pour plus d’informations, consultez [les bases de données SQL : prise en charge pour l’exportation de base de données SQL automatique](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines).|
| Base de données par serveur | Jusqu'à 5 000 | Jusqu'à 5000 bases de données sont autorisés par le serveur sur les serveurs de V12. |  
| DTUs par serveur | 45000 | 45000 DTUs sont disponibles par serveur sur les serveurs V12 pour la mise en service des bases de données, pools élastiques et entrepôts de données. |



## <a name="resources"></a>Ressources

[Abonnement Azure et des limites de Service, des Quotas et des contraintes](../azure-subscription-service-limits.md)

[Les niveaux de performances et de niveaux de Service de base de données SQL Azure](sql-database-service-tiers.md)

[Messages d’erreur pour les programmes de clients de la base de données SQL](sql-database-develop-error-messages.md)
