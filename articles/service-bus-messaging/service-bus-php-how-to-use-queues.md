<properties 
    pageTitle="L’utilisation de files d’attente de Bus de Service avec PHP | Microsoft Azure" 
    description="Découvrez comment utiliser les files d’attente de Bus de Service dans Azure. Exemples de code écrits en PHP." 
    services="service-bus" 
    documentationCenter="php" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>L’utilisation de files d’attente de Bus de Service

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ce guide vous montre comment utiliser les files d’attente de Bus de Service. Les exemples sont écrits en PHP et utilisent le [Kit de développement logiciel Azure pour PHP](../php-download-sdk.md). Les scénarios présentés incluent la **Création de files d’attente**, **Envoyer et recevoir des messages**et **suppression de files d’attente**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Création d’une application PHP

La seule condition requise pour la création d’une application PHP qui accède au service d’Azure Blob est de référencer des classes dans le [SDK Azure pour PHP](../php-download-sdk.md) à partir de votre code. Vous pouvez utiliser les outils de développement pour créer votre application, ou le bloc-notes.

> [AZURE.NOTE] Votre installation PHP doit également avoir l' [extension de OpenSSL](http://php.net/openssl) installé et activé.

Dans ce guide, vous allez utiliser les fonctionnalités de service qui peuvent être appelées à partir d’une application PHP localement ou dans le code s’exécutant dans un rôle web Azure, le rôle de travail ou le site Web.

## <a name="get-the-azure-client-libraries"></a>Obtenir le client Azure bibliothèques

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser le Bus de Service

Pour utiliser la file d’attente de Bus de Service API, effectuez les opérations suivantes :

1. Une référence au fichier de chargeur automatique à l’aide de la [require_once] [ require_once] instruction.
2. Référencer toutes les classes que vous pouvez utiliser.

L’exemple suivant montre comment inclure le fichier de chargeur automatique et faire référence à la classe **ServicesBuilder** .

> [AZURE.NOTE] Cet exemple montre comment (et autres exemples dans cet article) suppose que vous avez installé les bibliothèques clientes PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement ou sous la forme d’un package de poire, vous devez référencer le fichier de chargeur automatique **WindowsAzure.php** .

