<properties 
    pageTitle="Initier un basculement planifié ou de base de données de SQL Azure avec PowerShell | Microsoft Azure" 
    description="Initier un basculement planifié ou de base de données de SQL Azure à l’aide de PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>Initier un basculement planifié ou de base de données de SQL Azure avec PowerShell



> [AZURE.SELECTOR]
- [Azure portal](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Cet article vous montre comment initier un basculement planifié ou de base de données SQL avec PowerShell. Pour configurer la réplication de la zone géographique, voir [Configurer géo-réplication de base de données de SQL Azure](sql-database-geo-replication-powershell.md).



## <a name="initiate-a-planned-failover"></a>Initier un basculement planifié

Utilisez l’applet de commande **Set-AzureRmSqlDatabaseSecondary** avec le paramètre **- Failover** de promouvoir une base de données secondaire pour devenir la nouvelle base de données primaire, rétrogradation principal existant pour devenir un secondaire. Cette fonctionnalité est conçue pour un basculement planifié, comme au cours des exercices de récupération d’urgence et requiert que la base de données principale soit disponible.

La commande exécute le flux de travail suivant :

1. Basculer temporairement la réplication en mode synchrone. Alors toutes les transactions en cours à vider sur le site secondaire.

2. Changer les rôles des deux bases de données du partenariat de réplication géographique.  

Cette séquence garantit que les deux bases de données sont synchronisés avant de passer par les rôles et donc sans perte de données se produit. Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (l’ordre des secondes de 0 à 25) tandis que les rôles sont activées. Toute l’opération devrait prendre moins d’une minute pour effectuer dans des circonstances normales. Pour plus d’informations, voir [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).




Cette applet de commande renvoie lorsque le processus de basculement de la base de données secondaire vers le principal est terminé.

La commande suivante bascule les rôles de la base de données nommée « mydb » sur le serveur « srv2 » sous le groupe de ressources « rg2 » principal. Primaire d’origine à laquelle était connecté « db2 » utilisera secondaire après que deux bases de données sont complètement synchronisées.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] Dans de rares cas, il est possible que l’opération ne peut pas terminer et semble ne plus répondre. Dans ce cas, l’utilisateur peut appeler la commande de basculement sur incident (basculement non planifié) force et accepter la perte de données.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Initier un basculement non planifié à partir de la base de données principale à la base de données secondaire


Vous pouvez utiliser l’applet de commande **Set-AzureRmSqlDatabaseSecondary** avec des paramètres **– Basculement** et **- AllowDataLoss** pour promouvoir une base de données secondaire pour devenir la nouvelle base de données primaire de façon non planifiée, forcer la rétrogradation du principal existant pour devenir un secondaire à la fois lorsque la base de données principale n’est plus disponible.

Cette fonctionnalité est conçue pour la reprise après sinistre lors de la restauration de la disponibilité de la base de données est essentielle et une perte de données est acceptable. Lorsque le basculement forcé est appelée, la base de données secondaire spécifiée devient la base de données primaire immédiatement et commence à accepter des transactions d’écriture. Dès que la base de données primaire d’origine est en mesure de se reconnecter à cette nouvelle base de données principale après l’opération de basculement forcé, une sauvegarde incrémentielle est effectuée sur la base de données primaire d’origine, et l’ancienne base de données principale est effectuée dans une base de données secondaire pour la nouvelle base de données principale ; par la suite, il est simplement un réplica de la nouvelle primaire.

Mais, parce que la restauration du Point de temps n’est pas pris en charge sur les bases de données secondaires, si vous le souhaitez aux données de récupération engagées sur la base de données principale ancienne qui n’a pas été répliqué pour la nouvelle base de données principale, vous devez s’engager à CSS pour restaurer une base de données pour la sauvegarde du journal connus.

> [AZURE.NOTE] Si la commande est émise lorsque sont principal et secondaire en ligne l’ancien serveur principal va devenir la nouvelle secondaire immédiatement sans la synchronisation des données. Si la carte principale est la validation de transactions lorsque la commande est émise une perte de données peuvent se produire.


Si la base de données principale a plusieurs zones secondaires la commande partiellement réussira. L’image secondaire sur lequel la commande a été exécutée devient principal. L’ancien serveur principal reste toutefois principal, c'est-à-dire les couleurs deux primaires finissent dans un état incohérent et connectés par un lien de réplication interrompue. L’utilisateur devra réparer manuellement cette configuration à l’aide d’une API « supprimer secondaire » sur une de ces bases de données primaires.


La commande suivante bascule les rôles de la base de données nommée « mydb » principal lorsque le serveur principal n’est pas disponible. Primaire d’origine à laquelle était connecté « mydb » utilisera secondaire une fois qu’il est en ligne. À ce stade, la synchronisation risque de perte de données.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## <a name="next-steps"></a>Étapes suivantes   

- Après le basculement, assurez-vous que les exigences d’authentification de votre serveur et de base de données sont configurés sur le serveur primaire de nouveau. Pour plus d’informations, consultez [sécurité de base de données SQL après un sinistre](sql-database-geo-replication-security-config.md).
- Pour en savoir plus de la reprise après un sinistre à l’aide de géo-réplication Active, y compris les versions antérieures et valider les étapes de récupération et effectuer un exercice de restauration après sinistre, reportez-vous à la section [Des exercices de récupération après sinistre](sql-database-disaster-recovery.md)
- Pour un billet de blog de Sasha Nosov sur géo-réplication Active, consultez [coup de projecteur sur les nouvelles fonctionnalités de réplication géographique](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Pour plus d’informations sur la conception d’applications en nuage géo-réplication Active, consultez [conception d’applications cloud pour la continuité d’activité à l’aide de géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Pour plus d’informations sur l’utilisation de géo-réplication Active avec les pools élastique de la base de données, voir [stratégies de récupération d’urgence Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Pour une vue d’ensemble de l’entreprise continurity, consultez [Vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
