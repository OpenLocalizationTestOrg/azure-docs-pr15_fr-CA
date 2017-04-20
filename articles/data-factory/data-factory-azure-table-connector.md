<properties 
    pageTitle="Déplacer des données vers/à partir de la Table d’Azure | Microsoft Azure" 
    description="Découvrez comment déplacer des données vers/depuis le stockage par Table Azure à l’aide de la fabrique de données Azure." 
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
    ms.date="09/13/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Déplacement des données vers et à partir de la Table d’Azure à l’aide de la fabrique de données Azure

Cet article décrit comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données depuis/vers Azure Table depuis/vers un autre magasin de données. Cet article s’appuie sur l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble du déplacement des données et des combinaisons de magasin de données pris en charge avec l’activité de la copie.

## <a name="copy-data-wizard"></a>Assistant de duplication des données
Pour créer un pipeline qui copie des données vers/depuis le stockage par Table Azure le plus simple est d’utiliser l’Assistant de copie de données. Consultez [didacticiel : créer un tuyau à l’aide d’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapides sur la création d’un tuyau à l’aide de l’Assistant de copie de données. 

Les exemples suivants fournissent des exemples de définitions de JSON qui vous permet de créer un tuyau à l’aide de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [PowerShell d’Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils illustrent le copier des données vers et depuis le stockage par Table Azure et base de données de Blob Azure. Toutefois, les données peuvent être copiées **directement** à partir des sources des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.

## <a name="sample-copy-data-from-azure-table-to-azure-blob"></a>Exemple : Copier des données à partir de la Table d’Azure dans Azure Blob

L’exemple suivant s’affiche :

1.  Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (utilisé pour la table & de blob).
2.  Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [AzureTable](#azure-table-dataset-type-properties).
3.  Une sortie [dataset](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
3.  Le [pipeline](data-factory-create-pipelines.md) avec activité de copie qui utilise [AzureTableSource](#azure-table-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

L’exemple de copie des données appartenant à la partition par défaut dans une Table Azure dans un blob, toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples.

**Service de stockage Azure lié :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Usine de données Azure prend en charge deux types de services de stockage Azure lié : **AzureStorage** et **AzureStorageSas**. Pour le premier, vous spécifiez la chaîne de connexion qui inclut la clé de compte, et pour une version ultérieure, vous spécifiez l’Uri de la Signature de l’accès partagé (SAS). Pour plus d’informations, reportez-vous à la section [Services liés](#linked-services) .  

**Azure dataset d’entrée de Table :**

L’exemple suppose que vous avez créé une table « MyTable » dans la Table d’Azure.
 
Définition de « externe » : « true » informe le service Factory de données que le groupe de données est externe à l’usine de données et qu’il n’est pas produit par une activité sur le factory de données.

    {
      "name": "AzureTableInput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
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

**Dataset de sortie Blob Azure :**

Les données sont écrites dans un blob de nouveau toutes les heures (fréquence : heures, l’intervalle : 1). Le chemin d’accès du dossier pour le blob est calculée de manière dynamique en fonction de l’heure de début de la tranche qui est en cours de traitement. Le chemin d’accès du dossier utilise l’année, mois, jour et parties d’heures de l’heure de début. 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Pipeline avec l’activité de copie :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifié pour s’exécuter toutes les heures. Dans la définition de JSON de pipeline, le type de **source** est défini sur **AzureTableSource** et type de **récepteurs** est définie sur **BlobSink**. La requête SQL spécifiée avec la propriété **d’AzureTableSourceQuery** sélectionne les données de la partition par défaut toutes les heures à copier.

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureTabletoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                        {
                            "name": "AzureTableInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureTableSource",
                            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },              
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
             ]  
        }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-table"></a>Exemple : Copier des données d’Azure Blob Azure table

L’exemple suivant s’affiche :

1.  Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (utilisé pour la table & de blob)
3.  Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Une sortie [dataset](data-factory-create-datasets.md) de type [AzureTable](#azure-table-dataset-type-properties). 
4.  Le [pipeline](data-factory-create-pipelines.md) avec activité de copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) et [AzureTableSink](#azure-table-copy-activity-type-properties). 


Les copies d’exemple blob de données à partir d’un Azure temps série vers un Azure table toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples.

**Service de stockage Azure (pour les Table Azure et Blob) lié :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Usine de données Azure prend en charge deux types de services de stockage Azure lié : **AzureStorage** et **AzureStorageSas**. Pour le premier, vous spécifiez la chaîne de connexion qui inclut la clé de compte, et pour une version ultérieure, vous spécifiez l’Uri de la Signature de l’accès partagé (SAS). Pour plus d’informations, reportez-vous à la section [Services liés](#linked-services) . 

**Dataset d’entrée des Blob Azure :**

Données sont récupérées à partir d’un objet blob de nouveau toutes les heures (fréquence : heures, l’intervalle : 1). Le nom du chemin d’accès et de fichier de dossier pour le blob sont calculées de manière dynamique en fonction de l’heure de début de la tranche qui est en cours de traitement. Le chemin d’accès du dossier utilise la partie jour de l’heure de début, mois et année et nom de fichier utilise la partie heure de l’heure de début. « externe » : paramètre « true » informe le service Factory de données que le dataset est externe à l’usine de données et qu’il n’est pas produit par une activité sur le factory de données.
    
    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
          ],
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

**Table Azure dataset de sortie :**

L’exemple de copie des données dans une table nommée « MyTable » dans la Table d’Azure. Création d’une table Azure avec le même nombre de colonnes souhaité pour le fichier CSV de Blob contenant Nouvelles lignes sont ajoutées à la table toutes les heures. 

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Pipeline avec l’activité de copie :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifié pour s’exécuter toutes les heures. Dans la définition de JSON de pipeline, le type de **source** est défini sur **BlobSource** et type de **récepteurs** est définie sur **AzureTableSink**. 


    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureTableOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "AzureTableSink",
                "writeBatchSize": 100,
                "writeBatchTimeout": "01:00:00"
              }
            },
            "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },                      
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }

## <a name="linked-services"></a>Services liés
Il existe deux types de services liés, que vous pouvez utiliser pour lier un stockage blob Azure à une usine de données Azure. Ils sont : **AzureStorage** lié service et **AzureStorageSas** lié. Le service de stockage Azure lié fournit le factory de données disposant d’un accès global pour le stockage Azure. Considérant que, le SAS de stockage Azure (Signature de l’accès partagé) est lié service fournit le factory de données avec un accès restreint/temps au stockage Azure. Il n’y a pas de différences entre ces deux services liés. Choisissez le service lié adapté à vos besoins. Les sections suivantes fournissent plus de détails sur ces deux services liés.

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-table-dataset-type-properties"></a>Propriétés de type tableau Dataset Azure

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, consultez l’article de la [Création de groupes de données](data-factory-create-datasets.md) . Sections de structure, de disponibilité et de stratégie d’un groupe de données JSON sont similaires pour tous les types de groupe de données (SQL Azure, blob Azure, table Azure, etc..).

La section typeProperties est différente pour chaque type de groupe de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section **typeProperties** du groupe de données de type **AzureTable** possède les propriétés suivantes.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| tableName | Nom de la table dans l’instance de base de données de la Table Azure renvoyant à des services liés. | Oui. Lorsqu’un tableName est spécifié sans un azureTableSourceQuery, tous les enregistrements de la table sont copiées vers la destination. Si un azureTableSourceQuery est également spécifiée, les enregistrements de la table qui répond à la requête sont copiées vers la destination. |

### <a name="schema-by-data-factory"></a>Schéma en usine de données
Pour les banques de données sans schéma comme Table d’Azure, le service Data Factory déduit le schéma dans une des manières suivantes :

1.  Si vous spécifiez la structure des données à l’aide de la propriété **structure** dans la définition de groupe de données, le service Data Factory respecte cette structure comme le schéma. Dans ce cas, si une ligne ne contient pas une valeur pour une colonne, une valeur null est fournie pour celui-ci.
2. Si vous ne spécifiez pas la structure des données à l’aide de la propriété **structure** dans la définition de groupe de données, Data Factory déduit le schéma à l’aide de la première ligne dans les données. Dans ce cas, si la première ligne ne contient-elle pas le schéma complet, certaines colonnes sont ignorés dans le résultat de l’opération de copie.

Par conséquent, pour les sources de données sans schéma, la meilleure solution consiste à spécifier la structure de données à l’aide de la propriété de la **structure** .

## <a name="azure-table-copy-activity-type-properties"></a>Propriétés de type activité de copie de Table Azure

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Créer des Pipelines](data-factory-create-pipelines.md) . Les propriétés telles que nom, description, d’entrée et de sortie des groupes de données et les stratégies sont disponibles pour tous les types d’activités. 

D’autre part, les propriétés disponibles dans la section typeProperties de l’activité varient avec chaque type d’activité. Pour les activités de copie, qui varient selon les types de sources et les destinations.

**AzureTableSource** prend en charge les propriétés suivantes dans la section de typeProperties :

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | La requête personnalisée permet de lire les données. | Chaîne de requête de table Azure. Consultez les exemples dans la section suivante. | N° Lorsqu’un tableName est spécifié sans un azureTableSourceQuery, tous les enregistrements de la table sont copiées vers la destination. Si un azureTableSourceQuery est également spécifiée, les enregistrements de la table qui répond à la requête sont copiées vers la destination.
azureTableSourceIgnoreTableNotFound | Indiquer s’il a l’exception de la table n’existe pas. | TRUE<br/>FALSE | N° |

### <a name="azuretablesourcequery-examples"></a>exemples d’azureTableSourceQuery

Si la colonne de la Table d’Azure est de type chaîne : 

    azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"

Si la colonne de la Table d’Azure est de type datetime : 

    "azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"


**AzureTableSink** prend en charge les propriétés suivantes dans la section de typeProperties :


Propriété | Description | Valeurs autorisées | Obligatoire  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | Valeur par défaut partition qui peut être utilisé par le récepteur. | Une valeur de chaîne. | N° 
azureTablePartitionKeyName | Spécifiez le nom de la colonne dont les valeurs sont utilisées comme clés de la partition. Si non spécifié, AzureTableDefaultPartitionKeyValue est utilisé comme clé de partition. | Un nom de colonne. | N° |
azureTableRowKeyName | Spécifiez le nom de la colonne dont les valeurs de colonne sont utilisées comme clé de la ligne. Si non spécifié, utilisez un GUID pour chaque ligne. | Un nom de colonne. | N°  
azureTableInsertType | Le mode à insérer des données dans la table Azure.<br/><br/>Cette propriété contrôle si les lignes existantes dans la table de sortie avec les clés de partition et la ligne correspondantes ont leurs valeurs à remplacer ou à fusionner. <br/><br/>Pour en savoir plus sur le fonctionnement de ces paramètres (fusion et remplacement), consultez Rubriques [d’Insertion ou l’entité de fusion](https://msdn.microsoft.com/library/azure/hh452241.aspx) et [Insérer ou remplacer une entité](https://msdn.microsoft.com/library/azure/hh452242.aspx) . <br/><br> Ce paramètre s’applique au niveau de la ligne, pas au niveau de la table, et aucune de ces options supprime des lignes de la table de sortie qui n’existent pas dans l’entrée. | fusion (par défaut)<br/>remplacer | N° 
writeBatchSize | Insère des données dans la table Azure lorsque le writeBatchSize ou le writeBatchTimeout est atteint. | Entier (nombre de lignes)| Non (par défaut : 10000) 
writeBatchTimeout | Insère des données dans la table Azure lorsque le writeBatchSize ou le writeBatchTimeout est atteint. | TimeSpan<br/><br/>Exemple : « 00 : 20:00 » (20 minutes) | Non (valeur par défaut pour le délai d’attente de stockage client par défaut valeur s 90)

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mapper une colonne source vers une colonne de destination à l’aide du traducteur de propriété JSON avant de pouvoir utiliser la colonne de destination comme l’azureTablePartitionKeyName.

Dans l’exemple suivant, la colonne de la source DivisionID est mappé à la colonne de destination : DivisionID.  

    "translator": {
        "type": "TabularTranslator",
        "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
    } 

Le DivisionID est spécifié en tant que clé de partition. 

    "sink": {
        "type": "AzureTableSink",
        "azureTablePartitionKeyName": "DivisionID",
        "writeBatchSize": 100,
        "writeBatchTimeout": "01:00:00"
    }


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-azure-table"></a>Mappage de type pour la Table Azure

Comme mentionné dans l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , activité de copie effectue les conversions de type automatique à partir de types de sources pour recevoir les types de l’approche en deux étapes suivantes.

1. Convertir les types de source native type .NET
2. Convertir à partir du type .NET pour type de récepteurs natif

Lors du déplacement des données vers et à partir de la Table d’Azure, le suivant [mappages définis par le service de la Table d’Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) sont utilisés à partir de types d’Azure Table OData en type .NET et vice versa. 

| Type de données d’OData | Type .NET | Détails |
| --------------- | --------- | ------- |
| Edm.Binary | Byte] | Un tableau d’octets jusqu'à 64 Ko. |
| Edm.Boolean | bool | Une valeur de type Boolean. |
| Edm.DateTime | DateTime | Une valeur de 64 bits exprimée en temps universel coordonné (UTC). La plage prise en charge de DateTime commence à partir de 12:00 minuit, 1er janvier 1601 apr. J.C. (NOTRE ÈRE), UTC. La plage se termine au 31 décembre 9999. |
| Edm.Double | Double | Valeur à virgule flottante 64 bits. |
| Edm.Guid | GUID | Un identificateur global unique de 128 bits. |
| Edm.Int32 | Int32 ou int | Un entier de 32 bits. |
| Edm.Int64 | Int64 ou long | Un entier 64 bits. |
| Edm.String | Chaîne | Une valeur codées en UTF-16. Les valeurs de chaîne peuvent être de 64 Ko. |

### <a name="type-conversion-sample"></a>Exemple de Conversion de type

L’exemple suivant est pour copier les données à partir d’un Blob Azure Azure table avec des conversions de type. 

Supposons que le groupe de données Blob est au format CSV et comporte trois colonnes. Un d’eux est une colonne datetime au format datetime personnalisées à l’aide des noms Français abrégées pour les jours de la semaine. 

Définir le groupe de données Blob Source ainsi que les définitions de type pour les colonnes comme suit.
    
    {
        "name": " AzureBlobInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1,
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

Étant donné le mappage de type Azure Table OData type pour un type .NET, vous définissez la table dans la Table d’Azure avec le schéma suivant. 

**Schéma de Table Azure :**

Nom de la colonne | Type de
----------- | --------
ID utilisateur | Edm.Int64
nom | Edm.String 
LastLoginDate | Edm.DateTime

Définissez ensuite le groupe de données Azure Table comme suit. Vous n’êtes pas obligé de spécifier la section « structure » avec les informations de type dans la mesure où les informations de type sont déjà spécifiées dans le magasin de données sous-jacent.

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

Dans ce cas, Data Factory automatiquement conversions de type, y compris le champ Datetime au format datetime personnalisés à l’aide de la culture « fr-fr » lors du déplacement des données à partir de l’objet Blob Azure table.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Pour en savoir plus sur les facteurs clés cet impact sur les performances de transfert de données (copie activité) dans Azure Data Factory et les différentes manières d’optimiser, consultez [performances de copie d’activité & Guide d’optimisation](data-factory-copy-activity-performance.md).







