<properties 
    pageTitle="DocumentDB échelle et les tests de performances | Microsoft Azure" 
    description="Apprendre à effectuer une échelle et des tests de performances avec Azure DocumentDB"
    keywords="les tests de performances"
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="performance-and-scale-testing-with-azure-documentdb"></a>Performances et évolutivité de tests avec Azure DocumentDB
Test des performances et échelle est une étape clée dans le développement d’applications. Pour de nombreuses applications, la couche de base de données a un impact significatif sur les performances et l’évolutivité globales et est donc un élément essentiel de tests de performances. [DocumentDB d’Azure](https://azure.microsoft.com/services/documentdb/) est spécialement conçu pour l’évolutivité élastique et des performances prévisibles et par conséquent une solution adaptée aux applications qui ont besoin d’un niveau de base de données de haute performance. 

Cet article est une référence pour les développeurs implémentant des suites de tests de performances pour leurs charges de travail DocumentDB ou évaluer les DocumentDB pour les scénarios de haute performance. Il se concentre essentiellement sur les tests de performances isolé de la base de données, mais également les meilleures pratiques pour les applications de production.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :   

- Où puis-je trouver une exemple .NET d’application cliente pour les tests de performances de DocumentDB d’Azure ? 
- Comment atteindre des niveaux de débit élevé avec Azure DocumentDB de mon application de client ?

Pour vous familiariser avec le code, téléchargez le projet à partir de [l’Exemple de test de performances de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [AZURE.NOTE] L’objectif de cette application est de montrer les meilleures pratiques permettant d’extraire les meilleures performances de DocumentDB avec un petit nombre d’ordinateurs clients. Cela n’a pas été pour démontrer la capacité maximale du service, qui peut s’ajuster limitlessly.

Si vous recherchez des options de configuration côté client améliorer les performances de DocumentDB, consultez [conseils de performance DocumentDB](documentdb-performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Exécutez l’application de test des performances
Le moyen le plus rapide pour commencer est pour compiler et exécuter l’exemple .NET ci-dessous, comme décrit dans les étapes ci-dessous. Vous pouvez également examiner le code source et mettre en œuvre des configurations similaires à vos propres applications client.

**Étape 1 :** Télécharger le projet [d’Exemple de test de performances de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), ou une branche dans le référentiel de Github.

**Étape 2 :** Modifier les paramètres pour EndpointUrl, AuthorizationKey, CollectionThroughput et DocumentTemplate (facultatif) dans App.config.

> [AZURE.NOTE] Avant la mise en service des collections avec un haut débit, veuillez vous reporter à la [Page des prix](https://azure.microsoft.com/pricing/details/documentdb/) pour estimer les coûts par la collection. Stockage des factures DocumentDB et débit de façon indépendante sur une base horaire, afin que vous réaliserez des économies en supprimant ou en diminuant le débit de vos collections de DocumentDB après le test.

**Étape 3 :** Compilez et exécutez l’application console à partir de la ligne de commande. Vous devez voir la sortie comme suit :

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**Étape 4 (si nécessaire) :** Le débit indiqué (RU/s) à partir de l’outil doit être identique ou plus élevé que le débit de mise en service de la collection. Si ce n’est pas le cas, l’augmentation de la DegreeOfParallelism par petits incréments peut-être vous aider à atteindre la limite. Si des plateaux le débit de votre application client, lancer plusieurs instances de l’application sur les machines identiques ou différents vous aide à atteindre la limite de mise en service à travers les différentes instances. Si vous avez besoin d’aide sur cette étape, veuillez écrire un e-mail à askdocdb@microsoft.com ou remplir un ticket de support.

Une fois que vous avez l’application en cours d’exécution, vous pouvez essayer différentes [stratégies d’indexation](documentdb-indexing-policies.md) et des [niveaux de cohérence](documentdb-consistency-levels.md) afin de comprendre leur impact sur le débit et la latence. Vous pouvez également examiner le code source et mettre en œuvre des configurations similaires à vos propres applications de production ou de suites de tests.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, nous avons étudié comment vous pouvez effectuer des performances et une évolutivité test avec DocumentDB à l’aide d’une application de console .NET. Consultez les liens ci-dessous pour plus d’informations sur l’utilisation de DocumentDB.

* [Exemple de test de performances de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Options de configuration de client pour améliorer les performances de DocumentDB](documentdb-performance-tips.md)
* [Le partitionnement dans DocumentDB côté serveur](documentdb-partition-data.md)
* [Collections de DocumentDB et des niveaux de performances](documentdb-performance-levels.md)
* [Documentation du Kit de développement .NET DocumentDB sur MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Exemples de DocumentDB .NET](https://github.com/Azure/azure-documentdb-net)
* [Blog de DocumentDB sur les conseils de performance](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
