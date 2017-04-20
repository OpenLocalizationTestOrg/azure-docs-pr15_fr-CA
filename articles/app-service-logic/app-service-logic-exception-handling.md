<properties
   pageTitle="Gestion des exceptions de logique applications | Microsoft Azure"
   description="Découvrez des modèles d’erreur et de la gestion des exceptions avec les applications Azure logique"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-error-and-exception-handling"></a>Erreur d’applications de logique et de la gestion des exceptions

Logique d’applications fournit un ensemble rich d’outils et des modèles pour assurer vos intégrations sont solide et fiable contre les défaillances.  Un des défis liés à l’architecture d’intégration est de s’assurer que les périodes d’indisponibilité ou de problèmes de systèmes dépendants sont gérées correctement.  Logique d’applications rend la gestion des erreurs d’une expérience de première classe, ce qui vous donne les outils que nécessaires pour agir sur les exceptions et les erreurs dans vos flux de travail.

## <a name="retry-policies"></a>Réessayer les stratégies

Le type le plus simple d’exception et de la gestion des erreurs est une stratégie de nouvelle tentative.  Cette stratégie définit si l’action doit réessayer si la demande initiale a expiré ou n’a pas pu (toute demande ayant généré un 429 ou réponse 5xx).  Par défaut, toutes les actions réessayer 4 heures supplémentaires sur des intervalles de 20 secondes.  Par conséquent, si la première demande a reçu un `500 Internal Server Error` réponse, le moteur de workflow s’interrompt pendant 20 secondes et essayez de la requête à nouveau.  Si après toutes les tentatives la réponse est toujours une exception ou une panne, le flux de travail va continuer et marquer l’état de l’action en tant que `Failed`.

Vous pouvez configurer des stratégies de nouvelle tentative dans les **entrées** d’une action particulière.  Une stratégie de nouvelle tentative peut être configurée pour essayer des intervalles jusqu'à 4 fois plus de 1 heure.  Plus d’informations sur les propriétés d’entrée peuvent être [trouvée sur MSDN][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Si vous souhaitez que votre action de HTTP pour réessayer 4 fois et attendre 10 minutes entre chaque tentative vous aurait la définition suivante :

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Pour plus d’informations sur la syntaxe prise en charge, affichez la [section stratégie de nouvelle tentative sur MSDN][retryPolicyMSDN].

## <a name="runafter-property-to-catch-failures"></a>Propriété de RunAfter pour intercepter des erreurs

Chaque action d’application logique déclare quelles actions ont besoin pour réaliser avant le démarrage de l’action.  Vous pouvez considérer cela comme l’ordre des étapes de votre workflow.  Ce classement est connu comme le `runAfter` propriété dans la définition de l’action.  Il s’agit d’un objet qui décrit les actions et les statuts d’action aurait exécuté l’action.  Par défaut, toutes les actions ajoutées via le concepteur sont la valeur `runAfter` l’étape précédente, si l’étape précédente a été `Succeeded`.  Toutefois, vous pouvez personnaliser cette valeur pour déclencher des actions lorsque les actions précédentes sont `Failed`, `Skipped`, ou un jeu possible de ces valeurs.  Si vous souhaitez ajouter un élément à une rubrique de Bus de Service désignée après une action spécifique `Insert_Row` échoue, vous utiliseriez le suivant `runAfter` configuration :

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Avis du `runAfter` propriété est définie pour se déclencher si le `Insert_Row` action est `Failed`.  Pour exécuter l’action si le statut de l’action est `Succeeded`, `Failed`, ou `Skipped` la syntaxe est :

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

