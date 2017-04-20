<properties 
    pageTitle="Utiliser des activités d’apprentissage de l’ordinateur | Microsoft Azure" 
    description="Décrit la création créer des pipelines prédictives à l’aide de la fabrique de données Azure et d’Azure Machine Learning" 
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
    ms.date="09/06/2016" 
    ms.author="shlo"/>

# <a name="create-predictive-pipelines-using-azure-machine-learning-activities"></a>Créer des pipelines prédictives à l’aide des activités d’apprentissage d’ordinateur Azure   
> [AZURE.SELECTOR]
[La ruche](data-factory-hive-activity.md)  
[Porcs](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop en flux continu](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[La procédure stockée](data-factory-stored-proc-activity.md)
[Données lac Analytique U-SQL](data-factory-usql-activity.md)
[.NET personnalisés](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introduction

[Apprentissage de Machine Azure](https://azure.microsoft.com/documentation/services/machine-learning/) vous permet de vous permet de créer, tester et déployer des solutions d’analytique prédictive. À partir d’un point de vue générale, il s’effectue en trois étapes : 

1. **Créer une expérience de formation**. Vous effectuez cette étape à l’aide de Studio Azure ML. Le studio ML est un environnement collaboratif de développement visuel qui vous permet de former et de test d’un modèle de prévision analytique à l’aide de données d’apprentissage.
2. **Convertir en une expérience prédictive**. Une fois votre modèle a été formé avec les données existantes et que vous êtes prêt à utiliser pour évaluer les nouvelles données, vous préparez et Rationalisez votre expérience d’évaluation.
3. **Déployer comme un service web**. Vous pouvez publier votre expérience d’évaluation sous la forme d’un service web Azure. Vous pouvez envoyer des données à votre modèle via ce point de terminaison de service web et recevoir des prévisions de résultat à partir du modèle.  

Azure Data Factory vous permet de créer facilement des pipelines qui utilisent un publié [Azure Machine Learning] [ azure-machine-learning] web service pour analytique prédictive. Voir articles [d’Introduction à Azure Data Factory](data-factory-introduction.md) et [créer votre premier pipeline](data-factory-build-your-first-pipeline.md) rapidement mise en route du service Factory de données Azure. 

À l’aide de l' **Activité d’exécution du lot** dans un pipeline Azure Data Factory, vous pouvez appeler un service web de Azure ML pour effectuer des prévisions sur les données de lot. Consultez la section [appeler un ML Azure web service à l’aide de l’activité de l’exécution du traitement par lots](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) pour plus de détails.

Au fil du temps, les modèles prédictifs dans le ML Azure score des expériences doivent être formés à l’aide de nouveaux jeux de données d’entrée. Vous pouvez former un modèle d’Azure ML à partir d’un pipeline de données fabrique en procédant comme suit : 

1. Publier l’expérience de formation (expérience pas prédictive) sous la forme d’un service web. Vous procédez à cette étape dans le Studio de ML Azure comme vous le faisiez pour exposer l’expérience prédictive sous la forme d’un service web dans le scénario précédent.
2. Utilisez l’activité de l’exécution par lots Azure ML pour invoquer le service web pour l’expérimentation de la formation. En fait, vous pouvez utiliser l’activité de l’exécution par lots de ML Azure pour appeler le service web de formation et des services web score. 
  
Une fois que vous avez terminé avec le recyclage, à mettre à jour le service web de score (expérience prédictive exposé comme un service web) avec le modèle nouvellement formé. Voici la procédure : 

1. Ajouter un point de terminaison par défaut pour le service web de score. Le point de terminaison par défaut du service web ne peut pas être mis à jour, vous devez créer un point de terminaison par défaut via le portail Azure. Voir l’article [Créer les points de terminaison](../machine-learning/machine-learning-create-endpoint.md) pour des informations conceptuelles et de procédures.
2. Mettre à jour les services existants de ML Azure lié pour l’attribution de scores pour utiliser le point de terminaison par défaut. Commencer à utiliser le nouveau point de terminaison à utiliser le service web qui est mis à jour.
3. L' **Activité des ressources de mise à jour ML Azure** permet de mettre à jour le service web avec le modèle nouvellement formé.  

Pour plus d’informations, reportez-vous à la section [modèles de ML de Azure mise à jour à l’aide de l’activité de la ressource mise à jour](#updating-azure-ml-models-using-the-update-resource-activity) . 

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Appel d’un service web à l’aide d’activité de l’exécution du traitement par lots

Vous permet d’orchestrer de traitement et déplacement de données Azure Data Factory et puis effectuez l’exécution du lot à l’aide de la formation de Machine Azure. Voici les étapes de niveau supérieur :

1. Créer un service de formation de Machine Azure lié. Vous devez :
    1. **URI de demande** pour l’exécution du traitement par lots API. Vous pouvez trouver l’URI de la demande en cliquant sur le lien de **L’exécution par lots** dans la page des services web.
    1. **Clé de l’API** du service web de formation de Machine Azure publié. Vous pouvez trouver la clé de l’API en cliquant sur le service web que vous avez publié. 
 2. Utilisez l’activité **AzureMLBatchExecution** .

    ![Machine de formation de tableau de bord](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

    ![URI de lot](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scénario : Expériences à l’aide de Web service entrées/sorties font référence aux données dans le stockage Blob Azure
Dans ce scénario, le service Web de formation de Machine Azure effectue des prévisions à l’aide de données à partir d’un fichier dans un stockage blob Azure et stocke les résultats de la prévision dans le stockage blob. Le JSON suivant définit un pipeline de données fabrique avec une activité AzureMLBatchExecution. L’activité a le groupe de données **DecisionTreeInputBlob** en tant qu’entrée et **DecisionTreeResultBlob** comme sortie. Le **DecisionTreeInputBlob** est passée en tant qu’entrée pour le service web à l’aide de la propriété JSON **webServiceInput** . Le **DecisionTreeResultBlob** est passé sous la forme d’une sortie au service Web à l’aide de la propriété JSON **webServiceOutputs** .  

> [AZURE.IMPORTANT] 
> Si le service web accepte plusieurs entrées, utilisez la propriété **webServiceInputs** au lieu d’utiliser **webServiceInput**. Reportez-vous à la section [service Web nécessite plusieurs entrées](#web-service-requires-multiple-inputs) pour un exemple d’utilisation de la propriété webServiceInputs.
>  
> Groupes de données qui sont référencées par le **webServiceInput**/propriétés**webServiceInputs** et **webServiceOutputs** (dans **typeProperties**) doivent également être incluses dans l’activité, **entrées** et **sorties**.
> 
> Dans votre expérience ML d’Azure, entrée du service web et les voies de sortie et les paramètres globaux ont des noms par défaut (« input1 », « input2 ») que vous pouvez personnaliser. Les noms que vous utilisez pour les paramètres globalParameters, webServiceOutputs et webServiceInputs doivent correspondre exactement les noms dans les expériences. Vous pouvez afficher la charge utile de demande exemple sur la page d’aide de l’exécution par lots pour votre point de terminaison Azure ML vérifier le mappage attendu. 


    {
      "name": "PredictivePipeline",
      "properties": {
        "description": "use AzureML model",
        "activities": [
          {
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [
              {
                "name": "DecisionTreeInputBlob"
              }
            ],
            "outputs": [
              {
                "name": "DecisionTreeResultBlob"
              }
            ],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob"
                }                
            },
            "policy": {
              "concurrency": 3,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            }
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }

> [AZURE.NOTE] Seuls des entrées et des sorties de l’activité de la AzureMLBatchExecution peuvent être passés en tant que paramètres au service Web. Par exemple, dans l’extrait de code ci-dessus JSON, DecisionTreeInputBlob est une entrée pour l’activité de AzureMLBatchExecution, qui est transmise comme entrée au service Web via webServiceInput paramètre.   

### <a name="example"></a>Exemple

Cet exemple utilise le stockage Azure pour contenir les données d’entrée et de sortie. 

Il est recommandé que vous parcourez [votre premier pipeline avec Data Factory de génération] [ adf-build-1st-pipeline] didacticiel avant de passer par cet exemple. Utilisez l’éditeur de Factory de données pour créer des artefacts de Data Factory (services liés, des groupes de données, pipeline) dans cet exemple.   
 

1. Création d’un **service lié** pour le **Stockage Azure**. Si les fichiers d’entrée et de sortie sont des comptes de stockage différents, vous avez besoin de deux services liés. Voici un exemple JSON :

        {
          "name": "StorageLinkedService",
          "properties": {
            "type": "AzureStorage",
            "typeProperties": {
              "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
            }
          }
        }

2. Créer **d’entrée** Azure Data Factory **dataset**. Contrairement à certains autres données fabrique des groupes de données, ces ensembles de données doivent contenir des valeurs **folderPath** et de **nom de fichier** . Vous pouvez utiliser le partitionnement pour chaque exécution de lot (chaque tranche de données) traiter ou produire une entrée unique et fichiers de sortie. Vous devrez peut-être inclure certaines activités en amont pour transformer l’entrée dans le format de fichier CSV et le placer dans le compte de stockage pour chaque section. Dans ce cas, ne serait pas inclus les paramètres **externalData** et **externe** indiqués dans l’exemple suivant, et votre DecisionTreeInputBlob serait le dataset de sortie d’une autre activité.

        {
          "name": "DecisionTreeInputBlob",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "azuremltesting/input",
              "fileName": "in.csv",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Day",
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
    
    Votre fichier csv d’entrée doit avoir la ligne d’en-tête de colonne. Si vous utilisez l' **Activité de copie** pour créer ou le déplacement du csv dans le stockage blob, vous devez définir la propriété de récepteur **blobWriterAddHeader** à **true**. Par exemple :
    
         sink: 
         {
             "type": "BlobSink",     
             "blobWriterAddHeader": true 
         }
     
    Si le fichier csv n’a pas la ligne d’en-tête, vous pouvez voir l’erreur suivante : erreur dans l’activité de **: erreur de lecture de chaîne. Jeton inattendu : StartObject. Chemin d’accès '', ligne 1, position 1**.
3. Créer la **sortie** d’usine de données Azure **dataset**. Cet exemple utilise le partitionnement pour créer un chemin d’accès de sortie unique pour l’exécution de chaque tranche. Sans le partitionnement, l’activité risque de remplacer le fichier.

        {
          "name": "DecisionTreeResultBlob",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "azuremltesting/scored/{folderpart}/",
              "fileName": "{filepart}result.csv",
              "partitionedBy": [
                {
                  "name": "folderpart",
                  "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "yyyyMMdd"
                  }
                },
                {
                  "name": "filepart",
                  "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HHmmss"
                  }
                }
              ],
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Day",
              "interval": 15
            }
          }
        }

4. Création d’un **service lié** de type : **AzureMLLinkedService**, fournit l’API de clé et le modèle d’URL de l’exécution du traitement par lots.
        
        {
          "name": "MyAzureMLLinkedService",
          "properties": {
            "type": "AzureML",
            "typeProperties": {
              "mlEndpoint": "https://[batch execution endpoint]/jobs",
              "apiKey": "[apikey]"
            }
          }
        }
5. Enfin, créez un tuyau contenant une activité **AzureMLBatchExecution** . Lors de l’exécution, le pipeline effectue les opérations suivantes :
    1. Obtient l’emplacement du fichier d’entrée à partir de vos groupes de données d’entrée.
    2. Appelle l’exécution de lot d’Azure Machine Learning API
    3. Copie la sortie de l’exécution du lot dans le blob dans votre groupe de données de sortie. 

    > [AZURE.NOTE] Activité de la AzureMLBatchExecution peut avoir zéro, une ou plusieurs entrées et une ou plusieurs sorties.

        {
          "name": "PredictivePipeline",
          "properties": {
            "description": "use AzureML model",
            "activities": [
              {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                  {
                    "name": "DecisionTreeInputBlob"
                  }
                ],
                "outputs": [
                  {
                    "name": "DecisionTreeResultBlob"
                  }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }                
                },
                "policy": {
                  "concurrency": 3,
                  "executionPriorityOrder": "NewestFirst",
                  "retry": 1,
                  "timeout": "02:00:00"
                }
              }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
          }
        }

    Dates/heures de **début** et de **fin** doivent être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de **fin** est facultative. Si vous ne spécifiez pas de valeur pour la propriété **end** , il est calculé en tant que «**début + 48 heures.**» Pour exécuter le pipeline indéfiniment, spécifier la valeur de la propriété **end** **9999-09-09** . Pour plus d’informations sur les propriétés JSON, consultez [Référence de script de JSON](https://msdn.microsoft.com/library/dn835050.aspx) .

    > [AZURE.NOTE] Définition d’une entrée pour l’AzureMLBatchExecution d’activité est facultative. 

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scénario : Expériences à l’aide des Modules de lecteur/Writer pour faire référence à des données dans différents stockages

Un autre scénario courant lors de la création d’expériences d’Azure ML consiste à utiliser des modules de lecteur et de Writer. Le module de lecteur est utilisé pour charger des données dans une expérimentation et le module writer pour enregistrer des données à partir de vos expériences. Pour plus d’informations sur les modules de lecteur et de writer, consultez les rubriques de [lecteur](https://msdn.microsoft.com/library/azure/dn905997.aspx) et de [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) sur MSDN Library.     

Lorsque vous utilisez les modules de lecteur et de writer, il est recommandé d’utiliser un paramètre du service Web pour chaque propriété de ces modules de lecteur/writer. Ces paramètres web permettent de configurer les valeurs lors de l’exécution. Par exemple, vous pouvez créer une expérience avec un module de lecteur qui utilise une base de données de SQL Azure : XXX.database.windows.net. Une fois que le service web a été déployé, que vous souhaitez activer les clients du service web spécifier un autre Azure SQL Server est appelé YYY.database.windows.net. Vous pouvez utiliser un paramètre du service Web pour permettre à cette valeur pour être configuré.

> [AZURE.NOTE] Entrée du service Web et la sortie sont différents des paramètres de service Web. Dans le premier scénario, vous avez vu comment une entrée et la sortie peuvent être spécifiés pour un service Web de ML d’Azure. Dans ce scénario, vous passez des paramètres pour un service Web qui correspondent aux propriétés des modules de lecteur/writer. 

Examinons un scénario d’utilisation des paramètres de service Web. Vous avez un service web Azure Machine Learning déployé qui utilise un module de lecteur pour lire des données à partir d’une des sources de données prises en charge par formation de Machine Azure (par exemple : base de données de SQL Azure). Après que l’exécution de lot est exécutée, les résultats sont écrits à l’aide d’un module de Writer (de base de données SQL Azure).  Aucun sorties et entrées de service web ne sont définies dans les expériences. Dans ce cas, nous vous recommandons de configurer les paramètres du service web appropriée pour les modules de lecteur et de writer. Cette configuration permet la lecture/écriture de modules à configurer lors de l’utilisation de l’activité AzureMLBatchExecution. Vous spécifiez les paramètres de service Web dans la section **globalParameters** dans l’activité JSON comme suit. 


    "typeProperties": {
        "globalParameters": {
            "Param 1": "Value 1",
            "Param 2": "Value 2"
        }
    }

Vous pouvez également utiliser les [Fonctions d’usine de données](https://msdn.microsoft.com/library/dn835056.aspx) en passant les valeurs pour les paramètres de service Web, comme illustré dans l’exemple suivant :

    "typeProperties": {
        "globalParameters": {
           "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
        }
    }
 
> [AZURE.NOTE] Les paramètres de service Web sont sensibles à la casse, assurez-vous que les noms que vous spécifiez dans l’activité JSON correspondent à ceux qui sont exposées par le service Web. 

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>À l’aide d’un module de lecteur pour lire des données à partir de plusieurs fichiers dans Azure Blob
Pipelines de données volumineuses avec des activités telles que des porcs et ruche peut produire un ou plusieurs fichiers de sortie sans extensions. Par exemple, lorsque vous spécifiez une table externe de la ruche, les données de la table externe de la ruche peuvent être stockées dans le stockage blob Azure avec le 000000_0 de nom suivant. Vous pouvez utiliser le module de lecteur d’une expérience de lire plusieurs fichiers et les utiliser pour les prévisions. 

Lorsque vous utilisez le module de lecteur d’une expérience d’apprentissage automatique de Azure, vous pouvez spécifier les Blob Azure comme entrée. Les fichiers dans le stockage blob Azure peuvent être des fichiers de sortie (exemple : 000000_0) qui sont produites par un script de porc et de la ruche en cours d’exécution sur HDInsight. Le module de lecteur vous permet de lire des fichiers (avec aucune extension) en configurant le **chemin d’accès au conteneur de répertoire/blob**. Les **chemin d’accès au conteneur de** points aux points de conteneur et de **répertoire/blob** au dossier qui contient les fichiers comme indiqué dans l’image suivante. L’astérisque, \*) **Spécifie que tous les fichiers dans le dossier/conteneur (c'est-à-dire, données/aggregateddata/année = 2014/mois-6 /\*)** sont lus dans le cadre de l’expérimentation.

![Propriétés de Blob Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### <a name="example"></a>Exemple 
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline à activité AzureMLBatchExecution avec les paramètres de Service Web

    {
      "name": "MLWithSqlReaderSqlWriter",
      "properties": {
        "description": "Azure ML model with sql azure reader/writer",
        "activities": [
          {
            "name": "MLSqlReaderSqlWriterActivity",
            "type": "AzureMLBatchExecution",
            "description": "test",
            "inputs": [
              {
                "name": "MLSqlInput"
              }
            ],
            "outputs": [
              {
                "name": "MLSqlOutput"
              }
            ],
            "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
                "globalParameters": {
                    "Database server name": "<myserver>.database.windows.net",
                    "Database name": "<database>",
                    "Server user account name": "<user name>",
                    "Server user account password": "<password>"
                }              
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            },
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }
 
Dans l’exemple ci-dessus de JSON :

- Le service Web de formation Azure ordinateur déployé utilise un lecteur et un module writer en lecture/écriture de données depuis/vers une base de données de SQL Azure. Ce service Web expose les quatre paramètres suivants : nom du serveur, nom de la base de données, le nom de compte d’utilisateur Server et mot de passe utilisateur serveur de la base de données.  
- Dates/heures de **début** et de **fin** doivent être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de **fin** est facultative. Si vous ne spécifiez pas de valeur pour la propriété **end** , il est calculé en tant que «**début + 48 heures.**» Pour exécuter le pipeline indéfiniment, spécifier la valeur de la propriété **end** **9999-09-09** . Pour plus d’informations sur les propriétés JSON, consultez [Référence de script de JSON](https://msdn.microsoft.com/library/dn835050.aspx) .

### <a name="other-scenarios"></a>Autres scénarios

#### <a name="web-service-requires-multiple-inputs"></a>Service Web nécessite plusieurs entrées
Si le service web accepte plusieurs entrées, utilisez la propriété **webServiceInputs** au lieu d’utiliser **webServiceInput**. Groupes de données qui sont référencées par le **webServiceInputs** doit également être incluse dans l’activité d' **entrées**.
 
Dans votre expérience ML d’Azure, entrée du service web et les voies de sortie et les paramètres globaux ont des noms par défaut (« input1 », « input2 ») que vous pouvez personnaliser. Les noms que vous utilisez pour les paramètres globalParameters, webServiceOutputs et webServiceInputs doivent correspondre exactement les noms dans les expériences. Vous pouvez afficher la charge utile de demande exemple sur la page d’aide de l’exécution par lots pour votre point de terminaison Azure ML vérifier le mappage attendu.


    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [{
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [{
                    "name": "inputDataset1"
                }, {
                    "name": "inputDataset2"
                }],
                "outputs": [{
                    "name": "outputDataset"
                }],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": "inputDataset1",
                        "input2": "inputDataset2"
                    },
                    "webServiceOutputs": {
                        "output1": "outputDataset"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }

#### <a name="web-service-does-not-require-an-input"></a>Service Web ne nécessite pas d’entrée

Azure ML lot l’exécution des services web peuvent servir à exécuter des workflows, des scripts exemple de R ou les Python, qui ne nécessitent pas d’intrants. Ou bien, l’essai peut être configuré avec un module de lecteur qui n’expose pas les GlobalParameters. Dans ce cas, l’activité AzureMLBatchExecution doit être configurée comme suit :

    {
        "name": "scoring service",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "myBlob"
            }
        ],
        "typeProperties": {
            "webServiceOutputs": {
                "output1": "myBlob"
            }              
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },
   

#### <a name="web-service-does-not-require-an-inputoutput"></a>Service Web ne nécessite pas une entrée/sortie
Le service web de Azure ML lot d’exécution n’est peut-être pas une sortie Web Service configurée. Dans cet exemple, il n’y a aucune entrée de Service Web ou une sortie de, ni ne les GlobalParameters sont configurés. Il y a toujours une sortie configurée sur l’activité elle-même, mais il n’est pas donné comme un webServiceOutput.

    {
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "placeholderOutputDataset"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web Service utilise lecteurs et writers et l’exécution de l’activité uniquement lorsque les autres activités ont réussi

L’Azure ML web lecteur et writer de modules de service peuvent être configurés pour s’exécuter avec ou sans les GlobalParameters. Toutefois, vous souhaiterez incorporer des appels de service dans un pipeline qui utilise des dépendances de groupe de données pour invoquer le service uniquement lorsqu’un traitement en amont est terminée. Vous pouvez également déclencher d’autres actions après que l’exécution du lot terminée à l’aide de cette approche. Dans ce cas, vous pouvez exprimer les dépendances à l’aide des entrées de l’activité et les sorties, sans la nommer un d’eux en tant que Service Web entrées ou les sorties.

    {
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "inputs": [
            {
                "name": "upstreamData1"
            },
            {
                "name": "upstreamData2"
            }
        ],
        "outputs": [
            {
                "name": "downstreamData"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

Les **points** sont les suivants :

-   Si votre point de terminaison expérience utilise un webServiceInput : il est représenté par un groupe de données blob et est inclus dans les entrées de l’activité et la propriété webServiceInput. Dans le cas contraire, la propriété webServiceInput est omise. 
-   Si votre point de terminaison expérience utilise webServiceOutput(s) : ils sont représentés par des groupes de données blob et sont inclus dans les sorties de l’activité et dans la propriété webServiceOutputs. Sorties de l’activité et webServiceOutputs sont mappées par le nom de chaque sortie à l’expérimentation. Dans le cas contraire, la propriété webServiceOutputs est omise.
-   Si votre point de terminaison expérience expose globalParameter(s), elles figurent dans la propriété globalParameters de l’activité sous forme de paires de valeur clé. Dans le cas contraire, la propriété globalParameters est omise. Les clés sont sensibles à la casse. [Fonctions de Azure Data Factory](data-factory-scheduling-and-execution.md#data-factory-functions-reference) peuvent être utilisées dans les valeurs. 
- Des groupes de données supplémentaires peuvent figurer dans les entrées et les sorties de propriétés de l’activité, sans référencé dans le typeProperties de l’activité. Ces ensembles de données régissent l’exécution à l’aide de la section dépendances mais sont sinon ignorés par l’activité AzureMLBatchExecution. 


## <a name="updating-models-using-update-resource-activity"></a>Mise à jour de modèles à l’aide de la mise à jour de ressources l’activité
Au fil du temps, les modèles prédictifs dans le ML Azure score des expériences doivent être formés à l’aide de nouveaux jeux de données d’entrée. Une fois que vous avez terminé avec le recyclage, à mettre à jour le service web de score avec le modèle ML retrained. La procédure standard pour activer les modèles de Azure ML de réadaptation et mise à jour via des services web est les suivants : 

1. Créer une expérience dans [Azure ML Studio](https://studio.azureml.net). 
2. Lorsque vous êtes satisfait du modèle, permet de publier des services web pour à la fois **formation essayer** Azure ML Studio et notation /**expérience prédictive**.

Le tableau suivant décrit les services web utilisés dans cet exemple.  Pour plus d’informations, consultez [modèles de dispenser une formation de Machine par programme](../machine-learning/machine-learning-retrain-models-programmatically.md) .

| Type de service web | Description 
| :------------------ | :---------- 
| **Service web de formation** | Reçoit les données d’apprentissage et produit des modèles bien formés. La sortie de la reconversion est un fichier .ilearner dans un stockage Azure Blob.  **point de terminaison par défaut** est créé automatiquement pour vous, lorsque vous publiez l’expérience de formation sous la forme d’un service web. Vous pouvez créer plusieurs points de terminaison, mais il utilise uniquement le point de terminaison par défaut |
| **Service web de score** | Reçoit des exemples de données non labellisés et rend les prévisions. La sortie de prévision peut avoir diverses formes, par exemple un fichier .csv ou de lignes dans une base de données SQL d’Azure, selon la configuration de l’expérience. Le point de terminaison par défaut est créé automatiquement pour vous, lorsque vous publiez l’expérience prédictive sous la forme d’un service web. Créer deuxième **point de terminaison par défaut et être mis à jour** à l’aide du [portail Azure](https://manage.windowsazure.com). Vous pouvez créer plusieurs points de terminaison, mais cet exemple n'utilise qu’un seul point de terminaison mis à jour par défaut. Voir l’article [Créer les points de terminaison](../machine-learning/machine-learning-create-endpoint.md) pour les étapes.       
 
L’image suivante illustre la relation entre la formation et évaluation des points de terminaison dans Azure ML. 

![Services Web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)


Vous pouvez appeler le **service web de formation** à l’aide de l' **Activité de l’exécution du lot Azure ML**. Appel d’un service web de formation est identique à l’appel d’un service web de Azure ML (service web de notation) pour les données de définition de score. Les sections précédentes expliquent comment appeler un service web de Azure ML à partir d’un pipeline Azure Data Factory en détail. 
  
Vous pouvez appeler le **service web de score** pour mettre à jour le service web avec le modèle qui vient d’être formé à l’aide de l' **Activité de ressource mise à jour d’Azure ML** . Comme indiqué dans le tableau ci-dessus, vous devez créer et utiliser le point de terminaison par défaut mis à jour. En outre, mise à jour de tous les services liés existants dans votre usine de données à utiliser au point de terminaison par défaut afin qu’ils utilisent toujours le dernier modèle retrained. 

Le scénario suivant fournit plus de détails. Il a par exemple pour le recyclage et la mise à jour des modèles d’Azure ML à partir d’un pipeline Azure Data Factory. 
 
### <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scénario : recyclage et mise à jour d’un modèle d’Azure ML
Cette section fournit un pipeline d’exemple qui utilise l' **activité de l’exécution du lot Azure ML** pour former un modèle. Le pipeline utilise également l' **activité de ressources de mise à jour de ML Azure** pour mettre à jour le modèle dans le service web de score. Cette section fournit également des extraits de JSON pour tous les services liés, les datasets et les opportunités dans l’exemple. 

Voici l’affichage du diagramme du pipeline exemple. Comme vous pouvez le voir, l’activité de l’exécution par lots Azure ML prend l’entrée de la formation et génère une sortie de formation (fichier iLearner). L’activité de ressources de mise à jour de ML Azure prend la sortie de cette formation et met à jour le modèle dans la notation point de terminaison de service web. L’activité de la ressource mise à jour ne génère pas de sortie. Le placeholderBlob est simplement un dataset de sortie factice qui est requis par le service Factory de données Azure pour exécuter le pipeline. 

![diagramme du pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


#### <a name="azure-blob-storage-linked-service"></a>Service de stockage lié Blob Azure :
Le stockage Azure contient les données suivantes :

- données d’apprentissage. Les données d’entrée pour le service web de formation Azure ML.  
- fichier d’iLearner. La sortie du service web de formation Azure ML. Ce fichier est également l’entrée de l’activité de la ressource de mise à jour.  
   
Voici l’exemple de définition de JSON du service lié : 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
            }
        }
    }


#### <a name="training-input-dataset"></a>Dataset d’entrée de la formation :
Le jeu de données suivant représente les données d’entrée de formation pour le service web de formation Azure ML. L’activité de l’exécution par lots de ML Azure prend ce groupe de données comme entrée. 

    {
        "name": "trainingData",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "labeledexamples",
                "fileName": "labeledexamples.arff",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Week",
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

#### <a name="training-output-dataset"></a>Dataset de sortie de formation :
Le jeu de données suivant représente le fichier d’iLearner de sortie à partir du service web de formation Azure ML. L’activité de l’exécution par lots Azure ML produit ce dataset. Ce groupe de données est également l’entrée de l’activité de la ressource de mise à jour Azure ML.

    {
        "name": "trainedModelBlob",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "trainingoutput",
                "fileName": "model.ilearner",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Week",
                "interval": 1
            }
        }
    }

#### <a name="linked-service-for-azure-ml-training-endpoint"></a>Service lié pour le point de terminaison de formation Azure ML 
L’extrait suivant de JSON définit un service de formation de Machine Azure lié qui pointe vers le point de terminaison par défaut du service web formation. 

    {   
        "name": "trainingEndpoint",
        "properties": {
            "type": "AzureML",
            "typeProperties": {
                "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
                "apiKey": "myKey"
            }
        }
    }

Dans **Azure ML Studio**, procédez comme suit pour obtenir les valeurs de **mlEndpoint** et de **apiKey**:

1. Dans le menu de gauche, cliquez sur **Les SERVICES WEB** .
2. Cliquez sur le **service web de formation** dans la liste des services web. 
3. Cliquez sur Copier en regard de la zone de texte **clé de l’API** . Dans l’éditeur de données usine JSON, collez la clé dans le Presse-papiers.
4. Dans le **studio de ML d’Azure**, cliquez sur le lien de **L’exécution par lots** .
5. Copier l' **URI de la demande** à partir de la section **demande** et collez-le dans l’éditeur de données usine JSON.   


#### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Services liés pour point de terminaison score Azure ML mis à jour :
L’extrait suivant de JSON définit un service de formation de Machine Azure lié qui pointe vers le point de terminaison de mise à jour de celle par défaut du service web score.  

    {
        "name": "updatableScoringEndpoint2",
        "properties": {
            "type": "AzureML",
            "typeProperties": {
                "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
                "apiKey": "endpoint2Key",
                "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
            }
        }
    }


Avant de créer et de déployer un ML Azure lié service, suivez les étapes décrites dans l’article de [Créer les points de terminaison](../machine-learning/machine-learning-create-endpoint.md) pour créer un deuxième (celle par défaut et être mis à jour) point de terminaison pour le service web de score.

Après avoir créé le point de terminaison par défaut mis à jour, effectuez les opérations suivantes :

- Cliquez sur **L’exécution du traitement par lots** pour obtenir la valeur de l’URI de la propriété JSON **mlEndpoint** .
- Cliquez sur le lien de **Mise à jour de ressource** pour obtenir la valeur de l’URI de la propriété JSON **updateResourceEndpoint** . La clé de l’API est sur la page de point de terminaison elle-même (dans le coin en bas à droite). 

![point de terminaison de mise à jour](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

 
#### <a name="placeholder-output-dataset"></a>Espace réservé de groupe de données de sortie :
L’activité de la ressource de mise à jour de ML Azure ne génère pas de sortie. Toutefois, Azure Data Factory requiert un dataset de sortie pour la planification d’un pipeline. Par conséquent, nous utilisons un dataset mannequin/espace réservé dans cet exemple.  

    {
        "name": "placeholderBlob",
        "properties": {
            "availability": {
                "frequency": "Week",
                "interval": 1
            },
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "any",
                "format": {
                    "type": "TextFormat"
                }
            }
        }
    }


#### <a name="pipeline"></a>Pipeline
Le pipeline a deux activités : **AzureMLBatchExecution** et **AzureMLUpdateResource**. L’activité de l’exécution par lots de ML Azure prend les données d’apprentissage comme entrée et produit un fichier d’iLearner sous la forme d’une sortie. L’activité appelle le service web de formation (l’expérience de la formation est exposé comme un service web) avec les données d’entrée de formation et reçoit le fichier ilearner à partir de webservice. Le placeholderBlob est simplement un dataset de sortie factice qui est requis par le service Factory de données Azure pour exécuter le pipeline. 

![diagramme du pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


    {
        "name": "pipeline",
        "properties": {
            "activities": [
                {
                    "name": "retraining",
                    "type": "AzureMLBatchExecution",
                    "inputs": [
                        {
                            "name": "trainingData"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "trainedModelBlob"
                        }
                    ],
                    "typeProperties": {
                        "webServiceInput": "trainingData",
                        "webServiceOutputs": {
                            "output1": "trainedModelBlob"
                        }              
                     },
                    "linkedServiceName": "trainingEndpoint",
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "02:00:00"
                    }
                },
                {
                    "type": "AzureMLUpdateResource",
                    "typeProperties": {
                        "trainedModelName": "Training Exp for ADF ML [trained model]",
                        "trainedModelDatasetName" :  "trainedModelBlob"
                    },
                    "inputs": [
                        {
                            "name": "trainedModelBlob"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "placeholderBlob"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "name": "AzureML Update Resource",
                    "linkedServiceName": "updatableScoringEndpoint2"
                }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }


### <a name="reader-and-writer-modules"></a>Lecteur et Writer Modules

Un scénario courant pour utiliser les paramètres de service Web est l’utilisation de Writers et les lecteurs de SQL Azure. Le module de lecteur est utilisé pour charger des données dans une expérience à partir des services de gestion des données en dehors d’Azure Machine Learning Studio. Le module de writer est pour enregistrer les données dans les services de gestion des données en dehors d’Azure Machine Learning Studio à partir de vos expériences.  

Pour plus d’informations sur Azure Blob/Azure SQL lecteur/writer, consultez les rubriques de [lecteur](https://msdn.microsoft.com/library/azure/dn905997.aspx) et de [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) sur MSDN Library. L’exemple de la section précédente utilisé le lecteur d’Azure Blob et le writer de Blob d’Azure. Cette section décrit l’utilisation de la lecture d’Azure SQL et un Azure SQL writer.


## <a name="frequently-asked-questions"></a>Forum aux questions

**Q:** J’ai plusieurs fichiers qui sont générés par mon pipelines de données volumineuses. Puis-je utiliser l’activité AzureMLBatchExecution pour travailler sur tous les fichiers ?

**A:** Oui. Reportez-vous à la section **à l’aide d’un module de lecteur pour lire des données à partir de plusieurs fichiers dans Azure Blob** pour plus de détails. 

## <a name="azure-ml-batch-scoring-activity"></a>Activité des lots de ML Azure de score
Si vous utilisez l’activité **AzureMLBatchScoring** pour s’intégrer à la formation de Machine d’Azure, nous vous recommandons d’utiliser les dernières activités de **AzureMLBatchExecution** . 

L’activité AzureMLBatchExecution est introduite dans la version d’août 2015 de PowerShell d’Azure et d’Azure SDK.

Si vous souhaitez continuer à utiliser l’activité AzureMLBatchScoring, poursuivez la lecture par l’intermédiaire de cette section.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Activité de notation de lot ML Azure à l’aide du stockage Azure pour les entrées/sorties 

    {
      "name": "PredictivePipeline",
      "properties": {
        "description": "use AzureML model",
        "activities": [
          {
            "name": "MLActivity",
            "type": "AzureMLBatchScoring",
            "description": "prediction analysis on batch input",
            "inputs": [
              {
                "name": "ScoringInputBlob"
              }
            ],
            "outputs": [
              {
                "name": "ScoringResultBlob"
              }
            ],
            "linkedServiceName": "MyAzureMLLinkedService",
            "policy": {
              "concurrency": 3,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            }
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }

### <a name="web-service-parameters"></a>Paramètres de Service Web
Pour spécifier des valeurs pour les paramètres de service Web, ajoutez une section **typeProperties** à la section **AzureMLBatchScoringActivty** dans le pipeline de JSON, comme illustré dans l’exemple suivant : 

    "typeProperties": {
        "webServiceParameters": {
            "Param 1": "Value 1",
            "Param 2": "Value 2"
        }
    }


Vous pouvez également utiliser les [Fonctions d’usine de données](https://msdn.microsoft.com/library/dn835056.aspx) en passant les valeurs pour les paramètres de service Web, comme illustré dans l’exemple suivant :

    "typeProperties": {
        "webServiceParameters": {
           "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
        }
    }
 
> [AZURE.NOTE] Les paramètres de service Web sont sensibles à la casse, assurez-vous que les noms que vous spécifiez dans l’activité JSON correspondent à ceux qui sont exposées par le service Web. 

## <a name="see-also"></a>Voir aussi

- [Billet de blog Azure : mise en route avec Azure Data Factory et formation de Machine Azure](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 
