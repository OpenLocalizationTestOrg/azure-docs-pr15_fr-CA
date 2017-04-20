<properties
    pageTitle="Créer votre première fabrique de données (Gestionnaire de ressources du modèle) | Microsoft Azure"
    description="Dans ce didacticiel, vous créez un tuyau d’Azure Data Factory exemple à l’aide d’un modèle de gestionnaire de ressources Azure."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Didacticiel : Créer votre première fabrique de données Azure à l’aide du modèle de gestionnaire de ressources Azure
> [AZURE.SELECTOR]
- [Vue d’ensemble et des conditions préalables](data-factory-build-your-first-pipeline.md)
- [Azure portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modèle de gestionnaire de ressources](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Dans cet article, vous utilisez un modèle du Gestionnaire de ressources Azure pour créer votre première fabrique de données Azure.

## <a name="prerequisites"></a>Conditions préalables
- Lisez l’article de [Vue d’ensemble](data-factory-build-your-first-pipeline.md) et suivez les étapes de la **condition préalable** .
- Suivez les instructions de l’article de [procédure pour installer et configurer Azure PowerShell](../powershell-install-configure.md) pour installer la version la plus récente de Azure PowerShell sur votre ordinateur.
- Reportez-vous à la section [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md) pour en savoir plus sur les modèles du Gestionnaire de ressources Azure. 

## <a name="in-this-tutorial"></a>Dans ce didacticiel
Entité | Description  
------ | ----------- 
Service de stockage lié Azure | Lier votre compte de stockage Azure sur le factory de données. Le compte de stockage Azure conserve les données d’entrée et de sortie pour le pipeline dans cet exemple. 
Services liés à la demande de HDInsight| Liens une HDInsight à la demande sur le factory de données du cluster. Le cluster est créé automatiquement pour traiter les données et est supprimé une fois le traitement terminé.
Dataset d’entrée des Blob Azure | Fait référence au service de stockage Azure lié. Le service lié fait référence à un compte de stockage Azure et le groupe de données Azure Blob Spécifie le conteneur, le dossier et le nom du fichier dans le stockage qui contient les données d’entrée. 
Dataset de sortie Blob Azure | Fait référence au service de stockage Azure lié. Le service lié fait référence à un compte de stockage Azure et le groupe de données Azure Blob Spécifie le conteneur, le dossier et le nom du fichier dans le stockage qui contient les données de sortie. 
Pipeline de données | Le pipeline a une activité de type HDInsightHive utilise le groupe de données d’entrée et produit le dataset de sortie.   

Une fabrique de données peut avoir un ou plusieurs tuyaux. Un tuyau peut contenir une ou plusieurs activités. Il existe deux types d’activités : [activités de déplacement des données](data-factory-data-movement-activities.md) et des [activités de transformation des données](data-factory-data-transformation-activities.md). Dans ce didacticiel, vous créez un pipeline avec une activité (copie).

La section suivante fournit le modèle de gestionnaire de ressources complet pour la définition des entités de données usine afin que vous pouvez exécuter le didacticiel et tester le modèle rapidement. Pour comprendre comment chaque entité Data Factory est défini, consultez la section [Data Factory des entités dans le modèle](#data-factory-entities-in-the-template) .

## <a name="data-factory-json-template"></a>Modèle de données JSON d’usine
Le modèle de gestionnaire de ressources de niveau supérieur pour la définition d’une fabrique de données est : 

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": { ...
        },
        "variables": { ...
        },
        "resources": [
            {
                "name": "[parameters('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

Créez un fichier JSON nommé **ADFTutorialARM.json** dans le dossier **C:\ADFGetStarted** avec le contenu suivant :

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
            "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
            "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
            "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
            "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
            "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
            "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
            "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
            "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
        },
        "variables": {
            "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
            "azureStorageLinkedServiceName": "AzureStorageLinkedService",
            "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
            "blobInputDatasetName": "AzureBlobInput",
            "blobOutputDatasetName": "AzureBlobOutput",
            "pipelineName": "HiveTransformPipeline"
        },
        "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "2015-10-01",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "West US",
            "resources": [
            {
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "AzureStorage",
                    "description": "Azure Storage linked service",
                    "typeProperties": {
                        "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                    }
                }
            },
            {
                "type": "linkedservices",
                "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "HDInsightOnDemand",
                    "typeProperties": {
                        "clusterSize": 1,
                        "version": "3.2",
                        "timeToLive": "00:05:00",
                        "osType": "windows",
                        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('blobInputDatasetName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                    "typeProperties": {
                        "fileName": "[parameters('inputBlobName')]",
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "external": true
                }
            },
            {
                "type": "datasets",
                "name": "[variables('blobOutputDatasetName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Month",
                        "interval": 1
                    }
                }
            },
            {
                "type": "datapipelines",
                "name": "[variables('pipelineName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('hdInsightOnDemandLinkedServiceName')]",
                    "[variables('blobInputDatasetName')]",
                    "[variables('blobOutputDatasetName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "description": "Pipeline that transforms data using Hive script.",
                    "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                            "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                            "defines": {
                                "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                                "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                            }
                        },
                        "inputs": [
                            {
                                "name": "[variables('blobInputDatasetName')]"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "[variables('blobOutputDatasetName')]"
                            }
                        ],
                        "policy": {
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Month",
                            "interval": 1
                        },
                        "name": "RunSampleHiveActivity",
                        "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                    }
                    ],
                    "start": "2016-10-01T00:00:00Z",
                    "end": "2016-10-02T00:00:00Z",
                    "isPaused": false
                }
            }
            ]
        }
        ]
    }

