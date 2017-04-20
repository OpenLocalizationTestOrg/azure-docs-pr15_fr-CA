<properties
    pageTitle="Comment faire pour utiliser le stockage Azure Table Node.js | Microsoft Azure"
    description="Stocker des données structurées dans le nuage avec le stockage Azure Table, un magasin de données NoSQL."
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-nodejs"></a>Comment faire pour utiliser le stockage Azure Table Node.js

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Cette rubrique montre comment exécuter des scénarios courants utilisant le service de la Table d’Azure dans une application Node.js.

Les exemples de code dans cette rubrique supposent que vous disposez déjà d’une application de Node.js. Pour plus d’informations sur la création d’une application Node.js dans Azure, consultez ces rubriques :

- [Créer une application web de Node.js dans le Service d’application Azure](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [Générer et déployer une application web de Node.js vers Azure à l’aide de WebMatrix](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [Générer et déployer une application Node.js à un Service de Cloud Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (à l’aide de Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="configure-your-application-to-access-azure-storage"></a>Configurez votre application pour accéder au stockage d’Azure

Pour utiliser le stockage Azure, vous devez le Kit de développement de stockage Azure pour Node.js, qui inclut un ensemble de bibliothèques de commodité qui communiquent avec les services de reste de stockage.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilisez le nœud Gestionnaire de package (NPM) pour installer le package

1.  Utilisez une interface de ligne de commande **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix) et naviguez jusqu’au dossier où vous avez créé votre application.

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

Ajoutez le code suivant en haut du fichier **server.js** dans votre application :

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurer une connexion de stockage Azure

Le module azure lira les variables d’environnement AZURE\_stockage\_compte et AZURE\_stockage\_accès\_clé ou AZURE\_stockage\_connexion\_chaîne pour les informations requises pour vous connecter à votre compte de stockage Azure. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l’appel de **TableService**.

Pour obtenir un exemple de définition des variables d’environnement dans [Azure Portal](https://portal.azure.com) pour un site Web d’Azure, consultez [application de web Node.js à l’aide du Service de Table Azure].

## <a name="create-a-table"></a>Créer une table

Le code suivant crée un objet **TableService** et l’utilise pour créer une nouvelle table. Ajoutez le code suivant en haut de la **server.js**.

    var tableSvc = azure.createTableService();

L’appel à **createTableIfNotExists** crée une nouvelle table portant le nom spécifié s’il n’existe pas déjà. L’exemple suivant crée un nouveau tableau appelé 'mytable' s’il n’existe pas déjà :

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
      if(!error){
        // Table exists or created
      }
    });

Le `result.created` sera `true` si une nouvelle table est créée, et `false` si la table existe déjà. Le `response` contient des informations sur la demande.

### <a name="filters"></a>Filtres

Les opérations de filtrage facultatives peuvent être appliquées aux opérations effectuées à l’aide de **TableService**. Opérations de filtrage peuvent inclure la journalisation, l’automatiquement une nouvelle tentative, etc.. Les filtres sont des objets qui implémentent une méthode avec la signature :

    function handle (requestOptions, next)

Après avoir effectué le prétraitement sur les options de requête, la méthode doit appeler « suivant », en passant un rappel avec la signature suivante :

    function (returnObject, finalCallback, next)

Dans ce rappel et après le traitement de la returnObject (la réponse de la demande sur le serveur), le rappel doit appeler ensuite s’il existe pour poursuivre le traitement des autres filtres ou simplement appeler finalCallback sinon pour mettre fin à l’appel de service.

Deux filtres qui implémentent la logique des nouvelles tentatives sont inclus dans le Kit de développement Azure Node.js, **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **TableService** qui utilise **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Pour ajouter une entité, tout d’abord créer un objet qui définit les propriétés d’entité. Toutes les entités doivent contenir une **PartitionKey** et une **RowKey**, qui sont des identificateurs uniques pour l’entité.

* **PartitionKey** - détermine que l’entité est stockée dans la partition

* **RowKey** - identifie de façon unique l’entité au sein de la partition

À la fois **PartitionKey** et **RowKey** doivent être des valeurs de chaîne. Pour plus d’informations, consultez [Présentation du modèle de données de Service de Table](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Voici un exemple de définition d’une entité. Notez que **dueDate** est défini comme un type de **Edm.DateTime**. Le type est facultatif et types seront déduits si n’est pas spécifiés.

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [AZURE.NOTE] Il existe également un champ **d’horodatage** pour chaque enregistrement, qui est définie par Azure lorsqu’une entité est insérée ou mis à jour.

Vous pouvez également utiliser **entityGenerator** pour créer des entités. L’exemple suivant crée la même entité de tâche à l’aide de **entityGenerator**.

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Pour ajouter une entité à la table, passez l’objet d’entité à la méthode **insertEntity** .

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
      if(!error){
        // Entity inserted
      }
    });

Si l’opération réussit, `result` contiendra l' [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) de l’enregistrement inséré et `response` contient des informations sur l’opération.

Réponse d’exemple :

    { '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] Par défaut, **insertEntity** ne renvoie pas l’entité insérée dans le cadre de la `response` informations. Si vous prévoyez d’exécuter d’autres opérations sur cette entité, ou que vous souhaitez mettre en cache les informations, il peut être utile de disposer d’avoir renvoyé dans le cadre de la `result`. Vous pouvez effectuer cela en activant **echoContent** comme suit :
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Mise à jour d’une entité

Il existe plusieurs méthodes mettre à jour d’une entité existante :

* **replaceEntity** - met à jour une entité existante en le remplaçant

* **mergeEntity** - met à jour une entité existante en fusionnant les nouvelles valeurs de propriété dans l’entité existante

* **insertOrReplaceEntity** - met à jour une entité existante en le remplaçant. Si aucune entité n’existe, une nouvelle sera insérée

* **insertOrMergeEntity** - met à jour une entité existante en fusionnant des nouvelles valeurs de propriété dans l’objet existant. Si aucune entité n’existe, une nouvelle sera insérée

L’exemple suivant illustre la mise à jour d’une entité à l’aide de **replaceEntity**:

    tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] Par défaut, mise à jour d’une entité ne vérifie pas pour voir si les données en cours de mise à jour a déjà été modifiées par un autre processus. Pour prendre en charge les mises à jour simultanées :
>
> 1. Obtenir l’ETag de l’objet mis à jour. Cette valeur est retournée dans le cadre de la `response` pour toute entité concernant l’opération et peut être récupérée via `response['.metadata'].etag`.
>
> 2. Lorsque vous effectuez une opération de mise à jour sur une entité, ajoutez des informations ETag extraites précédemment à la nouvelle entité. Par exemple :
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3. Effectuer l’opération de mise à jour. Si l’entité a été modifiée depuis que vous avez récupéré la valeur ETag, tel qu’une autre instance de votre application, une `error` sera renvoyé indiquant que la condition de mise à jour spécifiée dans la demande n’était pas satisfaite.

Avec **replaceEntity** et **mergeEntity**, si l’entité est en cours de mise à jour n’existe pas, l’opération de mise à jour va échouer. Par conséquent si vous souhaitez stocker une entité, indépendamment de si elle existe déjà, utilisez **insertOrReplaceEntity** ou **insertOrMergeEntity**.

Le `result` de mise à jour réussie, opérations contiendra l' **Etag** de l’entité mise à jour.

## <a name="work-with-groups-of-entities"></a>Travailler avec des groupes d’entités

Il est parfois judicieux d’envoyer plusieurs opérations dans un lot pour garantir atomique de traitement par le serveur. Pour ce faire, utilisez la classe **TableBatch** pour créer un lot et puis utiliser la méthode **executeBatch** de **TableService** pour effectuer les opérations par lots.

 L’exemple suivant illustre l’envoi de deux entités dans un lot :

    var task1 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'Take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };
    var task2 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '2'},
      description: {'_':'Wash the dishes'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };

    var batch = new azure.TableBatch();

    batch.insertEntity(task1, {echoContent: true});
    batch.insertEntity(task2, {echoContent: true});

    tableSvc.executeBatch('mytable', batch, function (error, result, response) {
      if(!error) {
        // Batch completed
      }
    });

