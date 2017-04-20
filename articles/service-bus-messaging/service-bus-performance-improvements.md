<properties 
    pageTitle="Meilleures pratiques pour améliorer les performances à l’aide du Bus de Service | Microsoft Azure"
    description="Décrit comment utiliser le Bus des services Azure pour optimiser les performances lors de l’échange de messages de courtage."
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
    ms.date="10/25/2016"
    ms.author="sethm" />

# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Meilleures pratiques pour améliorer les performances à l’aide de la messagerie de Bus de Service

Cette rubrique décrit l’utilisation de la messagerie de Bus de Service Azure pour optimiser les performances lors de l’échange de messages de courtage. La première partie de cette rubrique décrit les différents mécanismes qui sont proposées afin d’augmenter les performances. La seconde partie fournit des conseils sur l’utilisation des Bus de Service d’une manière qui peut offrir les meilleures performances dans un scénario donné.

Tout au long de cette rubrique, le terme « client » fait référence à toute entité qui accède au Service Bus. Un client peut prendre le rôle d’un expéditeur ou un destinataire. Le terme « sender » est utilisé pour un client de file d’attente ou rubrique Bus de Service qui envoie des messages à une file d’attente de Bus de Service ou d’une rubrique. Le terme « receiver » fait référence à un client d’abonnement ou de la file d’attente de Bus de Service qui reçoit les messages d’une file d’attente de Bus de Service ou d’abonnement.

Ces rubriques présentent plusieurs concepts qui utilise des Bus de Service pour aider à augmenter les performances.

## <a name="protocols"></a>Protocoles

Bus de service permet aux clients d’envoyer et de recevoir des messages via trois protocoles

1. Avancées Message Queuing Protocol (AMQP)

2. Bus de service (SBMP) de protocole de messagerie

3. HTTP

À la fois AMQP et SBMP sont plus efficaces, parce qu’ils maintiennent la connexion au Bus de Service tant que la fabrique de messagerie existe. Il implémente également le traitement par lots et la lecture préalable. Sauf mention explicite, tout le contenu de cette rubrique suppose l’utilisation de AMQP ou SBMP.

## <a name="reusing-factories-and-clients"></a>Réutilisation des usines et des clients

Objets de client de Bus des services, tels que [QueueClient][] ou [MessageSender][], sont créés via un objet [MessagingFactory][] , qui fournit également une gestion interne de connexions. Vous ne devez pas fermer les fabriques de messagerie ou file d’attente, le sujet et clients de l’abonnement après l’envoi d’un message et recréer ensuite lorsque vous envoyez le message suivant. Fermeture d’une usine de messagerie supprime la connexion au service de Bus des services, et une nouvelle connexion est établie lors de la recréation de l’usine. Établissement d’une connexion est une opération coûteuse qui vous pouvez éviter en utilisant de nouveau la même usine et les objets client pour plusieurs opérations. Vous pouvez utiliser en toute sécurité de l’objet [QueueClient][] pour l’envoi de messages à partir de plusieurs threads et les opérations asynchrones simultanées. 

## <a name="concurrent-operations"></a>Opérations simultanées

Exécution d’une opération (envoyer, recevoir, supprimer, etc.) prend un certain temps. Ce temps inclut le traitement de l’opération par le service de Bus de Service en plus de la latence de la demande et la réponse. Pour augmenter le nombre d’opérations par heure, les opérations doivent s’exécuter simultanément. Vous pouvez pour cela de différentes manières :

-   **Les opérations asynchrones**: le client planifie les opérations en effectuant des opérations asynchrones. La requête suivante est démarrée avant que la requête précédente soit terminée. Voici un exemple d’une opération d’envoi asynchrone :

    ```
    BrokeredMessage m1 = new BrokeredMessage(body);
    BrokeredMessage m2 = new BrokeredMessage(body);
    
    Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
      {
        Console.WriteLine("Sent message #1");
      });
    Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
      {
        Console.WriteLine("Sent message #2");
      });
    Task.WaitAll(send1, send2);
    Console.WriteLine("All messages sent");
    ```

    Il s’agit d’un exemple d’opération de réception asynchrone :
    
    ```
    Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
    Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
    
    Task.WaitAll(receive1, receive2);
    Console.WriteLine("All messages received");
    
    async void ProcessReceivedMessage(Task<BrokeredMessage> t)
    {
      BrokeredMessage m = t.Result;
      Console.WriteLine("{0} received", m.Label);
      await m.CompleteAsync();
      Console.WriteLine("{0} complete", m.Label);
    }
    ```