>[AZURE.TIP] Les actions qui s’exécutent après une action précédente ont échoué et se terminer correctement, porte la mention `Succeeded`.  Cela signifie que si vous avec succès toutes les défaillances dans un flux de travail l’exécution proprement dite est marquée comme `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Les étendues et les résultats pour évaluer les actions

Semblable à la façon dont vous pouvez exécuter après des actions individuelles, vous pouvez également les actions de groupe conjointement à l’intérieur d’une [portée](app-service-logic-loops-and-scopes.md) - qui agissent comme un regroupement logique des actions.  Les étendues sont utiles pour organiser des actions de votre logique d’application et pour effectuer des évaluations de regroupement sur l’état d’une étendue.  Le champ d’application lui-même reçoit un état après exécution de toutes les actions au sein d’une étendue.  L’état de l’étendue est déterminé avec les mêmes critères sous la forme d’une série de tests : si l’action finale d’une branche de l’exécution est `Failed` ou `Aborted` le statut est `Failed`.

Vous pouvez `runAfter` une étendue a été marquée `Failed` pour déclencher des actions spécifiques pour tout problème qui s’est produite dans la portée.  En cours d’exécution après l’échec d’une étendue vous permet de créer une seule action pour intercepter les défaillances si l’échec de *toutes* les actions au sein de la portée.

### <a name="getting-the-context-of-failures-with-results"></a>Obtenir le contexte d’échecs de résultats

Interception des défaillances d’une application est très utile, mais vous pouvez également le contexte de comprendre exactement les actions qui a échoué et tous les erreurs ou les codes d’état qui ont été retournés.  Le `@result()` fonction de workflow fournit un contexte dans le résultat de toutes les actions au sein d’une étendue.

`@result()`prend un seul paramètre, le nom de l’étendue et retourne un tableau de tous les résultats d’action à partir de dans cette portée.  Ces objets d’action comprennent les mêmes attributs que la `@actions()` sort de l’objet, y compris l’action heure de début, heure fin de l’action, état de l’action, entrées d’action, ID de corrélation action et action.  Vous pouvez facilement associer un `@result()` fonctionne avec une `runAfter` pour envoyer les contexte de toutes les actions qui ont échoué dans une portée.

Si vous souhaitez exécuter une action *pour chaque* action d’une portée qui `Failed`, vous pouvez apparier `@result()` avec une action de **[Filtre de tableau](../connectors/connectors-native-query.md)** et d’une boucle **[ForEach](app-service-logic-loops-and-scopes.md)** .  Cela vous permet de filtrer le tableau des résultats pour les actions qui ont échoué.  Vous pouvez prendre le tableau des résultats filtrés et effectuer une action pour chaque échec à l’aide de la boucle **ForEach** .  Voici un exemple ci-dessous, suivi d’une explication détaillée.  Cet exemple envoie une demande HTTP POST avec le corps de la réponse de toutes les actions qui ont échoué dans la portée `My_Scope`.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Voici une description détaillée de ce qui se passe :

1. Action du **Tableau de filtre** pour filtrer le `@result('My_Scope')` pour obtenir le résultat de toutes les actions dans`My_Scope`
1. Condition du **Tableau de filtres** est un `@result()` élément dont l’état est égal à `Failed`.  Cela permettra de filtrer le tableau de tous les résultats de l’action à partir de `My_Scope` pour seulement un tableau de résultats de l’action qui a échoué.
1. Effectuer une action **pour chaque** sur les sorties de **Filtrer le tableau** .  Cela va effectuer un action *pour chaque* échoué de résultat de l’action nous filtrés ci-dessus.
    - S’il y a une action unique dans la portée qui ont échoué, les actions de la `foreach` ne s’exécute qu’une seule fois.  De nombreuses actions d’échecs risque de provoquer une action par erreur.
1. Envoyer un HTTP POST le `foreach` élément de corps de réponse, ou `@item()['outputs']['body']`.  Le `@result()` forme d’élément est le même que le `@actions()` de forme et peut être analysée de la même manière.
1. Également inclus deux en-têtes personnalisés avec le nom de l’action qui a échoué `@item()['name']` et l’échec d’exécute client ID de suivi `@item()['clientTrackingId']`.

Pour référence, Voici un exemple d’un seul `@result()` élément.  Vous pouvez voir les `name`, `body`, et `clientTrackingId` propriétés analysées dans l’exemple ci-dessus.  Il convient de noter qu’en dehors d’une `foreach`, `@result()` retourne un tableau de ces objets.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/foo/bar does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Vous pouvez utiliser les expressions ci-dessus pour effectuer différentes de modèles de gestion des exceptions.  Vous pouvez choisir d’exécuter une seule exception Gestion des actions en dehors de la portée qui accepte le tableau entier filtré de pannes et de supprimer les `foreach`.  Vous pouvez également inclure d’autres propriétés utiles à partir de la `@result()` réponse ci-dessus.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnostics de Windows Azure et télémétrie

Les modèles ci-dessus sont très utile pour gérer les erreurs et les exceptions au sein d’une série de tests, mais vous pouvez également identifier et répondre aux erreurs indépendantes de l’exécution proprement dite.  [Les tests de diagnostic Azure](app-service-logic-monitor-your-logic-apps.md) offre un moyen simple d’envoyer tous les événements de flux de travail (y compris tous les statuts d’exécuter et d’action) à un compte de stockage Azure ou à un concentrateur d’événements Azure.  Vous pouvez surveiller les journaux et les mesures ou les publier dans n’importe quel outil de surveillance que vous préférez, pour évaluer les statuts de l’exécution.  Une option de potentielle consiste à diffuser tous les événements par le biais de concentrateur d’événements Azure dans le [Flux de données Analytique](https://azure.microsoft.com/services/stream-analytics/).  Dans le flux de données Analytique, vous pouvez écrire des requêtes live sur des anomalies, moyennes ou des défaillances dans les journaux de diagnostic.  Analytique de flux peut sortir facilement à d’autres sources de données telles que les files d’attente, rubriques, SQL, DocumentDB et alimentation BI.

## <a name="next-steps"></a>Étapes suivantes
- [Voir comment un client créé avec des applications de la logique de gestion d’erreur robuste](app-service-logic-scenario-error-and-exception-handling.md)
- [Trouver plus d’exemples d’applications de logique et scénarios](app-service-logic-examples-and-scenarios.md)
- [Apprenez à créer des déploiements automatisés des applications de logique](app-service-logic-create-deploy-template.md)
- [Concevoir et déployer des applications de logique à partir de Visual Studio](app-service-logic-deploy-from-vs.md)


<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9