> [AZURE.NOTE] Vous pouvez rechercher un autre exemple de modèle de gestionnaire de ressources pour la création d’une fabrique de données Azure sur [didacticiel : créer un pipeline avec une activité de copie à l’aide d’un modèle de gestionnaire de ressources Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md).  

## <a name="parameters-json"></a>Paramètres de JSON 
Créez un fichier JSON nommé **ADFTutorialARM-Parameters.json** qui contient les paramètres pour le modèle de gestionnaire de ressources Azure.  

> [AZURE.IMPORTANT] Spécifiez le nom et la clé de votre compte de stockage Azure pour les paramètres **storageAccountName** et **storageAccountKey** dans ce fichier de paramètres. 

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "storageAccountName": {
                "value": "<Name of your Azure Storage account>"
            },
            "storageAccountKey": {
                "value": "<Key of your Azure Storage account>"
            },
            "blobContainer": {
                "value": "adfgetstarted"
            },
            "inputBlobFolder": {
                "value": "inputdata"
            },
            "inputBlobName": {
                "value": "input.log"
            },
            "outputBlobFolder": {
                "value": "partitioneddata"
            },
            "hiveScriptFolder": {
                "value": "script"
            },
            "hiveScriptFile": {
                "value": "partitionweblogs.hql"
            }
        }
    }

> [AZURE.IMPORTANT] Vous pouvez avoir des fichiers JSON de paramètre distinct pour le développement, le test et les environnements de production que vous pouvez utiliser avec le même modèle de données usine JSON. À l’aide d’un script PowerShell, vous pouvez automatiser le déploiement entités Data Factory dans ces environnements. 

## <a name="create-data-factory"></a>Création de la fabrique de données

1. Démarrer **PowerShell d’Azure** et exécutez la commande suivante : 
    - Exécutez `Login-AzureRmAccount` et entrez le nom d’utilisateur et le mot de passe que vous utilisez pour vous connecter au portail Azure.  
    - Exécutez `Get-AzureRmSubscription` pour afficher tous les abonnements pour ce compte.
    - Exécutez `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` pour sélectionner l’abonnement que vous souhaitez utiliser. Cet abonnement doit être identique à celle utilisée dans le portail Azure.
