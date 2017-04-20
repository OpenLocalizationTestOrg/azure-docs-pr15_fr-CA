<properties
    pageTitle="Les liaisons de concentrateur d’événements fonctions Azure | Microsoft Azure"
    description="Comprendre l’utilisation des liaisons de concentrateur d’événements Azure dans les fonctions d’Azure."
    services="functions"
    documentationCenter="na"
    authors="wesmc7777"
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
    ms.date="10/17/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-event-hub-bindings"></a>Liaisons de concentrateur d’événements fonctions Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et liaisons de [Concentrateur d’événements Azure](../event-hubs/event-hubs-overview.md) de code pour les fonctions d’Azure. Fonctions Azure prennent en charge les liaisons de déclencheur et de sortie pour les concentrateurs d’événement Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Si vous ne connaissez pas les concentrateurs d’événement Azure, consultez la [vue d’ensemble du concentrateur d’événements Azure](../event-hubs/event-hubs-overview.md).

## <a name="azure-event-hub-trigger-binding"></a>Concentrateur d’événements Azure déclencher de liaison

Un déclencheur de concentrateur d’événements Azure peut servir pour répondre à un événement envoyé à un flux d’événements événements concentrateur. Vous devez disposer d’un accès en lecture sur le concentrateur d’événements pour configurer une liaison de déclencheur.

#### <a name="functionjson-for-event-hub-trigger-binding"></a>Function.JSON pour concentrateur d’événements déclencher la liaison

Le fichier *function.json* pour un déclencheur concentrateur d’événements Azure spécifie les propriétés suivantes :

- `type`: Doit avoir la valeur *eventHubTrigger*.
- `name`: Le nom de la variable utilisé dans le code de la fonction pour le message d’événement concentrateur. 
- `direction`: Doit avoir la valeur *dans*. 
- `path`: Nom du concentrateur d’événements.
- `consumerGroup`: Il s’agit d’une propriété facultative permet de définir le [groupe de consommateurs](../event-hubs-overview.md#consumer-groups) utilisée pour s’abonner à des événements dans le concentrateur. En cas d’omission, le `$Default` groupe de consommateur est utilisé. 
- `connection`: Nom d’un paramètre d’application qui contient la chaîne de connexion à l’espace de noms résidant dans le concentrateur d’événements. Copier cette chaîne de connexion en cliquant sur le bouton **Informations de connexion** pour l’espace de noms, et non au concentrateur d’événements lui-même.  Cette chaîne de connexion doit avoir au moins autorisations de lecture pour activer le déclencheur.

        {
          "bindings": [
            {
              "type": "eventHubTrigger",
              "name": "myEventHubMessage",
              "direction": "in",
              "path": "MyEventHub",
              "connection": "myEventHubReadConnectionString"
            }
          ],
          "disabled": false
        }

#### <a name="azure-event-hub-trigger-c-example"></a>Exemple de déclencheur C# Azure concentrateur d’événements
 
À l’aide de la function.json d’exemple ci-dessus, le corps du message d’événement est enregistré à l’aide de C# code de la fonction ci-dessous :
 
    using System;
    
    public static void Run(string myEventHubMessage, TraceWriter log)
    {
        log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
    }

#### <a name="azure-event-hub-trigger-f-example"></a>Exemple de déclencheur F# Azure concentrateur d’événements

À l’aide de la function.json d’exemple ci-dessus, le corps du message d’événement est enregistré à l’aide de la F# code de fonction ci-dessous :

    let Run(myEventHubMessage: string, log: TraceWriter) =
        log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)

#### <a name="azure-event-hub-trigger-nodejs-example"></a>Déclencheur d’événement concentrateur Azure Node.js exemple
 
À l’aide de la function.json d’exemple ci-dessus, le corps du message de l’événement est enregistré à l’aide du code de la fonction Node.js ci-dessous :
 
    module.exports = function (context, myEventHubMessage) {
        context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
        context.done();
    };


## <a name="azure-event-hub-output-binding"></a>Liaison de sortie Azure concentrateur d’événements

Un concentrateur d’événements Azure liaison de sortie est utilisé pour écrire des événements dans un flux d’événements événements concentrateur. Vous devez disposer d’autorisation d’envoi à un concentrateur d’événements pour écrire des événements à celui-ci. 

#### <a name="functionjson-for-event-hub-output-binding"></a>liaison de sortie Function.JSON pour concentrateur d’événements

Le fichier *function.json* pour un concentrateur d’événements Azure sortie liaison spécifie les propriétés suivantes :

- `type`: Doit avoir la valeur *eventHub*.
- `name`: Le nom de la variable utilisé dans le code de la fonction pour le message d’événement concentrateur. 
- `path`: Nom du concentrateur d’événements.
- `connection`: Nom d’un paramètre d’application qui contient la chaîne de connexion à l’espace de noms résidant dans le concentrateur d’événements. Copier cette chaîne de connexion en cliquant sur le bouton **Informations de connexion** pour l’espace de noms, et non au concentrateur d’événements lui-même.  Cette chaîne de connexion doit avoir les autorisations d’envoi pour envoyer le message dans le flux d’événement concentrateur.
- `direction`: Doit avoir la valeur de *sortie*. 

        {
          "type": "eventHub",
          "name": "outputEventHubMessage",
          "path": "myeventhub",
          "connection": "MyEventHubSend",
          "direction": "out"
        }


#### <a name="azure-event-hub-c-code-example-for-output-binding"></a>Exemple de code C# du concentrateur événement de Azure pour la liaison de sortie
 
C# fonction code d’exemple suivant illustre l’écriture d’un événement à un flux d’événements concentrateur d’événements. Cet exemple représente le résultat de concentrateur d’événements de liaison ci-dessus appliqué à un déclencheur de minuterie C#.  
 
    using System;
    
    public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
    {
        String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    
        log.Verbose(msg);   
        
        outputEventHubMessage = msg;
    }

#### <a name="azure-event-hub-f-code-example-for-output-binding"></a>Exemple de code événement concentrateur F# de Azure pour la liaison de sortie

F# fonction code d’exemple suivant illustre l’écriture d’un événement à un flux d’événements concentrateur d’événements. Cet exemple représente le résultat de concentrateur d’événements de liaison ci-dessus appliqué à un déclencheur de minuterie C#.

    let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
        let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
        log.Verbose(msg);
        outputEventHubMessage <- msg;

#### <a name="azure-event-hub-nodejs-code-example-for-output-binding"></a>Exemple de code événement concentrateur Node.js Azure pour la liaison de sortie
 
Le code de fonction Node.js exemple suivant illustre l’écriture d’un événement à un flux d’événements concentrateur d’événements. Cet exemple représente le résultat de concentrateur d’événements de liaison ci-dessus appliqué à un déclencheur de minuterie Node.js.  
 
    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
        
        if(myTimer.isPastDue)
        {
            context.log('TimerTriggerNodeJS1 is running late!');
        }

        context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
        
        context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
    
        context.done();
    };

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
