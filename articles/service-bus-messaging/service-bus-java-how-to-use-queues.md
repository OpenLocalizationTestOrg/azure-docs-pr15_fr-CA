<properties
    pageTitle="Comment utiliser les files d’attente de Bus de Service avec Java | Microsoft Azure"
    description="Découvrez comment utiliser les files d’attente de Bus de Service dans Azure. Exemples de code écrits en Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>L’utilisation de files d’attente de Bus de Service

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Cet article décrit comment utiliser les files d’attente de Bus de Service. Les exemples sont écrits en Java et utilisent le [Azure SDK pour Java][]. Les scénarios présentés incluent la **Création de files d’attente**, **Envoyer et recevoir des messages**et **suppression de files d’attente**.

## <a name="what-are-service-bus-queues"></a>Que sont les files d’attente de Bus de Service ?

Files d’attente de Bus de service prend en charge un modèle de communication **par courtier de messagerie** . Lorsque vous utilisez des files d’attente, les composants d’une application distribuée ne communiquent pas directement avec eux ; au lieu de cela ils échangent des messages via une file d’attente, qui agit comme un intermédiaire (broker). Un producteur de message (expéditeur) transmet un message à la file d’attente, puis poursuit son traitement.
En mode asynchrone, un consommateur de message (récepteur) extrait le message de la file d’attente et la traite. Le producteur n’a pas à attendre une réponse du consommateur pour pouvoir continuer à traiter et envoyer des messages. Files d’attente offrent **en premier, premier sorti (FIFO)** remise des messages à un ou plusieurs consommateurs concurrentes. Autrement dit, les messages sont généralement reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente, et chaque message est reçue et traitée par le consommateur d’un seul message.

