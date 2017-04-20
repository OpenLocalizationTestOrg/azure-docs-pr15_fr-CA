<properties 
    pageTitle="Déplacer les données à partir de locaux très | Usine de données Azure" 
    description="Découvrez comment déplacer des données à partir de très sur site à l’aide de la fabrique de données Azure." 
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

# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Déplacement des données à partir de très sur site à l’aide de la fabrique de données Azure
Cet article décrit comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données à partir d’une très sur site dans un autre magasin de données. Cet article s’appuie sur l’article [d’activités de déplacement de données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble de transfert de données avec l’activité de copie et de combinaisons de magasin de données pris en charge.

Usine de données prend actuellement en charge un seul transfert de données à partir d’une très sur site à d’autres magasins de données, mais ne pas pour le déplacement des données issues d’autres magasins de données à un très sur site.


## <a name="enabling-connectivity"></a>Activation de la connectivité
Service de fabrique de données prend en charge la connexion à très sur site à l’aide de la passerelle de gestion des données. Consultez l’article de [transfert de données entre les emplacements locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et des instructions détaillées sur la configuration de la passerelle. La passerelle permet de se connecter à très même s’il est hébergé dans un ordinateur virtuel IaaS de Azure. 

Alors que vous pouvez installer la passerelle sur le même ordinateur local ou l’Azure VM comme le très, nous vous conseillons d’installer la passerelle sur un machine distinct/Azure IaaS VM. Passerelle sur un ordinateur distinct réduit les conflits de ressources et améliore les performances. Lorsque vous installez la passerelle sur un ordinateur distinct, l’ordinateur doit être en mesure d’accéder à l’ordinateur avec le très. 


## <a name="copy-data-wizard"></a>Assistant de duplication des données
Le moyen le plus simple pour créer un pipeline qui copie les données sur site très est d’utiliser l’Assistant de copie de données. Consultez [didacticiel : créer un tuyau à l’aide d’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapides sur la création d’un tuyau à l’aide de l’Assistant de copie de données. 

Les exemples suivants fournissent des exemples de définitions de JSON qui vous permet de créer un tuyau à l’aide de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [PowerShell d’Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils illustrent le copier des données à partir d’une très local à un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Exemple : Copier des données de locaux très vers Azure Blob

Cet exemple montre comment copier des données à partir d’une très sur site pour le stockage des objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple possède les entités de fabrique de données suivantes :

1.  Un service lié du type [OnPremisesHdfs](#hdfs-linked-service-properties).
2.  Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [partage de fichiers](#hdfs-dataset-type-properties).
4.  Une sortie [dataset](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [FileSystemSource](#hdfs-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir d’une très sur site pour un blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

Dans un premier temps, configurer la passerelle de gestion des données. Les instructions de l’article de [transfert de données entre les emplacements locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) . 

**Très lié service** Cet exemple utilise l’authentification Windows. Consultez la section [très lié service](#hdfs-linked-service-properties) pour différents types d’authentification que vous pouvez utiliser. 

    {
        "name": "HDFSLinkedService",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
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

**Très d’entrée de groupe de données** Ce groupe de données fait référence au dossier très DataTransfer/UnitTest /. Le pipeline de copie tous les fichiers dans ce dossier vers la destination. 

Définition de « externe » : « true » informe le service Factory de données que le groupe de données est externe à l’usine de données et qu’il n’est pas produit par une activité sur le factory de données.
    
    {
        "name": "InputDataset",
        "properties": {
            "type": "FileShare",
            "linkedServiceName": "HDFSLinkedService",
            "typeProperties": {
                "folderPath": "DataTransfer/UnitTest/"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }




**Dataset de sortie Blob Azure**

Les données sont écrites dans un blob de nouveau toutes les heures (fréquence : heures, l’intervalle : 1). Le chemin d’accès du dossier pour le blob est calculée de manière dynamique en fonction de l’heure de début de la tranche qui est en cours de traitement. Le chemin d’accès du dossier utilise l’année, mois, jour et parties d’heures de l’heure de début.

    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Le pipeline contient une activité de copie qui est configuré pour utiliser ces jeux de données d’entrée et de sortie et est planifié pour s’exécuter toutes les heures. Dans la définition de JSON de pipeline, le type de **source** est défini sur **FileSystemSource** et type de **récepteurs** est définie sur **BlobSink**. La requête SQL spécifiée pour la propriété de **requête** sélectionne les données de la dernière heure à copier.
    
    {
        "name": "pipeline",
        "properties":
        {
            "activities":
            [
                {
                    "name": "HdfsToBlobCopy",
                    "inputs": [ {"name": "InputDataset"} ],
                    "outputs": [ {"name": "OutputDataset"} ],
                    "type": "Copy",
                    "typeProperties":
                    {
                        "source":
                        {
                            "type": "FileSystemSource"
                        },
                        "sink":
                        {
                            "type": "BlobSink"
                        }
                    },
                    "policy":
                    {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "00:05:00"
                    }
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="hdfs-linked-service-properties"></a>Propriétés du Service liés de très

Le tableau suivant fournit la description pour les éléments JSON très spécifiques liées à service.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| type de | La propriété type doit être définie : **très** | Oui | 
| URL | URL pour le très | Oui |
| encryptedCredential | Sortie de la [Nouvelle-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) de l’information d’identification d’accès. | N° |
| nom d’utilisateur | Nom d’utilisateur pour l’authentification Windows. | Oui (pour l’authentification Windows)
| mot de passe | Mot de passe pour l’authentification Windows. | Oui (pour l’authentification Windows)
| authenticationType | Windows, ou anonyme. | Oui |
| nom de la passerelle | Nom de la passerelle que le service Factory de données doit utiliser pour se connecter à la très. | Oui |   

Pour plus d’informations sur la définition des informations d’identification pour les locaux très, reportez-vous à la section [informations d’identification de la configuration et de sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

### <a name="using-anonymous-authentication"></a>À l’aide de l’authentification anonyme

    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "userName": "hadoop",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-windows-authentication"></a>À l’aide de l’authentification Windows
    
    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }
 


## <a name="hdfs-dataset-type-properties"></a>Propriétés du type de groupe de données très

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, consultez l’article de la [Création de groupes de données](data-factory-create-datasets.md) . Sections de structure, de disponibilité et de stratégie d’un groupe de données JSON sont similaires pour tous les types de groupe de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de groupe de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le groupe de données de type **partage de fichiers** (y compris le groupe de données très) possède les propriétés suivantes

Propriété | Description | Obligatoire
-------- | ----------- | --------
folderPath | Chemin d’accès au dossier. Exemple :`myfolder`<br/><br/>Utilisez le caractère d’échappement ' \ ' pour les caractères spéciaux dans la chaîne. Par exemple : pour dossier\sous-dossier, spécifiez le dossier\\\\sous-dossier et de d:\samplefolder, spécifiez d:\\\\samplefolder.<br/><br/>Vous pouvez combiner cette propriété avec **partitionBy** pour que les chemins d’accès de dossier en fonction de la tranche de dates-heures de début et de fin. | Oui
nom de fichier | Spécifiez le nom du fichier dans le **folderPath** si vous souhaitez que la table pour faire référence à un fichier spécifique dans le dossier. Si vous ne spécifiez pas la valeur de cette propriété, le tableau pointe vers tous les fichiers dans le dossier.<br/><br/>Lorsque le nom de fichier n’est pas spécifié pour la sortie d’un groupe de données, le nom du fichier généré serait le suivant ce format : <br/><br/>Données. <Guid>.txt (par exemple : : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | N°
partitionedBy | partitionedBy peut servir à spécifier une dynamique folderPath, nom de fichier de données de série de temps. Exemple : folderPath de paramétrées pour chaque heure de données. | N°
fileFilter | Spécifier un filtre à utiliser pour sélectionner un sous-ensemble de fichiers dans le folderPath plutôt que tous les fichiers. <br/><br/>Valeurs autorisées sont : `*` (plusieurs caractères) et `?` (un seul caractère).<br/><br/>Exemple 1 :`"fileFilter": "*.log"`<br/>Exemple 2 :`"fileFilter": 2014-1-?.txt"`<br/><br/>**Remarque**: fileFilter s’applique à un groupe de données de partage de fichiers d’entrée | N°
| format | Les types de format suivants sont pris en charge : **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**et **ParquetFormat**. Définissez **la propriété sous un format** à l’une de ces valeurs. Consultez les sections [Spécifiant TextFormat](#specifying-textformat), [Spécifiant les AvroFormat](#specifying-avroformat), [Spécifiant les JsonFormat](#specifying-jsonformat), [Spécifiant la OrcFormat](#specifying-orcformat)et [ParquetFormat de spécifier](#specifying-parquetformat) pour plus de détails. Si vous souhaitez copier les fichiers en tant que-est entre bases orientées fichier (copie binaire), vous pouvez ignorer la section de format dans les deux définitions de jeu de données d’entrée et de sortie. | N° 
| compression | Permet de spécifier le type et le niveau de compression pour les données. Types pris en charge sont : **GZip**et **Deflate**et **BZip2** niveaux pris en charge sont : **optimale** et la **plus rapide**. Actuellement, les paramètres de compression ne sont pas pris en charge pour les données de **AvroFormat** ou de **OrcFormat**. Pour plus d’informations, reportez-vous à la section [prise en charge de la Compression](#compression-support) .  | N° |



> [AZURE.NOTE] nom de fichier et fileFilter ne peuvent pas être utilisés simultanément.


### <a name="using-partionedby-property"></a>À l’aide de la propriété de partionedBy

Comme mentionné dans la section précédente, vous pouvez spécifier une dynamique folderPath, nom de fichier de données de la série temps avec partitionedBy. Vous pouvez le faire avec les macros de données usine et la variable système SliceStart, SliceEnd d’indiquer la période logique pour une tranche de données donné. 

Pour en savoir plus sur la série de temps des groupes de données, la planification et tranches, consultez les articles de [Création des groupes de données](data-factory-create-datasets.md), [de planification et l’exécution](data-factory-scheduling-and-execution.md)et [Créer des Pipelines](data-factory-create-pipelines.md) . 

#### <a name="sample-1"></a>Exemple 1 :

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Dans cet exemple {section} est remplacée par la valeur de la variable de système SliceStart Factory de données dans le format (YYYYMMDDHH) spécifiée. La SliceStart fait référence pour l’heure de début de la tranche. Le folderPath est différent pour chaque section. Par exemple : wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemple 2 :

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

Dans cet exemple, l’année, mois, jour et heure de SliceStart sont extraits dans des variables distinctes qui sont utilisées par les propriétés CheminDossier et nom de fichier.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>Propriétés de type d’activité de copie très

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Créer des Pipelines](data-factory-create-pipelines.md) . Les propriétés nom, description, d’entrée et de sortie des tables, des stratégies sont disponibles pour tous les types d’activités. 

D’autre part, les propriétés disponibles dans la section typeProperties de l’activité varient avec chaque type d’activité. Pour les activités de copie, qui varient selon les types de sources et les destinations.

Pour les activités de copie, lorsque la source est de type **FileSystemSource** les propriétés suivantes sont disponibles dans la section de typeProperties :

**FileSystemSource** prend en charge les propriétés suivantes :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| récursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. | Valeur True, False (par défaut)| N° |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [copie activité performances & Tuning Guide](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés cet impact sur les performances de transfert de données (copie activité) dans Azure Data Factory et les différentes manières de l’optimiser.

