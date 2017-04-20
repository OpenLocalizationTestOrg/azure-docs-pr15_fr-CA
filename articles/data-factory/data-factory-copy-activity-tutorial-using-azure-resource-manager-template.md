<properties
    pageTitle="Didacticiel : Créer un pipeline à l’aide du Gestionnaire de ressources du modèle | Microsoft Azure"
    description="Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide du modèle de gestionnaire de ressources Azure."
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
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-resource-manager-template"></a>Didacticiel : Créer un pipeline avec une activité de copie à l’aide du modèle de gestionnaire de ressources Azure
> [AZURE.SELECTOR]
- [Vue d’ensemble et des conditions préalables](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Assistant copie](data-factory-copy-data-wizard-tutorial.md)
- [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modèle de gestionnaire de ressources Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Ce didacticiel vous montre comment créer et surveiller une fabrique de données Azure à l’aide d’un modèle de gestionnaire de ressources Azure. Le pipeline à l’usine de données copie les données depuis le stockage Blob Azure à une base de données de SQL Azure.

## <a name="prerequisites"></a>Conditions préalables
- Aller à la [vue d’ensemble et les conditions préalables](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) et suivez les étapes de la **condition préalable** .
- Suivez les instructions de l’article de [procédure pour installer et configurer Azure PowerShell](../powershell-install-configure.md) pour installer la version la plus récente de Azure PowerShell sur votre ordinateur. Dans ce didacticiel, vous utilisez PowerShell pour déployer des entités de données usine. 
- (facultatif) Reportez-vous à la section [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md) pour en savoir plus sur les modèles du Gestionnaire de ressources Azure.


## <a name="in-this-tutorial"></a>Dans ce didacticiel

Dans ce didacticiel, vous créez une fabrique de données avec les entités données usine suivantes :

Entité | Description  
------ | ----------- 
Service de stockage lié Azure | Lier votre compte de stockage Azure sur le factory de données. Stockage Azure est le magasin de données source et la base de données d’Azure SQL est le magasin de données de récepteur de l’activité de copie dans le didacticiel. Il spécifie le compte de stockage contenant les données d’entrée pour l’activité de copie. 
Service de liées de la base de données SQL Azure| Lie votre base de données Azure SQL sur le factory de données. Il spécifie la base de données Azure SQL qui contient les données de sortie de l’activité de copie. 
Dataset d’entrée des Blob Azure | Fait référence au service de stockage Azure lié. Le service lié fait référence à un compte de stockage Azure et le groupe de données Azure Blob Spécifie le conteneur, le dossier et le nom du fichier dans le stockage qui contient les données d’entrée. 
Dataset de sortie SQL Azure | Fait référence au service SQL Azure lié. Le service SQL Azure lié fait référence à un serveur SQL d’Azure et le groupe de données Azure SQL spécifie le nom de la table qui contient les données de sortie. 
Pipeline de données | Le pipeline a une activité de type copie qui prend le groupe de données blob Azure comme entrée et le groupe de données Azure SQL sous la forme d’une sortie. L’activité de copie copie les données à partir d’un Azure blob à une table dans la base de données SQL d’Azure.  

Une fabrique de données peut avoir un ou plusieurs tuyaux. Un tuyau peut contenir une ou plusieurs activités. Il existe deux types d’activités : [activités de déplacement des données](data-factory-data-movement-activities.md) et des [activités de transformation des données](data-factory-data-transformation-activities.md). Dans ce didacticiel, vous créez un pipeline avec une activité (copie).

![Copier les Blob Azure à une base de données SQL Azure](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

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

Créez un fichier JSON nommé **ADFCopyTutorialARM.json** dans le dossier **C:\ADFGetStarted** avec le contenu suivant :


    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
          "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
          "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
          "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
          "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
          "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
          "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
          } 
        },
        "variables": {
          "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
          "azureSqlLinkedServiceName": "AzureSqlLinkedService",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "blobInputDatasetName": "BlobInputDataset",
          "sqlOutputDatasetName": "SQLOutputDataset",
          "pipelineName": "Blob2SQLPipeline"
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
                "name": "[variables('azureSqlLinkedServiceName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlDatabase",
                  "description": "Azure SQL linked service",
                  "typeProperties": {
                    "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
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
                  "structure": [
                    {
                      "name": "Column0",
                      "type": "String"
                    },
                    {
                      "name": "Column1",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                    "fileName": "[parameters('sourceBlobName')]",
                    "format": {
                      "type": "TextFormat",
                      "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Day",
                    "interval": 1
                  },
                  "external": true
                }
              },
              {
                "type": "datasets",
                "name": "[variables('sqlOutputDatasetName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureSqlLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlTable",
                  "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
                  "structure": [
                    {
                      "name": "FirstName",
                      "type": "String"
                    },
                    {
                      "name": "LastName",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "tableName": "[parameters('targetSQLTable')]"
                  },
                  "availability": {
                    "frequency": "Day",
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
                  "[variables('azureSqlLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('sqlOutputDatasetName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "activities": [
                    {
                      "name": "CopyFromAzureBlobToAzureSQL",
                      "description": "Copy data frm Azure blob to Azure SQL",
                      "type": "Copy",
                      "inputs": [
                        {
                          "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                          "name": "[variables('sqlOutputDatasetName')]"
                        }
                      ],
                      "typeProperties": {
                        "source": {
                          "type": "BlobSource"
                        },
                        "sink": {
                          "type": "SqlSink",
                          "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                        },
                        "translator": {
                          "type": "TabularTranslator",
                          "columnMappings": "Column0:FirstName,Column1:LastName"
                        }
                      },
                      "Policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 3,
                        "timeout": "01:00:00"
                      }
                    }
                  ],
                  "start": "2016-10-02T00:00:00Z",
                  "end": "2016-10-03T00:00:00Z"
                }
              }
            ]
          }
        ]
      }

