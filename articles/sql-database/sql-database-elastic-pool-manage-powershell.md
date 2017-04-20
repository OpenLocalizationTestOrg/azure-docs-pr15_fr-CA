<properties 
    pageTitle="Gérer un pool de la base de données élastique (PowerShell) | Microsoft Azure" 
    description="Découvrez comment utiliser PowerShell pour gérer un pool élastique de la base de données."  
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/22/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-powershell"></a>Surveiller et de gérer un pool de la base de données élastique avec PowerShell 

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Gérer un [pool d’élastique de la base de données](sql-database-elastic-pool.md) à l’aide des applets de commande PowerShell. 

Pour les codes d’erreur courants, voir [des codes d’erreur SQL pour les applications de client de base de données SQL : base de données d’erreur de connexion et d’autres problèmes](sql-database-develop-error-messages.md).

Valeurs de pools, vous pouvez trouver dans [les limites de stockage et d’eDTU](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases). 

## <a name="prerequisites"></a>Conditions préalables

* PowerShell Azure 1.0 ou version ultérieure. Pour plus d’informations, consultez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).
* Les pools élastique de la base de données sont uniquement disponibles avec les serveurs SQL de base de données V12. Si vous disposez d’un serveur SQL de base de données V11, [utiliser PowerShell pour mettre à niveau vers V12 et créer un pool](sql-database-upgrade-server-portal.md) en une seule étape.


## <a name="move-a-database-into-an-elastic-pool"></a>Déplacer une base de données dans un pool élastique

