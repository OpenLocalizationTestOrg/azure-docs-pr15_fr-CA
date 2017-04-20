<properties 
    pageTitle="Déplacer des données vers/à partir d’Oracle à l’aide de Data Factory | Microsoft Azure" 
    description="Découvrez comment déplacer des données entre une base de données Oracle qui est local à l’aide de la fabrique de données Azure." 
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

# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Déplacement des données vers/à partir d’Oracle sur site à l’aide de la fabrique de données Azure 

Cet article décrit comment vous pouvez utiliser activité de copie de données en usine pour déplacer des données vers/à partir d’Oracle à partir de/vers une autre données stocker. Cet article s’appuie sur l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de transfert de données avec l’activité de copie et de combinaisons de magasin de données pris en charge.

## <a name="installation"></a>Installation 
Pour le service Factory de données Azure être en mesure de se connecter à votre base de données Oracle de locaux, vous devez installer les composants suivants : 

- Gestion de passerelle de données sur le même ordinateur qui héberge la base de données ou sur un ordinateur distinct afin d’éviter la concurrence pour les ressources de la base de données. Passerelle de gestion des données est un agent de client qui se connecte les sources de données sur site pour les services en nuage de façon sécurisée et gérée. Article de [déplacer des données entre des locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) voir pour plus d’informations sur la passerelle de gestion des données. 
- Fournisseur de données Oracle pour .NET. Ce composant est inclus dans les [Composants Oracle Data Access pour Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Installer la version appropriée (32/64 bits) sur la machine hôte où la passerelle est installée. [12.1 de .NET de fournisseur de données Oracle](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) peut accéder à la base de données Oracle 10 g Release 2 ou version ultérieure.

    Si vous choisissez de « Installation XCopy », procédez comme suit dans le fichier readme.htm. Nous vous recommandons de que choisir du programme d’installation avec une interface utilisateur (non-XCopy une). 
 
    Après avoir installé le fournisseur, redémarrez le service hôte de passerelle de gestion des données sur votre ordinateur à l’aide de Services applet (ou des) Gestionnaire de Configuration de passerelle de gestion des données.  

> [AZURE.NOTE] Voir [passerelle de résoudre les problèmes](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle des problèmes. 

## <a name="copy-data-wizard"></a>Assistant de duplication des données
La façon la plus simple pour créer un pipeline qui copie les données depuis/vers une base de données Oracle à une des banques de données récepteur pris en charge est d’utiliser l’Assistant de copie de données. Consultez [didacticiel : créer un tuyau à l’aide d’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapides sur la création d’un tuyau à l’aide de l’Assistant de copie de données. 

L’exemple suivant fournit des exemples de définitions de JSON qui vous permet de créer un tuyau à l’aide de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [PowerShell d’Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils illustrent le copier des données depuis/vers une base de données Oracle vers/depuis le stockage Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>Exemple : Copier des données d’Oracle pour les Blob Azure
Cet exemple montre comment copier les données d’une base de données Oracle de local à un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple possède les entités de fabrique de données suivantes :

1.  Un service lié du type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.  Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
4.  Une sortie [dataset](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.  Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) comme source et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) en tant que récepteur.

L’exemple copie les données d’une table dans une base de données Oracle de local à un objet blob de toutes les heures. Pour plus d’informations sur les différentes propriétés utilisées dans l’exemple, consultez la documentation dans les sections suivantes les exemples.

**Service de Oracle lié :**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Service de stockage lié Blob Azure :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Jeu de données d’entrée à Oracle :**

L’exemple suppose que vous ayez créé une table « MyTable » dans Oracle et il contient une colonne nommée « timestampcolumn » pour les données de série de temps. 

Définition de « externe » : « true » informe le service Factory de données que le groupe de données est externe à l’usine de données et qu’il n’est pas produit par une activité sur le factory de données.

    {
        "name": "OracleInput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
               "external": true,
            "availability": {
                "offset": "01:00:00",
                "interval": "1",
                "anchorDateTime": "2014-02-27T12:00:00",
                "frequency": "Hour"
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

Les données sont écrites dans un blob de nouveau toutes les heures (fréquence : heures, l’intervalle : 1). Le nom du chemin d’accès et de fichier de dossier pour le blob sont calculées de manière dynamique en fonction de l’heure de début de la tranche qui est en cours de traitement. Le chemin d’accès du dossier utilise l’année, mois, jour et parties d’heures de l’heure de début.
    
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


**Pipeline avec activité de copie :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifiée toutes les heures. Dans la définition de JSON de pipeline, le type de **source** est défini sur **OracleSource** et type de **récepteurs** est définie sur **BlobSink**.  La requête SQL spécifiée par la propriété de **oracleReaderQuery** sélectionne les données de la dernière heure à copier.

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " OracleInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


Vous devez ajuster la chaîne de requête en fonction de la configuration des dates dans votre base de données Oracle. Si vous voyez le message d’erreur suivant : 

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

Vous devrez peut-être modifier la requête, comme illustré dans l’exemple suivant (à l’aide de la fonction to_date) :

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>Exemple : Copier des données d’objet Blob d’Azure pour Oracle
Cet exemple montre comment copier des données à partir d’un stockage d’objets Blob Azure à une base de données Oracle de locaux. Toutefois, les données peuvent être copiées **directement** à partir des sources indiquées [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple possède les entités de fabrique de données suivantes :

1.  Un service lié du type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.  Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Une sortie [dataset](data-factory-create-datasets.md) de type [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
5.  Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) comme source [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) en tant que récepteur.

L’exemple copie les données à partir d’un objet blob à une table dans une base de données Oracle de locaux toutes les heures. Pour plus d’informations sur les différentes propriétés utilisées dans l’exemple, consultez la documentation dans les sections suivantes les exemples.

**Service de Oracle lié :**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Service de stockage lié Blob Azure :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Dataset d’entrée des Blob Azure**

Données sont récupérées à partir d’un objet blob de nouveau toutes les heures (fréquence : heures, l’intervalle : 1). Le nom du chemin d’accès et de fichier de dossier pour le blob sont calculées de manière dynamique en fonction de l’heure de début de la tranche qui est en cours de traitement. Le chemin d’accès du dossier utilise la partie jour de l’heure de début, mois et année et nom de fichier utilise la partie heure de l’heure de début. « externe » : paramètre « true » informe le service Factory de données que cette table est externe à l’usine de données et qu’il n’est pas produite par une activité sur le factory de données.

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

**Dataset de sortie d’Oracle :**

L’exemple suppose que vous avez créé une table « MyTable » dans Oracle. Créez la table dans Oracle avec le même nombre de colonnes souhaité pour le fichier CSV de Blob contenant. Nouvelles lignes sont ajoutées à la table toutes les heures.

    {
        "name": "OracleOutput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": "1"
            }
        }
    }


**Pipeline avec activité de copie :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifié pour s’exécuter toutes les heures. Dans la définition de JSON de pipeline, le type de **source** est défini sur **BlobSource** et le type de **récepteurs** est défini à **OracleSink**.  

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "OracleOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "OracleSink"
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


## <a name="oracle-linked-service-properties"></a>Propriétés du service Oracle lié

Le tableau suivant fournit la description pour les éléments JSON spécifiques à un service Oracle lié. 

Propriété | Description | Obligatoire
-------- | ----------- | --------
type de | La propriété type doit être définie : **OnPremisesOracle** | Oui
connectionString | Indiquez les informations nécessaires pour vous connecter à l’instance de base de données Oracle pour la propriété connectionString. | Oui 
nom de la passerelle | Nom de la passerelle qui sert à connecter au serveur Oracle sur site | Oui

Pour plus d’informations sur la définition des informations d’identification pour une source de données Oracle sur site, reportez-vous à la section [informations d’identification de la configuration et de sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .
## <a name="oracle-dataset-type-properties"></a>Propriétés du type de groupe de données Oracle

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, consultez l’article de la [Création de groupes de données](data-factory-create-datasets.md) . Sections de structure, de disponibilité et de stratégie d’un groupe de données JSON sont similaires pour tous les types de groupe de données (Oracle, blob Azure, table Azure, etc.).
 
La section typeProperties est différente pour chaque type de groupe de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties du groupe de données de type OracleTable possède les propriétés suivantes :

Propriété | Description | Obligatoire
-------- | ----------- | --------
tableName | Nom de la table dans la base de données Oracle auquel fait référence le service lié. | Non (si **oracleReaderQuery** de **OracleSource** est spécifiée).

## <a name="oracle-copy-activity-type-properties"></a>Propriétés de type activité Oracle copie

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Créer des Pipelines](data-factory-create-pipelines.md) . Les propriétés telles que nom, description, d’entrée et de sortie des tables et la stratégie sont disponibles pour tous les types d’activités. 

> [AZURE.NOTE]
L’activité de copie accepte une seule entrée et génère une seule sortie.

D’autre part, les propriétés disponibles dans la section typeProperties de l’activité varient avec chaque type d’activité. Pour les activités de copie, qui varient selon les types de sources et les destinations.

### <a name="oraclesource"></a>OracleSource
Dans l’activité de copie, lorsque la source est de type **OracleSource** les propriétés suivantes sont disponibles dans la section de **typeProperties** :

Propriété | Description |Valeurs autorisées | Obligatoire
-------- | ----------- | ------------- | --------
oracleReaderQuery | La requête personnalisée permet de lire les données. | Chaîne de requête SQL. Par exemple : sélectionnez *from MyTable <br/> <br/>si non spécifié, l’instruction SQL exécutée : sélectionnez* from MyTable | Non (si **tableName** du **groupe de données** est spécifiée).

### <a name="oraclesink"></a>OracleSink
**OracleSink** prend en charge les propriétés suivantes :

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
writeBatchTimeout | Temps d’attente pour l’opération d’insertion de lot terminer avant son expiration. | TimeSpan<br/><br/> Exemple : 00:30:00 (30 minutes). | N°
writeBatchSize | Insère des données dans la table SQL lorsque la taille de mémoire tampon atteint writeBatchSize.   | Entier (nombre de lignes)| Non (par défaut : 10000)  
sqlWriterCleanupScript | Permet de spécifier une requête pour l’activité de copie exécuter les tels que les données d’une section spécifique sont nettoyées. | Une instruction de requête. | N°
sliceIdentifierColumnName | Spécifiez le nom de colonne pour une activité de copie remplir avec un identificateur de tranche généré automatiquement, qui est utilisé pour nettoyer les données d’un secteur spécifique lorsque le réexécuter. | Nom de la colonne d’une colonne avec le type de données de binary(32). | N°


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Mappage de type pour Oracle

Comme mentionné dans l’activité de copie de l’article [des activités circulation de données](data-factory-data-movement-activities.md) effectue les conversions de type automatique à partir de types de sources pour recevoir des types avec l’approche 2-étape suivante :

1. Convertir les types de source native type .NET
2. Convertir à partir du type .NET pour type de récepteurs natif

Lorsque vous déplacez des données d’Oracle, les mappages suivants servent à partir du type de données d’Oracle au type .NET et vice versa.

Type de données Oracle | Type de données.NET Framework
---------------- | ------------------------
BFILE | Byte]
OBJET BLOB | Byte]
CHAR | Chaîne
CLOB | Chaîne
DATE | DateTime
FLOAT | Décimal
NOMBRE ENTIER | Décimal
INTERVALLE ANNEE A MOIS | Int32
INTERVALLE JOUR A SECONDE | TimeSpan
LONG | Chaîne
LONG RAW | Byte]
NCHAR | Chaîne
NCLOB | Chaîne
NUMÉRO DE | Décimal
NVARCHAR2 | Chaîne
BRUT | Byte]
ROWID | Chaîne
HORODATAGE | DateTime
ESTAMPILLE AVEC FUSEAU HORAIRE LOCAL | DateTime
ESTAMPILLE AVEC FUSEAU HORAIRE | DateTime
ENTIER NON SIGNÉ | Numéro de
VARCHAR2 | Chaîne
XML | Chaîne