![QueueConcepts](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

Files d’attente de Bus de service sont une technologie à usage général qui peut être utilisée pour un large éventail de scénarios :

- Communication entre les rôles web et worker dans une n-tier applications Azure.
- La communication entre les applications sur site et Azure hébergé des applications dans une solution hybride.
- Communication entre les composants d’une application distribuée en cours d’exécution sur site dans les différentes organisations ou départements d’une organisation.

À l’aide de files d’attente vous permet plus facilement faire évoluer vos applications et activer la résilience de votre architecture.

## <a name="create-a-service-namespace"></a>Créer un espace de noms du service

Pour commencer à utiliser des files d’attente de Bus de Service dans Azure, vous devez d’abord créer un espace de noms. Un espace de noms fournit un conteneur de portée pour l’adressage des ressources de Bus de Service au sein de votre application.

Pour créer un espace de noms :

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser le Bus de Service

Assurez-vous que vous avez installé le [Azure SDK pour Java][] avant la génération de cet exemple. Si vous utilisez Eclipse, vous pouvez installer le [Shared Computer Toolkit Azure pour Eclipse][] qui inclut l’Azure SDK pour Java. Vous pouvez ensuite ajouter les **Bibliothèques d’Azure Microsoft pour Java** à votre projet :

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Ajoutez le code suivant `import` instructions au début du fichier Java :

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Créer une file d’attente

Les opérations de gestion des files d’attente de Bus de Service peuvent être effectuées via la classe **ServiceBusContract** . Construit un objet **ServiceBusContract** avec une configuration appropriée qui encapsule le jeton d’associations de sécurité avec les autorisations pour gérer, et la classe **ServiceBusContract** est le seul point de communication avec Azure.

La classe **ServiceBusService** fournit des méthodes pour créer, énumérer et supprimer des files d’attente. L’exemple ci-dessous montre comment un objet **ServiceBusService** peut être utilisé pour créer une file d’attente nommée « TestQueue », avec un espace de noms « HowToSample » :

```
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Méthodes autoriser des propriétés de la file d’attente pour être réglée sur **QueueInfo** (par exemple : la valeur par défaut time-to-live (TTL) à appliquer aux messages envoyés à la file d’attente). L’exemple suivant montre comment créer une file d’attente nommée `TestQueue` avec une taille maximale de 5 Go :

````
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Notez que vous pouvez utiliser la méthode **listQueues** sur les objets de **ServiceBusContract** pour vérifier si une file d’attente avec un nom spécifié existe déjà dans un espace de noms du service.

## <a name="send-messages-to-a-queue"></a>Envoyer des messages à une file d’attente

Pour envoyer un message à une file d’attente de Bus de Service, votre application obtient un objet **ServiceBusContract** . Le code suivant montre comment envoyer un message pour le `TestQueue` file d’attente créée précédemment dans le `HowToSample` espace de noms :

```
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Pour les messages envoyés et reçus à partir de Bus de Service files d’attente sont des instances de la classe [BrokeredMessage][] . [BrokeredMessage][] objets ont un ensemble de propriétés standard (par exemple, [l’étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et de [la propriété TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire qui est utilisée pour inclure des propriétés spécifiques d’application personnalisée et un corps de données d’application arbitraire. Une application peut définir le corps du message en passant tout objet sérialisable dans le constructeur de la [BrokeredMessage][]et le sérialiseur approprié est utilisé pour sérialiser l’objet. Sinon, vous pouvez fournir un java **. E/S. InputStream** objet.

L’exemple suivant montre comment envoyer cinq tester des messages à la `TestQueue` **MessageSender** , nous avons obtenus dans l’extrait de code précédent :

```
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

Files d’attente de Bus de service prend en charge une taille maximale de message de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche de la prime](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés de l’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’y a aucune limite sur le nombre de messages dans une file d’attente, mais il existe une extrémité sur la taille totale des messages émanant d’une file d’attente. Cette taille de file d’attente est définie au moment de la création, avec un maximum de 5 Go.

## <a name="receive-messages-from-a-queue"></a>Recevoir des messages d’une file d’attente

Le principal moyen pour recevoir des messages d’une file d’attente consiste à utiliser un objet **ServiceBusContract** . Messages reçus peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**.

Lors de l’utilisation du mode **ReceiveAndDelete** , recevoir est une opération de prise de vue unique - autrement dit, lorsque le Bus de Service reçoit une demande de lecture d’un message dans une file d’attente, il marque le message comme étant consommé et retourne à l’application. Mode **ReceiveAndDelete** (qui est le mode par défaut) est le modèle le plus simple et le plus adapté à des scénarios dans lequel une application tolère ne pas traiter un message en cas de défaillance. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter.
Étant donné que le Bus de Service vous avez marqué le message comme étant consommé, puis lorsque l’application redémarre et commence à consommer des messages, il sera ont manqué le message qui a été utilisé avant l’incident.

En mode de **PeekLock** , recevoir devient une opération deux étapes, ce qui permet aux applications de prise en charge qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit une demande, il recherche du message suivant devant être consommés, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant **Supprimer** sur le message reçu. Lorsque le Service Bus voit l’appel **Supprimer** , il marque le message comme étant consommé et supprimer de la file d’attente.

L’exemple suivant montre comment les messages peuvent être reçus et traitement à l’aide du mode de **PeekLock** (pas le mode par défaut). L’exemple ci-dessous est une boucle infinie et traite les messages dès leur arrivée dans notre « TestQueue » :

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer l’application tombe en panne et messages illisibles

Bus de service fournit des fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou les difficultés de traitement d’un message. Si une application de récepteur ne parvient pas à traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur le message reçu (au lieu de la méthode **deleteMessage** ). Cela entraînera le Bus de Service déverrouiller le message dans la file d’attente et le rendre disponible à recevoir à nouveau, soit par la même application consommatrice, soit par une autre application prend beaucoup de.

Il existe également un délai d’expiration d’un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), puis le Bus de Service sera déverrouiller le message automatiquement et le rendre disponible à recevoir à nouveau.

Dans le cas où l’application se bloque après avoir traité le message, mais avant l’émission de la demande de **deleteMessage** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. On appelle souvent cela **Au moins une fois traitement**, autrement dit, chaque message sera traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis les développeurs d’applications doivent ajouter une logique supplémentaire à leur application pour gérer la remise des messages en double. Cette opération est souvent réalisée à l’aide de la méthode **getMessageId** du message, qui reste constante entre les tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base des files d’attente de Bus de Service, reportez-vous à la section [files d’attente, des rubriques et des abonnements][] pour plus d’informations.

Pour plus d’informations, consultez le [Centre pour développeurs Java](/develop/java/).


  [Azure SDK pour Java]: http://azure.microsoft.com/develop/java/
  [Azure Shared Computer Toolkit pour Éclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Files d’attente, des rubriques et des abonnements]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