## <a name="parameters-json"></a>Paramètres de JSON 
Créez un fichier JSON nommé **ADFCopyTutorialARM-Parameters.json** qui contient les paramètres pour le modèle de gestionnaire de ressources Azure. 

> [AZURE.IMPORTANT] Spécifiez le nom et la clé de votre compte de stockage Azure pour les paramètres **storageAccountName** et **storageAccountKey** .  

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": { 
            "storageAccountName": { "value": "<Name of the Azure storage account>"  },
            "storageAccountKey": {
                "value": "<Key for the Azure storage account>"
            },
            "sourceBlobContainer": { "value": "adftutorial" },
            "sourceBlobName": { "value": "emp.txt" },
            "sqlServerName": { "value": "<Name of the Azure SQL server>" },
            "databaseName": { "value": "<Name of the Azure SQL database>" },
            "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
            "sqlServerPassword": { "value": "<password for the user>" },
            "targetSQLTable": { "value": "emp" }
        }
    }

> [AZURE.IMPORTANT] Vous pouvez avoir des fichiers JSON de paramètre distinct pour le développement, le test et les environnements de production que vous pouvez utiliser avec le même modèle de données usine JSON. À l’aide d’un script PowerShell, vous pouvez automatiser le déploiement entités Data Factory dans ces environnements.  

## <a name="create-data-factory"></a>Création de la fabrique de données
1. Démarrer **PowerShell d’Azure** et exécutez la commande suivante :
    - Exécutez `Login-AzureRmAccount` et entrez le nom d’utilisateur et le mot de passe que vous utilisez pour vous connecter au portail Azure.  
    - Exécutez `Get-AzureRmSubscription` pour afficher tous les abonnements pour ce compte.
    - Exécutez `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` pour sélectionner l’abonnement que vous souhaitez utiliser. 
2. Exécutez la commande suivante pour déployer des entités de données fabrique en utilisant le modèle de gestionnaire de ressources que vous avez créé à l’étape 1.

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json

