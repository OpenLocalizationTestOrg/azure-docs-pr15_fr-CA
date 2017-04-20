<properties 
    pageTitle="Cache de migrer vers Redis | Microsoft Azure"
    description="Apprenez à migrer des applications de Service managé de Cache dans le Cache de Redis Azure"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/30/2016"
    ms.author="sdanie" />

# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Migrer à partir du Service de Cache géré vers Azure Redis Cache

Il est possible de migrer vos applications qui utilisent le Service de Cache de Managed Azure au Cache de Redis Azure avec peu de modifications à votre application, selon les fonctionnalités de gestion de Service de Cache utilisé par votre application de mise en cache. Alors que les API ne sont pas exactement les mêmes qu’ils sont similaires, et de votre code existant qui utilise un Service de Cache géré pour accéder à un cache peut être réutilisé avec peu de modifications. Cette rubrique montre comment effectuer la configuration nécessaire et les modifications d’application à migrer vos applications de gestion de Service de Cache d’utiliser le Cache de Redis d’Azure et montre comment certaines des fonctionnalités de Cache Redis d’Azure peuvent être utilisée pour implémenter les fonctionnalités d’un cache de Service de Cache géré.

## <a name="migration-steps"></a>Étapes de la migration

Les étapes suivantes sont nécessaires pour faire migrer une application de Service de Cache géré pour utiliser le Cache de Redis Azure.

-   Mapper les fonctionnalités de gestion de Service de Cache dans le Cache de Redis Azure
-   Choisissez une offre de Cache
-   Créer un Cache
-   Configurer les Clients de Cache
    -   Supprimer la Configuration de Service de Cache géré
    -   Configurer un client de cache à l’aide du NuGet Package StackExchange.Redis
-   Migration du code de gestion de Service de Cache
    -   Se connecter au cache à l’aide de la classe ConnectionMultiplexer
    -   Types de données primitifs d’accès dans le cache
    -   Travailler avec des objets .NET dans le cache
-   Migration de l’état de Session ASP.NET et la mise en cache de sortie pour le Cache de Redis Azure 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Mapper les fonctionnalités de gestion de Service de Cache dans le Cache de Redis Azure

Azure Service managé de Cache et de Cache Redis d’Azure sont similaires mais implémentent certaines de leurs fonctionnalités de différentes manières. Cette section décrit quelques-unes des différences et explique comment implémenter les fonctionnalités de gestion de Service de Cache dans le Cache de Redis Azure.

