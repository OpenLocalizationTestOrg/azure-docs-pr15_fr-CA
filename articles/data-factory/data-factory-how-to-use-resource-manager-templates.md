<properties 
    pageTitle="Utilisez le Gestionnaire de ressources des modèles dans Data Factory | Microsoft Azure" 
    description="Découvrez comment créer et utiliser des modèles de gestionnaire de ressources Azure pour créer des entités de données usine." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="shlo"/>

# <a name="use-templates-to-create-azure-data-factory-entities"></a>Utilisez des modèles pour créer des entités Azure Data Factory

## <a name="overview"></a>Vue d’ensemble
Lors de l’utilisation d’Azure Data Factory pour vos besoins d’intégration de données, vous pourrez être amené à réutiliser le même modèle sur plusieurs environnements différents ou de la mise en oeuvre de la même tâche répétitive dans la même solution. Les modèles vous aident à implémentez et gérez ces scénarios de façon simple. Modèles dans Azure Data Factory sont idéales pour les scénarios qui impliquent la réutilisation et la répétition.
 
Prenons le cas où une entreprise a 10 usines de fabrication dans le monde entier. Les journaux de chaque plante sont stockées dans une base de données de SQL Server sur site distinct. La société souhaite générer un seul entrepôt de données dans le nuage pour ad hoc analytique. Il souhaite également la même logique mais différentes configurations pour les environnements de développement, de test et de production. 

