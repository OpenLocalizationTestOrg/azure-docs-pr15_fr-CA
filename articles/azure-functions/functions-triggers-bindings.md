<properties
    pageTitle="Déclencheurs des fonctions et des liaisons d’Azure | Microsoft Azure"
    description="Comprendre comment utiliser des déclencheurs et des liaisons dans les fonctions d’Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="10/27/2016"
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Azure référence du développeur les déclencheurs et les liaisons de fonctions


Cette rubrique fournit une référence générale pour les liaisons et les déclencheurs. Il comprend certaines des fonctionnalités de liaison avancée et syntaxe prise en charge par tous les types de liaison.  

Si vous recherchez des informations détaillées autour de configuration et de codage d’un type spécifique de déclencheur ou de la liaison, vous souhaiterez peut-être cliquez sur l’un de le ci-dessous à la place des liaisons ou du déclencheur :

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)] 

Ces articles partent du principe que vous avez lu la [référence du développeur les fonctions Azure](functions-reference.md)et les articles de référence de développeur [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md)ou [Node.js](functions-reference-node.md) .



## <a name="overview"></a>Vue d’ensemble

Les déclencheurs sont des réponses d’événements utilisés pour déclencher votre code personnalisé. Ils vous permettent de répondre aux événements sur la plateforme Azure ou sur site. Les liaisons représentent les métadonnées nécessaires permettant de connecter votre code pour le trigger de votre choix ou d’entrée associée ou des données de sortie.

Pour obtenir une meilleure idée des liaisons différentes que vous pouvez intégrer à votre application de la fonction d’Azure, reportez-vous au tableau suivant.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]  

Pour mieux comprendre les déclencheurs et les liaisons en règle générale, vous voulez exécuter du code au processus un nouvel élément déposé dans une file d’attente de stockage Azure. Fonctions Azure fournit un déclencheur de file d’attente d’Azure pour prendre en charge cela. Vous devez, les informations suivantes pour contrôler la file d’attente :
 
- Le compte de stockage où se trouve la file d’attente.
- Le nom de la file d’attente.
- Un nom de variable par votre code pour voir le nouvel élément a été supprimé dans la file d’attente.  
 
Un déclencheur de la file d’attente liaison contient les informations pour une fonction d’Azure. Le fichier *function.json* pour chaque fonction contient toutes les liaisons associées.  Voici un exemple de liaison de déclencher des *function.json* contenant une file d’attente. 

    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        }
      ],
      "disabled": false
    }

Votre code peut envoyer différents types de sortie en fonction de la façon dont le nouvel élément de file d’attente est traité. Par exemple, vous pouvez souhaiter écrire un nouvel enregistrement à une table de stockage Azure.  Pour ce faire, vous pouvez configurer une liaison de sortie à une table de stockage Azure. Voici un exemple d' *function.json* qui inclut une liaison de sortie de table de stockage qui peut être utilisée avec un déclencheur de la file d’attente. 


    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        },
        {
          "type": "table",
          "name": "myNewUserTableBinding",
          "tableName": "newUserTable",
          "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
          "direction": "out"
        }
      ],
      "disabled": false
    }


La fonction C# suivante répond à un nouvel élément en cours de suppression dans la file d’attente et écrit une nouvelle entrée d’utilisateur dans une table de stockage Azure.

    #r "Newtonsoft.Json"

    using System;
    using Newtonsoft.Json;

    public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                    TraceWriter log)
    {
        // In this example the queue item is a JSON string representing an order that contains the name, 
        // address and mobile number of the new customer.
        dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);
    
        await myNewUserTableBinding.AddAsync(
            new Person() { 
                PartitionKey = "Test", 
                RowKey = Guid.NewGuid().ToString(), 
                Name = order.name,
                Address = order.address,
                MobileNumber = order.mobileNumber }
            );
    }
    
    public class Person
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Name { get; set; }
        public string Address { get; set; }
        public string MobileNumber { get; set; }
    }

Pour plus d’exemples de code et des informations plus spécifiques concernant les types de stockage Azure qui sont pris en charge, consultez [les fonctions Azure déclencheurs et les liaisons pour le stockage Azure](functions-bindings-storage.md).


Pour utiliser les fonctionnalités de liaison les plus avancées dans Azure portal, cliquez sur l’option de **l’Éditeur avancé** dans l’onglet **intégration** de votre fonction. L’éditeur avancé vous permet de modifier le *function.json* directement dans le portail.

## <a name="dynamic-parameter-binding"></a>Liaison de paramètre dynamique 

Au lieu d’une configuration statique, définition pour les propriétés de liaison de sortie, vous pouvez configurer les paramètres pour être lié dynamiquement aux données faisant partie d’une liaison d’entrée de votre déclencheur. Imaginez un scénario où de nouvelles commandes sont traitées à l’aide d’une file d’attente de stockage Azure. Chaque nouvel élément de file d’attente est une chaîne JSON contenant au moins les propriétés suivantes :

    {
      name : "Customer Name",
      address : "Customer's Address".
      mobileNumber : "Customer's mobile number."
    }

