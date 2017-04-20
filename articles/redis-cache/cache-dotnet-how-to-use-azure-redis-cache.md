<properties 
    pageTitle="Comment faire pour utiliser le Cache d’Azure Redis | Microsoft Azure" 
    description="Apprenez à améliorer les performances de vos applications Azure avec Azure Redis de Cache" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache"></a>Comment faire pour utiliser le Cache d’Azure Redis

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Ce guide vous explique comment commencer à utiliser **Le Cache de Redis Azure**. Cache de Redis Microsoft Azure est basée sur la populaire open source Redis le Cache. Il vous permet d’accéder à un cache de Redis dédié, sécurisé, géré par Microsoft. Un cache créé à l’aide du Cache de Redis Azure est accessible à partir de n’importe quelle application de Microsoft Azure.

Cache de Redis Microsoft Azure est disponible dans les niveaux suivants :

-   **Base** – nœud unique. Plusieurs tailles jusqu'à 53 go.
-   **Standard** – principal/réplicas de deux nœuds. Plusieurs tailles jusqu'à 53 go. 99,9 SLA DE %.
-   **Prime** – deux nœuds principal/réplica avec jusqu'à 10 milieu des fragments. Plusieurs tailles de 6 Go à 530 Go (nous contacter pour en savoir plus). Toutes les fonctionnalités de couche Standard et prise en charge, y compris pour [Redis le cluster](cache-how-to-premium-clustering.md) [Redis la persistance](cache-how-to-premium-persistence.md)et [Réseau virtuel d’Azure](cache-how-to-premium-vnet.md). 99,9 SLA DE %.

Chaque niveau est différent en termes de fonctionnalités et de prix. Pour plus d’informations sur la tarification, voir les [Détails de la tarification du Cache][].

Ce guide vous explique comment utiliser le client de [StackExchange.Redis][] à l’aide de C\# code. Les scénarios présentés incluent la **Création et la configuration d’un cache**, **Configuration des clients de cache**et **Ajout et suppression d’objets à partir du cache**. Pour plus d’informations sur l’utilisation du Cache Redis d’Azure, reportez-vous à la section [Étapes suivantes][] . Pour obtenir un didacticiel pas à pas de création d’application web avec Cache de Redis un MVC ASP.NET, reportez-vous à la section [comment créer une application Web avec Redis un Cache](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>
## <a name="get-started-with-azure-redis-cache"></a>Mise en route avec Azure Redis de Cache

Il est facile de mise en route avec Cache Redis d’Azure. Pour commencer, vous approvisionner et de configurez un cache. Ensuite, vous configurez les clients de cache afin qu’ils peuvent accéder au cache. Une fois que les clients de cache sont configurés, vous pouvez commencer à utiliser avec eux.

-   [Créer le cache][]
-   [Configurer les clients de cache][]

<a name="create-cache"></a>
## <a name="create-a-cache"></a>Créer un cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>Pour accéder à votre cache après qu’il est créé.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Pour plus d’informations sur la configuration de votre cache, consultez [comment configurer le Cache de Redis Azure](cache-configure.md).

<a name="NuGet"></a>
## <a name="configure-the-cache-clients"></a>Configurer les clients de cache

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Une fois votre projet client est configuré pour la mise en cache, vous pouvez utiliser les techniques décrites dans les sections suivantes pour travailler avec votre cache.

<a name="working-with-caches"></a>
## <a name="working-with-caches"></a>Utilisation des Caches

Les étapes de cette section décrivent comment effectuer des tâches courantes avec Cache.

