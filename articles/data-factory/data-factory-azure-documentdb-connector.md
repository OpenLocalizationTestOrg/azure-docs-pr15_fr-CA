<properties 
    pageTitle="Déplacer des données vers/à partir de DocumentDB | Microsoft Azure" 
    description="Découvrez comment déplacer des données vers/à partir de la collection d’Azure DocumentDB à l’aide de la fabrique de données Azure" 
    services="data-factory, documentdb" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="multiple" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-documentdb-using-azure-data-factory"></a>Déplacement des données vers et à partir de DocumentDB à l’aide de la fabrique de données Azure

Cet article décrit comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données vers Azure DocumentDB à partir des données d’un autre stockent et déplacement les données vers une autre banque de données à partir de DocumentDB. Cet article s’appuie sur l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de transfert de données avec l’activité de copie et de combinaisons de magasin de données pris en charge.

Les exemples suivants indiquent comment copier des données vers et à partir de DocumentDB d’Azure et de stockage des objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à partir des sources des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  

> [AZURE.NOTE] Copie de données à partir des données de IaaS Azure/le locaux stocke pour Azure DocumentDB et inversement est pris en charge avec la passerelle de gestion des données de la version 2.1 et supérieures.

## <a name="sample-copy-data-from-documentdb-to-azure-blob"></a>Exemple : Copier des données à partir de DocumentDB vers Azure Blob

L’exemple ci-dessous s’affiche :

1. Un service lié du type [DocumentDb](#azure-documentdb-linked-service-properties).
2. Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Une sortie [dataset](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple de copie des données dans Azure DocumentDB pour les Blob Azure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples.

**Service de DocumentDB lié Azure :**

    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Service de stockage lié Blob Azure :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure dataset d’entrée de base de données de Document :**

L’exemple suppose que vous avez une collection nommée de **personne** dans une base de données Azure DocumentDB.
 
Définition de « externe » : « true » et en spécifiant les informations de stratégie externalData le service Factory de données Azure que la table est externe à l’usine de données et ne produit pas par une activité sur le factory de données.

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }


**Dataset de sortie Blob Azure :**

Données sont copiées dans un blob de nouveau toutes les heures avec le chemin d’accès de l’objet blob reflétant le datetime spécifique avec la granularité de l’heure.

    {
      "name": "PersonBlobTableOut",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

Exemple de document JSON dans la collection de la personne dans une base de données DocumentDB : 

    {
      "PersonId": 2,
      "Name": {
        "First": "Jane",
        "Middle": "",
        "Last": "Doe"
      }
    }

DocumentDB prend en charge l’interrogation de documents via un SQL comme syntaxe hiérarchiques documents JSON. 

Exemple : Sélectionnez Person.Name.Middle de Person.PersonId, Person.Name.First AS FirstName, MiddleName, Person.Name.Last comme nom de personne

Le pipeline suivant copie les données à partir de la collection de la personne dans la base de données DocumentDB à un objet blob Azure. Dans le cadre de l’activité de copier l’entrée et de sortie, jeux de données ont été spécifiés.  
    
    {
      "name": "DocDbToBlobPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                "nestingSeparator": "."
              },
              "sink": {
                "type": "BlobSink",
                "blobWriterAddHeader": true,
                "writeBatchSize": 1000,
                "writeBatchTimeout": "00:00:59"
              }
            },
            "inputs": [
              {
                "name": "PersonDocumentDbTable"
              }
            ],
            "outputs": [
              {
                "name": "PersonBlobTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromDocDbToBlob"
          }
        ],
        "start": "2015-04-01T00:00:00Z",
        "end": "2015-04-02T00:00:00Z"
      }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-documentdb"></a>Exemple : Copier des données depuis les Blob Azure dans Azure DocumentDB

L’exemple ci-dessous s’affiche :