Vous pouvez souhaiter envoyer au client un message de texte SMS à l’aide de votre compte de Twilio sous la forme d’une mise à jour que la commande a été reçue.  Vous pouvez configurer le `body` et `to` liaison lier dynamiquement de sortie du champ de votre Twilio la `name` et `mobileNumber` qui faisaient partie de l’entrée de comme suit.

    {
      "name": "myNewOrderItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newOrders",
      "connection": "orders_STORAGE"
    },
    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "{mobileNumber}",
      "from": "+XXXYYYZZZZ",
      "body": "Thank you {name}, your order was received",
      "direction": "out"
    },
 
Maintenant le code de la fonction ne doit initialiser le paramètre de sortie comme suit. Lors de l’exécution, les propriétés de sortie seront liées aux données d’entrée souhaitées.

C#

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message variable.
    message = new SMSMessage();

    // When using dynamic parameter binding no need to set this in code.
    // message.body = msg;
    // message.to = myNewOrderItem.mobileNumber

Node.js

    context.bindings.message = {
        // When using dynamic parameter binding no need to set this in code.
        //body : msg,
        //to : myNewOrderItem.mobileNumber
    };




## <a name="random-guids"></a>GUID aléatoire

Fonctions Azure fournit une syntaxe pour générer un GUID aléatoire avec vos liaisons. La syntaxe de liaison suivante va écrire la sortie dans un objet BLOB de nouveau avec un nom unique dans un conteneur de stockage Azure : 

    {
      "type": "blob",
      "name": "blobOutput",
      "direction": "out",
      "path": "my-output-container/{rand-guid}"
    }



## <a name="returning-a-single-output"></a>Renvoi d’une seule sortie

Dans le cas où votre code de fonction retourne une sortie unique, vous pouvez utiliser une liaison de sortie nommée `$return` pour conserver une signature plus naturelle de la fonction dans votre code. Cela permet uniquement avec les langues qui prennent en charge la valeur de retour (C#, Node.js, F#). La liaison est similaire à la liaison de sortie blob suivant qui est utilisée avec un déclencheur de la file d’attente.

    {
      "bindings": [
        {
          "type": "queueTrigger",
          "name": "input",
          "direction": "in",
          "queueName": "test-input-node"
        },
        {
          "type": "blob",
          "name": "$return",
          "direction": "out",
          "path": "test-output-node/{id}"
        }
      ]
    }


Le code C# suivant retourne la sortie plus naturellement sans utiliser un `out` paramètre dans la signature de la fonction.


    public static string Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }

    // Async example
    public static Task<string> Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }


Cette approche est illustrée ci-dessous avec Node.js.

    module.exports = function (context, input) {
        var json = JSON.stringify(input);
        context.log('Node.js script processed queue message', json);
        context.done(null, json);
    }

F# exemple fourni ci-dessous.

    let Run(input: WorkItem, log: TraceWriter) =
        let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
        log.Info(sprintf "F# script processed queue message '%s'" json)
        json

Cela peut être également utilisé avec plusieurs paramètres de sortie en désignant une seule sortie avec `$return`.


## <a name="route-support"></a>Prise en charge de l’itinéraire

Par défaut, lorsque vous créez une fonction d’un déclencheur HTTP, ou WebHook, la fonction est adressable avec un itinéraire du formulaire :

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Vous pouvez personnaliser cet itinéraire à l’aide de l’option `route` propriété sur le déclencheur HTTP d’entrée de liaison. Par exemple, le fichier *function.json* suivant définit un `route` propriétés d’un déclencheur HTTP :

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [ "get" ],
          "route": "products/{category:alpha}/{id:int?}"
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }

À l’aide de cette configuration, la fonction est maintenant adressable avec l’itinéraire suivant au lieu de la gamme d’origine.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

Cela permet au code de la fonction prendre en charge les deux paramètres dans l’adresse, `category` et `id`. Vous pouvez utiliser une [Contrainte d’itinéraire API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) avec vos paramètres. Le code de fonction C# suivant utilise les deux paramètres.

    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
                                                    TraceWriter log)
    {
        if (id == null)
           return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
        else
           return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
    }

Voici le code de la fonction Node.js à utiliser les mêmes paramètres d’itinéraire.

    module.exports = function (context, req) {

        var category = context.bindingData.category;
        var id = context.bindingData.id;
    
        if (!id) {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "All " + category + " items were requested."
            };
        }
        else {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: category + " item with id = " + id + " was requested."
            };
        }
    
        context.done();
    } 

Par défaut, tous les itinéraires de fonction sont précédés *d’api*. Vous pouvez également personnaliser ou supprimer le préfixe à l’aide de la `http.routePrefix` propriété dans votre fichier *host.json* . L’exemple suivant supprime le préfixe de l’itinéraire *api* à l’aide d’une chaîne vide pour le préfixe dans le fichier *host.json* .

    {
      "http": {
        "routePrefix": ""
      }
    }

Pour plus d’informations sur la façon de mettre à jour le fichier *host.json* de votre fonction, voir [Comment faire pour l’application de la fonction de mise à jour des fichiers](functions-reference.md#fileupdate). 

En ajoutant cette configuration, la fonction est maintenant adressable avec l’itinéraire suivant :

    http://<yourapp>.azurewebsites.net/products/electronics/357

Pour plus d’informations sur les autres propriétés que vous pouvez configurer dans le fichier *host.json* , reportez-vous à la section [référence de host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).





## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Test d’une fonction](functions-test-a-function.md)
* [Une fonction de mise à l’échelle](functions-scale.md)
