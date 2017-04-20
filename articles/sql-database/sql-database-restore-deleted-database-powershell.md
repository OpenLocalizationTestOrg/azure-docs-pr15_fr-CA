<properties
    pageTitle="Restaurer une base de données SQL Azure supprimés (PowerShell) | Microsoft Azure"
    description="Restaurer une base de données SQL Azure supprimés (PowerShell)."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-a-deleted-azure-sql-database-by-using-powershell"></a>Restaurer une base de données de SQL Azure supprimés à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-recovery-using-backups.md)
- [Restauration supprimé DB : portail](sql-database-restore-deleted-database-portal.md)
- [**Restauration supprimé DB : PowerShell**](sql-database-restore-deleted-database-powershell.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="get-a-list-of-deleted-databases"></a>Obtenir la liste des bases de données supprimées

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"

$DeletedDatabases = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName
```

## <a name="restore-your-deleted-database-into-a-standalone-database"></a>Restauration de vos bases de données supprimées dans une base de données autonome

Obtenir la sauvegarde de bases de données supprimées que vous souhaitez restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387(v=azure.300/).aspx) . Puis, démarrez la restauration de la sauvegarde de bases de données supprimées à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390(v=azure.300/).aspx) .

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"
```


## <a name="restore-your-deleted-database-into-an-elastic-database-pool"></a>Restaurer votre base de données supprimée dans un pool d’élasticité de la base de données

Obtenir la sauvegarde de bases de données supprimées que vous souhaitez restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387(v=azure.300/).aspx) . Puis, démarrez la restauration de la sauvegarde de bases de données supprimées à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390(v=azure.300/).aspx) .

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01"
```


## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de l’activité commerciale et scénarios, consultez [vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- Pour obtenir des informations sur les sauvegardes de base de données SQL Azure automatisé, consultez [SQL de base de données des sauvegardes automatisées](sql-database-automated-backups.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [restauration d’une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Active-géo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, voir [copie de la base de données](sql-database-copy.md)
