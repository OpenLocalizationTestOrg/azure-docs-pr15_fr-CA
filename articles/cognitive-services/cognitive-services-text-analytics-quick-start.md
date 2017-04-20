<properties
    pageTitle="Guide de démarrage rapide : API de Machine d’apprentissage texte Analytique | Microsoft Azure"
    description="Machine Azure texte Analytique - Guide de démarrage rapide de la formation"
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

# <a name="getting-started-with-the-text-analytics-apis-to-detect-sentiment-key-phrases-topics-and-language"></a>Mise en route avec les API d’Analytique de texte pour détecter le sentiment, expressions clés, rubriques et langue

<a name="HOLTop"></a>

Ce document décrit comment intégrée pour votre service ou votre application d’utiliser les [API d’Analytique de texte](//go.microsoft.com/fwlink/?LinkID=759711).
Vous pouvez utiliser ces API pour détecter le sentiment, expressions clés, rubriques et la langue du texte de votre. [Cliquez ici pour voir une démonstration interactive de l’expérience.](//go.microsoft.com/fwlink/?LinkID=759712)

Consultez les [définitions des API](//go.microsoft.com/fwlink/?LinkID=759346) pour la documentation technique relative à l’API.

Ce guide concerne la version 2 de l’API. Pour plus d’informations sur la version 1 de l’API, [reportez-vous à ce document](../machine-learning/machine-learning-apps-text-analytics.md).

À la fin de ce didacticiel, vous serez en mesure de détecter par programmation :

- **Sentiment** - est texte, positive ou négative ?

- **Expressions de clé** - quelles sont les personnes aborder dans un seul article ?

- **Rubriques** - quelles sont les personnes traitant de nombreux articles ?

- **Langues** - langue est un texte écrit ?

Notez que cette API frais 1 transaction par document soumis. Par exemple, si vous demandez le sentiment de 1000 des documents dans un seul appel, 1000 transactions seront déduites.



<a name="Overview"></a>
## <a name="general-overview"></a>Vue d’ensemble ##

Ce document est un guide pas à pas. Notre objectif est de vous guider à travers les étapes nécessaires pour former un modèle et vous oriente vers des ressources qui vous permettra de mettre en production. Cet exercice prend environ 30 minutes.

Pour ces tâches, vous avez besoin d’un éditeur et appeler les points de terminaison RESTful dans la langue de votre choix.

Allons-y !

## <a name="task-1---signing-up-for-the-text-analytics-apis"></a>Tâche 1 : signature pour les API d’Analytique de texte ####

Dans cette tâche, vous s’inscrire pour le service d’analytique de texte.

1. Accédez à **Services cognitifs** dans [Azure Portal](//go.microsoft.com/fwlink/?LinkId=761108) et vérifiez **Que Analytique du texte** est sélectionné en tant que type l’API.

1. Sélectionnez un plan. Vous pouvez sélectionner la **couche libre pour 5 000 transactions par mois**. Comme un plan libre, vous ne sera pas débitée pour utiliser le service. Vous devez ouvrir une session sur votre abonnement Azure. 

1. Remplissez les autres champs et créer votre compte.

1. Une fois que vous vous inscrivez pour texte Analytique, trouver votre **Clé d’API**. Copiez la clé primaire, car vous en aurez besoin lorsque vous utilisez les services de l’API.


## <a name="task-2---detect-sentiment-key-phrases-and-languages"></a>Tâche 2 - détecter sentiment, des langues et des expressions clés ####

Il est facile de détecter le sentiment, des langues et des expressions clés dans votre texte. Vous obtiendrez par programmation les mêmes résultats que l' [expérience de démonstration](//go.microsoft.com/fwlink/?LinkID=759712) retourne.

>[AZURE.TIP] Pour l’analyse de sentiment, il est recommandé de fractionner le texte en phrases. Cela entraîne généralement une précision plus élevée dans les prévisions de sentiment.

Notez que les langues prises en charge sont les suivantes :

| Fonctionnalité | Codes de langue pris en charge |
|:-----|:----|
| Sentiment | `en`(En anglais), `es` (espagnol), `fr` (Français), `pt` (portugais) |
| Expressions clés | `en`(En anglais), `es` (espagnol), `de` (allemand), `ja` (japonais) |


1. Vous devez définir les en-têtes pour les éléments suivants. Notez que JSON est actuellement le seul format accepté d’entrée pour les API. XML n’est pas pris en charge.

        Ocp-Apim-Subscription-Key: <your API key>
        Content-Type: application/json
        Accept: application/json

1. Ensuite, mettre en forme les lignes d’entrée au format JSON. Pour le sentiment, expressions clés et la langue, le format est le même. Notez que chaque ID doit être unique et sera l’ID retourné par le système. La taille maximale d’un document unique pouvant être envoyé est de 10 Ko et la taille totale maximale de l’entrée soumise est de 1 Mo. Pas plus de 1 000 documents peuvent être présentées dans un seul appel. Limitation du débit existe à un débit de 100 appels par minute - Nous vous recommandons par conséquent de soumettre les grandes quantités de documents dans un seul appel. Langue est un paramètre facultatif qui doit être spécifié si l’analyse de caractères non-ASCII. Voici un exemple d’entrée ci-dessous, où le paramètre facultatif `language` pour le sentiment analyse ou clé extraction de phrase est incluse :

        {
            "documents": [
                {
                    "language": "en",
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "language": "en",
                    "id": "100",
                    "text": "Final document"
                }
            ]
        }

1. Effectuer un appel **POST** sur le système avec l’entrée pour des expressions clés, sentiment et la langue. Les URL seront présente comme suit :

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. Cet appel renverra un JSON réponse avec les codes de mise en forme et propriétés détectées. Voici un exemple de la sortie de sentiment ci-dessous (avec les détails de l’erreur exclus). Dans le cas de sentiment, un score entre 0 et 1 est retourné pour chaque document :

        // Sentiment response
        {
            "documents": [
                {
                    "id": "1",
                    "score": "0.934"
                },
                ...
                {
                    "id": "100",
                    "score": "0.002"
                },
            ]
        }

        // Key phrases response
        {
            "documents": [
                {
                    "id": "1",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
                ...
                {
                    "id": "100",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
            ]
        }

        // Languages response
        {
            "documents": [
                {
                    "id": "1",
                    "detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
                },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
                }
            ]
        }


## <a name="task-3---detect-topics-in-a-corpus-of-text"></a>Tâche 3 - détecter les rubriques dans un corpus de texte ####

Il s’agit d’une API qui vient d’être lancée qui retourne haut a détecté des rubriques pour une liste des soumis des enregistrements texte. Une rubrique est identifiée avec une phrase clé, ce qui peut être une ou plusieurs des mots. L’API est conçu pour fonctionner parfaitement pour un texte écrit court, humain, tels que les révisions et les commentaires des utilisateurs.

Cette API requiert **un minimum de 100 enregistrements texte** à présenter, mais est conçu pour détecter des rubriques sur des centaines de milliers d’enregistrements. Tous les enregistrements d’autres que l’anglais ou les enregistrements avec des mots de moins de 3 seront ignorés et ne seront donc pas attribués aux sujets. Pour la détection de la rubrique, la taille maximale d’un document unique pouvant être envoyé est 30 Ko et la taille totale maximale de l’entrée soumise est 30 Mo. Détection de rubrique est limitée à 5 envois toutes les 5 minutes de taux.

Il existe deux paramètres d’entrée supplémentaire **facultatif** qui peuvent d’améliorer la qualité des résultats :

- **Mots vides.**  Ces mots et leurs formulaires Fermer (par exemple pluriels) seront exclus du pipeline de détection d’ensemble de la rubrique. Utilisez cette option pour les mots courants (par exemple, « problème », « erreur » et « utilisateur » peuvent être un choix appropriés pour la réclamation d’un client sur les logiciels). Chaque chaîne doit être un seul mot.
- **Arrêter les phrases** - ces phrases seront exclus de la liste des rubriques retournées. Utilisez cette option pour exclure les rubriques génériques que vous ne voulez pas voir dans les résultats. Par exemple, « Microsoft » et « Azure » serait les choix appropriés pour les rubriques à exclure. Les chaînes peuvent contenir plusieurs mots.

Suivez ces étapes pour détecter des rubriques dans votre texte.

1. Mettre en forme l’entrée au format JSON. Cette fois, vous pouvez définir des mots et arrêter les phrases.

        {
            "documents": [
                {
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "id": "100",
                    "text": "Final document"
                }
            ],
            "stopWords": [
                "issue", "error", "user"
            ],
            "stopPhrases": [
                "Microsoft", "Azure"
            ]
        }

1. En utilisant les mêmes en-têtes tel que défini dans la tâche 2, appeler un **POST** sur le point de terminaison de rubriques :

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. Ceci renverra un `operation-location` sous l’en-tête dans la réponse, où la valeur est l’URL de requête pour les rubriques qui en résulte :

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. Requête retourné `operation-location` régulièrement avec une requête **GET** . Une fois par minute est recommandée.

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. Le point de terminaison renvoie une réponse, y compris `{"status": "notstarted"}` avant traitement, `{"status": "running"}` lors du traitement et de `{"status": "succeeded"}` à la sortie une fois terminée. Vous pouvez utiliser ensuite de la sortie qui sera au format suivant (note les détails tels que les dates et le format d’erreur ont été exclus de cet exemple) :

        {
            "status": "succeeded",
            "operationProcessingResult": {
                "topics": [
                    {
                        "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
                        "score": "5"
                        "keyPhrase": "first topic name"
                    },
                    ...
                    {
                        "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
                        "score": "3"
                        "keyPhrase": "final topic name"
                    }
                ],
                "topicAssignments": [
                    {
                        "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
                        "documentId": "1",
                        "distance": "0.354"
                    },
                    ...
                    {
                        "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
                        "documentId": "55",
                        "distance": "0.758"
                    },            
                ]
            }
        }

Notez que la réponse correcte des rubriques à partir de la `operations` point de terminaison aura le schéma suivant :

    {
            "topics" : [{
                "id" : "string",
                "score" : "number",
                "keyPhrase" : "string"
            }],
            "topicAssignments" : [{
                "documentId" : "string",
                "topicId" : "string",
                "distance" : "number"
            }],
            "errors" : [{
                "id" : "string",
                "message" : "string"
            }]
        }

Des explications sur chaque partie de cette réponse sont les suivants :

**rubriques**

| Clé | Description |
|:-----|:----|
| ID | Un identificateur unique pour chaque rubrique. |
| score de | Nombre de documents affectés à une rubrique. |
| keyPhrase | Un mot ou une expression pour la rubrique synthèse. |

**topicAssignments**

| Clé | Description |
|:-----|:----|
| documentId | Identificateur pour le document. Correspond à l’ID fourni dans l’entrée. |
| IDRubrique | Identifiant de la rubrique à laquelle le document a été affecté à. |
| distance | Score de document-rubrique affiliation entre 0 et 1. Bas à une distance de score est de l’affiliation de la rubrique est renforcée. |

**erreurs**

| Clé | Description |
|:-----|:----|
| ID | Identificateur unique du document d’entrée l’erreur fait référence à. |
| Message | Message d’erreur. |

## <a name="next-steps"></a>Étapes suivantes ##

Félicitations ! Vous avez maintenant terminé à l’aide d’analytique de texte sur les données. Vous pouvez souhaiter maintenant examiner à l’aide d’un outil tel que [BI de puissance](//powerbi.microsoft.com) à visualiser vos données, ainsi que l’automatisation de vos idées pour vous donner une vue en temps réel de vos données de texte.

Pour voir comment les capacités Analytique du texte, par exemple sentiment, peuvent être utilisées dans le cadre d’un robot, consultez l’exemple [Bot émotionnelle](http://docs.botframework.com/en-us/bot-intelligence/language/#example-emotional-bot) sur le site de Bot Framework.
