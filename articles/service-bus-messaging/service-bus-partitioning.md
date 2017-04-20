<properties 
    pageTitle="Partitionné de files d’attente et les rubriques | Microsoft Azure"
    description="Décrit comment partitionner des rubriques et des files d’attente de Bus de Service à l’aide de plusieurs courtiers de messages."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm;hillaryc" />

# <a name="partitioned-queues-and-topics"></a>Rubriques et partitionnées de files d’attente

Bus des services Azure utilise plusieurs courtiers de messages pour traiter les messages et les banques de messagerie pour stocker les messages. Une rubrique ou une file d’attente conventionnelle est géré par un agent de message unique et stockée dans une banque d’informations. Bus de service permet également de files d’attente ou de rubriques pour être partitionnées entre plusieurs courtiers de messages et les banques de messagerie. Cela signifie que le débit global d’une rubrique ou une file d’attente partitionnée n’est plus limité par les performances d’un courtier de message unique ou de la banque d’informations. En outre, une panne temporaire d’une banque d’information ne restitue pas un partitionnées de file d’attente ou une rubrique non disponible. Rubriques et les files d’attente partitionnées peuvent contenir toutes les Bus de Service fonctionnalités avancées, telles que la prise en charge des transactions et des sessions.

Pour plus d’informations sur les éléments internes du Bus des services, consultez la rubrique de [l’architecture de Bus de Service][] .

## <a name="how-it-works"></a>Mode de fonctionnement

Chaque file d’attente partitionnée ou la rubrique se compose de plusieurs fragments. Chaque fragment est stocké dans un autre magasin de messagerie et géré par un agent de message différent. Lorsqu’un message est envoyé à une file d’attente partitionnée ou la rubrique, Bus de Service affecte le message à l’un des fragments. La sélection s’effectue au hasard par le Bus de Service ou à l’aide d’une clé de partition que l’expéditeur peut spécifier.

Lorsqu’un client souhaite recevoir un message d’une file d’attente partitionnée ou d’un abonnement d’une rubrique partitionnée, les requêtes de Service Bus tous les fragments de messages, puis retourne le premier message qui est obtenu à partir d’une des banques de messagerie au destinataire. L’autre les messages et les renvoie lorsqu’il reçoit plus les caches de Bus de service reçoivent des demandes. Un client du destinataire n’est pas conscient de ce partitionnement ; le comportement de clients d’une rubrique ou une file d’attente partitionnée (par exemple, lire, terminer, différer, lettres mortes, lecture anticipée) est identique à celui d’une entité standard.

Il n’existe aucun coût supplémentaire lors de l’envoi d’un message à, ou recevez un message d’une file d’attente partitionnée ou la rubrique.

## <a name="enable-partitioning"></a>Activer le partitionnement

Pour utiliser partitionnées de files d’attente et les rubriques avec Bus des services Azure, utiliser le SDK Azure version 2.2 ou version ultérieure, ou spécifier `api-version=2013-10` dans votre HTTP demande.

Vous pouvez créer des files d’attente de Bus de services et rubriques dans les tailles de 1, 2, 3, 4 ou 5 Go (la valeur par défaut est de 1 Go). Partitionnement activé, Bus de Service crée 16 partitions pour chaque Go que vous spécifiez. Par conséquent, si vous créez une file d’attente est de 5 Go en taille, avec 16 partitions la taille maximale de file d’attente devient (5 \* 16) = 80 Go. Vous pouvez voir la taille maximale de votre file d’attente partitionnée ou une rubrique en examinant son entrée sur le [portail Azure][].

