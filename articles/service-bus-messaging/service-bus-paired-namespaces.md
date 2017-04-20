<properties 
    pageTitle="Bus de service associés des espaces de noms | Microsoft Azure"
    description="Détails d’implémentation de paires espace de noms et de coût"
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
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Paire de détails de mise en œuvre d’espace de noms et un coût

La méthode [PairNamespaceAsync][] , à l’aide d’une instance de [SendAvailabilityPairedNamespaceOptions][] , effectue des tâches visibles en votre nom. Parce qu’il sont considérations de coût lors de l’utilisation de la fonctionnalité, il est utile de comprendre ces tâches afin que vous attendez le comportement lorsqu’elle se produit. L’API engage le comportement automatique suivant de votre part :

-   Création de files d’attente de la file d’attente.
-   Création d’un objet [MessageSender][] qui communique avec les files d’attente ou de rubriques.
-   Lorsqu’une entité de messagerie devient indisponible, envoie une commande ping messages à l’entité dans une tentative de détection lorsque cette entité est de nouveau disponible.
-   Crée également un ensemble de « pompes de messages » que déplacer des messages entre les files d’attente de la file d’attente pour les files d’attente principales.
-   Coordonne la clôture/défaillante des instances [MessagingFactory][] primaires et secondaires.

À un niveau élevé, la fonctionnalité fonctionne comme suit : lorsque l’entité principale est en bon état, aucune modification de comportement se produit. Lorsque la durée de [FailoverInterval][] s’écoule et l’entité principale voit réussie n’envoie après un non transitoires [MessagingException][] ou une [TimeoutException se produit][], le comportement suivant se produit :

1.  Envoyer des opérations à l’entité principale sont désactivées et que le système connecte à l’entité principale jusqu'à ce que les requêtes ping peuvent être remis.

2.  Une file d’attente du backlog aléatoire est sélectionné.

3.  Objets de [BrokeredMessage][] sont acheminés vers la file d’attente de la file d’attente choisie.

1.  En cas d’échec d’une opération d’envoi vers la file d’attente de la file d’attente choisie, cette file d’attente est extraite de la rotation et une nouvelle file d’attente est sélectionné. Tous les expéditeurs sur l’instance de [MessagingFactory][] obtenir des informations de l’échec.

Les figures suivantes décrivent la séquence. Tout d’abord, l’expéditeur envoie des messages.

![Les espaces de noms associés][0]

En cas de panne pour envoyer à la file d’attente principale, l’expéditeur commence à envoyer des messages à une file d’attente du backlog choisi au hasard. Simultanément, elle démarre une tâche ping.

![Les espaces de noms associés][1]

À ce stade, les messages sont toujours dans la file d’attente secondaire et n’ont pas été remis à la file d’attente principale. Une fois que la file d’attente principale est à nouveau en bon état, au moins un processus doit être exécuté le syphon. Le syphon remet les messages à partir de toutes les files d’attente en retard de traitement différents pour les entités de destination approprié (files d’attente et rubriques).

![Les espaces de noms associés][2]

Le reste de cette rubrique décrit les détails spécifiques du fonctionnement de ces éléments.

## <a name="creation-of-backlog-queues"></a>Création de files d’attente de la file d’attente

L’objet [SendAvailabilityPairedNamespaceOptions][] transmis à la méthode [PairNamespaceAsync][] indique le nombre de files d’attente de la file d’attente à utiliser. Chaque file d’attente de la file d’attente est créé avec les propriétés suivantes explicitement ensemble (toutes les autres valeurs sont définies par défaut [QueueDescription][] ) :

| Chemin d’accès                                   | [espace de noms principal] / x-servicebus-transfert / [index] où [index] est une valeur en [0, BacklogQueueCount) |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes                     | 5120                                                                                                 |
| MaxDeliveryCount                       | int. MaxValue                                                                                         |
| DefaultMessageTimeToLive               | TimeSpan.MaxValue                                                                                    |
| AutoDeleteOnIdle                       | TimeSpan.MaxValue                                                                                    |
| LockDuration                           | 1 minute                                                                                             |
| EnableDeadLetteringOnMessageExpiration | valeur True                                                                                                 |
| EnableBatchedOperations                | valeur True                                                                                                 |

Par exemple, la première file d’attente de la file d’attente créée pour l' espace de noms **contoso** est nommé `contoso/x-servicebus-transfer/0`.

Lors de la création des files d’attente, le code vérifie d’abord si une file d’attente existe. Si la file d’attente n’existe pas, la file d’attente est créée. Le code ne pas nettoyer les files d’attente de la file d’attente « extra ». Plus précisément, si l’application avec l' espace de noms principal **contoso** demande cinq files d’attente mais une file d’attente de la file d’attente avec le chemin d’accès de file d’attente `contoso/x-servicebus-transfer/7` existe, cette file d’attente de la file d’attente supplémentaire est toujours présente, mais n’est pas utilisé. Le système autorise explicitement en retard de traitement supplémentaire files d’attente existent qui ne seraient pas utilisées. En tant que propriétaire de l’espace de noms, vous êtes chargé de nettoyer les files d’attente inutilisés/indésirables en retard de traitement. La raison de cette décision est que le Bus de Service ne peut pas savoir quelles fins existent pour toutes les files d’attente dans votre espace de noms. En outre, si une file d’attente existe avec le nom donné, mais ne répond pas à la valeur [QueueDescription][], puis vos raisons sont les vôtres pour modifier le comportement par défaut. Aucune garantie n’est effectuées pour des modifications pour les files d’attente de la file d’attente par votre code. Veillez à tester vos modifications de manière approfondie.

