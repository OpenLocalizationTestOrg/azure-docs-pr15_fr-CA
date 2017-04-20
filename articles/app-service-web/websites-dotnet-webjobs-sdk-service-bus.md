<properties 
    pageTitle="L’utilisation du Bus des services Azure avec le SDK WebJobs" 
    description="Apprenez à utiliser les rubriques et les files d’attente du Bus des services Azure avec le SDK WebJobs." 
    services="app-service\web, service-bus" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-service-bus-with-the-webjobs-sdk"></a>L’utilisation du Bus des services Azure avec le SDK WebJobs

## <a name="overview"></a>Vue d’ensemble

Ce guide fournit des exemples de code C# qui montrent comment déclencher un processus lors de la réception d’un message de Bus des services Azure. Les exemples de code utilisent le [Kit de développement logiciel WebJobs](websites-dotnet-webjobs-sdk.md) version 1.x.

Le guide suppose que vous savez [comment créer un projet de WebJob dans Visual Studio avec des chaînes de connexion qui pointent vers votre compte de stockage](websites-dotnet-webjobs-sdk-get-started.md).

Les extraits de code affichent uniquement les fonctions, pas le code qui crée la `JobHost` objet comme dans cet exemple :

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

Un [exemple de code complet Bus de Service](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs) est dans le référentiel webjobs azure-exemples sdk sur GitHub.com.

## <a id="prerequisites"></a>Conditions préalables

Pour travailler avec le Bus de Service, vous devez installer le package NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) en plus des autres packages WebJobs SDK. 

Vous devez également définir la chaîne de connexion de AzureWebJobsServiceBus outre les chaînes de connexion de stockage.  Vous pouvez le faire le `connectionStrings` section du fichier App.config, comme illustré dans l’exemple suivant :

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

Pour un exemple de projet qui inclut le paramètre de chaîne de connexion de Service Bus dans le fichier App.config, consultez [exemple de Bus de Service](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus). 

Les chaînes de connexion peuvent également être définies dans l’environnement runtime Azure, puis substitue les paramètres d’App.config lorsque le WebJob s’exécute dans Azure ; Pour plus d’informations, consultez [Mise en route avec le Kit de développement logiciel WebJobs](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account).

## <a id="trigger"></a>Comment déclencher une fonction lors de la réception d’un message de file d’attente de Bus de Service

Pour écrire une fonction qui le SDK WebJobs appelle lors de la réception d’un message de la file d’attente, utilisez la `ServiceBusTrigger` attribut. Le constructeur d’attribut a un paramètre qui spécifie le nom de la file d’attente d’interrogation.

### <a name="how-servicebustrigger-works"></a>Fonctionnement de ServiceBusTrigger

Le Kit de développement logiciel reçoit un message de `PeekLock` mode et appelle `Complete` sur le message, si la fonction se termine avec succès, ou les appels `Abandon` si la fonction échoue. Si la fonction s’exécute plus longtemps que la `PeekLock` délai d’attente, le verrou est renouvelé automatiquement.

Bus de service opère sa propre gestion de file d’attente de poison qui ne peut pas être contrôlée ou configurée par le SDK WebJobs. 

### <a name="string-queue-message"></a>Message de la file d’attente chaîne

L’exemple de code suivant lit un message de la file d’attente qui contient une chaîne et écrit la chaîne dans le tableau de bord WebJobs SDK.

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**Remarque :** Si vous créez les file d’attente messages dans une application qui n’utilise pas le SDK WebJobs, veillez à [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) la valeur « text/plain ».

### <a name="poco-queue-message"></a>Message de file d’attente POCO