-   **Plusieurs unités de fabrication**: tous les clients (expéditeurs en plus des récepteurs) qui sont créés par l’usine même de partagent une connexion TCP. Le débit maximal de messages est limité par le nombre d’opérations que vous pouvez accéder par le biais de cette connexion TCP. Le débit peut être obtenu avec la même usine varie considérablement avec des boucles TCP et la taille des messages. Pour obtenir des débits plus importants, vous devez utiliser plusieurs fabriques de messagerie.

## <a name="receive-mode"></a>Mode de réception

Lors de la création d’un client de la file d’attente ou d’un abonnement, vous pouvez spécifier un mode de réception : *verrouillage de lecture* ou *de réception et supprimer*. La réception par défaut le mode est [PeekLock][]. Lorsqu’il fonctionne dans ce mode, le client envoie une demande à recevoir un message de Service Bus. Une fois que le client a reçu le message, il envoie une demande pour terminer le message.

Lorsque vous définissez le mode réception pour [ReceiveAndDelete][], les deux étapes sont combinés dans une seule demande. Cela réduit le nombre total d’opérations et peut améliorer le débit global des messages. Ce gain de performances est fourni au risque de perdre des messages.

Bus de service ne gère pas les transactions pour les opérations de réception et supprimer. En outre, la sémantique de verrouillage en lecture est requise pour tous les scénarios dans lesquels le client souhaite différer ou [mortes](service-bus-dead-letter-queues.md) un message.

## <a name="client-side-batching"></a>Le traitement par lots du côté client

Le traitement par lots du côté client permet à un client de file d’attente ou la rubrique de différer l’envoi d’un message pendant une certaine période de temps. Si le client envoie des messages supplémentaires au cours de cette période, il transmet les messages en un seul lot. Le traitement par lots du côté client, est également un client de la file d’attente/abonnement au traitement par lots de plusieurs demandes **complète** dans une demande unique. Le traitement par lots n’est disponible que pour les opérations asynchrones **Envoyer** et **complète** . Opérations synchrones sont immédiatement envoyées au service de Bus de Service. Le traitement par lots ne pas se produire pour lire ou recevoir des opérations, ni le traitement par lots se produit-elle sur les clients.

Si le lot dépasse la taille maximale des messages, le dernier message est retiré du lot, et le client envoie immédiatement le lot. Le dernier message devient le premier message du lot suivant. Par défaut, un client utilise un intervalle de lot de 20 ms. Vous pouvez modifier l’intervalle de traitement par lots en définissant la propriété [BatchFlushInterval][] avant de créer la fabrique de messagerie. Ce paramètre affecte tous les clients qui sont créées par cette fabrique.

Pour désactiver le traitement par lots, définissez la propriété [BatchFlushInterval][] à **TimeSpan.Zero**. Par exemple :

```
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Le traitement par lots n’affecte pas le nombre d’opérations de messagerie facturables et est disponible uniquement pour le protocole de Bus de Service client. Le protocole HTTP ne prend pas en charge le traitement par lots.

## <a name="batching-store-access"></a>Le traitement par lots d’accès au magasin

Pour augmenter le débit d’un file d’attente/rubrique/abonnement, Service Bus lots plusieurs messages lorsqu’il écrit dans son magasin interne. Si activé sur une file d’attente ou une rubrique, écrire des messages dans la banque d’informations va être regroupés par lots. Si activé sur un abonnement ou une file d’attente, suppression des messages de la banque d’informations va être regroupés par lots. Si l’accès au magasin par lot est activée pour une entité, le Bus de Service retarde une opération d’écriture magasin concernant cette entité en jusqu'à 20 ms. Les opérations de stockage supplémentaires qui se produisent au cours de cet intervalle sont ajoutées au lot. Accès au magasin par lots affecte uniquement les opérations **Envoyer** et **complète** ; recevoir les opérations ne sont pas affectées. Accès au magasin par lots est une propriété d’une entité. Le traitement par lots se produit sur toutes les entités qui permettent l’accès au magasin par lot.

Lorsque vous créez une nouvelle file d’attente, la rubrique ou l’abonnement, accès au magasin par lot est activée par défaut. Pour désactiver l’accès au magasin par lot, définir la propriété [EnableBatchedOperations][] sur **false** avant la création de l’entité. Par exemple :

```
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Accès au magasin par lot n’affecte pas le nombre d’opérations de messagerie facturables et est une propriété d’une file d’attente, une rubrique ou un abonnement. Il est indépendant du mode réception et le protocole utilisé entre un client et le service de Bus de Service.

