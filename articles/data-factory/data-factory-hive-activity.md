<properties 
    pageTitle="Activité de la ruche" 
    description="Découvrez comment vous pouvez utiliser l’activité de la ruche dans une usine de données Azure pour exécuter des requêtes de la ruche sur un cluster de HDInsight sur la demande/votre propre." 
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
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="hive-activity"></a>Activité de la ruche
> [AZURE.SELECTOR]
[La ruche](data-factory-hive-activity.md)  
[Porcs](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop en flux continu](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[La procédure stockée](data-factory-stored-proc-activity.md)
[Données lac Analytique U-SQL](data-factory-usql-activity.md)
[.NET personnalisés](data-factory-use-custom-activities.md)

L’activité de la ruche de HDInsight dans un [pipeline](data-factory-create-pipelines.md) de données Factory exécute des requêtes de ruche sur cluster de basés sur Windows/Linux de HDInsight [votre propre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Cet article s’appuie sur l’article [des activités de transformation de données](data-factory-data-transformation-activities.md) , qui présente une vue d’ensemble de la transformation de données et les activités de transformation pris en charge.

## <a name="syntax"></a>Syntaxe

    {
        "name": "Hive Activity",
        "description": "description",
        "type": "HDInsightHive",
        "inputs": [
          {
            "name": "input tables"
          }
        ],
        "outputs": [
          {
            "name": "output tables"
          }
        ],
        "linkedServiceName": "MyHDInsightLinkedService",
        "typeProperties": {
          "script": "Hive script",
          "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
    }
    
## <a name="syntax-details"></a>Détails de la syntaxe

Propriété | Description | Obligatoire
-------- | ----------- | --------
nom | Nom de l’activité | Oui
Description | Texte décrivant l’activité qui est utilisée pour | N°
type de | HDinsightHive | Oui
entrées | Intrants consommés par l’activité de la ruche | N°
sorties | Sorties produites par l’activité de la ruche | Oui 
linkedServiceName | Référence au cluster HDInsight enregistré comme service lié dans Data Factory | Oui 
script | Spécifier le script de ruche en ligne | N°
chemin d’accès du script | Stockez le script de la ruche dans un stockage blob Azure et fournir le chemin d’accès au fichier. Utilisez la propriété 'script' ou « scriptPath ». Les deux ne peuvent pas être utilisés ensemble. Le nom de fichier respecte la casse. | N° 
définit | Spécifiez les paramètres sous forme de paires clé/valeur pour le référencement dans le script de la ruche à l’aide de 'hiveconf'  | N°

## <a name="example"></a>Exemple

Prenons un exemple de jeu journaux analytique, dans lequel vous souhaitez identifier le temps passé par les utilisateurs de jeux lancée par votre société. 

Le journal suivant est un exemple jeu de journal, qui est une virgule (`,`) séparés et contient les champs suivants : ProfileID, SessionStart, durée, SrcIPAddress et le type de partie.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

Le **script de la ruche** pour traiter ces données :

    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID       string, 
        SessionStart    string, 
        Duration        int, 
        SrcIPAddress    string, 
        GameType        string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
    
    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (   
        ProfileID   string, 
        Duration    int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
    
    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID

Pour exécuter ce script de ruche dans un pipeline de données usine, vous devez effectuer les opérations suivantes

1. Création d’un service lié pour enregistrer [vos propres HDInsight compute cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou de configurer le [cluster de calcul à la demande HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Nous allons appeler ce service lié « HDInsightLinkedService ».
2. Création d’un [service lié](data-factory-azure-blob-connector.md) pour configurer la connexion au stockage Azure Blob qui héberge les données. Nous allons appeler ce service lié « StorageLinkedService »
3. Créer [des groupes de données](data-factory-create-datasets.md) pointant vers l’entrée et les données de sortie. Nous allons appeler le dataset d’entrée « HiveSampleIn » et le dataset de sortie « HiveSampleOut »
4. Copie de la requête de la ruche en tant que fichier de stockage des objets Blob Azure configuré à l’étape #2. Si le stockage pour héberger les données est différent de celui qui héberge le fichier de requête, créer un service de stockage Azure lié distinct et la référencer dans l’activité. **ScriptPath **permet de spécifier le chemin d’accès au fichier de requête de ruche et **scriptLinkedService** pour spécifier le stockage Azure qui contient le fichier de script. 

    > [AZURE.NOTE] Vous pouvez également fournir la ruche de script inline dans la définition de l’activité à l’aide de la propriété **script** . Nous ne recommandons pas cette approche en tant que tous les caractères spéciaux dans le script dans le document JSON doit être échappés et peut entraîner des problèmes de débogage. La meilleure pratique consiste à suivre l’étape #4.
5.  Créer un pipeline à l’activité de la HDInsightHive. L’activité de processus/transformations de données.

        {
          "name": "HiveActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                  {
                    "name": "HiveSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "HiveSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
              }
            ]
          }
        }

6.  Déployer le pipeline. Consultez l’article de [pipelines de création](data-factory-create-pipelines.md) pour plus d’informations. 
7.  Surveiller le tuyau en utilisant les vues de surveillance et de gestion en usine de données. Consultez [analyse et de gérer les pipelines de données usine](data-factory-monitor-manage-pipelines.md) article pour plus de détails. 


## <a name="specifying-parameters-for-a-hive-script"></a>Spécification des paramètres d’un script de la ruche  
Dans cet exemple, le jeu de journaux sont ingérées quotidiennement dans le stockage Blob Azure et sont stockés dans un dossier partitionné avec la date et l’heure. Vous souhaitez paramétrer le script de la ruche et passer dynamiquement de l’emplacement du dossier d’entrée lors de l’exécution et également de produire la sortie partitionnée avec la date et l’heure.

Pour utiliser le script de ruche paramétré, effectuez les opérations suivantes

- Définissez les paramètres dans la **définit**.

        {
            "name": "HiveActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "HiveActivitySample",
                    "type": "HDInsightHive",
                    "inputs": [
                        {
                            "name": "HiveSampleIn"
                          }
                    ],
                    "outputs": [
                        {
                            "name": "HiveSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                }
            ]
          }
        }

- Dans le Script de la ruche, consultez le paramètre à l’aide de **${hiveconf:parameterName}**. 

        DROP TABLE IF EXISTS HiveSampleIn; 
        CREATE EXTERNAL TABLE HiveSampleIn 
        (
            ProfileID   string, 
            SessionStart    string, 
            Duration    int, 
            SrcIPAddress    string, 
            GameType    string
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
        
        DROP TABLE IF EXISTS HiveSampleOut; 
        CREATE EXTERNAL TABLE HiveSampleOut 
        (
            ProfileID   string, 
            Duration    int
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
        
        INSERT OVERWRITE TABLE HiveSampleOut
        Select 
            ProfileID,
            SUM(Duration)
        FROM HiveSampleIn Group by ProfileID


## <a name="see-also"></a>Voir aussi
- [Activité de porc](data-factory-pig-activity.md)
- [MapReduce activité](data-factory-map-reduce.md)
- [Activité de diffusion en continu d’Hadoop](data-factory-hadoop-streaming-activity.md)
- [Appeler des programmes d’allumage](data-factory-spark.md)
- [Appeler des scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)









