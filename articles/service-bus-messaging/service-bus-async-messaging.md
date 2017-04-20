<properties 
    pageTitle="Bus des services de messagerie asynchrone | Microsoft Azure"
    description="Description de la messagerie asynchrone du Bus de Service."
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

# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Modèles de messagerie asynchrones et haute disponibilité

Messagerie asynchrone peut être implémentée de plusieurs manières différentes. Avec les files d’attente, des rubriques et des abonnements, Bus des services Azure prend en charge l’asynchronie via une banque et un mécanisme de transfert. Dans une opération normale (synchrone), vous envoyez des messages à des files d’attente et les rubriques et recevez des messages des files d’attente et les abonnements. Vous écrivez des applications dépendent de ces entités est toujours disponible. Lorsque l’état de l’entité change, en raison d’autres circonstances, vous avez besoin d’un moyen de fournir une entité de capacité réduite qui peut répondre aux besoins de la plupart des.

Les applications utilisent des modèles de messagerie asynchrones à activer un certain nombre de scénarios de communication. Vous pouvez créer des applications dans lesquelles clients peuvent envoyer des messages aux services, même lorsque le service n’est pas en cours d’exécution. Pour les applications de cette expérience rafales de communications, une file d’attente peuvent aider à niveau la charge en fournissant un emplacement pour les communications de la mémoire tampon. Enfin, vous pouvez obtenir un équilibreur de charge simple mais efficace pour distribuer les messages sur plusieurs ordinateurs.

Pour maintenir la disponibilité d’une de ces entités, envisagez un certain nombre de façons différentes dans lequel ces entités peuvent apparaître non disponibles pour un système de messagerie durable. En règle générale, nous voyons l’entité deviennent indisponibles pour les applications que nous écrire des différentes manières suivantes :

- Impossible d’envoyer des messages.

- Impossible de recevoir des messages.

- Impossible de gérer les entités (créer, récupérer, mettre à jour ou supprimer des entités).

- Impossible de contacter le service.

Pour chacun de ces échecs, modes de défaillance différents existent qui permettent à une application de continuer à travailler à un certain niveau de fonctionnalité réduite. Par exemple, un système qui peut envoyer des messages mais pas en recevoir peut toujours recevoir des commandes des clients, mais ne peut pas traiter les commandes. Cette rubrique présente les problèmes potentiels qui peuvent se produire, et comment ces problèmes sont atténuées. Bus de service a introduit un certain nombre de mesures d’atténuation, vous devez opter, et elle aborde également les règles régissant l’utilisation de ces atténuations opt-in.

## <a name="reliability-in-service-bus"></a>Fiabilité de Bus des services

Il existe plusieurs façons de gérer les problèmes de message et entité et il existe des directives régissant l’utilisation appropriée de ces mesures d’atténuation. Pour comprendre les instructions, vous devez d’abord comprendre ce que peut échouer dans le Bus de Service. En raison de la conception de systèmes d’Azure, tous ces problèmes ont tendance à être éphémères. À un niveau élevé, les différentes causes d’indisponibilité s’affichent comme suit :

-   La limitation d’un système externe dont dépend le Bus des services. La limitation se produit à partir des interactions avec les ressources de calcul et de stockage.

-   Problème pour un système dont dépend le Bus des services. Par exemple, une partie donnée du stockage peut rencontrer des problèmes.

-   Échec du Bus de Service sur un seul sous-système. Dans ce cas, un nœud de calcul peut obtenir dans un état incohérent et devez redémarrer lui-même, à l’origine de toutes les entités, qu'il sert à équilibrer à d’autres nœuds. Cela peut provoquer une courte période de traitement du message lent.

-   Échec du Service de Bus dans un centre de données Azure. Il s’agit d’une « défaillance irrémédiable » au cours de laquelle le système est inaccessible pour le nombre de minutes ou quelques heures.

> [AZURE.NOTE] Le terme **stockage** peut signifier à la fois le stockage Azure et SQL Azure.

Bus de service contient un certain nombre de facteurs atténuants pour ces problèmes. Les sections suivantes décrivent chaque problème et leur minimisation respectifs.

