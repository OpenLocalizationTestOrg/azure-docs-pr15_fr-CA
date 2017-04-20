<properties 
    pageTitle="Créer des définitions d’application de la logique | Microsoft Azure" 
    description="Apprenez à écrire la définition de JSON pour les applications de logique" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="author-logic-app-definitions"></a>Définitions de logique application auteur
Cette rubrique montre comment utiliser les définitions [d’Applications de logique d’Azure](app-service-logic-what-are-logic-apps.md) , qui est un langage simple et déclaratif de JSON. Si vous ne l’avez pas déjà fait, vérifiez tout d’abord comment [créer une nouvelle application logique](app-service-logic-create-a-logic-app.md) . Vous pouvez également lire le [matériau de référence complète du langage de définition sur MSDN](http://aka.ms/logicappsdocs).

## <a name="several-steps-that-repeat-over-a-list"></a>Plusieurs étapes qui se répètent sur une liste

Vous pouvez exploiter pour répéter des éléments sur un tableau de jusqu'à 10 k et effectuer une action pour chaque [type de foreach](app-service-logic-loops-and-scopes.md) .

## <a name="a-failure-handling-step-if-something-goes-wrong"></a>Une étape de gestion des erreurs, si quelque chose se passe mal

Vous souhaitez généralement pouvoir écrire une *étape de correction* , une certaine logique qui s’exécute, si **et seulement si**, un ou plusieurs de vos appels a échoué. Dans cet exemple, nous allons l’obtention de données à partir de différents emplacements, mais en cas d’échec de l’appel, je souhaite publier un message de quelque sorte imagettes cette défaillance ultérieure :  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "postToErrorMessageQueue": {
            "type": "ApiConnection",
            "inputs": "...",
            "runAfter": {
                "readData": ["Failed"]
            }
        }
    },
    "outputs": {}
}
```

Vous pouvez rendre l’utilisation de la `runAfter` propriété pour spécifier le `postToErrorMessageQueue` ne doit être exécutée `readData` est **Échec**.  Il peut s’agir également une liste des valeurs possibles, par conséquent, `runAfter` peut être `["Succeeded", "Failed"]`.

Enfin, parce que vous avez désormais géré l’erreur, nous marquer n’est plus l’exécuter comme **ayant échoué**. Comme vous pouvez le voir, cette exécution est **réussi** bien qu’une étape a échoué, parce que j’ai écrit l’étape pour gérer ce problème.

## <a name="two-or-more-steps-that-execute-in-parallel"></a>Étapes de deux (ou plus) qui s’exécutent en parallèle

Pour que l’exécution de plusieurs actions en parallèle, les `runAfter` propriété doit être équivalente à l’exécution. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "branch1": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        },
        "branch2": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        }
    },
    "outputs": {}
}
```

Comme vous pouvez le voir dans l’exemple ci-dessus, les deux `branch1` et `branch2` sont configurés pour s’exécuter après avoir `readData`. Par conséquent, les deux de ces branches seront exécutées en parallèle :

![Parallèle](./media/app-service-logic-author-definitions/parallel.png)

Vous pouvez voir que l’horodatage pour les deux branches est identique. 

## <a name="join-two-parallel-branches"></a>Joindre deux branches parallèles.

Vous pouvez joindre deux actions qui ont été définies pour s’exécuter en parallèle, en ajoutant des éléments à la `runAfter` propriété similaire à ci-dessus.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![Parallèle](./media/app-service-logic-author-definitions/join.png)

## <a name="mapping-items-in-a-list-to-some-different-configuration"></a>Mappage des éléments d’une liste à une autre configuration