1. Un service lié du type [DocumentDb](#azure-documentdb-linked-service-properties).
2. Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Une sortie [dataset](data-factory-create-datasets.md) de type [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) et [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).


L’exemple de copie des données à partir de Azure blob dans Azure DocumentDB. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples.

**Service de stockage lié Blob Azure :**
    
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Service de DocumentDB lié Azure :**
    
    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Dataset d’entrée des Blob Azure :**

    {
      "name": "PersonBlobTableIn",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "MiddleName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "fileName": "input.csv",
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Dataset de sortie DocumentDB Azure :**

L’exemple de copie des données dans une collection nommée « Personne ».

    {
      "name": "PersonDocumentDbTableOut",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "Name.First",
            "type": "String"
          },
          {
            "name": "Name.Middle",
            "type": "String"
          },
          {
            "name": "Name.Last",
            "type": "String"
          }
        ],
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

Le pipeline suivant copie des données d’Azure Blob à la collection de personne dans le DocumentDB. Dans le cadre de l’activité de copier l’entrée et de sortie, jeux de données ont été spécifiés. 
    
    {
      "name": "BlobToDocDbPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "DocumentDbCollectionSink",
                "nestingSeparator": ".",
                "writeBatchSize": 2,
                "writeBatchTimeout": "00:00:00"
              }
              "translator": {
                  "type": "TabularTranslator",
                  "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
              }
            },
            "inputs": [
              {
                "name": "PersonBlobTableIn"
              }
            ],
            "outputs": [
              {
                "name": "PersonDocumentDbTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromBlobToDocDb"
          }
        ],
        "start": "2015-04-14T00:00:00Z",
        "end": "2015-04-15T00:00:00Z"
      }
    }
 
Si l’exemple d’entrée de blob est en tant que 

    1,John,,Doe

Puis le résultat JSON dans DocumentDB sera en tant que :

    {
      "Id": 1,
      "Name": {
        "First": "John",
        "Middle": null,
        "Last": "Doe"
      },
      "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
    }
    
DocumentDB est un magasin de NoSQL pour les documents JSON, où des structures imbriquées sont autorisées. Usine de données Azure permet à l’utilisateur indiquer la hiérarchie via **nestingSeparator**, qui est «. » Dans cet exemple. Avec le séparateur, l’activité de copie générera l’objet « Nom » avec des éléments de trois enfants tout d’abord, deuxième prénom et, en fonction de « Name.First », « Name.Middle » et « Name.Last » dans la définition de table.

## <a name="azure-documentdb-linked-service-properties"></a>Propriétés de DocumentDB des services liés Azure

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service de DocumentDB d’Azure lié. 

| **Propriété** | **Description** | **Obligatoire** |
| -------- | ----------- | --------- |
| type de | La propriété type doit être définie : **DocumentDb** | Oui |
| connectionString | Indiquez les informations nécessaires pour vous connecter à la base de données de DocumentDB d’Azure. | Oui |

## <a name="azure-documentdb-dataset-type-properties"></a>Propriétés de type DocumentDB Dataset Azure

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, veuillez consulter l’article de la [Création de groupes de données](data-factory-create-datasets.md) . Les sections comme la structure, la disponibilité et la stratégie d’un groupe de données JSON sont similaires pour tous les types de groupe de données (SQL Azure, blob Azure, table Azure, etc..).
 
La section typeProperties est différente pour chaque type de groupe de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties du groupe de données de type **DocumentDbCollection** possède les propriétés suivantes.

| **Propriété** | **Description** | **Obligatoire** |
| -------- | ----------- | -------- |
| collectionName | Nom de la collection de documents DocumentDB. | Oui |


Exemple :

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

### <a name="schema-by-data-factory"></a>Schéma en usine de données
Pour les banques de données sans schéma comme DocumentDB, le service Data Factory déduit le schéma dans une des manières suivantes :  

1.  Si vous spécifiez la structure des données à l’aide de la propriété **structure** dans la définition de groupe de données, le service Data Factory respecte cette structure comme le schéma. Dans ce cas, si une ligne ne contient pas une valeur pour une colonne, une valeur null sera fournie pour celui-ci.
2.  Si vous ne spécifiez pas la structure des données à l’aide de la propriété **structure** dans la définition de groupe de données, le service Data Factory déduit le schéma à l’aide de la première ligne dans les données. Dans ce cas, si la première ligne ne contient-elle pas le schéma complet, certaines colonnes seront manquantes dans le résultat de l’opération de copie.

Par conséquent, pour les sources de données sans schéma, la meilleure solution consiste à spécifier la structure de données à l’aide de la propriété de la **structure** .

## <a name="azure-documentdb-copy-activity-type-properties"></a>Propriétés de type activité de copie DocumentDB Azure

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, veuillez consulter l’article [Création de tuyaux](data-factory-create-pipelines.md) . Les propriétés telles que nom, description, d’entrée et de sortie des tables et la stratégie sont disponibles pour tous les types d’activités.
 