Dans ce cas, une tâche doit être répétée dans le même environnement, mais avec des valeurs différentes sur les fabriques 10 données pour chaque usine. En effet, la **répétition** est présent. Création de modèles permet l’abstraction de ce flux générique (c'est-à-dire, les tuyaux ayant les mêmes activités dans chaque usine de données), mais utilise un fichier de paramètre distinct pour chaque usine.

En outre, que l’organisation souhaite déployer ces fabriques 10 données à plusieurs fois dans des environnements différents, les modèles peuvent utiliser cette **réutilisation** en utilisant des fichiers de paramètre distinct pour les environnements de développement, de test et de production.

## <a name="templating-with-azure-resource-manager"></a>Création de modèles avec le Gestionnaire de ressources Azure
[Modèles du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md#template-deployment) sont un excellent moyen d’atteindre des modèles dans Azure Data Factory. Les modèles de gestionnaire de ressources définissent l’infrastructure et la configuration de votre solution Azure via un fichier de JSON. Parce que le Gestionnaire de ressources Azure modèles fonctionnent avec services Azure tous/la plupart, il peut largement utilisé pour gérer facilement toutes les ressources de vos ressources d’Azure. Consultez [les modèles de création d’un gestionnaire de ressources Azure](../resource-group-authoring-templates.md) pour plus d’informations sur les modèles du Gestionnaire de ressources en général. 

## <a name="tutorials"></a>Didacticiels
Consultez les didacticiels suivants pour obtenir des instructions pas à pas créer des entités de la fabrique de données à l’aide des modèles du Gestionnaire de ressources :

- [Didacticiel : Créer un pipeline pour copier des données à l’aide du modèle de gestionnaire de ressources Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [Didacticiel : Créer un pipeline pour traiter des données à l’aide du modèle de gestionnaire de ressources Azure](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Modèles d’usine de données sur Github
Découvrez les modèles Azure démarrage rapide suivants sur Github : 

- [Créer une fabrique de données pour copier des données depuis le stockage Blob Azure à une base de données de SQL Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
- [Créer une fabrique de données avec l’activité de ruche de cluster d’Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
- [Créer une fabrique de données pour copier des données depuis Salesforce vers Azure BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)

N’hésitez pas à partager vos modèles Azure Data Factory à [démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates/). Reportez-vous au [guide de contribution](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) lors du développement de modèles qui peuvent être partagés via ce référentiel. 

Les sections suivantes fournissent plus d’informations sur la définition des ressources de l’usine de données dans un modèle de gestionnaire de ressources. 

## <a name="defining-data-factory-resources-in-templates"></a>Définition des ressources de l’usine de données dans les modèles
Le modèle de niveau supérieur pour la définition d’une fabrique de données est :

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
        { "type": "linkedservices",
            ...
        },
        {"type": "datasets",
            ...
        },
        {"type": "dataPipelines",
            ...
        }
    }

### <a name="define-data-factory"></a>Définir la fabrique de données

Vous définissez une fabrique de données dans le modèle de gestionnaire de ressources, comme indiqué dans l’exemple suivant :

    "resources": [
    {
        "name": "[variables('<mydataFactoryName>')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "East US"
    }

Le dataFactoryName est défini dans « variables » sous la forme :

    "dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",

### <a name="define-linked-services"></a>Définir les services liés 
    
    "type": "linkedservices",
    "name": "[variables('<LinkedServiceName>')]",
    "apiVersion": "2015-10-01",
    "dependsOn": [ "[variables('<dataFactoryName>')]" ],
    "properties": {
        ...
    }


Pour plus d’informations sur les propriétés JSON pour le service lié spécifique que vous souhaitez déployer, reportez-vous à la section [Service de stockage lié](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [Calculer des Services liés](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Le paramètre « dependsOn » Spécifie le nom de la fabrique de données correspondante. Un exemple de définition d’un service lié pour le stockage Azure est indiqué dans la définition de JSON suivante :

### <a name="define-datasets"></a>Définir des groupes de données

    "type": "datasets",
    "name": "[variables('<myDatasetName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<myDatasetLinkedServiceName>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        ...
    }

Pour plus d’informations sur les propriétés JSON pour le type de jeu de données spécifique que vous souhaitez déployer, voir [prise en charge des magasins de données](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Remarque le paramètre « dependsOn » Spécifie le nom de la fabrique de données correspondants et le stockage lié service. Un exemple de définition de type de groupe de données de stockage des objets blob Azure est indiqué dans la définition de JSON suivante :

    "type": "datasets",
    "name": "[variables('storageDataset')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('storageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "[variables('storageLinkedServiceName')]",
    "typeProperties": {
        "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
        "fileName": "[parameters('sourceBlobName')]",
        "format": {
            "type": "TextFormat"
        }
    },
    "availability": {
        "frequency": "Hour",
        "interval": 1
    }

### <a name="define-pipelines"></a>Définir des pipelines

    "type": "dataPipelines",
    "name": "[variables('<mypipelineName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<inputDatasetLinkedServiceName>')]",
        "[variables('<outputDatasetLinkedServiceName>')]",
        "[variables('<inputDataset>')]",
        "[variables('<outputDataset>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        activities: {
            ...
        }
    }

Consultez la section [définition des pipelines](data-factory-create-pipelines.md#pipeline-json) pour plus d’informations sur les propriétés JSON pour définir le pipeline spécifique et les activités que vous souhaitez déployer. Notez le paramètre « dependsOn » Spécifie le nom de la fabrique de données, et tout correspondant lié des services ou des groupes de données. Un exemple d’un pipeline qui copie les données depuis le stockage Blob Azure à une base de données de SQL Azure est illustré dans l’extrait suivant de JSON :

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
        "start": "2016-10-03T00:00:00Z",
        "end": "2016-10-04T00:00:00Z"

## <a name="parameterizing-data-factory-template"></a>Paramétrage d’un modèle de fabrique de données
Pour des recommandations sur le paramétrage, reportez-vous à l’article [méthodes conseillées pour la création de modèles du Gestionnaire de ressources Azure](../resource-manager-template-best-practices.md#parameters) . En général, l’utilisation du paramètre doit être réduite en particulier si les variables peuvent être utilisées à la place. Fournir uniquement des paramètres dans les scénarios suivants :

- Paramètres varient suivant l’environnement (exemple : développement, test et production)
- Données confidentielles (telles que les mots de passe)

Si vous avez besoin d’extraire des secrets [Azure clé coffre-fort](../key-vault/key-vault-get-started.md) lors du déploiement des entités Azure Data Factory à l’aide de modèles, spécifiez le **nom secret** et le **coffre-fort clé** comme indiqué dans l’exemple suivant :

    "parameters": {
        "storageAccountKey": { 
            "reference": {
                "keyVault": {
                    "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
                },
                "secretName": "<secretName>"
            }, 
        },
        ...
    }

> [AZURE.NOTE] Lors de l’exportation de modèles pour les fabriques de données existant n’est actuellement pas encore pris en charge, il est. 