### <a name="throttling"></a>La limitation de

Avec un Bus de Service, la limitation permet la gestion de taux message coopérative. Chaque nœud Service Bus abrite de nombreuses entités. Chacune de ces entités sollicite le système en termes de processeur, mémoire, stockage et autres facettes. Lorsqu’un de ces facettes détecte l’utilisation qui dépasse les seuils définis, le Bus de Service peut refuser une demande donnée. L’appelant reçoit un [ServerBusyException][] et réessaie après 10 secondes.

Sous la forme d’une réduction des risques, le code doit lire l’erreur et stopper les tentatives du message pendant au moins 10 secondes. Étant donné que l’erreur peut se produire entre les composants de l’application client, il est probable que chaque élément s’exécute indépendamment la logique des nouvelles tentatives. Le code peut réduire la probabilité de limitée en activant le partitionnement sur une file d’attente ou une rubrique.

### <a name="issue-for-an-azure-dependency"></a>Problème pour une dépendance Azure

Autres composants dans Azure peuvent parfois avoir des problèmes de service. Par exemple, lorsqu’un système qui utilise des Bus de Service est en cours de mise à niveau, ce système peut rencontrer temporairement des fonctionnalités réduites. Pour contourner ces types de problèmes, Bus de Service examine régulièrement et implémente les atténuations. Effets secondaires de ces facteurs atténuants ne s’affichent pas. Par exemple, pour gérer des problèmes temporaires avec le stockage, le Bus des services implémente un système qui permet les opérations d’envoi de message à fonctionner de manière cohérente. En raison de la nature de l’atténuation, un message peut prendre jusqu'à 15 minutes pour apparaître dans la file d’attente affecté ou l’abonnement et être prêt pour une opération de réception. En règle générale, la plupart des entités ne rencontreront pas ce problème. Toutefois, étant donné le nombre d’entités dans le Bus de Service au sein d’Azure, cette opération est parfois nécessaire pour un petit sous-ensemble de clients de Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Défaillances du Bus de service sur un seul sous-système

Avec n’importe quelle application, les circonstances peuvent entraîner un composant interne de Bus de Service d’incohérences. Bus de Service détecte, il collecte des données à partir de l’application pour vous aider à diagnostiquer ce qui s’est passé. Une fois les données collectées, l’application est redémarrée lors d’une tentative de retourner à un état cohérent. Ce processus se produit assez rapidement, et les résultats d’une entité semble ne pas être disponible pour quelques minutes, mais généralement la fois sont beaucoup plus courts.

Dans ces cas, l’application cliente génère une exception [System.TimeoutException][] ou [MessagingException][] . Bus de service contient une atténuation de ce problème dans l’écran client automatisée de logique de recherche. Une fois la période de nouvelle tentative est épuisée et que le message n’est pas remis, vous pouvez Explorer à l’aide d’autres fonctionnalités telles que les [espaces de noms associés][]. Les espaces de noms associés ont les autres restrictions qui sont abordées dans cet article.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Échec du Service de Bus dans un centre de données Azure

La raison la plus probable d’une défaillance dans un centre de données Azure est un déploiement de mise à niveau a échoué de Bus de Service ou un système dépendant. Comme la plate-forme a la maturation, la probabilité de ce type de défaillance a diminué. Une panne du centre de données peut également se produire pour des raisons qui incluent les éléments suivants :

-   Panne électrique (bloc d’alimentation et de la production d’ENERGIE disparaissent).
-   Connectivité (pause internet entre vos clients et vos Azure).

Dans les deux cas, une catastrophe naturelle ou humaine a provoqué le problème. Pour contourner ce problème et vous assurer que vous pouvez toujours envoyer des messages, vous pouvez utiliser des [espaces de noms associés][] pour activer les messages à envoyer à un autre emplacement pendant l’emplacement principal est en bon état à nouveau. Pour plus d’informations, consultez [méthodes conseillées pour l’isolation des applications contre les interruptions de Service Bus et les sinistres][].

## <a name="paired-namespaces"></a>Les espaces de noms associés

