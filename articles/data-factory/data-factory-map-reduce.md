<properties 
    pageTitle="Appeler le programme MapReduce provenant d’une usine de données Azure" 
    description="Découvrez comment traiter les données en exécutant des programmes de MapReduce sur un cluster Azure HDInsight à partir d’une fabrique de données Azure." 
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
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="invoke-mapreduce-programs-from-data-factory"></a>Appeler des programmes MapReduce provenant d’une usine de données
> [AZURE.SELECTOR]
[La ruche](data-factory-hive-activity.md)  
[Porcs](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop en flux continu](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[La procédure stockée](data-factory-stored-proc-activity.md)
[Données lac Analytique U-SQL](data-factory-usql-activity.md)
[.NET personnalisés](data-factory-use-custom-activities.md)

L’activité HDInsight MapReduce dans un [pipeline](data-factory-create-pipelines.md) de données Factory exécute MapReduce programmes sur [votre propre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) serveur ou [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight de basés sur Windows/Linux cluster. Cet article s’appuie sur l’article [des activités de transformation de données](data-factory-data-transformation-activities.md) , qui présente une vue d’ensemble de la transformation de données et les activités de transformation pris en charge.

## <a name="introduction"></a>Introduction 
Un tuyau dans une usine de données Azure traite les données dans les services de stockage à l’aide des services de calcul lié. Il contient une séquence d’activités dans lequel chaque activité effectue une opération de traitement spécifiques. Cet article décrit l’utilisation de l’activité de MapReduce HDInsight.
 
Pour plus d’informations sur l’exécution des scripts de porc/ruche sur un cluster basé sur Windows/Linux de HDInsight d’une canalisation à l’aide des activités de porc de HDInsight et de la ruche, reportez-vous à la section de [porc](data-factory-pig-activity.md) et de [la ruche](data-factory-hive-activity.md) . 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON pour l’activité de HDInsight MapReduce 

Dans la définition de JSON pour l’activité de la HDInsight : 
 
1. Définir le **type** de l' **activité** à **HDInsight**.
3. Spécifiez le nom de la classe pour la propriété **className** .
4. Spécifiez le chemin d’accès pour le fichier JAR, y compris le nom de fichier pour la propriété du **jarFilePath** .
5. Spécifier le service lié qui fait référence au stockage Blob Azure qui contient le fichier JAR **jarLinkedService** propriété.   
6. Spécifiez les arguments du programme MapReduce dans la section **arguments** . Lors de l’exécution, vous voyez plusieurs arguments supplémentaires (par exemple : mapreduce.job.tags) à partir du framework MapReduce. Pour différencier vos arguments avec les arguments MapReduce, utilisez option et la valeur en tant qu’arguments comme indiqué dans l’exemple suivant (- s,--entrée,--sortie, etc., sont des options de suivi immédiatement par leurs valeurs).

        {
            "name": "MahoutMapReduceSamplePipeline",
            "properties": {
                "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
                "activities": [
                    {
                        "type": "HDInsightMapReduce",
                        "typeProperties": {
                            "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                            "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                            "jarLinkedService": "StorageLinkedService",
                            "arguments": [
                                "-s",
                                "SIMILARITY_LOGLIKELIHOOD",
                                "--input",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                                "--output",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                                "--maxSimilaritiesPerItem",
                                "500",
                                "--tempDir",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                            ]
                        },
                        "inputs": [
                            {
                                "name": "MahoutInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "MahoutOutput"
                            }
                        ],
                        "policy": {
                            "timeout": "01:00:00",
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "MahoutActivity",
                        "description": "Custom Map Reduce to generate Mahout result",
                        "linkedServiceName": "HDInsightLinkedService"
                    }
                ],
                "start": "2014-01-03T00:00:00Z",
                "end": "2014-01-04T00:00:00Z",
                "isPaused": false,
                "hubName": "mrfactory_hub",
                "pipelineMode": "Scheduled"
            }
        }
    
    

Vous pouvez utiliser l’activité MapReduce HDInsight pour exécuter n’importe quel fichier jar de MapReduce sur un cluster HDInsight. Dans l’exemple suivant JSON de définition d’un pipeline, l’activité de la HDInsight est configurée pour exécuter un fichier JAR de Mahout.

## <a name="sample-on-github"></a>Exemple de GitHub
Vous pouvez télécharger un exemple d’utilisation de l’activité MapReduce HDInsight : [Exemples d’usine de données sur GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Exécution du programme de statistiques
Le pipeline dans cet exemple exécute le programme de mappage/réduction du nombre de mots sur votre cluster Azure HDInsight.   

### <a name="linked-services"></a>Services liés
Tout d’abord, vous créez un service lié pour lier le stockage Azure qui est utilisé par le cluster Azure HDInsight sur le factory de données Azure. Si vous copiez/collez le code suivant, n’oubliez pas de remplacer le **nom de compte** et **clé de compte** avec le nom et la clé de votre stockage Azure. 

#### <a name="azure-storage-linked-service"></a>Service de stockage lié Azure

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
Ensuite, vous créez un service lié pour lier votre cluster Azure HDInsight sur le factory de données Azure. Si vous copiez/collez le code suivant, remplacer le **nom de cluster HDInsight** avec le nom de votre cluster de HDInsight et modifier les valeurs nom et mot de passe utilisateur.   

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
Le pipeline dans cet exemple ne prend pas les entrées. Vous spécifiez un groupe de données de sortie de l’activité de MapReduce HDInsight. Ce groupe de données est simplement un groupe de données fictive qui est nécessaire pour réaliser la planification de pipeline.  

    {
        "name": "MROutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "WordCountOutput1.txt",
                "folderPath": "example/data/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Pipeline
Le pipeline dans cet exemple n'a qu’une seule activité de type : HDInsightMapReduce. Parmi les propriétés importantes dans le JSON sont les suivantes : 

Propriété | Notes
:-------- | :-----
type de | Le type doit être défini sur **HDInsightMapReduce**. 
nom de classe | Nom de la classe est : **wordcount**
jarFilePath | Chemin d’accès du fichier jar qui contient la classe. Si vous copiez/collez le code suivant, n’oubliez pas de modifier le nom du cluster. 
jarLinkedService | Service stockage lié Azure qui contient le fichier jar. Ce service lié fait référence à l’espace de stockage est associé au cluster HDInsight. 
arguments | Le programme wordcount prend deux arguments, une entrée et une sortie. Le fichier d’entrée est le fichier davinci.txt.
fréquence/intervalle | Les valeurs de ces propriétés correspondent le dataset de sortie. 
linkedServiceName | fait référence au service HDInsight lié que vous avez créé précédemment.   

    {
        "name": "MRSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run the Word Count Program",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "wordcount",
                        "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "/example/data/gutenberg/davinci.txt",
                            "/example/data/WordCountOutput1"
                        ]
                    },
                    "outputs": [
                        {
                            "name": "MROutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "MRActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-03T00:00:00Z",
            "end": "2014-01-04T00:00:00Z"
        }
    }

## <a name="run-spark-programs"></a>Exécuter des programmes d’allumage
Vous pouvez utiliser MapReduce activité pour exécuter des programmes d’allumage sur votre cluster HDInsight Spark. Pour plus d’informations, reportez-vous à la section [programmes appeler une étincelle provenant d’une usine de données Azure](data-factory-spark.md) .  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 
## <a name="see-also"></a>Voir aussi
- [Activité de la ruche](data-factory-hive-activity.md)
- [Activité de porc](data-factory-pig-activity.md)
- [Activité de diffusion en continu d’Hadoop](data-factory-hadoop-streaming-activity.md)
- [Appeler des programmes d’allumage](data-factory-spark.md)
- [Appeler des scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
