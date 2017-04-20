<properties 
    pageTitle="Créer des applications qui utilisent des abonnements et des rubriques du Bus de Service | Microsoft Azure"
    description="Introduction à la publication-s’abonner des fonctionnalités offertes par les abonnements et les rubriques du Bus de Service."
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

# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Créer des applications qui utilisent des abonnements et des rubriques du Bus des services

Bus des services Azure prend en charge un ensemble de technologies de middleware de nuage, orientés message, notamment fiable message queuing et durables de publication / d’abonnement de messagerie. Cet article s’appuie sur les informations fournies dans les [applications de création qui utilisent des files d’attente de Bus de Service](service-bus-create-queues.md) et offre une introduction aux fonctions de publication et d’abonnement proposé par thèmes du Bus de Service.

## <a name="evolving-retail-scenario"></a>Scénario de vente au détail en constante évolution

Cet article poursuit le scénario de vente au détail utilisé dans les [applications de création qui utilisent des files d’attente de Bus de Service](service-bus-create-queues.md). Souvenez-vous que les données client à partir de chaque Point de vente (PDV) terminaux doivent être routées vers un système de gestion de stock qui utilise ces données pour déterminer à quel moment le stock doit être réapprovisionné. Chaque terminal POS signale ses données commerciales en envoyant des messages à la file d’attente **DataCollectionQueue** , où elles restent jusqu'à ce qu’elles sont reçues par le système de gestion de stock, comme illustré ici :

![Bus de service 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Pour faire évoluer ce scénario, un nouvel impératif a été ajouté au système : le propriétaire souhaite être en mesure de surveiller les performances de la banque d’informations en temps réel.

Pour répondre à cette exigence, le système doit « cliquez sur » Désactiver le flux de données de ventes. Nous avons toujours chaque message envoyé par les terminaux PDS pour être envoyé vers le système de gestion de stock comme avant, mais nous avons une autre copie de chaque message que nous pouvons utiliser pour présenter l’affichage du tableau de bord pour le propriétaire de la Boutique.

Dans tous les cas de ce type, dans lequel vous avez besoin de chaque message à être consommés en plusieurs parties, vous pouvez utiliser le Bus de Service *rubriques*. Rubriques fournissent un modèle de publication et d’abonnement dans lequel chaque message publié est rendu disponible pour un ou plusieurs abonnements enregistrés avec le sujet. En revanche, des files d’attente, chaque message est reçu par un seul consommateur.

Messages sont envoyés vers une rubrique de la même façon qu’ils sont envoyés à une file d’attente. Toutefois, les messages ne sont pas reçues à partir de la rubrique directement ; elles sont reçues à partir des abonnements. Vous pouvez considérer un abonnement à une rubrique comme une file d’attente virtuelle qui reçoit une copie des messages envoyés à ce sujet. Les messages sont reçus d’un abonnement identique telles qu’elles sont reçues à partir d’une file d’attente.

Pour revenir au scénario de vente au détail, la file d’attente est remplacée par une rubrique et un abonnement est ajouté, ce qui permet du composant de système de gestion de stock. Le système se présente comme suit :

![Bus de service 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

La configuration ici effectue le même à la conception précédente basée sur la file d’attente. En d’autres termes, les messages envoyés à la rubrique sont acheminés à l’abonnement de **stock** à partir duquel le **Système de gestion de stock** consomme les.

Pour prendre en charge le tableau de bord de gestion, nous créons un second abonnement sur le sujet, comme illustré ici :

![Bus de service 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Avec cette configuration, chaque message des TPV est rendu disponible pour le **tableau de bord** et le **stock** des abonnements.

## <a name="show-me-the-code"></a>Afficher le code

L’article [créer les applications qui utilisent des files d’attente de Bus de Service](service-bus-create-queues.md) décrit comment ouvrir un compte Azure et créer un espace de noms du service. Pour utiliser un espace de noms du Bus de Service, une application doit référencer l’assembly de Bus des services, en particulier Microsoft.ServiceBus.dll. Le moyen le plus simple pour référencer les dépendances de Service Bus est d’installer le [package Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)du Bus de Service. Vous pouvez également trouver l’assembly en tant que partie du SDK Azure. Le téléchargement est disponible sur la [page de téléchargement du Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/).

### <a name="create-the-topic-and-subscriptions"></a>Créer la rubrique et les abonnements

Opérations de gestion pour le Bus de Service d’échanges d’entités (rubriques de files d’attente et de publication et d’abonnement) sont effectuées via la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Informations d’identification appropriées sont nécessaires pour créer une instance de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) pour un espace de noms particulier. Bus de service utilise un modèle de sécurité en fonction de [Signature de l’accès partagé (SAS)](service-bus-sas-overview.md) . La classe [TokenProvider a](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) représente un fournisseur de jeton de sécurité avec les méthodes de fabrique intégré retourner certains fournisseurs bien connus de jetons. Nous allons utiliser une méthode [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) pour stocker les informations d’identification d’associations de sécurité. L’instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) est puis construit avec l’adresse de base de l’espace de noms du Service Bus et le fournisseur de jetons.

La classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fournit des méthodes pour créer, énumérer et supprimer des entités de messagerie. Le code qui est présenté ici montre comment l’instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) est créé et utilisé pour créer la rubrique **DataCollectionTopic** .

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

Notez qu’il existe des surcharges de la méthode [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) qui vous permettent de définir les propriétés de la rubrique. Par exemple, vous pouvez définir la valeur par défaut time-to-live (TTL) pour les messages envoyés à la rubrique. Ensuite, ajoutez les abonnements de **stock** et le **tableau de bord** .

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>Envoyer des messages à la rubrique