Pour les opérations de lot réussie, `result` contient des informations pour chaque opération dans le lot.

### <a name="work-with-batched-operations"></a>Utilisation des opérations par lots

Opérations ajoutées à un lot peuvent être inspectées en affichant le `operations` propriété. Vous pouvez également utiliser les méthodes suivantes pour travailler avec des opérations :

* **Effacer** - efface toutes les opérations d’un lot

* **getOperations** - Obtient une opération dans le lot

* **hasOperations** - renvoie la valeur true si le lot contient des opérations

* **removeOperations** - supprime une opération

* **taille** - renvoie le nombre d’opérations dans le lot

## <a name="retrieve-an-entity-by-key"></a>Récupérer une entité par clé

Pour retourner une entité spécifique en fonction de la **PartitionKey** et **RowKey**, utilisez la méthode **retrieveEntity** .

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

Une fois cette opération terminée, `result` contient l’entité.

## <a name="query-a-set-of-entities"></a>Interroger un ensemble d’entités

Pour interroger une table, utilisez l’objet **TableQuery** pour créer une expression de requête en utilisant les clauses suivantes :

* **Sélectionnez** - les champs à renvoyer à partir de la requête

* **où** - where clause

    * **et** - un `and` condition where

    * **ou** - une `or` condition where

* **haut** - le nombre d’éléments à extraire