1. Exécutez la commande suivante pour déployer des entités de données fabrique en utilisant le modèle de gestionnaire de ressources que vous avez créé à l’étape 1. 

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json

## <a name="monitor-pipeline"></a>Pipeline de moniteur
 
1.  Après la connexion au [portail Azure](https://portal.azure.com/), cliquez sur **Parcourir** et sélectionnez **les références de données**.
        ![Parcourir -> fabriques de données](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.  De la lame de **Fabriques de données** , cliquez sur le factory de données (**TutorialFactoryARM**) que vous avez créé.   
2.  Dans la lame **Factory de données** pour votre fabrique de données, cliquez sur **diagramme**.
        ![Mosaïque de diagramme](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.  Dans la **Vue Affichage des diagrammes**, vous voyez une vue d’ensemble des tuyaux et des datasets utilisés dans ce didacticiel.
    
    ![Affichage des diagrammes](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. Dans l’affichage des tâches, double-cliquez sur le groupe de données **AzureBlobOutput**. Vous constatez que la tranche qui est en cours de traitement.

    ![Groupe de données](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Lorsque le traitement est terminé, vous voyez la section dans l’état **prêt** . Création d’un cluster d’HDInsight de la demande dure un certain temps (environ 20 minutes). Par conséquent, prévoyez le tuyau à prendre **environ 30 minutes** pour traiter la tranche.

    ![Groupe de données](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png) 
10. Lorsque la tranche est dans l’état **prêt** , vérifiez le dossier **partitioneddata** dans le conteneur **adfgetstarted** votre stockage blob pour les données de sortie.  

Pour obtenir des instructions sur l’utilisation des lames portails Azure pour surveiller le pipeline et les groupes de données que vous avez créé dans ce didacticiel, consultez [pipeline et moniteur de groupes de données](data-factory-monitor-manage-pipelines.md) .

Vous pouvez également utiliser les surveiller et gérer l’application à surveiller vos pipelines de données. Consultez [moniteur et de gérer les pipelines Azure Data Factory à l’aide de la surveillance de l’application](data-factory-monitor-manage-app.md) pour plus d’informations sur l’utilisation de l’application. 

> [AZURE.IMPORTANT] Le fichier d’entrée est supprimé lorsque la tranche est traitée avec succès. Par conséquent, si vous souhaitez réexécuter la tranche ou ce didacticiel à nouveau, vous pouvez télécharger le fichier d’entrée (input.log) dans le dossier inputdata du conteneur adfgetstarted.

## <a name="data-factory-entities-in-the-template"></a>Entités dans le modèle de fabrique de données
### <a name="define-data-factory"></a>Définir la fabrique de données
Vous définissez une fabrique de données dans le modèle de gestionnaire de ressources, comme indiqué dans l’exemple suivant :  

    "resources": [
    {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US"
    }

Le dataFactoryName est défini comme suit : 
      
      "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",

Il s’agit d’une chaîne unique en fonction de l’ID de groupe de ressources.  

### <a name="defining-data-factory-entities"></a>Définition des entités de données usine
Les entités suivantes de la fabrique de données sont définies dans le modèle JSON : 

- [Service de stockage lié Azure](#azure-storage-linked-service)
- [Services liés à la demande de HDInsight](#hdinsight-on-demand-linked-service)
- [Jeu de données d’entrée blob Azure](#azure-blob-input-dataset)
- [Dataset de sortie blob Azure](#azure-blob-output-dataset)
- [Pipeline de données avec une activité de copie](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Service de stockage lié Azure
Vous spécifiez le nom et la clé de votre compte de stockage Azure dans cette section. Pour plus d’informations sur les propriétés JSON permet de définir un service de stockage Azure liés, consultez [stockage Azure lié service](data-factory-azure-blob-connector.md#azure-storage-linked-service) . 

      {
        "type": "linkedservices",
        "name": "[variables('azureStorageLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "AzureStorage",
          "description": "Azure Storage linked service",
          "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
          }
        }
      }

**ConnectionString** utilise les paramètres storageAccountName et storageAccountKey. Les valeurs de ces paramètres passés à l’aide d’un fichier de configuration. La définition utilise également des variables : azureStroageLinkedService et dataFactoryName sont définis dans le modèle. 
    
#### <a name="hdinsight-on-demand-linked-service"></a>Services liés à la demande de HDInsight
Reportez-vous [calculer des services liés](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) à l’article pour plus d’informations sur les propriétés JSON permet de définir un service lié à la demande de HDInsight.  

      {
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "HDInsightOnDemand",
          "typeProperties": {
            "clusterSize": 1,
            "version": "3.2",
            "timeToLive": "00:05:00",
            "osType": "windows",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
          }
        }
      }

Notez les points suivants : 

- Le Factory de données crée un cluster **Windows** HDInsight avec le JSON ci-dessus. Vous pourriez également y créer un cluster **Linux** HDInsight. Pour plus d’informations, reportez-vous à la section [Service de lié à la demande HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
- Vous pouvez utiliser **votre propre cluster HDInsight** au lieu d’utiliser un cluster d’HDInsight à la demande. Pour plus d’informations, reportez-vous à la section [HDInsight des services liés](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
- Le cluster HDInsight crée un **conteneur par défaut** dans le stockage blob que vous avez spécifié dans le JSON (**linkedServiceName**). HDInsight ne supprime pas ce conteneur lorsque le cluster est supprimé. Ce comportement est voulu par la conception. Avec le service de HDInsight liée à la demande, un HDInsight de cluster est créé chaque fois qu’une tranche doit être traitée à moins qu’il existe un live cluster (**la propriété timeToLive**) et est supprimé lorsque le traitement est terminé.

    Comme plusieurs sections sont traitées, vous voyez de nombreux conteneurs dans le stockage blob Azure. Si vous ne devez pas les pour des tâches de dépannage, vous souhaiterez les supprimer afin de réduire le coût de stockage. Les noms de ces conteneurs obéissent à un modèle : « chargeur automatique de documents**yourdatafactoryname**-**linkedservicename**- datetimestamp ». Utilisez des outils tels que [Microsoft Storage Explorer](http://storageexplorer.com/) pour supprimer les conteneurs dans le stockage blob Azure.

Pour plus d’informations, reportez-vous à la section [Service de lié à la demande HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .



#### <a name="azure-blob-input-dataset"></a>Jeu de données d’entrée blob Azure
Vous spécifiez les noms de conteneur blob, dossier et fichier qui contient les données d’entrée. Pour plus d’informations sur les propriétés JSON permet de définir un groupe de données Blob d’Azure, reportez-vous à la section [Propriétés du groupe de données Blob d’Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) . 

      {
        "type": "datasets",
        "name": "[variables('blobInputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
          "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
              "type": "TextFormat",
              "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          },
          "external": true
        }
      }

Cette définition utilise les paramètres suivants définis dans le modèle de paramètre : blobContainer, inputBlobFolder et inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Dataset de sortie Blob Azure
Vous spécifiez les noms de conteneur blob et le dossier qui contient les données de sortie. Pour plus d’informations sur les propriétés JSON permet de définir un groupe de données Blob d’Azure, reportez-vous à la section [Propriétés du groupe de données Blob d’Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) .  

      {
        "type": "datasets",
        "name": "[variables('blobOutputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
          "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
              "type": "TextFormat",
              "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          }
        }
      }

Cette définition utilise les paramètres suivants définis dans le modèle de paramètre : blobContainer et outputBlobFolder. 

#### <a name="data-pipeline"></a>Pipeline de données
Vous définissez un pipeline qui transforment les données en exécutant le script de la ruche sur un cluster d’Azure HDInsight à la demande. Pour une description des éléments JSON permet de définir un pipeline dans cet exemple, consultez [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json) . 

    {
        "type": "datapipelines",
        "name": "[variables('pipelineName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('hdInsightOnDemandLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('blobOutputDatasetName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "description": "Pipeline that transforms data using Hive script.",
          "activities": [
            {
              "type": "HDInsightHive",
              "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                  "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                  "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
              },
              "inputs": [
                {
                  "name": "[variables('blobInputDatasetName')]"
                }
              ],
              "outputs": [
                {
                  "name": "[variables('blobOutputDatasetName')]"
                }
              ],
              "policy": {
                "concurrency": 1,
                "retry": 3
              },
              "scheduler": {
                "frequency": "Month",
                "interval": 1
              },
              "name": "RunSampleHiveActivity",
              "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
            }
          ],
          "start": "2016-10-01T00:00:00Z",
          "end": "2016-10-02T00:00:00Z",
          "isPaused": false
        }
      }

## <a name="reuse-the-template"></a>Réutiliser le modèle 
Dans le didacticiel, vous avez créé un modèle pour définir les entités de la fabrique de données et un modèle pour le passage des valeurs pour les paramètres. Pour utiliser le même modèle pour déployer des entités de données usine aux différents environnements, vous créez un fichier de paramètres pour chaque environnement et l’utiliser lors du déploiement sur cet environnement.     

Exemple :  

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json

Notez que la première commande utilise le fichier de paramètres pour l’environnement de développement, un deuxième pour l’environnement de test et l’environnement de production.  

Vous pouvez également réutiliser le modèle pour effectuer des tâches répétitives. Par exemple, vous avez besoin créer de nombreuses fabriques de données avec un ou plusieurs tuyaux qui implémentent la même logique, mais chaque usine de données utilise le stockage Azure différentes et des comptes de la base de données de SQL Azure. Dans ce scénario, vous utilisez le même modèle, dans le même environnement (développement, test ou production) avec des fichiers de paramètres différentes pour créer des fabriques de données. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Modèle de gestionnaire de ressources pour la création d’une passerelle
Voici un exemple de modèle de gestionnaire de ressources pour la création d’une passerelle logique à l’arrière. Installer une passerelle sur votre ordinateur de locaux ou l’Azure IaaS VM et enregistrer la passerelle avec service Factory de données à l’aide d’une clé. Pour plus d’informations, voir [déplacer des données entre local et le nuage](data-factory-move-data-between-onprem-and-cloud.md) .

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "GatewayUsingArmDF",
            "apiVersion": "2015-10-01",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "eastus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "gateways",
                        "apiVersion": "[variables('apiVersion')]",
                        "name": "GatewayUsingARM",
                        "properties": {
                            "description": "my gateway"
                        }
                    }            
                ]
            }
        ]
    }

Ce modèle crée une fabrique de données nommée GatewayUsingArmDF avec une passerelle nommée : GatewayUsingARM. 

## <a name="see-also"></a>Voir aussi
| Rubrique | Description |
| :---- | :---- |
| [Activités de Transformation des données](data-factory-data-transformation-activities.md) | Cet article fournit une liste d’activités de transformation de données (tels que la transformation de la ruche de HDInsight vous avez utilisé dans ce didacticiel) pris en charge par l’usine de données Azure. |
| [Planification et exécution](data-factory-scheduling-and-execution.md) | Cet article explique les aspects de la planification et l’exécution du modèle d’application Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory et comment les utiliser pour construire de bout en bout orientées données des flux de travail pour votre scénario ou une entreprise. |
| [Groupes de données](data-factory-create-datasets.md) | Cet article vous aide à comprendre les groupes de données dans Azure Data Factory.
| [Surveiller et gérer les pipelines à l’aide de la surveillance de l’application](data-factory-monitor-manage-app.md) | Cet article décrit comment faire pour contrôler, gérer et déboguer des pipelines à l’aide du contrôle et application de gestion. 

  

