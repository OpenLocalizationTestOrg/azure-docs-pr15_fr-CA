<properties
   pageTitle="Charger des données depuis le stockage blob Azure dans l’entrepôt de données SQL (PolyBase) | Microsoft Azure"
   description="Découvrez comment utiliser PolyBase pour charger des données depuis le stockage blob Azure dans l’entrepôt de données SQL. Charger les quelques tables de données publiques dans le schéma d’entrepôt de données de vente au détail de Contoso."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-azure-blob-storage-into-sql-data-warehouse-polybase"></a>Charger des données depuis le stockage blob Azure dans l’entrepôt de données SQL (PolyBase)

> [AZURE.SELECTOR]
- [Fabrique de données](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Utilisez les commandes PolyBase et T-SQL pour charger des données depuis le stockage blob Azure dans l’entrepôt de données SQL Azure. 

Pour faire simple, ce didacticiel charge deux tables à partir d’un Blob de stockage Azure publique dans le schéma de l’entrepôt de données de vente au détail de Contoso. Pour charger le jeu de données complet, exécutez l’exemple [charge l’entrepôt de données de vente au détail Contoso complet][] à partir du référentiel Microsoft SQL Server Samples.

Dans ce didacticiel, vous allez :

1. Configurer PolyBase à charger depuis le stockage blob Azure
2. Charger des données publiques dans votre base de données
3. Une fois le chargement terminé, exécuter des optimisations.


## <a name="before-you-begin"></a>Avant de commencer
Pour exécuter ce didacticiel, vous avez besoin d’un compte Azure qui possède déjà une base de données SQL Data Warehouse. Si vous n’avez pas cela, voir [créer un entrepôt de données SQL][].

## <a name="1-configure-the-data-source"></a>1. configurer la source de données

PolyBase utilise des objets externes T-SQL pour définir l’emplacement et les attributs des données externes. Les définitions d’objet externe sont stockées dans l’entrepôt de données SQL. Les données elles-mêmes sont stockées en externe.

### <a name="11-create-a-credential"></a>1.1. Créer des informations d’identification

**Ignorez cette étape** si vous chargez les données publiques de Contoso. Vous n’avez pas besoin un accès sécurisé aux données publics car il est déjà accessible à quiconque.

**Ne pas ignorer cette étape** si vous utilisez ce didacticiel en tant que modèle pour le chargement de vos propres données. Pour accéder aux données via une information d’identification, utilisez le script suivant pour créer une étendue de base de données d’informations d’identification et ensuite l’utiliser lors de la définition de l’emplacement de la source de données.


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
```

Passez à l’étape 2.

### <a name="12-create-the-external-data-source"></a>1.2. Créer la source de données externe

Utilisez cette commande de [Créer une SOURCE de données externe][] pour stocker l’emplacement des données et le type de données. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [AZURE.IMPORTANT] Si vous choisissez d’effectuer des conteneurs de stockage le blob azure public, gardez à l’esprit que le propriétaire des données vous sera facturée pour les données des frais de sortie lorsque des données quittent le centre de données. 

## <a name="2-configure-data-format"></a>2. configurer le format de données

Les données sont stockées dans des fichiers texte dans le stockage blob Azure, et chaque champ est séparé par un délimiteur. Exécutez cette commande [Créer un FORMAT de fichier externe][] pour spécifier le format des données dans les fichiers texte. Les données de Contoso sont décompressées et tuyau délimités.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,   FORMAT_OPTIONS  (   FIELD_TERMINATOR = '|'
                    ,   STRING_DELIMITER = ''
                    ,   DATE_FORMAT      = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,   USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. créer les tables externes

Maintenant que vous avez spécifié le format source et le fichier de données, vous êtes prêt à créer les tables externes. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Créer un schéma pour les données. 

Pour créer un emplacement pour stocker les données de Contoso dans votre base de données, créer un schéma.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Créez les tables externes. 

Exécutez ce script pour créer les tables externes DimProduct et FactOnlineSales. Nous essayons ici est définition des noms de colonnes et les types de données et leur liaison à l’emplacement et le format des fichiers de stockage du blob Azure. La définition est stockée dans le magasin de données SQL et les données sont toujours dans le Blob de stockage Azure.

Le paramètre **d’emplacement** est le dossier sous le dossier racine dans le Blob de stockage Azure. Chaque table est dans un autre dossier.


```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
 
--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. chargement des données
Il existe différentes façons d’accéder aux données externes.  Vous pouvez interroger des données directement à partir de la table externe, charger les données dans des tables de base de données ou ajouter des données externes à des tables de base de données existante.  


### <a name="41-create-a-new-schema"></a>4.1. Créer un nouveau schéma

SACT crée une nouvelle table qui contient des données.  Commencez par créer un schéma pour les données de contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Charger les données dans des tables

Pour charger des données depuis le stockage blob Azure et l’enregistrer dans une table dans votre base de données, utilisez l’instruction [CREATE TABLE AS SELECT (Transact-SQL)][] . Chargement avec SACT s’appuie sur les tables externes fortement typés que vous venez de créer. Pour charger les données dans des tables, utilisez une instruction [SACT][] par table. 

SACT crée une nouvelle table et le remplit avec les résultats d’une instruction select. SACT définit la nouvelle table pour que les mêmes colonnes et les types de données en tant que les résultats de l’instruction select. Si vous sélectionnez toutes les colonnes d’une table externe, la nouvelle table sera un réplica des colonnes et des types de données dans la table externe.

Dans cet exemple, nous créons à la fois la dimension et la table de faits en tant que distribuées des tables de hachage. 


```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 le suivi de la progression du chargement

Vous pouvez suivre la progression de la charge à l’aide des vues de gestion dynamique (DMV). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r;
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. optimiser la compression de columnstore

Par défaut, SQL Data Warehouse stocke le tableau sous la forme d’un index ordonné en clusters columnstore. Issue d’une charge, certaines des lignes de données ne peuvent pas être compressé dans le columnstore.  Il existe de nombreuses raisons pourquoi cela peut se produire. Pour plus d’informations, voir [Gérer les index de columnstore][].

Pour optimiser les performances de la requête et de compression columnstore après une charge, reconstruisez la table pour forcer l’index columnstore pour compresser toutes les lignes. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Pour plus d’informations sur la maintenance des index de columnstore, consultez l’article de [gérer des index de columnstore][] .

## <a name="6-optimize-statistics"></a>6. optimiser les statistiques

Il est préférable de créer des statistiques de colonne immédiatement après un chargement. Il existe certains choix pour les statistiques. Par exemple, si vous créez des statistiques de colonne unique sur toutes les colonnes qu’il peut prendre un certain temps pour reconstruire toutes les statistiques. Si vous savez que certaines colonnes ne vont pas être dans les prédicats de requête, vous pouvez ignorer la création des statistiques sur ces colonnes.

Si vous décidez de créer des statistiques de colonne unique sur toutes les colonnes de chaque table, vous pouvez utiliser l’exemple de code d’une procédure stockée `prc_sqldw_create_stats` dans l’article [statistiques][] .

L’exemple suivant est un bon point de départ pour la création de statistiques. Il crée des statistiques de colonne unique sur chaque colonne de la table de dimension et chaque colonne de jointure dans les tables de faits. Vous pouvez toujours ajouter ultérieurement sur une ou plusieurs colonnes de statistiques pour les autres colonnes de table de faits.


```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Réalisation déverrouillée !

Vous avez correctement chargé des données publiques dans le magasin de données SQL Azure. Excellent travail !

Vous pouvez maintenant démarrer interrogeant les tables à l’aide de requêtes comme suit :

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Étapes suivantes
Pour charger les données de magasin de données de vente au détail de Contoso complets, utilisez le script pour plus de conseils de développement, consultez [vue d’ensemble du développement SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Créez un entrepôt de données SQL]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[Vue d’ensemble du développement SQL Data Warehouse]: sql-data-warehouse-overview-develop.md
[gérer les index de columnstore]: sql-data-warehouse-tables-index.md
[Statistiques]: sql-data-warehouse-tables-statistics.md
[SACT]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CRÉER LA SOURCE DE DONNÉES EXTERNE]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[CRÉER LE FORMAT DE FICHIER EXTERNE]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[CRÉER TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Chargement de l’entrepôt de données de vente au détail Contoso complet]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
