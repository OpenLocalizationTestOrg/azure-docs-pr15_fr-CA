<properties 
    pageTitle="Déplacer les données de Redshift d’Amazon à l’aide de Data Factory | Microsoft Azure" 
    description="Découvrez comment déplacer des données à partir de Redshift d’Amazon à l’aide de la fabrique de données Azure." 
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
    ms.date="08/23/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Déplacement des données à partir d’Amazon Redshift à l’aide de la fabrique de données Azure

Cet article décrit comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données à partir de Redshift d’Amazon aux données d’une autre banque. Cet article s’appuie sur l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de transfert de données avec l’activité de copie et la liste des banques de données de source/récepteur.  

Usine de données prend actuellement en charge uniquement déplacement des données à partir de Redshift d’Amazon pour d’autres magasins de données, mais ne pas pour le déplacement des données provenant d’autres banques de données d’Amazon Redshift.

## <a name="prerequisites"></a>Conditions préalables

- Si vous déplacez des données dans une banque de données locale, vous pouvez accorder passerelle de gestion des données (utiliser l’adresse IP de l’ordinateur) l’accès au cluster d’Amazon Redshift. Pour obtenir des instructions, reportez-vous à la section [Autoriser l’accès au cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) . 
- Si vous déplacez des données dans une banque de données Azure, consultez [Plages de IP de centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) pour calculer les plages d’adresses IP (y compris les plages SQL) utilisées par les centres de données Microsoft Azure. 

## <a name="copy-data-wizard"></a>Assistant de duplication des données
La façon la plus simple pour créer un pipeline qui copie les données à partir de Redshift d’Amazon est d’utiliser l’Assistant de copie de données. Consultez [didacticiel : créer un tuyau à l’aide d’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapides sur la création d’un tuyau à l’aide de l’Assistant de copie de données. 

L’exemple suivant fournit des exemples de définitions de JSON qui vous permet de créer un tuyau à l’aide de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [PowerShell d’Azure](data-factory-copy-activity-tutorial-using-powershell.md). Il vous indique comment copier des données à partir de Redshift d’Amazon pour le stockage des objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-redshift-to-azure-blob"></a>Exemple : Copier des données à partir de Redshift d’Amazon dans Azure Blob
Cet exemple montre comment copier les données d’une base de données Redshift d’Amazon pour un stockage de Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple possède les entités de fabrique de données suivantes :

- Un service lié du type [AmazonRedshift](#linked-service-properties).
- Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](#dataset-type-properties).
- Une sortie [dataset](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](#copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir d’un résultat de requête dans les Redshift d’Amazon dans un blob toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

**Service de Redshift d’Amazon lié**

    {
        "name": "AmazonRedshiftLinkedService",
        "properties":
        {
            "type": "AmazonRedshift",
            "typeProperties":
            {
                "server": "< The IP address or host name of the Amazon Redshift server >",
                "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
                "database": "<The database name of the Amazon Redshift database>",
                "username": "<username>",
                "password": "<password>"
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

**Jeu de données d’entrée Amazon Redshift**

Définition de **« externe » : true** informe le service Factory de données que le dataset est externe à l’usine de données et qu’il n’est pas produit par une activité sur le factory de données. Définir cette propriété sur true sur un groupe de données d’entrée qui n’est pas produit par une activité dans le pipeline.

    {
        "name": "AmazonRedshiftInputDataset",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "AmazonRedshiftLinkedService",
            "typeProperties": {
                "tableName": "<Table name>"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


**Dataset de sortie Blob Azure**

Les données sont écrites dans un blob de nouveau toutes les heures (fréquence : heures, l’intervalle : 1). Le chemin d’accès du dossier pour le blob est calculée de manière dynamique en fonction de l’heure de début de la tranche qui est en cours de traitement. Le chemin d’accès du dossier utilise l’année, mois, jour et parties d’heures de l’heure de début.

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifié pour s’exécuter toutes les heures. Dans la définition de JSON de pipeline, le type de **source** est défini sur **RelationalSource** et type de **récepteurs** est définie sur **BlobSink**. La requête SQL spécifiée pour la propriété de **requête** sélectionne les données de la dernière heure à copier.
    
    {
        "name": "CopyAmazonRedshiftToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonRedshiftInputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
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
                    "name": "AmazonRedshiftToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propriétés du service lié

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service de Redshift d’Amazon lié.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| type de | La propriété type doit être définie : **AmazonRedshift**. | Oui |
| serveur | Nom hôte ou adresse IP du serveur Redshift d’Amazon. | Oui |
| port | Le numéro du port TCP utilisé par le serveur de Redshift d’Amazon pour écouter les connexions clientes. | Non, de valeur par défaut : 5439 |
| base de données | Nom de la base de données Redshift d’Amazon. | Oui |
| nom d’utilisateur | Nom de l’utilisateur qui a accès à la base de données.| Oui |
| mot de passe | Mot de passe pour le compte d’utilisateur.| Oui |


## <a name="dataset-type-properties"></a>Propriétés du type de groupe de données

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, consultez l’article de la [Création de groupes de données](data-factory-create-datasets.md) . Sections de structure, de disponibilité et de stratégie sont similaires pour tous les types de groupe de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de groupe de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le groupe de données de type **RelationalTable** (qui inclut le dataset d’Amazon Redshift) possède les propriétés suivantes

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| tableName | Nom de la table dans la base de données Redshift d’Amazon renvoyant à des services liés. | Non (si la **requête** de **RelationalSource** est spécifiée). | 

## <a name="copy-activity-type-properties"></a>Copier les propriétés de type d’activité

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Créer des Pipelines](data-factory-create-pipelines.md) . Les propriétés nom, description, d’entrée et de sortie des tables, des stratégies sont disponibles pour tous les types d’activités. 

D’autre part, les propriétés disponibles dans la section **typeProperties** de l’activité varient avec chaque type d’activité. Pour les activités de copie, qui varient selon les types de sources et les destinations.

Lorsque la source de l’activité de copie est de type **RelationalSource** (qui inclut Amazon Redshift) les propriétés suivantes sont disponibles dans la section de typeProperties :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| requête | La requête personnalisée permet de lire les données. | Chaîne de requête SQL. Par exemple : sélectionnez * from MyTable. | Non (si **tableName** du **groupe de données** est spécifiée). | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-amazon-redshift"></a>Mappage de type de Redshift d’Amazon

Comme mentionné dans l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , activité de copie effectue les conversions de type automatique à partir de types de sources pour recevoir les types de l’approche en deux étapes suivantes :

1. Convertir les types de source native type .NET
2. Convertir à partir du type .NET pour type de récepteurs natif

Lors du déplacement des données vers Amazon Redshift, les mappages suivants permettra à partir de types d’Amazon Redshift types .NET.

Type de Redshift d’Amazon | Type basé sur le .NET
-------------------- | ---------------
SMALLINT | Int16
NOMBRE ENTIER | Int32
BIGINT | Int64
DÉCIMAL | Décimal
RÉEL | Unique
DOUBLE PRÉCISION | Double
VALEUR BOOLÉENNE | Chaîne
CHAR | Chaîne
VARCHAR | Chaîne
DATE | DateTime
HORODATAGE | DateTime
TEXTE | Chaîne



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [copie activité performances & Tuning Guide](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés cet impact sur les performances de transfert de données (copie activité) dans Azure Data Factory et les différentes manières de l’optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 
- [Activité de copie de didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions étape par étape pour la création d’un pipeline avec une activité de copie. 