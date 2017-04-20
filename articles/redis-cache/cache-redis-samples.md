<properties 
    pageTitle="Exemples de Redis de Cache Azure | Microsoft Azure" 
    description="Apprenez à utiliser le Cache de Redis Azure" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-samples"></a>Exemples de Redis de Cache Azure 

Cette rubrique fournit une liste d’exemples de Cache de Redis d’Azure, couvrant les scénarios de connexion à un cache, la lecture et l’écriture des données vers et à partir d’un cache et en utilisant les fournisseurs de Cache de Redis d’ASP.NET. Certains exemples sont des projets téléchargeables, et certains fournissent des instructions et incluent des extraits de code, mais ne sont pas liés à un projet téléchargeable.

## <a name="hello-world-samples"></a>Exemples de Hello world

Les exemples de cette section décrivent les bases de la connexion à une instance de Cache Redis d’Azure et de lecture et d’écriture de données dans le cache à l’aide d’une variété de langues et Redis des clients.

L’exemple [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) illustre comment effectuer diverses opérations de cache à l’aide du client .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

Cet exemple montre comment :

-   Utiliser les diverses options de connexion
-   Lire et écrire des objets dans le cache à l’aide des opérations synchrones et asynchrones
-   Utilisez les commandes MGET/MSET Redis pour renvoyer des valeurs de clés spécifiées.
-   Effectuer des opérations transactionnelles de Redis
-   Travailler avec des listes de Redis et ensembles triés
-   Stocker les objets .NET à l’aide des sérialiseurs de JsonConvert
-   Jeux de Redis permet de mettre en œuvre le balisage
-   Travailler avec Cluster de Redis

Pour plus d’informations, consultez la documentation de [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) sur github et pour plusieurs scénarios d’utilisation, consultez les [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) des tests unitaires.

[Comment utiliser le Cache de Redis d’Azure avec les Python](cache-python-get-started.md) montre comment débuter avec Cache de Redis Azure utilisant les Python et le client [redis-py](https://github.com/andymccurdy/redis-py) .

[Travailler avec des objets dans le cache de .NET](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) vous présente une façon pour sérialiser des objets .NET afin de pouvoir écrire et les lire à partir d’une instance de Cache Redis d’Azure. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Utiliser le Cache de Redis sous la forme d’une mise à l’échelle du fond de panier pour ASP.NET SignalR

[Utiliser le Cache de Redis sous la forme d’une mise à l’échelle du fond de panier pour ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) illustre comment vous pouvez utiliser le Cache de Redis Azure sous la forme d’un fond de panier SignalR. Pour plus d’informations sur le fond de panier, voir [Évolution SignalR avec Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Redis exemple de requête de Cache client

Cet exemple illustre des comparaisons des performances entre l’accès aux données à partir d’un cache et l’accès aux données à partir du stockage de persistance. Cet exemple comporte deux projets.

-   [Comment Redis un Cache peut améliorer les performances en mettant en cache les données de démonstration](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-   [La base de données et du Cache des semences pour la démo](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>La mise en cache de sortie et de l’état de Session ASP.NET

[Utilisez le Azure Redis Cache pour stocker ASP.NET SessionState et OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) illustre comment vous permet d’utiliser le Cache de Redis Azure pour stocker la Session de ASP.NET et de Cache de sortie en utilisant les fournisseurs de SessionState et OutputCache pour Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Gérer le Cache Redis Azure avec MAML

[Gérer le Azure Redis Cache à l’aide de bibliothèques de gestion Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) illustre comment utiliser les bibliothèques de gestion Azure pour gérer - (créer / mettre à jour / Supprimer) votre Cache. 

## <a name="custom-monitoring-sample"></a>Exemple de surveillance de personnalisé

L’exemple de [données de contrôle d’accès Redis Cache](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) montre comment vous pouvez accéder à des données d’analyse pour votre Cache de Redis Azure à l’extérieur du portail Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Un clone de style Twitter écrit à l’aide de PHP et Redis

L’exemple de [Retwis](https://github.com/SyntaxC4-MSFT/retwis) est la Redis Hello World. Il s’agit d’un clone de réseau social Twitter-style minimal écrit à l’aide de Redis et PHP en utilisant le client [Predis](https://github.com/nrk/predis) . Le code source est conçu pour être très simple et en même temps pour afficher les différentes Redis des structures de données.

## <a name="bandwidth-monitor"></a>Moniteur de la bande passante

L’exemple de [Moniteur de la bande passante](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) vous permet de surveiller la bande passante utilisée sur le client. Pour mesurer la bande passante, exécutez l’exemple sur l’ordinateur client de cache et effectuer des appels au cache observer la bande passante indiquée par l’échantillon de moniteur de la bande passante.
