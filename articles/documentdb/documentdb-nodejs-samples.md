<properties
    pageTitle="Exemples de NoSQL Node.js pour DocumentDB | Microsoft Azure"
    description="Trouver NoSQL Node.js exemples de github pour les tâches courantes dans les DocumentDB, y compris les opérations CRUD pour les documents JSON dans les bases de données NoSQL."
    keywords="exemples de Node.js"
    services="documentdb"
    authors="moderakh"
    manager="jhubbard"
    editor="monicar"
    documentationCenter="nodejs"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="moderakh"/>


# <a name="documentdb-nodejs-examples"></a>Exemples de DocumentDB Node.js

> [AZURE.SELECTOR]
- [Exemples .NET](documentdb-dotnet-samples.md)
- [Exemples de Node.js](documentdb-nodejs-samples.md)
- [Exemples de Python](documentdb-python-samples.md)
- [Galerie d’exemples de Code Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Exemples de solutions qui effectuent des opérations et autres opérations courantes sur Azure DocumentDB ressources sont incluses dans le référentiel de GitHub [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) . Cet article fournit :

- Liens vers les tâches de chacun des fichiers projet exemple Node.js.
- Des liens vers le API connexe référencent contenu.

**Conditions préalables**

1. Vous avez besoin d’un compte Azure pour utiliser ces exemples de Node.js :
    - Vous pouvez [Ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/): vous obtenez des crédits vous permet d’essayer les services Azure payés et même après leur utilisation jusqu'à vous pouvez conserver le compte et utilisation libre des services Azure, tels que les sites Web. Votre carte de crédit ne sera jamais être débitée, sauf si vous modifiez vos paramètres explicitement et que vous demandez à percevoir.
   - Vous pouvez [Activer les avantages de Visual Studio d’abonné](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): votre Visual Studio l’abonnement fournit les crédits que vous pouvez utiliser pour des services Azure payés chaque mois.
2. Vous devez également le [Kit de développement logiciel Node.js](documentdb-sdk-node.md).

    > [AZURE.NOTE] Chaque échantillon est autonome, il définit lui-même et nettoie après lui-même. En tant que tel, les échantillons émettre plusieurs appels à [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection). Chaque fois que cela est fait à votre abonnement est facturé pour 1 heure d’utilisation par le niveau de performances de la collection en cours de création.

## <a name="database-examples"></a>Exemples de base de données

Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) du projet [DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Créer une base de données](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) | [DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase)
[Un compte pour une base de données de requête](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) | [DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabases)
[Lire une base de données par Id](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) | [DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Liste des bases de données d’un compte](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) | [DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Supprimer une base de données](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) | [DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase)

## <a name="collection-examples"></a>Exemples de collection

Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) du projet [CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Créer une collection](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)
[Lire une liste de toutes les collections dans une base de données](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) | [DocumentClient.readCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollections)
[Obtenir une collection _self](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Obtenir une collection par Id](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Obtenir le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) | [DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers)
[Modifier le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) | [DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer)
[Suppression d’une collection](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) | [DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection)

## <a name="document-examples"></a>Exemples de documents

Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) du projet [DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Créer des documents](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument)
[Lire le document pour une collection](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Lire un document par ID](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Lire le document uniquement si le document a été modifié.](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Requête pour les documents](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) | [DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments)
[Remplacer un document](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |  [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)
[Remplacer le document avec contrôle de ETag conditionnel](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |  [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Supprimer un document](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) | [DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument)

## <a name="indexing-examples"></a>Exemples d’indexation

Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) du projet [IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
Créer une collection avec par défaut l’indexation | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html)
[Indexer un document spécifique](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) | [indexingDirective : 'include'](http://azure.github.io/azure-documentdb-node/global.html#indexingDirective)
[Excluez manuellement un document spécifique à partir de l’index](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) | [RequestOptions.indexingDirective](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Utilisation de l’indexation différée pour l’importation en bloc ou de lire des collections lourdes](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) | [IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#IndexingMode)
[Inclure des chemins spécifiques d’un document dans l’indexation](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) | [IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy)
[Exclure l’indexation de certains chemins d’accès](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) | [ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy)
[Permettre une analyse sur un chemin d’accès de la chaîne lors d’une opération de gamme](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347)| [ExcludedPath.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions)
[Créer un index de la plage sur un chemin d’accès de la chaîne](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) | [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument)
[Créer une collection avec une valeur par défaut indexPolicy, puis mettre à jour en ligne](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html)

Pour plus d’informations sur l’indexation, voir [DocumentDB des stratégies d’indexation](documentdb-indexing-policies.md).

## <a name="server-side-programming-examples"></a>Exemples de programmation côté serveur

Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) du projet [ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Créer une procédure stockée](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) | [DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure)
[Exécuter une procédure stockée](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) | [DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure)

Pour plus d’informations sur la programmation côté serveur, reportez-vous à la section [programmation côté serveur de DocumentDB : procédures stockées, les déclencheurs de base de données et fichiers UDF](documentdb-programming.md).

## <a name="partitioning-examples"></a>Exemples de partitionnement

Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) du projet [partitionnement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Utiliser un HashPartitionResolver](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) | [HashPartitionResolver](http://azure.github.io/azure-documentdb-node/HashPartitionResolver.html)

Pour plus d’informations sur le partitionnement des données dans DocumentDB, consultez [données de Partition et l’échelle dans DocumentDB](documentdb-partition-data.md).
