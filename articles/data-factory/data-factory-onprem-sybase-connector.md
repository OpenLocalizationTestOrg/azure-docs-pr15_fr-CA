<properties 
    pageTitle="Déplacer les données à partir de Sybase | Usine de données Azure" 
    description="Découvrez comment déplacer des données à partir de la base de données Sybase à l’aide de la fabrique de données Azure." 
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
    ms.date="09/20/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-sybase-using-azure-data-factory"></a>Déplacement des données à partir de Sybase à l’aide de la fabrique de données Azure 

Cet article décrit comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données vers une autre banque de données de Sybase. Cet article s’appuie sur l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de transfert de données avec l’activité de copie et de combinaisons de magasin de données pris en charge.

Service de fabrique de données prend en charge la connexion à des sources de Sybase sur site à l’aide de la passerelle de gestion des données. Consultez l’article de [transfert de données entre les emplacements locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et des instructions détaillées sur la configuration de la passerelle. 

> [AZURE.NOTE]
> Passerelle est requise même si la base de données Sybase est hébergé dans un ordinateur virtuel IaaS de Azure. Vous pouvez installer la passerelle sur la même VM IaaS comme magasin de données ou sur une machine virtuelle différente dans la mesure où la passerelle peut se connecter à la base de données. 

Usine de données prend actuellement en charge uniquement transférer des données à partir de Sybase pour d’autres magasins de données, mais pas d’autres banques de données dans Sybase.

## <a name="installation"></a>Installation

Pour la passerelle de gestion des données pour vous connecter à la base de données Sybase, vous devez installer le [fournisseur de données pour Sybase](http://go.microsoft.com/fwlink/?linkid=324846) sur le même système que la passerelle de gestion des données.

> [AZURE.NOTE] Voir [passerelle de résoudre les problèmes](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle des problèmes. 

## <a name="copy-data-wizard"></a>Assistant de duplication des données
La façon la plus simple pour créer un pipeline qui copie les données à partir d’une base de données Sybase à une des banques de données récepteur pris en charge est d’utiliser l’Assistant de copie de données. Consultez [didacticiel : créer un tuyau à l’aide d’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapides sur la création d’un tuyau à l’aide de l’Assistant de copie de données. 

L’exemple suivant fournit des exemples de définitions de JSON qui vous permet de créer un tuyau à l’aide de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [PowerShell d’Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils illustrent le copier des données à partir de la base de données Sybase vers le stockage Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.   

## <a name="sample-copy-data-from-sybase-to-azure-blob"></a>Exemple : Copier des données à partir de Sybase vers Azure Blob
Cet exemple montre comment copier des données à partir d’une base de données Sybase vers un stockage de Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple possède les entités de fabrique de données suivantes :

1.  Un service lié du type [OnPremisesSybase](data-factory-onprem-sybase-connector.md#sybase-linked-service-properties).
2.  Un service apprécié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](data-factory-onprem-sybase-connector.md#sybase-dataset-type-properties).
4.  Une sortie [dataset](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Le [pipeline](data-factory-create-pipelines.md) avec activité de copie qui utilise [RelationalSource](data-factory-onprem-sybase-connector.md#sybase-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir d’un résultat de requête dans la base de données Sybase dans un blob toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

Dans un premier temps, le programme d’installation la passerelle de gestion des données. Les instructions se trouvent dans l’article de [transfert de données entre les emplacements locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Service de Sybase lié :**

    {
        "name": "OnPremSybaseLinkedService",
        "properties": {
            "type": "OnPremisesSybase",
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


**Jeu de données d’entrée à Sybase :**

L’exemple suppose que vous ayez créé une table « MyTable » dans Sybase et il contient une colonne appelée « timestamp » pour les données de série de temps.

Définition de « externe » : true informe le service Data Factory que ce groupe de données est externe à l’usine de données et qu’il n’est pas produit par une activité sur le factory de données. Notez que le **type** du service lié est défini à : **RelationalTable**. 
    
    {
        "name": "SybaseDataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremSybaseLinkedService",
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
        "name": "AzureBlobSybaseDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifiée toutes les heures. Dans la définition de JSON de pipeline, le type de **source** est défini sur **RelationalSource** et type de **récepteurs** est définie sur **BlobSink**. La requête SQL spécifiée pour la propriété de **requête** sélectionne les données de bases de données. Table Orders dans la base de données.


    {
        "name": "CopySybaseToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from DBA.Orders"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "SybaseDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobSybaseDataSet"
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
                    "name": "SybaseToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="sybase-linked-service-properties"></a>Propriétés de service de Sybase lié

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service de Sybase lié.

Propriété | Description | Obligatoire
-------- | ----------- | --------
type de | La propriété type doit être définie : **OnPremisesSybase** | Oui
serveur | Nom du serveur Sybase. | Oui
base de données | Nom de la base de données Sybase. | Oui 
schéma  | Nom du schéma dans la base de données. | N°
authenticationType | Type d’authentification utilisé pour se connecter à la base de données Sybase. Les valeurs possibles sont : anonyme, Basic et Windows. | Oui
nom d’utilisateur | Spécifiez le nom d’utilisateur si vous utilisez l’authentification Basic ou Windows. | N°
mot de passe | Spécifiez le mot de passe pour le compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. |  N°
nom de la passerelle | Nom de la passerelle que le service Factory de données doit utiliser pour se connecter à la base de données de Sybase sur site. | Oui 

Pour plus d’informations sur la définition des informations d’identification pour une source de données de Sybase sur site, reportez-vous à la section [informations d’identification de la configuration et de sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="sybase-dataset-type-properties"></a>Propriétés du type de groupe de données Sybase

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, consultez l’article de la [Création de groupes de données](data-factory-create-datasets.md) . Sections de structure, de disponibilité et de stratégie d’un groupe de données JSON sont similaires pour tous les types de groupe de données (SQL Azure, blob Azure, table Azure, etc..).

La section typeProperties est différente pour chaque type de groupe de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section **typeProperties** pour le groupe de données de type **RelationalTable** (qui inclut le groupe de données Sybase) possède les propriétés suivantes :

Propriété | Description | Obligatoire
-------- | ----------- | --------
tableName | Nom de la table dans l’instance de base de données Sybase renvoyant à des services liés. | Non (si la **requête** de **RelationalSource** est spécifiée).

## <a name="sybase-copy-activity-type-properties"></a>Propriétés de type activité Sybase copie 

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, reportez-vous à l’article [Création de pipeline](data-factory-create-pipelines.md) . Les propriétés telles que nom, description, d’entrée et de sortie des tables et la stratégie sont disponibles pour tous les types d’activités. 

D’autre part, les propriétés disponibles dans la section typeProperties de l’activité varient avec chaque type d’activité. Pour les activités de copie, qui varient selon les types de sources et les destinations.

Lorsque la source est de type **RelationalSource** (y compris Sybase) les propriétés suivantes sont disponibles dans la section de **typeProperties** :

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
requête | La requête personnalisée permet de lire les données. | Chaîne de requête SQL. Par exemple : sélectionnez * from MyTable. | Non (si **tableName** du **groupe de données** est spécifiée).

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-sybase"></a>Mappage de type pour Sybase

Comme mentionné dans l’article [d’Activités de déplacement des données](data-factory-data-movement-activities.md) , l’activité de copie effectue les conversions de type automatique à partir de types de sources pour recevoir des types avec l’approche 2-étape suivante :

1. Convertir les types de source native type .NET
2. Convertir à partir du type .NET pour type de récepteurs natif

Sybase prend en charge T-SQL et les types de T-SQL. Pour une table de mappage de types sql à un type .NET, consultez [Un connecteur SQL Azure](data-factory-azure-sql-connector.md) l’article.

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [copie activité performances & Tuning Guide](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés cet impact sur les performances de transfert de données (copie activité) dans Azure Data Factory et les différentes manières de l’optimiser.