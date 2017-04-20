<properties 
    pageTitle="Déplacer les données de Cassandra à l’aide de Data Factory | Microsoft Azure" 
    description="Découvrez comment déplacer des données d’une base de données de Cassandra sur site à l’aide de la fabrique de données Azure." 
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
    ms.date="09/07/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Déplacer les données d’une base de données de Cassandra sur site à l’aide de la fabrique de données Azure
Cet article décrit comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour copier des données à partir d’une base de données de Cassandra sur site à n’importe quel magasin de données répertorié sous la colonne de récepteur dans la section [prise en charge de Sources et les destinations](data-factory-data-movement-activities.md#supported-data-stores) . Cet article s’appuie sur l’article [d’activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de transfert de données avec l’activité de copie et de combinaisons de magasin de données pris en charge.

Usine de données prend actuellement en charge uniquement des transfert de données à partir d’une base de données Cassandra pris en charge du [récepteur des magasins de données](data-factory-data-movement-activities.md#supported-data-stores), mais pas de déplacement des données issues d’autres magasins de données à une base de données Cassandra.

## <a name="prerequisites"></a>Conditions préalables
Pour le service Factory de données Azure être en mesure de se connecter à votre base de données de Cassandra sur site, vous devez installer les éléments suivants : 

- Passerelle de gestion des données 2.0 ou une version ultérieure sur le même ordinateur qui héberge la base de données ou sur un ordinateur distinct afin d’éviter la concurrence pour les ressources de la base de données. Passerelle de gestion des données est un logiciel qui connecte les sources de données sur site pour les services en nuage de façon sécurisée et gérée. Article de [déplacer des données entre des locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) voir pour plus d’informations sur la passerelle de gestion des données.
  
    Lorsque vous installez la passerelle, il installe automatiquement un pilote ODBC Cassandra de Microsoft utilisé pour se connecter à la base de données de Cassandra. 

> [AZURE.NOTE] Voir [passerelle de résoudre les problèmes](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle des problèmes. 

## <a name="copy-data-wizard"></a>Assistant de duplication des données
Pour créer un pipeline qui copie les données à partir d’une base de données Cassandra à toute les prise en charge de récepteurs banques de données le plus simple est d’utiliser l’Assistant de copie de données. Consultez [didacticiel : créer un tuyau à l’aide d’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapides sur la création d’un tuyau à l’aide de l’Assistant de copie de données. 

L’exemple suivant fournit des exemples de définitions de JSON qui vous permet de créer un tuyau à l’aide de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [PowerShell d’Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils illustrent le copier des données à partir de la base de données Cassandra dans le stockage Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.   


## <a name="sample-copy-data-from-cassandra-to-blob"></a>Exemple : Copier des données de Cassandra de Blob
L’exemple copie les données à partir d’une base de données Cassandra à un blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. Données peuvent être copiées directement à un des récepteurs indiqués dans l’article [d’Activités de déplacement des données](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory. 

- Un service lié du type [OnPremisesCassandra](#onpremisescassandra-linked-service-properties).
- Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [groupe de données](data-factory-create-datasets.md) d’entrée de type [CassandraTable](#cassandratable-properties).
- Une sortie [dataset](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [CassandraSource](#cassandrasource-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Service de Cassandra lié**

Cet exemple utilise le service **Cassandra** lié. Consultez la section [Cassandra lié de service](#onpremisescassandra-linked-service-properties) pour les propriétés prises en charge par ce service lié.  

    {
        "name": "CassandraLinkedService",
        "properties":
        {
            "type": "OnPremisesCassandra",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "host": "mycassandraserver",
                "port": 9042,
                "username": "user",
                "password": "password",
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

**Jeu de données d’entrée Cassandra**

    {
        "name": "CassandraInput",
        "properties": {
            "linkedServiceName": "CassandraLinkedService",
            "type": "CassandraTable",
            "typeProperties": {
                "tableName": "mytable",
                "keySpace": "mykeyspace" 
            },
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

Le fait de définir **externe** **true** indique le service Factory de données que le dataset est externe à l’usine de données et qu’il n’est pas produit par une activité sur le factory de données.

**Dataset de sortie Blob Azure**

Les données sont écrites dans un blob de nouveau toutes les heures (fréquence : heures, l’intervalle : 1). 

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/fromcassandra"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Activité de copie de pipeline**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifié pour s’exécuter toutes les heures. Dans la définition de JSON de pipeline, le type de **source** est défini sur **CassandraSource** et type de **récepteurs** est définie sur **BlobSink**. 

Consultez [les propriétés de type RelationalSource](#cassandrasource-type-properties) pour la liste des propriétés prises en charge par le RelationalSource. 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2016-06-01T18:00:00",
            "end":"2016-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":[  
            {
                "name": "CassandraToAzureBlob",
                "description": "Copy from Cassandra to an Azure blob",
                "type": "Copy",
                "inputs": [
                {
                    "name": "CassandraInput"
                }
                ],
                "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
                ],
                "typeProperties": {
                    "source": {
                        "type": "CassandraSource",
                        "query": "select id, firstname, lastname from mykeyspace.mytable"
        
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
## <a name="onpremisescassandra-linked-service-properties"></a>Propriétés du service OnPremisesCassandra lié

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service de Cassandra lié.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| type de | La propriété type doit être définie : **OnPremisesCassandra** | Oui |
| hôte | Une ou plusieurs adresses IP ou les noms d’hôte des serveurs de Cassandra.<br/><br/>Spécifier une liste séparée par des virgules d’adresses IP ou des noms d’hôte à se connecter simultanément à tous les serveurs. | Oui | 
| port | Le port TCP utilisé par le serveur Cassandra pour écouter les connexions clientes. | Non, de valeur par défaut : 9042 |
| authenticationType | Base ou anonyme | Oui |
| nom d’utilisateur |Spécifiez le nom d’utilisateur pour le compte d’utilisateur. | Oui, si authenticationType est définie sur Basic. |
| mot de passe | Spécifiez le mot de passe pour le compte d’utilisateur.  | Oui, si authenticationType est définie sur Basic. |
| nom de la passerelle | Le nom de la passerelle qui sert à connecter à la base de données de Cassandra sur site. | Oui |
| encryptedCredential | Informations d’identification chiffrées par la passerelle. | N° | 

## <a name="cassandratable-properties"></a>Propriétés de CassandraTable

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, consultez l’article de la [Création de groupes de données](data-factory-create-datasets.md) . Sections de structure, de disponibilité et de stratégie d’un groupe de données JSON sont similaires pour tous les types de groupe de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de groupe de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le groupe de données de type **CassandraTable** possède les propriétés suivantes

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| keyspace | Nom du keyspace ou du schéma de base de données de Cassandra. | Oui (si la **requête** pour **CassandraSource** n’est pas définie). |
| tableName | Nom de la table de base de données de Cassandra. | Oui (si la **requête** pour **CassandraSource** n’est pas définie). |


## <a name="cassandrasource-type-properties"></a>Propriétés de type CassandraSource
Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Créer des Pipelines](data-factory-create-pipelines.md) . Les propriétés telles que nom, description, d’entrée et de sortie des tables et la stratégie sont disponibles pour tous les types d’activités. 

D’autre part, les propriétés disponibles dans la section typeProperties de l’activité varient avec chaque type d’activité. Pour les activités de copie, qui varient selon les types de sources et les destinations.

Lorsque la source est de type **CassandraSource**, les propriétés suivantes sont disponibles dans la section de typeProperties :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| requête | La requête personnalisée permet de lire les données. | SQL-92 ou une requête CQL. Reportez-vous à [CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Lors de l’utilisation de requêtes SQL, spécifiez le **nom de name.table keyspace** pour représenter la table que vous souhaitez interroger. | Non (si tableName et keyspace sur le groupe de données sont définis).  |
| consistencyLevel | Le niveau de cohérence Spécifie le nombre de réplicas doivent répondre à une demande de lecture avant de renvoyer des données à l’application cliente. Cassandra vérifie le nombre spécifié de réplicas des données répondre à la demande de lecture. | UN, DEUX, TROIS, QUORUM, TOUS, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Pour plus d’informations, reportez-vous à la section [configuration la cohérence des données](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) . | N° Valeur par défaut est un. |  


### <a name="type-mapping-for-cassandra"></a>Mappage du type pour Cassandra
Type de Cassandra | Type basé sur le .net
--------------- | ---------------
ASCII | Chaîne 
BIGINT | Int64
OBJET BLOB | Byte]
VALEUR BOOLÉENNE | Valeur booléenne
DÉCIMAL | Décimal
DOUBLE | Double
FLOAT | Unique
INET | Chaîne
INT | Int32
TEXTE | Chaîne
HORODATAGE | DateTime
TIMEUUID | GUID
UUID | GUID
VARCHAR | Chaîne
VARINT | Décimal

> [AZURE.NOTE]  
> Pour la collection types (mappage, ensemble, liste, etc.), consultez section de [travailler avec les types de collection Cassandra à l’aide de la table virtuelle](#work-with-collections-using-virtual-table) . 
> 
> Types définis par l’utilisateur ne sont pas pris en charge.
> 
> La longueur des longueurs des colonnes binaires et chaîne ne peut pas être supérieure à 4000. 

## <a name="work-with-collections-using-virtual-table"></a>Travailler avec des collections à l’aide de la table virtuelle
Azure Data Factory utilise un pilote ODBC intégré pour vous connecter à et de copier des données à partir de votre base de données Cassandra. Pour les types de collection, y compris la carte, de jeu et de liste, le pilote normalise à nouveau les données dans des tables virtuelles correspondants. En particulier, si une table contient des colonnes de la collection, le pilote génère les tables virtuelles suivantes :

-   Une **table de base**, qui contient les mêmes données que la table réelle à l’exception des colonnes de la collection. La table de base utilise le même nom que la table réelle qu’il représente.
-   Une **table virtuelle** pour chaque colonne de collection, qui développe les données imbriquées. Les tables virtuelles qui représentent les collections sont nommés en utilisant le nom de la table réelle, un séparateur de «_vt_» et le nom de la colonne.

Tables virtuelles font référence aux données de la table réelle, l’activation du pilote accéder aux données dénormalisées. Pour plus d’informations, reportez-vous à la section exemple. Vous pouvez accéder au contenu des collections de Cassandra en interrogeant et en joignant les tables virtuelles.

Vous pouvez tirer parti de l' [Assistant copie](data-factory-data-movement-activities.md#data-factory-copy-wizard) pour intuitivement permet d’afficher la liste des tables dans la base de données de Cassandra, y compris les tables virtuelles et afficher des données à l’intérieur. Vous pouvez également créer une requête dans l’Assistant de copie et valider pour voir le résultat.

### <a name="example"></a>Exemple
Par exemple, le suivant « ExampleTable » est une table de base de données Cassandra qui contient une colonne de clé primaire entier est nommé « pk_int », une colonne de texte nommé value, une colonne de liste, une colonne de table et une colonne de jeu (nommé « StringSet »).

pk_int | Valeur | Liste | Carte |   StringSet
------ | ----- | ---- | --- | --------
1 | « valeur exemple 1 » | ["1", "2", "3"]  | {« S1 » : « A », « S2 » : « b »} | {"A", "B", "C"}
3 | « exemple de valeur 3 » | [« 100 », « 101 », « 102 », « 105 »] | {« S1 » : « t »} | {"A", "E"}

Le pilote génère plusieurs tables virtuelles pour représenter cette table unique. Les colonnes de clé étrangère dans les tables virtuelles font référence les colonnes de clé primaire dans la table réelle et d’indiquer les lignes de la table réelle la ligne de la table virtuelle correspond à. 

La première table virtuelle est nommée « ExampleTable » de la table de base est illustrée dans le tableau suivant. La table de base contient les mêmes données que la table de base de données d’origine, sauf pour les collections, omises de ce tableau et développée dans d’autres tables virtuelles.

pk_int | Valeur
------ | -----
1 | « valeur exemple 1 »
3 | « exemple de valeur 3 »

Les tableaux suivants présentent les tables virtuelles qui renormalize les données des colonnes de la liste, carte et StringSet. Les colonnes dont les noms se terminent par « _index » ou « _key » indiquent la position des données dans la liste ou la table d’origine. Les colonnes dont les noms se terminent par « _value » contiennent les données de développé à partir de la collection.

#### <a name="table-exampletablevtlist"></a>Table « ExampleTable_vt_List » :
pk_int | List_index | List_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### <a name="table-exampletablevtmap"></a>Table « ExampleTable_vt_Map » :
pk_int | Map_key | Map_value
------ | ------- | ---------
1 | S1 | A
1 | S2 | b
3 | S1 | t

#### <a name="table-exampletablevtstringset"></a>Table « ExampleTable_vt_StringSet » :
pk_int | StringSet_value
------ | ---------------
1 | A
1 | B
1 | C
3 | A
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [copie activité performances & Tuning Guide](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés cet impact sur les performances de transfert de données (copie activité) dans Azure Data Factory et les différentes manières de l’optimiser.
