<properties 
    pageTitle="Copier une base de données Azure SQL à l’aide de PowerShell | Microsoft Azure" 
    description="Créer une copie d’une base de données Azure SQL à l’aide de PowerShell" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/08/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-powershell"></a>Copier une base de données Azure SQL à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-copy.md)
- [Azure portal](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Cet article explique comment copier une base de données SQL avec PowerShell sur le même serveur, sur un autre serveur ou une base de données dans un [pool d’élastique de la base de données](sql-database-elastic-pool.md). L’opération de copie de base de données utilise l’applet de commande [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644.aspx) . 


Pour terminer cet article, vous devez les éléments suivants :

- Une base de données Azure SQL (une base de données à copier). Si vous ne disposez pas d’une base de données SQL, créez-en un en suivant les étapes décrites dans cet article : [créer votre première base de données de SQL Azure](sql-database-get-started.md).
- La version la plus récente de PowerShell d’Azure. Pour plus d’informations, consultez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


De nombreuses nouvelles fonctionnalités de base de données de SQL seulement sont pris en charge que lorsque vous utilisez le [modèle de déploiement d’Azure le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md), afin que les exemples utilisent les [applets de commande PowerShell de base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) pour le Gestionnaire de ressources. Le modèle classique de déploiement existant [applets de commande de base de données de SQL Azure (classique)](https://msdn.microsoft.com/library/azure/dn546723.aspx) sont pris en charge pour la compatibilité descendante, mais nous vous recommandons de qu'utiliser les applets de commande du Gestionnaire de ressources.


>[AZURE.NOTE] Selon la taille de votre base de données, l’opération de copie peut prendre un certain temps pour terminer.


## <a name="copy-a-sql-database-to-the-same-server"></a>Copier une base de données SQL sur le même serveur

Pour créer la copie sur le même serveur, omettez le `-CopyServerName` paramètre (ou sur le même serveur).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Copier une base de données SQL vers un autre serveur

Pour créer la copie sur un autre serveur, vous devez inclure le `-CopyServerName` paramètre et définissez-la sur un autre serveur. La *copie* de serveur doit déjà exister. Si elle est dans un groupe de ressources différent, puis vous devez également inclure le `-CopyResourceGroupName` paramètre.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-database-pool"></a>Copier une base de données SQL dans un pool d’élasticité de la base de données

Pour créer une copie d’une base de données SQL dans un pool, définissez le `-ElasticPoolName` paramètre à un pool existant.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Résoudre les noms d’accès

Pour résoudre les connexions d’accès à l’issue de l’opération de copie, voir [résolution de connexions](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="example-powershell-script"></a>Exemple de script PowerShell

Le script suivant suppose que tous les groupes de ressources, des serveurs, et le pool existe déjà (remplacer les valeurs de la variable avec vos ressources existantes). Tout doit exister, à l’exception de la copie de la base de données.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId
    
    
    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"
    
    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"
    
    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName
    
    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName
    
    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"
    
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName



    

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la copie d’une base de données de SQL Azure, consultez [copie d’une base de données SQL d’Azure](sql-database-copy.md) .
- Voir la [copie d’une base de données Azure SQL via le portail Azure](sql-database-copy-portal.md) pour copier une base de données à l’aide du portail Azure.
- Voir la [copie d’une base de données Azure SQL à l’aide de T-SQL](sql-database-copy-transact-sql.md) pour copier une base de données à l’aide de Transact-SQL.
- Voir [comment gérer la sécurité de base de données Azure SQL après un sinistre](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et des connexions d’accès lors de la copie d’une base de données vers un autre serveur logique.


## <a name="additional-resources"></a>Ressources supplémentaires

- [Nouvelle-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
- [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
- [Gestion des accès](sql-database-manage-logins.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données pour un type sac à DOS](sql-database-export.md)
- [Vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- [Documentation de la base de données de SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Référence d’applet de commande PowerShell SQL Azure de base de données](https://msdn.microsoft.com/library/mt574084.aspx)