## <a name="prefetching"></a>Lecture anticipée

Lecture anticipée permet au client de charger des messages supplémentaires à partir du service lorsqu’il effectue une opération de réception file d’attente ou d’abonnement. Le client stocke ces messages dans un cache local. La taille du cache est déterminée par les propriétés [QueueClient.PrefetchCount][] ou [SubscriptionClient.PrefetchCount][] . Chaque client qui permet la lecture anticipée gère son propre cache. Un cache n’est pas partagé entre les clients. Si le client initie une opération de réception et son cache est vide, le service transmet un lot de messages. La taille du lot est égal à la taille de la mémoire cache ou 256 Ko, la plus faible étant retenu. Si le client initie une opération de réception et le cache contient un message, le message est pris à partir du cache.

Lorsqu’un message est préalablement, le service verrouille le message préalablement lues. En procédant ainsi, le message de préalablement lues ne peut pas être reçu par un autre récepteur. Si le destinataire ne peut pas terminer le message avant que le verrou n’expire, le message devient disponible pour les autres récepteurs. La copie préalablement lues du message reste dans le cache. Le récepteur qui utilise la copie mise en cache a expiré recevra une exception lorsqu’il essaie de terminer ce message. Par défaut, le verrouillage de message expire au bout de 60 secondes. Cette valeur peut être étendue à 5 minutes. Pour éviter la consommation de messages arrivés à expiration, la taille du cache doit toujours être inférieure au nombre de messages qui peuvent être consommés par un client au sein de l’intervalle de délai d’attente de verrou.

Lorsque vous utilisez l’expiration du verrouillage par défaut de 60 secondes, une bonne valeur pour [SubscriptionClient.PrefetchCount][] est 20 fois le maximum des taux de traitement de tous les récepteurs de la fabrique. Par exemple, une fabrique crée des récepteurs de 3, et chaque récepteur peut traiter jusqu'à 10 messages par seconde. Le nombre de lecture anticipée ne doit pas dépasser 20\*3\*10 = 600. Par défaut, [QueueClient.PrefetchCount][] est définie sur 0, ce qui signifie qu’aucun des messages supplémentaires ne sont lues à partir du service.

Lecture anticipée de messages augmente le débit global d’une file d’attente ou d’abonnement car il réduit le nombre total d’opérations de message ou de boucles. L’extraction du premier message, toutefois, prendra plus de temps (en raison de la taille du message accrue). Réception de messages préalablement lues sera plus rapide, car ces messages ont déjà été téléchargés par le client.

La propriété time-to-live de (vie TTL) d’un message est vérifiée par le serveur au moment où que le serveur envoie le message au client. Le client ne vérifie pas la propriété durée de vie du message lors de la réception du message. Au lieu de cela, le message peut être reçu même si la durée de vie du message est dépassée alors que le message a été mis en cache par le client.

Lecture anticipée n’affecte pas le nombre d’opérations de messagerie facturables et est disponible uniquement pour le protocole de Bus de Service client. Le protocole HTTP ne gère pas la lecture anticipée. Lecture anticipée n’est disponible pour les opérations de réception à la fois synchrones et asynchrones.

## <a name="express-queues-and-topics"></a>Express des rubriques et des files d’attente

Entités Express permettent un débit élevé et réduit de scénarios de latence. Avec les entités express, si un message est envoyé à une file d’attente ou une rubrique, le message n'est pas immédiatement stocké dans la banque d’informations. Au lieu de cela, elle est mise en cache dans la mémoire. Si un message reste dans la file d’attente pendant plus de quelques secondes, il est automatiquement écrite dans stable de stockage, afin de le protéger contre la perte en raison d’une panne. Écrire le message dans une mémoire cache augmente le débit et réduit la latence, car il n’y a pas d’accès au support de stockage stable au moment où que le message est envoyé. Les messages qui sont consommés au bout de quelques secondes ne sont pas écrits dans la banque d’informations. L’exemple suivant crée une rubrique expresse.

