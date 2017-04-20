<properties 
    pageTitle="Données Factory - journal de modification de l’API .NET | Microsoft Azure" 
    description="Décrit les modifications de dernière minute, des ajouts de fonctionnalités, des correctifs de bogues etc.... dans une version spécifique de l’API .NET de la fabrique de données Azure." 
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
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="spelluru"/>

# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - journal des modifications de l’API .NET 
Cet article fournit des informations sur les modifications apportées sur le Factory de données Azure SDK avec une version spécifique. Vous pouvez trouver le dernier package NuGet pour Azure Data Factory [ici](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) 

## <a name="version-4110"></a>Version 4.11.0
Ajouts de fonctionnalités :

- Les types de services liés suivants ont été ajoutés :
    - [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
    - [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
    - [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
- Les types de groupe de données suivantes ont été ajoutées : 
    - [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
    - [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
- Les types de source suivantes ont été ajoutées :
    - [MongoDbSource](https://msdn.microsoft.com/en-US/library/mt765123.aspx)

## <a name="version-4100"></a>Version 4.10.0
- Les propriétés optionnelles suivantes ont été ajoutées à TextFormat :
    - [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
    - [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
    - [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
- Les types de services liés suivants ont été ajoutés :
    - [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
    - [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
- Les types de groupe de données suivantes ont été ajoutées :
    - [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
- Les types de source suivantes ont été ajoutées :
    - [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
- Ajouter des propriétés de [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) à AzureMLBatchExecutionActivity 
    - Activer le passage web plusieurs entrées de service pour une expérience d’apprentissage automatique de Azure


## <a name="version-491"></a>Version 4.9.1

### <a name="bug-fix"></a>Résolution de bogue

- Déconseiller l’authentification basée sur les WebApi pour [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Version 4.9.0

### <a name="feature-additions"></a>Ajouts de fonctionnalités

- Ajouter des propriétés [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) et [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) CopyActivity. Pour plus d’informations sur la fonction, reportez-vous à la section [copie d’intermédiaire](data-factory-copy-activity-performance.md#staged-copy) .


### <a name="bug-fix"></a>Résolution de bogue

- Introduire une surcharge de la méthode [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) , qui accepte une instance de [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) .
- Sélectionnez [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) et [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) comme étant facultatifs dans CopySink.

## <a name="version-480"></a>Version 4.8.0

### <a name="feature-additions"></a>Ajouts de fonctionnalités
- Les propriétés optionnelles suivantes ont été ajoutées au type d’activité de copie à activer le réglage des performances de copie :
    - [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
    - [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Version 4.7.0

### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Ajouter un nouveau type StorageFormat type [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) pour copier les fichiers ligne optimisée (ORC) sous forme de colonnes.
* Ajouter des propriétés [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) et PolyBaseSettings SqlDWSink.
    * Permet l’utilisation de PolyBase pour copier les données dans l’entrepôt de données SQL.

## <a name="version-461"></a>Version 4.6.1

### <a name="bug-fixes"></a>Correctifs de bogues
* Résout les demandes HTTP de windows de l’activité de la liste.
    * Supprime le nom de groupe de ressources et le nom d’usine de données à partir de la charge utile de demande.

## <a name="version-460"></a>Version 4.6.0

### <a name="feature-additions"></a>Ajouts de fonctionnalités

- Les propriétés suivantes ont été ajoutées à [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
    - [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
    - [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
    - [Groupes de données](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
- Les propriétés suivantes ont été ajoutées à [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
    - [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
- Pour définir des groupes de données dont les données sont au format JSON, ajouté de nouveaux [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) type [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) . 

## <a name="version-450"></a>Version 4.5.0

### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Ajout [des opérations de liste de la fenêtre d’activité](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
    * Méthodes ajoutées pour récupérer windows d’activité avec les filtres basés sur les types d’entité (c'est-à-dire, fabriques de données, des groupes de données, pipelines et activités).
* Les types de services liés suivants ont été ajoutés : 
    * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Les types de groupe de données suivantes ont été ajoutées : 
    * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Les types de source suivantes ont été ajoutées :  
    * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Version 4.4.0

### <a name="feature-additions"></a>Ajouts de fonctionnalités

- Le type de service lié suivant a été ajouté en tant que sources de données et les récepteurs pour les activités de copie :
    - [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Pour des informations conceptuelles et des exemples, reportez-vous à la section [Service lié de SAS de stockage Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) . 

## <a name="version-430"></a>Version 4.3.0

### <a name="feature-additions"></a>Ajouts de fonctionnalités

- Le havre de paix de types service lié suivant été ajouté en tant que sources de données pour les activités de copie :
    - [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Pour des informations conceptuelles et des exemples, voir [déplacer des données à partir de très à l’aide de la fabrique de données](data-factory-hdfs-connector.md) . 
    - [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Pour des informations conceptuelles et des exemples, voir [déplacer à l’aide de la fabrique de données Azure stocke les données à partir d’ODBC](data-factory-odbc-connector.md) . 

## <a name="version-420"></a>Version 4.2.0

### <a name="feature-additions"></a>Ajouts de fonctionnalités

- Le type d’activité suivant a été ajouté : [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Pour plus d’informations sur l’activité, consultez [modèles de ML de Azure mise à jour à l’aide de l’activité de la ressource mise à jour](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity).
- Une propriété facultative nouveau [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) a été ajouté à la [classe de AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx). 
- Les propriétés [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) et [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) ont été ajoutées à la classe [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) . 
- Permettre la configuration des délais d’attente pour les appels du client au service Data Factory. 


## <a name="version-410"></a>Version 4.1.0

### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Les types de services liés suivants ont été ajoutés : 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Les types d’activités suivants ont été ajoutés : 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Les types de groupe de données suivantes ont été ajoutées : 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Les types suivants de source et le récepteur pour activité de copie ont été ajoutées :
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## <a name="version-401"></a>Version 4.0.1

### <a name="breaking-changes"></a>Modifications avec rupture
Les classes suivantes ont été renommés. Les nouveaux noms sont les noms d’origine des classes avant la mise à jour 4.0.0. 
 
Nom en 4.0.0 | Nom de version 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## <a name="version-400"></a>Version 4.0.0

### <a name="breaking-changes"></a>Modifications avec rupture



- Les classes/interfaces suivantes ont été renommés.

| Ancien nom | Nouveau nom |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Table | [Groupe de données](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    

- Les méthodes de la **liste** de résultats paginés maintenant. Si la réponse contient une propriété de **NextLink** non vide, l’application cliente doit continuer l’extraction de la page suivante jusqu'à ce que toutes les pages sont renvoyées.  Voici un exemple : 

        PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
        var pipelines = new List<Pipeline>(response.Pipelines);
    
        string nextLink = response.NextLink;
        while (string.IsNullOrEmpty(response.NextLink))
        {
            PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
            pipelines.AddRange(nextResponse.Pipelines);
    
            nextLink = nextResponse.NextLink;
        }
    
- **Liste** pipeline API retourne uniquement le résumé d’un pipeline au lieu de tous les détails. Par exemple, les activités dans un résumé de pipeline contiennent uniquement name et type.

### <a name="feature-additions"></a>Ajouts de fonctionnalités
- La classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) prend en charge deux nouvelles propriétés, **SliceIdentifierColumnName** et **SqlWriterCleanupScript**, pour prendre en charge la copie idempotent à l’entrepôt de données SQL Azure. Consultez l’article [d’Entrepôt de données de SQL Azure](data-factory-azure-sql-data-warehouse-connector.md) , en particulier, le [mécanisme 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) et [2 du mécanisme](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) , et pour plus d’informations sur ces propriétés.

- Nous prennent désormais en charge exécutant la procédure stockée par rapport à des sources de base de données de SQL Azure et d’Azure SQL Data Warehouse dans le cadre de l’activité de copie. Les classes [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) et [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) ont les propriétés suivantes : **SqlReaderStoredProcedureName** et **StoredProcedureParameters**. Pour plus d’informations sur ces propriétés, consultez les articles de la [Base de données de SQL Azure](data-factory-azure-sql-connector.md#sqlsource) et [d’Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) sur Azure.com.  