```
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Dans les exemples ci-dessous, les `require_once` instruction est toujours affichée, mais uniquement les classes nécessaires pour exécuter l’exemple sont référencés.

## <a name="set-up-a-service-bus-connection"></a>Configurer une connexion de Bus des services

Pour instancier un client de Bus des services, vous devez avoir une chaîne de connexion valide dans ce format :

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Où **point de terminaison** est généralement au format `[yourNamespace].servicebus.windows.net`.

Pour créer un client de service d’Azure, vous devez utiliser la classe **ServicesBuilder** . Vous pouvez :

* Passez la chaîne de connexion directement.
* Utiliser le **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
    * Par défaut, il est fourni avec prise en charge d’une source externe - variables d’environnement
    * Vous pouvez ajouter de nouvelles sources en étendant la classe **ConnectionStringSource**

Pour les exemples présentés ici, la chaîne de connexion sera passée directement.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="how-to-create-a-queue"></a>Comment : créer une file d’attente

Vous pouvez effectuer des opérations de gestion des files d’attente de Bus de Service via la classe **ServiceBusRestProxy** . Un objet **ServiceBusRestProxy** est construit par la méthode de fabrique **ServicesBuilder::createServiceBusService** avec une chaîne de connexion appropriée qui encapsule les autorisations de jetons pour le gérer.

L’exemple suivant montre comment instancier un **ServiceBusRestProxy** et appelez **ServiceBusRestProxy -> createQueue** pour créer une file d’attente nommée `myqueue` dans un `MySBNamespace` espace de noms de service :

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    $queueInfo = new QueueInfo("myqueue");
        
    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [AZURE.NOTE] Vous pouvez utiliser la `listQueues` méthode sur `ServiceBusRestProxy` objets pour vérifier si une file d’attente avec un nom spécifié existe déjà dans un espace de noms.

## <a name="how-to-send-messages-to-a-queue"></a>Comment : envoyer des messages à une file d’attente

Pour envoyer un message à une file d’attente de Bus de Service, votre application appelle la méthode **ServiceBusRestProxy -> sendQueueMessage** . Le code suivant montre comment envoyer un message à la `myqueue` file d’attente créée précédemment dans le `MySBNamespace` espace de noms de service.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");
    
    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Messages envoyés à (et reçus à partir de) files d’attente de Bus de Service sont des instances de la classe **BrokeredMessage** . **BrokeredMessage** objets possèdent un ensemble de méthodes standard (par exemple, **getLabel**, **getTimeToLive**, **setLabel**et **setTimeToLive**) et des propriétés qui sont utilisées pour stocker des propriétés personnalisées spécifiques à l’application et un corps de données d’application arbitraire.

Files d’attente de Bus de service prend en charge une taille maximale de message de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche de la prime](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés de l’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’y a aucune limite sur le nombre de messages dans une file d’attente, mais il existe une extrémité sur la taille totale des messages émanant d’une file d’attente. Cette limite sur la taille de la file d’attente est de 5 Go.

## <a name="how-to-receive-messages-from-a-queue"></a>Comment recevoir des messages d’une file d’attente

Le meilleur moyen de recevoir des messages d’une file d’attente est d’utiliser une méthode **ServiceBusRestProxy -> receiveQueueMessage** . Les messages peuvent être reçus dans deux modes différents : **ReceiveAndDelete** (par défaut) et **PeekLock**.

Lors de l’utilisation du mode **ReceiveAndDelete** , recevoir est une opération de prise de vue unique - autrement dit, lorsque le Bus de Service reçoit une demande de lecture d’un message dans une file d’attente, il marque le message comme étant consommé et retourne à l’application. **ReceiveAndDelete** est le modèle le plus simple et plus adapté à des scénarios dans lequel une application tolère ne pas traiter un message en cas de panne. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service vous avez marqué le message comme étant consommé, puis lorsque l’application redémarre et commence à consommer des messages, il sera ont manqué le message qui a été utilisé avant l’incident.

En **PeekLock** mode, la réception d’un message devient une opération deux étapes, ce qui permet aux applications de prise en charge qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit une demande, il recherche le message suivant à consommer verrous pour empêcher les autres utilisateurs à partir de la réception et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en passant le message reçu à **ServiceBusRestProxy -> deleteMessage**. Lorsque le Bus des services voit le **deleteMessage** appeler, il marque le message comme étant consommé et supprimer de la file d’attente.

L’exemple suivant montre comment un message peut être reçu et traités à l’aide du mode de **PeekLock** (pas le mode par défaut).

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
        
    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/windowsazure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment : gérer l’application tombe en panne et messages illisibles

Bus de service fournit des fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou les difficultés de traitement d’un message. Si une application de récepteur ne parvient pas à traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur le message reçu (au lieu de la méthode **deleteMessage** ). Cela entraînera le Bus de Service déverrouiller le message dans la file d’attente et le rendre disponible à recevoir à nouveau, soit par la même application consommatrice, soit par une autre application prend beaucoup de.

Il existe également un délai d’expiration d’un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), puis le Bus de Service sera déverrouiller le message automatiquement et le rendre disponible à recevoir à nouveau.

Dans le cas où l’application se bloque après avoir traité le message, mais avant l’émission de la demande de **deleteMessage** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. On appelle souvent cela **Au moins une fois la transformation**; en d’autres termes, chaque message est traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis ajouter une logique supplémentaire aux applications pour gérer la remise des messages en double est recommandé. Cette opération est souvent réalisée à l’aide de la méthode **getMessageId** du message, qui reste constante entre les tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base des files d’attente de Bus de Service, reportez-vous à la section [files d’attente, des rubriques et des abonnements][] pour plus d’informations.

Pour plus d’informations, consultez également le [Centre pour développeurs PHP](/develop/php/).

[Files d’attente, des rubriques et des abonnements]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once

 