```
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Si un message contenant des informations importantes qui ne doivent pas être perdues est envoyé à une entité expresse, l’expéditeur peut forcer le Bus de Service immédiatement rendre persistant le message pour le support de stockage stable en affectant à la propriété [ForcePersistence][] la **valeur true**.

## <a name="use-of-partitioned-queues-or-topics"></a>Utilisation de files d’attente partitionnées ou de rubriques

En interne, le Bus des services utilise le même nœud et messagerie stocker pour traiter et stocker tous les messages d’une entité de messagerie (file d’attente ou rubrique). Un partitionnées de file d’attente ou une rubrique, d’autre part, est distribuée sur plusieurs nœuds et les banques de messagerie. Rubriques et partitionnées de files d’attente non seulement génèrent un débit plus élevé que les rubriques et les files d’attente normales, ils présentent également une disponibilité supérieure. Pour créer une entité partitionnée, définir la propriété [EnablePartitioning][] sur **true**, comme indiqué dans l’exemple suivant. Pour plus d’informations sur les entités partitionnées, voir [partitionnée d’entités de messagerie][].

```
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## <a name="use-of-multiple-queues"></a>Utilisation de plusieurs files d’attente

Si elle n’est pas possible d’utiliser une file d’attente partitionnée ou la rubrique ou la charge prévue ne peut pas être gérée par une seule file d’attente partitionnée ou une rubrique, vous devez utiliser plusieurs entités de messagerie. Lorsque vous utilisez plusieurs entités, créer un client dédié pour chaque entité, au lieu d’utiliser le même client pour toutes les entités.

## <a name="development--testing-features"></a>Fonctionnalités de test et de développement

Bus de service possède une fonctionnalité qui est utilisé spécifiquement pour le développement qui **ne doit jamais être utilisé dans les configurations de production**.

[TopicDescription.EnableFilteringMessagesBeforePublishing](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing.aspx)
- Lorsque de nouvelles règles ou des filtres sont ajoutés à la rubrique, EnableFilteringMessagesBeforePublishing peut être utilisé pour vérifier que la nouvelle expression de filtre fonctionne comme prévu.

## <a name="scenarios"></a>Scénarios

Les sections suivantes décrivent les scénarios de messagerie classiques et présentent les paramètres par défaut de Bus de Service. Débits sont classés comme petite (moins de 1 message par seconde), modérée (1 message/seconde ou plu, mais moins de 100 messages/seconde) et élevé (100 messages/seconde ou plus). Le nombre de clients est classé en tant que petite modéré (5 ou moins), (plus de 5, mais inférieur ou égal à 20) et de grande taille (plus de 20).

### <a name="high-throughput-queue"></a>File d’attente de haut débit

Objectif : Maximiser le débit d’une file d’attente unique. Le nombre d’expéditeurs et de destinataires est faible.

-   Utiliser une file d’attente partitionnée pour l’amélioration des performances et de disponibilité.

-   Pour augmenter la vitesse de transmission globale dans la file d’attente, utilisez plusieurs fabriques de message à créer des expéditeurs. Pour chaque expéditeur, utiliser des opérations asynchrones ou plusieurs threads.

-   Pour augmenter le taux global de la réception à partir de la file d’attente, utilisez plusieurs fabriques de message pour créer des récepteurs.

-   Pour tirer parti du traitement par lots du côté client, utiliser des opérations asynchrones.

-   Définir l’intervalle de traitement par lots d’aller-retour de 50 millisecondes pour réduire le nombre de transmissions de protocole de Bus de Service client. Si plusieurs expéditeurs sont utilisés, augmentez l’intervalle de traitement par lots de 100 millisecondes.

-   Laissez l’accès banque par lot. Cela permet d’augmenter le taux global auquel les messages puissent être écrits dans la file d’attente.

-   Définir le nombre de lecture anticipée à 20 fois le maximum des taux de traitement de tous les récepteurs d’une fabrique. Cela permet de réduire le nombre de transmissions de protocole de Bus de Service client.

### <a name="multiple-high-throughput-queues"></a>Plusieurs files d’attente de haut débit

Objectif : Optimiser le débit global de plusieurs files d’attente. Le débit d’une file d’attente est modéré ou élevé.

