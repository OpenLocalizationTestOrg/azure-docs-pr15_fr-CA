<properties
   pageTitle="Scénario d’application logique : créer un déclencheur de Bus des services Azure fonctions | Microsoft Azure"
   description="Utilisez les fonctions d’Azure pour créer un déclencheur de Bus de Service d’une application de logique"
   services="logic-apps,functions"
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
   ms.date="05/23/2016"
   ms.author="jehollan"/>

# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>Scénario d’application logique : créer un déclencheur de Bus des services Azure à l’aide de fonctions d’Azure

Vous pouvez utiliser les fonctions d’Azure pour créer un déclencheur pour une application de logique lorsque vous déployez un écouteur longue ou une tâche. Par exemple, vous pouvez créer une fonction qui écoutent une file d’attente et puis déclenche immédiatement une application logique comme un déclencheur d’émission.

## <a name="build-the-logic-app"></a>Générez l’application logique

Dans cet exemple, vous avez une fonction en cours d’exécution pour chaque application logique qui doit être déclenchée. Tout d’abord, créez une application de logique qui comporte un déclencheur de la demande HTTP. La fonction appelle ce point de terminaison à chaque réception d’un message de la file d’attente.  

1. Créer une nouvelle application logique ; Sélectionnez le déclencheur **manuel - lorsqu’une demande HTTP est reçue** .  
   Vous pouvez éventuellement spécifier un schéma JSON à utiliser avec le message de la file d’attente à l’aide d’un outil tel que [jsonschema.net](http://jsonschema.net). Collez le schéma dans le déclencheur. Ainsi, le Concepteur de comprendre la forme des données, et plus facilement flux de propriétés via le workflow.
1. Ajouter des étapes supplémentaires que vous souhaitez se produisent après la réception d’un message de la file d’attente. Par exemple, envoyer un message électronique via Office 365.  
1. Enregistrer l’application de la logique pour générer l’URL de rappel pour le déclencheur à l’application de cette logique. L’URL apparaît sur la carte de déclencheur.

![Le rappel s’affiche sur la carte de déclencheur][1]

## <a name="build-the-function"></a>Créer la fonction

Ensuite, vous devez créer une fonction qui agissent comme le déclencheur et écouter de la file d’attente.

1. Dans les [fonctions d’Azure portal](https://functions.azure.com/signin), sélectionnez **Nouvelle fonction**et puis sélectionnez le modèle de **ServiceBusQueueTrigger - C#** .

    ![Azure portal de fonctions][2]

2. Configurez la connexion à la file d’attente de Bus de services (qui utilise le Bus de Service Azure SDK `OnMessageReceive()` écouteur).
3. Écrire une fonction simple pour appeler le point de terminaison logique app (créé précédemment) à l’aide du message de la file d’attente en tant que déclencheur. Voici un exemple complet d’une fonction. L’exemple utilise un `application/json` type de contenu de message, mais vous pouvez le modifier si nécessaire.

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";

   public static void Run(string myQueueItem, TraceWriter log)
   {

       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Pour tester, ajouter un message de la file d’attente via un outil comme [l’Explorateur de Bus de Service](https://github.com/paolosalvatori/ServiceBusExplorer). Voir l’application logique se déclenche immédiatement après que la fonction reçoit le message.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG
