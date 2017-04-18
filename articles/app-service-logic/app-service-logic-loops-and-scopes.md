<properties
   pageTitle="Applications de logique boucles, étendues et Debatching | Microsoft Azure"
   description="Boucle d’application logique, la portée et concepts de debatching"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# <a name="logic-apps-loops-scopes-and-debatching"></a>Applications de logique boucles, étendues et Debatching
  
>[AZURE.NOTE] Cette version de l’article s’applique au schéma de logique Apps 2016-04-01-aperçu et les versions ultérieures.  Concepts sont similaires pour les schémas antérieurs, mais étendues sont uniquement disponibles pour ce schéma et versions ultérieures.
  
## <a name="foreach-loop-and-arrays"></a>Boucle ForEach et les baies
  
Logique d’applications vous permet une boucle sur un ensemble de données et exécuter une action pour chaque élément.  Ceci est possible via la `foreach` action.  Dans le concepteur, vous pouvez spécifier pour ajouter une boucle for each.  Après avoir sélectionné le tableau que vous souhaitez parcourir, vous pouvez commencer à Ajout d’actions.  Actuellement, vous êtes limité à une seule action par boucle foreach, mais cette restriction est levée dans les semaines à venir.  Une fois dans la boucle, vous pouvez commencer à spécifier ce qui doit se produire à chaque valeur du tableau.

Si vous utilisez le mode de code, vous pouvez spécifier une boucle for each comme ci-dessous.  Il s’agit d’un exemple d’une boucle for each qui envoie un e-mail pour chaque adresse de messagerie qui contient « microsoft.com » :

```
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A `foreach` action peut itérer sur les tableaux jusqu'à 5 000 lignes.  Chaque itération peut exécuter en parallèle, il peut être nécessaire d’ajouter des messages à une file d’attente si le contrôle de flux est nécessaire.
  
## <a name="until-loop"></a>Jusqu'à ce que la boucle
  
  Vous pouvez effectuer une action ou une série d’actions jusqu'à ce qu’une condition est remplie.  Le scénario le plus courant pour ce appelle un point de terminaison jusqu'à ce que vous obteniez la réponse que vous recherchez.  Dans le concepteur, vous pouvez spécifier, pour ajouter un jusqu'à ce que la boucle.  Après l’ajout d’actions à l’intérieur de la boucle, vous pouvez définir la condition de sortie, ainsi que la boucle de limites.  Il existe un délai de 1 minute entre les cycles de boucle.
  
  Si vous utilisez le mode de code, vous pouvez spécifier un jusqu'à ce que la boucle comme ci-dessous.  Il s’agit d’un exemple de l’appel d’un point de terminaison HTTP jusqu'à ce que le corps de la réponse a la valeur « Terminés ».  Il s’achève lorsque deux 
  
  * Réponse HTTP a le statut « Terminé »
  * Elle a essayé pendant 1 heure
  * Il a effectué une boucle de 100 fois
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn et Debatching

Parfois, un déclencheur peut recevoir un tableau d’éléments que vous voulez debatch et démarrer un flux de travail par article.  Ceci peut être accompli la `spliton` commande.  Par défaut, si votre swagger déclencheur spécifie une charge utile qui est un tableau, une `spliton` sera ajoutée et lancer une exécution par article.  SplitOn peut uniquement être ajouté à un déclencheur.  Cela peut être manuellement configuré ou substituée dans la définition de la vue code.  Actuellement SplitOn pouvez debatch tableaux jusqu'à 5 000 éléments.  Vous ne pouvez pas avoir un `spliton` et également implémenter le modèle de réponse synchrone.  Tout workflow appelé qui a un `response` action en plus de `spliton` s’exécuter de façon asynchrone et envoyer une immédiate `202 Accepted` réponse.  

SplitOn peut être spécifié dans la vue code, comme dans l’exemple suivant.  Ce reçoit un tableau d’éléments et debatches sur chaque ligne.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequency": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Étendues

Il est possible de grouper une série d’actions à l’aide d’une étendue.  Ceci est particulièrement utile pour la mise en œuvre de la gestion des exceptions.  Dans le concepteur, vous pouvez ajouter une nouvelle étendue et commencer à ajouter des actions à l’intérieur.  Vous pouvez définir des étendues en mode de code comme suit :


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
