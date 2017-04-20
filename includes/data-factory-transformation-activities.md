Usine de données Azure prend en charge les activités transformation suivantes qui peuvent être ajoutées à des tuyaux soit individuellement ou en chaîne avec une autre activité.

Activité de transformation de données |  Environnement informatique 
:----------------------- | :--------------------
[La ruche](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Porcs](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop en continu](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Machine d’activités de formation : l’exécution du lot et des ressources de mise à jour](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[Procédure stockée](../articles/data-factory/data-factory-stored-proc-activity.md) | SQL Azure, SQL Azure Data Warehouse ou de SQL Server |
[Données Analytique de LAC U-SQL](../articles/data-factory/data-factory-usql-activity.md) | Données Azure lac Analytique 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou un lot d’Azure
   
> [AZURE.NOTE] 
> Vous pouvez utiliser MapReduce activité pour exécuter des programmes d’allumage sur votre cluster HDInsight Spark. Pour plus d’informations, reportez-vous à la section [programmes appeler une étincelle provenant d’une usine de données Azure](../articles/data-factory/data-factory-spark.md) .
> Vous pouvez créer une activité personnalisée pour exécuter des scripts de R sur votre cluster HDInsight avec R installé. Reportez-vous [à l’aide d’exécuter le Script R Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).