Il existe plusieurs façons de créer une rubrique ou une file d’attente partitionnée. Lorsque vous créez la file d’attente ou une rubrique à partir de votre application, vous pouvez activer le partitionnement de la file d’attente ou une rubrique en affectant à la propriété [QueueDescription.EnablePartitioning][] ou [TopicDescription.EnablePartitioning][] respectivement à **true**. Ces propriétés doivent être définies au moment de la file d’attente ou la rubrique est créée. Il n’est pas possible de modifier ces propriétés dans une file d’attente ou une rubrique. Par exemple :

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Vous pouvez également créer une rubrique ou une file d’attente partitionnée dans Visual Studio ou dans le [portail Azure][]. Lorsque vous créez une nouvelle file d’attente ou une rubrique dans le portail, définissez l’option **Activer le partitionnement** dans la lame de **Paramètres généraux** de la file d’attente ou la rubrique fenêtre **paramètres** **true**. Dans Visual Studio, cliquez sur la case à cocher **Activer la partition** dans la boîte de dialogue **Nouvelle file d’attente** ou de la **Nouvelle rubrique** .

## <a name="use-of-partition-keys"></a>Utilisation de clés de la partition

Lorsqu’un message est placé dans une file d’attente partitionnée ou la rubrique, Bus de Service vérifie la présence d’une clé de partition. S’il en trouve un, il sélectionne le fragment basé sur cette clé. S’il ne trouve pas une clé de partition, il sélectionne le fragment basé sur un algorithme interne.

### <a name="using-a-partition-key"></a>À l’aide d’une clé de partition

Certains scénarios, tels que les sessions ou les transactions, demander que les messages soient stockés dans un fragment spécifique. Tous ces scénarios requièrent l’utilisation d’une clé de partition. Tous les messages qui utilisent la même clé de partition sont affectés à la même fragment. Si le fragment est temporairement indisponible, le Bus de Service renvoie une erreur.

Selon le scénario, les propriétés de message différents sont utilisées comme clé de partition :

**ID de session**: si un message [BrokeredMessage.SessionId][] la propriété a la valeur, alors que le Bus des services utilise cette propriété comme clé de partition. De cette façon, tous les messages qui appartiennent à la même session sont gérés par le même courtier de messages. Ainsi, le Bus des services afin de garantir l’ordre et la cohérence des États de session de message.

**PartitionKey**: si un message a la propriété [BrokeredMessage.PartitionKey][] , mais pas la propriété [BrokeredMessage.SessionId][] est définie, puis le Bus des services utilise la propriété [PartitionKey][] en tant que clé de partition. Si le message est à la fois l' [ID de session][] et le jeu de propriétés de [PartitionKey][] , les deux propriétés doivent être identiques. Si la propriété [PartitionKey][] est définie à une valeur différente de la propriété [SessionId][] , le Bus de Service renvoie une exception **InvalidOperationException** . La propriété [PartitionKey][] doit être utilisée si un expéditeur envoie les messages transactionnels prenant en charge non-session. Clé de partition permet de s’assurer que tous les messages qui sont envoyés dans une transaction sont gérées par le même courtier de messagerie.

**MessageId**: si la file d’attente ou la rubrique [QueueDescription.RequiresDuplicateDetection][] la propriété a la valeur **true** et que les propriétés [BrokeredMessage.SessionId][] ou [BrokeredMessage.PartitionKey][] ne sont pas définies, alors que la propriété [BrokeredMessage.MessageId][] sert de clé de partition. (Notez que les bibliothèques Microsoft .NET et AMQP affecter automatiquement un ID de message si l’application émettrice ne le fait pas.) Dans ce cas, toutes les copies du même message sont gérées par le même courtier de messages. Ainsi, le Bus de Service détecter et éliminer les doublons de messages. Si la propriété [QueueDescription.RequiresDuplicateDetection][] n’est pas définie sur **true**, le Bus des services ne tient pas compte de la propriété [MessageId][] sous la forme d’une clé de partition.

### <a name="not-using-a-partition-key"></a>N’utilisez ne pas une clé de partition

En l’absence d’une clé de partition, Bus de Service distribue des messages de façon alternée à tous les fragments de la rubrique ou la file d’attente partitionnée. Si le fragment choisi n’est pas disponible, le Bus de Service affecte le message à un autre fragment. De cette façon, l’opération d’envoi réussit en dépit de l’indisponibilité provisoire d’une banque d’informations.

