<properties
    pageTitle="Comment faire pour utiliser le stockage de file d’attente à partir de Node.js | Microsoft Azure"
    description="Apprenez à utiliser le service de file d’attente d’Azure pour créer et supprimer des files d’attente, insérer, obtenir et supprimer les messages. Exemples écrits en Node.js."
    services="storage"
    documentationCenter="nodejs"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>


# <a name="how-to-use-queue-storage-from-nodejs"></a>Comment faire pour utiliser le stockage de file d’attente à partir de Node.js

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous explique comment exécuter des scénarios courants utilisant le service de file d’attente de Microsoft Azure. Ces exemples sont écrits à l’aide de l’API Node.js. Les scénarios présentés comprennent **Insertion**, **lecture**, **mise en route**et **suppression de** file d’attente messages, ainsi que **Création et suppression de files d’attente**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Créer une Application Node.js

Créez une application Node.js vide. Pour des instructions de création d’une application Node.js, voir [créer une application web de Node.js dans le Service d’application Azure], [Générer et déployer une application Node.js à un Service de Cloud Azure] à l’aide de Windows PowerShell, ou [Générer et déployer une application web Node.js Azure à l’aide de Web Matrix].

## <a name="configure-your-application-to-access-storage"></a>Configurez votre Application pour le stockage d’accès

Pour utiliser le stockage Azure, vous devez le Kit de développement de stockage Azure pour Node.js, qui inclut un ensemble de bibliothèques de commodité qui communiquent avec les services de reste de stockage.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilisez le nœud Gestionnaire de package (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande tels que **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix), accédez au dossier où vous avez créé votre exemple d’application.

2.  Dans la fenêtre de commande, tapez **npm installer le stockage azure** . Sortie de la commande est semblable à l’exemple suivant.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier si un **nœud\_modules** dossier a été créé. À l’intérieur de ce dossier, vous trouverez le package de **stockage azure** , qui contient les bibliothèques que vous avez besoin pour accéder au stockage.

### <a name="import-the-package"></a>Importer le package

À l’aide du bloc-notes ou un autre éditeur de texte, ajoutez le code suivant en haut du fichier **server.js** de l’application dans laquelle vous avez l’intention d’utiliser le stockage :

    var azure = require('azure-storage');

## <a name="setup-an-azure-storage-connection"></a>Programme d’installation une connexion de stockage Azure

Le module azure lira les variables d’environnement AZURE\_stockage\_compte et AZURE\_stockage\_accès\_clé ou AZURE\_stockage\_connexion\_chaîne pour les informations requises pour vous connecter à votre compte de stockage Azure. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l’appel de **createQueueService**.

Pour obtenir un exemple de définition des variables d’environnement dans [Azure Portal](https://portal.azure.com) pour un site Web d’Azure, consultez [application de web Node.js à l’aide du Service de Table Azure].

## <a name="how-to-create-a-queue"></a>Comment : Créer une file d’attente

Le code suivant crée un objet **QueueService** , qui vous permet d’utiliser des files d’attente.

    var queueSvc = azure.createQueueService();

Utilisez la méthode **createQueueIfNotExists** , qui renvoie la file d’attente spécifiée si elle existe déjà ou crée une nouvelle file d’attente avec le nom spécifié, s’il n’existe pas déjà.

    queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
      }
    });

Si la file d’attente est créé, `result.created` a la valeur true. Si la file d’attente existe, `result.created` a la valeur false.

### <a name="filters"></a>Filtres

Les opérations de filtrage facultatives peuvent être appliquées aux opérations effectuées à l’aide de **QueueService**. Opérations de filtrage peuvent inclure la journalisation, l’automatiquement une nouvelle tentative, etc.. Les filtres sont des objets qui implémentent une méthode avec la signature :

    function handle (requestOptions, next)

Après avoir effectué le prétraitement sur les options de requête, la méthode doit appeler « suivant », en passant un rappel avec la signature suivante :

    function (returnObject, finalCallback, next)

