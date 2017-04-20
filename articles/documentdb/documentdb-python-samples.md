<properties 
    pageTitle="Exemples de NoSQL Python pour DocumentDB | Microsoft Azure" 
    description="Trouver les NoSQL Python exemples de github pour les tâches courantes dans les DocumentDB, y compris les opérations CRUD pour les documents JSON dans les bases de données NoSQL." 
    keywords="exemples de Python"
    services="documentdb" 
    authors="moderakh" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter="python"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/18/2016" 
    ms.author="moderakh"/>


# <a name="documentdb-python-examples"></a>Exemples les DocumentDB Python

> [AZURE.SELECTOR]
- [Exemples .NET](documentdb-dotnet-samples.md)
- [Exemples de Node.js](documentdb-nodejs-samples.md)
- [Exemples de Python](documentdb-python-samples.md)
- [Galerie d’exemples de Code Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Exemples de solutions qui effectuent des opérations et autres opérations courantes sur Azure DocumentDB ressources sont incluses dans le référentiel de GitHub [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) . Cet article fournit :

- Liens vers les tâches de chacun des fichiers projet exemple Python. 
- Des liens vers le API connexe référencent contenu.

**Conditions préalables**

1. Vous avez besoin d’un compte Azure pour utiliser ces exemples de Python :
    - Vous pouvez [Ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/): vous obtenez des crédits vous permet d’essayer les services Azure payés et même après leur utilisation jusqu'à vous pouvez conserver le compte et utilisation libre des services Azure, tels que les sites Web. Votre carte de crédit ne sera jamais être débitée, sauf si vous modifiez vos paramètres explicitement et que vous demandez à percevoir.
   - Vous pouvez [Activer les avantages de Visual Studio d’abonné](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): votre Visual Studio l’abonnement fournit les crédits que vous pouvez utiliser pour des services Azure payés chaque mois.
2. Vous devez également les [Python SDK](documentdb-sdk-python.md). 

    > [AZURE.NOTE] Chaque échantillon est autonome, il définit lui-même et nettoie après lui-même. En tant que tel, les échantillons émettre plusieurs appels à [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Chaque fois que cela est fait à votre abonnement est facturé pour 1 heure d’utilisation par le niveau de performances de la collection en cours de création. 

## <a name="database-examples"></a>Exemples de base de données

Le fichier [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) du projet [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Créer une base de données](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) | [document_client. CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Un compte pour une base de données de requête](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) | [document_client. QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Lire une base de données par Id](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) | [document_client. Readmodèle](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Liste des bases de données d’un compte](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) | [document_client. ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Supprimer une base de données](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) | [document_client. DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)

## <a name="collection-examples"></a>Exemples de collection 

Le fichier [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) du projet [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Créer une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) | [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Lire une liste de toutes les collections dans une base de données](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) | [document_client. ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Obtenir une collection par Id](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) | [document_client. ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Obtenir le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) | [DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Modifier le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) | [document_client. ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Suppression d’une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) | [document_client. DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
