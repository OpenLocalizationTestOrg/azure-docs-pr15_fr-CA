<properties 
    pageTitle="Déplacer les données d’un serveur FTP | Microsoft Azure" 
    description="Découvrez comment déplacer des données à partir d’un serveur FTP à l’aide de la fabrique de données Azure." 
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
    ms.date="10/20/2016" 
    ms.author="spelluru"/>

# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Déplacement des données à partir d’un serveur FTP à l’aide de la fabrique de données Azure
Cet article décrit l’utilisation de l’activité de copie dans Azure Data Factory pour déplacer des données à partir d’un serveur FTP vers un magasin de données du récepteur pris en charge. Cet article s’appuie sur l’article [d’activités de déplacement de données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble de transfert de données avec l’activité de copie et de la liste des magasins de données pris en charge en tant que sources/récepteurs. 

Usine de données prend actuellement en charge un seul transfert de données à partir d’un serveur FTP pour d’autres magasins de données, mais ne pas pour le déplacement des données provenant d’autres banques de données sur un serveur FTP. Il prend en charge à la fois sur site et de nuage de serveurs FTP. 

Si vous déplacez des données d’un serveur **sur site** FTP dans un magasin de données de nuage (exemple : stockage des objets Blob Azure), installer et utiliser la passerelle de gestion des données. La passerelle de gestion des données est un agent de client qui est installé sur votre ordinateur local, qui permet aux services de cloud pour se connecter à la ressource de locaux. Pour plus d’informations sur la passerelle, reportez-vous à la section [Passerelle de gestion des données](data-factory-data-management-gateway.md) . Consultez l’article de [transfert de données entre les emplacements locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) pour obtenir des instructions détaillées sur la configuration de la passerelle et de l’utiliser. La passerelle vous permet de vous connecter à un serveur FTP, même si le serveur est sur une machine virtuelle (VM) à Azure IaaS. 

Vous pouvez installer la passerelle sur le même ordinateur local ou l’Azure IaaS VM en tant que serveur FTP. Toutefois, nous vous recommandons d’installer la passerelle sur un ordinateur distinct ou une VM de IaaS Azure distinct afin d’éviter les conflits de ressources et pour améliorer les performances. Lorsque vous installez la passerelle sur un ordinateur distinct, l’ordinateur doit être en mesure d’accéder au serveur FTP. 

## <a name="copy-data-wizard"></a>Assistant de duplication des données
La façon la plus simple pour créer un pipeline qui copie les données à partir d’un serveur FTP est d’utiliser l’Assistant de copie de données. Consultez [didacticiel : créer un tuyau à l’aide d’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapides sur la création d’un tuyau à l’aide de l’Assistant de copie de données. 

Les exemples suivants fournissent des exemples de définitions de JSON qui vous permet de créer un tuyau à l’aide de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [PowerShell d’Azure](data-factory-copy-activity-tutorial-using-powershell.md). 

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Exemple : Copier des données à partir du serveur FTP dans blob Azure

Cet exemple montre comment copier des données à partir d’un serveur FTP pour un stockage de Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple possède les entités de fabrique de données suivantes :

- Service de type [Serveur_ftp](#ftp-linked-service-properties)lié.
- Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [partage de fichiers](#fileshare-dataset-type-properties).
- Une sortie [dataset](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [FileSystemSource](#ftp-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir d’un serveur FTP pour un blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

**FTP lié service** Cet exemple utilise l’authentification de base avec le nom d’utilisateur et le mot de passe en texte brut. Vous pouvez également utiliser une des manières suivantes : 

- Authentification anonyme 
- Authentification de base avec les informations d’identification cryptées
- FTP sur SSL/TLS (FTP)

Consultez la section [FTP lié service](#ftp-linked-service-properties) pour différents types d’authentification que vous pouvez utiliser. 

    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",          
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
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

**Jeu de données d’entrée de FTP** Ce groupe de données fait référence au dossier FTP `mysharedfolder` et `test.csv`. Le pipeline de copie du fichier vers la destination. 

Définition de « externe » : « true » informe le service Factory de données que le groupe de données est externe à l’usine de données et qu’il n’est pas produit par une activité sur le factory de données.
    
    {
      "name": "FTPFileInput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
          "folderPath": "mysharedfolder",
          "fileName": "test.csv",
          "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Dataset de sortie Blob Azure**

Les données sont écrites dans un blob de nouveau toutes les heures (fréquence : heures, l’intervalle : 1). Le chemin d’accès du dossier pour le blob est calculée de manière dynamique en fonction de l’heure de début de la tranche qui est en cours de traitement. Le chemin d’accès du dossier utilise l’année, mois, jour et parties d’heures de l’heure de début.

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
        "name": "pipeline",
        "properties": {
            "activities": [{
                "name": "FTPToBlobCopy",
                "inputs": [{
                    "name": "FtpFileInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource"
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
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }],
            "start": "2016-08-24T18:00:00Z",
            "end": "2016-08-24T19:00:00Z"
        }
    }

## <a name="ftp-linked-service-properties"></a>Propriétés du Service lié FTP

Le tableau suivant fournit la description pour les éléments JSON spécifiques à FTP Services liés.

| Propriété | Description | Obligatoire | Par défaut |
| -------- | ----------- | -------- | ------- | 
| type de | Serveur_ftp doit indiquer la propriété de type | Oui | &nbsp;
| hôte | Nom ou adresse IP du serveur FTP | Oui | &nbsp;
| authenticationType | Spécifiez le type d’authentification | Oui | Base, anonyme |
| nom d’utilisateur | Utilisateur qui a accès au serveur FTP | N° | &nbsp;
| mot de passe | Mot de passe de l’utilisateur (username) | N° | &nbsp;
| encryptedCredential | Informations d’identification chiffrées pour accéder au serveur FTP | N° | &nbsp;
| nom de la passerelle | Nom de la passerelle passerelle de gestion des données pour vous connecter à un serveur FTP de locaux | N°    | &nbsp;
| port | Port sur lequel écoute le serveur FTP | N° | 21 |
| enableSsl | Spécifiez s’il faut utiliser FTP sur un canal SSL/TLS | N° | valeur True | 
| enableServerCertificateValidation | Spécifiez s’il faut activer la validation du certificat serveur SSL lors de l’utilisation de FTP sur un canal SSL/TLS | N° | valeur True | 

### <a name="using-anonymous-authentication"></a>À l’aide de l’authentification anonyme

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {     
                "authenticationType": "Anonymous",
                "host": "myftpserver.com"
            }
        }
    }

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>À l’aide du nom d’utilisateur et le mot de passe en texte brut pour l’authentification de base
    
    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "username": "Admin",
                "password": "123456"
            }
        }
    }


### <a name="using-port-enablessl-enableservercertificatevalidation"></a>À l’aide du port, enableSsl, enableServerCertificateValidation

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",  
                "username": "Admin",
                "password": "123456",
                "port": "21",
                "enableSsl": true,
                "enableServerCertificateValidation": true
            }
        }
    }

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>À l’aide d’encryptedCredential pour l’authentification et de la passerelle

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "encryptedCredential": "xxxxxxxxxxxxxxxxx",
                "gatewayName": "mygateway"
            }
        }
    }

Pour plus d’informations sur la définition des informations d’identification pour une source de données sur les sites FTP, reportez-vous à la section [informations d’identification de la configuration et de sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

[AZURE.INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]   
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Propriétés de type d’activité FTP copie

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Créer des Pipelines](data-factory-create-pipelines.md) . Les propriétés nom, description, d’entrée et de sortie des tables, des stratégies sont disponibles pour tous les types d’activités. 

D’autre part, les propriétés disponibles dans la section typeProperties de l’activité varient avec chaque type d’activité. Pour les activités de copie, les propriétés de type varient selon les types de sources et les destinations.

[AZURE.INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]   

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [copie activité performances & Tuning Guide](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés cet impact sur les performances de transfert de données (copie activité) dans Azure Data Factory et les différentes manières de l’optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 

- [Activité de copie de didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions étape par étape pour la création d’un pipeline avec une activité de copie. 
