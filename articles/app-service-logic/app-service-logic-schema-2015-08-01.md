<properties 
    pageTitle="Nouveau schéma version 2015-08-01-aperçu" 
    description="Apprenez à écrire la définition de JSON de la dernière version des applications de logique" 
    authors="stepsic-microsoft-com" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>
    
# <a name="new-schema-version-2015-08-01-preview"></a>Nouveau schéma version 2015-08-01-aperçu

Le nouveau schéma et une API de version pour les applications de logique a un certain nombre d’améliorations qui améliorent la fiabilité et la facilité d’utilisation des applications de logique. Il existe des différences clés 4 :

1. Le type d’action **APIApp** a été mis à jour à un nouveau type d’action **APIConnection** .
2. **Répétez** a été renommé **Foreach**.
3. L’application d’API **Écouteur HTTP** n’est plus nécessaire.
4. Appel de flux de travail enfant utilise un nouveau schéma.

## <a name="1-moving-to-api-connections"></a>1. le déplacement de connexions d’API

La plus importante modification est que vous n’avez plus besoin déployer des applications de l’API dans votre abonnement Azure à utiliser l’API. Il existe 2 façons d’utiliser des API :
* L’API managée
* L’API de votre Web personnalisé

Chacun de ces est géré différemment car leur gestion et modèles d’hébergement sont différents. L’un des avantages de ce modèle sont que vous utilisez n’est plus limité aux ressources qui sont déployés dans votre groupe de ressources. 

### <a name="managed-apis"></a>API managé

Il existe un certain nombre d’API qui est gérés par Microsoft à votre place, telles que Office 365, force de vente, Twitter, FTP etc.... Certaines de ces API managée peuvent être utilisé en tant que-est, par exemple de Bing Translation, tandis que d’autres nécessitent la configuration. Cette configuration est appelée *connexion*.

Par exemple, lorsque vous utilisez Office 365, vous devez créer une connexion qui contient votre jeton de connexion à Office 365. Ce jeton est stocké en toute sécurité et actualisé afin que votre application logique peut appeler toujours de l’API de 365 Office. Ou bien, si vous souhaitez vous connecter à votre serveur SQL ou FTP, vous devez créer une connexion avec la chaîne de connexion. 

À l’intérieur de la définition de ces actions sont appelées `APIConnection`. Voici un exemple d’une connexion qui appelle Office 365 pour envoyer un e-mail :

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

La partie des entrées qui est unique pour les connexions de l’API est la `host` objet. Cet onglet contient deux parties : `api` et `connection`.

Le `api` a le runtime URL d’où que les API managée est hébergé. Vous pouvez voir toutes les API gérées disponibles pour vous en appelant `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Lorsque vous utilisez une API, il peut ou ne peut pas avoir tous les **paramètres de connexion** définis. Si elle n’est pas, aucune **connexion** n’est requise. Dans ce cas, vous devrez créer une connexion. Lorsque vous créez cette connexion, il a donc le nom que vous choisissez, et puis vous faire référence dans le `connection` objet à l’intérieur de la `host` objet. Pour créer une connexion dans un groupe de ressources, appelez :

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Avec le corps suivant :


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues" : {
        "accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location" : "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>Déploiement d’API gérées dans un modèle de gestionnaire de ressources d’Azure

Vous pouvez créer une application complète dans un modèle ARM tant qu’il ne nécessite pas de connexion interactive. Si elle requiert une connexion, vous pouvez définir tout avec le modèle ARM, mais devrez toujours visitez le portail pour autoriser les connexions. 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/',parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:,Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

Vous pouvez le voir dans cet exemple, que les connexions sont simplement normales ressources qui résident dans votre groupe de ressources. Ils font référence à le managedAPIs disponibles dans votre abonnement.

### <a name="your-custom-web-apis"></a>Votre API Web personnalisé

Si vous utilisez votre propre API (en particulier, ceux non gérés par Microsoft), vous devez utiliser l’action **HTTP** intégrée à les appeler. Pour disposer d’une expérience idéale, vous devez exposer un point de terminaison swagger correspondant à votre API. Cela permettra le Concepteur d’application logique restituer les entrées et les sorties de votre API. Sans un swagger, le concepteur sera uniquement en mesure d’afficher les entrées et les sorties en tant qu’objets JSON opaques.

Voici un exemple illustrant la nouvelle `metadata.apiDefinitionUrl` propriété :
```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

Si vous hébergez votre API de Web service de **l’Application** puis il apparaît automatiquement dans la liste des actions disponibles dans le concepteur. Si ce n’est pas le cas, vous devrez les coller directement dans l’URL. Le point de terminaison swagger doit être non authentifié pour être utilisable dans le Concepteur d’applications logique (même si vous pouvez sécuriser l’API lui-même avec toutes les méthodes sont prises en charge dans le Swagger).

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>L’utilisation de vos applications d’API déjà déployées avec 2015-08-01-aperçu

Si vous aviez déployé une application API, vous pouvez l’appeler via l’action **HTTP** .

Par exemple, si vous utilisez la boîte de dépôt à la liste des fichiers, avoir quelque chose comme ceci dans votre définition de version de schéma **2014-12-01-aperçu** :

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Vous pouvez construire l’opération équivalente à HTTP comme ci-dessous (la section Paramètres de la reste de définition d’application logique inchangé) :

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Parcours de ces propriétés, un par un :

