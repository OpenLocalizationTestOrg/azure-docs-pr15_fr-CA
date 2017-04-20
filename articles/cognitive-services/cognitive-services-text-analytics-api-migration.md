<properties
    pageTitle="Mise à niveau vers la Version 2 de l’API d’Analytique texte | Microsoft Azure"
    description="Apprentissage de texte Analytique - mise à niveau vers la Version 2 de l’ordinateur Azure"
    services="cognitive-services"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>

# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>Mise à niveau vers la Version 2 de l’Analytique de texte API #

Ce guide vous guidera tout au long du processus de mise à niveau de votre code d’utiliser la [première version de l’API](../machine-learning/machine-learning-apps-text-analytics.md) à l’aide de la deuxième version. 

Si vous n’avez pas utilisé l’API et que vous souhaitez en savoir plus, vous pouvez **[en savoir plus sur l’API ici](//go.microsoft.com/fwlink/?LinkID=759711)** ou **[Suivez le Guide de démarrage rapide](//go.microsoft.com/fwlink/?LinkID=760860)**. Pour des informations techniques de référence, reportez-vous à la **[Définition de l’API](//go.microsoft.com/fwlink/?LinkID=759346)**.

### <a name="part-1-get-a-new-key"></a>Partie 1. Obtenir une nouvelle clé ###

Tout d’abord, vous devez obtenir une nouvelle clé d’API à partir du **Portail Azure**:

1. Accédez au service par le biais de la [Galerie d’Intelligence Cortana](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2)Analytique du texte. Ici, vous trouverez également des liens vers les exemples de code et de documentation.

1. Cliquez sur **inscription**. Ce lien vous conduira vers le portail de gestion Azure, où vous pouvez vous inscrire pour le service.

1. Sélectionnez un plan. Vous pouvez sélectionner la **couche libre pour 5 000 transactions par mois**. Comme un plan libre, vous ne sera pas débitée pour utiliser le service. Vous devez ouvrir une session sur votre abonnement Azure. 

1. Une fois que vous vous inscrivez pour texte Analytique, vous aurez une **Clé d’API**. Copiez cette clé, car vous en aurez besoin lorsque vous utilisez les services de l’API.

### <a name="part-2-update-the-headers"></a>Partie 2. Mettre à jour des en-têtes ###

Mettre à jour les valeurs d’en-tête envoyé comme indiqué ci-dessous. Notez que la clé du compte est n’est plus codée.

**Version 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Version 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>Partie 3. Mise à jour de l’URL de base ###

**Version 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Version 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>4 de la partie. Les formats pour les langues, les phrases clés et sentiment de mise à jour ###

#### <a name="endpoints"></a>Points de terminaison ####

OBTENIR des points de terminaison sont désormais déconseillées, afin que toutes les entrées doivent être transmises sous la forme d’une demande POST. Mettre à jour les points de terminaison à celles indiquées ci-dessous.

| |Point de terminaison unique version 1|Point de terminaison de lot version 1|Point de terminaison version 2|
|---|---|---|---|
|Type d’appel|Télécharger|Publier|Publier|
|Sentiment|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|Expressions clés|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|Traduction|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### <a name="input-formats"></a>Formats d’entrée ####

Notez que seul format de publication est désormais accepté, afin de vous devez remettre en forme n’importe quelle entrée qui utilisait les points de terminaison du document unique en conséquence. Les entrées ne respectent pas la casse.

**Version 1 (lot)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Version 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>Sortie de sentiment ####

**Version 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>Sortie à partir des expressions clés ####

**Version 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>Sortie à partir de langages ####


**Version 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>4 b de partie. Les formats des rubriques de mise à jour ###

#### <a name="endpoints"></a>Points de terminaison ####

| |Point de terminaison version 1 | Point de terminaison version 2|
|---|---|---|
|Soumettre pour la détection de rubrique (de démarrage POST)|```StartTopicDetection```|```topics```|
|Résultats de rubrique d’extraction (GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### <a name="input-formats"></a>Formats d’entrée ####

**Version 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Version 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>Résultats de l’envoi ####

**Version 1 (POST)**

Auparavant, la tâche est terminée, vous recevez la sortie suivante de JSON, où le jobId serait ajouté à une URL pour extraire le résultat.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Version 2 (POST)**

La réponse contiendront une valeur d’en-tête comme suit, où `operation-location` est utilisée comme point de terminaison à interroger pour les résultats :

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>Résultats de l’opération ####

**Version 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2 (GET)**

Comme avant, **interroger périodiquement la sortie** (la période suggérée est la minute) jusqu'à ce que la sortie est renvoyée. 

Lorsque l’API rubriques est terminé, une lecture de l’état `succeeded` sera retourné. Cela inclura ensuite les résultats dans le format illustré ci-dessous :

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>Partie 5. Testez-le ! ###

Vous devez maintenant être fin prêt ! Testez votre code avec un petit échantillon pour vous assurer que vous pouvez traiter vos données.