Ensuite, supposons que nous voulons obtenir un contenu complètement différent en fonction d’une valeur d’une propriété. Nous pouvons créer un mappage de valeurs à des destinations en tant que paramètre :  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/en-us/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
            },
            "conditions": [{
                "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
            }],
            "forEach": "@body('getArticles').responseData.feed.entries"
        }
    }
}
```

Dans ce cas, nous avons d’abord obtenir une liste d’articles, et la deuxième étape recherche alors dans un mappage, basé sur la catégorie qui a été définie en tant que paramètre, l’URL pour obtenir le contenu à partir de. 

Faites attention ici de deux éléments : le [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) fonction est utilisée pour vérifier si la catégorie correspond à l’une des catégories connues définies. Ensuite, une fois la catégorie, nous pouvons extraire l’élément de la carte à l’aide de crochets : `parameters[...]`. 

## <a name="working-with-strings"></a>Utilisation des chaînes

Il existe de nombreuses fonctions qui peut être utilisé pour manipuler des chaînes. Prenons un exemple dans lequel nous avons une chaîne que nous souhaitons passer à un système, mais nous ne sommes pas convaincus, codage de caractères pourra être correctement géré. Une option consiste à base64 coder cette chaîne. Toutefois, afin d’éviter l’échappement dans une URL, nous allons maintenant de remplacer certains caractères. 

Nous souhaitons également une sous-chaîne de la de la commande nom car les 5 premiers caractères ne sont pas utilisés.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

Utilisation de l’intérieur :

1. Obtenir le [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) nom de l’auteur de la commande Ceci renvoie le nombre total de caractères

2. Soustraire 5 (parce que nous devrons une chaîne plus courte)

3. Prendre le [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) . Nous allons commencer à index `5` et passez le reste de la chaîne.

4. Convertir cette sous-chaîne à un [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) chaîne

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)tous les `+` avec des caractères`-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)tous les `/` avec des caractères`_`

## <a name="working-with-date-times"></a>Utilisation des dates

Dates peut être utile, en particulier lorsque vous essayez d’extraire des données à partir d’une source de données qui ne gère pas naturellement les **déclencheurs**.  Vous pouvez également utiliser des dates à déterminer combien de temps différentes étapes prennent. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "actions" {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
                },
                "runAfter": {}
            }
            "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
        }
    },
    "outputs": {}
}
```

Dans cet exemple, nous allons extraire le `startTime` de l’étape précédente. Puis nous sommes l’obtention de l’heure en cours et en soustrayant une seconde :[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (vous pouvez utiliser d’autres unités de temps telles que `minutes` ou `hours`). Enfin, nous pouvons comparer ces deux valeurs. Si la première est inférieure à la seconde, puis cela signifie que plus d’une seconde s’est écoulée depuis que l’ordre a d’abord été placés. 

Notez également que nous pouvons utiliser des formateurs de chaîne pour formater les dates : la chaîne de requête utiliser [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) pour obtenir la RFC1123. Tous les date de mise en forme [est décrite sur MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). 

## <a name="using-deployment-time-parameters-for-different-environments"></a>À l’aide des paramètres au moment du déploiement pour les différents environnements

Il est courant d’avoir un cycle de vie de déploiement où vous disposez d’un environnement de développement, un environnement de test et un environnement de production. Dans tous ces vous pouvez souhaitez que la même définition, mais utiliser des bases de données différentes, par exemple. De même, vous souhaitez utiliser la même définition dans plusieurs régions différentes pour une haute disponibilité, mais souhaitez que chaque instance d’application logique pour parler de la base de données de cette région. 

Notez que cela est différent de prendre des paramètres différents au *moment de l’exécution*, que vous devez utiliser le `trigger()` fonctionne comme indiqué ci-dessus. 

Vous pouvez démarrer avec une définition très simple tel que celui-ci :

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

Puis, dans le `PUT` demande, pour l’application de la logique, vous pouvez fournir le paramètre `uri`. Notez qu’il n’est plus une valeur par défaut de ce paramètre est obligatoire dans la charge utile app de logique :

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

Dans chaque environnement, vous pouvez ensuite fournir une valeur différente pour le `connection` paramètre. 

Consultez la [documentation de l’API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) pour toutes les options pour la création et la gestion des applications de logique. 