## <a name="custom-messagesender"></a>MessageSender personnalisé

Lors de l’envoi, tous les messages passent par un objet [MessageSender][] interne qui se comporte normalement lorsque tout fonctionne et que vous redirige vers les files d’attente de la file d’attente en «. » Lors de la réception d’une défaillance non transitoires, une minuterie démarre. Après une période [TimeSpan][] est constitué par la valeur de la propriété [FailoverInterval][] au cours de laquelle aucun message de réussite est envoyé, le basculement est activé. À ce stade, les événements suivants se produisent pour chaque entité :

- Une tâche ping exécute chaque [PingPrimaryInterval][] pour vérifier si l’entité est disponible. Une fois cette tâche réussit, tout le code client qui utilise l’entité immédiatement commence à envoyer de nouveaux messages à l’espace de noms principal.

- Les demandes ultérieures à envoyer à cette même entité de tous les autres expéditeurs entraîne la [BrokeredMessage][] envoyées pour être modifié dans la file d’attente de la file d’attente. La modification supprime certaines propriétés de l’objet [BrokeredMessage][] et les stocke ailleurs. Les propriétés suivantes sont compensées et ajoutées sous un alias, autorisant le Service Bus et le Kit de développement logiciel traiter les messages de manière uniforme :

| Ancien nom de la propriété       | Nouveau nom de propriété |
|-------------------------|-------------------|
| ID de session               | x-ms-sessionid.    |
| Propriété TimeToLive              | x-ms-la propriété timetolive   |
| ScheduledEnqueueTimeUtc | x-ms-chemin d’accès         |

Le chemin de destination d’origine est également stocké dans le message sous la forme d’une propriété nommée x-ms-chemin d’accès. Cette conception permet des messages pour de nombreuses entités peuvent coexister dans une file d’attente de la file d’attente unique. Les propriétés sont traduites par le syphon.

L’objet [MessageSender][] personnalisé peut rencontrer des problèmes lorsque les messages d’approchent de la limite de 256 Ko et basculement sur incident est activé. L’objet [MessageSender][] personnalisé stocke les messages pour toutes les files d’attente et les rubriques ensemble dans les files d’attente de la file d’attente. Cet objet contient à la fois les messages à partir du nombre de couleurs primaires dans les files d’attente de la file d’attente. Pour gérer l’équilibrage entre de nombreux clients qui ne savent pas chacune des autres, le Kit de développement logiciel choisit aléatoirement une file d’attente de la file d’attente pour chaque [QueueClient][] ou le [TopicClient][] vous créez dans le code.

## <a name="pings"></a>Commandes ping

Un message ping est un vide [BrokeredMessage][] dont la propriété [ContentType][] la valeur application/vnd.ms-servicebus-ping et une valeur de [la propriété TimeToLive][] de 1 seconde. Cette commande ping a une caractéristique spéciale dans le Bus de Service : le serveur remet jamais un ping lorsqu’un appelant demande une [BrokeredMessage][]. Par conséquent, vous devrez jamais savoir comment recevoir et ignorer ces messages. Chaque entité (file d’attente unique ou rubrique) de chaque instance de [MessagingFactory][] par le client va être exécutée sur lorsqu’ils sont considérés comme étant non disponible. Par défaut, cela se produit une fois par minute. Messages ping sont considérés comme des messages de Bus des services réguliers et peuvent entraîner des frais de bande passante et des messages. Dès que les clients détectent que le système est disponible, les messages d’arrêt.

## <a name="the-syphon"></a>Le syphon

Au moins un programme exécutable de l’application doit s’exécuter activement le syphon. Le syphon exécute un long sondage recevoir qui dure de 15 minutes. Lorsque toutes les entités sont disponibles et que vous avez 10 files d’attente de la file d’attente, l’application qui héberge le syphon appelle l’opération de réception 40 fois par heure, 960 heures par jour et une fois 28800 dans les 30 jours. Lorsque le syphon est activement déplacement de messages de la file d’attente dans la file d’attente principale, chaque message rencontre les taxes suivantes (frais standard de taille de message et de la bande passante s’appliquent à toutes les étapes) :

1.  Envoyer à la file d’attente.

2.  Réception à partir de la file d’attente.

3.  Envoyer vers le serveur principal.

4.  Recevoir du site principal.

## <a name="closefault-behavior"></a>Fermer/erreur de comportement

Dans une application qui héberge le syphon, une fois les défaillances de [MessagingFactory][] primaires ou secondaires, ou est fermée sans son partenaire étant également défectueux/fermée et le syphon détecte cet état, les actes de syphon. Si les autres [MessagingFactory][] n’est pas fermée dans 5 secondes, la syphon sera de pannes le toujours ouvrir [MessagingFactory][].

## <a name="next-steps"></a>Étapes suivantes

Pour une présentation détaillée de la messagerie asynchrone du Bus de Service, reportez-vous à la section [modèles de messagerie asynchrones et une haute disponibilité][] . 

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Exception TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [Propriété TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Modèles de messagerie asynchrones et haute disponibilité]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png