Pour donner le Bus de Service suffisamment de temps pour la file d’attente le message dans un autre fragment, la valeur de [MessagingFactorySettings.OperationTimeout][] spécifiée par le client qui envoie le message doit être supérieure à 15 secondes. Il est recommandé de définir la propriété [OperationTimeout][] la valeur par défaut de 60 secondes.

Notez qu’une clé de partition « épingle » un message à un fragment spécifique. Si la banque d’informations qui contient ce fragment n’est pas disponible, le Bus de Service renvoie une erreur. En l’absence d’une clé de partition, Bus de Service peuvent choisir un autre fragment, et l’opération réussit. Par conséquent, il est recommandé que vous ne fournissez pas une clé de partition sauf s’il est requis.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Rubriques avancées : utilisez des transactions avec entités partitionnées

Les messages qui sont envoyés dans le cadre d’une transaction doivent spécifier une clé de partition. Cela peut être une des propriétés suivantes : [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][]ou [BrokeredMessage.MessageId][]. Tous les messages qui sont envoyés dans le cadre de la même transaction doivent spécifier la même clé de partition. Si vous essayez d’envoyer un message sans une clé de partition dans une transaction, le Bus de Service renvoie une exception **InvalidOperationException** . Si vous tentez d’envoyer plusieurs messages dans la même transaction possédant des clés de l’autre partition, Bus de Service renvoie une exception **InvalidOperationException** . Par exemple :

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Si les propriétés qui servent une clé de partition sont définies, Bus de Service d’épingle le message à un fragment spécifique. Ce problème se produit si une transaction est utilisée ou non. Il est recommandé que vous ne spécifiez pas une clé de partition si elle n’est pas nécessaire.

## <a name="using-sessions-with-partitioned-entities"></a>À l’aide de sessions avec les entités partitionnées

Pour envoyer un message transactionnel à un sujet de session prenant en charge ou de la file d’attente, le message doit être la propriété [BrokeredMessage.SessionId][] définie. Si la propriété [BrokeredMessage.PartitionKey][] est également spécifiée, elle doit être identique à la propriété [SessionId][] . Si elles diffèrent, le Bus de Service renvoie une exception **InvalidOperationException** .

Contrairement aux files d’attente (non partitionné) régulières ou rubriques, il n’est pas possible d’utiliser une transaction unique pour envoyer plusieurs messages au cours de sessions différentes. Dans ce cas, le Bus de Service renvoie une exception **InvalidOperationException **. Par exemple :

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Transfert automatique des messages avec des entités partitionnées

Bus des services Azure prend en charge le transfert automatique des messages à partir de, à ou entre des entités partitionnées. Pour activer le transfert automatique des messages, définissez la propriété [QueueDescription.ForwardTo][] sur la file d’attente source ou l’abonnement. Si le message spécifie une clé de partition ([SessionId][], [PartitionKey][]ou [MessageId][]), cette clé de partition est utilisée pour l’entité de destination.

## <a name="considerations-and-guidelines"></a>Considérations et les instructions