La fonction [d’espaces de noms associés][] prend en charge des scénarios dans lequel une entité de Bus des services ou le déploiement au sein d’un centre de données devient indisponible. Bien que cet événement se produit rarement, des systèmes distribués encore doivent être préparées à gérer le pire des cas. En règle générale, cet événement se produit, car un élément dépend des Bus de Service rencontre un problème à court terme. Pour maintenir la disponibilité des applications pendant une panne, les utilisateurs du Service Bus permet deux espaces de noms distincts, préférence dans les centres de données distincts, pour héberger leurs entités de messagerie. Le reste de cette section utilise la terminologie suivante :

-   Espace de noms principal : l’espace de noms avec lesquels votre application interagit, pour envoyer et recevoir des opérations.

-   Espace de noms secondaire : l’espace de noms qui agit comme une sauvegarde de l’espace de noms principal. Logique de l’application n’interagit pas avec cet espace de noms.

-   Intervalle de basculement sur incident : le laps de temps pour accepter les échecs normales avant que l’application bascule à partir de l’espace de noms principal vers l’espace de noms secondaire.

Appariement de prise en charge des espaces de noms *Envoyer la disponibilité*. Envoyer la disponibilité conserve la possibilité d’envoyer des messages. Pour utiliser la disponibilité de l’envoi, votre application doit remplir les conditions suivantes :

1.  Les messages sont reçus uniquement à partir de l’espace de noms principal.

2.  Les messages envoyés à une file d’attente ou rubrique d’arriver dans le désordre.

3.  Si votre application utilise des sessions, des messages au sein d’une session arrivent en désordre. Il s’agit d’un arrêt de fonctionnement normal de sessions. Cela signifie que votre application utilise les sessions de grouper logiquement des messages. L’état de session est conservé uniquement sur l’espace de noms principal.

4.  Messages au cours d’une session arrivent en désordre. Il s’agit d’un arrêt de fonctionnement normal de sessions. Cela signifie que votre application utilise les sessions de grouper logiquement des messages.

5.  L’état de session est conservé uniquement sur l’espace de noms principal.

6.  La file d’attente principale peut être en ligne et commencer à accepter des messages avant que la file d’attente secondaire remet tous les messages dans la file d’attente principale.

Les sections suivantes traitent de l’API, comment les API sont implémentés et présente des exemples de code qui utilisent la fonctionnalité. Notez qu’il y a des implications de facturation associées à cette fonctionnalité.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>L’API de MessagingFactory.PairNamespaceAsync

La fonction d’espaces de noms associés comprend la méthode [PairNamespaceAsync][] sur la classe [Microsoft.ServiceBus.Messaging.MessagingFactory][] :

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Une fois la tâche terminée, l’association de l’espace de noms est également terminé et prêt à agir pour les [MessageReceiver][], [QueueClient][], ou [TopicClient][] créées avec l’instance de [MessagingFactory][] . [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][] est la classe de base pour les différents types de correspondance qui sont disponibles avec un objet [MessagingFactory][] . Actuellement, la seule classe dérivée est une nommée [SendAvailabilityPairedNamespaceOptions][], qui implémente l’envoi des exigences de disponibilité. [SendAvailabilityPairedNamespaceOptions][] possède un ensemble de constructeurs qui s’appuient sur l’autre. En examinant le constructeur avec le plus de paramètres, vous pouvez comprendre le comportement des autres constructeurs.

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Ces paramètres ont la signification suivante :

-   *secondaryNamespaceManager*: une instance [NamespaceManager][] initialisée pour l’espace de noms secondaire la méthode [PairNamespaceAsync][] permet de définir l’espace de noms secondaire. Le Gestionnaire d’espace de noms est utilisé pour obtenir la liste des files d’attente dans l’espace de noms et pour vous assurer que les files d’attente de la file d’attente requise existent. Si les files d’attente n’existent pas, ils sont créés. [NamespaceManager][] requiert la possibilité de créer un jeton à la revendication de la **Gérer** .