|Fonctionnalité de Service de Cache gérée|La prise en charge du Service de Cache gérée|Prise en charge de Azure Redis de Cache|
|---|---|---|
|Cache nommé|Un cache par défaut est configuré, et dans le cache Standard et Premium des offres, à neuf autres cache nommé peuvent être configurés si vous le souhaitez.|Les caches Azure Redis ont un nombre configurable de bases de données (16 par défaut) qui peut être utilisé pour implémenter une fonctionnalité semblable au cache nommé. Pour plus d’informations, reportez-vous à la section [configuration du serveur par défaut Redis](cache-configure.md#default-redis-server-configuration).|
|Haute disponibilité|Fournit une disponibilité élevée pour les éléments dans le cache dans les offres de cache Standard et Premium. Si les éléments sont perdus à la suite d’une défaillance, les copies de sauvegarde des éléments dans le cache sont toujours disponibles. Écrit dans la mémoire cache secondaire est effectuées de façon synchrone.|Haute disponibilité est disponible dans les offres de cache Standard et Premium, qui ont une configuration primaire/réplicas de deux nœuds (chaque partagé dans un cache de prime a une paire principal/réplica). Écrit dans le réplica est effectuées en mode asynchrone. Pour plus d’informations, consultez [tarification du Cache Redis d’Azure](https://azure.microsoft.com/pricing/details/cache/).|
|Notifications|Permet aux clients de recevoir des notifications asynchrones lors de diverses opérations de cache se produisent sur un cache nommé.|Les applications de client peuvent utiliser des Redis pub/sub ou [notifications de Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) pour obtenir une fonctionnalité similaire aux notifications.|
|Cache local|Stocke une copie des objets mis en cache localement sur le client pour accéder très rapidement.|Les applications client devra implémenter cette fonctionnalité à l’aide d’un dictionnaire ou une structure de données similaires.|
|Stratégie d’éviction|Aucun ou LRU. La stratégie par défaut est LRU.|Cache de Redis Azure prend en charge les stratégies d’expulsion suivantes : allkeys-lru, lru-volatile, volatile aléatoire, aléatoire allkeys-volatile-durée de vie, le noeviction. La stratégie par défaut est volatile-lru. Pour plus d’informations, reportez-vous à la section [configuration du serveur par défaut Redis](cache-configure.md#default-redis-server-configuration).|
|Stratégie d’expiration|La stratégie d’expiration par défaut est absolue, et l’intervalle d’expiration par défaut est de dix minutes. Stratégies coulissant et jamais sont également disponibles.|Éléments du cache n’expirent pas par défaut, mais un délai d’expiration peut être configuré sur une base par écriture à l’aide de surcharges de jeu de cache. Pour plus d’informations, consultez [Ajouter et récupérer des objets à partir du cache](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache).|
|Régions et marquage|Les régions sont des sous-groupes d’éléments mis en cache. Régions prennent également en charge l’annotation des éléments mis en cache avec les chaînes descriptifs supplémentaires appelés balises. Régions prennent en charge la possibilité d’effectuer des opérations de recherche sur des éléments balisés dans cette région. Tous les éléments dans une région se trouvent au sein d’un seul nœud du cluster de cache.|Un cache Redis se compose d’un seul nœud (à moins que le cluster de Redis est activé) pour le concept de zones de Service de Cache géré ne s’applique pas. Redis prend en charge la recherche et des opérations de caractère générique lors de la récupération des clés afin de balises descriptives peuvent être incorporées dans les noms de clé et permet de récupérer les éléments ultérieurement. Pour obtenir un exemple d’implémentation d’une solution de marquage à l’aide de Redis, consultez [l’implémentation de cache le balisage avec Redis](http://stackify.com/implementing-cache-tagging-redis/).|
|Sérialisation|Cache managé prend en charge NetDataContractSerializer, BinaryFormatter et l’utilisation de sérialiseurs personnalisés. La valeur par défaut est NetDataContractSerializer.|Il est de la responsabilité de l’application cliente pour sérialiser des objets .NET avant de les placer dans le cache, avec le choix du sérialiseur à un développeur de l’application client. Pour plus d’informations et d’exemples de code, voir [utilisation des objets .NET dans le cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).|
| Émulateur de cache | Cache géré fournit un émulateur de cache local. | Cache de Redis Azure ne dispose pas d’un émulateur, mais vous pouvez [exécuter la version de MSOpenTech de redis-server.exe localement](cache-faq.md#cache-emulator) pour fournir une expérience de l’émulateur. |

## <a name="choose-a-cache-offering"></a>Choisissez une offre de Cache

Cache de Redis Microsoft Azure est disponible dans les niveaux suivants :

-   **Base** – nœud unique. Plusieurs tailles jusqu'à 53 go.
-   **Standard** – principal/réplicas de deux nœuds. Plusieurs tailles jusqu'à 53 go. 99,9 SLA DE %.
-   **Prime** – deux nœuds principal/réplica avec jusqu'à 10 milieu des fragments. Plusieurs tailles de 6 Go à 530 Go (nous contacter pour en savoir plus). Toutes les fonctionnalités de couche Standard et prise en charge, y compris pour [Redis le cluster](cache-how-to-premium-clustering.md) [Redis la persistance](cache-how-to-premium-persistence.md)et [Réseau virtuel d’Azure](cache-how-to-premium-vnet.md). 99,9 SLA DE %.

Chaque niveau est différent en termes de fonctionnalités et de prix. Les fonctionnalités sont abordées plus loin dans ce guide et pour plus d’informations sur la tarification, voir les [Détails de la tarification du Cache](https://azure.microsoft.com/pricing/details/cache/).

Un point de départ de la migration consiste à choisir la taille qui correspond à la taille de votre cache de Service de Cache géré précédente, puis mettre à l’échelle vers le haut ou vers le bas en fonction des exigences de votre application. Pour plus de conseils sur le choix de l’offre de Cache Redis d’Azure droite, consultez [offre de Cache de Redis et de la taille dois-je utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Créer un Cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configurer les Clients de Cache

Une fois que le cache est créé et configuré, l’étape suivante consiste à supprimer la configuration du Service de Cache géré, ajouter l’ajout de la configuration de Cache Redis d’Azure et référence afin que les clients de cache peuvent accéder au cache.

-   Supprimer la Configuration de Service de Cache géré
-   Configurer un client de cache à l’aide du NuGet Package StackExchange.Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Supprimer la Configuration de Service de Cache géré

Avant que les applications clientes peuvent être configurées pour Azure Redis Cache, la configuration du Service de Cache géré existante et références d’assembly doivent être supprimées en désinstallant le package NuGet de Service de Cache géré.

Pour désinstaller le package NuGet de Service de Cache géré, droit sur le projet client dans **L’Explorateur de solutions** et sélectionnez **Manage NuGet Packages**. Sélectionnez le nœud **packages d’installé** et tapez W**indowsAzure.Caching** dans la zone de packages installés de recherche. Sélectionnez **Windows Azure** **Cache** (ou à **Windows Azure** **De mise en cache** en fonction de la version du package NuGet) et cliquez sur **désinstaller**, puis cliquez sur **Fermer**.

![Désinstaller le Package NuGet de Service Azure Cache géré](./media/cache-migrate-to-redis/IC757666.jpg)

Le package NuGet de Service de Cache géré en désinstallant les assemblys gérés de Service de Cache et les entrées de Service managé de Cache dans le fichier app.config ou web.config de l’application cliente. Étant donné que certains paramètres personnalisés ne peuvent pas être supprimés lors de la désinstallation du package NuGet, ouvrez web.config ou app.config et vous assurer que les éléments suivants sont complètement supprimés.

S’assurer que les `dataCacheClients` est supprimée de la `configSections` élément. Ne supprimez pas l’intégralité de la `configSections` élément ; Il vous suffit de supprimer le `dataCacheClients` entrée, si elle est présente.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
    </configSections>

Assurez-vous que le `dataCacheClients` section est supprimée. Le `dataCacheClients` section sera similaire à l’exemple suivant.

    <dataCacheClients>
      <dataCacheClientname="default">
        <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
        <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
        <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="true">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Une fois la configuration du Service de Cache géré est supprimée, vous pouvez configurer le client de cache comme décrit dans la section suivante.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configurer un client de cache à l’aide du NuGet Package StackExchange.Redis

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migration du code de gestion de Service de Cache

L’API pour le client de cache StackExchange.Redis est similaire au Service Cache géré. Cette section fournit une vue d’ensemble des différences.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Se connecter au cache à l’aide de la classe ConnectionMultiplexer

Dans la gestion du Service de Cache, connexions au cache ont été traitées par le `DataCacheFactory` et `DataCache` classes. Dans le Cache de Redis Azure, ces connexions sont gérées par la `ConnectionMultiplexer` classe.

Ajoutez le code suivant à l’aide de l’instruction au début de tous les fichiers à partir de laquelle vous souhaitez accéder au cache.

    using StackExchange.Redis
                                
Si cet espace de noms ne résout pas, assurez-vous que vous avez ajouté le package NuGet de StackExchange.Redis comme décrit dans [configurer les clients de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

>[AZURE.NOTE] Notez que le client StackExchange.Redis requiert.NET Framework 4 ou version ultérieure.

Pour vous connecter à une instance de Cache Redis d’Azure, appelez la méthode statique `ConnectionMultiplexer.Connect` méthode et passer le point de terminaison et la clé. Une approche de partage un `ConnectionMultiplexer` instance dans votre application doit avoir une propriété statique qui retourne une instance connectée, similaire à l’exemple suivant. Cela fournit une façon thread-safe pour initialiser un seul connecté `ConnectionMultiplexer` instance. Dans cet exemple `abortConnect` est définie sur false, ce qui signifie que l’appel réussira même si une connexion pour le cache n’est pas établie. Une fonctionnalité clé de `ConnectionMultiplexer` est qu’elle restaure automatiquement une connectivité au cache une fois le problème de réseau ou d’autres causes sont résolus.

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

Le point de terminaison de cache, les clés et les ports peuvent être obtenus de la lame de **Redis de Cache** pour votre instance de cache. Pour plus d’informations, consultez [Propriétés de Cache de Redis](cache-configure.md#properties).

Une fois la connexion établie, renvoyer une référence à la base de données du cache Redis en appelant le `ConnectionMultiplexer.GetDatabase` méthode. L’objet retourné à partir de la `GetDatabase` méthode est un objet de léger direct et qu’il n’a pas besoin d’être stockées.

    IDatabase cache = Connection.GetDatabase();
    
    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);
    
    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Le client StackExchange.Redis utilise le `RedisKey` et `RedisValue` les types d’accès et de stockage des éléments dans le cache. Ces types de correspondent des types de langage plus primitifs, y compris la chaîne et souvent ne sont pas utilisés directement. Redis chaînes sont le type de base de la valeur de Redis et peuvent contenir de nombreux types de données, y compris les flux de données binaires sérialisées, et alors que vous ne pouvez pas utiliser directement le type, vous utilisez les méthodes qui contiennent des `String` dans le nom. Pour les types de données primitifs plus vous stocker et récupérer des éléments du cache à l’aide de la `StringSet` et `StringGet` méthodes, sauf si vous stockez des collections ou autres types de données de Redis dans le cache. 

`StringSet`et `StringGet` sont très similaires au Service géré de Cache `Put` et `Get` méthodes, avec une importante différence étant que les avant de définir et d’obtenir un objet .NET dans le cache vous devez sérialiser au préalable. 

Lors de l’appel `StringGet`, si l’objet existe, il est retourné, et si elle n’est pas le cas, la valeur null est renvoyée. Dans ce cas, vous pouvez récupérer la valeur de la source de données et stocker dans le cache pour une utilisation ultérieure. Il s’agit du modèle de cache-aside.

Pour spécifier la date d’expiration d’un élément dans le cache, utilisez la `TimeSpan` paramètre de `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

Cache de Redis Azure peuvent travailler avec les objets .NET ainsi que les types de données primitifs, mais avant un objet .NET peut être mise en cache qu’il doit être sérialisé. C’est la responsabilité du développeur de l’application. Le développeur flexibilité dans le choix du sérialiseur. Pour plus d’informations et d’exemples de code, voir [utilisation des objets .NET dans le cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Migration de l’état de Session ASP.NET et la mise en cache de sortie pour le Cache de Redis Azure

Cache de Redis Azure a des fournisseurs pour l’état de Session ASP.NET et la sortie de Page mise en cache. Pour faire migrer votre application qui utilise les versions managées de Service de Cache de ces fournisseurs, tout d’abord supprimer les sections de votre fichier web.config et configurer les versions de Cache de Redis Azure des fournisseurs. Pour plus d’informations sur les fournisseurs ASP.NET de Cache Redis Azure, consultez [Fournisseur d’état de Session ASP.NET pour le Cache de Redis Azure](cache-aspnet-session-state-provider.md) et le [Fournisseur de Cache de sortie ASP.NET pour le Cache de Redis d’Azure](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Étapes suivantes

Pour des didacticiels, des exemples, des vidéos, etc., explorez la [documentation de Cache Redis d’Azure](https://azure.microsoft.com/documentation/services/cache/) .

