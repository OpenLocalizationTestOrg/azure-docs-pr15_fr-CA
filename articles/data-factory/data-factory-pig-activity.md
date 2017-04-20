<properties 
    pageTitle="Activité de porc" 
    description="Découvrez comment vous pouvez utiliser l’activité de porcs dans une usine de données Azure pour exécuter des scripts de porc sur un cluster de HDInsight sur la demande/votre propre." 
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
    ms.date="08/31/2016" 
    ms.author="shlo"/>

# <a name="pig-activity"></a>Activité de porc
> [AZURE.SELECTOR]
[La ruche](data-factory-hive-activity.md)  
[Porcs](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop en flux continu](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[La procédure stockée](data-factory-stored-proc-activity.md)
[Données lac Analytique U-SQL](data-factory-usql-activity.md)
[.NET personnalisés](data-factory-use-custom-activities.md)

L’activité HDInsight porc dans une usine de données [pipeline](data-factory-create-pipelines.md) exécute des requêtes de porc sur [votre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) serveur ou [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight de basés sur Windows/Linux cluster. Cet article s’appuie sur l’article [des activités de transformation de données](data-factory-data-transformation-activities.md) , qui présente une vue d’ensemble de la transformation de données et les activités de transformation pris en charge.

## <a name="syntax"></a>Syntaxe

    {
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "Pig Activity",
                "description": "description",
                "type": "HDInsightPig",
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
                    "script": "Pig script",
                    "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                    "defines": {
                        "param1": "param1Value"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
            }
        ]
      }
    }

## <a name="syntax-details"></a>Détails de la syntaxe

Propriété | Description | Obligatoire
-------- | ----------- | --------
nom | Nom de l’activité | Oui
Description | Texte décrivant l’activité qui est utilisée pour | N°
type de | HDinsightPig | Oui
entrées | Une ou plusieurs entrées consommées par l’activité du porc | N°
sorties | Une ou plusieurs sorties produites par l’activité du porc | Oui
linkedServiceName | Référence au cluster HDInsight enregistré comme service lié dans Data Factory | Oui
script | Spécifier le script de porc en ligne | N°
chemin d’accès du script | Stockez le script de porcs dans un stockage blob Azure et fournir le chemin d’accès au fichier. Utilisez la propriété 'script' ou « scriptPath ». Les deux ne peuvent pas être utilisés ensemble. Le nom de fichier respecte la casse. | N°
définit | Spécifiez les paramètres sous forme de paires clé/valeur pour le référencement dans le script de porc | N°

## <a name="example"></a>Exemple

Prenons un exemple de jeu journaux analytique, dans lequel vous souhaitez identifier le temps passé par les joueurs de jouer à des jeux lancés par votre société.
 
L’exemple de journal jeu suivant est un fichier séparés par des virgules (,). Il contient les champs suivants : ProfileID, SessionStart, durée, SrcIPAddress et le type de partie.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

Le **script de porc** pour traiter ces données :

    PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    
    GroupProfile = Group PigSampleIn all;
    
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    
    Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Pour exécuter ce script de porcs dans un pipeline de données usine, effectuez les opérations suivantes :

1. Création d’un service lié pour enregistrer [vos propres HDInsight compute cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou de configurer le [cluster de calcul à la demande HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Nous allons appeler ce service lié **HDInsightLinkedService**.
2.  Création d’un [service lié](data-factory-azure-blob-connector.md) pour configurer la connexion au stockage Azure Blob qui héberge les données. Nous allons appeler ce service lié **StorageLinkedService**.
3.  Créer [des groupes de données](data-factory-create-datasets.md) pointant vers l’entrée et les données de sortie. Nous allons appeler le dataset d’entrée **PigSampleIn** et le dataset de sortie **PigSampleOut**.
4.  Copiez la requête de porcs dans un fichier du stockage Blob Azure configuré à l’étape #2. Si le stockage Azure qui héberge les données est différent de celui qui héberge le fichier de requête, créez un service de stockage Azure lié distinct. Consultez le service lié dans la configuration de l’activité. **ScriptPath **permet de spécifier le chemin d’accès au fichier de script de porc et **scriptLinkedService**. 
    
    > [AZURE.NOTE] Vous pouvez également fournir des porcs script inline dans la définition de l’activité à l’aide de la propriété **script** . Toutefois, nous ne recommandons pas cette approche en tant que tous les caractères spéciaux dans le script doit être d’échappement et peuvent provoquer des problèmes de débogage. La meilleure pratique consiste à suivre l’étape #4.
5. Créer le pipeline à l’activité de la HDInsightPig. Cette activité traite les données d’entrée en exécutant le script de porc sur le cluster de HDInsight.

        {
          "name": "PigActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                  {
                    "name": "PigSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "PigSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
              }
            ]
          }
        } 
6. Déployer le pipeline. Consultez l’article de [pipelines de création](data-factory-create-pipelines.md) pour plus d’informations. 
7. Surveiller le tuyau en utilisant les vues de surveillance et de gestion en usine de données. Consultez [analyse et de gérer les pipelines de données usine](data-factory-monitor-manage-pipelines.md) article pour plus de détails.

## <a name="specifying-parameters-for-a-pig-script"></a>Spécification des paramètres d’un script de porc 

Prenons l’exemple suivant : jeu de journaux sont ingérées quotidiennement dans le stockage Blob Azure et stockés dans un dossier partitionnée date sur la base et l’heure. Vous souhaitez paramétrer le script de porc et passer dynamiquement de l’emplacement du dossier d’entrée lors de l’exécution et également de produire la sortie partitionnée avec la date et l’heure.
 
Pour utiliser le script de porc paramétré, effectuez les opérations suivantes :

- Définissez les paramètres dans la **définit**.

        {
            "name": "PigActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "PigActivitySample",
                    "type": "HDInsightPig",
                    "inputs": [
                        {
                            "name": "PigSampleIn"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "PigSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    }
                }
            ]
          }
        }  
      
- Dans le Script de porc, reportez-vous aux paramètres à l’aide de '**$parameterName**' comme indiqué dans l’exemple suivant :

        PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);   
        GroupProfile = Group PigSampleIn all;       
        PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);      
        Store PigSampleOut into '$Output' USING PigStorage (','); 


## <a name="see-also"></a>Voir aussi
- [Activité de la ruche](data-factory-hive-activity.md)
- [MapReduce activité](data-factory-map-reduce.md)
- [Activité de diffusion en continu d’Hadoop](data-factory-hadoop-streaming-activity.md)
- [Appeler des programmes d’allumage](data-factory-spark.md)
- [Appeler des scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


