<properties 
    pageTitle="L’utilisation des thèmes du Bus de Service avec PHP | Microsoft Azure" 
    description="Découvrez comment utiliser les thèmes du Bus de Service avec PHP dans Azure." 
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
    ms.date="10/14/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Comment faire pour utiliser des abonnements et des rubriques du Bus des services

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article vous montre comment utiliser les abonnements et les rubriques du Bus de Service. Les exemples sont écrits en PHP et utilisent le [Kit de développement logiciel Azure pour PHP](../php-download-sdk.md). Les scénarios présentés incluent la **Création de rubriques et les abonnements**, **Création de filtres d’abonnement**, **Envoyer des messages à une rubrique**, **recevoir des messages à partir d’un abonnement**et **suppression des rubriques et des abonnements**.

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Création d’une application PHP

La seule condition requise pour la création d’une application PHP qui accède au service d’objet Blob d’Azure consiste à référencer des classes dans le [SDK Azure pour PHP](../php-download-sdk.md) à partir de votre code. Vous pouvez utiliser les outils de développement pour créer votre application, ou le bloc-notes.

> [AZURE.NOTE] Votre installation PHP doit également avoir l' [extension de OpenSSL](http://php.net/openssl) installé et activé.

Cet article décrit comment utiliser les fonctionnalités du service qui peuvent être appelées au sein d’une application PHP localement ou dans le code s’exécutant dans un rôle web Azure, le rôle de travail ou le site Web.

## <a name="get-the-azure-client-libraries"></a>Obtenir le client Azure bibliothèques

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser le Bus de Service

Pour utiliser les API de Bus de Service :

1. Une référence au fichier de chargeur automatique à l’aide de la [require_once] [ require-once] instruction.
2. Référencer toutes les classes que vous pouvez utiliser.

L’exemple suivant montre comment inclure le fichier de chargeur automatique et faire référence à la classe **ServiceBusService** .

> [AZURE.NOTE] Cet exemple montre comment (et autres exemples dans cet article) suppose que vous avez installé les bibliothèques clientes PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement ou sous la forme d’un package de poire, vous devez référencer le fichier de chargeur automatique **WindowsAzure.php** .

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Dans les exemples suivants, la `require_once` instruction est toujours affichée, mais uniquement les classes nécessaires pour exécuter l’exemple sont référencés.

## <a name="set-up-a-service-bus-connection"></a>Configurer une connexion de Bus des services

Pour instancier un client de Service Bus vous devez contenir une chaîne de connexion valide de ce format :

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Où `Endpoint` est généralement au format `https://[yourNamespace].servicebus.windows.net`.

Pour créer un client de service d’Azure, vous devez utiliser la classe **ServicesBuilder** . Vous pouvez :

* Passez la chaîne de connexion directement.
* Utiliser le **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
    * Par défaut, il est livré avec la prise en charge d’une source externe - variables d’environnement.
    * Vous pouvez ajouter de nouvelles sources en étendant la classe **ConnectionStringSource** .

Pour les exemples présentés ici, la chaîne de connexion est passée directement.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
    
$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Créer une rubrique

Vous pouvez effectuer des opérations de gestion des rubriques du Bus de Service via la classe **ServiceBusRestProxy** . Un objet **ServiceBusRestProxy** est construit par la méthode de fabrique **ServicesBuilder::createServiceBusService** avec une chaîne de connexion appropriée qui encapsule les autorisations de jetons pour le gérer.

L’exemple suivant montre comment instancier un **ServiceBusRestProxy** et appelez **ServiceBusRestProxy -> createTopic** pour créer une rubrique nommée `mytopic` dans un `MySBNamespace` espace de noms :

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;
    
// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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

> [AZURE.NOTE] Vous pouvez utiliser la `listTopics` méthode sur `ServiceBusRestProxy` objets pour vérifier si une rubrique avec un nom spécifié existe déjà dans un espace de noms du service.

## <a name="create-a-subscription"></a>Créer un abonnement

Abonnement à une rubrique est également créés avec la méthode **ServiceBusRestProxy -> createSubscription** . Les abonnements sont nommés et peuvent disposer d’un filtre facultatif qui restreint l’ensemble des messages transmis à la file d’attente virtuelle de l’abonnement.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Le filtre de le **MatchAll** est le filtre par défaut qui est utilisé si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement. Lorsque le filtre de le **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « mysubscription » et utilise le filtre de **MatchAll** par défaut.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec des filtres

Vous pouvez également définir des filtres qui vous permettent de spécifier lesquelles messages envoyés à une rubrique doivent apparaître dans un abonnement à une rubrique spécifique. Le type de filtre pris en charge par les abonnements plus souple est le **SqlFilter**, qui implémente un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur SqlFilters, consultez [Les propriétés SqlFilter.SqlExpression][sqlfilter].

> [AZURE.NOTE] Chaque règle sur un abonnement traite les messages entrants de manière indépendante, ajout de leurs messages de résultat à l’abonnement. En outre, chaque nouvel abonnement a un objet de la **règle** par défaut avec un filtre qui ajoute tous les messages à partir de la rubrique à l’abonnement. Pour recevoir uniquement les messages correspondant à votre filtre, vous devez supprimer la règle par défaut. Vous pouvez supprimer la règle par défaut à l’aide de la `ServiceBusRestProxy->deleteRule` méthode.

