<properties
   pageTitle="Charger les données avec Azure Data Factory | Microsoft Azure"
   description="Apprenez à charger les données avec Azure Data Factory"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>

# <a name="load-data-with-azure-data-factory"></a>Charger les données avec Azure Data Factory 

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Fabrique de données](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)  

Ce didacticiel vous montre comment créer un pipeline dans Azure Data Factory pour déplacer les données de Blob de stockage Azure à l’entrepôt de données SQL Azure. Les étapes suivantes, vous serez à même de :

+ Définir des exemples de données dans un Blob de stockage Azure.
+ Connecter des ressources sur le Factory de données Azure.
+ Créer un pipeline pour déplacer des données depuis le stockage BLOB SQL Data Warehouse.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## <a name="before-you-begin"></a>Avant de commencer

Pour vous familiariser avec la fabrique de données Azure, consultez [Introduction à l’usine de données Azure][].

### <a name="create-or-identify-resources"></a>Créez ou identifiez des ressources

Avant de commencer ce didacticiel, vous devez disposer des ressources suivantes :

   + **Blob de stockage Azure**: ce didacticiel utilise des Blob de stockage Azure comme source de données pour le tuyau fabrique de données Azure, et vous pouvez donc avoir un disponible pour stocker les données d’exemple. Si vous n’avez pas encore, apprenez comment [créer un compte de stockage][].

   + **SQL Data Warehouse**: ce didacticiel déplace les données de Blob de stockage Azure à SQL Data Warehouse et donc besoin d’un data warehouse en ligne qui est chargé avec les exemples de données AdventureWorksDW. Si vous ne disposez pas déjà d’un data warehouse, découvrez comment [mettre en service un][Create a SQL Data Warehouse]. Si vous disposez d’un entrepôt de données mais que vous n’avez pas mettre en service avec les exemples de données, vous pouvez [charger manuellement][Load sample data into SQL Data Warehouse].

   + **Usine de données Azure**: Azure Data Factory se termine la charge réelle, et vous pouvez donc avoir un que vous pouvez utiliser pour construire le pipeline de déplacement de données. Si vous n’avez pas encore, apprenez à en créer un à l’étape 1 de la [mise en route avec Azure Data Factory (éditeur de fabrique de données)][].

   + **AZCopy**: vous devez AZCopy pour copier les exemples de données à partir de votre client local à votre Blob de stockage Azure. Pour des instructions d’installation, consultez la [documentation de AZCopy][].

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>Étape 1 : Copie des exemples de données dans Azure stockage Blob

Une fois que tous les éléments prêts, vous êtes prêt à copier les exemples de données dans le Blob de stockage Azure.

1. [Télécharger les exemples de données][]. Ces données ajoute trois ans des données de vente pour vos exemples de données AdventureWorksDW.

2. Utilisez cette commande AZCopy pour copier les trois années de données pour le Blob de stockage Azure.

    ````
    AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
    ````


## <a name="step-2-connect-resources-to-azure-data-factory"></a>Étape 2 : Connectez les ressources sur le Factory de données Azure

Maintenant que les données sont en place, nous pouvons créer pipeline pour déplacer les données depuis le stockage blob Azure dans Data Warehouse de SQL Azure Data Factory.

Pour commencer, ouvrez le [portail Azure][] et sélectionnez votre fabrique de données dans le menu de gauche.

### <a name="step-21-create-linked-service"></a>Étape 2.1 : Créer des services liés

Lier votre compte de stockage Azure et un entrepôt de données SQL à votre usine de données.  

1. Tout d’abord, commencez le processus d’inscription en cliquant sur la section « Services liés » de votre fabrique de données, puis cliquez sur « Nouveau magasin de données. » Choisissez un nom pour enregistrer votre stockage azure sous, sélectionnez le stockage Azure comme votre type et puis entrez votre nom de compte et votre clé de compte.