Le Kit de développement logiciel désérialise automatiquement un message de la file d’attente qui contient JSON pour un POCO [(Plain ancien objet CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) type. L’exemple de code suivant lit un message de la file d’attente contenant un `BlobInformation` objet doté d’un `BlobName` propriété :

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Pour des exemples de code illustrant comment utiliser les propriétés de la POCO pour travailler avec les BLOB et les tables dans la même fonction, consultez la [version de files d’attente de stockage de cet article](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

Si votre code qui crée le message de la file d’attente n’utilise pas le SDK WebJobs, utilisez un code similaire à l’exemple suivant :

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### <a name="types-servicebustrigger-works-with"></a>Types ServiceBusTrigger fonctionne avec

Outre `string` et POCO types, vous pouvez utiliser la `ServiceBusTrigger` attribut avec un tableau d’octets ou un `BrokeredMessage` objet.

## <a id="create"></a>Comment faire pour créer des messages de file d’attente de Bus de Service

Pour écrire une fonction qui crée une nouvelle utilisation de messages de file d’attente du `ServiceBus` d’attribut et passez le nom de la file d’attente au constructeur d’attribut. 


### <a name="create-a-single-queue-message-in-a-non-async-function"></a>Créer un message de la file d’attente unique dans une fonction non-async

L’exemple de code suivant utilise un paramètre de sortie pour créer un nouveau message dans la file d’attente nommée « outputqueue » avec le même contenu que le message reçu dans la file d’attente nommée « inputqueue ».

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

Le paramètre de sortie pour la création d’un message de la file d’attente unique peut être un des types suivants :

* `string`
* `byte[]`
* `BrokeredMessage`
* Un type sérialisable POCO que vous définissez. Automatiquement sérialisée au format JSON.

Pour les paramètres de type POCO, un message de la file d’attente est toujours créé lorsque la fonction se termine ; Si le paramètre est null, le Kit de développement crée un message de la file d’attente qui retournera la valeur null lorsque le message est reçu et désérialisé. Pour les autres types, si le paramètre est null aucun message de la file d’attente n’est créée.

### <a name="create-multiple-queue-messages-or-in-async-functions"></a>Créer plusieurs messages de la file d’attente ou dans des fonctions d’async

Pour créer plusieurs messages, utilisez le `ServiceBus` d’attribut avec `ICollector<T>` ou `IAsyncCollector<T>`, comme illustré dans l’exemple de code suivant :

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Chaque message de la file d’attente est créée immédiatement lors de la `Add` méthode est appelée.

## <a id="topics"></a>L’utilisation des thèmes du Bus de Service

Pour écrire une fonction qui le Kit de développement appelle lorsqu’un message est reçu sur un sujet de Bus des services, utilisez le `ServiceBusTrigger` l’attribut avec le constructeur qui prend le nom de la rubrique et le nom de l’abonnement, comme illustré dans l’exemple de code suivant :

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

Pour créer un message dans une rubrique, utilisez les `ServiceBus` attribut avec un nom de rubrique la même manière que vous l’utilisez avec un nom de file d’attente.

## <a name="features-added-in-release-11"></a>Fonctionnalités ajoutées à la version 1.1

Les fonctionnalités suivantes ont été ajoutées dans la version 1.1 :

* Autoriser la personnalisation complète de traitement par le biais du message `ServiceBusConfiguration.MessagingProvider`.
* `MessagingProvider`prend en charge la personnalisation du Service Bus `MessagingFactory` et `NamespaceManager`.
* A `MessageProcessor` modèle de stratégie vous permet de spécifier un processeur de file d’attente/rubrique.
* Accès concurrentiel de traitement de message est pris en charge par défaut. 
* Facilite la personnalisation de `OnMessageOptions` par `ServiceBusConfiguration.MessageOptions`.
* Autoriser [AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) à spécifier sur `ServiceBusTriggerAttribute` / `ServiceBusAttribute` (pour les scénarios où vous ne disposez pas des droits de gestion). 

## <a id="queues"></a>Rubriques connexes couvertes par l’article sur les procédures de files d’attente de stockage

Pour plus d’informations sur les scénarios de WebJobs SDK non spécifiques au Bus de Service, voir [comment utiliser le stockage Azure de file d’attente avec le SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Les sujets abordés dans cet article sont les suivants :

* Fonctions d’async
* Plusieurs instances
* Arrêt progressif
* Utilisez les attributs WebJobs SDK dans le corps d’une fonction
* Définir les chaînes de connexion du Kit de développement logiciel dans le code
* Définir les valeurs pour WebJobs SDK paramètres du constructeur dans le code
* Déclencher une fonction manuellement
* Écrire des journaux

## <a id="nextsteps"></a>Étapes suivantes

Ce guide fournit des exemples de code qui montrent comment gérer des scénarios courants pour l’utilisation de Bus des services Azure. Pour plus d’informations sur l’utilisation de WebJobs d’Azure et le SDK WebJobs, reportez-vous à la section [Ressources recommandé des WebJobs d’Azure](http://go.microsoft.com/fwlink/?linkid=390226).
 