-   *messagingFactory*: l’instance de [MessagingFactory][] de l’espace de noms secondaire. L’objet [MessagingFactory][] est utilisé pour envoyer et, si la propriété [EnableSyphon][] est définie sur **true**, recevoir des messages dans les files d’attente de la file d’attente.

-   *backlogQueueCount*: le nombre de files d’attente de la file d’attente à créer. Cette valeur doit être au moins 1. Lors de l’envoi de messages à la file d’attente, une de ces files d’attente est sélectionnée de manière aléatoire. Si vous définissez la valeur sur 1, seule file d’attente peut jamais utilisé. Lorsque cela se produit et que la file d’attente d’une file d’attente génère des erreurs, le client est de ne pas pouvoir essayer une file d’attente de la file d’attente différente et risquent de ne pas envoyer votre message. Nous vous recommandons de définir cette valeur et certains plus grande valeur par défaut à la valeur 10. Vous pouvez le modifier pour une valeur supérieure ou inférieure en fonction de la quantité de données votre application envoie par jour. Chaque file d’attente de la file d’attente peut contenir jusqu'à 5 Go de messages.

-   *failoverInterval*: le laps de temps pendant laquelle vous acceptez les échecs sur l’espace de noms principal avant de passer n’importe quelle entité unique l’espace de noms secondaire. Basculements sur une base entité par entité. Entités dans un espace de noms unique live fréquemment dans les différents nœuds de Bus de Service. Une défaillance dans une entité n’implique pas une défaillance dans un autre. Vous pouvez définir cette valeur à [System.TimeSpan.Zero][] pour le basculement vers le secondaire immédiatement après votre première défaillance non transitoire. Échecs de déclencher le minuteur de basculement sont tout [MessagingException][] dans lequel la propriété [IsTransient][] a la valeur false ou un [System.TimeoutException][]. Autres exceptions telles que [UnauthorizedAccessException][] n’entraînent pas de basculement sur incident, car ils indiquent que le client est configuré de manière incorrecte. Un [ServerBusyException][] ne provoque pas de basculement car le modèle correct consiste à attendre 10 secondes, puis renvoyez le message.

-   *enableSyphon*: indique que cette association particulière doit syphon également les messages à partir de l’espace de noms secondaire à l’espace de noms principal. En général, les applications qui envoient des messages doivent définir cette valeur à **false**; les applications qui reçoivent des messages doivent définir cette valeur à **true**. La raison à cela est que souvent, il y a moins de destinataires de messages que les expéditeurs de messages. En fonction du nombre de destinataires, vous pouvez choisir d’utiliser une instance d’une application unique pour gérer les droits de syphon. À l’aide de récepteurs de nombreuses implications facturation pour chaque file d’attente de la file d’attente.

Pour utiliser le code, créez une instance de [MessagingFactory][] primaire, une instance de [MessagingFactory][] secondaire, une instance [NamespaceManager][] secondaire et une instance de [SendAvailabilityPairedNamespaceOptions][] . L’appel peut être aussi simple que le suivant :

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Une fois la tâche retournée par la méthode [PairNamespaceAsync][] terminée, tout est configuré et prêt à l’emploi. Avant que la tâche est renvoyée, ne peut pas terminé toutes les tâches d’arrière-plan nécessaires pour l’appariement à travailler de droite. Par conséquent, vous devez démarrer pas d’envoi de messages jusqu'à ce que la tâche retourne. Cas d’échec, par exemple les informations d’identification incorrectes ou d’échec pour créer les files d’attente de la file d’attente, ces exceptions seront levées dès que la tâche se termine. Une fois la tâche retourne une valeur, vérifiez que les files d’attente ont été trouvés ou créés en examinant la propriété [BacklogQueueCount][] sur votre instance de [SendAvailabilityPairedNamespaceOptions][] . Le code précédent, cette opération se présente comme suit :

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base de la messagerie asynchrone dans le Bus des services, lire plus d’informations sur [les espaces de noms associés][].

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Méthodes conseillées pour l’isolation des applications contre les interruptions de Service Bus et les sinistres]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]:https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [les espaces de noms associés]: service-bus-paired-namespaces.md