Dans ce rappel et après le traitement de la returnObject (la réponse de la demande sur le serveur), le rappel doit appeler ensuite s’il existe pour poursuivre le traitement des autres filtres ou simplement l’appel de finalCallback dans le cas contraire pour finir l’appel de service.

Deux filtres qui implémentent la logique des nouvelles tentatives sont inclus dans le Kit de développement Azure Node.js, **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **QueueService** qui utilise **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="how-to-insert-a-message-into-a-queue"></a>Comment : Insérer un Message dans une file d’attente

Pour insérer un message dans une file d’attente, utilisez la méthode **createMessage** pour créer un nouveau message et l’ajouter à la file d’attente.

    queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
      if(!error){
        // Message inserted
      }
    });

## <a name="how-to-peek-at-the-next-message"></a>Comment : Lire le Message suivant

Vous pouvez lire le message avant une file d’attente sans le supprimer de la file d’attente en appelant la méthode **peekMessages** . Par défaut, **peekMessages** affiche un seul message.

    queueSvc.peekMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
      }
    });

Le `result` contient le message.

> [AZURE.NOTE] À l’aide de **peekMessages** lorsqu’il y a qu'aucun message dans la file d’attente ne renvoie pas une erreur, cependant aucun messages ne seront retournés.

## <a name="how-to-dequeue-the-next-message"></a>Comment : File d’attente du Message suivant

Traitement d’un message est un processus en deux étapes :

1. File d’attente du message.

2. Supprimer le message.

Pour retirer un message, utilisez **getMessages**. Ainsi, les messages invisibles dans la file d’attente, pour aucun autre client ne peut les traiter. Une fois que votre application a traité un message, appelez **deleteMessage** pour le supprimer de la file d’attente. L’exemple suivant obtient un message, puis la supprime :

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
          if(!error){
            //message deleted
          }
        });
      }
    });

> [AZURE.NOTE] Par défaut, un message est masqué uniquement pendant 30 secondes, après quoi elle est visible pour les autres clients. Vous pouvez spécifier une valeur différente à l’aide de `options.visibilityTimeout` avec **getMessages**.

> [AZURE.NOTE]
> À l’aide de **getMessages** lorsqu’il y a qu'aucun message dans la file d’attente ne renvoie pas une erreur, cependant aucun messages ne seront retournés.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Comment : Modifier le contenu d’un Message en file d’attente

Vous pouvez modifier le contenu d’un message sur place dans la file d’attente à l’aide de **updateMessage**. L’exemple suivant met à jour le texte d’un message :

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Got the message
        var message = result[0];
        queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
          if(!error){
            // Message updated successfully
          }
        });
      }
    });

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Comment : Obtenir des Options supplémentaires pour la file d’attente des Messages

Il existe deux méthodes que vous pouvez personnaliser la récupération des messages à partir d’une file d’attente :

* `options.numOfMessages`-Récupérer un lot de messages (32 maximum.)
* `options.visibilityTimeout`-Définir un délai d’expiration de l’invisibilité plus ou moins longtemps.

L’exemple suivant utilise la méthode **getMessages** pour obtenir les 15 messages en un seul appel. Puis il traite chaque message en utilisant une boucle for. Il définit également le délai d’expiration de l’invisibilité à cinq minutes pour tous les messages retournés par cette méthode.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
      if(!error){
        // Messages retreived
        for(var index in result){
          // text is available in result[index].messageText
          var message = result[index];
          queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
            if(!error){
              // Message deleted
            }
          });
        }
      }
    });

## <a name="how-to-get-the-queue-length"></a>Comment : Obtenir la longueur de la file d’attente

Le **getQueueMetadata** retourne des métadonnées relatives à la file d’attente, y compris le nombre approximatif de messages en attente dans la file d’attente.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
      if(!error){
        // Queue length is available in result.approximateMessageCount
      }
    });

## <a name="how-to-list-queues"></a>Comment : Files d’attente de liste

Pour récupérer une liste de files d’attente, utilisez **listQueuesSegmented**. Pour récupérer une liste filtrée par un préfixe spécifique, utilisez **listQueuesSegmentedWithPrefix**.

    queueSvc.listQueuesSegmented(null, function(error, result, response){
      if(!error){
        // result.entries contains the list of queues
      }
    });