- **Fonctionnalités de cohérence élevé**: si une entité utilise des fonctionnalités telles que des sessions, la détection des doublons ou un contrôle explicite de la clé de partitionnement, alors que les opérations de messagerie sont toujours routées vers fragments spécifiques. Si un des fragments rencontrer un trafic élevé ou le magasin sous-jacent ne fonctionne pas correctement, ces opérations échouent et disponibilité est réduite. En général, la cohérence est bien plue importante que les entités non partitionnée ; uniquement un sous-ensemble du trafic rencontre des problèmes, plutôt que de tout le trafic.
- **Gestion**: les opérations Create, Update et Delete doivent être effectuées sur tous les fragments de l’entité. Si n’importe quel fragment est défectueux il peut entraîner des échecs pour ces opérations. Pour l’opération d’obtention des informations telles que le nombre de messages doivent être regroupées à partir de tous les fragments. Si n’importe quel fragment est incorrect, l’état de disponibilité d’entité est signalée comme limitée.
- **Scénarios de message de faible volume**: pour de tels scénarios, en particulier lorsque l’utilisation du protocole HTTP, vous devrez peut-être exécuter plusieurs opérations de réception afin d’obtenir tous les messages. Pour les demandes de réception, le front-end effectue une réception sur tous les fragments et les caches de toutes les réponses reçues. Une demande de réception suivantes sur la même connexion devrait bénéficier de cette mise en cache et de recevoir des latences sera inférieures. Toutefois, si vous avez plusieurs connexions ou que vous utilisez le protocole HTTP, qui établit une nouvelle connexion pour chaque demande. En tant que tel, il n’y a aucune garantie qu’il serait débarquer sur le même nœud. Si tous les messages existants sont verrouillés et mis en cache dans un autre front-end, l’opération de réception renvoie la valeur **null**. Messages arriveront à expiration et vous pouvez les recevoir à nouveau. HTTP keep-alive est recommandé.
- **Parcourir/lire des messages**: PeekBatch ne renvoie pas toujours le nombre de messages spécifié dans la [propriété du MessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecount.aspx). Il existe deux raisons principales à cela. L’une des raisons sont que la taille de la synthèse de la collection de messages dépasse la taille maximale de 256 Ko. Une autre raison est que si la file d’attente ou une rubrique a la [propriété du EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) la valeur **true**, une partition peut-être pas suffisamment de messages pour effectuer le nombre demandé de messages. En règle générale, si une application souhaite recevoir un certain nombre de messages, il doit appeler [PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) à plusieurs reprises jusqu'à ce qu’il obtient le nombre de messages, ou plus, aucun message à lire. Pour plus d’informations, y compris des exemples de code, consultez [QueueClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) ou [SubscriptionClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx).

## <a name="latest-added-features"></a>Dernières nouvelles fonctionnalités

- Ajouter ou supprimer des règles est désormais pris en charge avec les entités partitionnées. Diffère les entités non partitionnée, ces opérations ne sont pas pris en charge dans les transactions. 
- AMQP est désormais pris en charge pour envoyer et recevoir des messages vers et à partir d’une entité partitionnée.
- AMQP est désormais pris en charge pour les opérations suivantes : [Lot envoyer](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.sendbatch.aspx), [Lot recevoir](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.receivebatch.aspx), [réception, par numéro de séquence](https://msdn.microsoft.com/library/azure/hh330765.aspx), [lire](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx), [Renouveler le verrou](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.renewmessagelock.aspx), [Message d’agenda](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.schedulemessageasync.aspx), [Annuler le Message planifié](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync.aspx), [Ajouter une règle](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Supprimer une règle](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Verrouillage de Session renouveler](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx), [Définir l’état de Session](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx), [Obtenir l’état de Session](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx), [Lire des Messages de Session](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.peek.aspx)et [Énumérer les Sessions](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.getmessagesessionsasync.aspx).

## <a name="partitioned-entities-limitations"></a>Limitations des entités partitionnées

Actuellement les Bus de Service impose les restrictions suivantes sur des files d’attente partitionnées et rubriques :

-   Rubriques et partitionnées de files d’attente ne gèrent pas l’envoi de messages qui appartiennent à différentes sessions en une seule transaction.
-   Bus de service autorise actuellement jusqu'à 100 partitionnées de files d’attente ou les rubriques par espace de noms. Chaque file d’attente partitionnée ou la rubrique compte vers le quota de 10 000 entités par espace de noms (ne s’applique pas au niveau de la prime).

## <a name="next-steps"></a>Étapes suivantes

Consultez la rubrique sur la [prise en charge de AMQP 1.0 pour le Bus de Service partitionnée de files d’attente et des rubriques][] pour en savoir plus sur le partitionnement des entités de messagerie. 

  [Architecture de Bus de service]: service-bus-architecture.md
  [Azure portal]: https://portal.azure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [ID de session]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [Prise en charge de AMQP 1.0 pour les files d’attente de Bus de Service partitionnée et rubriques]: service-bus-partitioned-queues-and-topics-amqp-overview.md
