<properties 
    pageTitle="Prise en charge de AMQP 1.0 pour le Bus de Service partitionnée de files d’attente et les rubriques | Microsoft Azure" 
    description="Apprenez à utiliser les rubriques et les files d’attente de Advanced Message Queuing Protocol (AMQP) 1.0 avec Bus de Service partitionnée." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="hillaryc" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="hillaryc;sethm"/>

# <a name="amqp-10-support-for-service-bus-partitioned-queues-and-topics"></a>Prise en charge de AMQP 1.0 pour les files d’attente de Bus de Service partitionnée et rubriques 

Bus des services Azure prend désormais en charge le Advanced Message Queuing Protocol (**AMQP**) 1.0 pour le Bus de Service **partitionné de files d’attente et des rubriques.**

**AMQP** est un protocole de files d’attente de messages standard ouvert qui vous permet de développer des applications multiplates-formes à l’aide de différents langages de programmation. Pour des informations générales sur AMQP prise en charge dans le Bus des services, consultez [1.0 AMQP prise en charge dans le Bus de Service](service-bus-amqp-overview.md).

**Les rubriques et les files d’attente de partitionnées**, également appelés *entités partitionnées*, offrent plus de disponibilité, de fiabilité et de débit que les files d’attente non partitionnée conventionnels et les rubriques. Pour plus d’informations sur les entités partitionnées, voir [Entités de messagerie partitionnées](service-bus-partitioning.md).

L’ajout de AMQP 1.0 en tant que protocole pour communiquer avec les rubriques et les files d’attente partitionnées vous permet de concevoir des applications interopérables qui peuvent tirer parti de la disponibilité, la fiabilité et tout au long d’offertes par les entités du Bus de Service partitionnée.

Pour plus d’informations au niveau du câble AMQP 1.0 protocol, qui explique comment le Bus de Service implémente et repose sur la spécification technique OASIS AMQP, consultez le [guide de protocole 1.0 AMQP dans le Bus des services Azure et concentrateurs d’événement](service-bus-amqp-protocol-guide.md).    

## <a name="use-amqp-with-partitioned-queues"></a>Utiliser AMQP avec des files d’attente partitionnées

Files d’attente sont utiles pour les scénarios qui requièrent de découplage temporelle, charge l’audit, l’équilibrage de charge et un couplage lâche. Une file d’attente, éditeurs envoyer des messages à la file d’attente et les consommateurs reçoivent les messages de la file d’attente, dans les situations où un message peut être reçu uniquement une fois. Un bon exemple de cela est un système de stock dans lequel les terminaux point de vente pour publier les données sur une file d’attente et non directement sur le système de gestion de stock. Le système de gestion de stock utilise ensuite les données à tout moment pour gérer le réapprovisionnement de stock. Cela a plusieurs avantages, notamment le système de gestion de stock n’a ne pas à être en ligne à tout moment. Pour plus d’informations sur les files d’attente de Bus de Service, reportez-vous à la section [créer les applications qui utilisent des files d’attente de Bus de Service](service-bus-create-queues.md). 

Une file d’attente partitionnée supplémentaire augmente la disponibilité, la fiabilité et le débit pour les applications, ces files d’attente sont partitionnées sur plusieurs courtiers de messages et les banques de messagerie.     

### <a name="create-partitioned-queues"></a>Créer des files d’attente partitionnées

Vous pouvez créer une file d’attente partitionnée à l’aide du [Azure portal classique][] et le Kit de développement de Bus de Service. Pour créer une file d’attente partitionnée, définir la propriété [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) sur **true** dans l’instance de [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) . Le code suivant montre comment créer une file d’attente partitionnée en utilisant le Kit de développement de Bus de Service. 
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Envoyer et recevoir des messages à l’aide d’AMQP

Vous pouvez envoyer des messages et recevoir des messages d’une file d’attente partitionnée à l’aide de AMQP en tant que protocole, en affectant à la propriété [type de transport](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) comme indiqué dans le code suivant.  

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="use-amqp-with-partitioned-topics"></a>Utiliser AMQP avec des rubriques partitionnées

Les rubriques sont conceptuellement semblables aux files d’attente, mais les rubriques peuvent acheminer une copie du même message à plusieurs *abonnements*. Dans une rubrique, éditeurs envoient des messages à la rubrique et les consommateurs reçoivent les messages à partir des abonnements. Dans le scénario de système de point de vente de stock, terminaux de publier des données dans la rubrique. Le système de gestion de stock reçoit ensuite des messages à partir d’un abonnement. En outre, un système de surveillance peut le même message à partir d’un autre abonnement. Pour plus d’informations sur les rubriques du Bus de Service et des abonnements, consultez [créer les applications qui utilisent des abonnements et des rubriques du Bus de Service](service-bus-create-topics-subscriptions.md). 

Comme avec les files d’attente, sujets partitionnées augmentent la disponibilité, la fiabilité et le débit pour les applications, que leurs abonnements et ces rubriques sont partitionnées sur plusieurs courtiers de messages et les banques de messagerie. 

### <a name="create-partitioned-topics"></a>Créer des rubriques partitionnées

Vous pouvez créer une rubrique partitionnée à l’aide du [Azure portal classique][] et le Kit de développement de Bus de Service. Pour créer une rubrique partitionnée, définir la propriété [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) sur **true** dans l’instance de [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . Le code suivant montre comment créer une rubrique partitionnée en utilisant le Kit de développement de Bus de Service.
    
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Envoyer et recevoir des messages à l’aide d’AMQP

Vous pouvez envoyer des messages et recevoir des messages à partir d’un abonnement de rubrique partitionnées à l’aide de AMQP en tant que protocole, en affectant à la propriété [type de transport](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), comme illustré dans le code suivant.  

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
    
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
    
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les informations supplémentaires suivantes pour plus d’informations sur les entités de messagerie partitionnées ainsi que AMQP.

*    [Entités de messagerie partitionnées](service-bus-partitioning.md)
*    [OASIS Advanced Message Queuing (AMQP) Version 1.0 du protocole](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Prise en charge de AMQP 1.0 dans le Bus des services](service-bus-amqp-overview.md)
*    [1.0 AMQP dans le guide de protocole de Bus des services Azure et concentrateurs d’événement](service-bus-amqp-protocol-guide.md)
*    [L’utilisation de l’API Java Message Service (JMS) avec Bus de Service et de AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [L’utilisation de AMQP 1.0 avec l’API .NET du Bus de Service](service-bus-dotnet-advanced-message-queuing.md)

[Azure portal classique]: http://manage.windowsazure.com
