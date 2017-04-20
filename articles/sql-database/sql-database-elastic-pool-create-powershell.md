<properties
    pageTitle="Créer un nouveau pool d’élasticité de la base de données avec PowerShell | Microsoft Azure"
    description="Découvrez comment utiliser PowerShell pour la base de données de SQL Azure cloisonnée ressources en créant un pool élastique évolutive de la base de données pour gérer plusieurs bases de données."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="create-a-new-elastic-database-pool-with-powershell"></a>Créer un nouveau pool de la base de données élastique avec PowerShell

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Apprenez à créer un [pool d’élastique de la base de données](sql-database-elastic-pool.md) à l’aide des applets de commande PowerShell. 

Pour les codes d’erreur courants, voir [des codes d’erreur SQL pour les applications de client de base de données SQL : base de données d’erreur de connexion et d’autres problèmes](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Pools élastiques sont généralement disponible (GA) dans toutes les régions d’Azure, à l’exception du Nord centrale nous et ouest de l’Inde où il est actuellement en mode Aperçu.  Vous trouverez dans les meilleurs délais des GA de pools élastiques dans ces régions. Également, pools élastiques ne prennent pas en charge les bases de données à l’aide [de mémoire OLTP ou en mémoire analytique](sql-database-in-memory.md).


Vous devez exécuter Azure PowerShell 1.0 ou version ultérieure. Pour plus d’informations, consultez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

## <a name="create-a-new-pool"></a>Créer un nouveau pool

L’applet de commande [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) crée un nouveau pool. Les valeurs d’eDTU par pool, min et max Dtus sont limitées par la valeur de niveau de service (basic, standard ou premium). Consultez [eDTU et stockage limite élastiques pools de bases de données et élastiques](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>Créer une nouvelle base de données élastique dans un pool

Utilisez l’applet de commande [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) et définir le paramètre **ElasticPoolName** dans le pool cible. Pour déplacer une base de données existante dans un pool, voir [déplacer une base de données dans un pool élastique](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>Créer un pool et le remplir avec plusieurs nouvelles bases de données 

Création d’un grand nombre de bases de données dans un pool peut être lorsque terminé à l’aide d’un portail du ou des applets de commande PowerShell ne créer qu’une seule base de données à la fois. Pour automatiser la création dans un nouveau pool, reportez-vous à [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="example-create-a-pool-using-powershell"></a>Exemple : création d’un pool à l’aide de PowerShell 

Ce script crée un nouveau groupe de ressources Azure et un nouveau serveur. Lorsque vous y êtes invité, fournissez un nom d’utilisateur administrateur et le mot de passe pour le nouveau serveur (pas vos informations d’identification Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>Étapes suivantes

- [Gérer votre pool](sql-database-elastic-pool-manage-powershell.md)
- [Créer des travaux élastiques](sql-database-elastic-jobs-overview.md) Travaux élastiques vous permettent d’exécuter les scripts T-SQL par rapport à n’importe quel nombre de bases de données dans le pool.
- [Évolution horizontale avec la base de données de SQL Azure](sql-database-elastic-scale-introduction.md): outils élastique de la base de données à l’horizontale.

