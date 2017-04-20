<properties
    pageTitle="Restaurer une base de données de SQL Azure à partir d’une sauvegarde geo redondant (PowerShell) | Microsoft Azure"
    description="Restaurer une base de données de SQL Azure dans un nouveau serveur à partir d’une sauvegarde geo redondant"
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

# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Restaurer une base de données de SQL Azure à partir d’une sauvegarde geo redondant à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-recovery-using-backups.md)
- [Géo-restauration : Portail d’Azure](sql-database-geo-restore-portal.md)

Cet article vous explique comment restaurer votre base de données dans un nouveau serveur à l’aide de géo-restauration. Pour ce faire par le biais de PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="geo-restore-your-database-into-a-standalone-database"></a>Geo-restaurer votre base de données dans une base de données autonome

1. Obtenir la sauvegarde redondante géographique de votre base de données que vous souhaitez restaurer à l’aide de la [Get-AzureRmSqlDatabaseGeoBackup] (applet de commande https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Démarrer la restauration de la sauvegarde geo redondant à l’aide de la [restauration-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx).

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## <a name="geo-restore-your-database-into-an-elastic-database-pool"></a>Geo-restaurer votre base de données dans un pool d’élasticité de la base de données

1. Obtenir la sauvegarde redondante géographique de votre base de données que vous souhaitez restaurer à l’aide de la [Get-AzureRmSqlDatabaseGeoBackup] (applet de commande https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Démarrer la restauration de la sauvegarde geo redondant à l’aide de la [restauration-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx). Spécifiez le nom de pool que vous souhaitez restaurer votre base de données dans.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  


## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de l’activité commerciale et scénarios, consultez [vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md).
- Pour obtenir des informations sur les sauvegardes de base de données SQL Azure automatisé, consultez [SQL de base de données des sauvegardes automatisées](sql-database-automated-backups.md).
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [restauration d’une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md).
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication Active](sql-database-geo-replication-overview.md).  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, voir [copie de la base de données](sql-database-copy.md).
