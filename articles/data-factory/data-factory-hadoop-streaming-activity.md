<properties 
    pageTitle="Activité de diffusion en continu d’Hadoop" 
    description="Découvrez comment vous pouvez utiliser l’activité de diffusion en continu d’Hadoop dans une usine de données Azure pour exécuter des programmes de diffusion en continu de Hadoop sur un cluster de HDInsight sur la demande/votre propre." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="shlo"/>

# <a name="hadoop-streaming-activity"></a>Activité de diffusion en continu d’Hadoop
> [AZURE.SELECTOR]
[La ruche](data-factory-hive-activity.md)  
[Porcs](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop en flux continu](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[La procédure stockée](data-factory-stored-proc-activity.md)
[Données lac Analytique U-SQL](data-factory-usql-activity.md)
[.NET personnalisés](data-factory-use-custom-activities.md)

Vous pouvez utiliser l’activité HDInsightStreamingActivity invoquer un travail Hadoop de diffusion en continu à partir d’un pipeline Azure Data Factory. L’extrait JSON suivant montre la syntaxe d’utilisation de la HDInsightStreamingActivity dans un fichier JSON de pipeline. 

L’activité de diffusion en continu de HDInsight dans un [pipeline](data-factory-create-pipelines.md) de données Factory exécute Hadoop de diffusion en continu des programmes sur [votre propre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) serveur ou [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight de basés sur Windows/Linux cluster. Cet article s’appuie sur l’article [des activités de transformation de données](data-factory-data-transformation-activities.md) , qui présente une vue d’ensemble de la transformation de données et les activités de transformation pris en charge.

## <a name="json-sample"></a>Exemple JSON
Le cluster HDInsight est automatiquement rempli avec les données (davinci.txt) et les exemples de programmes (wc.exe et cat.exe). Par défaut, le nom du conteneur qui est utilisé par le cluster HDInsight est le nom du cluster. Par exemple, si votre nom de cluster est myhdicluster, le nom du conteneur blob associé serait myhdicluster. 

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<nameofthecluster>/example/apps/wc.exe",
                            "<nameofthecluster>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService",
                        "getDebugInfo": "Failure"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

Notez les points suivants :

1. La valeur du **linkedServiceName** le nom du service lié qui pointe vers votre cluster HDInsight sur lequel la tâche mapreduce en continu est exécutée.
2. Définir le type de l’activité à **HDInsightStreaming**.
3. Pour la propriété **Mappeur** , spécifiez le nom de l’exécutable du mappeur. Dans l’exemple, cat.exe est l’exécutable du mappeur.
4. Pour la propriété **réducteur** , spécifiez le nom de l’exécutable du réducteur. Dans l’exemple, wc.exe est l’exécutable du réducteur.
5. Pour la propriété de type **d’entrée** , spécifiez le fichier d’entrée (y compris l’emplacement) pour le mappeur. Dans l’exemple : "wasb://adfsample@ <account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt » : adfsample est le conteneur de l’objet blob et exemple/données/Gutenberg est le dossier davinci.txt est l’objet blob.
6. Pour la propriété type de **sortie** , spécifiez le fichier de sortie (y compris l’emplacement) pour le réducteur. La sortie de la tâche de diffusion en continu de Hadoop est écrite à l’emplacement spécifié pour cette propriété.
7. Dans la section **filePaths** , spécifiez les chemins d’accès pour les exécutables du Mappeur et réducteur. Dans l’exemple : « adfsample/example/apps/wc.exe », adfsample est le conteneur de l’objet blob, exemple/apps est le dossier, et wc.exe est le fichier exécutable.
8. Pour la propriété **fileLinkedService** , spécifiez le service de stockage Azure lié qui représente le stockage Azure qui contient les fichiers spécifiés dans la section filePaths.
9. Pour la propriété **arguments** , spécifiez les arguments pour le travail en continu.
10. La propriété **getDebugInfo** est un élément facultatif. Lorsqu’elle est définie à échec, les journaux sont téléchargés uniquement en cas d’échec. Lorsqu’elle est définie à tous, les journaux sont toujours téléchargés, quelle que soit l’état d’exécution.

> [AZURE.NOTE] Comme indiqué dans l’exemple, vous spécifiez un groupe de données de sortie de l’activité de diffusion en continu Hadoop pour la propriété **sort** . Ce groupe de données est simplement un groupe de données fictive qui est nécessaire pour réaliser la planification de pipeline. Vous n’avez pas besoin de spécifier n’importe quel groupe de données d’entrée pour l’activité pour les **entrées** de propriété.  

    
## <a name="example"></a>Exemple
Le pipeline dans cette procédure pas à pas s’exécute le programme de mappage/réduction en continu de statistiques sur votre cluster Azure HDInsight. 

### <a name="linked-services"></a>Services liés

#### <a name="azure-storage-linked-service"></a>Service de stockage lié Azure
Tout d’abord, vous créez un service lié pour lier le stockage Azure qui est utilisé par le cluster Azure HDInsight sur le factory de données Azure. Si vous copiez/collez le code suivant, n’oubliez pas de remplacer le nom de compte et clé de compte avec le nom et la clé de votre stockage Azure. 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### <a name="azure-hdinsight-linked-service"></a>Service de HDInsight lié Azure
Ensuite, vous créez un service lié pour lier votre cluster Azure HDInsight sur le factory de données Azure. Si vous copiez/collez le code suivant, remplacer le nom de cluster HDInsight avec le nom de votre cluster de HDInsight et modifier les valeurs nom et mot de passe utilisateur. 
    
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }

### <a name="datasets"></a>Groupes de données

#### <a name="output-dataset"></a>Dataset de sortie
Le pipeline dans cet exemple ne prend pas les entrées. Vous spécifiez un groupe de données de sortie de l’activité de diffusion en continu de HDInsight. Ce groupe de données est simplement un groupe de données fictive qui est nécessaire pour réaliser la planification de pipeline. 

    {
        "name": "StreamingOutputDataset",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/streamingdata/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                },
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Pipeline

Le pipeline dans cet exemple n'a qu’une seule activité de type : **HDInsightStreaming**. 

Le cluster HDInsight est automatiquement rempli avec les données (davinci.txt) et les exemples de programmes (wc.exe et cat.exe). Par défaut, le nom du conteneur qui est utilisé par le cluster HDInsight est le nom du cluster. Par exemple, si votre nom de cluster est myhdicluster, le nom du conteneur blob associé serait myhdicluster.  

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<blobcontainer>/example/apps/wc.exe",
                            "<blobcontainer>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

## <a name="see-also"></a>Voir aussi
- [Activité de la ruche](data-factory-hive-activity.md)
- [Activité de porc](data-factory-pig-activity.md)
- [MapReduce activité](data-factory-map-reduce.md)
- [Appeler des programmes d’allumage](data-factory-spark.md)
- [Appeler des scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