Vous pouvez déplacer une base de données vers ou à partir d’un pool avec [AzureRmSqlDatabase de l’ensemble](https://msdn.microsoft.com/library/azure/mt619433.aspx). 

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-a-pool"></a>Modifier les paramètres de performance d’un pool

Lorsque les performances sont amoindries, vous pouvez modifier les paramètres du pool pour l’adapter à la croissance. Utilisez l’applet de commande [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) . Définir le paramètre - Dtu à l’eDTUs par pool. Reportez-vous à la section [limites d’eDTU et de stockage](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases) pour les valeurs possibles.  

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## <a name="get-the-status-of-pool-operations"></a>Obtenir l’état des opérations du pool

Création d’un pool peut prendre un temps. Pour suivre l’état des opérations, y compris la création et les mises à jour, utilisez l’applet de commande [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) .

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## <a name="get-the-status-of-moving-an-elastic-database-into-and-out-of-a-pool"></a>Obtenir l’état du déplacement d’une base de données élastique vers et à partir d’un pool

Déplacement d’une base de données peut prendre un temps. Suivi un déplacement à l’aide de l’applet de commande [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) .

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-a-pool"></a>Obtenir des données d’utilisation des ressources d’un pool

Mesures qui peuvent être récupérés sous forme de pourcentage de la limite de pool de ressources :   


| Nom de mesure | Description |
| :-- | :-- |
| processeur\_% | Utilisation de compute moyenne en pourcentage de la limite du pool. |
| physique\_données\_lecture\_% | Utilisation d’e/s moyenne en pourcentage, en fonction de la limite du pool. |
| journal des\_écrire\_% | L’utilisation des ressources d’écriture moyenne en pourcentage de la limite du pool. | 
| DTU\_consommation\_% | Utilisation d’eDTU moyenne en pourcentage de la limite d’eDTU pour le pool | 
| stockage\_% | Utilisation des moyens de stockage sous forme de pourcentage de la limite de stockage du pool. |  
| travailleurs\_% | Travailleurs simultanés maximum (demandes) sous forme de pourcentage, en fonction de la limite du pool. |  
| sessions\_% | Nombre maximal de sessions simultané en pourcentage, en fonction de la limite du pool. | 
| eDTU_limit | Max pool élastique DTU paramètre actuel pour ce pool élastique pendant cet intervalle. |
| stockage\_limite | Limite de stockage max pool élastique en cours pour ce pool élastique en mégaoctets la définition au cours de cet intervalle. |
| eDTU\_utilisé | EDTUs moyen utilisé par le pool dans cet intervalle. |
| stockage\_utilisé | Stockage moyen utilisé par le pool dans cet intervalle en octets |

**Périodes de granularité et de rétention de mesures :**

* Données seront renvoyées au niveau de granularité de 5 minutes.  
* Rétention des données est de 35 jours.  

Cette applet de commande et les API limite le nombre de lignes qui peuvent être récupérées dans un seul appel à 1000 lignes (environ 3 jours de données au niveau de granularité de 5 minutes). Mais cette commande peut être appelée plusieurs fois avec des intervalles de temps de début et de fin pour récupérer le plus de données 

Pour récupérer les mesures :

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## <a name="get-resource-usage-data-for-an-elastic-database"></a>Obtenir des données d’utilisation des ressources pour une base de données élastique

Ces API est les mêmes que les API (V12) actuels utilisés pour la surveillance de l’utilisation des ressources d’une base de données autonome, à la différence de sémantique suivante. 

De cette API, les mesures récupérées sont exprimées sous la forme d’un pourcentage de la par eDTUs max (ou bouchon équivalent pour la mesure sous-jacent comme une UC, d’e/s etc.) définie pour ce pool. Par exemple, 50 % d’utilisation d’une de ces mesures indique que la consommation de ressource spécifique est à 50 % de la limite de cache de base de données pour cette ressource dans le pool parent par. 

Pour récupérer les mesures :

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## <a name="add-an-alert-to-a-pool-resource"></a>Ajouter une alerte à une ressource de pool

Vous pouvez ajouter des règles d’alerte pour les ressources pour envoyer des notifications par courrier électronique ou les chaînes d’alerte aux [points de terminaison URL](https://msdn.microsoft.com/library/mt718036.aspx) lorsque la ressource atteint un seuil d’utilisation que vous définissez. Utilisez l’applet de commande Add-AzureRmMetricAlertRule. 

> [AZURE.IMPORTANT]L’utilisation des ressources pour les Pools élastique de surveillance a un retard de 20 minutes au moins. Définition d’alertes de moins de 30 minutes pour les Pools élastique n’est actuellement pas pris en charge. Toutes les alertes définies pour les Pools élastique avec une période (paramètre appelé «-WindowSize » dans les API PowerShell) de moins de 30 minutes ne peut pas être déclenché. Veuillez vous assurer que les alertes que vous définissez pour les Pools élastique utilisent une période (WindowSize) de 30 minutes ou plus.

Cet exemple ajoute une alerte pour recevoir une notification lorsque la consommation d’un pool eDTU dépasse certains seuils.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail 

## <a name="add-alerts-to-all-databases-in-a-pool"></a>Ajouter des alertes pour toutes les bases de données dans un pool

Vous pouvez ajouter des règles d’alerte à une base de données dans un pool élastique pour envoyer des notifications par courrier électronique ou les chaînes d’alerte pour les [points de terminaison URL](https://msdn.microsoft.com/library/mt718036.aspx) lorsqu’une ressource atteint un seuil d’utilisation défini par l’alerte. 

> [AZURE.IMPORTANT] L’utilisation des ressources pour les Pools élastique de surveillance a un retard de 20 minutes au moins. Définition d’alertes de moins de 30 minutes pour les Pools élastique n’est actuellement pas pris en charge. Toutes les alertes définies pour les Pools élastique avec une période (paramètre appelé «-WindowSize » dans les API PowerShell) de moins de 30 minutes ne peut pas être déclenché. Veuillez vous assurer que les alertes que vous définissez pour les Pools élastique utilisent une période (WindowSize) de 30 minutes ou plus.

Cet exemple ajoute une alerte pour chacune des bases de données dans un pool pour recevoir une notification lorsque la consommation de cette base de données DTU dépasse certains seuils.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    } 



## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>Collecter et analyser les données d’utilisation des ressources sur plusieurs pools dans un abonnement

Lorsque vous avez un grand nombre de bases de données dans un abonnement, il est difficile à contrôler chaque pool élastique séparément. Au lieu de cela, les applets de commande PowerShell de base de données SQL et les requêtes T-SQL peuvent être combinés pour collecter les données d’utilisation des ressources de plusieurs pools et de leurs bases de données pour la surveillance et l’analyse de l’utilisation des ressources. Vous trouverez un [exemple d’implémentation](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) d’un tel jeu de scripts powershell dans le référentiel d’exemples GitHub SQL Server et la documentation sur ce qu’il fait et comment l’utiliser.

Pour utiliser cet exemple d’implémentation, procédez comme suit ci-dessous.


1. Téléchargez [documentation et des scripts](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools):
2. Modifier les scripts pour votre environnement. Spécifiez un ou plusieurs serveurs qui héberge les pools élastiques.
3. Spécifiez une base de données de télémétrie où les métriques collectés doivent être stockés. 
4. Personnaliser le script pour spécifier la durée d’exécution de scripts.

À un niveau élevé, les scripts effectuent les opérations suivantes :

*   Énumère tous les serveurs dans un abonnement Azure donné (ou une liste spécifiée de serveurs).
*   Exécute une tâche d’arrière-plan pour chaque serveur. La tâche s’exécute dans une boucle à intervalles réguliers et collecte les données de télémétrie pour tous les pools dans le serveur. Il charge ensuite les données collectées dans la base de données de télémétrie spécifié.
*   Énumère une liste de bases de données de chaque pool pour collecter les données d’utilisation de ressources de base de données. Il charge ensuite les données collectées dans la base de données de télémétrie.

Mesures dans la base de données de télémétrie recueillies peuvent être analysées pour surveiller la santé des pools élastiques et les bases de données qu’il contient. Le script installe également une prédéfinis fonction Table (TVF) dans la base de données de télémétrie pour aider l’agrégat à la métrique d’une fenêtre de temps spécifiée. Par exemple, les résultats de la TVF peuvent être utilisés pour afficher « top N élastiques pools avec l’utilisation d’eDTU maximale dans un laps de temps donné. » Utilisez éventuellement des outils d’analyse tels que Excel ou alimentation BI pour interroger et analyser les données collectées.

## <a name="example-retrieve-resource-consumption-metrics-for-a-pool-and-its-databases"></a>Exemple : récupérer les mesures de la consommation de ressources pour un pool et ses bases de données

Cet exemple extrait les mesures de la consommation pour un pool élastique donné et toutes ses bases de données. Les données collectées sont mis en forme et l’écrit dans un fichier au format .csv. Le fichier peut être parcouru avec Excel.

    $subscriptionId = '<Azure subscription id>'       # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name
        
    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC
    
    # Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
    
    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
    
    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }
    
    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }
    
    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
    
    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## <a name="latency-of-elastic-pool-operations"></a>Latence des opérations élastique

