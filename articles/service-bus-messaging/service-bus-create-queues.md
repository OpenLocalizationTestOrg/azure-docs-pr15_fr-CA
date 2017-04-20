<properties 
    pageTitle="Écrire des applications qui utilisent des files d’attente de Bus de Service | Microsoft Azure"
    description="Comment écrire une application simple basée sur la file d’attente qui utilise le Bus de Service."
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-queues"></a>Créer des applications qui utilisent des files d’attente de Bus de Service

Cette rubrique décrit les files d’attente de Bus de Service et indique comment écrire une application simple basée sur la file d’attente qui utilise le Bus de Service.

Imaginez un scénario dans l’univers de la vente au détail dans lequel les données de vente à partir de terminaux de sale (POS) individuels doivent être routées vers un système de gestion de stock qui utilise les données pour déterminer à quel moment le stock doit être réapprovisionné. Cette solution utilise le Bus des services de messagerie pour la communication entre les terminaux et le système de gestion de stock, comme illustré dans la figure suivante :

![Image de files d’attente de Bus de service 1](./media/service-bus-create-queues/IC657161.gif)

Chaque terminal POS signale ses données commerciales en envoyant des messages à l' **DataCollectionQueue**. Ces messages restent dans cette file d’attente jusqu'à ce qu’ils soient récupérés par le système de gestion de stock. Ce modèle est souvent appelé *une messagerie asynchrone*, car le terminal POS n’a pas à attendre une réponse à partir du système de gestion de stocks pour poursuivre le traitement.

## <a name="why-queuing"></a>Pourquoi queuing ?

Avant d’examiner le code nécessaire à la configuration de cette application, prendre en compte les avantages de l’utilisation d’une file d’attente dans ce scénario, au lieu des TPV parler directement (de façon synchrone) pour le système de gestion de stock.

### <a name="temporal-decoupling"></a>Découplage temporelle

Avec le modèle de messagerie asynchrone, producteurs et consommateurs n’ont pas à être en ligne en même temps. L’infrastructure de messagerie fiable stocke les messages jusqu'à ce que la partie (consommateur) est prête à les recevoir. Cela signifie que les composants de l’application distribuée peuvent être déconnectés, soit volontairement ; par exemple, pour la maintenance, ou suite à une panne de composant, sans affecter l’ensemble du système. En outre, l’application consommatrice ne peut traiter qu’être en ligne pendant certaines heures de la journée. Par exemple, dans ce scénario de vente au détail, le système de gestion de stock uniquement peut-être en ligne après la fin de la journée.

### <a name="load-leveling"></a>Nivellement de charge

Dans de nombreuses applications charge système varie avec le temps, alors que le temps de traitement requis pour chaque unité de travail est généralement constant. Echanger producteurs de message et les consommateurs avec une file d’attente signifie que l’application consommatrice (le travailleur) ne doit être mis en service pour une charge moyenne plutôt qu’un pic de charge de service. La profondeur de la file d’attente s’agrandit et contrat comme varie en fonction de la charge entrante. Cela directement économise de l’argent en ce qui concerne le montant de l’infrastructure requise pour traiter la charge de l’application.