## <a name="monitor-pipeline"></a>Pipeline de moniteur
1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre compte Azure.
2. Sur les **fabriques de données** dans le menu de gauche (ou) cliquez sur **plus de services** et sur les **fabriques de données** sous catégorie **INTELLIGENCE + ANALYTICS** .

    ![Menu de fabriques de données](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. Dans la page **des fabriques de données** , rechercher et trouver votre fabrique de données. 

    ![Recherche de la fabrique de données](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. Cliquez sur le factory de données Azure. Vous consultez la page d’accueil de la fabrique de données.

    ![Page d’accueil de la fabrique de données](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
5. Cliquez sur mosaïque du **diagramme** pour afficher le diagramme de votre fabrique de données.

    ![Affichage du schéma de la fabrique de données](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-diagram-view.png)
6. Dans l’affichage des tâches, double-cliquez sur le groupe de données **SQLOutputDataset**. Vous consultez cet état de la tranche. Lorsque l’opération de copie est terminée, le statut de définir sur **prêt.**

    ![Tranche de sortie dans l’état prêt](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/output-slice-ready.png)
7. Lorsque la tranche est dans l’état **prêt** , vérifiez que les données sont copiées dans la table **employés** dans la base de données SQL d’Azure.

Pour obtenir des instructions sur l’utilisation des lames portails Azure pour surveiller le pipeline et les groupes de données que vous avez créé dans ce didacticiel, consultez [pipeline et moniteur de groupes de données](data-factory-monitor-manage-pipelines.md) .

Vous pouvez également utiliser les surveiller et gérer l’application à surveiller vos pipelines de données. Consultez [moniteur et de gérer les pipelines Azure Data Factory à l’aide de la surveillance de l’application](data-factory-monitor-manage-app.md) pour plus d’informations sur l’utilisation de l’application.


## <a name="data-factory-entities-in-the-template"></a>Entités dans le modèle de fabrique de données

### <a name="define-data-factory"></a>Définir la fabrique de données
Vous définissez une fabrique de données dans le modèle de gestionnaire de ressource comme indiqué dans l’exemple suivant :  

    "resources": [
    {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US"
    }

Le dataFactoryName est défini comme suit : 
      
    "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"

Il s’agit d’une chaîne unique en fonction de l’ID de groupe de ressources.  

### <a name="defining-data-factory-entities"></a>Définition des entités de données usine
Les entités suivantes de la fabrique de données sont définies dans le modèle JSON : 

1. [Service de stockage lié Azure](#azure-storage-linked-service)
2. [Service de liées de SQL Azure](#azure-sql-database-linked-service)
3. [Groupe de données blob Azure](#azure-blob-dataset)
4. [Groupe de données SQL Azure](#azure-sql-dataset)
5. [Pipeline de données avec une activité de copie](#data-pipeline)

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

La propriété connectionString utilise les paramètres storageAccountName et storageAccountKey. Les valeurs de ces paramètres passés à l’aide d’un fichier de configuration. La définition utilise également des variables : azureStroageLinkedService et dataFactoryName sont définis dans le modèle. 
    
#### <a name="azure-sql-database-linked-service"></a>Service de liées de la base de données SQL Azure
Vous spécifiez le nom du serveur SQL d’Azure, le nom de la base de données, le nom d’utilisateur et le mot de passe utilisateur dans cette section. Pour plus d’informations sur les propriétés JSON permet de définir un service SQL Azure liés, consultez [Azure SQL liées à service](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) .  

    {
        "type": "linkedservices",
        "name": "[variables('azureSqlLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "Azure SQL linked service",
            "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
            }
        }
    }

La propriété connectionString utilise sqlServerName, databaseName, sqlServerUserName et sqlServerPassword les paramètres dont les valeurs sont passées à l’aide d’un fichier de configuration. La définition utilise également les variables suivantes à partir du modèle : azureSqlLinkedServiceName, dataFactoryName.

#### <a name="azure-blob-dataset"></a>Groupe de données blob Azure
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
            "structure": [
            {
                "name": "Column0",
                "type": "String"
            },
            {
                "name": "Column1",
                "type": "String"
            }
            ],
            "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            },
            "external": true
        }
    }

#### <a name="azure-sql-dataset"></a>Groupe de données SQL Azure
Vous spécifiez le nom de la table dans la base de données Azure SQL contenant les données copiées à partir du stockage Azure Blob. Pour plus d’informations sur les propriétés JSON permet de définir un groupe de données Azure SQL, reportez-vous à la section [Propriétés du groupe de données Azure SQL](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) . 

    {
        "type": "datasets",
        "name": "[variables('sqlOutputDatasetName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]",
            "[variables('azureSqlLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
            "structure": [
            {
                "name": "FirstName",
                "type": "String"
            },
            {
                "name": "LastName",
                "type": "String"
            }
            ],
            "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

#### <a name="data-pipeline"></a>Pipeline de données
Vous définissez un pipeline qui copie les données du groupe de données blob Azure au groupe de données Azure SQL. Pour une description des éléments JSON permet de définir un pipeline dans cet exemple, consultez [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json) . 

    {
        "type": "datapipelines",
        "name": "[variables('pipelineName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]",
            "[variables('azureStorageLinkedServiceName')]",
            "[variables('azureSqlLinkedServiceName')]",
            "[variables('blobInputDatasetName')]",
            "[variables('sqlOutputDatasetName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "activities": [
            {
                "name": "CopyFromAzureBlobToAzureSQL",
                "description": "Copy data frm Azure blob to Azure SQL",
                "type": "Copy",
                "inputs": [
                {
                    "name": "[variables('blobInputDatasetName')]"
                }
                ],
                "outputs": [
                {
                    "name": "[variables('sqlOutputDatasetName')]"
                }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "SqlSink",
                        "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                    },
                    "translator": {
                        "type": "TabularTranslator",
                        "columnMappings": "Column0:FirstName,Column1:LastName"
                    }
                },
                "Policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 3,
                    "timeout": "01:00:00"
                }
            }
            ],
            "start": "2016-10-02T00:00:00Z",
            "end": "2016-10-03T00:00:00Z"
        }
    }

## <a name="reuse-the-template"></a>Réutiliser le modèle 
Dans le didacticiel, vous avez créé un modèle pour définir les entités de la fabrique de données et un modèle pour le passage des valeurs pour les paramètres. Le pipeline de copie des données à partir d’un compte de stockage Azure à une base de données Azure SQL spécifiée à l’aide de paramètres. Pour utiliser le même modèle pour déployer des entités de données usine aux différents environnements, vous créez un fichier de paramètres pour chaque environnement et l’utiliser lors du déploiement sur cet environnement.     

Exemple :  

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json

Notez que la première commande utilise le fichier de paramètres pour l’environnement de développement, un deuxième pour l’environnement de test et l’environnement de production.  

Vous pouvez également réutiliser le modèle pour effectuer des tâches répétitives. Par exemple, vous avez besoin créer de nombreuses fabriques de données avec un ou plusieurs tuyaux qui implémentent la même logique, mais chaque usine de données utilise le stockage Azure différentes et des comptes de la base de données de SQL Azure. Dans ce scénario, vous utilisez le même modèle, dans le même environnement (développement, test ou production) avec des fichiers de paramètres différentes pour créer des fabriques de données.   