Pour obtenir le débit maximal sur plusieurs files d’attente, utilisez les paramètres décrits pour maximiser le débit d’une file d’attente unique. En outre, utiliser des fabriques différentes pour créer des clients qui envoient ou reçoivent des files d’attente différentes.

### <a name="low-latency-queue"></a>File d’attente de faible latence

Objectif : Réduire la latence de bout en bout d’une file d’attente ou une rubrique. Le nombre d’expéditeurs et de destinataires est faible. Le débit de la file d’attente est faible ou modérée.

-   Utiliser une file d’attente partitionnée pour améliorer la disponibilité.

-   Désactiver le traitement par lots du côté client. Le client envoie immédiatement un message.

-   Désactiver l’accès au magasin par lot. Le service écrit immédiatement le message dans la banque.

-   Si vous utilisez un seul client, définir le nombre de lecture anticipée à 20 fois le taux de traitement du récepteur. Si vous recevez plusieurs messages à la file d’attente en même temps, le protocole de Bus de Service client les transmet en même temps. Lorsque le client reçoit le message suivant, ce message est déjà dans le cache local. Le cache doit être faible.

-   Si vous utilisez plusieurs clients, définissez le nombre de lecture anticipée sur 0. Ce faisant, le second client peut le deuxième message alors que le premier client traite toujours le premier message.

### <a name="queue-with-a-large-number-of-senders"></a>File d’attente avec un grand nombre d’expéditeurs

Objectif : Augmenter le débit d’une file d’attente ou une rubrique avec un grand nombre d’expéditeurs. Chaque expéditeur envoie des messages avec un taux de niveau « modéré ». Le nombre de récepteurs est faible.

Bus de service permet jusqu'à 1000 connexions simultanées à une entité de messagerie (ou 5000 à l’aide de AMQP). Cette limite s’applique au niveau de l’espace de noms, et les files d’attente/rubriques/abonnements recouvertes par la limite de connexions simultanées par espace de noms. Pour les files d’attente, ce nombre est partagé entre les expéditeurs et les récepteurs. Si toutes les connexions de 1000 sont requises pour les expéditeurs, vous devez remplacer la file d’attente avec un sujet et un seul abonnement. Une rubrique accepte jusqu'à 1000 connexions simultanées provenant d’expéditeurs, alors que l’abonnement accepte un 1000 simultanées des connexions supplémentaires à partir de récepteurs. Si plus de 1000 expéditeurs simultanés sont nécessaires, l’expéditeur doit envoyer des messages du protocole de Bus de Service via le protocole HTTP.

Pour maximiser le débit, effectuez les opérations suivantes :

-   Utiliser une file d’attente partitionnée pour l’amélioration des performances et de disponibilité.

-   Si chaque expéditeur réside dans un processus différent, utilisez uniquement une seule fabrique par processus.

-   Pour tirer parti du traitement par lots du côté client, utiliser des opérations asynchrones.

-   La valeur par défaut de l’intervalle de 20 ms de traitement par lots permet de réduire le nombre de transmissions de protocole de Bus de Service client.

-   Laissez l’accès banque par lot. Cela permet d’augmenter le taux global auquel les messages puissent être écrits dans la file d’attente ou une rubrique.

-   Définir le nombre de lecture anticipée à 20 fois le maximum des taux de traitement de tous les récepteurs d’une fabrique. Cela permet de réduire le nombre de transmissions de protocole de Bus de Service client.

### <a name="queue-with-a-large-number-of-receivers"></a>File d’attente avec un grand nombre de récepteurs

Objectif : Maximiser le taux de réception d’une file d’attente ou d’abonnement avec un grand nombre de récepteurs. Chaque destinataire reçoit des messages à une vitesse modérée. Le nombre d’expéditeurs est petit.

Bus de service permet jusqu'à 1000 connexions simultanées à une entité. Si une file d’attente nécessite plus de 1000 récepteurs, vous devez remplacer la file d’attente avec une rubrique et plusieurs abonnements. Chaque abonnement peut prendre en charge jusqu'à 1000 de connexions simultanées. Récepteurs peuvent également accéder à la file d’attente via le protocole HTTP.

Pour maximiser le débit, effectuez les opérations suivantes :

-   Utiliser une file d’attente partitionnée pour l’amélioration des performances et de disponibilité.

-   Si chaque récepteur réside dans un processus différent, utilisez uniquement une seule fabrique par processus.