![Image de files d’attente de Bus de service 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Équilibrage de la charge

À mesure que la charge augmente, plus les processus de travail peuvent être ajoutés pour lire à partir de la file d’attente de travail. Chaque message est traité par un seul processus de travail. En outre, cette extraction d’équilibrage de charge permet pour une utilisation optimale des ordinateurs de travail même si les ordinateurs de travail diffèrent en ce qui concerne la puissance de traitement, comme ils extrait les messages à leur propre taux maximal. Le modèle de consommateur concurrents est souvent qualifié de ce modèle.

![Image de files d’attente de Bus de service 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Couplage lâche

À l’aide de message queuing d’intermédiaire entre les consommateurs et les producteurs de message fournit un intrinsèque couplage lâche entre les composants. Étant donné que les producteurs et les consommateurs ne sont pas conscients des uns des autres, un consommateur peut être mis à niveau sans avoir aucun effet sur le producteur. En outre, la topologie de messagerie peut évoluer sans affecter les points de terminaison existants. Nous y reviendrons plus lorsque nous parlons de publication et d’abonnement.

## <a name="show-me-the-code"></a>Afficher le code

La section suivante montre comment utiliser le Bus de Service pour générer cette application.

### <a name="sign-up-for-an-azure-account"></a>Inscrivez-vous à un compte Azure

Vous aurez besoin d’un compte Azure pour commencer à travailler avec le Bus de Service. Si vous n’en avez pas encore, vous pouvez vous inscrire pour obtenir un compte gratuit [ici](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="create-a-namespace"></a>Créer un espace de noms

Une fois que vous avez un abonnement, vous pouvez [créer un nouvel espace de noms](service-bus-create-namespace-portal.md). Chaque espace de noms agit comme un conteneur pour un ensemble d’entités de Bus de Service d’étendue. Donner un nom unique à votre nouvel espace de noms sur tous les comptes de Service Bus. 

### <a name="install-the-nuget-package"></a>Installez le package NuGet

Pour utiliser l’espace de noms du Bus de Service, une application doit référencer l’assembly de Bus des services, en particulier Microsoft.ServiceBus.dll. Vous pouvez trouver cet assembly dans le cadre du Kit de développement Microsoft Azure, et que le téléchargement est disponible sur la [page de téléchargement du Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/). Toutefois, le [package NuGet de Bus de Service](https://www.nuget.org/packages/WindowsAzure.ServiceBus) est le moyen le plus simple d’obtenir l’API du Bus de Service et configurer votre application avec toutes les dépendances de Service Bus.

### <a name="create-the-queue"></a>Créer la file d’attente

Opérations de gestion pour le Bus de Service d’échanges d’entités (rubriques de files d’attente et de publication et d’abonnement) sont effectuées via la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Bus de service utilise un modèle de sécurité en fonction de [Signature de l’accès partagé (SAS)](service-bus-sas-overview.md) . La classe [TokenProvider a](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) représente un fournisseur de jeton de sécurité avec les méthodes de fabrique intégré retourner certains fournisseurs bien connus de jetons. Nous allons utiliser une méthode [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) pour stocker les informations d’identification d’associations de sécurité. L’instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) est puis construit avec l’adresse de base de l’espace de noms du Service Bus et le fournisseur de jetons.

La classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fournit des méthodes pour créer, énumérer et supprimer des entités de messagerie. Le code qui est présenté ici montre comment l’instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) est créé et utilisé pour créer la file d’attente de **DataCollectionQueue** .

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Notez qu’il existe des surcharges de la méthode [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) qui permettent les propriétés de la file d’attente pour être réglé. Par exemple, vous pouvez définir la valeur par défaut time-to-live (TTL) pour les messages envoyés à la file d’attente.

### <a name="send-messages-to-the-queue"></a>Envoyer des messages à la file d’attente

Pour le moment de l’exécution des opérations sur les entités de Bus de Service ; par exemple, envoyer et recevoir des messages, une application doit créer tout d’abord un objet [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . Semblable à la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , l’instance de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) est créé à partir de l’adresse de base de l’espace de noms du service et le fournisseur de jetons.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

Pour les messages envoyés et reçus à partir de Bus de Service files d’attente sont des instances de la classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Cette classe compose d’un ensemble de propriétés standard (par exemple, [l’étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et de [la propriété TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire qui sert à contenir les propriétés de l’application et un corps de données d’application arbitraire. Une application peut affecter l’organisme en passant tout objet sérialisable (l’exemple suivant passe dans un objet **DonnéesVentes** qui représente les données de ventes à partir du terminal POS), qui utilise le [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) pour sérialiser l’objet. Sinon, un objet de [flux](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) peut être fourni.

Pour envoyer des messages à une file d’attente donnée, dans notre cas, le **DataCollectionQueue**, le plus simple est d’utiliser [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) pour créer un objet [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) directement à partir de l’instance de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Réception de messages de la file d’attente

Pour recevoir des messages de la file d’attente, vous pouvez utiliser un objet de [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) que vous créez directement à partir de la [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) à l’aide de [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Les récepteurs de messages peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**. La [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) est définie lorsque le destinataire du message est créé, en tant que paramètre à l’appel de [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) .


Lorsque vous utilisez le mode **ReceiveAndDelete** , la réception est une opération de prise de vue unique ; Autrement dit, lorsque le Bus de Service reçoit la requête, il marque le message comme étant consommé et retourne à l’application. **ReceiveAndDelete** est le modèle le plus simple et plus adapté à des scénarios dans lesquels l’application peut tolérer ne pas traitement d’un message si une défaillance. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service marqué le message comme étant consommé, lorsque les redémarrages de l’application et commence à lire des messages, il sera ont manqué le message qui a été utilisé avant l’incident.

En **PeekLock** mode, la réception devient une opération de deux étapes, ce qui permet de prendre en charge les applications qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit la requête, recherche du message suivant devant être consommés, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant [complète](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sur le message reçu. Lorsque le Bus des services voit la [complète](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) appeler, il marque le message comme étant consommé.

Deux autres résultats sont possibles. Tout d’abord, si l’application ne parvient pas à traiter le message pour une raison quelconque, il peut appeler [abandonner](https://msdn.microsoft.com/library/azure/hh181837.aspx) sur le message reçu (au lieu de [Terminer](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Ainsi, le Bus de Service déverrouiller le message et le rendre disponible à recevoir à nouveau, soit par le même client ou par un autre consommateur de fin. En second lieu, il y a un délai d’attente associé au verrou et si l’application ne peut pas traiter le message avant du verrou de délai d’attente expire (par exemple, si l’application se bloque), Bus de Service sera déverrouiller le message et le rendre disponible à recevoir à nouveau (essentiellement en effectuant une opération [d’Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) par défaut).

Notez que si l’application se bloque après elle traite le message, mais avant la [fin](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) la demande a été émise, le message sera remis à nouveau à l’application lorsqu’il redémarre. Ceci est souvent appelé traitement *Au moins une fois* . Cela signifie que chaque message sera traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, une logique supplémentaire est nécessaire dans l’application pour détecter les doublons. Ceci peut être réalisé en fonction de la propriété [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) du message. La valeur de cette propriété reste constante entre les tentatives de remise. Cela est appelé *Une seule fois* de traitement.

Le code qui est présenté ici reçoit et traite un message en utilisant le mode **PeekLock** , qui est la valeur par défaut si aucune valeur de [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) n’est explicitement fournie.

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
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

### <a name="use-the-queue-client"></a>Utiliser le client de la file d’attente

Les exemples plus haut dans cette section créé des objets [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) et [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) directement à partir de la [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) pour envoyer et recevoir des messages de la file d’attente, respectivement. Une autre approche est d’utiliser la classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) , qui prend en charge les opérations send et receive en plus des fonctionnalités plus avancées, telles que des sessions.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base des files d’attente, reportez-vous à la section [créer les applications qui utilisent des abonnements et des rubriques du Bus de Service](service-bus-create-topics-subscriptions.md) pour continuer cette discussion en utilisant les fonctions de publication et d’abonnement des thèmes du Bus de Service et des abonnements.