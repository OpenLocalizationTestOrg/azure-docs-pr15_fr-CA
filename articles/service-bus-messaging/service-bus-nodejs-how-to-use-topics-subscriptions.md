<properties 
    pageTitle="L’utilisation des thèmes du Bus de Service avec Node.js | Microsoft Azure" 
    description="Découvrez comment utiliser les thèmes du Bus de Service et des abonnements dans Azure à partir d’une application Node.js." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Comment les abonnements et les rubriques du Bus de Service d’utilisation

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ce guide explique comment utiliser des thèmes du Bus de Service et des abonnements à partir d’applications de Node.js. Les scénarios présentés incluent la **Création de rubriques et les abonnements**, **Création de filtres d’abonnement**, **l’envoi de messages** à une rubrique, **recevoir des messages à partir d’un abonnement**et **Supprimer des abonnements et des rubriques**. Pour plus d’informations sur les rubriques et les abonnements, consultez la section [étapes suivantes](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Créer une application Node.js

Créez une application Node.js vide. Pour obtenir des instructions sur la création d’une application Node.js, consultez [créer et déployer une application Node.js à un Site Web de Azure], [Service de Cloud Node.js][] à l’aide de Windows PowerShell, ou un Site Web avec WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser le Bus de Service

Pour utiliser le Service Bus, téléchargez le package de Node.js Azure. Ce package comprend un ensemble de bibliothèques de communiquer avec les services de reste de Bus de Service.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilisez le nœud Gestionnaire de package (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande tels que **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix), accédez au dossier où vous avez créé votre exemple d’application.

2.  Tapez **npm installer azure** dans la fenêtre de commande, ce qui devrait la sortie suivante :

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

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier si un **nœud\_modules** dossier a été créé. À l’intérieur de ce dossier, recherchez le package **azure** , qui contient les bibliothèques que vous avez besoin pour accéder aux rubriques du Bus de Service.

### <a name="import-the-module"></a>Le module d’importation

À l’aide du bloc-notes ou un autre éditeur de texte, ajoutez le code suivant en haut du fichier **server.js** de l’application :

```
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurer une connexion de Bus des services

Le module Azure lit les variables d’environnement AZURE\_SERVICEBUS\_espace de noms et d’AZURE\_SERVICEBUS\_accès\_clé pour les informations requises pour se connecter à un Bus de Service. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l’appel de **createServiceBusService**.

Pour obtenir un exemple de définir les variables d’environnement dans un fichier de configuration pour un Service de Cloud Azure, consultez [Service de Cloud Node.js avec le stockage][].

Pour obtenir un exemple de définition des variables d’environnement dans [Azure portal classique][] pour un site Web d’Azure, consultez [Application de Web Node.js avec le stockage][].

## <a name="create-a-topic"></a>Créer une rubrique

L’objet **ServiceBusService** vous permet d’utiliser des thèmes. Le code suivant crée un objet **ServiceBusService** . L’ajouter dans la partie supérieure du fichier **server.js** , après l’instruction pour importer le module azure :

```
var serviceBusService = azure.createServiceBusService();
```

En appelant **createTopicIfNotExists** sur l’objet **ServiceBusService** , la rubrique spécifiée sera retournée (si elle existe), ou une nouvelle rubrique, portant le nom spécifié est créée. Le code suivant utilise **createTopicIfNotExists** pour créer ou se connecter à la rubrique nommée « MyTopic » :

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** prend également en charge des options supplémentaires qui vous permettent de remplacer les paramètres de rubrique par défaut tels que la durée de vie du message ou de la taille du nombre de rubriques. L’exemple suivant définit la taille maximale de rubrique pour 5 Go avec une durée de vie de 1 minute :

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtres

Les opérations de filtrage facultatives peuvent être appliquées aux opérations effectuées à l’aide de **ServiceBusService**. Opérations de filtrage peuvent inclure la journalisation, l’automatiquement une nouvelle tentative, etc.. Les filtres sont des objets qui implémentent une méthode avec la signature :

```
function handle (requestOptions, next)
```

Après l’exécution de prétraitement sur les options de requête, la méthode appelle `next` en passant un rappel avec la signature suivante :

```
function (returnObject, finalCallback, next)
```

Dans ce rappel et après le traitement de la **returnObject** (la réponse de la demande sur le serveur), le rappel soit doit appeler ensuite s’il existe pour poursuivre le traitement des autres filtres ou simplement l’appel de **finalCallback** dans le cas contraire pour finir l’appel de service.

Deux filtres qui implémentent la logique des nouvelles tentatives sont inclus dans le Kit de développement Azure Node.js, **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **ServiceBusService** qui utilise **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="create-subscriptions"></a>Créer des abonnements

Abonnement à une rubrique est également créés avec l’objet **ServiceBusService** . Les abonnements sont nommés et peuvent avoir un filtre facultatif qui limite l’ensemble des messages remis à la file d’attente virtuelle de l’abonnement.

> [AZURE.NOTE] Les abonnements sont persistantes et continueront à exister jusqu'à ce qu’une d’elles, ou la rubrique, ils sont associés, sont supprimés. Si votre application contient la logique pour créer un abonnement, il doit tout d’abord vérifier si l’abonnement existe déjà à l’aide de la méthode **getSubscription** .

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Le filtre de le **MatchAll** est le filtre par défaut qui est utilisé si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement. Lorsque le filtre de le **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « AllMessages » et utilise le filtre de **MatchAll** par défaut.

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec des filtres

Vous pouvez également créer des filtres qui permettent de vous à portée de messages envoyés à une rubrique devez s’afficher dans un abonnement à une rubrique spécifique.

Le type de filtre pris en charge par les abonnements plus souple est le **SqlFilter**, qui implémente un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur les expressions qui peuvent être utilisées avec un filtre SQL, passez en revue les [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] syntaxe.

Les filtres peuvent être ajoutés à un abonnement à l’aide de la méthode **createRule** de l’objet **ServiceBusService** . Cette méthode vous permet d’ajouter de nouveaux filtres à un abonnement existant.

> [AZURE.NOTE] Comme le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez d’abord supprimer le filtre par défaut ou l' **MatchAll** remplace tous les autres filtres que vous pouvez spécifier. Vous pouvez supprimer la règle par défaut à l’aide de la méthode **deleteRule** de l’objet **ServiceBusService** .

L’exemple suivant crée un abonnement nommé `HighMessages` avec un **SqlFilter** qui sélectionne uniquement les messages qui comportent une propriété personnalisée **messagenumber** supérieure à 3 :

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

De la même façon, l’exemple suivant crée un abonnement nommé `LowMessages` avec un **SqlFilter** qui sélectionne uniquement les messages qui ont une propriété **messagenumber** inférieure ou égale à 3 :

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Lorsqu’un message est maintenant envoyé à `MyTopic`, elle est envoyée à l’abonné à des récepteurs la `AllMessages` abonnement de rubrique et sélectivement livrée aux récepteurs d’abonné à la `HighMessages` et `LowMessages` abonnement à une rubrique (en fonction du contenu du message).

## <a name="how-to-send-messages-to-a-topic"></a>Comment faire pour envoyer des messages à une rubrique

Pour envoyer un message à une rubrique du Bus des services, votre application doit utiliser la méthode **sendTopicMessage** de l’objet **ServiceBusService** .
Les messages envoyés aux rubriques du Bus de Service sont les objets **BrokeredMessage** .
**BrokeredMessage** objets ont un ensemble de propriétés standard (par exemple, **l’étiquette** et de **la propriété TimeToLive**), un dictionnaire qui est utilisée pour inclure des propriétés spécifiques d’application personnalisée et un corps de données de type chaîne. Une application peut définir le corps du message en passant une valeur de chaîne à la **sendTopicMessage** et toutes les propriétés standard requises seront remplies par les valeurs par défaut.

L’exemple suivant montre comment envoyer cinq messages à 'MyTopic' de test. Notez que la valeur de la propriété **format** de chaque message varie en fonction de l’itération de la boucle (Cela permet de déterminer les abonnements recevoir) :

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Rubriques du Bus de service prend en charge une taille maximale de message de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche de la prime](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés de l’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’y a aucune limite sur le nombre de messages conservés dans une rubrique, mais il existe un embout de la taille totale des messages émanant d’une rubrique. Cette taille de rubrique est définie au moment de la création, avec un maximum de 5 Go.

## <a name="receive-messages-from-a-subscription"></a>Recevoir des messages à partir d’un abonnement

Les messages sont reçus à partir d’un abonnement à l’aide de la méthode **receiveSubscriptionMessage** sur l’objet **ServiceBusService** . Par défaut, les messages sont supprimés de l’abonnement lorsqu’ils sont lus ; Toutefois, vous pouvez lire (lecture) et verrouiller le message sans le supprimer de l’abonnement en définissant paramètre facultatif **isPeekLock** sur **true**.

Le comportement par défaut de lecture et de suppression du message dans le cadre de l’opération de réception est le modèle le plus simple et plus adapté à des scénarios dans lequel une application tolère ne pas traiter un message en cas de panne. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service vous avez marqué le message comme étant consommé, puis lorsque l’application redémarre et commence à consommer des messages, il sera ont manqué le message qui a été utilisé avant l’incident.

Si le paramètre **isPeekLock** est défini sur **true**, la réception devient une opération deux étapes, ce qui permet aux applications de prise en charge qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit une demande, il recherche du message suivant devant être consommés, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application.
Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception par l’appel de méthode de **deleteMessage** et fournissant le message à supprimer en tant que paramètre. La méthode **deleteMessage** va marquer le message comme étant consommé et le supprimer de l’abonnement.

L’exemple suivant montre comment les messages peuvent être reçus et traitement à l’aide de **receiveSubscriptionMessage**. L’exemple reçoit d’abord supprime un message de l’abonnement de 'LowMessages' et puis reçoit un message provenant de l’abonnement de 'HighMessages' à l’aide de **isPeekLock** la valeur true. Il supprime ensuite le message à l’aide de **deleteMessage**:

```
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer l’application tombe en panne et messages illisibles

Bus de service fournit des fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou les difficultés de traitement d’un message. Si une application de récepteur ne parvient pas à traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur l’objet **ServiceBusService** . Cela entraînera le Bus de Service déverrouiller le message dans l’abonnement et le rendre disponible à recevoir à nouveau, soit par la même application consommatrice, soit par une autre application prend beaucoup de.

Il existe également un délai d’expiration d’un message verrouillé au sein de l’abonnement, et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), puis déverrouille le message automatiquement de Bus de Service et le rend disponible à recevoir à nouveau.