- Généralement la modification de l’eDTUs min par eDTUs de base de données ou max par base de données se termine dans les 5 minutes ou moins.
- La modification de l’eDTUs par pool dépend de la quantité totale d’espace utilisé par toutes les bases de données dans le pool. Modifications moyenne de 90 minutes ou moins par 100 Go. Par exemple, si l’espace total utilisé par toutes les bases de données dans le pool est 200 Go, puis la latence attendue pour la modification de l’eDTU du pool par pool est de 3 heures ou moins.

## <a name="migrate-from-v11-to-v12-servers"></a>Migrer à partir de la V11, V12 serveurs

Applets de commande PowerShell sont disponibles pour démarrer, arrêter ou contrôler une mise à niveau à V12 de base de données SQL Azure V11 ou toute autre version pré-V12.

- [Mise à niveau V12 de base de données SQL à l’aide de PowerShell](sql-database-upgrade-server-powershell.md)

Pour une documentation de référence sur ces applets de commande PowerShell, voir :


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Démarrer-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


L’applet de commande Stop signifie Annuler, de ne pas attendre. Il n’y a aucune manière de reprendre une mise à niveau, que vous devez redémarrer à partir du début. L’applet de commande Stop nettoie et libère toutes les ressources appropriées.

## <a name="next-steps"></a>Étapes suivantes

- [Créer des travaux élastiques](sql-database-elastic-jobs-overview.md) Travaux élastiques vous permettent d’exécuter les scripts T-SQL par rapport à n’importe quel nombre de bases de données dans le pool.
- Reportez-vous à la section [mise à l’échelle des base de données de SQL Azure](sql-database-elastic-scale-introduction.md): outils de base de données élastique permet d’horizontale, déplacer des données, de requête ou de créer des transactions.
