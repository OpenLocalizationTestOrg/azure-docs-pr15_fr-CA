<properties 
    pageTitle="Initier un basculement planifié ou de base de données de SQL Azure avec Transact-SQL | Microsoft Azure" 
    description="Initier un basculement planifié ou de base de données de SQL Azure à l’aide de Transact-SQL" 
    services="sql-database" 
    documentationCenter="" 
    authors="CarlRabeler" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="08/29/2016"
    ms.author="carlrab"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>Initier un basculement planifié ou de base de données de SQL Azure avec Transact-SQL


> [AZURE.SELECTOR]
- [Azure portal](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Cet article vous montre comment lancer le basculement vers une base de données SQL secondaire à l’aide de Transact-SQL. Pour configurer la réplication de la zone géographique, voir [Configurer géo-réplication de base de données de SQL Azure](sql-database-geo-replication-transact-sql.md).



Pour déclencher le basculement, vous devez :

- Une connexion d’accès DBManager sur le serveur primaire, ont db_ownership de la base de données locale que vous serez géo-réplication, et être DBManager sur l’ou les serveurs partenaire auquel vous allez configurer la réplication-Geo.
- SQL Server Management Studio (SSMS)


> [AZURE.IMPORTANT] Il est recommandé de toujours utiliser la dernière version du Management Studio reste synchronisé avec les mises à jour Microsoft Azure et de base de données SQL. [Mise à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).




## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>Initier un basculement planifié la promotion d’une base de données secondaire pour devenir la nouvelle primaire

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour promouvoir une base de données secondaire pour devenir la nouvelle base de données principale de la manière prévue, rétrogradation principal existant pour devenir un secondaire. Cette instruction est exécutée sur la base de données master sur le serveur de base de données de SQL Azure logique dans lequel réside la base répliquée geo secondaire qui est promue. Cette fonctionnalité est conçue pour un basculement planifié, comme au cours des exercices de la reprise après sinistre et requiert que la base de données principale soit disponible.

La commande exécute le flux de travail suivant :

1. Bascule temporairement la réplication en mode synchrone, à l’origine de toutes les transactions en cours à vider sur le site secondaire et le blocage de toutes les nouvelles transactions ;

2. Bascule les rôles des deux bases de données du partenariat de réplication géographique.  

Cette séquence garantit que les deux bases de données sont synchronisés avant de passer par les rôles et donc sans perte de données se produit. Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (l’ordre des secondes de 0 à 25) tandis que les rôles sont activées. Si la base de données principale a plusieurs bases de données secondaires, la commande se reconfigureront automatiquement les autres serveurs secondaires pour se connecter à la nouvelle primaire.  Toute l’opération devrait prendre moins d’une minute pour effectuer dans des circonstances normales. Pour plus d’informations, voir [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et les [Niveaux de Service](sql-database-service-tiers.md).


Utilisez les étapes suivantes pour initier un basculement planifié.

1. Dans Management Studio, connectez-vous au serveur logique de base de données de SQL Azure dans lequel se trouve une base de données secondaire géo-répliquées.

2. Ouvrez le dossier bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur le **masque**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour basculer de la base de données secondaire vers le rôle principal.

        ALTER DATABASE <MyDB> FAILOVER;

4. Cliquez sur **exécuter** pour exécuter la requête.

>[AZURE.NOTE] Dans de rares cas, il est possible que l’opération ne peut pas terminer et semble bloquée. Dans ce cas, l’utilisateur peut exécuter la commande de basculement de force et accepter la perte de données.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Initier un basculement non planifié à partir de la base de données principale à la base de données secondaire

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour promouvoir une base de données secondaire pour devenir la nouvelle base de données primaire de façon non planifiée, forcer la rétrogradation du principal existant pour devenir un secondaire à la fois lorsque la base de données principale n’est plus disponible. Cette instruction est exécutée sur la base de données master sur le serveur de base de données de SQL Azure logique dans lequel réside la base répliquée geo secondaire qui est promue.

Cette fonctionnalité est conçue pour la reprise après sinistre lors de la restauration de la disponibilité de la base de données est essentielle et une perte de données est acceptable. Lorsque le basculement forcé est appelée, la base de données secondaire spécifiée devient la base de données primaire immédiatement et commence à accepter des transactions d’écriture. Dès que la base de données primaire d’origine est en mesure de se reconnecter à cette nouvelle base de données principale, une sauvegarde incrémentielle est effectuée sur la base de données primaire d’origine, et l’ancienne base de données principale est effectuée dans une base de données secondaire pour la nouvelle base de données principale ; par la suite, il est simplement un réplica de synchronisation de la nouvelle primaire.

Toutefois, étant donné que le Point de restauration du temps n’est pas pris en charge sur les bases de données secondaires, si l’utilisateur souhaite récupérer les données validées dans la base de données principale ancienne qui n’avait pas été répliqué pour la nouvelle base de données principale avant le basculement forcé s’est produite, l’utilisateur devra engager pour récupérer cette perte de données.

Si la base de données principale a plusieurs bases de données secondaires, la commande se reconfigureront automatiquement les autres serveurs secondaires pour se connecter à la nouvelle primaire.

Utilisez les étapes suivantes pour initier un basculement non planifié.

1. Dans Management Studio, connectez-vous au serveur logique de base de données de SQL Azure dans lequel se trouve une base de données secondaire géo-répliquées.

2. Ouvrez le dossier bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur le **masque**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour basculer de la base de données secondaire vers le rôle principal.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Cliquez sur **exécuter** pour exécuter la requête.

>[AZURE.NOTE] Si la commande est émise lorsque sont principal et secondaire en ligne l’ancien serveur principal va devenir la nouvelle secondaire immédiatement sans la synchronisation des données. Si la carte principale est la validation de transactions lorsque la commande est émise une perte de données peuvent se produire.



## <a name="next-steps"></a>Étapes suivantes   

- Après le basculement, assurez-vous que les exigences d’authentification de votre serveur et de base de données sont configurés sur le serveur primaire de nouveau. Pour plus d’informations, consultez [sécurité de base de données SQL après un sinistre](sql-database-geo-replication-security-config.md).
- Pour en savoir plus de la reprise après un sinistre à l’aide de géo-réplication Active, y compris les versions antérieures et valider les étapes de récupération et effectuer un exercice de restauration après sinistre, voir [Récupération d’urgence](sql-database-disaster-recovery.md)
- Pour un billet de blog de Sasha Nosov sur géo-réplication Active, consultez [coup de projecteur sur les nouvelles fonctionnalités de réplication géographique](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Pour plus d’informations sur la conception d’applications en nuage géo-réplication Active, consultez [conception d’applications cloud pour la continuité d’activité à l’aide de géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Pour plus d’informations sur l’utilisation de géo-réplication Active avec les pools élastique de la base de données, voir [stratégies de récupération d’urgence Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Pour une vue d’ensemble de l’entreprise continurity, consultez [Vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
