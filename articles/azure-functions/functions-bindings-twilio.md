<properties
    pageTitle="Liaison de fonctions Twilio Azure | Microsoft Azure"
    description="Comprendre l’utilisation des liaisons de Twilio avec des fonctions d’Azure."
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
    ms.date="10/20/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-twilio-output-binding"></a>Liaison de sortie Azure fonctions Twilio

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et utiliser des liaisons de Twilio avec fonctions d’Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Prend en charge des fonctions Azure Twilio sortie de liaisons pour activer les fonctions envoyer des messages texte SMS avec quelques lignes de code et un compte [Twilio](https://www.twilio.com/) . 
 

## <a name="functionjson-for-azure-notification-hub-output-binding"></a>liaison de sortie Function.JSON de concentrateur de Notification Azure

Le fichier function.json fournit les propriétés suivantes :

- `name`: Nom variable utilisée dans le code de la fonction de message texte Twilio SMS.
- `type`: doit être défini sur *« twilioSms »*.
- `accountSid`: Cette valeur doit être définie sur le nom d’un paramètre d’application qui contient le Sid de compte Twilio.
- `authToken`: Cette valeur doit être définie sur le nom d’un paramètre d’application qui contient votre jeton d’authentification Twilio.
- `to`: Cette valeur est définie pour le numéro de téléphone que le texte SMS est envoyé à.
- `from`: Cette valeur est définie pour le numéro de téléphone que le texte SMS est envoyé à partir de.
- `direction`: doit avoir la valeur *« out »*.
- `body`: Cette valeur peut être utilisée pour coder le message texte SMS, si vous n’avez pas besoin de définir dynamiquement dans le code de votre fonction. 

 
Function.json de l’exemple :

    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "+1704XXXXXXX",
      "from": "+1425XXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Liaison de sortie exemple C# file d’attente de déclencheur avec Twilio

#### <a name="synchronous"></a>Synchrone

Ce code exemple synchrone d’un déclencheur de file d’attente de stockage Azure utilise un paramètre de sortie pour envoyer un message texte à un client qui a passé une commande.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"

    using System;
    using Newtonsoft.Json;
    using Twilio;

    public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        message = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        message.Body = msg;
        message.To = order.mobileNumber;
    }

#### <a name="asynchronous"></a>Asynchrone

Ce code exemple asynchrone d’un déclencheur de file d’attente de stockage Azure envoie un message texte à un client qui a passé une commande.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"
     
    using System;
    using Newtonsoft.Json;
    using Twilio;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        SMSMessage smsText = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        smsText.Body = msg;
        smsText.To = order.mobileNumber;
        
        await message.AddAsync(smsText);
    }


## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Liaison de sortie exemple Node.js de déclencheur de file d’attente avec Twilio

Cet exemple montre comment Node.js envoie un message texte à un client qui a passé une commande.

    module.exports = function (context, myQueueItem) {
        context.log('Node.js queue trigger function processed work item', myQueueItem);
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        var msg = "Hello " + myQueueItem.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the message binding.
        context.bindings.message = {};
    
        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        context.bindings.message = {
            body : msg,
            to : myQueueItem.mobileNumber
        };
    
        context.done();
    };

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