Si les files d’attente ne peut pas être retournés, `result.continuationToken` peut servir en tant que premier paramètre de la **listQueuesSegmented** ou le deuxième paramètre de **listQueuesSegmentedWithPrefix** pour récupérer davantage de résultats.

## <a name="how-to-delete-a-queue"></a>Comment : Supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’il contient, appelez la méthode **deleteQueue** sur l’objet de file d’attente.

    queueSvc.deleteQueue(queueName, function(error, response){
      if(!error){
        // Queue has been deleted
      }
    });

Pour effacer tous les messages d’une file d’attente sans le supprimer, utilisez **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Comment : manipuler les Signatures d’accès partagé

Signatures d’accès partagé (SAS) sont un moyen sécurisé pour fournir un accès granulaire aux files d’attente sans fournir votre nom de compte de stockage ou des clés. Associations de sécurité sont souvent utilisées pour fournir un accès limité à vos files d’attente, par exemple permettant à une application mobile envoyer des messages.

Une application de confiance comme un service en nuage génère un SAS à l’aide de la **generateSharedAccessSignature** de la **QueueService**et lui fournit à une application non fiable ou de niveau de confiance partiel. Par exemple, il s’agit d’une application mobile. Les associations de sécurité sont générée à l’aide d’une stratégie, qui décrit les dates de début et de fin au cours de laquelle les associations de sécurité sont valide, ainsi que le niveau d’accès accordé au titulaire du SAS.

L’exemple suivant génère une nouvelle stratégie d’accès partagé qui autorise le détenteur SAS ajouter des messages à la file d’attente et expiration de 100 minutes après l’heure de que sa création.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

    var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
    var host = queueSvc.host;

Notez que les informations d’hôte doivent être également, comme il est requis lorsque le titulaire SAS tente d’accéder à la file d’attente.

L’application cliente utilise les associations de sécurité avec **QueueServiceWithSAS** pour effectuer des opérations sur la file d’attente. L’exemple suivant se connecte à la file d’attente et crée un message.

    var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
    sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
      if(!error){
        //message added
      }
    });

Étant donné que les associations de sécurité a été générée avec access d’ajouter, si une tentative a été faite pour lire, de mettre à jour ou de supprimer des messages, une erreur est renvoyée.

### <a name="access-control-lists"></a>Listes de contrôle d’accès

Vous pouvez également utiliser une liste de contrôle d’accès (ACL) pour définir la stratégie d’accès pour un SAS. Cela est utile si vous souhaitez permettre à plusieurs clients pour accéder à la file d’attente, mais fournissent des stratégies d’accès différentes pour chaque client.

Une ACL est implémentée à l’aide d’un tableau des stratégies d’accès, dont l’ID est associé à chaque stratégie. L’exemple suivant définit deux stratégies ; une pour « Utilisateur1 » et l’autre pour 'utilisateur2' :

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

L’exemple suivant obtient l’ACL en cours pour **myqueue**, puis ajoute les nouvelles stratégies à l’aide de **setQueueAcl**. Cette approche permet de :

    var extend = require('extend');
    queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Une fois que l’ACL a été définie, vous pouvez ensuite créer un SAS basé sur l’ID d’une stratégie. L’exemple suivant crée un nouveau SAS pour 'utilisateur2' :

    queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du stockage de la file d’attente, cliquez sur ces liens pour en savoir plus sur les tâches de stockage plus complexes.

-   Visitez le [Blog de l’équipe stockage Azure][].
-   Visitez le référentiel du [Kit de développement logiciel de nœud de stockage Azure][] sur GitHub.

  [Kit de développement logiciel de nœud de stockage Azure]: https://github.com/Azure/azure-storage-node
  [using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: https://portal.azure.com
  [Créer une application web de Node.js dans le Service d’application Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Application web de Node.js à l’aide du Service de Table Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md


  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png



  [Générer et déployer une application Node.js à un Service de Cloud Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Blog de l’équipe stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Générer et déployer une application web de Node.js vers Azure à l’aide de Web Matrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
