<properties 
    pageTitle="Déplacer les données de DB2 | Usine de données Azure" 
    description="En savoir plus sur la façon de déplacer des données d’une base de données DB2 à l’aide de la fabrique de données Azure" 
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
    ms.date="09/06/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-db2-using-azure-data-factory"></a>Déplacement des données à partir de DB2 à l’aide de la fabrique de données Azure
Cet article décrit comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données à partir de DB2 aux données d’une autre banque. Cet article s’appuie sur l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de transfert de données avec l’activité de copie et de combinaisons de magasin de données pris en charge.

Usine de données prend en charge la connexion à des sources de DB2 sur site à l’aide de la passerelle de gestion des données. Consultez l’article de [transfert de données entre les emplacements locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et des instructions détaillées sur la configuration de la passerelle. 

> [AZURE.NOTE]
La passerelle permet de se connecter à DB2, même s’il est hébergé dans Azure IaaS VMs. Si vous essayez de vous connecter à une instance de DB2 hébergées dans le cloud, vous pouvez également installer l’instance passerelle dans la VM IaaS.

Usine de données prend actuellement en charge uniquement transférer des données à partir de DB2 pour d’autres magasins de données, mais pas d’autres magasins de données pour DB2. 

## <a name="installation"></a>Installation 

La passerelle de gestion des données fournit un pilote DB2 intégré qui prend en charge les éléments suivants : 

- SQLAM 9 / 10 / 11
- DB2 pour LUW (Linux, Unix, Windows)
- DB2 pour z/OS et DB2 pour i (alias en tant que / 400)

Par conséquent, vous n’avez plus besoin d’installer manuellement les pilotes lors de la copie des données à partir de DB2.

> [AZURE.NOTE] Voir [passerelle de résoudre les problèmes](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle des problèmes. 

## <a name="copy-data-wizard"></a>Assistant de duplication des données
La façon la plus simple pour créer un pipeline qui copie les données à partir d’une base de données DB2 est d’utiliser l’Assistant de copie de données. Consultez [didacticiel : créer un tuyau à l’aide d’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapides sur la création d’un tuyau à l’aide de l’Assistant de copie de données. 

Les exemples suivants fournissent des exemples de définitions de JSON qui vous permet de créer un tuyau à l’aide de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [PowerShell d’Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils illustrent le copier des données à partir de la base de données DB2 et de stockage des objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.

## <a name="sample-copy-data-from-db2-to-azure-blob"></a>Exemple : Copier des données à partir de DB2 dans Azure Blob

Cet exemple montre comment copier les données d’une base de données de DB2 local à un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple possède les entités de fabrique de données suivantes :

1.  Un service lié du type [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties).
2.  Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3.  Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4.  Une sortie [dataset](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
5.  Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

L’exemple copie les données à partir d’un résultat de requête dans une base de données DB2 pour un blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

Dans un premier temps, installer et configurer une passerelle de gestion des données. Les instructions sont dans l’article de [transfert de données entre les emplacements locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Service de DB2 lié :**

    {
        "name": "OnPremDb2LinkedService",
        "properties": {
            "type": "OnPremisesDb2",
            "typeProperties": {
                "server": "<server>",
                "database": "<database>",
                "schema": "<schema>",
                "authenticationType": "<authentication type>",
                "username": "<username>",
                "password": "<password>",
                "gatewayName": "<gatewayName>"
            }
        }
    }


**Service de stockage lié Blob Azure :**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorageLinkedService",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
            }
        }
    }

**Groupe de données DB2 d’entrée :**

L’exemple suppose que vous ayez créé une table « MyTable » dans DB2 et il contient une colonne appelée « timestamp » pour les données de série de temps.

Définition de « externe » : true informe le service Data Factory que ce groupe de données est externe à l’usine de données et qu’il n’est pas produit par une activité sur le factory de données. Notez que le **type** est défini sur **RelationalTable**. 

    {
        "name": "Db2DataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremDb2LinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }


**Dataset de sortie Blob Azure :**

Les données sont écrites dans un blob de nouveau toutes les heures (fréquence : heures, l’intervalle : 1). Le chemin d’accès du dossier pour le blob est calculée de manière dynamique en fonction de l’heure de début de la tranche qui est en cours de traitement. Le chemin d’accès du dossier utilise l’année, mois, jour et parties d’heures de l’heure de début.

    {
        "name": "AzureBlobDb2DataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline avec activité de copie :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifié pour s’exécuter toutes les heures. Dans la définition de JSON de pipeline, le type de **source** est défini sur **RelationalSource** et type de **récepteurs** est définie sur **BlobSink**. La requête SQL spécifiée pour la propriété de **requête** sélectionne les données de la table commandes.

    {
        "name": "CopyDb2ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from \"Orders\""
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Db2DataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobDb2DataSet"
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
                    "name": "Db2ToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="db2-linked-service-properties"></a>Propriétés du service DB2 liée

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service de DB2 lié. 

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| type de | La propriété type doit être définie : **OnPremisesDB2** | Oui |
| serveur | Nom du serveur DB2. | Oui |
| base de données | Nom de la base de données DB2. | Oui |
| schéma | Nom du schéma dans la base de données. Le nom de schéma est sensible à la casse. | N° |
| authenticationType | Type d’authentification utilisé pour se connecter à la base de données DB2. Les valeurs possibles sont : anonyme, Basic et Windows. | Oui |
| nom d’utilisateur | Spécifiez le nom d’utilisateur si vous utilisez l’authentification Basic ou Windows. | N° |
| mot de passe | Spécifiez le mot de passe pour le compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. | N° |
| nom de la passerelle | Nom de la passerelle que le service Factory de données doit utiliser pour se connecter à la base de données de DB2 sur site. | Oui |

Pour plus d’informations sur la définition des informations d’identification pour une source de données DB2 sur site, reportez-vous à la section [informations d’identification de la configuration et de sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) . 


## <a name="db2-dataset-type-properties"></a>Propriétés du type de groupe de données DB2

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, consultez l’article de la [Création de groupes de données](data-factory-create-datasets.md) . Sections de structure, de disponibilité et de stratégie d’un groupe de données JSON sont similaires pour tous les types de groupe de données (SQL Azure, blob Azure, table Azure, etc..).

La section typeProperties est différente pour chaque type de groupe de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le groupe de données de type RelationalTable (qui inclut le groupe de données DB2) possède les propriétés suivantes.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| tableName | Nom de la table dans l’instance de base de données DB2 renvoyant à des services liés. Le paramètre tableName est sensible à la casse. | Non (si la **requête** de **RelationalSource** est spécifiée). |

## <a name="db2-copy-activity-type-properties"></a>Propriétés de type activité DB2

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Créer des Pipelines](data-factory-create-pipelines.md) . Les propriétés nom, description, d’entrée et de sortie des tables, des stratégies sont disponibles pour tous les types d’activités. 

D’autre part, les propriétés disponibles dans la section typeProperties de l’activité varient avec chaque type d’activité. Pour les activités de copie, qui varient selon les types de sources et les destinations.

Pour les activités de copie, lorsque la source est de type **RelationalSource** (qui inclut la DB2) les propriétés suivantes sont disponibles dans la section de typeProperties :


| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------- | -------------- |
| requête | La requête personnalisée permet de lire les données. | Chaîne de requête SQL. Par exemple : `"query": "select * from "MySchema"."MyTable""`. | Non (si **tableName** du **groupe de données** est spécifiée).|

> [AZURE.NOTE] Les noms de schéma et de table respectent la casse. Placez les noms de « » (guillemets doubles) dans la requête.  

**Exemple :**

    "query": "select * from "DB2ADMIN"."Customers""


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-db2"></a>Mappage de type pour DB2
Comme mentionné dans l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , l’activité de copie effectue les conversions de type automatique à partir de types de sources pour recevoir des types avec l’approche 2-étape suivante :

1. Convertir les types de source native type .NET
2. Convertir à partir du type .NET pour type de récepteurs natif

Lors du déplacement de données pour DB2, les mappages suivants sont utilisés à partir de DB2 type pour un type .NET.

Type de base de données DB2 | Type de.NET Framework 
----------------- | ------------------- 
SmallInt | Int16
Nombre entier | Int32
BigInt | Int64
Réel | Unique
Double | Double
Float | Double
Décimal | Décimal
DecimalFloat | Décimal
Numérique | Décimal
Date | DateTime
Heure | TimeSpan
Horodatage | DateTime
XML | Byte]
Char | Chaîne
VarChar | Chaîne
LongVarChar | Chaîne
DB2DynArray | Chaîne
Binaire | Byte]
VarBinary | Byte]
LongVarBinary | Byte]
Graphique | Chaîne
VarGraphic | Chaîne
LongVarGraphic | Chaîne
CLOB | Chaîne
Objet BLOB | Byte]
DbClob | Chaîne
SmallInt | Int16
Nombre entier | Int32
BigInt | Int64
Réel | Unique
Double | Double
Float | Double
Décimal | Décimal
DecimalFloat | Décimal
Numérique | Décimal
Date | DateTime
Heure | TimeSpan
Horodatage | DateTime
XML | Byte]
Char | Chaîne


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [copie activité performances & Tuning Guide](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés cet impact sur les performances de transfert de données (copie activité) dans Azure Data Factory et les différentes manières de l’optimiser.


