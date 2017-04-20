<properties
    pageTitle="Restauration d’une base de données de SQL Azure à un point antérieur dans le temps (PowerShell) | Microsoft Azure"
    description="Restaurer une base de données de SQL Azure à un point antérieur dans le temps"
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
    ms.workload="NA"
    ms.date="07/17/2016"
    ms.author="sstein"/>

# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>Restauration d’une base de données de SQL Azure à un point antérieur dans le temps avec PowerShell

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-recovery-using-backups.md)
- [Restauration de point-à-temps : Portail d’Azure](sql-database-point-in-time-restore-portal.md)

Cet article vous explique comment restaurer votre base de données à un point antérieur dans le temps à partir [de la base de données SQL des sauvegardes automatisées](sql-database-automated-backups.md). Pour cela, à l’aide de PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="restore-your-database-to-a-point-in-time-as-a-standalone-database"></a>Restaurer votre base de données à un point dans le temps, comme une base de données autonome

1. La base de données que vous souhaitez restaurer à l’aide de la [Get-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurer la base de données à un point dans le temps à l’aide de la [restauration-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx).

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## <a name="restore-your-database-to-a-point-in-time-into-an-elastic-database-pool"></a>Restaurer votre base de données à un point dans le temps dans un pool d’élasticité de la base de données

1. La base de données que vous souhaitez restaurer à l’aide de la [Get-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurer la base de données à un point dans le temps à l’aide de la [restauration-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx).

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"


## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de l’activité commerciale et scénarios, consultez [vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- Pour obtenir des informations sur les sauvegardes de base de données SQL Azure automatisé, consultez [SQL de base de données des sauvegardes automatisées](sql-database-automated-backups.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [restauration d’une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Active-géo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, voir [copie de la base de données](sql-database-copy.md)