| Propriété action |  Description |
| --------------- | -----------  |
| `type` | `Http`Au lieu de`APIapp` |
| `metadata.apiDefinitionUrl` | Si vous souhaitez utiliser cette action dans le Concepteur d’applications logique, vous voudrez inclure le point de terminaison de métadonnées. Il est construit à partir de :`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Il est construit à partir de :`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Toujours`POST` |
| `inputs.body` | Identiques aux paramètres application api | 
| `inputs.authentication` | Identique à l’authentification d’application api |

Cette approche devrait fonctionner pour toutes les actions d’application API. Toutefois, gardez à l’esprit que ces applications API précédentes ne sont plus pris en charge, et vous devez vous déplacer à l’un des deux autres options ci-dessus (une API managée ou votre API Web personnalisé d’hébergement).

## <a name="2-repeat-renamed-to-foreach"></a>2. Répétez renommé Foreach

Pour la version de schéma précédent, nous avons reçu beaucoup de commentaires des clients qui **Répétez** n’était pas clair et n’a pas capturé correctement de que c’est vraiment une boucle for each. Par conséquent, nous avons renommé ensuite **Foreach**. Par exemple :

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

Devrait maintenant être formulée comme suit :

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

Précédemment, la fonction `@repeatItem()` a été utilisé pour faire référence à l’élément en cours qui est itéré. Cette opération a été simplifiée pour juste `@item()`. 

### <a name="referencing-the-outputs-of-the-foreach"></a>Référencer les sorties de Foreach
Pour simplifier davantage, les sorties des actions de **Foreach** ne seront pas encapsulés dans un objet appelé **repeatItems**. Cela signifie que les sorties de la répétition ci-dessus ont été :

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Maintenant il sera :

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Lorsque vous référencez ces sorties, pour obtenir le corps de l’action vous devrez faire :

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

Vous pouvez désormais effectuer à la place :

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Avec ces modifications, les fonctions de `@repeatItem()`, `@repeatBody()` et `@repeatOutputs()` sont supprimés.

## <a name="3-native-http-listener"></a>3. port d’écoute HTTP natif 
Les fonctions d’écouteur HTTP sont désormais intégrées, afin que vous n’avez plus besoin de déployer une application API d’écouteur HTTP. Découvrez [comment rendre votre point de terminaison logique app callable ici tous les détails](app-service-logic-http-endpoint.md). 

Avec ces modifications, la fonction `@accessKeys()` est supprimé et a été remplacé par le `@listCallbackURL()` fonction aux fins d’obtenir le point de terminaison (le cas échéant). En outre, vous maintenant devez définir au moins un déclencheur dans votre logique d’application maintenant. Si vous souhaitez `/run` le flux de travail, vous devez avoir un d’une `manual`, `apiConnectionWebhook` ou `httpWebhook` déclencheurs. 

## <a name="4-calling-child-workflows"></a>4. flux de travail enfant appel

Auparavant, l’appel de flux de travail enfant nécessitait accédant à ce flux de travail et obtenir le jeton d’accès puis en collant dans la définition de l’application de la logique que vous souhaitez appeler cet enfant. Avec la nouvelle version de schéma, le moteur d’applications logique génère automatiquement un SAS lors de l’exécution pour le flux de travail enfant, ce qui signifie que vous n’êtes pas obligé de coller les secrets de la définition.  Voici un exemple :

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

Une deuxième amélioration est que nous remettrez les workflows enfants un accès complet à la demande entrante. Cela signifie que vous pouvez passer des paramètres dans la section de *requêtes* et de l’objet *en-têtes* et que vous pouvez définir entièrement de la totalité du corps.

Enfin, il y a des modifications apportées au workflow enfant. Considérant que, avant que vous pouvez simplement appeler un workflow enfant directement ; maintenant, vous devez définir un point de terminaison du déclencheur dans le flux de travail pour le parent à appeler. En règle générale, cela signifie que vous allez ajouter un déclencheur de type **manuel** et l’utiliser ensuite dans la définition parente. Notez que la `host` propriété a spécifiquement une `triggerName`, parce que vous devez toujours spécifier le déclencheur que vous appelez.

## <a name="other-changes"></a>Autres modifications

### <a name="new-queries-property"></a>Nouvelle propriété de requêtes
Tous les types d’actions prennent désormais en charge une nouvelle entrée appelée **requêtes**. Il peut s’agir d’un objet structuré plutôt que de devoir assembler la chaîne à la main.

### <a name="parse-function-renamed"></a>fonction Parse() renommée
Comme nous ajouterons bientôt autres types de contenu, la `parse()` fonction a été renommée en `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Bientôt disponible : Enterprise Integration API
À ce stade, nous ne pas encore avoir géré des versions de l’API d’intégration entreprise disponibles (par exemple, AS2). Ils seront bientôt couverts dans la [feuille de route](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). En même temps, vous pouvez utiliser votre déploiement BizTalk APIs existantes via l’action HTTP, comme indiqué ci-dessus dans « Utilisation de vos applications d’API déjà déployées. »
