<properties 
    pageTitle="Déplacer les données d’Amazon Simple Storage Service à l’aide de Data Factory | Microsoft Azure" 
    description="Découvrez comment déplacer des données à partir d’Amazon Simple Storage Service (S3) à l’aide de la fabrique de données Azure." 
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
    ms.date="10/24/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Déplacement des données à partir d’Amazon Simple Storage Service à l’aide de la fabrique de données Azure

Cet article décrit comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données à partir d’Amazon Simple Storage Service (S3) à des données d’une autre banque. Cet article s’appuie sur l’article [d’activités de déplacement de données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble de transfert de données et la liste des magasins de données prises en charge de source/récepteur avec activité de copie.  

Usine de données prend actuellement en charge uniquement DEPLACEMENT DE DONNEES d’Amazon S3 à d’autres magasins de données, mais ne pas pour le déplacement des données issues d’autres magasins de données à Amazon S3.

## <a name="required-permissions"></a>Autorisations requises

Pour copier des données d’Amazon S3, assurez-vous que vous avez été accordées autorisations ci-dessous :

- **S3:GetObject** et **s3:GetObjectVersion** pour les opérations d’objet Amazon S3
- **S3:ListBucket** et **s3:ListAllMyBuckets** (utilisé dans l’Assistant de copie uniquement) pour les opérations de compartiment de S3 Amazon 

Vous trouverez la liste complète des autorisations Amazon S3 avec détails de [Spécifier des autorisations dans une stratégie](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="copy-data-wizard"></a>Assistant de duplication des données
La façon la plus simple pour créer un pipeline qui copie les données à partir d’Amazon S3 est d’utiliser l’Assistant de copie de données. Consultez [didacticiel : créer un tuyau à l’aide d’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapides sur la création d’un tuyau à l’aide de l’Assistant de copie de données. 

L’exemple suivant fournit des exemples de définitions de JSON qui vous permet de créer un tuyau à l’aide de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [PowerShell d’Azure](data-factory-copy-activity-tutorial-using-powershell.md). Il vous montre comment copier les données d’Amazon S3 pour le stockage des objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-s3-to-azure-blob"></a>Exemple : Copier des données à partir d’Amazon S3 dans Azure Blob
Cet exemple montre comment copier des données à partir d’un S3 Amazon pour un stockage de Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple possède les entités de fabrique de données suivantes :

- Un service lié du type [AwsAccessKey](#linked-service-properties).
- Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [AmazonS3](#dataset-type-properties).
- Une sortie [dataset](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [FileSystemSource](#copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir de S3 d’Amazon pour un blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

**Service de S3 Amazon lié**

    {
        "name": "AmazonS3LinkedService",
        "properties": {
            "type": "AwsAccessKey",
            "typeProperties": {
                "accessKeyId": "<access key id>",
                "secretAccessKey": "<secret access key>"
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

**Jeu de données d’entrée Amazon S3**

Définition de **« externe » : true** informe le service Factory de données que le dataset est externe à l’usine de données et qu’il n’est pas produit par une activité sur le factory de données. Définir cette propriété sur true sur un groupe de données d’entrée qui n’est pas produit par une activité dans le pipeline.

    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
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
                "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifié pour s’exécuter toutes les heures. Dans la définition de JSON de pipeline, le type de **source** est défini sur **FileSystemSource** et type de **récepteurs** est définie sur **BlobSink**. 
    
    {
        "name": "CopyAmazonS3ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "FileSystemSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonS3InputDataset"
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
                    "name": "AmazonS3ToBlob"
                }
            ],
            "start": "2014-08-08T18:00:00Z",
            "end": "2014-08-08T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propriétés du service lié

Le tableau suivant fournit la description pour les éléments JSON spécifiques à Amazon S3 (**AwsAccessKey**) lié service.

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------- | ------- |  
| accessKeyID | ID de la clé secrète d’accès. | chaîne | Oui |
| secretAccessKey | La clé secrète d’accès lui-même. | Chaîne secrète cryptée | Oui | 


## <a name="dataset-type-properties"></a>Propriétés du type de groupe de données

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, consultez l’article de la [Création de groupes de données](data-factory-create-datasets.md) . Sections de structure, de disponibilité et de stratégie sont similaires pour tous les types de groupe de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de groupe de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le groupe de données de type **AmazonS3** (qui inclut le dataset d’Amazon S3) possède les propriétés suivantes

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------- | ------ | 
| bucketName | Le nom de compartiment de S3. | Chaîne | Oui |
| clé | La clé de l’objet S3. | Chaîne | N° | 
| préfixe | Préfixe de la clé de l’objet S3. Les objets dont les clés commencent par ce préfixe sont sélectionnés. S’applique uniquement lorsque la clé est vide. | Chaîne | N° | 
| Version | La version de l’objet S3 si S3 de contrôle de version est activé. | Chaîne | N° |  
| format | Les types de format suivants sont pris en charge : **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Définissez **la propriété sous un format** à l’une de ces valeurs. Consultez les sections [Spécifiant TextFormat](#specifying-textformat), [Spécifiant les AvroFormat](#specifying-avroformat), [Spécifiant les JsonFormat](#specifying-jsonformat), [Spécifiant la OrcFormat](#specifying-orcformat)et [ParquetFormat de spécifier](#specifying-parquetformat) pour plus de détails. Si vous souhaitez copier les fichiers en tant que-est entre bases orientées fichier (copie binaire), vous pouvez ignorer la section de format dans les deux définitions de jeu de données d’entrée et de sortie.| N°
| compression | Permet de spécifier le type et le niveau de compression pour les données. Types pris en charge sont : **GZip**et **Deflate**et **BZip2** niveaux pris en charge sont : **optimale** et la **plus rapide**. Actuellement, les paramètres de compression ne sont pas pris en charge pour les données de **AvroFormat** ou de **OrcFormat**. Pour plus d’informations, reportez-vous à la section [prise en charge de la Compression](#compression-support) .  | N° |

> [AZURE.NOTE] bucketName + clé spécifie l’emplacement de l’objet S3 où compartiment est le conteneur racine pour les objets de S3 et la clé est le chemin d’accès complet à l’objet de S3.

### <a name="sample-dataset-with-prefix"></a>Exemple de dataset avec le préfixe

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "prefix": "testFolder/test",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }

### <a name="sample-data-set-with-version"></a>Ensemble de données exemple (sans la version)

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


### <a name="dynamic-paths-for-s3"></a>Chemins d’accès dynamiques pour S3

Dans l’exemple, nous utilisons des valeurs fixes pour les propriétés de clé et bucketName dans le dataset d’Amazon S3. 

    "key": "testFolder/test.orc",
    "bucketName": "testbucket",

Vous pouvez avoir à Data Factory de calculer la clé et la bucketName de manière dynamique au moment de l’exécution à l’aide de variables système comme SliceStart.

    "key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
    "bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

Vous pouvez faire de même pour la propriété de préfixe d’un groupe de données Amazon S3. Pour obtenir une liste des variables et des fonctions prises en charge, reportez-vous à la section [variables système et les fonctions de Data Factory](data-factory-functions-variables.md) . 


[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## <a name="copy-activity-type-properties"></a>Copier les propriétés de type d’activité

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Créer des Pipelines](data-factory-create-pipelines.md) . Les propriétés nom, description, d’entrée et de sortie des tables, des stratégies sont disponibles pour tous les types d’activités. 

D’autre part, les propriétés disponibles dans la section **typeProperties** de l’activité varient avec chaque type d’activité. Pour les activités de copie, qui varient selon les types de sources et les destinations.

Lorsque la source de l’activité de copie est de type **FileSystemSource** (qui inclut Amazon S3), les propriétés suivantes sont disponibles dans la section de typeProperties :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- | 
| récursive | Spécifie de manière récursive S3 de répertorier les objets situés sous le répertoire. | Vrai/faux | N° | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [copie activité performances & Tuning Guide](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés cet impact sur les performances de transfert de données (copie activité) dans Azure Data Factory et les différentes manières de l’optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 
- [Activité de copie de didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions étape par étape pour la création d’un pipeline avec une activité de copie. 
