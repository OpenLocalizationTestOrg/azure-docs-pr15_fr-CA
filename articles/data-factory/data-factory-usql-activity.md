<properties 
    pageTitle="Exécuter un script SQL-U sur Azure données lac Analytique d’Azure Data Factory" 
    description="Découvrez comment traiter les données en exécutant des scripts de U-SQL Azure données lac Analytique compute service." 
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
    ms.date="09/12/2016" 
    ms.author="spelluru"/>

# <a name="run-u-sql-script-on-azure-data-lake-analytics-from-azure-data-factory"></a>Exécuter un script SQL-U sur Azure données lac Analytique d’Azure Data Factory
> [AZURE.SELECTOR]
[La ruche](data-factory-hive-activity.md)  
[Porcs](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop en flux continu](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[La procédure stockée](data-factory-stored-proc-activity.md)
[Données lac Analytique U-SQL](data-factory-usql-activity.md)
[.NET personnalisés](data-factory-use-custom-activities.md)
 
Un tuyau dans une usine de données Azure traite les données dans les services de stockage à l’aide des services de calcul lié. Il contient une séquence d’activités dans lequel chaque activité effectue une opération de traitement spécifiques. Cet article décrit l' **Activité de données lac Analytique U-SQL** qui exécute un script **SQL-U** sur un service de calcul lié **Azure données lac Analytique** . 

> [AZURE.NOTE] 
> Créer un compte Azure données lac Analytique avant de créer un tuyau avec une activité de données lac Analytique U-SQL. Pour en savoir plus sur Azure données lac Analytique, consultez [mise en route avec Azure données lac Analytique](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Consultez [créer votre premier didacticiel de pipeline](data-factory-build-your-first-pipeline.md) pour obtenir la procédure détaillée pour créer une fabrique de données, les services liés, des groupes de données et un tuyau. Utiliser des extraits de JSON avec données fabrique éditeur ou Visual Studio ou Azure PowerShell pour créer des entités de données usine.

## <a name="azure-data-lake-analytics-linked-service"></a>Données Azure lac Analytique lié Service
Vous créez un service **Azure données lac Analytique** lié pour lier un service de calcul Azure données lac Analytique sur une fabrique de données Azure. L’activité de données lac Analytique U-SQL dans le pipeline fait référence à ce service lié. 

L’exemple suivant fournit la définition de JSON pour un service Azure données lac Analytique est lié. 

    {
        "name": "AzureDataLakeAnalyticsLinkedService",
        "properties": {
            "type": "AzureDataLakeAnalytics",
            "typeProperties": {
                "accountName": "adftestaccount",
                "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
                "authorization": "<authcode>",
                "sessionId": "<session ID>", 
                "subscriptionId": "<subscription id>",
                "resourceGroupName": "<resource group name>"
            }
        }
    }


Le tableau suivant fournit des descriptions pour les propriétés utilisées dans la définition de JSON. 

Propriété | Description | Obligatoire
-------- | ----------- | --------
Type de | La propriété type doit être définie : **AzureDataLakeAnalytics**. | Oui
nom de compte | Nom du compte Analytique du lac données Azure. | Oui
dataLakeAnalyticsUri | URI du lac Analytique de données Azure. |  N° 
autorisation | Code d’autorisation est automatiquement récupéré après en cliquant sur le bouton **Autoriser** dans l’éditeur de la fabrique de données et à la fin de la connexion d’accès OAuth.  | Oui 
subscriptionId | Id d’abonnement Azure | Non (si non spécifié, l’inscription de la fabrique de données est utilisée). 
resourceGroupName | Nom du groupe de ressources Azure |  Non (si non spécifié, groupe de ressources de la fabrique de données est utilisé).
ID de session | id de la session de la session d’autorisation OAuth. Chaque id de session est unique et ne peut être utilisé qu’une seule fois. La session Id est généré automatiquement dans l’éditeur de la fabrique de données. | Oui

Le code d’autorisation que vous avez généré à l’aide du bouton **Autoriser** expire après un certain temps. Reportez-vous au tableau suivant pour les délais d’expiration pour les différents types de comptes d’utilisateurs. Le message d’erreur suivant peut s’afficher de message lorsque l’authentification **jeton a expiré**: erreur d’opération des informations d’identification : invalid_grant - AADSTS70002 : erreur de validation des informations d’identification. AADSTS70008 : Le droit d’accès fourni est expiré ou révoqué. L’ID de trace : ID de corrélation d18629e8-af88-43c5-88e3-d8419eb1fca1 : fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp : 2015-12-15 21:09:31Z

 
| Type d’utilisateur | Expire après |
| :-------- | :----------- | 
| Comptes d’utilisateurs non gérés par Azure Active Directory (@hotmail.com, @live.com, etc..) | 12 heures |
| Comptes utilisateurs gérés par Azure Active Directory (DAS) | exécution de 14 jours à compter de la dernière tranche. <br/><br/>90 jours, si une tranche basée sur les services OAuth lié s’exécute au moins une fois tous les 14 jours. |

Pour éviter de/résoudre cette erreur, autoriser à l’aide de l' **Autoriser** à nouveau le bouton lorsque le **jeton a expiré** et le redéploiement du service lié. Vous pouvez également générer des valeurs pour les propriétés de **l’ID de session** et **d’autorisation** par programme à l’aide de code dans la section suivante. 

  
### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Pour générer des valeurs d’ID de session et d’autorisation par programme 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Consultez la [AzureDataLakeStoreLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe de AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)et [AuthorizationSessionGetResponse classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) les rubriques pour plus d’informations sur les classes de fabrique des données utilisées dans le code. Ajoutez une référence à : Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pour la classe WindowsFormsWebAuthenticationDialog. 
 
 
## <a name="data-lake-analytics-u-sql-activity"></a>Activité de données Analytique de LAC U-SQL 

L’extrait suivant de JSON définit un pipeline avec une activité de données lac Analytique U-SQL. La définition d’activité comporte une référence au service Analytique de LAC de données Azure lié créé précédemment.   
  

    {
        "name": "ComputeEventsByRegionPipeline",
        "properties": {
            "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
            "activities": 
            [
                {
                    "type": "DataLakeAnalyticsU-SQL",
                    "typeProperties": {
                        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                        "scriptLinkedService": "StorageLinkedService",
                        "degreeOfParallelism": 3,
                        "priority": 100,
                        "parameters": {
                            "in": "/datalake/input/SearchLog.tsv",
                            "out": "/datalake/output/Result.tsv"
                        }
                    },
                    "inputs": [
                        {
                            "name": "DataLakeTable"
                        }
                    ],
                    "outputs": 
                    [
                        {
                            "name": "EventsByRegionTable"
                        }
                    ],
                    "policy": {
                        "timeout": "06:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "EventsByRegion",
                    "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
                }
            ],
            "start": "2015-08-08T00:00:00Z",
            "end": "2015-08-08T01:00:00Z",
            "isPaused": false
        }
    }


Le tableau suivant décrit les noms et les descriptions de propriétés qui sont spécifiques à cette activité. 

Propriété | Description | Obligatoire
:-------- | :----------- | :--------
type de | La propriété type doit être définie à **DataLakeAnalyticsU-SQL**. | Oui
scriptPath | Chemin d’accès au dossier qui contient le script SQL-U. Nom du fichier est sensible à la casse. | N° (si vous utilisez un script)
scriptLinkedService | Services liés qui lie le stockage qui contient le script sur le factory de données | N° (si vous utilisez un script)
script | Spécifiez un script inline au lieu de spécifier scriptPath et scriptLinkedService. Par exemple : « script » : « Test de création de base de données ». | N° (si vous utilisez scriptPath et scriptLinkedService)
degreeOfParallelism | Le nombre maximal de nœuds utilisés simultanément pour exécuter la tâche. | N°
priorité | Détermine quelles tâches tous en attente doivent être sélectionnées pour s’exécuter en premier. Plus le nombre, la priorité est élevée. | N° 
paramètres | Paramètres du script SQL-U | N° 

Voir la [Définition de Script de SearchLogProcessing.txt](#script-definition) pour la définition de script. 

## <a name="sample-input-and-output-datasets"></a>Exemple d’entrée et de sortie des groupes de données

### <a name="input-dataset"></a>Jeu de données d’entrée
Dans cet exemple, les données d’entrée se trouve dans un magasin de LAC de données Azure (fichier SearchLog.tsv dans le dossier datalake/entrée). 

    {
        "name": "DataLakeTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }   

### <a name="output-dataset"></a>Dataset de sortie
Dans cet exemple, les données de la sortie générées par le script SQL-U sont stockées dans un magasin de LAC de données Azure (dossier datalake/sortie). 

    {
        "name": "EventsByRegionTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="sample-data-lake-store-linked-service"></a>Magasin de LAC données exemple lié Service
Voici la définition de l’échantillon, que le lac Azure Data Store lié de service utilisé par les jeux de données d’entrée/sortie. 

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

Article de [déplacer des données vers et à partir du magasin de LAC de données Azure](data-factory-azure-datalake-connector.md) voir pour les descriptions des propriétés JSON. 

## <a name="sample-u-sql-script"></a>Exemple de Script SQL-U 

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv(nullEscape:"#NULL#");
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start <= DateTime.Parse("2012/02/19");
    
    OUTPUT @rs1   
        TO @out
          USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

Les valeurs de **@in** et **@out** dans le script SQL-U, les paramètres sont transmis dynamiquement par le chargeur automatique de documents à l’aide de la section « Paramètres ». Consultez la section « Paramètres » dans la définition de pipeline.

Vous pouvez spécifier des autres propriétés telles que degreeOfParallelism et de la priorité ainsi que dans la définition de pipeline pour les travaux qui s’exécutent sur le service Azure données lac Analytique.

## <a name="dynamic-parameters"></a>Paramètres dynamiques
Dans l’exemple de définition de pipeline, entrée et de sortie sont affectées aux paramètres avec des valeurs codées en dur. 

    "parameters": {
        "in": "/datalake/input/SearchLog.tsv",
        "out": "/datalake/output/Result.tsv"
    }

Il est possible d’utiliser à la place des paramètres dynamiques. Par exemple : 

    "parameters": {
        "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
        "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
    }

Dans ce cas, les fichiers d’entrée sont toujours extraits à partir du dossier /datalake/input et les fichiers de sortie sont générées dans le dossier /datalake/output. Les noms de fichier sont dynamiques en fonction de l’heure de début de tranche.  