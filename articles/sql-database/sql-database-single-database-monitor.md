<properties
    pageTitle="Surveillance des performances de base de données dans la base de données de SQL Azure | Microsoft Azure"
    description="Obtenir des informations sur les options d’analyse de votre base de données avec des outils Azure et les vues de gestion dynamiques."
    keywords="surveillance de la base de données, les performances de base de données en nuage"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/27/2016"
    ms.author="carlrab"/>

# <a name="monitoring-database-performance-in-azure-sql-database"></a>Surveillance des performances de base de données dans la base de données de SQL Azure
Analyse des performances d’une base de données SQL Azure démarre avec l’analyse de l’utilisation des ressources par rapport au niveau de performance de base de données choisie. Surveillance vous aide à que déterminer si votre base de données possède des capacités excédentaires ou s’il rencontre des problèmes, car les ressources sont le seuil maximal, puis déterminez s’il est temps pour ajuster le niveau de performances et le [niveau de service](sql-database-service-tiers.md) de votre base de données. Vous pouvez analyser votre base de données à l’aide d’outils graphiques dans [Azure portal](https://portal.azure.com) ou les [vues de gestion dynamique](https://msdn.microsoft.com/library/ms188754.aspx)SQL.

## <a name="monitor-databases-using-the-azure-portal"></a>Surveiller l’utilisation du portail Azure des bases de données

Dans le [portail Azure](https://portal.azure.com/), vous pouvez surveiller l’utilisation d’une seule base de données en sélectionnant votre base de données, puis en cliquant sur le graphique **d’analyse** . Cela ouvre une fenêtre de **mesure** que vous pouvez modifier en cliquant sur le bouton **Modifier le graphique** . Ajoutez les mesures suivantes :

- Pourcentage de l’UC
- Pourcentage DTU
- Pourcentage d’e/s de données
- Pourcentage de taille de base de données

Une fois que vous avez ajouté ces métriques, vous pouvez continuer à les afficher dans le graphique **d’analyse** avec plus de détails sur la fenêtre de la **métrique** . Toutes les mesures de quatre affichent le pourcentage d’utilisation moyenne par rapport à la **DTU** de votre base de données. Consultez l’article de [niveaux de service](sql-database-service-tiers.md) pour plus d’informations sur DTUs.

![Service de surveillance de niveau de performances de la base de données.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Vous pouvez également configurer des alertes sur les mesures de performances. Cliquez sur le bouton **Ajouter une alerte** dans la fenêtre de la **métrique** . Suivez l’Assistant pour configurer l’alerte. Vous avez l’option d’alerte si les métriques dépassent un certain seuil, ou si la mesure est inférieur à un certain seuil.

Par exemple, si vous pensez que la charge de travail sur la croissance de votre base de données, vous pouvez choisir de configurer un message d’alerte chaque fois que votre base de données atteint 80 % sur toutes les métriques de performances. Vous pouvez l’utiliser comme un avertissement pour déterminer quand vous devez basculer vers le niveau de performances supérieur suivant.

Les métriques de performances peuvent également vous aider à déterminer si vous êtes en mesure de revenir à un niveau de performances inférieur. Supposons que vous utilisez une base de données Standard S2 et de toutes les mesures de performances montrent que la base de données sur moyenne n’utilise pas plus de 10 % à un moment donné. Il est probable que la base de données fonctionne bien dans S1 Standard. Toutefois, gardez à l’esprit des charges de travail PIC ou fluctuent avant de prendre la décision de passer à un niveau de performances inférieur.

## <a name="monitor-databases-using-dmvs"></a>Moniteur de bases de données à l’aide de la DMV

Les mêmes mesures qui sont exposés dans le portail sont également disponibles par l’intermédiaire de vues système : [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) la logique **principale** de la base de données de votre serveur et [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dans la base de données de l’utilisateur. Utilisez **sys.resource_stats** si vous avez besoin analyser les données moins précises sur une période plus longue. Utilisez **sys.dm_db_resource_stats** si vous avez besoin analyser les données plus granulaires dans un laps de temps plus petite. Pour plus d’informations, consultez [Conseils pour les performances de base de données SQL Azure](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

>[AZURE.NOTE] **Sys.dm_db_resource_stats** retourne un résultat vide lorsque utilisé dans Web et Business edition bases de données sont annulées.

Pour les pools d’élastique de la base de données, vous pouvez surveiller des bases de données individuelles dans le pool avec les techniques décrites dans cette section. Mais vous pouvez également surveiller le pool dans son ensemble. Pour plus d’informations, consultez [moniteur et de gérer un pool élastique de la base de données](sql-database-elastic-pool-manage-portal.md).
