<properties 
    pageTitle="Transfert automatique des Bus de Service d’entités de messagerie | Microsoft Azure"
    description="Comment chaîner une file d’attente ou d’un abonnement à une autre file d’attente ou une rubrique."
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
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Chaînage des entités de Service Bus avec transfert automatique

La fonction de *transfert automatique* vous permet de chaîner une file d’attente ou d’un abonnement à une autre file d’attente ou la rubrique qui fait partie du même espace de noms. Lorsque le transfert automatique est activé, le Bus des services automatiquement supprime les messages qui sont placés dans la première file d’attente ou l’abonnement (source) et les place dans la deuxième file d’attente ou rubrique (de destination). Notez qu’il est toujours possible d’envoyer un message à l’entité de destination, directement. En outre, il n’est pas possible à une file d’attente secondaire, comme une file d’attente de lettres mortes, à une autre file d’attente ou la rubrique de la chaîne.

## <a name="using-auto-forwarding"></a>À l’aide de transfert automatique

Vous pouvez activer le transfert automatique en définissant les propriétés de la [SubscriptionDescription.ForwardTo][] ou de [QueueDescription.ForwardTo][] sur les objets [QueueDescription][] ou [SubscriptionDescription][] pour la source, comme dans l’exemple suivant.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

L’entité de destination doit exister au moment de que la création de l’entité source. Si l’entité de destination n’existe pas, le Bus de Service renvoie une exception lorsque vous êtes invité à créer l’entité source.

Transfert automatique vous permet de faire évoluer une rubrique individuelle. Bus de service limite le [nombre d’abonnements sur un sujet donné](service-bus-quotas.md) à 2 000. Vous pouvez répondre à des abonnements supplémentaires en créant des rubriques de second niveau. Notez que même si vous ne sont pas liés par la limitation du Bus de Service sur le nombre d’abonnements, ajout d’un second niveau de rubriques peut améliorer le débit global de votre sujet.

![Scénario de transfert automatique][0]

Vous pouvez également utiliser transfert automatique de dissocier les expéditeurs de messages à partir de récepteurs. Par exemple, considérez un système ERP qui se compose de trois modules : traitement des commandes, la gestion des stocks et la gestion de la relation client. Chacun de ces modules génère des messages en file d’attente dans une rubrique correspondante. Alice et Bob sont des commerciaux qui cherchent à tous les messages liés à leurs clients. Pour recevoir ces messages, Alice et Bob créent une file d’attente personnelle et un abonnement sur chacune des rubriques ERP qui transfèrent automatiquement tous les messages dans leur file d’attente.

![Scénario de transfert automatique][1]

En cas d’Alice sur vacances, sa file d’attente personnelle, plutôt que la rubrique ERP, se remplit. Dans ce scénario, car un représentant commercial n’a pas reçu tous les messages, aucun des rubriques ERP jamais parvenir à quota.

## <a name="auto-forwarding-considerations"></a>Considérations sur le transfert automatique

Si l’entité de destination a accumulé de nombreux messages et dépasse le quota ou l’entité de destination est désactivée, l’entité source ajoute les messages à sa [file d’attente de lettres mortes](service-bus-dead-letter-queues.md) jusqu'à ce que l’espace est la destination (ou l’entité est à nouveau activée). Ces messages continuera de vivre dans la file d’attente de lettres mortes, donc vous devez explicitement de recevoir et de les traiter à partir de la file d’attente de lettres mortes.

Lorsque le chaînage des rubriques individuelles afin d’obtenir une rubrique composite avec le nombre d’abonnements, il est recommandé que vous disposez d’un nombre modéré d’abonnements sur la rubrique de premier niveau et le nombre d’abonnements sur les sujets de second niveau. Par exemple, une rubrique de premier niveau avec 20 abonnements, chacun d’eux chaîné à une rubrique de second niveau avec 200 abonnements, permet un débit supérieur à une rubrique de premier niveau avec 200 abonnements, chacun lié à une rubrique de second niveau avec 20 abonnements.

Bus de service d’échéances une opération pour chaque message transmis. Par exemple, envoi d’un message à une rubrique avec 20 abonnements, chacun d’eux configuré pour le transfert automatique des messages vers une autre file d’attente ou rubrique, est facturé en tant que 21 opérations si tous les abonnements de premier niveau reçoivent une copie du message.

Pour créer un abonnement qui est chaîné à une autre file d’attente ou une rubrique, le créateur de l’abonnement doit avoir à **Gérer** les autorisations sur la source et l’entité de destination. Envoi de messages à la rubrique source requiert uniquement des autorisations **Envoyer** de la rubrique de la source.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le transfert automatique, consultez les rubriques de référence suivantes :

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

Pour en savoir plus sur les améliorations de performances des Bus de Service, voir [entités de messagerie partitionnées][].

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [Entités de messagerie partitionnées]: service-bus-partitioning.md