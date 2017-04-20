<properties 
    pageTitle="Comment configurer des points de terminaison Azure Machine Learning dans le flux de données Analytique | Microsoft Azure" 
    description="Fonctions définies par l’utilisateur de langage machine dans le flux de données Analytique"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"
/>

# <a name="machine-learning-integration-in-stream-analytics"></a>Intégration de formation dans le flux de données Analytique de l’ordinateur

Analytique de flux prend en charge les fonctions définies par l’utilisateur, faire appel à des points de terminaison de formation de Machine Azure. Prise en charge de l’API REST de cette fonctionnalité est décrite dans la [bibliothèque d’API de flux Analytique reste](https://msdn.microsoft.com/library/azure/dn835031.aspx). Cet article fournit des informations supplémentaires nécessaires à la réussite de l’implémentation de cette fonctionnalité dans le flux de données Analytique. Un didacticiel a également été validé et est disponible [ici](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Présentation : La terminologie Azure Machine formation

Formation de Machine Microsoft Azure fournit un outil de collaboration, de glisser-déplacer que vous permet de créer, tester et déployer des solutions d’analytique prédictive sur vos données. Cet outil est appelé le *Studio d’apprentissage Azure Machine*. Le studio est utilisé pour interagir avec les ressources d’apprentissage de l’ordinateur et facilement générer, tester et effectuer une itération sur votre conception. Ces ressources ainsi que leurs définitions sont inférieurs.

- **Espace de travail**: l' *espace de travail* est un conteneur qui contient toutes les autres ressources d’apprentissage automatique dans un conteneur de contrôle et de gestion.
- **Expérience**: *expériences* sont créés par les chercheurs de données d’utiliser des groupes de données et de former un modèle d’apprentissage machine.
- **Point de terminaison**: *points de terminaison* sont l’objet d’apprentissage Machine Azure permet de tirer des fonctionnalités en tant qu’entrée, appliquer un modèle d’apprentissage machine spécifié et renvoyer un résultat évaluée.
- **Score de Webservice**: un *score webservice* est une collection de points de terminaison comme indiqué ci-dessus.

Chaque point de terminaison a une API pour l’exécution du lot et l’exécution synchrone. Flux de données Analytique utilise l’exécution synchrone. Le service spécifique est appelé un [Service de requête/réponse](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs) dans AzureML studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Apprentissage des ressources nécessaires pour les tâches de flux de données Analytique de l’ordinateur

Aux fins de flux de données Analytique de traitement, un point de terminaison de demande/réponse, une [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key)et une définition swagger sont tous nécessaires à la réussite de l’exécution. Analytique de flux a un point de terminaison supplémentaire qui crée l’url pour le point de terminaison swagger, recherche l’interface et qui retourne une définition de FDU par défaut à l’utilisateur.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configurer un flux Analytique et apprentissage UDF via l’API REST de l’ordinateur

À l’aide d’autres API, vous pouvez configurer votre projet pour appeler des fonctions de langage Machine de Azure. Les étapes sont les suivantes :

1. Créer une tâche de flux de données Analytique
2. Définir une entrée
3. Définir une sortie
4. Créer une fonction définie par l’utilisateur (UDF)
5. Écrire une transformation de flux Analytique qui appelle l’UDF
6. Démarrer le travail

## <a name="creating-a-udf-with-basic-properties"></a>Création d’un fichier UDF avec des propriétés de base

Par exemple, l’exemple de code suivant crée une FDU scalaire nommée *newudf* qui lie à un point de terminaison de formation de Machine Azure. Notez que *point de terminaison* (service URI) sont disponibles sur la page d’aide API pour le service choisi et *apiKey* sont accessibles sur la page principale de Services.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Corps de requête d’exemple :  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Appelez RetrieveDefaultDefinition le point de terminaison par défaut UDF

Une fois que le squelette de FDU est créée, la définition complète de la FDU est nécessaire. Le point de terminaison RetreiveDefaultDefinition vous permet d’obtenir la définition par défaut pour une fonction scalaire qui est liée à un point de terminaison de formation de Machine Azure. La charge utile ci-dessous vous demande obtenir la définition de la FDU par défaut pour une fonction scalaire qui est liée à un point de terminaison de formation de Machine Azure. Il ne spécifie pas le point de terminaison comme il ont déjà été fournie au cours de la demande PUT. Analytique de flux appelle le point de terminaison fournie dans la demande si elle est fournie explicitement. Sinon, il utilise la référencé à l’origine. Ici l’utilise UDF une chaîne unique paramètre (une phrase) et renvoie une seule sortie de type string qui indique l’étiquette « sentiment » de cette phrase.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Corps de requête d’exemple :  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Un exemple de sortie de ce serait voir quelque chose comme ci-dessous.  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>UDF correctif avec la réponse 

Maintenant le fichier UDF doit être appliqué avec la réponse précédente, comme indiqué ci-dessous.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Corps de la requête (sortie de RetrieveDefaultDefinition) :

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implémenter la transformation du flux Analytique pour appeler le fichier UDF

Désormais interroger la FDU (ici nommée scoreTweet) pour tous les événements d’entrée et d’écrire une réponse pour cet événement à une sortie.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
