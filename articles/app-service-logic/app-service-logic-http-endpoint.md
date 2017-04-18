<properties
   pageTitle="Logique des applications comme points de terminaison pouvant être appelé par"
   description="Comment créer et configurer des points de terminaison de déclencheur et les utiliser dans une logique d’application dans le Service d’application Azure"
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


# <a name="logic-apps-as-callable-endpoints"></a>Logique des applications comme points de terminaison pouvant être appelé par

Logique d’applications en mode natif peut exposer un point de terminaison HTTP synchrone en tant que déclencheur.  Vous pouvez également utiliser le modèle de points de terminaison pouvant être appelé pour appeler la logique d’applications sous la forme d’un flux de travail imbriqué via l’action « workflow » dans une logique d’application.

Il existe 3 types de déclencheurs qui peuvent recevoir des demandes :

* Demande
* ApiConnectionWebhook
* HttpWebhook

Pour le reste de cet article, nous allons utiliser la **demande** que dans l’exemple, mais tous les principes s’appliquent même à 2 autres types de déclencheurs.

## <a name="adding-a-trigger-to-your-definition"></a>Ajout d’un déclencheur à votre définition
La première étape consiste à ajouter un déclencheur de votre définition d’application logique qui peut recevoir des demandes entrantes.  Vous pouvez rechercher dans le concepteur pour « Demande HTTP » ajouter la carte de déclencheur. Vous pouvez définir un corps de demande JSON schéma, et le concepteur génère des jetons pour vous aider à analyser et transmettre des données à partir du déclencheur manuel via le workflow.  Je recommande d’utiliser un outil tel que [jsonschema.net](http://jsonschema.net) pour générer un schéma JSON à partir d’une charge de corps d’exemple.

![Carte de déclencheur de demande][2]

Après avoir enregistré votre définition d’application de la logique, une URL de rappel sera générée similaire à celui-ci :
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

Cette URL contient une clé des associations de sécurité dans les paramètres de requête utilisés pour l’authentification.

Vous pouvez également obtenir ce point de terminaison dans le portail Azure :

![][1]

Ou bien, en appelant :

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>Sécurité pour l’URL de déclencheur

URL de rappel d’application logique sont générées en toute sécurité à l’aide d’une Signature d’accès partagé.  La signature est transmise comme paramètre de requête et doit être validée avant l’application de la logique se déclenche.  Il est généré par une combinaison unique d’une clé secrète par application de la logique, le nom du déclencheur et l’opération en cours.  Sauf si un utilisateur a accès à la clé d’application logique secrète, ils ne serait pas en mesure de générer une signature valide.

## <a name="calling-the-logic-app-triggers-endpoint"></a>L’appel de point de terminaison du déclencheur application logique

Une fois que vous avez créé le point de terminaison pour votre déclencheur, vous pouvez déclencher celui-ci via un `POST` l’URL complète. Vous pouvez inclure des en-têtes supplémentaires et tout son contenu dans le corps.

Si le type de contenu est `application/json` puis vous pourrez à partir des propriétés de référence à l’intérieur de la requête. Sinon, elle sera traitée comme une seule unité binaire qui peut être passée à d’autres API, mais ne peuvent pas être référencée à l’intérieur du flux de travail sans convertir le contenu.  Par exemple, si vous passez `application/xml` contenu que vous pouvez utiliser `@xpath()` pour effectuer une extraction xpath, ou `@json()` pour convertir à partir de XML en JSON.  Types de plus d’informations sur l’utilisation de contenu [se trouve ici](app-service-logic-content-type.md)

En outre, vous pouvez spécifier un schéma JSON dans la définition. Ainsi, le concepteur générer des jetons que vous pouvez ensuite passer en étapes.  Par exemple ce qui suit apporte une `title` et `name` disponibles dans le Concepteur de jeton :

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>Référencer le contenu de la demande entrante

Le `@triggerOutputs()` fonction affiche le contenu de la demande entrante. Par exemple, il serait :

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

Vous pouvez utiliser la `@triggerBody()` raccourci pour accéder à la `body` propriété spécifiquement. 

## <a name="responding-to-the-request"></a>Répondre à la demande

Pour certaines requêtes qui démarre une application logique, vous souhaiterez sans doute répondre avec du contenu à l’appelant. Il existe un nouveau type d’action appelé **réponse** qui peut être utilisé pour construire le code d’état, les corps et les en-têtes de réponse. Notez que si aucune **réponse** n’est présent, le point de terminaison logique application sera *immédiatement* répondre avec **202 accepté**.

![Action de réponse HTTP][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

Les réponses sont les suivantes :

| Propriété | Description |
| -------- | ----------- |
| statusCode | Le code d’état HTTP pour répondre à la demande entrante. Il peut être n’importe quel code d’état valide commence par 2xx, 4xx ou 5xx. codes d’état 3xx ne sont pas autorisés. | 
| corps | Un objet de corps qui peut être une chaîne, un objet JSON ou le contenu même binaire référencé à partir d’une étape précédente. | 
| en-têtes | Vous pouvez définir le nombre d’en-têtes à inclure dans la réponse | 

Toutes les étapes dans la logique d’application qui sont requis pour la réponse doivent se terminer dans les *60 secondes* pour la demande d’origine recevoir la réponse **à moins que le flux de travail est appelée comme une application de logique imbriquées**. Si aucune action de réponse n’est atteinte dans 60 secondes puis la requête entrante doit expirer et recevoir une réponse de l' **expiration de délai 408 Client** HTTP.  Pour les applications de logique imbriqués, le parent logique d’application continue à attendre une réponse jusqu'à la fin, indépendamment de la quantité de temps nécessaire.

## <a name="advanced-endpoint-configuration"></a>Configuration avancée du point de terminaison

Applications de logique ont en charge intégrée pour le point de terminaison d’un accès direct et de toujours utiliser le `POST` méthode pour lancer une exécution de l’application de la logique. L’application d' **Écouteur HTTP** API précédemment également pris en charge modifiant les segments d’URL et de la méthode HTTP. Vous pouvez même configurer de sécurité supplémentaire ou un domaine personnalisé en l’ajoutant à l’hôte d’application API (l’application Web hébergeant l’application API). 

Cette fonctionnalité est disponible grâce à la **gestion de l’API**:
* [La méthode de la demande de modification](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Modifier les segments de l’URL de la demande](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurer vos domaines de la gestion des API dans l’onglet **configurer** dans Azure portal classique
* Configurer la stratégie pour contrôler l’authentification de base (**liaison nécessaire**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Résumé de la migration à partir de 2014-12-01-aperçu

|  2014-12-01-aperçu | 2016-06-01 |
|---------------------|--------------------|
| Cliquez sur l’application d' **Écouteur HTTP** API | Cliquez sur **déclencheur manuel** (aucune application API requise) |
| Port d’écoute HTTP définition «*envoie de réponse automatique*» | Soit inclure une action de **réponse** ou pas dans la définition de flux de travail |
| Configurer l’authentification de base ou OAuth | via la gestion de l’API |
| Configurer la méthode HTTP | via la gestion de l’API |
| Configurer le chemin d’accès relatif | via la gestion de l’API |
| Référence de l’organisme entrant via`@triggerOutputs().body.Content` | Référence via`@triggerOutputs().body` |
| Action de **réponse HTTP d’envoyer** sur l’écouteur HTTP | Cliquez sur **répondre à la demande HTTP** (aucune application API requise)


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png