**Remarque :** L’activité de copie accepte une seule entrée et génère une seule sortie.

Propriétés disponibles dans la section typeProperties de l’activité en revanche varient avec chaque type d’activité, et en cas d’activité de copie qu’ils varient selon les types de sources et les destinations.

En cas d’activité de copie lorsque la source est de type **DocumentDbCollectionSource** les propriétés suivantes sont disponibles dans la section de **typeProperties** :

| **Propriété** | **Description** | **Valeurs autorisées** | **Obligatoire** |
| ------------ | --------------- | ------------------ | ------------ |
| requête | Spécifiez la requête pour lire des données. | Prise en charge par DocumentDB de chaîne de requête. <br/><br/>Exemple :`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` | N° <br/><br/>Si non spécifié, l’instruction SQL exécutée :`select <columns defined in structure> from mycollection` 
| nestingSeparator | Caractère spécial pour indiquer que le document est imbriqué. | N’importe quel caractère. <br/><br/>DocumentDB est un magasin de NoSQL pour les documents JSON, où des structures imbriquées sont autorisées. Usine de données Azure permet à l’utilisateur indiquer la hiérarchie via nestingSeparator, qui est «. » dans les exemples ci-dessus. Avec le séparateur, l’activité de copie générera l’objet « Nom » avec des éléments de trois enfants tout d’abord, deuxième prénom et, en fonction de « Name.First », « Name.Middle » et « Name.Last » dans la définition de table. | N°

**DocumentDbCollectionSink** prend en charge les propriétés suivantes :

| **Propriété** | **Description** | **Valeurs autorisées** | **Obligatoire** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | Un caractère spécial dans le nom de colonne source pour indiquer que ce document imbriqué est nécessaire. <br/><br/>Par exemple, ci-dessus : `Name.First` dans la sortie de table génère la structure JSON suivante dans le document DocumentDB :<br/><br/>« Nom » : {<br/>  « Première » : « Jean »<br/>}, | Caractère utilisé pour séparer les niveaux d’imbrication.<br/><br/>La valeur par défaut est `.` (point). | Caractère utilisé pour séparer les niveaux d’imbrication. <br/><br/>La valeur par défaut est `.` (point). | N° | 
| writeBatchSize | Nombre de requêtes parallèles au service de DocumentDB pour créer des documents.<br/><br/>Vous pouvez affiner les performances lors de la copie des données vers/à partir de DocumentDB à l’aide de cette propriété. Vous pouvez vous attendre à de meilleures performances lorsque vous augmentez writeBatchSize, car plusieurs demandes parallèles à DocumentDB sont envoyés. Toutefois, vous devez éviter les limitations qui peut lever le message d’erreur : « Demande de taux est grand ».<br/><br/>La limitation est décidée par un certain nombre de facteurs, y compris la taille des documents, nombre de termes dans les documents, l’indexation de la stratégie de collection de la cible, etc.. Pour les opérations de copie, vous pouvez utiliser une collection plue (par exemple, S3) pour que le débit le plus disponible (demande de 2 500 unités/seconde). | Nombre entier | Non (par défaut : 10000) |
| writeBatchTimeout | Temps d’attente pour l’opération se termine avant l’expiration du délai. | TimeSpan<br/><br/> Exemple : « 00 : 30:00 » (30 minutes). | N° |
 
## <a name="appendix"></a>Appendice
1. **Question :**  
    est la mise à jour de la prise en charge des activités de copie des enregistrements existants ?

    **Réponse :**  
    n°

2. **Question :**  
    comment fait une nouvelle tentative de copie à la demande DocumentDB avec déjà copié les enregistrements ?

    **Réponse :**  
    si des enregistrements ont un champ « ID » et l’opération de copie a tente d’insérer un enregistrement avec le même ID, l’opération de copie lève une erreur.  
 
3. **Question :** 
    Data Factory prend-il en charge [plage ou la segmentation des données basé sur hachage](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)? 

    **Réponse :** 
    n° 
4. **Question :** 
    puis-je spécifier plus d’une collection de DocumentDB pour une table ?
    
    **Réponse :** 
    n° Qu’une seule collection peut être spécifiée pour l’instant.
     
## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [copie activité performances & Tuning Guide](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés cet impact sur les performances de transfert de données (copie activité) dans Azure Data Factory et les différentes manières de l’optimiser.