-   Récepteurs peuvent utiliser les opérations synchrones ou asynchrones. Étant donné le taux de réception modérée d’un récepteur individuel, le traitement par lots du côté client de la demande complète n’affecte pas le débit de récepteur.

-   Laissez l’accès banque par lot. Cela permet de réduire la charge globale de l’entité. Il permet également de réduire le taux global auquel les messages puissent être écrits dans la file d’attente ou une rubrique.

-   Définir le nombre de prefetch avec une valeur faible (par exemple, PrefetchCount = 10). Cela empêche des récepteurs d’inactivité autres récepteurs ont de grandes quantités de messages mis en cache.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Rubrique avec un petit nombre d’abonnements

Objectif : Maximiser le débit d’une rubrique avec un petit nombre d’abonnements. Un message est reçu par le nombre d’abonnements, ce qui signifie que le taux de réception combinées sur tous les abonnements est supérieur à la vitesse de transmission. Le nombre d’expéditeurs est petit. Le nombre de destinataires par abonnement est de petite taille.

Pour maximiser le débit, effectuez les opérations suivantes :

-   Utilisez une rubrique partitionnée pour l’amélioration des performances et de disponibilité.

-   Pour augmenter la vitesse de transmission globale dans la rubrique, utilisez plusieurs fabriques de message à créer des expéditeurs. Pour chaque expéditeur, utiliser des opérations asynchrones ou plusieurs threads.

-   Pour augmenter le taux de réception global d’un abonnement, utilisez plusieurs fabriques de message pour créer des récepteurs. Pour chaque destinataire, utilisez des opérations asynchrones ou plusieurs threads.

-   Pour tirer parti du traitement par lots du côté client, utiliser des opérations asynchrones.

-   La valeur par défaut de l’intervalle de 20 ms de traitement par lots permet de réduire le nombre de transmissions de protocole de Bus de Service client.

-   Laissez l’accès banque par lot. Cela permet d’augmenter le taux global auquel les messages puissent être écrits dans la rubrique.

-   Définir le nombre de lecture anticipée à 20 fois le maximum des taux de traitement de tous les récepteurs d’une fabrique. Cela permet de réduire le nombre de transmissions de protocole de Bus de Service client.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Rubrique avec un grand nombre de souscriptions

Objectif : Maximiser le débit d’un sujet à un grand nombre de souscriptions. Un message est reçu par le nombre d’abonnements, ce qui signifie que le taux de réception combinées sur tous les abonnements est nettement supérieur à la vitesse de transmission. Le nombre d’expéditeurs est petit. Le nombre de destinataires par abonnement est de petite taille.

En général, les rubriques avec un grand nombre de souscriptions exposent un faible débit global si tous les messages sont acheminés pour tous les abonnements. Cela est dû par le fait que chaque message est reçu plusieurs fois, et tous les messages qui sont contenus dans une rubrique et toutes ses abonnements sont stockés dans la même banque. Il est supposé que le nombre d’expéditeurs et de nombre de récepteurs par abonnement sont petites. Bus de service prend en charge jusqu'à 2 000 abonnements par rubrique.

Pour maximiser le débit, effectuez les opérations suivantes :

-   Utilisez une rubrique partitionnée pour l’amélioration des performances et de disponibilité.

-   Pour tirer parti du traitement par lots du côté client, utiliser des opérations asynchrones.

-   La valeur par défaut de l’intervalle de 20 ms de traitement par lots permet de réduire le nombre de transmissions de protocole de Bus de Service client.

-   Laissez l’accès banque par lot. Cela permet d’augmenter le taux global auquel les messages puissent être écrits dans la rubrique.

-   Définir le nombre de lecture préalable pour le taux de réception attendue 20 fois en secondes. Cela permet de réduire le nombre de transmissions de protocole de Bus de Service client.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’optimisation des performances du Bus de Service, voir [entités de messagerie partitionnées][].

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [PeekLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [ReceiveAndDelete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [BatchFlushInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.netmessagingtransportsettings.batchflushinterval.aspx
  [EnableBatchedOperations]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations.aspx
  [QueueClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.prefetchcount.aspx
  [SubscriptionClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.prefetchcount.aspx
  [ForcePersistence]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.forcepersistence.aspx
  [EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [Entités de messagerie partitionnées]: service-bus-partitioning.md
  