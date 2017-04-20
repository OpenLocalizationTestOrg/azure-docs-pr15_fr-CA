<properties 
    pageTitle="Créer des groupes de données dans une usine de données Azure | Microsoft Azure" 
    description="Apprenez à créer des groupes de données dans Azure Data Factory avec des exemples d’utilisation des propriétés de décalage et anchorDateTime."
    keywords="créer le groupe de données, exemple de groupe de données, offset-exemple"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="shlo"/>

# <a name="datasets-in-azure-data-factory"></a>Groupes de données dans une usine de données Azure
Cet article décrit les groupes de données dans Azure Data Factory et inclut des exemples de décalage, anchorDateTime et bases de données de type/offset.

Lorsque vous créez un groupe de données, vous créez un pointeur vers les données que vous souhaitez traiter. Les données sont traitées (entrée/sortie) d’une activité et une activité est contenue dans un pipeline. Un groupe de données d’entrée représente l’entrée d’une activité dans le pipeline et la sortie d’un groupe de données représente la sortie de l’activité.

Groupes de données identifient les données dans les différents magasins de données, tels que des tables, des fichiers, des dossiers et des documents. Après avoir créé un groupe de données, vous pouvez l’utiliser avec les activités dans un pipeline. Par exemple, un groupe de données peut être un groupe de données d’entrée/sortie d’une activité de copie ou d’une activité HDInsightHive. Le portail Azure vous donne une présentation visuelle de tous vos pipelines et les données des entrées et des sorties. En un coup de œil, vous voyez toutes les relations et les dépendances de vos pipelines sur toutes vos sources afin de toujours savoir d'où proviennent les données et où il va.

Dans Azure Data Factory, vous pouvez obtenir des données à partir d’un groupe de données à l’aide d’activité de la copie dans un pipeline.

> [AZURE.NOTE] Si vous êtes nouveau sur le Factory de données Azure, consultez [Introduction à la fabrique de données Azure](data-factory-introduction.md) pour une vue d’ensemble du service de fabrique de données Azure. Reportez-vous à la section [créer votre première fabrique de données](data-factory-build-your-first-pipeline.md) pour obtenir un didacticiel pour créer votre première fabrique de données. Ces deux articles fournissent que vous vous avez besoin pour mieux comprendre cet article des informations générales. 

## <a name="define-datasets"></a>Définir des groupes de données
Un groupe de données dans Azure Data Factory est définie comme suit : 


    {
        "name": "<name of dataset>",
        "properties": {
            "type": "<type of dataset: AzureBlob, AzureSql etc...>",
            "external": <boolean flag to indicate external data. only for input datasets>,
            "linkedServiceName": "<Name of the linked service that refers to a data store.>",
            "structure": [
                {
                    "name": "<Name of the column>",
                    "type": "<Name of the type>"
                }
            ],
            "typeProperties": {
                "<type specific property>": "<value>",
                "<type specific property 2>": "<value 2>",
            },
            "availability": {
                "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
                "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
            },
           "policy": 
            {      
            }
        }
    }

Le tableau suivant décrit les propriétés dans le JSON ci-dessus :   