## <a name="troubleshooting-tips"></a>Conseils de dépannage

**Problème :** Vous verrez le **message d’erreur**: activité de copie remplies des paramètres non valides : 'UnknownParameterName', message détaillé : Impossible de trouver le .net demandée fournisseur de données .NET Framework. Il ne peut pas être installé ».  

**Causes possibles :**

1. Le fournisseur de données.NET Framework pour Oracle n’a pas été installé.
2. Le fournisseur de données.NET Framework pour Oracle a été installé pour.NET Framework 2.0 et n’est pas trouvé dans les dossiers de.NET Framework 4.0. 

**Résolution/de contournement :**

1. Si vous n’avez pas installé le fournisseur .NET pour Oracle, [l’installer](http://www.oracle.com/technetwork/topics/dotnet/downloads/) et recommencez le scénario. 
2. Si vous obtenez le message d’erreur même après l’installation du fournisseur, procédez comme suit : 
    1. Ouvrez Configuration ordinateur de .NET 2.0 à partir du dossier : <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Recherche pour **Le fournisseur de données Oracle pour .NET**et vous devez être en mesure de trouver une entrée comme indiqué dans l’unwn d’exemple suivantes dans le code suivant exemple des Nations uniesder **system.data** -> **DbProviderFactories**:
            “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.  Copie de cette entrée dans le fichier machine.config dans le dossier v4.0 suivant : <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config et modifier la version de 4.xxx.x.x.
3.  Installez « < chemin d’accès ODP.NET installé > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll » dans le global assembly cache (GAC) en exécutant `gacutil /i [provider path]`.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [copie activité performances & Tuning Guide](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés cet impact sur les performances de transfert de données (copie activité) dans Azure Data Factory et les différentes manières de l’optimiser.
