<properties
   pageTitle="Chargez les grandes quantités de données dans le magasin de LAC de données à l’aide de méthodes hors connexion | Microsoft Azure"
   description="Utilisez l’outil AdlCopy pour copier les données d’objets BLOB de stockage Azure au magasin de données lac"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/07/2016"
   ms.author="nitinme"/>

# <a name="use-azure-import-export-service-for-offline-copy-of-data-to-data-lake-store"></a>Utiliser le Service d’importation / exportation de Azure pour une copie hors connexion de données au magasin de données lac

Dans cet article, vous allez découvrir comment copier des groupes de données volumineux (> 200 Go) dans un magasin de LAC de données Azure à l’aide des méthodes de copie hors connexion, comme le [Service d’importation/exportation Azure](../storage/storage-import-export-service.md). En particulier, le fichier utilisé comme exemple dans cet article est de 339,420,860,416 octets c'est-à-dire environ 319 Go sur le disque. Appelons cette 319GB.tsv de fichier.

Azure Service d’importation/exportation vous permet de transférer en toute sécurité de grandes quantités de données pour le stockage des objets blob Azure par les lecteurs de disque dur à un centre de données Azure d’expédition.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Compte de stockage azure**.

- **Compte d’azure données lac Analytique (facultatif)** - en voir [mise en route avec Azure données lac Analytique](../data-lake-analytics/data-lake-analytics-get-started-portal.md) pour obtenir des instructions sur la façon de créer un compte de banque de données lac.


## <a name="preparing-the-data"></a>Préparation des données

Avant d’utiliser le Service d’importation/exportation, nous devons interrompre le fichier de données pour être transférés **dans les copies qui sont moins de 200 Go** en taille. C’est parce que l’outil d’importation ne fonctionne pas avec les fichiers de plus de 200 Go. Pour se conformer, dans ce didacticiel, nous fractionnez le fichier en segments de 100 Go octets chacun. Pour cela, à l’aide de [Cygwin](https://cygwin.com/install.html). Cygwin prend en charge la commande Linux qui permet aux utilisateurs d’effectuer facilement cette opération. Dans notre cas, nous utilisons la commande suivante.

    split -b 100m 319GB.tsv

L’opération de fractionnement crée des fichiers dont les noms ci-dessous.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Préparez les disques avec des données

Suivez les instructions de [l’à l’aide](../storage/storage-import-export-service.md) d’un service d’importation/exportation de Azure (sous la section **préparer vos lecteurs** ) pour préparer vos disques durs. Voici le flux global comment préparer le disque.

1. Obtention d’un disque dur qui satisfait l’impératif à utiliser pour le Service d’importation/exportation de Auzre.

2. Identifier un compte de stockage Azure où les données seront copiées qu’une seule fois il si fourni au centre de données Azure.

3. Utilisez l' [Outil d’importation/exportation d’Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), un utilitaire de ligne de commande. Voici un extrait de l’exemple sur l’utilisation de l’outil.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Pour des exemples d’extraits plus sur l’utilisation de l' **Outil d’importation/exportation d’Azure**, reportez-vous à la section [Service d’importation/exportation Azure à l’aide de](../storage/storage-import-export-service.md) .

4. La commande ci-dessus crée un fichier journal à l’emplacement spécifié. Ce fichier journal vous permet de créer une tâche d’importation à partir du [Portail classique d’Azure](https://manage.windowsazure.com).

## <a name="create-an-import-job"></a>Créer une tâche d’importation

Vous pouvez maintenant créer une tâche d’importation en suivant les instructions [à l’aide](../storage/storage-import-export-service.md) d’un service d’importation/exportation de Azure (la section **créer la tâche d’importation** ). Pour cette tâche d’importation, avec d’autres détails, également fournir le fichier journal créé lors de la préparation des lecteurs de disque.

## <a name="physically-ship-the-disks"></a>Physiquement expédier les disques

Vous pouvez maintenant expédier des physiquement les disques à un centre de données Azure où les données sont copiées dans les Blobs de stockage Azure que vous avez fourni lors de la création de la tâche d’importation. Également, lors de la création du travail, si vous avez choisi pour fournir les informations de suivi par la suite, vous pouvez maintenant revenir à votre tâche d’importation et mis à jour le numéro de suivi.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Copier des données d’objets BLOB de stockage Azure au magasin de LAC de données Azure

Une fois que l’état de la tâche d’importation indique terminé, vous pouvez vérifier si les données sont disponibles dans les Blobs de stockage Azure que vous aviez spécifié. Vous pouvez ensuite utiliser les diverses méthodes pour atteindre le lac Azure Data Store de ces données à partir du BLOB de stockage Azure. Pour toutes les options disponibles pour le téléchargement de données, voir [Ingesting des données dans le magasin de données lac](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

Dans cette section, vous trouverez les définitions de JSON qui vous permet de créer un pipeline Azure Data Factory pour copier les données. Vous pouvez utiliser ces définitions de JSON à partir du [portail Azure](../data-factory/data-factory-copy-activity-tutorial-using-azure-portal.md) ou de [Visual Studio](../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md) ou de [PowerShell d’Azure](../data-factory/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Source liée Service (stockage Azure Blob)

````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Cibler des services liés (banque de LAC de données Azure)

````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Jeu de données d’entrée
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Jeu de données de sortie
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Pipeline (activité de copie)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Pour plus d’informations sur l’utilisation d’Azure Data Factory pour déplacer les données de Blob de stockage Azure et d’Azure Data Store de LAC, voir [déplacer des données à partir de l’objet Blob de stockage Azure au magasin de LAC Azure Data à l’aide de la fabrique de données Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Reconstruire les fichiers de données dans le magasin de LAC de données Azure

Nous avons commencé avec un fichier qui a été 319 Go en taille et tirez-en dans des fichiers de plus petite taille afin qu’il peut être transféré en utilisant le Service d’importation/exportation Azure. Maintenant que les données dans le magasin de LAC de données Azure, nous pouvez reconstrcut le fichier à sa taille d’origine. Vous pouvez utiliser la cmldts Azure PowerShell suivant pour le faire.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans le magasin de données lac](data-lake-store-secure-data.md)
- [Utiliser des données Azure lac Analytique avec le magasin de données lac](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilisez HDInsight Azure avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)