L’exemple suivant crée un abonnement nommé **HighMessages** avec un **SqlFilter** qui sélectionne uniquement les messages dont une propriété personnalisée de **format** supérieure à 3 (voir [Envoyer des messages à une rubrique](#send-messages-to-a-topic) pour plus d’informations sur l’ajout de propriétés personnalisées pour les messages) :

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Notez que ce code nécessite l’utilisation d’un espace de noms supplémentaire : `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

De la même façon, l’exemple suivant crée un abonnement nommé **LowMessages** avec un **SqlFilter** qui sélectionne uniquement les messages qui ont une propriété **MessageNumber** inférieure ou égale à 3 :

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Désormais, lorsqu’un message est envoyé à la `mytopic` rubrique, il est toujours livré aux récepteurs d’abonné à la `mysubscription` abonnement et sélectivement livrée aux récepteurs d’abonné à la `HighMessages` et `LowMessages` abonnements (en fonction du contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoyer des messages à une rubrique

Pour envoyer un message à une rubrique du Bus des services, votre application appelle la méthode **ServiceBusRestProxy -> sendTopicMessage** . Le code suivant montre comment envoyer un message à la `mytopic` rubrique précédemment créé dans la `MySBNamespace` espace de noms de service.

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
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Les messages envoyés aux rubriques du Bus de Service sont des instances de la classe **BrokeredMessage** . **BrokeredMessage** objets possèdent un ensemble de propriétés standard et méthodes (par exemple, **getLabel**, **getTimeToLive**, **setLabel**et **setTimeToLive**), ainsi que les propriétés qui peuvent être utilisées pour stocker des propriétés personnalisées spécifiques à l’application. L’exemple suivant montre comment envoyer des messages test 5 du `mytopic` rubrique précédemment créé. La méthode **setProperty** est utilisée pour ajouter une propriété personnalisée (`MessageNumber`) à chaque message. Notez que la `MessageNumber` valeur de la propriété varie sur chaque message (vous pouvez utiliser cette valeur pour déterminer les abonnements reçoivent, comme indiqué dans la section [Création d’un abonnement](#create-a-subscription) ) :

```
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);
            
    // Set custom property.
    $message->setProperty("MessageNumber", $i);
            
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Rubriques du Bus de service prend en charge une taille maximale de message de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche de la prime](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés de l’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’y a aucune limite sur le nombre de messages conservés dans une rubrique, mais il existe un embout de la taille totale des messages émanant d’une rubrique. Cette limite de taille de rubrique est de 5 Go. Pour plus d’informations sur les quotas, reportez-vous à la section [quotas de Bus de Service][].

## <a name="receive-messages-from-a-subscription"></a>Recevoir des messages à partir d’un abonnement

Le meilleur moyen de recevoir des messages à partir d’un abonnement est d’utiliser une méthode **ServiceBusRestProxy -> receiveSubscriptionMessage** . Messages reçus peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** (par défaut) et **PeekLock**.

Lors de l’utilisation du mode **ReceiveAndDelete** , recevoir est une opération de prise de vue unique ; Autrement dit, lorsque le Bus de Service reçoit une demande de lecture d’un message dans un abonnement, il marque le message comme étant consommé et retourne à l’application. **ReceiveAndDelete** est le modèle le plus simple et plus adapté à des scénarios dans lequel une application tolère ne pas traiter un message en cas de panne. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service vous avez marqué le message comme étant consommé, puis lorsque l’application redémarre et commence à consommer des messages, il sera ont manqué le message qui a été utilisé avant l’incident.

En **PeekLock** mode, la réception d’un message devient une opération deux étapes, ce qui permet aux applications de prise en charge qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit une demande, il recherche du message suivant devant être consommés, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en passant le message reçu à **ServiceBusRestProxy -> deleteMessage**. Lorsque le Bus des services voit le **deleteMessage** appeler, il marque le message comme étant consommé et supprimer de la file d’attente.

L’exemple suivant montre comment recevoir et de traiter un message à l’aide du mode de **PeekLock** (pas le mode par défaut). 

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
    
    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment : gérer l’application tombe en panne et messages illisibles

Bus de service fournit des fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou les difficultés de traitement d’un message. Si une application de récepteur ne parvient pas à traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur le message reçu (au lieu de la méthode **deleteMessage** ). Cela entraînera le Bus de Service déverrouiller le message dans la file d’attente et le rendre disponible à recevoir à nouveau, soit par la même application consommatrice, soit par une autre application prend beaucoup de.

Il existe également un délai d’expiration d’un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), puis le Bus de Service sera déverrouiller le message automatiquement et le rendre disponible à recevoir à nouveau.

Dans le cas où l’application se bloque après avoir traité le message, mais avant l’émission de la demande de **deleteMessage** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. On appelle souvent cela **Au moins une fois la transformation**; en d’autres termes, chaque message est traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis les développeurs d’applications doivent ajouter une logique supplémentaire aux applications pour gérer la remise des messages en double. Cette opération est souvent réalisée à l’aide de la méthode **getMessageId** du message, qui reste constante entre les tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer les rubriques et les abonnements

Pour supprimer une rubrique ou un abonnement, utilisez respectivement les méthodes **ServiceBusRestProxy -> deleteSubscripton** , ou l' **ServiceBusRestProxy -> deleteTopic** . Notez que la suppression d’une rubrique supprime également tous les abonnements qui sont enregistrés à l’aide de la rubrique.

L’exemple suivant montre comment supprimer une rubrique intitulée `mytopic` et ses abonnements inscrits.

```
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

À l’aide de la méthode **deleteSubscription** , vous pouvez supprimer un abonnement indépendamment :

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base des files d’attente de Bus de Service, reportez-vous à la section [files d’attente, des rubriques et des abonnements][] pour plus d’informations.

[Files d’attente, des rubriques et des abonnements]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[Quotas de Bus des services]: service-bus-quotas.md
