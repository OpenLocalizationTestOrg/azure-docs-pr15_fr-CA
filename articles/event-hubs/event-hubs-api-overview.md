<properties 
    pageTitle="Vue d’ensemble des API de concentrateurs d’événement Azure | Microsoft Azure"
    description="Un résumé de quelques du client .NET de concentrateurs d’événement clé API."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm" />

# <a name="event-hubs-api-overview"></a>Vue d’ensemble des API de concentrateurs événements

Cet article résume quelques-unes des principales API du client .NET de concentrateurs d’événement. Il existe deux catégories : gestion et API d’exécution. API d’exécution sont constitués de toutes les opérations nécessaires pour envoyer et recevoir un message. Opérations de gestion permettent de gérer un état d’entité de concentrateurs d’événement par la création, la mise à jour et suppression d’entités.

Scénarios d’analyse s’étendent de gestion et exécution. Pour une documentation de référence détaillées sur les API .NET, reportez-vous aux références de [Service Bus.NET](https://msdn.microsoft.com/library/azure/mt419900.aspx) et [API de EventProcessorHost](https://msdn.microsoft.com/library/azure/mt445521.aspx) .

## <a name="management-apis"></a>API de gestion

Pour effectuer les opérations de gestion suivant, vous devez disposer des **Gérer** les autorisations sur l’espace de noms de concentrateurs de l’événement :

### <a name="create"></a>Créer

```
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### <a name="update"></a>Mise à jour

```
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### <a name="delete"></a>Supprimer

```
namespaceManager.DeleteEventHubAsync("Event Hub name").Wait();
```

## <a name="run-time-apis"></a>API d’exécution

### <a name="create-publisher"></a>Créer une publication

```
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("Event Hub name");
```

### <a name="publish-message"></a>Publier le message

```
// Create the device/temperature metric
MetricEvent info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
EventData data = new EventData(new byte[10]); // Byte array
EventData data = new EventData(Stream); // Stream 
EventData data = new EventData(info, serializer) //Object and serializer 
    {
       PartitionKey = info.DeviceId.ToString()
    };

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Créer consommateur

```
// Create the Event Hubs client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
EventHubConsumerGroup defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));
                        
// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Consommer le message

```
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)
                                    
// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Hôte du processeur d’événements API

Ces API fournit des capacités de résistance au processus de travail peut devenir indisponible, en répartissant les milieu des fragments sur la disposition des employés.

```
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

string eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
string blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

EventHubDescription eventHubDescription = new EventHubDescription(EventHubName);
EventProcessorHost host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
            host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
host.UnregisterEventProcessorAsync().Wait();   
```

L’interface [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) est défini comme suit :

```
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            Process messages here
        }
        
        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }


    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les scénarios de concentrateurs d’événement, consultez ces liens :

- [Ce qui est des concentrateurs d’événement Azure ?](event-hubs-what-is-event-hubs.md)
- [Vue d’ensemble des concentrateurs événements](event-hubs-overview.md)
- [Concentrateurs d’événement guide de programmation](event-hubs-programming-guide.md)
- [Exemples de code événement concentrateurs](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hubs&f[0].Type=SearchText&ac=5)

Les références de l’API .NET sont ici :

- [Références de service Bus et les API .NET de concentrateurs événement](https://msdn.microsoft.com/library/azure/mt419900.aspx)
- [Référence de l’API hôte du processeur d’événements](https://msdn.microsoft.com/library/azure/mt445521.aspx)
