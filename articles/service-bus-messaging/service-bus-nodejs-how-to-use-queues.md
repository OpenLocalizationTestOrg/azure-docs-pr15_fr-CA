<properties 
    pageTitle="Comment utiliser les files d’attente de Bus de Service dans Node.js | Microsoft Azure" 
    description="Découvrez comment utiliser les files d’attente de Bus de Service dans Azure à partir d’une application Node.js." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>L’utilisation de files d’attente de Bus de Service

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Cet article décrit comment utiliser les files d’attente de Bus de Service dans Node.js. Les exemples sont écrits en JavaScript et utilisent le module Node.js Azure. Les scénarios présentés incluent la **Création de files d’attente**, **Envoyer et recevoir des messages**et **suppression de files d’attente**. Pour plus d’informations sur les files d’attente, consultez la section [étapes suivantes](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-nodejs-application"></a>Créer une application Node.js

Créez une application Node.js vide. Pour obtenir des instructions sur la création d’une application Node.js, consultez [créer et déployer une application Node.js à un site Web d’Azure][], ou le [Service en nuage Node.js][] à l’aide de Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser le Bus de Service

Pour utiliser le Bus des services Azure, téléchargez et utilisez le package Node.js Azure. Ce package comprend un ensemble de bibliothèques de communiquer avec les services de reste de Bus de Service.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilisez le nœud Gestionnaire de package (NPM) pour obtenir le package

1. Utilisez la fenêtre de commande **Windows PowerShell pour Node.js** pour accéder à la **c:\\nœud\\sbqueues\\WebRole1** dossier dans lequel vous avez créé votre exemple d’application.

2. Tapez **npm installer azure** dans la fenêtre de commande, ce qui devrait se traduire par une sortie similaire à ce qui suit :

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```

3. Vous pouvez exécuter manuellement la commande **ls** pour vérifier si un **nœud\_modules** dossier a été créé. À l’intérieur de ce dossier, recherchez le package **azure** , qui contient les bibliothèques que vous avez besoin d’accéder aux files d’attente de Bus de Service.

### <a name="import-the-module"></a>Le module d’importation

À l’aide du bloc-notes ou un autre éditeur de texte, ajoutez le code suivant en haut du fichier **server.js** de l’application :

```
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configurer une connexion de Bus des services Azure

Le module Azure lit les variables d’environnement AZURE\_SERVICEBUS\_espace de noms et d’AZURE\_SERVICEBUS\_accès\_clé pour obtenir les informations requises pour se connecter à un Bus de Service. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l’appel de **createServiceBusService**.

Pour obtenir un exemple de définir les variables d’environnement dans un fichier de configuration pour un Service de Cloud Azure, consultez [Service de Cloud Node.js avec le stockage][].

Pour obtenir un exemple de définition des variables d’environnement dans [Azure portal classique][] pour un site Web d’Azure, consultez [Application de Web Node.js avec le stockage][].

## <a name="create-a-queue"></a>Créer une file d’attente

L’objet **ServiceBusService** vous permet d’utiliser des files d’attente de Bus de Service. Le code suivant crée un objet **ServiceBusService** . L’ajouter dans la partie supérieure du fichier **server.js** , après l’instruction pour importer le module Azure :

```
var serviceBusService = azure.createServiceBusService();
```

En appelant **createQueueIfNotExists** sur l’objet **ServiceBusService** , la file d’attente spécifié est retournée (si elle existe), ou une nouvelle file d’attente avec le nom spécifié est créée. Le code suivant utilise **createQueueIfNotExists** pour créer ou se connecter à la file d’attente nommée `myqueue`:

```
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

**createServiceBusService** prend également en charge des options supplémentaires qui vous permettent de remplacer les paramètres de file d’attente par défaut tels que de la durée de la taille de la file d’attente direct ou maximale du message. L’exemple suivant définit la taille de la file d’attente maximale de 5 Go et un temps de vie) la valeur TTL (de 1 minute :

```
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtres

Les opérations de filtrage facultatives peuvent être appliquées aux opérations effectuées à l’aide de **ServiceBusService**. Opérations de filtrage peuvent inclure la journalisation, l’automatiquement une nouvelle tentative, etc.. Les filtres sont des objets qui implémentent une méthode avec la signature :

```
function handle (requestOptions, next)
```

Après son traitement préalable sur les options de requête, la méthode doit appeler `next`, en passant un rappel avec la signature suivante :

```
function (returnObject, finalCallback, next)
```

Dans ce rappel et après le traitement de la **returnObject** (la réponse de la demande sur le serveur), le rappel doit appeler `next` s’il existe pour poursuivre le traitement des autres filtres, ou simplement appeler `finalCallback`, qui met fin à l’appel de service.

Deux filtres qui implémentent la logique des nouvelles tentatives sont inclus dans le Kit de développement Azure Node.js, **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **ServiceBusService** qui utilise **ExponentialRetryPolicyFilter**:

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Envoyer des messages à une file d’attente

Pour envoyer un message à une file d’attente de Bus de Service, votre application appelle la méthode **sendQueueMessage** sur l’objet **ServiceBusService** . Messages envoyés à (et reçus à partir de) files d’attente de Bus de Service sont les objets **BrokeredMessage** et disposent d’un ensemble de propriétés standard (par exemple, **l’étiquette** et de **la propriété TimeToLive**), un dictionnaire qui est utilisée pour inclure des propriétés personnalisées spécifiques à l’application et un corps de données d’application arbitraire. Une application peut définir le corps du message en passant une chaîne en tant que message. Toutes les propriétés standard requises sont remplies avec les valeurs par défaut.

L’exemple suivant montre comment envoyer un message de test à la file d’attente nommée `myqueue` à l’aide de **sendQueueMessage**:

```
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Files d’attente de Bus de service prend en charge une taille maximale de message de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche de la prime](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés de l’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’y a aucune limite sur le nombre de messages dans une file d’attente, mais il existe une extrémité sur la taille totale des messages émanant d’une file d’attente. Cette taille de file d’attente est définie au moment de la création, avec un maximum de 5 Go. Pour plus d’informations sur les quotas, reportez-vous à la section [quotas de Bus de Service][].

## <a name="receive-messages-from-a-queue"></a>Recevoir des messages d’une file d’attente

Les messages sont reçus à partir d’une file d’attente à l’aide de la méthode **receiveQueueMessage** sur l’objet **ServiceBusService** . Par défaut, les messages sont supprimés de la file d’attente lorsqu’ils sont lus ; Toutefois, vous pouvez lire (lecture) et verrouiller le message sans le supprimer de la file d’attente en définissant paramètre facultatif **isPeekLock** sur **true**.

Le comportement par défaut de lecture et de suppression du message dans le cadre de l’opération de réception est le modèle le plus simple et plus adapté à des scénarios dans lequel une application tolère ne pas traiter un message en cas de panne. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service vous avez marqué le message comme étant consommé, puis lorsque l’application redémarre et commence à consommer des messages, il sera ont manqué le message qui a été utilisé avant l’incident.

Si le paramètre **isPeekLock** est défini sur **true**, la réception devient une opération deux étapes, ce qui permet aux applications de prise en charge qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit une demande, il recherche du message suivant devant être consommés, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception par l’appel de méthode de **deleteMessage** et fournissant le message à supprimer en tant que paramètre. La méthode **deleteMessage** marquer le message comme étant consommé et supprimer de la file d’attente.

L’exemple suivant montre comment recevoir et traiter des messages à l’aide de **receiveQueueMessage**. L’exemple de premier reçoit et supprime un message, puis reçoit un message à l’aide de la propriété **isPeekLock** la valeur **true**, puis supprime le message à l’aide de **deleteMessage**:

```
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer l’application tombe en panne et messages illisibles

Bus de service fournit des fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou les difficultés de traitement d’un message. Si une application de récepteur ne parvient pas à traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur l’objet **ServiceBusService** . Cela entraînera le Bus de Service déverrouiller le message dans la file d’attente et le rendre disponible à recevoir à nouveau, soit par la même application consommatrice, soit par une autre application prend beaucoup de.

Il existe également un délai d’expiration d’un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), puis le Bus de Service sera déverrouiller le message automatiquement et le rendre disponible à recevoir à nouveau.

Dans le cas où l’application se bloque après avoir traité le message, mais avant l’appel de la méthode **deleteMessage** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. On appelle souvent cela **Au moins une fois traitement**, autrement dit, chaque message sera traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis les développeurs d’applications doivent ajouter une logique supplémentaire à leur application pour gérer la remise des messages en double. Cette opération est souvent réalisée à l’aide de la propriété **MessageId** du message, qui reste constante entre les tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les files d’attente, consultez les ressources suivantes.

-   [Files d’attente, des rubriques et des abonnements][]
-   Référentiel [Azure SDK pour noeud][] de GitHub
-   [Centre de développement Node.js](/develop/nodejs/)

  [Azure SDK de nœud]: https://github.com/Azure/azure-sdk-for-node
  [Azure portal classique]: http://manage.windowsazure.com
  
  [Service de Cloud de Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Files d’attente, des rubriques et des abonnements]: service-bus-queues-topics-subscriptions.md
  [Créer et déployer une application Node.js à un site Web d’Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Service de Cloud de Node.js avec le stockage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Application Web de Node.js avec le stockage]: ../storage/storage-nodejs-how-to-use-table-storage.md
  [Quotas de Bus des services]: service-bus-quotas.md
 