Pour le moment de l’exécution des opérations sur les entités de Bus de Service ; par exemple, envoyer et recevoir des messages, une application doit créer tout d’abord un objet [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . Semblable à la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , l’instance de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) est créé à partir de l’adresse de base de l’espace de noms du service et le fournisseur de jetons.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

Pour les messages envoyés et reçus à partir des rubriques du Bus des services, sont des instances de la classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Cette classe compose d’un ensemble de propriétés standard (par exemple, [l’étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et de [la propriété TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire qui sert à contenir les propriétés de l’application et un corps de données d’application arbitraire. Une application peut affecter l’organisme en passant tout objet sérialisable (l’exemple suivant passe dans un objet **DonnéesVentes** qui représente les données de ventes à partir du terminal POS), qui utilise le [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) pour sérialiser l’objet. Sinon, un objet de [flux](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) peut être fourni.

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

La façon la plus simple pour envoyer des messages à la rubrique est à utiliser [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) pour créer un objet [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) directement à partir de l’instance de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>Recevoir des messages à partir d’un abonnement

Similaire à l’utilisation de files d’attente, pour recevoir des messages à partir d’un abonnement, que vous pouvez utiliser un objet de [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) que vous créez directement à partir de la [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) à l’aide de [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Vous pouvez utiliser l’une des deux différentes recevoir des modes (**ReceiveAndDelete** et **PeekLock**), comme indiqué dans les [applications de création qui utilisent des files d’attente de Bus de Service](service-bus-create-queues.md).

Notez que lorsque vous créez un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) pour les abonnements, le paramètre *entityPath* du formulaire `topicPath/subscriptions/subscriptionName`. Par conséquent, pour créer un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) pour la souscription de **l’inventaire** de la rubrique **DataCollectionTopic** , *entityPath* doit être définie `DataCollectionTopic/subscriptions/Inventory`. Le code apparaît comme suit :

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

## <a name="subscription-filters"></a>Filtres d’abonnement

Jusqu’ici, dans ce cas tous les messages envoyés à la rubrique sont accessibles à tous les abonnements inscrits. La phrase clé ici est « disposition. » Abonnements de Bus de Service voir tous les messages envoyés à la rubrique, vous pouvez copier uniquement un sous-ensemble de ces messages à la file d’attente d’abonnement virtuel. Cette opération est effectuée à l’aide de *filtres*d’abonnement. Lorsque vous créez un abonnement, vous pouvez fournir une expression de filtre sous la forme d’un prédicat de style SQL92 qui fonctionne avec les propriétés du message, à la fois les propriétés système (par exemple, l' [étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) et les propriétés de l’application, comme les **fichiers nom_banque** dans l’exemple précédent.

Évolution du scénario pour illustrer ce propos, une deuxième banque est à ajouter à notre scénario de vente au détail. Données de vente à partir de tous les terminaux PDS à partir de deux magasins devront encore être routé vers le système de gestion centralisé des stocks, mais un directeur de magasin à l’aide de l’outil du tableau de bord n’est plus intéressé par les performances de ce magasin. Vous pouvez utiliser l’abonnement filtrage pour atteindre cet objectif. Notez que lorsque vous publiez des messages du TPV, ils définissent la propriété application **StoreName** sur le message. Étant donné les deux banques, par exemple **Redmond** et **Seattle**, les terminaux PDS dans le Redmond stockent cachet leurs messages de données de ventes avec un **StoreName** égal à **Redmond**, tandis que les terminaux de PDV du magasin Seattle utilisent un **StoreName** égal à **Seattle**. Le directeur du magasin de la banque de Redmond souhaite uniquement afficher les données de ses terminaux de PDV. Le système s’affiche comme suit :

![Service-Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Pour définir cette gamme, vous créez l’abonnement de **tableau de bord** comme suit :

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Avec ce filtre d’abonnement, seuls les messages qui ont la propriété **StoreName** **Redmond** seront copiés à la file d’attente virtuelle pour l’abonnement de **tableau de bord** . Il existe beaucoup plus de filtrage de l’abonnement, cependant. Les applications peuvent avoir plusieurs règles de filtre par abonnement en plus de la possibilité de modifier les propriétés d’un message lorsqu’il passe à la file d’attente virtuelle d’un abonnement.

## <a name="summary"></a>Résumé

Toutes les raisons d’utiliser queuing décrit dans [Création d’applications qui utilisent des files d’attente de Bus de Service](service-bus-create-queues.md) s’appliquent également aux rubriques, en particulier :

- Découplage temporelle – message producteurs et consommateurs n’ont pas à être en ligne en même temps.

- Nivellement de charge – les pics de charge sont faibles sont supprimés par le sujet de l’activation d’applications consommatrices peuvent être mis en service pour la charge moyenne au lieu des pics de charge.

- Équilibrage de la charge : similaire à une file d’attente, vous pouvez avoir plusieurs consommateurs concurrents à l’écoute sur un seul abonnement, avec chaque message transmis à un seul des consommateurs, ce qui permet l’équilibrage de la charge.

- Couplage lâche – vous pouvez évoluer le réseau de messagerie sans affecter les points de terminaison existants ; par exemple, ajout d’abonnements ou la modification des filtres à une rubrique pour autoriser de nouveaux consommateurs.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des files d’attente dans le scénario de retail POS, voir [créer des applications qui utilisent des files d’attente de Bus de Service](service-bus-create-queues.md) .