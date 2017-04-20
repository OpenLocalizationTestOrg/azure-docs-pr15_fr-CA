<properties
    pageTitle="Gestion de base de données SQL Azure via le portail Azure | Microsoft Azure"
    description="Apprenez à utiliser le portail Azure pour gérer une base de données relationnelle dans le nuage en utilisant le portail Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.date="09/19/2016"
    ms.author="sstein"/>


# <a name="managing-azure-sql-databases-using-the-azure-portal"></a>Gestion des bases de données SQL Azure via le portail Azure


> [AZURE.SELECTOR]
- [Azure portal](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

[Azure portal](https://portal.azure.com/) permet de créer, surveiller et gérer les serveurs et les bases de données SQL d’Azure. Cet article fournit une description rapide et des liens pour les détails des tâches plus courantes.

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Afficher vos bases de données SQL d’Azure, les serveurs et les pools

Pour afficher les services de base de données SQL disponibles, cliquez sur **plus de services**et tapez **SQL** dans la zone de recherche :

![Base de données SQL](./media/sql-database-manage-portal/sql-services.png)


## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Comment créer ou afficher les bases de données SQL d’Azure ?

Pour ouvrir la lame de **bases de données SQL** , cliquez sur **bases de données SQL**, puis cliquez sur la base de données que vous souhaitez utiliser ou cliquez sur **+ Ajouter** pour créer une base de données SQL. Pour plus d’informations, voir [Création d’une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md).


![Bases de données SQL](./media/sql-database-manage-portal/sql-databases.png)


## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Comment créer ou afficher les serveurs SQL d’Azure ?

Pour ouvrir la lame **les serveurs SQL** , cliquez sur **les serveurs SQL**, puis cliquez sur le serveur que vous souhaitez utiliser ou cliquez sur **+ Ajouter** pour créer un serveur SQL. Pour plus d’informations, voir [Création d’une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md).

![Serveurs SQL](./media/sql-database-manage-portal/sql-servers.png)


## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Comment créer ou afficher les pools élastiques de SQL ?

Pour ouvrir la lame de **pools élastiques de SQL** , cliquez sur **pools élastiques de SQL**, puis cliquez sur le pool que vous souhaitez utiliser ou cliquez sur **+ Ajouter** pour créer un pool. Pour plus d’informations, voir [Création d’un pool élastique de la base de données avec le portail Azure](sql-database-elastic-pool-create-portal.md).

![Pools élastiques de SQL](./media/sql-database-manage-portal/elastic-pools.png)



## <a name="how-do-i-update-or-view-sql-database-settings"></a>Comment mettre à jour ou afficher les paramètres de base de données SQL ?

Pour afficher ou mettre à jour les paramètres de votre base de données, cliquez sur le paramètre de votre choix sur la lame de la base de données SQL :


![Paramètres de base de données SQL](./media/sql-database-manage-portal/settings.png)


## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Comment pour trouver un nom de serveur complet de bases de données SQL ?

Pour afficher le nom de votre serveur de bases de données, cliquez sur **vue d’ensemble** sur la lame de la **base de données SQL** et notez le nom du serveur :


![Paramètres de base de données SQL](./media/sql-database-manage-portal/server-name.png)


## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Comment gérer les règles de pare-feu pour contrôler l’accès à mon serveur SQL et la base de données ?

Pour afficher, créer ou mettre à jour les règles de pare-feu, cliquez sur **pare-feu de serveur** sur la lame de la **base de données SQL** . Pour plus d’informations, consultez [configurer une règle de pare-feu au niveau du serveur de base de données de SQL Azure via le portail Azure](sql-database-configure-firewall-settings.md).


![règles de pare-feu](./media/sql-database-manage-portal/sql-database-firewall.png)


## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Comment modifier mon SQL de base de données service niveau niveau de performance ou ?


Pour mettre à jour le niveau de service niveau ou les performances d’une base de données SQL, cliquez sur **niveau de tarification (échelle de DTUs)** sur la lame de la **base de données SQL** . Pour plus d’informations, consultez [Modifier la couche et les performances niveau de service (couche de tarification) d’une base de données SQL](sql-database-scale-up.md).


![niveaux de prix](./media/sql-database-manage-portal/pricing-tier.png)


## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Comment configurer l’audit et la détection d’une base de données SQL de la menace ?

Pour configurer la détection de menaces et de l’audit pour une base de données SQL, cliquez sur **détection d’audit et les menaces** sur la lame de la **base de données SQL** . Pour plus d’informations, consultez [mise en route de l’audit de base de données SQL](sql-database-auditing-get-started.md)et [mise en route de la détection des menaces de base de données SQL](sql-database-threat-detection-get-started.md).


## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Comment configurer les données dynamiques de masquage pour une base de données SQL ?

Pour configurer les données dynamiques de masquage pour une base de données SQL, cliquez sur **données dynamiques de masquage** sur la lame de la **base de données SQL** . Pour plus d’informations, consultez [mise en route de masquage données dynamiques SQL de base de données](sql-database-dynamic-data-masking-get-started.md).


## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Comment pour configurer le chiffrement transparent des données (TDE) pour une base de données SQL ?

Pour configurer le chiffrement transparent des données pour une base de données SQL, cliquez sur **chiffrement de données Transparent** sur la lame de la **base de données SQL** . Pour plus d’informations, voir [Activer le TDE sur une base de données à l’aide du portail](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Comment afficher ou modifier la taille maximale d’une base de données SQL ?

Pour afficher ou modifier la taille, une base de données SQL, cliquez sur **taille de la base de données** sur la lame de la **base de données SQL** . Mettre à jour la taille maximale d’une base de données en modifiant le niveau de performances ou de niveau de service. Pour plus d’informations, consultez [Modifier la couche et les performances niveau de service (couche de tarification) d’une base de données SQL](sql-database-scale-up.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Comment surveiller et améliorer les performances d’une base de données SQL ?

Pour surveiller et améliorer les caractéristiques de performances d’une base de données SQL, cliquez sur **vue d’ensemble des performances** sur la lame de la **base de données SQL** . Pour plus d’informations, consultez [Analyse de performances de base de données SQL](sql-database-performance.md).


## <a name="how-do-i-configure-geo-replication"></a>Comment pour configurer géo-réplication ?

Pour configurer la réplication géographique pour une base de données SQL, cliquez sur **Géo-réplication** sur la lame de la **base de données SQL** . Pour plus d’informations, consultez [Configurer géo-réplication de base de données de SQL Azure avec le portail Azure](sql-database-geo-replication-portal.md).


## <a name="how-do-i-failover-to-a-geo-replicated-sql-database"></a>Comment basculer vers une base de données de réplication géographique SQL ?

Pour le basculement vers le secondaire géo-répliquées, cliquez sur **Réplication géographique** sur la lame de la **base de données SQL** , puis cliquez sur **basculement**. Pour plus d’informations, reportez-vous à [initier un basculement planifié ou de base de données de SQL Azure avec le portail Azure](sql-database-geo-replication-failover-portal.md).


## <a name="how-do-i-copy-a-sql-database"></a>Comment pour copier une base de données SQL ?

Pour copier une base de données SQL, cliquez sur **Copier** sur la lame de la **base de données SQL** . Pour plus d’informations, voir [Copier une base de données Azure SQL via le portail Azure](sql-database-copy-portal.md).


![Paramètres de base de données SQL](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Comment archiver une base de données Azure SQL dans un fichier de type sac à DOS ?

Pour créer un type sac à DOS d’une base de données SQL, cliquez sur **Exporter** sur la lame de la **base de données SQL** . Pour plus d’informations, reportez-vous à [Archive d’une base de données Azure SQL dans un fichier de type sac à DOS à l’aide du portail Azure](sql-database-export.md).


![Exportation de base de données SQL](./media/sql-database-manage-portal/sql-database-export.png)



## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Comment restaurer une base de données SQL à un point antérieur dans le temps ?

Pour restaurer une base de données SQL, cliquez sur **restaurer** sur la lame de la **base de données SQL** . Pour plus d’informations, consultez [restauration d’une base de données de SQL Azure à un point antérieur dans le temps avec le portail Azure](sql-database-point-in-time-restore-portal.md).


![Paramètres de base de données SQL](./media/sql-database-manage-portal/sql-database-restore.png)


## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Comment créer une base de données Azure SQL à partir d’un fichier de type sac à DOS ?

Pour créer une base de données SQL à partir d’un fichier de type sac à DOS, cliquez sur **base de données de l’importation** sur le **SQL serveur** lame. Pour plus d’informations, reportez-vous à la section [importation d’un fichier de type sac à DOS pour créer une base de données SQL d’Azure](sql-database-import.md).


![Serveur SQL](./media/sql-database-manage-portal/server-commands.png)


## <a name="how-do-i-restore-a-deleted-sql-database"></a>Comment restaurer une base de données SQL supprimé ?

Pour restaurer une base de données SQL supprimé, cliquez sur **Supprimer les bases de données** sur la blade **server SQL** (le serveur SQL contenant la base de données qui a été supprimé). Pour plus d’informations, consultez [restauration d’une base de données Azure SQL supprimé, l’utilisation du portail Azure](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Comment pour supprimer une base de données SQL ?

Pour supprimer une base de données SQL, cliquez sur **Supprimer** sur la lame de la **base de données SQL** . 

![Paramètres de base de données SQL](./media/sql-database-manage-portal/sql-database-delete.png)



## <a name="additional-resources"></a>Ressources supplémentaires

- [Base de données SQL](sql-database-technical-overview.md)
- [Surveiller et de gérer un pool élastique de la base de données avec le portail Azure](sql-database-elastic-pool-manage-portal.md)