L’exemple suivant crée une requête qui renvoie les cinq premiers éléments avec un PartitionKey de 'hometasks'.

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

**Sélectionnez** n’étant pas utilisé, tous les champs seront retournés. Pour exécuter la requête sur une table, utilisez **queryEntities**. L’exemple suivant utilise une requête pour renvoyer des entités à partir de 'mytable'.

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

En cas de réussite, `result.entries` contient un tableau d’entités correspondant à la requête. Si la requête n’a pas pu renvoyer toutes les entités, `result.continuationToken` non -*null* et utilisable comme troisième paramètre de **queryEntities** pour récupérer davantage de résultats. Pour la requête initiale, utilisez *null* pour le troisième paramètre.

### <a name="query-a-subset-of-entity-properties"></a>Un sous-ensemble des propriétés de l’entité de requête

Une requête dans une table peut récupérer quelques champs à partir d’une entité.
Cela réduit la bande passante et peut améliorer les performances de la requête, en particulier pour les grandes entités. Utilisation de la clause **select** et passe les noms des champs à renvoyer. Par exemple, la requête suivante retourne uniquement les champs **description** et **dueDate** .

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-an-entity"></a>Supprimer une entité

Vous pouvez supprimer une entité à l’aide de ses clés de partition et de ligne. Dans cet exemple, l’objet **Tâche1** contient les valeurs **RowKey** et **PartitionKey** de l’entité à supprimer. Puis, l’objet est passé à la méthode **deleteEntity** .

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'}
    };

    tableSvc.deleteEntity('mytable', task, function(error, response){
      if(!error) {
        // Entity deleted
      }
    });

