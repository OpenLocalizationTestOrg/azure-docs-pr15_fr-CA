<properties 
    pageTitle="Initier un basculement planifié ou de base de données de SQL Azure avec le portail Azure | Microsoft Azure" 
    description="Initier un basculement planifié ou de base de données de SQL Azure en utilisant le portail Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>Initier un basculement planifié ou de base de données de SQL Azure avec le portail Azure


> [AZURE.SELECTOR]
- [Azure portal](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Cet article vous montre comment lancer le basculement vers une base de données SQL secondaire avec le [portail Azure](http://portal.azure.com). Pour configurer la réplication de la zone géographique, voir [Configurer géo-réplication de base de données de SQL Azure](sql-database-geo-replication-portal.md).


## <a name="initiate-a-failover"></a>Initier un basculement sur incident

La base de données secondaire peut être activé pour devenir le serveur primaire.  

1. Dans [Azure portal](http://portal.azure.com) rechercher la base de données primaire du partenariat de réplication géographique.
2. Sur la lame de la base de données SQL, sélectionnez **tous les paramètres** > **Géo-réplication**.
3. Dans la liste de **serveurs secondaires** , sélectionnez la base de données à laquelle que vous souhaitez devenir la nouvelle primaire et cliquez sur **Basculer**.

    ![basculement sur incident][2]

4. Cliquez sur **Oui** pour lancer le basculement.

La commande sera immédiatement basculer la base de données secondaire dans le rôle principal. 

Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (l’ordre des secondes de 0 à 25) tandis que les rôles sont activées. Si la base de données principale a plusieurs bases de données secondaires, la commande se reconfigureront automatiquement les autres serveurs secondaires pour se connecter à la nouvelle primaire. Toute l’opération devrait prendre moins d’une minute pour effectuer dans des circonstances normales. 

>[AZURE.NOTE] Si le serveur principal est en ligne et la validation des transactions lorsque la commande est émise une perte de données peut se produire.


## <a name="next-steps"></a>Étapes suivantes   

- Après le basculement, assurez-vous que les exigences d’authentification de votre serveur et de base de données sont configurés sur le serveur primaire de nouveau. Pour plus d’informations, consultez [sécurité de base de données SQL après un sinistre](sql-database-geo-replication-security-config.md).
- Pour en savoir plus de la reprise après un sinistre à l’aide de géo-réplication Active, y compris les versions antérieures et valider les étapes de récupération et effectuer un exercice de restauration après sinistre, reportez-vous à la section [Des exercices de récupération après sinistre](sql-database-disaster-recovery.md)
- Pour un billet de blog de Sasha Nosov sur géo-réplication Active, consultez [coup de projecteur sur les nouvelles fonctionnalités de réplication géographique](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Pour plus d’informations sur la conception d’applications en nuage géo-réplication Active, consultez [conception d’applications cloud pour la continuité d’activité à l’aide de géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Pour plus d’informations sur l’utilisation de géo-réplication Active avec les pools élastique de la base de données, voir [stratégies de récupération d’urgence Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Pour une vue d’ensemble de l’entreprise continurity, consultez [Vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)




<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png