| Propriété | Description | Obligatoire | Par défaut |
| -------- | ----------- | -------- | ------- |
| nom | Nom du groupe de données. Consultez [Azure Data Factory - règles d’affectation de noms](data-factory-naming-rules.md) pour les règles d’attribution de noms. | Oui | NA |
| type de | Type du groupe de données. Spécifiez un des types pris en charge par l’usine de données Azure (par exemple : AzureBlob, AzureSqlTable). <br/><br/>Pour plus d’informations, reportez-vous à la section [Type de groupe de données](#Type) . | Oui | NA |
| structure de | Schéma du dataset<br/><br/>Pour plus d’informations, consultez [La Structure](#Structure) Dataset section. | N° | NA |
| typeProperties | Propriétés correspondant au type sélectionné. Pour plus d’informations sur les types de prises en charge et leurs propriétés, reportez-vous à la section [Type de groupe de données](#Type) . | Oui | NA |
| externe | Indicateur booléen pour indiquer si un groupe de données est explicitement généré par un pipeline de fabrique des données ou non.  | N° | False | 
| disponibilité | Définit la fenêtre de traitement ou le modèle de découpe pour la production du groupe de données. <br/><br/>Pour plus d’informations, reportez-vous à la section [Disponibilité du Dataset](#Availability) section. <br/><br/>Pour plus d’informations sur le découpage de modèle, consultez l’article de [planification et l’exécution](data-factory-scheduling-and-execution.md) du groupe de données. | Oui | NA
| stratégie de | Définit les critères ou à la condition que les tranches du groupe de données doivent remplir. <br/><br/>Pour plus d’informations, consultez [Stratégie de groupe de données](#Policy) section. | N° | NA |

## <a name="dataset-example"></a>Exemple de DataSet
Dans l’exemple suivant, le groupe de données représente une table nommée **MyTable** dans une **base de données de SQL d’Azure**. 

    {
        "name": "DatasetSample",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": 
            {
                "tableName": "MyTable"
            },
            "availability": 
            {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

Notez les points suivants : 

- type est défini sur AzureSqlTable.
- propriété de type de tableName (spécifique au type de AzureSqlTable) est définie sur MyTable.
- linkedServiceName fait référence à un service lié du type AzureSqlDatabase. Voir la définition du service lié suivant. 
- fréquence de disponibilité est définie à jour et l’intervalle est défini sur 1, ce qui signifie que la section est produite quotidiennement.  

AzureSqlLinkedService est défini comme suit :

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }

Dans le JSON ci-dessus : 

- type est défini sur AzureSqlDatabase
- propriété de type connectionString spécifie des informations pour vous connecter à une base de données SQL d’Azure.  


Comme vous pouvez le voir, le service lié définit comment se connecter à une base de données SQL d’Azure. Le dataset définit quelle table est utilisé comme une entrée/sortie pour l’activité dans un pipeline. La section activités dans votre [pipeline](data-factory-create-pipelines.md) JSON Spécifie si le groupe de données est utilisée comme un groupe de données d’entrée ou de sortie.


> [AZURE.IMPORTANT] À moins qu’un groupe de données est fabriqué par Azure Data Factory, il doit être marqué comme étant **externe**. Ce paramètre s’applique généralement aux entrées de la première activité dans un pipeline.   

## <a name="Type"></a>Type de groupe de données
Les sources de données prises en charge et les types de groupe de données sont alignés. Consultez les rubriques figurant dans l’article [d’Activités de déplacement des données](data-factory-data-movement-activities.md#supported-data-stores) pour plus d’informations sur les types et la configuration des groupes de données. Par exemple, si vous utilisez des données à partir d’une base de données SQL d’Azure, cliquez sur base de données de SQL Azure dans la liste des banques de données pris en charge pour obtenir des informations détaillées.  

## <a name="Structure"></a>Structure de groupe de données
La section **structure** définit le schéma du dataset. Il constituée une collection de noms et de types de données des colonnes.  Dans l’exemple suivant, le groupe de données a slicetimestamp de trois colonnes et NomProjet pageviews et qu’ils sont de type : chaîne, chaîne et décimal, respectivement.

    structure:  
    [ 
        { "name": "slicetimestamp", "type": "String"},
        { "name": "projectname", "type": "String"},
        { "name": "pageviews", "type": "Decimal"}
    ]

## <a name="Availability"></a>Disponibilité du groupe de données
La section **disponibilité** dans un groupe de données définit la fenêtre de traitement (horaire, quotidienne, hebdomadaire, etc.) ou le modèle de découpage pour le groupe de données. Voir [planification et l’exécution de](data-factory-scheduling-and-execution.md) l’article pour plus de détails sur le modèle de découpage et de la dépendance du dataset. 

La section suivante de disponibilité indique que le dataset de sortie est produit toutes les heures (ou) entrée dataset est disponible, toutes les heures :

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 1   
    }

Le tableau suivant décrit les propriétés que vous pouvez utiliser dans la section disponibilité : 

| Propriété | Description | Obligatoire | Par défaut |
| -------- | ----------- | -------- | ------- |
| fréquence | Spécifie l’unité de temps pour la production de tranche de dataset.<br/><br/>**Fréquence des prises en charge**: Minute, heure, jour, semaine, mois | Oui | NA |
| intervalle | Spécifie un multiplicateur de fréquence<br/><br/>« Intervalle de fréquence x » détermine la fréquence à laquelle la tranche est générée.<br/><br/>Si vous avez besoin pour être découpés en tranches sur une base horaire, le groupe de données, vous définissez la **fréquence** **heures**, et l' **intervalle** à **1**.<br/><br/>**Remarque :** Si vous spécifiez la fréquence en minutes, nous vous conseillons de définir l’intervalle de pas moins de 15 | Oui | NA |
| style | Spécifie si la section doit être produite à la début/la fin de l’intervalle.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Si la fréquence est définie sur mois et style est défini sur EndOfInterval, la tranche est produite le dernier jour du mois. Si le style est défini sur StartOfInterval, la tranche est produite sur le premier jour du mois.<br/><br/>Si la fréquence est définie sur le jour et le style est défini sur EndOfInterval, la tranche est produite durant la dernière heure du jour.<br/><br/>Si la fréquence est réglée sur l’heure et le style est défini sur EndOfInterval, la tranche est générée à la fin de l’heure. Par exemple, pour une tranche de période de 13 h 00 à 14 h 00, la tranche est produite à 14 h 00. | N° | EndOfInterval |
| anchorDateTime | Définit la position absolue dans le temps permet de calculer les limites de la section groupe de données par le planificateur. <br/><br/>**Remarque :** Si le AnchorDateTime a des parties de date plus précise que la fréquence les parties plus granulaires sont ignorés. <br/><br/>Par exemple, si l' **intervalle** est **toutes les heures** (fréquence : heure et intervalle : 1) et **AnchorDateTime** contient les **minutes et les secondes**, puis les parties **minutes et les secondes** de la AnchorDateTime sont ignorés. | N° | 01/01/0001 |
| décalage | TimeSpan par lequel le début et la fin de toutes les tranches de dataset sont décalés. <br/><br/>**Remarque :** Si à la fois anchorDateTime et l’offset sont spécifiés, le résultat est l’équipe de travail combinée. | N° | NA |

### <a name="offset-example"></a>exemple de décalage

Tous les jours tranches qui commencent à 6 heures au lieu de minuit de la valeur par défaut. 

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

La **fréquence** est réglée à **jour** et **l’intervalle** est défini sur **1** (une fois par jour) : Si vous souhaitez que la tranche à produire à 6 heures et non à l’heure par défaut : 12 : 00. N’oubliez pas que cette heure est une heure UTC. 

## <a name="anchordatetime-example"></a>exemple d’anchorDateTime

**Exemple :** les tranches de dataset 23 heures début 2007-04-19T08:00:00

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 23, 
        "anchorDateTime":"2007-04-19T08:00:00"  
    }

## <a name="offsetstyle-example"></a>Exemple de type/offset

Si vous avez besoin de dataset sur une base mensuelle sur la date et une heure (supposons 3e de chaque mois à 08:00), utilisez la balise de **décalage** pour définir la date et l’heure qu’il doit exécuter. 

    {
      "name": "MyDataset",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "availability": {
          "frequency": "Month",
          "interval": 1,
          "offset": "3.08:10:00",
          "style": "StartOfInterval"
        }
      }
    }


## <a name="Policy"></a>Stratégie de groupe de données

La section de la **stratégie** dans la définition de groupe de données définit les critères ou à la condition que les tranches du groupe de données doivent remplir.

### <a name="validation-policies"></a>Stratégies de validation

| Nom de la stratégie | Description | Appliqué à | Obligatoire | Par défaut |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Vérifie que les données d’un **blob d’Azure** remplissent les conditions de la taille minimale (en mégaoctets). | Blob Azure | N° | NA |
|minimumRows | Vérifie que les données un dans **base de données d’Azure SQL** ou dans une **table Azure** contient le nombre minimal de lignes. | <ul><li>Base de données SQL Azure</li><li>Table Azure</li></ul> | N° | NA

#### <a name="examples"></a>Exemples

**minimumSizeMB :**

    "policy":
    
    {
        "validation":
        {
            "minimumSizeMB": 10.0
        }
    }

**minimumRows**

    "policy":
    {
        "validation":
        {
            "minimumRows": 100
        }
    }

### <a name="external-datasets"></a>Groupes de données externes

Groupes de données externes sont celles qui ne sont pas fabriqués par un pipeline en cours d’exécution dans l’usine de données. Si le groupe de données est marquée comme étant **externes**, la stratégie de **ExternalData** peut-être être définie pour influencer le comportement de la disponibilité de tranche du groupe de données. 

À moins qu’un groupe de données est fabriqué par Azure Data Factory, il doit être marqué comme étant **externe**. Ce paramètre s’applique généralement aux entrées de la première activité dans un pipeline à moins que l’activité ou le chaînage de pipeline est utilisée. 

| Nom | Description | Obligatoire | Valeur par défaut  |
| ---- | ----------- | -------- | -------------- |
| dataDelay | Durée du délai de la vérification de la disponibilité des données externes pour la tranche donnée. Par exemple, si les données sont supposé pour être disponible pour toutes les heures, vérifie les données externes soient disponibles et la tranche correspondante est prêt peut être retardée à l’aide de dataDelay.<br/><br/>S’applique uniquement à l’heure actuelle.  Par exemple, si elle est de 1:00 PM dès maintenant, et cette valeur est de 10 minutes, la validation commence à 1:10 PM.<br/><br/>Ce paramètre n’affecte pas les tranches dans le passé (tranches avec l’heure de fin de tranche + dataDelay < maintenant) sont traitées sans délai.<br/><br/>Heure supérieure à 23:59 heures doivent spécifié en utilisant le format day.hours:minutes:seconds. Par exemple, pour spécifier les 24 heures, n’utilisez pas de 24:00:00 ; Utilisez plutôt 1.00:00:00. Si vous utilisez 24:00:00, il est traité comme 24 jours (24.00:00:00). Pour 1 jour et 4 heures, spécifiez 1:04:00:00. | N° | 0 |
| retryInterval | Le temps d’attente entre une panne et la renouveler la tentative. S’applique à l’heure actuelle ; Si la commande précédente essayez ayant échoué, nous attendre longtemps après la dernière tentative. <br/><br/>Si elle est de 1:00 pm dès maintenant, nous commençons la première tentative. Si la durée pour terminer le premier contrôle de validation est de 1 minute et l’opération a échoué, la prochaine tentative est à 1:00 1 min (durée) + 1 minute (intervalle) = 1:02 pm. <br/><br/>Pour les tranches dans le passé, il n’y a aucun délai. La nouvelle tentative se produit immédiatement. | N° | 00:01:00 (1 minute) | 
| retryTimeout | Le délai d’attente pour chaque nouvelle tentative.<br/><br/>Si cette propriété est définie à 10 minutes, la validation doit être effectuée dans les 10 minutes. S’il prend plu de 10 minutes pour effectuer la validation, le nouvel essai arrive à expiration.<br/><br/>Si toutes les tentatives de validation a été dépassé, la tranche est marquée comme TimedOut. | N° | 00:10:00 (10 minutes) |
| maximumRetry | Nombre de fois pour vérifier la disponibilité des données externes. La valeur maximale autorisée est de 10. | N° | 3 | 

## <a name="scoped-datasets"></a>Étendue des groupes de données
Vous pouvez créer des groupes de données qui ont une portée limitée à un tuyau à l’aide de la propriété de **groupes de données** . Ces ensembles de données utilisable uniquement par les activités au sein de ce pipeline mais pas par les activités d’autres pipelines. L’exemple suivant définit un pipeline avec deux groupes de données - InputDataset-rdc et OutputDataset-rdc - à utiliser dans le pipeline :  

> [AZURE.IMPORTANT] Étendue des groupes de données sont pris en charge uniquement avec les pipelines uniques (**pipelineMode** valeur **OneTime**). Pour plus d’informations, reportez-vous à la section [Onetime pipeline](data-factory-scheduling-and-execution.md#onetime-pipeline) .

    {
        "name": "CopyPipeline-rdc",
        "properties": {
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": false
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "InputDataset-rdc"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset-rdc"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1,
                        "style": "StartOfInterval"
                    },
                    "name": "CopyActivity-0"
                }
            ],
            "start": "2016-02-28T00:00:00Z",
            "end": "2016-02-28T00:00:00Z",
            "isPaused": false,
            "pipelineMode": "OneTime",
            "expirationTime": "15.00:00:00",
            "datasets": [
                {
                    "name": "InputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "InputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/input",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": true,
                        "policy": {}
                    }
                },
                {
                    "name": "OutputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "OutputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/output",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": false,
                        "policy": {}
                    }
                }
            ]
        }
    }