<properties
    pageTitle="Base de données SQL : Est-il un DTU ? | Microsoft Azure"
    description="Comprendre un Azure SQL base de données à l’unité de transaction est."
    keywords="options de base de données, les performances de la base de données"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Expliquer les unités de Transaction de base de données (DTUs) et les unités de Transaction élastique de base de données (eDTUs)

Cet article explique les unités de Transaction de base de données (DTUs) et les unités de Transaction élastique de base de données (eDTUs) et que se passe-t-il lorsque vous avez atteint le maximum DTUs ou eDTUs.  

## <a name="what-are-database-transaction-units-dtus"></a>Quelles sont les unités de Transaction de base de données (DTUs)

Un DTU est une unité de mesure des ressources qui peuvent être disponibles pour une base de données de Azure SQL autonome à un niveau de performances spécifiques au sein d’un [niveau de service de base de données autonome](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels). Un DTU est une mesure combinée du processeur, la mémoire et les données d’e/s et des e/s du journal de transactions dans un taux déterminé par une charge de travail OLTP d’évaluation conçu pour correspondre aux charges de travail OLTP réels. Doublement des DTUs en augmentant le niveau de performances d’une base de données équivaut à doubler l’ensemble des ressources disponibles pour cette base de données. Par exemple, une base de données Premium P11 1750 DTUs fournit 350 x DTU plus calculer la puissance à une base de données de base avec 5 DTUs. Pour comprendre la méthodologie de la charge de travail OLTP banc d’essai utilisé pour déterminer le mélange DTU, consultez [vue d’ensemble de la référence de base de données SQL](sql-database-benchmark-overview.md).

![Présentation de la base de données SQL : unique DTUs de base de données par couche et de niveau](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Vous pouvez [Modifier les niveaux de service](sql-database-scale-up.md) à tout moment avec très peu de temps à votre application (en général moins de quatre secondes en moyenne). Pour de nombreuses entreprises et les applications, la possibilité de créer des bases de données et l’accès vers le haut ou vers le bas les performances de la base de données unique à la demande est suffisante, en particulier si des modèles d’utilisation sont relativement prévisibles. Mais si vous avez des modèles d’utilisation imprévisibles, il peut le rendre difficile à gérer les coûts et votre modèle d’entreprise. Dans ce scénario, vous utilisez un pool élastique avec un certain nombre d’eDTUs.

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Quelles sont les unités de Transaction élastique de base de données (eDTUs)

Un eDTU est une unité de mesure de l’ensemble des ressources (DTUs) qui peut être partagé entre plusieurs bases de données sur un serveur SQL d’Azure - appelé un [pool élastique](sql-database-elastic-pool.png). Pools élastiques offrent une solution simple et économique pour gérer les objectifs de performance pour plusieurs bases de données les plus diverses et modèles d’utilisation imprévisibles. Voir [pools élastiques et des niveaux de service](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) pour plus d’informations.

![Présentation de la base de données SQL : eDTUs par couche et de niveau](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Un pool est donné un nombre défini d’eDTUs, pour un prix défini. Au sein du pool, bases de données individuelles sont indiquées la flexibilité à l’échelle automatique dans les paramètres du jeu. Sous une charge importante, une base de données peut consommer plus d’eDTUs pour répondre à la demande. Bases de données sous des charges lumière en consommant moins et bases de données sans charge ne consomment aucune eDTUs. Provisionnement des ressources pour l’ensemble du pool plutôt que pour des bases de données unique simplifie vos tâches de gestion. De plus, vous avez un budget prévisible pour le pool.

EDTUs supplémentaires peuvent être ajoutés à un pool existant avec aucun temps d’arrêt de la base de données ou pas d’impact sur les bases de données dans le pool élastique. De la même façon, si eDTUs supplémentaires ne sont plus nécessaires qu’ils pouvoir retirer un pool existant à n’importe quel point dans le temps. Vous pouvez ajouter ou soustraire des bases de données au pool. Si une base de données est prévisible sous-utilisation des ressources, la sortie.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Comment puis-je déterminer le nombre de DTUs requises par mon travail ?

Si vous cherchez à migrer un existant sur site ou charge de machine virtuelle de SQL Server pour la base de données de SQL Azure, vous pouvez utiliser la [Calculatrice de DTU](http://dtucalculator.azurewebsites.net/) de estimer le nombre de DTUs nécessaire. Pour une charge de travail de base de données de SQL Azure existante, vous pouvez utiliser [Insight de performances de requête de base de données SQL](sql-database-query-performance.md) afin de comprendre votre consommation de ressource de base de données (DTUs) pour obtenir une visibilité plus profonde comment optimiser votre charge de travail. Vous pouvez également utiliser la DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) pour obtenir les informations de consommation de ressources pour la dernière heure. Sinon, la vue de catalogue [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) peut également être interrogé pour obtenir les mêmes données pour les 14 derniers jours, bien qu’à une faible fidélité des moyennes de cinq minutes.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Comment savoir si l’avantage pourrais à partir d’un pool de ressources d’élastique ?

Les pools sont adaptés pour un grand nombre de bases de données avec des modèles d’utilisation spécifique. Une base de données spécifiée, ce modèle se caractérise par un faible taux d’utilisation moyenne avec des pics d’utilisation relativement peu fréquentes. Base de données de SQL évalue l’utilisation des ressources historiques des bases de données dans un serveur de base de données SQL et recommande la configuration du pool approprié dans le portail Azure automatiquement. Pour plus d’informations, consultez [quand un pool élastique de la base de données doit être utilisé ?](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Que se passe-t-il lorsque j’atteins mon DTUs maximales

Les niveaux de performance sont étalonnés et régies pour fournir les ressources nécessaires pour exécuter votre charge de travail de base de données dans les limites maximum autorisées pour votre niveau de performances/de niveau de service sélectionné. Si votre charge de travail atteint les limites de l’une des limites d’e/s de données/UC/journal des e/s, vous continuez de recevoir les ressources à la valeur maximale autorisée de niveau, mais vous êtes susceptible de voir des latences accrues pour vos requêtes. Ces limites n’entraînent pas d’erreurs, mais plutôt un ralentissement de la charge de travail, sauf si le ralentissement est donc que les requêtes commencent minutage. Si vous atteignez les limites de sessions/demandes de maximal d’utilisateurs simultanés autorisés (threads de travail), vous voyez des erreurs explicites. Reportez-vous à la section [limites de ressource de base de données de SQL Azure](sql-database-resource-limits.md) pour plus d’informations sur la limite de ressources autres que l’unité centrale, mémoire, e/s et les e/s du journal de transactions.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les DTUs et l’eDTUs disponible pour les bases de données autonomes et élastiques pools, reportez-vous à la section [niveau de Service](sql-database-service-tiers.md) .
- Reportez-vous à la section [limites de ressource de base de données de SQL Azure](sql-database-resource-limits.md) pour plus d’informations sur la limite de ressources autres que l’unité centrale, mémoire, e/s et les e/s du journal de transactions.
- Reportez-vous à la section [Aperçu de performances de requête de base de données SQL](sql-database-query-performance.md) afin de comprendre votre consommation (DTUs).
- Consultez [vue d’ensemble de la référence de base de données SQL](sql-database-benchmark-overview.md) pour comprendre la méthodologie de la charge de travail OLTP banc d’essai utilisé pour déterminer le mélange DTU.