Dans le cas où l’application se bloque après avoir traité le message, mais avant l’appel de la méthode **deleteMessage** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. On appelle souvent cela **Au moins une fois traitement**, autrement dit, chaque message sera traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis les développeurs d’applications doivent ajouter une logique supplémentaire à leur application pour gérer la remise des messages en double. Cette opération est souvent réalisée à l’aide de la propriété **MessageId** du message, qui reste constante entre les tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer les rubriques et les abonnements

Rubriques et les abonnements sont persistantes et doivent être supprimés explicitement via [Azure portal classique][] ou par programme.
L’exemple suivant montre comment supprimer la rubrique nommée `MyTopic`:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

Suppression d’une rubrique supprimera également tous les abonnements qui sont enregistrés à l’aide de la rubrique. Les abonnements peuvent également être supprimés indépendamment. L’exemple suivant montre comment supprimer un abonnement nommé `HighMessages` de la `MyTopic` sujet :

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base des rubriques du Bus des services, cliquez sur ces liens pour en savoir plus.

-   Reportez-vous à la section [files d’attente, des rubriques et des abonnements][].
-   Référence des API pour [SqlFilter][].
-   Visitez le référentiel [Azure SDK pour nœud][] sur GitHub.

  [Azure SDK de nœud]: https://github.com/Azure/azure-sdk-for-node
  [Azure portal classique]: https://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Files d’attente, des rubriques et des abonnements]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Service de Cloud de Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Créer et déployer une application Node.js à un Site Web de Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Service de Cloud de Node.js avec le stockage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Application Web de Node.js avec le stockage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
 
