<properties 
    pageTitle="Files d’attente du Bus des services, des rubriques et des abonnements | Microsoft Azure"
    description="Vue d’ensemble du Bus de Service d’entités de messagerie."
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
    ms.date="10/14/2016"
    ms.author="sethm" />

# <a name="service-bus-queues-topics-and-subscriptions"></a>Abonnements, des rubriques et des files d’attente de Bus de Service

Bus des services Microsoft Azure prend en charge un ensemble de technologies en nuage, middleware orienté message y compris fiable message queuing et durables de publication / d’abonnement de messagerie. Ces fonctionnalités de messagerie traitée par le Broker peuvent considérer comme découplé les fonctionnalités prise en charge de publication-abonnement de messagerie temporelle découplage et scénarios à l’aide de la messagerie fabric le Bus de Service d’équilibrage de charge. Communication découplée a présente de nombreux avantages ; par exemple, clients et les serveurs peuvent se connecter en fonction des besoins et effectué leurs propres opérations de manière asynchrone.

Les entités de messagerie qui forment le noyau des fonctionnalités de messagerie contemporains dans le Bus de Service sont des files d’attente, les rubriques/abonnements et les règles et les actions.

## <a name="queues"></a>Files d’attente

Files d’attente offrent First In, remise des messages à un ou plusieurs consommateurs concurrents premier sorti (FIFO). Autrement dit, messages généralement doivent être reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente, et chaque message est reçue et traitée par le consommateur d’un seul message. Des principaux avantages de l’utilisation de files d’attente sont atteindre « découplage temporel » des composants d’application. En d’autres termes, les producteurs (expéditeurs) et les consommateurs (récepteurs) inutile à l’envoi et la réception de messages en même temps, dans la mesure où les messages sont stockés durablement dans la file d’attente. En outre, le producteur n’a pas à attendre une réponse du consommateur pour pouvoir continuer à traiter et envoyer des messages.

Un avantage connexe est « charger un audit, » qui permet d’envoyer et de recevoir des messages à des taux différents producteurs et les consommateurs. Dans de nombreuses applications, la charge système varie dans le temps ; Cependant, le temps de traitement requis pour chaque unité de travail est généralement constant. Echanger producteurs de message et les consommateurs avec une file d’attente signifie que l’application consommatrice ne doit être configuré pour être en mesure de gérer la charge moyenne au lieu des pics de charge. La profondeur de la file d’attente augmente et contrats comme les variations de la charge entrante. Cela directement économise de l’argent en ce qui concerne le montant de l’infrastructure requise pour traiter la charge de l’application. À mesure que la charge augmente, plus les processus de travail peuvent être ajoutés pour lire à partir de la file d’attente. Chaque message est traité par un seul processus de travail. En outre, cette extraction d’équilibrage de charge permet pour une utilisation optimale des ordinateurs de travail même si les ordinateurs de travail diffèrent en ce qui concerne la puissance de traitement, comme ils extrait les messages à leur propre taux maximal. Le modèle « concurrence consommateur » est souvent qualifié de ce modèle.

À l’aide de files d’attente d’intermédiaire entre les consommateurs et les producteurs de message fournit un couplage lâche inhérent entre les composants. Étant donné que les producteurs et les consommateurs ne sont pas conscients des uns des autres, un consommateur peut être mis à niveau sans avoir aucun effet sur le producteur.