2. Pour enregistrer SQL Data Warehouse accédez à la section « Auteur et déployer », sélectionnez « Nouveau magasin de données », puis « Entrepôt de données de SQL Azure ». Copier et coller dans ce modèle et puis renseignez vos informations spécifiques.

    ```JSON
    {
        "name": "<Linked Service Name>",
        "properties": {
            "description": "",
            "type": "AzureSqlDW",
            "typeProperties": {
                 "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
             }
        }
    }
    ```

### <a name="step-22-define-the-dataset"></a>Étape 2.2 : Définir le groupe de données

Après avoir créé les services liés, nous devons définir les ensembles de données.  Ici, cela signifie définition de la structure de données qui sont déplacées à partir de votre système de stockage à votre data warehouse.  Vous pouvez en savoir plus sur la création

1. Lancer ce processus, accédez à la section « Auteur et déploiement » de votre fabrique de données.

2. Cliquez sur « Nouveau groupe de données », puis « Stockage Azure Blob » à lier votre système de stockage à votre usine de données.  Vous pouvez utiliser le script ci-dessous pour définir vos données dans le stockage Azure Blob :

    ```JSON
    {
        "name": "<Dataset Name>",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "<linked storage name>",
            "typeProperties": {
                "folderPath": "<containter name>",
                "fileName": "FactInternetSales.csv",
                "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }
    ```

3. Définissons à présent notre dataset pour SQL Data Warehouse. Nous allons commencer de la même manière, en cliquant sur « Nouveau groupe de données », puis « Entrepôt de données de SQL Azure ».

    ```JSON
    {
        "name": "DWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": "AzureSqlDWLinkedService",
            "typeProperties": {
                "tableName": "FactInternetSales"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

## <a name="step-3-create-and-run-your-pipeline"></a>Étape 3 : Créer et exécuter votre pipeline

Enfin, nous avons configurer et exécuter le pipeline dans Azure Data Factory.  Il s’agit de l’opération qui se termine le mouvement des données réelles.  Vous trouverez une vue complète des opérations que vous pouvez réaliser des Data Warehouse de SQL Azure Data Factory [ici][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

Dans la section « Auteur et déployer », cliquez sur « Autres commandes », puis « Nouveau Pipeline ».  Après avoir créé le pipeline, vous pouvez utiliser le sous code pour transférer les données vers le data warehouse :

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
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
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, commencez par l’affichage :

- [Filière de formation azure Data Factory][].
- [Connecteur d’entrepôt de données SQL azure][]. Il s’agit de la rubrique de référence de base pour l’utilisation de la fabrique de données Azure avec l’entrepôt de données SQL Azure.


Ces rubriques fournissent des informations détaillées sur le Factory de données Azure. Ils abordent la base de données de SQL Azure ou HDInsight, mais les informations s’appliquent également à l’entrepôt de données SQL Azure.

- [Didacticiel : mise en route avec Azure Data Factory][] C’est le didacticiel de base pour le traitement des données avec Azure Data Factory. Dans ce didacticiel, vous allez créer votre premier pipeline qui utilise HDInsight pour transformer et d’analyser des journaux web sur une base mensuelle. Notez, il n’existe aucune activité de copie dans ce didacticiel.
- [Didacticiel : copier des données à partir de l’objet Blob de stockage Azure à la base de données de SQL Azure][]. Dans ce didacticiel, vous créez un tuyau dans Azure Data Factory pour copier les données de Blob de stockage Azure à une base de données de SQL Azure.

<!--Image references-->

<!--Article references-->
[Documentation de AZCopy]: ../storage/storage-use-azcopy.md
[Connecteur de magasin de données SQL Azure]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Créer un compte de stockage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Mise en route avec Azure Data Factory (éditeur de fabrique de données)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introduction à la fabrique de données Azure]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Didacticiel : Copier des données d’objet Blob le stockage Azure à la base de données de SQL Azure]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Didacticiel : Mise en route de la fabrique de données Azure]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Cursus Azure Data Factory]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Azure portal]: https://portal.azure.com
[Télécharger les exemples de données]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv
