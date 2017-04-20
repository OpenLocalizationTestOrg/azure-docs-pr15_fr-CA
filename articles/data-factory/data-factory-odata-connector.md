<properties 
    pageTitle="Déplacer les données à partir de sources d’OData | Usine de données Azure" 
    description="Découvrez comment déplacer des données à partir de sources d’OData à l’aide de la fabrique de données Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Déplacer les données source à partir d’un OData à l’aide de la fabrique de données Azure
Cet article décrit comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données d’une source OData dans un autre magasin de données. Cet article s’appuie sur l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de transfert de données avec l’activité de copie et de combinaisons de magasin de données pris en charge.

> [AZURE.NOTE] Cette prise en charge connecteur OData copie de données à partir de deux nuage OData et les sources d’OData sur site. Dans le second cas, vous devez installer la passerelle de gestion des données. Article de [déplacer des données entre des locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) voir pour plus d’informations sur la passerelle de gestion des données.

## <a name="copy-data-wizard"></a>Assistant de duplication des données
La façon la plus simple pour créer un pipeline qui copie les données d’une source OData est d’utiliser l’Assistant de copie de données. Consultez [didacticiel : créer un tuyau à l’aide d’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapides sur la création d’un tuyau à l’aide de l’Assistant de copie de données. 

Les exemples suivants fournissent des exemples de définitions de JSON qui vous permet de créer un tuyau à l’aide de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [PowerShell d’Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils illustrent le copier des données provenant d’une source OData dans un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.


## <a name="sample-copy-data-from-odata-source-to-azure-blob"></a>Exemple : Copier des données à partir de la source OData dans Azure Blob

Cet exemple montre comment copier des données provenant d’une source OData dans le stockage Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple possède les entités de fabrique de données suivantes :

1.  Un service lié du type [OData](#odata-linked-service-properties).
2.  Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [ODataResource](#odata-dataset-type-properties).
4.  Une sortie [dataset](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](#odata-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple de copie des données à partir de la requête sur une source OData à un blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

**OData lié service** Cet exemple utilise l’authentification de base. Consultez la section [OData lié service](#odata-linked-service-properties) pour différents types d’authentification que vous pouvez utiliser. 

    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
                "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
            }
        }
    }


**Service de stockage lié Azure**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
              "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }

**Groupe de données OData d’entrée**

Définition de « externe » : « true » informe le service Factory de données que le groupe de données est externe à l’usine de données et qu’il n’est pas produit par une activité sur le factory de données.
    
    {
        "name": "ODataDataset",
        "properties": 
        {
            "type": "ODataResource",
            "typeProperties": 
            {
                "path": "Products" 
            },
            "linkedServiceName": "ODataLinkedService",
            "structure": [],
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3               
            }
        }
    }

Définition spécifiant le **chemin d’accès** du groupe de données est facultative. 


**Dataset de sortie Blob Azure**

Les données sont écrites dans un blob de nouveau toutes les heures (fréquence : heures, l’intervalle : 1). Le chemin d’accès du dossier pour le blob est calculée de manière dynamique en fonction de l’heure de début de la tranche qui est en cours de traitement. Le chemin d’accès du dossier utilise l’année, mois, jour et parties d’heures de l’heure de début.

    {
        "name": "AzureBlobODataDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Activité de copie de pipeline**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifié pour s’exécuter toutes les heures. Dans la définition de JSON de pipeline, le type de **source** est défini sur **RelationalSource** et type de **récepteurs** est définie sur **BlobSink**. La requête SQL spécifiée pour la propriété de **requête** sélectionne les données les plus récentes (les plus récentes) à partir de la source OData.
    
    {
        "name": "CopyODataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "?$select=Name, Description&$top=5",
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "ODataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobODataDataSet"
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
                    "name": "ODataToBlob"
                }
            ],
            "start": "2016-02-01T18:00:00Z",
            "end": "2016-02-03T19:00:00Z"
        }
    }


La spécification de **requête** dans la définition de pipeline est facultative. L' **URL** que le service Data Factory utilise pour récupérer des données est : URL spécifiée dans le service lié (obligatoire) + chemin d’accès spécifié dans le groupe de données (facultatif) + la requête dans le pipeline (facultatif). 

## <a name="odata-linked-service-properties"></a>OData lié les propriétés du Service

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service OData lié.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| type de | La propriété type doit être définie : **OData** | Oui |
| URL| URL du service OData. | Oui |
| authenticationType | Type d’authentification utilisé pour se connecter à la source OData. <br/><br/> Pour le cloud OData, les valeurs possibles sont anonymes et Basic. Pour les locaux, OData, les valeurs possibles sont anonyme, Basic et Windows. | Oui | 
| nom d’utilisateur | Spécifiez le nom d’utilisateur si vous utilisez l’authentification de base. | Oui (uniquement si vous utilisez l’authentification de base) | 
| mot de passe | Spécifiez le mot de passe pour le compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. | Oui (uniquement si vous utilisez l’authentification de base) | 
| nom de la passerelle | Nom de la passerelle que le service Factory de données doit utiliser pour se connecter au service OData sur site. Spécifiez uniquement si vous copiez des données à partir de la source d’OData sur les prem. | N° |

### <a name="using-basic-authentication"></a>À l’aide de l’authentification de base

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### <a name="using-anonymous-authentication"></a>À l’aide de l’authentification anonyme
    
    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>À l’aide de l’authentification Windows l’accès à la source d’OData sur site

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
                "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }



## <a name="odata-dataset-type-properties"></a>Propriétés du type de groupe de données OData

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, consultez l’article de la [Création de groupes de données](data-factory-create-datasets.md) . Sections de structure, de disponibilité et de stratégie d’un groupe de données JSON sont similaires pour tous les types de groupe de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de groupe de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le groupe de données de type **ODataResource** (qui inclut le groupe de données OData) possède les propriétés suivantes

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| chemin d’accès | Chemin d’accès à la ressource OData | N° | 

## <a name="odata-copy-activity-type-properties"></a>Propriétés de type d’activité de copie OData

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Créer des Pipelines](data-factory-create-pipelines.md) . Les propriétés telles que nom, description, d’entrée et de sortie des tables et la stratégie sont disponibles pour tous les types d’activités. 

D’autre part, les propriétés disponibles dans la section typeProperties de l’activité varient avec chaque type d’activité. Pour les activités de copie, qui varient selon les types de sources et les destinations.

Lorsque la source est de type **RelationalSource** (qui inclut OData) les propriétés suivantes sont disponibles dans la section de typeProperties :

| Propriété | Description | Exemple | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| requête | La requête personnalisée permet de lire les données. | « ? $select = nom, Description et $top = 5 » | N° | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odata"></a>Mappage de type pour OData

Comme mentionné dans l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , activité de copie effectue les conversions de type automatique à partir de types de sources pour recevoir des types avec l’approche 2-étape suivante :

1. Convertir les types de source native type .NET
2. Convertir à partir du type .NET pour type de récepteurs natif

Lorsque les magasins de déplacement des données à partir des données OData, types de données OData sont mappés aux types .NET.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [copie activité performances & Tuning Guide](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés cet impact sur les performances de transfert de données (copie activité) dans Azure Data Factory et les différentes manières de l’optimiser.