-   [Se connecter au cache][]
-   [Ajouter et récupérer des objets à partir du cache][]
-   [Travailler avec des objets .NET dans le cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## <a name="connect-to-the-cache"></a>Se connecter au cache

Pour utiliser un cache par programme, vous avez besoin d’une référence dans le cache. Ajoutez le code suivant en haut de n’importe quel fichier à partir duquel vous voulez le client StackExchange.Redis permet d’accéder à un Cache de Redis d’Azure.

    using StackExchange.Redis;

>[AZURE.NOTE] Le client StackExchange.Redis requiert.NET Framework 4 ou version ultérieure.

La connexion au Cache Redis Azure est gérée par le `ConnectionMultiplexer` classe. Cette classe est conçue pour être partagés et réutilisés dans l’ensemble de votre application cliente et ne doit pas être créé sur une base par opération. 

Se connecter à un Cache de Redis Azure et de retourner une instance de connecté `ConnectionMultiplexer`, appelez la méthode statique `Connect` méthode et passer le point de terminaison du cache et la clé comme l’exemple suivant. Utilisez la clé générée à partir du portail Azure comme paramètre de mot de passe.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Avertissement : Ne jamais banque d’informations d’identification dans le code source. Pour simplifier cet exemple, je suis leur affichage dans le code source. Pour plus d’informations sur le stockage des informations d’identification, voir [comment les chaînes d’Application et de travail des chaînes de connexion][] .

Si vous ne souhaitez pas utiliser SSL, soit définir `ssl=false` ou omettre le `ssl` paramètre.

>[AZURE.NOTE] Le port non-SSL est désactivé par défaut pour les nouveaux caches. Pour obtenir des instructions sur l’activation du port non-SSL, consultez les [Ports d’accès](cache-configure.md#access-ports)...

Une approche de partage un `ConnectionMultiplexer` instance dans votre application doit avoir une propriété statique qui retourne une instance connectée, similaire à l’exemple suivant. Cela fournit une façon thread-safe pour initialiser un seul connecté `ConnectionMultiplexer` instance. Dans ces exemples `abortConnect` est définie sur false, ce qui signifie que l’appel réussira même si une connexion au Cache Azure Redis n’est pas établie. Une fonctionnalité clé de `ConnectionMultiplexer` est qu’elle restaure automatiquement une connectivité au cache une fois le problème de réseau ou d’autres causes sont résolus.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

Pour plus d’informations sur les options de configuration de connexion avancées, voir [modèle de configuration de StackExchange.Redis][].

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Une fois la connexion établie, renvoyer une référence à la base de données du cache redis en appelant le `ConnectionMultiplexer.GetDatabase` méthode. L’objet retourné à partir de la `GetDatabase` méthode est un objet de léger direct et qu’il n’a pas besoin d’être stockées.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Maintenant que vous savez comment vous connecter à une instance de Cache Redis d’Azure et renvoyer une référence à la base de données de cache, jetons un œil à l’utilisation de la mémoire cache.

<a name="add-object"></a>
## <a name="add-and-retrieve-objects-from-the-cache"></a>Ajouter et récupérer des objets à partir du cache

Les éléments peuvent être stockés dans et extraite d’un cache à l’aide de la `StringSet` et `StringGet` méthodes.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis stocke la plupart des données en tant que chaînes de Redis, mais ces chaînes peuvent contenir de nombreux types de données, y compris les données binaires sérialisées, ce qui peuvent être utilisées lorsque .NET de stocker des objets dans le cache.

Lors de l’appel `StringGet`, si l’objet existe, il est retourné, et si elle n’est pas le cas `null` est retourné. Dans ce cas, vous pouvez récupérer la valeur de la source de données et stocker dans le cache pour une utilisation ultérieure. Il s’agit du modèle de cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Pour spécifier la date d’expiration d’un élément dans le cache, utilisez la `TimeSpan` paramètre de `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>Travailler avec des objets .NET dans le cache

Cache de Redis Azure peut mettre en cache les objets .NET ainsi que les types de données primitifs, mais avant un objet .NET peut être mise en cache qu’il doit être sérialisé. Il est de la responsabilité du développeur de l’application et fournit la flexibilité du développeur dans le choix du sérialiseur.

Une méthode simple pour sérialiser des objets consiste à utiliser la `JsonConvert` dans [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) , les méthodes de sérialisation et sérialiser vers et à partir de JSON. L’exemple suivant montre un get et un ensemble à l’aide d’un `Employee` instance d’objet.


    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    
        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base, cliquez sur ces liens pour en savoir plus sur le Cache Redis d’Azure.

-   Découvrez les fournisseurs ASP.NET pour le Cache de Redis d’Azure.
    -   [Fournisseur d’état de Session Redis Azure](cache-aspnet-session-state-provider.md)
    -   [Azure Redis fournisseur de Cache de sortie d’ASP.NET du Cache](cache-aspnet-output-cache-provider.md)
-   [Activer les diagnostics de la mémoire cache](cache-how-to-monitor.md#enable-cache-diagnostics) afin de pouvoir le [Moniteur de](cache-how-to-monitor.md) l’état de santé de votre cache. Vous pouvez afficher les mesures dans le portail d’Azure, et vous pouvez également [télécharger et consulter](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) à l’aide d’outils de votre choix.
-   Consultez la [documentation du StackExchange.Redis du cache client][].
    -   Cache de Redis Azure sont accessibles à partir de nombreux clients de Redis et langages de développement. Pour plus d’informations, consultez [http://redis.io/clients][].
-   Azure Redis Cache peut également être utilisé avec les services tiers et des outils tels que Redsmin et Redis le Gestionnaire de bureau.
    -   Pour plus d’informations sur Redsmin, reportez-vous à la section [comment récupérer une chaîne de connexion Redis d’Azure et d’utiliser avec Redsmin][].
    -   Accès et vérifiez vos données dans le Cache de Redis Azure avec une interface utilisateur graphique à l’aide de [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
-   Consultez la documentation de [redis][] et en savoir plus sur les [redis des types de données][] et [une présentation de 15 minutes pour Redis des types de données][].



<!-- INTRA-TOPIC LINKS -->
[Étapes suivantes]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Créer le cache]: #create-cache
[Configure the cache]: #enable-caching
[Configurer les clients de cache]: #NuGet
[Working with Caches]: #working-with-caches
[Se connecter au cache]: #connect-to-cache
[Ajouter et récupérer des objets à partir du cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://redis.IO/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Comment récupérer une chaîne de connexion Redis d’Azure et l’utiliser avec Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modèle de configuration de StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Mettre en cache les détails de tarification]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Documentation de client de cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis des types de données]: http://redis.io/topics/data-types
[une introduction de 15 minutes pour les types de données Redis]: http://redis.io/topics/data-types-intro

[Fonctionnement des chaînes de l’Application et des chaînes de connexion]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


