<properties
    pageTitle="API d’apprentissage machine : Texte Analytique | Microsoft Azure"
    description="API d’Analytique de Microsoft Machine Learning texte peuvent être utilisées pour analyser le texte non structuré pour analyse de sentiment, extraction de phrase clé, détection de la langue et détection de la rubrique."
    services="machine-learning"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/> 

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>


# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>API de machine d’apprentissage : Analytique du texte pour le Sentiment, clé d’Extraction d’une Phrase, de la détection de langue et de détection de la rubrique

>[AZURE.NOTE] Ce guide est pour la version 1 de l’API. Pour la version 2, [**reportez-vous à ce document**](../cognitive-services/cognitive-services-text-analytics-quick-start.md). Version 2 est désormais la version par défaut de cette API.

## <a name="overview"></a>Vue d’ensemble

L’API d’Analytique de texte est une suite de texte analytique [services web](https://datamarket.azure.com/dataset/amla/text-analytics) générés avec formation de Machine Azure. L’API peut être utilisée pour analyser le texte non structuré pour des tâches telles que l’analyse de sentiment, extraction de phrase clé, détection de la langue et détection de la rubrique. Aucune donnée de formation n’est nécessaire pour utiliser cette interface API : simplement mettre vos données de texte. Cette API utilise langage avancée techniques de traitement à optimale dans les prévisions de la classe.

Vous pouvez voir analytique du texte en action sur notre [site de démonstration](https://text-analytics-demo.azurewebsites.net/), où vous trouverez également [exemples](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) d’implémentation d’analytique de texte dans C# et Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

---

## <a name="sentiment-analysis"></a>Analyse de sentiment

L’API retourne une notation numérique entre 0 et 1. Scores proche de 1 indiquent le sentiment positif, alors que les scores proche de 0 sentiment négatif. Score de sentiment est généré à l’aide de techniques de classification. Les fonctionnalités d’entrée au classifieur incluent de n-grammes, fonctions générées à partir de la partie du discours balises et incorporations de word. Actuellement, l’anglais est le seul langage pris en charge.
 
## <a name="key-phrase-extraction"></a>Extraction de la phrase clé

L’API retourne une liste de chaînes qui dénote les principaux points de discussion dans le texte d’entrée. Nous utilisons des techniques à partir des outils de traitement de langage naturel sophistiqués de Microsoft Office. Actuellement, l’anglais est le seul langage pris en charge.

## <a name="language-detection"></a>Détection de la langue

L’API retourne la langue détectée et une notation numérique entre 0 et 1. Scores proche de 1 indiquent 100 % de certitude que la langue identifiée est true. Un total de 120 langues sont prises en charge.

## <a name="topic-detection"></a>Détection de la rubrique

Il s’agit d’une API qui vient d’être lancée qui retourne haut a détecté des rubriques pour une liste des soumis des enregistrements texte. Une rubrique est identifiée avec une phrase clé, ce qui peut être une ou plusieurs des mots. Cette API requiert un minimum de 100 enregistrements texte à présenter, mais est conçu pour détecter des rubriques sur des centaines de milliers d’enregistrements. Notez que cette API frais 1 transaction par enregistrement de texte soumis. L’API est conçu pour fonctionner parfaitement pour un texte écrit court, humain, tels que les révisions et les commentaires des utilisateurs.

---

## <a name="api-definition"></a>Définition de l’API

### <a name="headers"></a>En-têtes

Veillez à inclure les en-têtes appropriés dans votre demande, qui doit être comme suit :

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Vous trouverez votre clé de compte à partir de votre compte, sur le [Marché de données Azure](https://datamarket.azure.com/account/keys). Notez qu’actuellement seul JSON est acceptée pour les formats d’entrée et de sortie. XML n’est pas pris en charge.

---

## <a name="single-response-apis"></a>API de réponse unique

### <a name="getsentiment"></a>GetSentiment

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Exemple de demande**

Dans l’appel ci-dessous, nous invitons analyse sentiment de la phrase « Hello World » :

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Ceci renverra une réponse comme suit :

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
        "Score":1.0
    }

---

### <a name="getkeyphrases"></a>GetKeyPhrases

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Exemple de demande**

Dans l’appel ci-dessous, nous invitons que les phrases clés trouvent dans le texte « Il était un hôtel merveilleux afin de rester à, avec décor unique et convivial » :

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
    Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Ceci renverra une réponse comme suit :

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
      "KeyPhrases":[
        "wonderful hotel",
        "unique decor",
        "friendly staff"
      ]
    }
 
---

### <a name="getlanguage"></a>GetLanguage

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Exemple de demande**

Dans l’appel GET ci-dessous, nous invitons pour le sentiment d’expressions clés dans le texte *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
    Text=Hello+World

Ceci renverra une réponse comme suit :

    {
      "UnknownLanguage": false,
      "DetectedLanguages": [{
        "Name": "English",
        "Iso6391Name": "en",
        "Score": 1.0
      }]
    }

**Paramètres facultatifs**

`NumberOfLanguagesToDetect`est un paramètre facultatif. La valeur par défaut est 1.

---

## <a name="batch-apis"></a>API de traitement par lots

Le service de texte Analytique vous permet de faire sentiment et la phrase clé des extractions en mode batch. Notez que chacun des enregistrements marqués nombre comme une transaction unique. Par exemple, si vous demandez le sentiment de 1000 enregistrements en un seul appel, 1000 transactions seront déduites.

Notez que les numéros entrés dans le système sont les ID renvoyés par le système. Le service web ne vérifie pas que ces identificateurs sont uniques. Il est de la responsabilité de l’appelant pour vérifier l’unicité. 


### <a name="getsentimentbatch"></a>GetSentimentBatch

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Exemple de demande**

Dans l’appel POST ci-dessous, nous invitons les éléments des expressions « Hello World », « Foo Bonjour » et « Hello mon monde » dans le corps de la demande :

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Corps de la demande :

    {"Inputs":
    [
        {"Id":"1","Text":"hello world"},
        {"Id":"2","Text":"hello foo world"},
        {"Id":"3","Text":"hello my world"},
    ]}

Dans la réponse ci-dessous, vous obtenez la liste des notes associées à votre texte ID :

    {
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


---

### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Exemple de demande**

Dans cet exemple, nous invitons pour la liste des éléments pour des expressions clés dans les textes suivants : 

* « Il était un hôtel merveilleux afin de rester à, avec décor unique et convivial »
* « Il était une conférence build incroyable, avec très intéressant parle »
* « Le trafic a été terrible, j’ai passé trois heures à l’aéroport »

Cette demande est effectuée sous la forme d’un appel POST sur le point de terminaison :

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Corps de la demande :

    {"Inputs":
    [
        {"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
        {"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
        {"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
    ]}

Dans la réponse ci-dessous, vous obtenez la liste des expressions clés associées à votre texte ID :

    { "odata.metadata":"<url>",
        "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

---

### GetLanguageBatch

In the POST call below, we are requesting language detection for two text inputs:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Request body:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

This returns the following response, where English is detected in the first input and French in the second input:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "Anglais",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "Français",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## <a name="topic-detection-apis"></a>API de détection de rubrique

Il s’agit d’une API qui vient d’être lancée qui retourne haut a détecté des rubriques pour une liste des soumis des enregistrements texte. Une rubrique est identifiée avec une phrase clé, ce qui peut être une ou plusieurs des mots. Notez que cette API frais 1 transaction par enregistrement de texte soumis.

Cette API requiert un minimum de 100 enregistrements texte à présenter, mais est conçu pour détecter des rubriques sur des centaines de milliers d’enregistrements.


### <a name="topics--submit-job"></a>Rubriques – travail d’envoi

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Exemple de demande**


Dans l’appel POST ci-dessous, nous invitons les rubriques pour un ensemble de plus de 100 articles, où les premier et derniers des articles d’entrée sont affichés, et deux StopPhrases sont inclus.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Corps de la demande :

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

Dans la réponse ci-dessous, vous obtenez l’ID de travail pour le travail soumis :

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

Une liste d’un seul mot ou plusieurs phrases de word qui ne doivent pas être retournés en tant que des rubriques. Peut être utilisé pour filtrer les rubriques très génériques. Par exemple, dans un groupe de données sur les révisions de l’hôtel, « hôtel » et « hostel » est peut-être phrases d’arrêt cohérent.  

### <a name="topics--poll-for-job-results"></a>Rubriques – sondage pour les résultats des travaux

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Exemple de demande**

Passez le JobId retourné à partir de l’étape 'Envoyer job' pour extraire les résultats. Nous vous conseillons de contacter ce point de terminaison chaque minute jusqu'à ce que le statut = « Complète » dans la réponse. Prendra environ 10 minutes pour une tâche complète, ou plus de temps pour les projets avec plusieurs milliers d’enregistrements.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Pendant le traitement, la réponse sera comme suit :

    {
        "odata.metadata":"<url>",
        "Status":"Running",
        "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


L’API renvoie le résultat au format JSON dans le format suivant :

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
        ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


Les propriétés pour chaque partie de la réponse sont les suivants :

**Propriétés de TopicInfo**

| Clé | Description |
|:-----|:----|
| IDRubrique | Un identificateur unique pour chaque rubrique. |
| Score de | Nombre d’enregistrements affectés à une rubrique. |
| KeyPhrase | Un mot ou une expression pour la rubrique synthèse. Peut être 1 ou plusieurs mots. |

**Propriétés de TopicAssignment**

| Clé | Description |
|:-----|:----|
| ID | Identificateur de l’enregistrement. Correspond à l’ID fourni dans l’entrée. |
| IDRubrique | L’ID de la rubrique dont l’enregistrement a été affecté. |
| Distance | Confiance que l’enregistrement appartient à la rubrique. Distance plus proche de zéro indique une confiance plus élevée. |
