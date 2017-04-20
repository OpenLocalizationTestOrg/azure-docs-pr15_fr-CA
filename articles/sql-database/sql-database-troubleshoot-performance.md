<properties
    pageTitle="Conseils de réglage des performances de base de données de SQL | Microsoft Azure"
    description="Conseils pour l’optimisation dans la base de données de SQL Azure, grâce à l’évaluation et l’amélioration des performances."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="SQL réglage des performances, des conseils, le réglage des performances sql, le réglage des performances de base de données sql du réglage des performances de base de données"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-database-performance-tuning-tips"></a>Conseils de réglage de performances de la base de données SQL
Vous pouvez modifier le [niveau de service](sql-database-service-tiers.md) d’une base de données unique ou augmenter l’eDTUs d’un pool d’élasticité de la base de données à tout moment pour améliorer les performances, mais vous pouvez identifier les opportunités d’améliorer et d’optimiser les performances de la requête tout d’abord. Optimisation médiocre des requêtes et les index manquants sont des causes de mauvaises performances. Cet article fournit des conseils pour les performances de la base de données SQL.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>Étapes pour évaluer et régler les performances de base de données
1.  Dans le [Portail Azure](https://portal.azure.com), cliquez sur **bases de données SQL**, sélectionnez la base de données et puis utiliser le plan de surveillance pour rechercher des ressources proche de leur maximum. La consommation de DTU est affichée par défaut. Cliquez sur **Modifier** pour modifier la plage de temps et les valeurs affichées.
2.  [Aperçu des performances de requête](sql-database-query-performance.md) permet d’évaluer les requêtes à l’aide de DTUs et ensuite utiliser le [Gestionnaire de base de données SQL](sql-database-advisor.md) pour afficher des recommandations pour la création et la suppression d’index, paramétrage de requêtes et résoudre les problèmes de schéma.
3.  Vous pouvez utiliser des vues de gestion dynamique (DMV), les événements étendus (Xevents) et la banque de requête dans SSMS pour obtenir les paramètres de performances en temps réel. Consultez la [rubrique de guide de performances](sql-database-performance-guidance.md) pour la surveillance et le réglage des conseils détaillés.


    > [AZURE.IMPORTANT] Il est recommandé de toujours utiliser la dernière version du Management Studio reste synchronisé avec les mises à jour Microsoft Azure et de base de données SQL. [Mise à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="steps-to-improve-database-performance-with-more-resources"></a>Étapes à suivre pour améliorer les performances de la base de données avec davantage de ressources
1.  Bases de données unique, vous pouvez [Modifier les niveaux de service](sql-database-scale-up.md) à la demande pour améliorer les performances de la base de données.
2.  Pour plusieurs bases de données, envisagez d’utiliser des [pools d’élastique de la base de données](sql-database-elastic-pool-guidance.md) à l’échelle automatiquement des ressources.
