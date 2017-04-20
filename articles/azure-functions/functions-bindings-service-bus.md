<properties
    pageTitle="Déclencheurs de Bus de Service des fonctions et des liaisons d’Azure | Microsoft Azure"
    description="Comprendre comment utiliser des déclencheurs de Bus des services Azure et les liaisons dans les fonctions d’Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="fonctions Azure, fonctions, traitement de l’événement, calcul dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-service-bus-triggers-and-bindings-for-queues-and-topics"></a>Azure des déclencheurs de Bus de Service des fonctions et des liaisons pour les files d’attente et les rubriques

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et les déclencheurs de Bus des services Azure de code et les liaisons dans les fonctions d’Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="sbtrigger"></a>Déclencheur de file d’attente ou rubrique du Bus des services

#### <a name="functionjson"></a>Function.JSON

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de la fonction pour la file d’attente ou rubrique ou le message de la file d’attente ou une rubrique. 
- `queueName`: Pour la file d’attente de déclencheur uniquement, le nom de la file d’attente à interroger.
- `topicName`: Pour la rubrique déclencheur uniquement, le nom de la rubrique à interroger.
- `subscriptionName`: Pour la rubrique déclencheur uniquement, le nom de l’abonnement.
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de Service Bus. La chaîne de connexion doit être un espace de noms de Service Bus, ne pas limité à une file d’attente spécifique ou d’une rubrique. Si la chaîne de connexion n’ont gérer les droits, les `accessRights` propriété. Si vous laissez `connection` vide, la liaison ou le déclencheur fonctionnera avec la chaîne de connexion du Bus de Service par défaut pour l’application de la fonction, qui est spécifiée par le paramètre d’application AzureWebJobsServiceBus.
- `accessRights`: Spécifie les droits d’accès pour la chaîne de connexion. La valeur par défaut est `manage`. La valeur `listen` si vous utilisez une chaîne de connexion qui ne fournit pas de gérer les autorisations. Dans le cas contraire les fonctions runtime peut essayer et échec pour effectuer des opérations nécessitant des droits de gestion.
- `type`: Doit avoir la valeur *serviceBusTrigger*.
- `direction`: Doit avoir la valeur *dans*. 

Exemple *function.json* d’un déclencheur de file d’attente de Bus de Service :

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-code-example-that-processes-a-service-bus-queue-message"></a>Exemple de code C# qui traite un message de file d’attente de Bus de Service

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### <a name="f-code-example-that-processes-a-service-bus-queue-message"></a>F# exemple de code qui traite un message de file d’attente de Bus de Service

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### <a name="nodejs-code-example-that-processes-a-service-bus-queue-message"></a>Exemple de code Node.js qui traite un message de file d’attente de Bus de Service

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### <a name="supported-types"></a>Types pris en charge

Le message de la file d’attente des Bus de Service peut être désérialisé à un des types suivants :

* Objet (à partir de JSON)
* chaîne
* tableau d’octets 
* `BrokeredMessage`(C#) 

#### <a id="sbpeeklock"></a>Problème de PeekLock

L’exécution de fonctions reçoit un message de `PeekLock` mode et appelle `Complete` sur le message, si la fonction se termine avec succès, ou les appels `Abandon` si la fonction échoue. Si la fonction s’exécute plus longtemps que la `PeekLock` délai d’attente, le verrou est renouvelé automatiquement.

#### <a id="sbpoison"></a>Gestion des messages empoisonnés

Bus de service effectue ses propres messages incohérents, qui ne peut pas être contrôlé ou configurées dans configuration des fonctions d’Azure ou le code de gestion des. 

#### <a id="sbsinglethread"></a>Simple-threading

Par défaut, les fonctions runtime traite plusieurs messages de la file d’attente de simultanément. Pour diriger le runtime pour ne traiter qu’une seule file d’attente ou un message de la rubrique à la fois, la valeur `serviceBus.maxConcurrrentCalls` à 1 dans le fichier *host.json* . Pour plus d’informations sur le fichier *host.json* , reportez-vous à la section [Structure de dossiers](functions-reference.md#folder-structure) dans l’article de référence de développeur et [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) dans le wiki de référentiel de WebJobs.Script.

## <a id="sboutput"></a>Liaison de sortie de file d’attente de Bus de service ou de la rubrique

#### <a name="functionjson"></a>Function.JSON

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de la fonction pour la file d’attente ou le message de la file d’attente. 
- `queueName`: Pour la file d’attente de déclencheur uniquement, le nom de la file d’attente à interroger.
- `topicName`: Pour la rubrique déclencheur uniquement, le nom de la rubrique à interroger.
- `subscriptionName`: Pour la rubrique déclencheur uniquement, le nom de l’abonnement.
- `connection`: Identique à celle des Bus de Service du déclencheur.
- `accessRights`: Spécifie les droits d’accès pour la chaîne de connexion. La valeur par défaut est `manage`. La valeur `send` si vous utilisez une chaîne de connexion qui ne fournit pas de gérer les autorisations. Dans le cas contraire les fonctions runtime peut essayer et échec pour effectuer des opérations qui nécessitent droits de gestion, telles que la création de files d’attente.
- `type`: Doit avoir la valeur *serviceBus*.
- `direction`: Doit avoir la valeur de *sortie*. 

Exemple *function.json* pour l’utilisation d’un déclencheur de minuteur pour écrire des messages de file d’attente de Bus de Service :

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="supported-types"></a>Types pris en charge

Fonctions Azure peuvent créer un message de file d’attente de Bus de Service à partir d’un des types suivants.

* Objet (toujours crée un message JSON, crée le message avec un objet null si la valeur est null lorsque la fonction se termine)
* chaîne (crée un message si la valeur est non null lorsque la fonction se termine)
* tableau d’octets (fonctionne comme une chaîne) 
* `BrokeredMessage`(C#, fonctionne comme une chaîne)

Pour créer plusieurs messages dans une fonction C#, vous pouvez utiliser `ICollector<T>` ou `IAsyncCollector<T>`. Un message est créé lorsque vous appelez le `Add` méthode.

#### <a name="c-code-examples-that-create-service-bus-queue-messages"></a>Exemples de code C# qui créent des messages de file d’attente de Bus de Service

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### <a name="f-code-example-that-creates-a-service-bus-queue-message"></a>F# exemple de code qui crée un message de file d’attente de Bus de Service

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### <a name="nodejs-code-example-that-creates-a-service-bus-queue-message"></a>Exemple de code Node.js qui crée un message de file d’attente de Bus de Service

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