Création d’une file d’attente est un processus en plusieurs étapes. Vous effectuez des opérations de gestion pour le Bus de Service d’échanges d’entités (files d’attente et rubriques) via la classe [Microsoft.ServiceBus.NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , qui est construite en fournissant l’adresse de base de l’espace de noms du Bus de services et les informations d’identification de l’utilisateur. [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fournit des méthodes pour créer, énumérer et supprimer des entités de messagerie. Après avoir créé un objet [Microsoft.ServiceBus.TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) à partir du nom SAS et la clé et un objet de gestion d’espace de noms service, vous pouvez utiliser la méthode [Microsoft.ServiceBus.NamespaceManager.CreateQueue](https://msdn.microsoft.com/library/azure/hh293157.aspx) pour créer la file d’attente. Par exemple :

```
// Create management credentials
TokenProvider credentials = TokenProvider. CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Vous pouvez ensuite créer un objet de file d’attente et une messagerie fabrique avec l’URI du Bus de Service en tant qu’argument. Par exemple :

```
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

Vous pouvez envoyer des messages à la file d’attente. Par exemple, si vous disposez d’une liste de messages de courtage appelée `MessageList`, le code ressemble à ce qui suit :

```
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Vous pouvez ensuite recevoir des messages de la file d’attente, comme suit :

```
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

En mode [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , l’opération de réception est la prise de vue unique ; Autrement dit, lorsque le Bus de Service reçoit la requête, il marque le message comme étant consommé et retourne à l’application. [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) est le modèle le plus simple et fonctionne mieux pour les scénarios dans lesquels l’application peut tolérer ne traite ne pas un message en cas de panne. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service marque le message comme étant consommé, lorsque l’application redémarre et commence à consommer des messages à nouveau, il est ont manqué au message qui a été utilisé avant l’incident.

En mode de [PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , l’opération de réception devient deux étapes, ce qui permet aux applications de prise en charge qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit la requête, recherche du message suivant devant être consommés, verrous pour empêcher les autres utilisateurs à partir de la réception et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant [complète](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sur le message reçu. Lorsque le Bus des services voit la [complète](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) appeler, il marque le message comme étant consommé.

Si l’application ne parvient pas à traiter le message pour une raison quelconque, il peut appeler la méthode [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) sur le message reçu (au lieu de [Terminer](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Cela permet de Bus de Service déverrouiller le message et le rendre disponible à recevoir à nouveau, soit par le même client ou par un autre consommateur concurrent. D’autre part, un délai associé au verrou et si l’application ne parvient pas à traiter le message avant le verrou délai expire (par exemple, si l’application se bloque), Bus de Service déverrouille le message et le rend disponible à recevoir à nouveau (essentiellement en effectuant une opération [d’Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) par défaut).

Notez que dans le cas où l’application se bloque après avoir traité le message, mais avant que la demande [complète](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) est émise, le message est remis à nouveau à l’application lorsqu’il redémarre. On appelle souvent cela *Au moins une fois *une transformation ; Autrement dit, chaque message est traité au moins une fois. Toutefois, dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis une logique supplémentaire est requis dans l’application de détecter les doublons qui peuvent être réalisés en fonction de la propriété **MessageId** du message, qui reste constante entre les tentatives de remise. Cela est appelé *Une seule fois* de traitement.

Pour plus d’informations et un exemple de la façon de créer et d’envoyer des messages vers et à partir de files d’attente, consultez le [Bus de Service demandé de messagerie didacticiel de .NET](service-bus-brokered-tutorial-dotnet.md).

## <a name="topics-and-subscriptions"></a>Rubriques et abonnements

Contrairement aux files d’attente, dans lequel chaque message est traité par un seul consommateur, les *abonnements* et les *rubriques* fournissent un formulaire un-à-plusieurs de la communication, dans un modèle de *publication et d’abonnement* . Utile pour la mise à l’échelle à un très grand nombre de destinataires, chaque message publié est rendu disponible pour chaque abonnement enregistré avec le sujet. Messages envoyés à une rubrique et remis à un ou plusieurs abonnements associés, selon les règles de filtre qui peuvent être définies sur une base par abonnement. Les abonnements permet de limiter les messages qu’ils souhaitent recevoir des filtres supplémentaires. Les messages sont envoyés vers une rubrique de la même manière, ils sont envoyés à une file d’attente, mais les messages ne sont pas reçues directement à partir de la rubrique. Au lieu de cela, elles sont reçues à partir des abonnements. Un abonnement de rubrique ressemble à une file d’attente virtuelle qui reçoit une copie des messages qui sont envoyés à la rubrique. Les messages sont reçus d’un abonnement identique à la façon dont elles sont reçues à partir d’une file d’attente.

Par comparaison, la fonctionnalité d’envoi de messages d’une file d’attente correspond directement à une rubrique et ses fonctionnalités de réception des messages est mappé à un abonnement. Entre autres choses, cela signifie que les abonnements prennent en charge les mêmes modèles décrits précédemment dans cette section en ce qui concerne les files d’attente : consommateur concurrent découplage temporelle, charge l’audit et l’équilibrage de charge.

Création d’une rubrique est similaire à la création d’une file d’attente, comme illustré dans l’exemple de la section précédente. Créez l’URI de service et ensuite utiliser la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) pour créer de l’espace de noms client. Vous pouvez ensuite créer une rubrique à l’aide de la méthode [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) . Par exemple :

```
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Ensuite, ajoutez les abonnements comme vous le souhaitez :

```
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

Vous pouvez ensuite créer un client de la rubrique. Par exemple :

```
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

À l’aide de l’expéditeur du message, vous pouvez envoyer et recevoir des messages vers et à partir de la rubrique, comme indiqué dans la section précédente. Par exemple :

```
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Similaires aux files d’attente, les messages sont reçus d’un abonnement à l’aide d’un objet [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) au lieu d’un objet [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) . Créer le client d’abonnement, en passant le nom du sujet, le nom de l’abonnement et (éventuellement) le mode de réception en tant que paramètres. Par exemple, avec l’abonnement de **stock** :

```
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Règles et actions

Dans de nombreux scénarios, les messages qui ont des caractéristiques spécifiques doivent être traitées de différentes façons. Pour ce faire, vous pouvez configurer des abonnements pour rechercher les messages qui ont besoin des propriétés et puis apporter certaines modifications à ces propriétés. Bien que les abonnements de Bus des services afficher tous les messages envoyés à la rubrique, vous ne pouvez copier qu’un sous-ensemble de ces messages à la file d’attente d’abonnement virtuel. Pour cela, à l’aide de filtres d’abonnement. Ces modifications sont appelées des *actions de filtrage*. Lorsqu’un abonnement est créé, vous pouvez fournir une expression de filtre qui opère sur les propriétés du message, les propriétés du système (par exemple, l' **étiquette**) et propriétés d’application personnalisée (par exemple, **StoreName**.) L’expression de filtre SQL est facultative sans une expression de filtre SQL, toute action de filtre définie sur un abonnement sera effectuée sur tous les messages pour cet abonnement.

À l’aide de l’exemple précédent, pour filtrer les messages provenant uniquement de **banque 1**, vous devez créer l’abonnement de tableau de bord comme suit :

```
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Avec ce filtre d’abonnement sur place, seuls les messages qui ont le `StoreName` propriété `Store1` sont copiés dans la file d’attente virtuelle pour le `Dashboard` abonnement.

Pour plus d’informations sur les valeurs de filtre possibles, reportez-vous à la documentation pour les classes [SqlFilter](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx) et [SqlRuleAction](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlruleaction.aspx) . Consultez également la [demandé de la messagerie : filtres avancés](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) et des exemples de [Filtres de la rubrique](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters) .

## <a name="next-steps"></a>Étapes suivantes

Consultez la rubrique suivante rubriques pour obtenir des informations et des exemples d’utilisation de Bus de Service dans les entités de messagerie avancées.

- [Vue d’ensemble de la messagerie Bus des services](service-bus-messaging-overview.md)
- [Bus de service demandé de messagerie .NET didacticiel](service-bus-brokered-tutorial-dotnet.md)
- [Bus de service demandé de messagerie reste didacticiel](service-bus-brokered-tutorial-rest.md)
- [Exemples de filtres rubrique](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters)
- [Messagerie de courtage : Avancée des exemples de filtres](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