> [AZURE.NOTE] Envisagez d’utiliser des ETags lors de la suppression d’éléments, pour vous assurer que l’élément n’a pas été modifié par un autre processus. Pour plus d’informations sur l’utilisation des ETags, consultez [mise à jour d’une entité](#update-an-entity) .

## <a name="delete-a-table"></a>Supprimer une table

Le code suivant supprime une table dans un compte de stockage.

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

Si vous n’êtes pas sûr de l’existence de la table, utilisez **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Utiliser les jetons de continuation

Lorsque vous interrogez des tables pour de grandes quantités de résultats, recherchez les jetons de continuation. Grandes quantités de données peuvent être disponibles pour votre requête que vous n’avez ne peut-être pas réalisé si vous ne créez pas de reconnaître lorsqu’un jeton de continuation est présent.

Résultats de l’objet renvoyé lors de l’interrogation des jeux d’entités un `continuationToken` propriété lorsque ce jeton est présent. Vous pouvez ensuite utiliser ce lors de l’exécution d’une requête de continuer à déplacer entre les entités de la partition et table.

Lors de l’interrogation, le paramètre continuationToken peut être fourni entre l’instance d’objet de requête et la fonction de rappel :

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Si vous Inspectez le `continuationToken` objet, vous trouverez des propriétés telles que `nextPartitionKey`, `nextRowKey` et `targetLocation`, qui peut être utilisé pour itérer tous les résultats.

Il existe également un exemple de continuation dans le mis en pension Node.js de stockage Azure sur GitHub. Recherchez `examples/samples/continuationsample.js`.

## <a name="work-with-shared-access-signatures"></a>Avec des signatures d’accès partagé

Signatures d’accès partagé (SAS) sont un moyen sécurisé de fournir granulaire de l’accès aux tables sans fournir votre nom de compte de stockage ou des clés. Associations de sécurité sont souvent utilisées pour restreindre l’accès à vos données, comme l’autorisation d’une application mobile pour interroger les enregistrements.

Une application de confiance comme un service en nuage génère un SAS à l’aide de la **generateSharedAccessSignature** de la **TableService**et lui fournit à une application non fiable ou de niveau de confiance partiel, comme une application mobile. Les associations de sécurité sont générée à l’aide d’une stratégie, qui décrit les dates de début et de fin au cours de laquelle les associations de sécurité sont valide, ainsi que le niveau d’accès accordé au titulaire du SAS.

L’exemple suivant génère une nouvelle stratégie d’accès partagé qui permettra au détenteur de SAS (« r ») la table de requête et expiration de 100 minutes après l’heure de que sa création.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
    var host = tableSvc.host;

Notez que les informations d’hôte doivent être également, comme il est requis lorsque le titulaire SAS tente d’accéder à la table.

L’application cliente utilise les associations de sécurité avec **TableServiceWithSAS** pour effectuer des opérations sur la table. L’exemple suivant se connecte à la table et exécute une requête.

    var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
    var query = azure.TableQuery()
      .where('PartitionKey eq ?', 'hometasks');

    sharedTableService.queryEntities(query, null, function(error, result, response) {
      if(!error) {
        // result contains the entities
      }
    });

Étant donné que les associations de sécurité a été générée avec accès de requête, si une tentative a été faite pour insérer, mettre à jour ou supprimer des entités, une erreur est renvoyée.

### <a name="access-control-lists"></a>Listes de contrôle d’accès

Vous pouvez également utiliser une liste de contrôle d’accès (ACL) pour définir la stratégie d’accès pour un SAS. Cela est utile si vous souhaitez permettre à plusieurs clients pour accéder à la table, mais fournissent des stratégies d’accès différentes pour chaque client.

Une ACL est implémentée à l’aide d’un tableau des stratégies d’accès, dont l’ID est associé à chaque stratégie. L’exemple suivant définit deux stratégies, un pour « Utilisateur1 » et une pour 'utilisateur2' :

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

L’exemple suivant obtient l’ACL en cours pour la table **hometasks** et ajoute les nouvelles stratégies à l’aide de **setTableAcl**. Cette approche permet de :

    var extend = require('extend');
    tableSvc.getTableAcl('hometasks', function(error, result, response) {
    if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Une fois que l’ACL a été définie, vous pouvez ensuite créer un SAS basé sur l’ID d’une stratégie. L’exemple suivant crée un nouveau SAS pour 'utilisateur2' :

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes.

-   [Blog de l’équipe stockage azure][].
-   Référentiel du [Kit de développement logiciel de nœud de stockage Azure][] sur GitHub.
-   [Centre de développement Node.js](/develop/nodejs/)

  [Kit de développement logiciel de nœud de stockage Azure]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Blog de l’équipe stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [Application web de Node.js à l’aide du Service de Table Azure]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md
