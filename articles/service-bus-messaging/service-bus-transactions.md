<properties 
    pageTitle="Transactions de Bus de service | Microsoft Azure" 
    description="Vue d’ensemble des transactions atomiques du Bus des services Azure et envoyer par" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="10/04/2016"
    ms.author="clemensv;sethm"/>

# <a name="overview-of-service-bus-transaction-processing"></a>Vue d’ensemble du traitement des transactions de Bus des services

Cet article décrit les fonctionnalités de transaction de Bus des services Azure. La plupart de la discussion est illustrée par les [Transactions atomiques avec exemple de Bus de Service](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions). Cet article est limité à une vue d’ensemble du traitement des transactions et de la fonctionnalité *Envoyer via le* Bus des services, tandis que l’échantillon de Transactions atomiques est plus large et plus complexe dans la portée.

## <a name="transactions-in-service-bus"></a>Transactions dans le Bus des services

Une [transaction](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions#what-are-transactions) regroupe deux ou plusieurs opérations dans une *portée de l’exécution*. Par nature, une telle transaction doit s’assurer que toutes les opérations appartenant à un groupe donné d’opérations réussissent ou échouent ensemble. À cet égard les transactions agissent comme une seule unité, ce qui est souvent appelée *atomicité*. 

Bus de service est un service Broker pour les messages transactionnels et garantit une intégrité transactionnelle pour toutes les opérations internes sur les banques de messages. Tous les transferts de messages à l’intérieur de Bus de Service, telles que le déplacement des messages à une [file d’attente de lettres mortes](service-bus-dead-letter-queues.md) ou le [transfert automatique](service-bus-auto-forwarding.md) des messages entre les entités sont transactionnelles. En tant que tel, si le Bus de Service accepte un message, il a déjà été stocké et étiqueté avec un numéro de séquence. Par la suite, les transferts de message dans le Bus de Service sont des opérations coordonnées entre les entités, et ni entraîne des pertes (source réussit et cible échoue) ou à la duplication (défaillance de la source et cible réussit) du message.

Bus de service prend en charge les opérations de regroupement par rapport à une seule entité messagerie (file d’attente, rubrique, abonnement) dans la portée d’une transaction. Par exemple, vous pouvez envoyer plusieurs messages à une file d’attente à partir d’une étendue de transaction, et les messages seront uniquement validées dans le journal de la file d’attente lorsque la transaction se termine avec succès.

## <a name="operations-within-a-transaction-scope"></a>Opérations au sein d’une étendue de transaction 

Les opérations pouvant être effectuées dans une portée de transaction sont les suivantes :

- ** [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx), [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)**: envoyer, SendAsync, SendBatch, SendBatchAsync 

- **[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)**: complète, CompleteAsync, Abandon, AbandonAsync, lettres mortes, DeadletterAsync, différer, DeferAsync, RenewLock, RenewLockAsync 

Recevoir des opérations ne sont pas incluses, car il est supposé que l’application acquiert des messages à l’aide du mode [ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , à l’intérieur de certaines reçoivent la boucle ou avec un [OnMessage](https://msdn.microsoft.com/library/azure/dn369601.aspx) rappel et seulement une étendue de transaction pour le traitement du message s’ouvre.

Ensuite, la disposition du message (terminé, abandon, lettres mortes, différer) se produit au sein de l’étendue et en fonction, le résultat global de la transaction.

## <a name="transfers-and-send-via"></a>Transferts et « envoyer »

Pour activer le transfert transactionnel des données à partir d’une file d’attente à un processeur, puis vers une autre file, Bus de Service prend en charge les *transferts*. Dans une opération de transfert, un expéditeur envoie tout d’abord un message à une file de « transfert » et la file d’attente de transfert déplace immédiatement le message vers la file d’attente de destination prévue à l’aide de la même implémentation de transfert fiable qui dépend de la capacité de transfert automatique. Le message n’est jamais validé au journal de la file d’attente transfert de sorte qu’il devienne visible pour les utilisateurs de la file d’attente transfert.

La puissance de cette fonctionnalité transactionnelle devient évidente lorsque la file d’attente de transfert lui-même est la source des messages d’entrée de l’expéditeur. En d’autres termes, le Bus des services peut transférer le message vers la file d’attente de destination « via » de la file d’attente de transfert, lors de l’exécution complète (ou différer, ou mortes) sur le message d’entrée, en une seule opération atomique. 

### <a name="see-it-in-code"></a>Voir dans le code

Pour configurer ce transfert, vous créez un expéditeur de message ciblant la file d’attente de destination via la file d’attente de transfert. Vous disposez également d’un récepteur qui extrait les messages de cette même file d’attente. Par exemple :

```
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Une transaction simple puis utilise ces éléments, comme dans l’exemple suivant :

```
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour plus d’informations sur les files d’attente de Bus de Service :

- [Chaînage des entités de Service Bus avec transfert automatique](service-bus-auto-forwarding.md)
- [Exemple de transfert automatique](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AutoForward)
- [Transactions atomiques avec exemple de Bus des services](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)
- [Files d’attente et de Bus des services Azure files d’attente par rapport](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [L’utilisation de files d’attente de Bus de Service](service-bus-dotnet-get-started-with-queues.md)