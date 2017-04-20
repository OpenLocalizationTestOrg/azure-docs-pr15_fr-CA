<properties
   pageTitle="Charger des données à partir de SQL Server dans l’entrepôt de données SQL Azure (PolyBase) | Microsoft Azure"
   description="Utilise l’utilitaire bcp pour exporter des données à partir de SQL Server vers des fichiers plats, AZCopy pour importer des données dans le stockage blob Azure et PolyBase pour recevoir les données dans l’entrepôt de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-with-polybase-in-sql-data-warehouse"></a>Charger les données avec PolyBase dans le magasin de données SQL

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Ce didacticiel montre comment charger des données dans l’entrepôt de données SQL à l’aide de AzCopy et PolyBase. Lorsque vous avez terminé, vous saurez comment :

- AzCopy permet de copier des données vers le stockage blob Azure
- Créer des objets de base de données pour définir les données
- Exécuter une requête T-SQL pour charger les données

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## <a name="prerequisites"></a>Conditions préalables

Pour exécuter ce didacticiel, vous avez besoin

- Une base de données SQL Data Warehouse.
- Un compte de stockage Azure de type Standard localement stockage redondant (Standard-LRS), stockage géo-redondants Standard (Standard-GRS), ou le stockage de géo-redondants Standard-accès en lecture (Standard-RAGRS).
- Utilitaire de ligne de commande AzCopy. Téléchargez et installez la [dernière version de AzCopy][] qui est installé avec les outils de stockage Microsoft Azure.

    ![Outils de stockage Azure](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## <a name="step-1-add-sample-data-to-azure-blob-storage"></a>Étape 1 : Ajouter des exemples de données pour le stockage des objets blob Azure

Pour charger des données, nous devons placer des exemples de données dans un stockage blob Azure. Dans cette étape, nous avons remplir un blob Azure Storage avec des exemples de données. Par la suite, nous allons utiliser PolyBase pour charger ces exemples de données dans votre base de données SQL Data Warehouse.

### <a name="a-prepare-a-sample-text-file"></a>A. Préparer un exemple de fichier texte

Pour préparer un exemple de fichier texte :

1. Ouvrez le bloc-notes et copiez les lignes de données suivantes dans un nouveau fichier. Enregistrer dans votre répertoire temporaire local en tant que % temp%\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="b-find-your-blob-service-endpoint"></a>B. Trouver votre point de terminaison de service d’objet blob

Pour trouver votre point de terminaison de service d’objet blob :

1. À partir du portail d’Azure, sélectionnez **Parcourir** > **Comptes de stockage**.
2. Cliquez sur le compte de stockage que vous souhaitez utiliser.
3. De la lame de compte de stockage, cliquez sur objets BLOB

    ![Cliquez sur les objets BLOB](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. Enregistrer votre URL de point de terminaison de service blob pour plus tard.

    ![Point de terminaison de service BLOB](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### <a name="c-find-your-azure-storage-key"></a>C. Recherchez votre clé de stockage Azure

Pour trouver votre clé de stockage Azure :

1. À partir du portail Azure, sélectionnez **Parcourir** > **Comptes de stockage**.
2. Cliquez sur le compte de stockage que vous souhaitez utiliser.
3. Sélectionnez **tous les paramètres** > **clés d’accès**.
4. Cliquez sur la zone de copie pour copier une des touches d’accès rapide dans le Presse-papiers.

    ![Copiez la clé de stockage Azure](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### <a name="d-copy-the-sample-file-to-azure-blob-storage"></a>D. Copiez le fichier exemple pour le stockage des objets blob Azure

Pour copier les données pour le stockage des objets blob Azure :

1. Ouvrez une invite de commande et changez les répertoires pour le répertoire d’installation AzCopy. Cette commande passe au répertoire d’installation par défaut sur un ordinateur client Windows 64 bits.

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. Exécutez la commande suivante pour télécharger le fichier. Spécifiez l’URL du point de terminaison de service blob pour <blob service endpoint URL> et votre clé de compte de stockage Azure pour < azure_storage_account_key >.

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Voir aussi [Mise en route avec l’utilitaire de ligne de commande de AzCopy][version la plus récente de AzCopy].

### <a name="e-explore-your-blob-storage-container"></a>E. Explorez votre conteneur de stockage des blob

Pour afficher le fichier que vous avez téléchargé pour le stockage des objets blob :

1. Revenez à la blade de service d’objet Blob.
2. Sous conteneurs, double-cliquez sur **modifiable**.
3. Pour parcourir le chemin d’accès à vos données, cliquez sur le dossier **datedimension** , et vous verrez votre fichier téléchargé **DimDate2.txt**.
4. Pour afficher les propriétés, cliquez sur **DimDate2.txt**.
5. Notez que dans la lame de propriétés Blob, vous pouvez télécharger ou supprimer le fichier.

    ![Objet blob de vue stockage Azure](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## <a name="step-2-create-an-external-table-for-the-sample-data"></a>Étape 2 : Créer une table externe pour les exemples de données

Dans cette section, nous créons une table externe qui définit les données d’exemple.

PolyBase utilise des tables externes d’accéder aux données dans le stockage blob Azure. Étant donné que les données ne sont pas stockées dans l’entrepôt de données SQL, PolyBase gère l’authentification pour les données externes à l’aide des informations d’identification de base de données de portée.

L’exemple de cette étape utilise ces instructions Transact-SQL pour créer une table externe.

- [Créer la clé principale (Transact-SQL)][] pour crypter le mot de passe de votre base de données étendue d’informations d’identification.
- [Création de base de données étendue d’informations d’identification (Transact-SQL)][] pour spécifier les informations d’authentification de votre compte de stockage Azure.
- [Créer une Source de données externe (Transact-SQL)][] pour spécifier l’emplacement de votre stockage blob Azure.
- [Créer fichier de Format externe (Transact-SQL)][] pour spécifier le format de vos données.
- [Créer la Table externe (Transact-SQL)][] pour spécifier la définition de table et l’emplacement des données.

Exécutez cette requête sur votre base de données SQL Data Warehouse. Il créera une table externe nommée DimDate2External dans le schéma dbo qui pointe vers les données d’exemple DimDate2.txt dans le stockage blob Azure.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


Dans l’Explorateur d’objets SQL Server dans Visual Studio, vous pouvez voir le format de fichier externe, source de données externe et la table DimDate2External.

![Afficher la table externe](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## <a name="step-3-load-data-into-sql-data-warehouse"></a>Étape 3 : Charger des données dans Data Warehouse de SQL

Une fois la table externe est créée, vous pouvez charger les données dans une nouvelle table ou l’insérer dans une table existante.

- Pour charger les données dans une nouvelle table, exécutez l’instruction [CREATE TABLE AS SELECT (Transact-SQL)][] . La nouvelle table aura les colonnes dans la requête. Les types de données des colonnes correspondra à des types de données dans la définition de la table externe.
- Pour charger les données dans une table existante, utilisez l’insertion de [... SELECT (Transact-SQL)][] instruction.

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Étape 4 : Créer des statistiques sur vos données chargées

SQL Data Warehouse ne créer auto ni statistiques mises à jour automatiques. Pour obtenir des performances élevées pour les requêtes, il est donc important de créer des statistiques pour chaque colonne de chaque table après le premier chargement. Il est également important de mettre à jour des statistiques après des modifications importantes dans les données.

Cet exemple crée des statistiques de colonne unique sur la nouvelle table de DimDate2.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Pour plus d’informations, reportez-vous à la section [statistiques][].  


## <a name="next-steps"></a>Étapes suivantes
Consultez le [guide de PolyBase][] pour plus d’informations que vous devez connaître lorsque vous développez une solution qui utilise PolyBase.

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[Guide des PolyBase]: ./sql-data-warehouse-load-polybase-guide.md
[dernière version de AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[CRÉER la SOURCE de données externe (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CRÉER le FORMAT de fichier externe (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[CRÉER la TABLE externe (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[CRÉER TABLE AS SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSÉRER... SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CRÉER la clé principale (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[CRÉER des informations d’identification de base de données inclus dans l’étendue (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx
