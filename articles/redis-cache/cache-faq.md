<properties 
    pageTitle="Cache d’Azure Redis FAQ | Microsoft Azure" 
    description="Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour le Cache de Redis Azure" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-faq"></a>Cache d’Azure Redis Forum aux questions

Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour le Cache de Redis d’Azure. 


## <a name="what-if-my-question-isnt-answered-here"></a>Que se passe-t-il si n’est pas répondu à ma question ici ?

Si votre question n’est pas répertoriée ici, faites-le nous savoir et nous vous aiderons à trouver une réponse.

-   Vous pouvez publier une question dans le [thread de Disqus](#comments) à la fin de cette FAQ et faites appel à l’équipe de Cache d’Azure et d’autres membres de la Communauté à propos de cet article.
-   Pour atteindre un public plus large, vous pouvez publier une question sur le [Forum MSDN de Cache Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) et s’engager avec l’équipe de Cache d’Azure et d’autres membres de la Communauté.
-   Si vous souhaitez faire une demande de fonctionnalité, vous pouvez soumettre vos demandes et des idées à [Azure Redis Cache utilisateur voix](https://feedback.azure.com/forums/169382-cache).
-   Vous pouvez également envoyer un e-mail à nous évaluations [externes](mailto:azurecache@microsoft.com)du Cache Azure.

## <a name="azure-redis-cache-basics"></a>Bases d’Azure Redis de Cache

Le Forum aux questions de cette section couvre quelques notions de base du Cache Redis d’Azure.

-    [Quel est le Cache de Redis Azure ?](#what-is-azure-redis-cache)
-    [Comment puis-je commencer avec Cache de Redis Azure ?](#how-can-i-get-started-with-azure-redis-cache)

Les questions fréquentes suivantes couvrent les concepts de base et des questions à propos de Cache Redis d’Azure et sont traités dans une des autres sections du Forum aux questions sur.

-   [Offre de Redis de Cache et de la taille dois-je utiliser ?](#what-redis-cache-offering-and-size-should-i-use)
-   [Les clients de cache Redis puis-je utiliser ?](#what-redis-cache-clients-can-i-use)
-   [Y a-t-il un émulateur local pour Cache de Redis Azure ?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Comment surveiller la santé et les performances de mon cache ?](#how-do-i-monitor-the-health-and-performance-of-my-cache)



## <a name="planning-faqs"></a>Questions fréquentes sur la planification

-   [Offre de Redis de Cache et de la taille dois-je utiliser ?](#what-redis-cache-offering-and-size-should-i-use)
-   [Performances de Redis de Cache Azure](#azure-redis-cache-performance)
-   [Dans quelle région dois-je rechercher mon cache ?](#in-what-region-should-i-locate-my-cache)
-   [Mode de facturation pour le Cache de Redis Azure ?](#how-am-i-billed-for-azure-redis-cache)
-   [Puis-je utiliser le Cache de Redis Azure avec Azure gouvernement nuage ou de nuage de Chine Azure ?](#can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud)



## <a name="development-faqs"></a>Forum aux questions de développement

-   [En quoi consistent les options de configuration de StackExchange.Redis ?](#what-do-the-stackexchangeredis-configuration-options-do)
-   [Les clients de cache Redis puis-je utiliser ?](#what-redis-cache-clients-can-i-use)
-   [Y a-t-il un émulateur local pour Cache de Redis Azure ?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Comment puis-je exécuter des commandes de Redis ?](#how-can-i-run-redis-commands)
-   [Pourquoi le Cache de Redis Azure n’a pas une référence de bibliothèque de classes MSDN comme les autres services Azure ?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
-   [Puis-je utiliser le Cache de Redis Azure comme un cache de session PHP ?](#can-i-use-azure-redis-cache-as-a-php-session-cache)


## <a name="security-faqs"></a>FAQ sur la sécurité

-   [Quand dois-je pour activer le port non-SSL pour se connecter à Redis ?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)


## <a name="production-faqs"></a>FAQ de la production

-   [Quelles sont certaines meilleures pratiques de production ?](#what-are-some-production-best-practices)
-   [Quelles sont les considérations lors de l’utilisation des commandes courantes de Redis ?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
-   [Comment puis-je évaluer et tester les performances de mon cache ?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Détails importants sur la croissance de pool de threads](#important-details-about-threadpool-growth)
-   [Activer le catalogue global du serveur obtenir plus de débit sur le client lors de l’utilisation de StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)


## <a name="monitoring-and-troubleshooting-faqs"></a>Surveillance et dépannage de FAQ

Le Forum aux questions de cette section couvre courantes de surveillance et de réponses aux questions posées. Pour plus d’informations sur la surveillance et le dépannage de vos instances de Cache Redis d’Azure, voir [Comment faire pour surveiller le Cache Redis d’Azure](cache-how-to-monitor.md) et [comment résoudre les problèmes de Cache de Redis d’Azure](cache-how-to-troubleshoot.md).

-   [Comment surveiller la santé et les performances de mon cache ?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-   [Cache de diagnostic stockage paramètres de mon compte modifiés, pourquoi ?](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-   [Pourquoi les tests de diagnostic est activé pour certains nouveaux caches mais pas d’autres ?](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-   [Pourquoi affiche-t-il des délais d’attente ?](#why-am-i-seeing-timeouts)
-   [Pourquoi mon client a été déconnectée du cache ?](#why-was-my-client-disconnected-from-the-cache)


## <a name="prior-cache-offering-faqs"></a>Forum aux questions de l’offre de Cache préalable

-   [L’offre de Cache d’Azure est pour moi ?](#which-azure-cache-offering-is-right-for-me)


### <a name="what-is-azure-redis-cache"></a>Quel est le Cache de Redis Azure ?

Cache de Redis Azure est basée sur la populaire open source [Redis du cache](http://redis.io). Il vous permet d’accéder à un cache de Redis dédié, sécurisé, géré par Microsoft et accessible à partir de n’importe quelle application dans Azure. Pour une présentation plus détaillée, voir la page de [Cache de Azure Redis](https://azure.microsoft.com/services/cache/) sur Azure.com.


### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Comment puis-je commencer avec Cache de Redis Azure ?

Il existe plusieurs façons que vous pouvez commencer avec Cache Redis d’Azure.

-    Vous pouvez extraire un de nos didacticiels disponibles pour [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)et [Python](cache-python-get-started.md). 
-    Vous pouvez regarder [comment créer haute Performance à l’aide de Microsoft Azure Redis Cache Apps](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
-    Vous pouvez consulter la documentation du client pour les clients qui correspondent à la langue du développement de votre projet pour voir comment utiliser Redis. Il existe de nombreux clients de Redis qui peuvent être utilisés avec Cache Redis d’Azure. Pour obtenir une liste de clients de Redis, consultez [http://redis.io/clients](http://redis.io/clients).


Si vous ne disposez pas d’un compte Azure, vous pouvez :

-    [Ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Vous obtenez des crédits qui peuvent être utilisés pour tester des services Azure payés. Même après que les crédits sont épuisés, vous pouvez conserver le compte et utiliser les fonctionnalités et services Azure gratuits.
-    [Avantages d’abonné activation de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Votre abonnement MSDN vous donne des crédits que vous pouvez utiliser pour des services Azure payés chaque mois.


<a name="cache-size"></a>
### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Offre de Redis de Cache et de la taille dois-je utiliser ?
Chaque Cache de Redis Azure offre différents niveaux de la **taille**, **la bande passante**, **haute disponibilité**et des options de **contrat SLA** .

Voici les considérations sur la sélection d’une offre de Cache.

-   **Mémoire**: 250 Mo – 53 Go offrent des niveaux Standard et la base. Le niveau de prime offre jusqu'à 530 Go avec plus disponible [sur demande](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Pour plus d’informations, consultez [Tarification d’Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/).
-   **Les performances du réseau**: Si vous disposez d’une charge de travail qui nécessite un débit élevé le niveau de prime offre davantage de bande passante par rapport à Standard ou Basic. Au sein de chaque niveau de caches de plus grandes taille ont également davantage de bande passante en raison de la machine virtuelle sous-jacent qui héberge le cache. Consultez le [tableau suivant](#cache-performance) pour plus d’informations.
-   **Débit**: niveau de prime de l’offre le débit disponible maximal. Si le client ou le serveur de cache atteint les limites de la bande passante, vous recevrez des délais d’attente du côté client. Pour plus d’informations, consultez le tableau suivant.
-   **Haute disponibilité/SLA**: Cache de Redis Azure garantit qu’un cache Standard/Premium seront disponible au moins 99,9 % du temps. Pour en savoir plus sur notre SLA, consultez [Tarification d’Azure Redis Cache](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Le contrat SLA traite uniquement de la connectivité aux points de terminaison du Cache. Le contrat SLA ne couvre pas la protection contre la perte de données. Nous recommandons d’utiliser la fonctionnalité de persistance des données de Redis dans le niveau de prime pour accroître la résilience contre la perte de données.
-   **Persistance des données redis**: couche de la prime vous permet de faire persister les données du cache dans un compte de stockage Azure. Dans un cache Basique/Standard toutes les données sont stockées dans la mémoire. En cas d’infrastructure sous-jacente problèmes peuvent être des pertes potentielles de données. Nous recommandons d’utiliser la fonctionnalité de persistance des données de Redis dans le niveau de prime pour accroître la résilience contre la perte de données. Cache de Redis Azure offre RDB et UNEDE (prochainement) options de persistance de Redis. Pour plus d’informations, consultez [comment configurer la persistance d’un Cache de Redis prime Azure](cache-how-to-premium-persistence.md).
-   **Redis le Cluster**: pour créer des caches de plus de 53 Go ou aux données de partagé sur plusieurs nœuds de Redis, vous pouvez utiliser Redis de clustering, qui est disponible dans le niveau de prime. Chaque nœud se compose d’une paire de cache principal/réplica pour une haute disponibilité. Pour plus d’informations, consultez [comment configurer la mise en cluster pour un Cache de Redis prime Azure](cache-how-to-premium-clustering.md).
-   **Sécurité accrue et l’isolation du réseau**: déploiement d’Azure Virtual Network (VNET) fournit une sécurité renforcée et l’isolement pour votre Cache Redis d’Azure, ainsi que des sous-réseaux, des stratégies de contrôle d’accès, et d’autres fonctionnalités pour davantage limitent l’accès. Pour plus d’informations, consultez [comment configurer la prise en charge de réseau virtuel pour un Cache de Redis prime Azure](cache-how-to-premium-vnet.md).
-   **Configurer Redis**: dans les couches Standard et Premium, vous pouvez configurer les Redis pour les notifications de Keyspace.
-   **Nombre maximal de connexions client**: niveau de prime de l’offre le nombre maximal de clients pouvant se connecter à Redis, avec un nombre élevé de connexions pour les caches de tailles plus importantes. [Consultez la page tarification pour plus d’informations](https://azure.microsoft.com/pricing/details/cache/).
-   **Noyau dédié pour le serveur de Redis**: le niveau de prime toutes les tailles de cache ont un noyau dédié pour Redis. Dans la norme de Basic/niveaux la C1 taille et ci-dessus ont un noyau dédié pour le serveur de Redis.
-   **Redis est monothread** et posséder plus de deux cœurs ne fournit pas d’avantages supplémentaires par rapport aux deux cœurs, mais de plus grande taille VM ont généralement plus de bande passante que les plus petits. Si le client ou le serveur de cache atteint les limites de la bande passante, vous recevrez des délais d’attente du côté client.
-   **Améliorations des performances**: Caches dans le niveau de prime sont déployés sur le matériel qui possède des processeurs plus rapides et donne de meilleures performances par rapport à la couche de base ou Standard. Les Caches de niveau Premium ont un débit plus élevé et faible latence.

<a name="cache-performance"></a>
### <a name="azure-redis-cache-performance"></a>Performances de Redis de Cache Azure

Le tableau suivant indique les valeurs de bande passante maximale observées lors des tests de différentes tailles de la norme et met en cache les primes à l’aide de `redis-benchmark.exe` à partir d’une VM Iaas contre le point de terminaison du Cache Redis d’Azure. Notez que ces valeurs ne sont pas garantis et qu’il n’y a pas de SLA pour ces numéros, mais doit être typique. Vous devez charger votre propre application pour déterminer la taille de cache de droite pour votre application de test.

À partir de ce tableau, nous pouvons dessiner les conclusions suivantes.

-   Le débit pour les caches qui sont de la même taille est plus élevé dans le niveau de prime par rapport à la couche Standard. Par exemple, avec une mémoire Cache de 6 Go, débit de P1 est de 140K RPS par rapport aux 49 K pour C3.
-   Avec Redis de clustering, le débit augmente de façon linéaire en augmentant le nombre de milieu des fragments (nœuds) dans le cluster. Par exemple, si vous créez un cluster P4 de 10 milieu des fragments, puis le débit disponible est de 250 Ko * 10 = 2,5 millions de RPS.
-   Le débit pour les tailles de clé plus importants est la plus élevé dans le niveau de prime par rapport à la couche Standard.

| Niveau de tarification             | Taille   | Coeurs de processeur | Bande passante disponible                                    | Taille de la clé 1 Ko                            |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **Tailles de cache standard** |        |           | **Mégabits par seconde (Mo/s) / mégaoctets par seconde (Mo/s)** | **Nombre de requêtes par seconde (RPS)**            |
| C0                       | 250 MO | Partagé    | 5 / 0.625                                              | 600                                      |
| C1                       | 1 GO   | 1         | 100 / 12,5                                             | 12200                                    |
| C2                       | 2,5 GO | 2         | 200 / 25                                               | 24000                                    |
| C3                       | 6 GO   | 4         | 400 / 50                                               | 49000                                    |
| C4                       | 13 GO  | 2         | 500 / 62,5                                             | 61000                                    |
| C5                       | 26 GO  | 4         | 1000 / 125                                             | 115000                                   |
| C6                       | 53 GO  | 8         | 2000 / 250                                             | 150000                                   |
| **Tailles de cache Premium**  |        | **Coeurs de processeur par partagé**  |                                         | **Nombre de requêtes par seconde (RPS), par partagé** |
| P1                       | 6 GO   | 2         | 1000 / 125                                             | 140000                                   |
| P2                       | 13 GO  | 4         | 2000 / 250                                             | 220000                                   |
| P3                       | 26 GO  | 4         | 2000 / 250                                             | 220000                                   |
| P4                       | 53 GO  | 8         | 4000 / 500                                             | 250000                                   |


Pour obtenir des instructions sur le téléchargement de tels que les outils de Redis `redis-benchmark.exe`, consultez la [Comment puis-je exécuter des commandes de Redis ?](#cache-commands) section.

<a name="cache-region"></a>
### <a name="in-what-region-should-i-locate-my-cache"></a>Dans quelle région dois-je rechercher mon cache ?

Pour obtenir les meilleures performances et une latence plus faible, recherchez votre Cache Redis d’Azure dans la même région que votre application de client de cache.

<a name="cache-billing"></a>
### <a name="how-am-i-billed-for-azure-redis-cache"></a>Mode de facturation pour le Cache de Redis Azure ?

Le prix Redis de Cache Azure est [ici](https://azure.microsoft.com/pricing/details/cache/). La page tarification répertorie la tarification à un taux horaire. Les caches sont facturés sur une base par minute de l’heure à laquelle le cache est créé, jusqu'à ce que l’heure à laquelle un cache est supprimé. Il n’existe aucune option permettant d’arrêter ou d’interrompre la facturation d’un cache.


## <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud"></a>Puis-je utiliser le Cache de Redis Azure avec Azure gouvernement nuage ou de nuage de Chine Azure ?

Oui, le Cache de Redis Azure est disponible dans Azure gouvernement Cloud et nuage de Chine Azure. Notez que les URL pour l’accès et la gestion du Cache de Redis Azure sont différents dans le nuage de gouvernement Azure et le nuage de Chine Azure comparé les nuage Public Azure. Pour plus d’informations sur les considérations lors de l’utilisation du Cache Redis d’Azure avec nuage de gouvernement d’Azure et d’Azure Chine Cloud, voir [Bases de données de gouvernement Azure - Cache Redis d’Azure](../azure-government/documentation-government-services-database.md#azure-redis-cache) et [d’Azure Cloud de Chine - Cache Redis d’Azure](https://www.azure.cn/documentation/services/redis-cache/).

Pour plus d’informations sur l’utilisation du Cache de Redis Azure avec PowerShell dans le Cloud de gouvernement Azure et le nuage de Chine Azure, voir [comment se connecter au nuage du gouvernement Azure ou de nuage de Chine Azure](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


<a name="cache-configuration"></a>
### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>En quoi consistent les options de configuration de StackExchange.Redis ?

StackExchange.Redis possède de nombreuses options. Cette section présente une partie des paramètres courants. Pour obtenir des informations plus détaillées sur les options de StackExchange.Redis, consultez [configuration de StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Description|Recommandation
---|---|---
AbortOnConnectFail|Lorsque la valeur true, la connexion ne se reconnecte pas après une panne de réseau.|La valeur false et StackExchange.Redis vous permettent de vous reconnecter automatiquement.
ConnectRetry|Le nombre de répétitions de tentatives de connexion pendant initiale se connecter.| Consultez les notes ci-dessous pour obtenir des instructions. |
ConnectTimeout|Délai d’attente en ms pour les opérations de connexion.| Consultez les notes ci-dessous pour obtenir des instructions. |

Dans la plupart des cas, les valeurs par défaut du client sont suffisantes. Vous pouvez régler les options en fonction de votre charge de travail.

-   **Nouvelles tentatives**
    -   Pour le ConnectRetry et ConnectTimeout, le Conseil général est Échec rapide puis réessayez. Cela dépend de votre charge de travail et combien de temps de moyenne qu’il prend pour votre client à émettre une commande Redis et recevoir une réponse.
    -   Laisser StackExchange.Redis vous reconnecter automatiquement au lieu de la vérification de l’état de la connexion et vous reconnecter à vous-même. **Évitez d’utiliser la propriété ConnectionMultiplexer.IsConnected**.
    -   Snowballing - parfois vous risquez de rencontrer un problème dans lequel vous sont une nouvelle tentative et cela snowballs et que vous ne le fait jamais. Dans ce cas, vous devez envisager en utilisant un algorithme de réessayer d’exponentielle intervalle comme décrit dans [Réessayer les lignes directrices](best-practices-retry-general.md) publiées par le groupe Microsoft Patterns & Practices.
-   **Valeurs de délai d’attente**
    -   Pensez à votre charge de travail et définir les valeurs en conséquence. Si vous stockez des valeurs élevées, définir le délai d’expiration une valeur plus élevée.
    -   La valeur `AbortOnConnectFail` StackExchange.Redis false et permettent une nouvelle pour vous.
    -   Utiliser une seule instance de ConnectionMultiplexer pour l’application. Vous pouvez utiliser une LazyConnection pour créer une instance unique qui est retournée par une propriété de connexion, comme indiqué dans [se connecter au cache à l’aide de la classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
    -   Définir le `ConnectionMultiplexer.ClientName` propriété app Nom d’instance unique pour des fins de diagnostic.
    -   Utilisez plusieurs `ConnectionMultiplexer` instances pour les charges de travail personnalisées.
    -   Vous pouvez suivre ce modèle si vous disposez de variation de charge dans votre application. Par exemple :
    -   Vous pouvez avoir un multiplexeur pour gérer les clés de grande taille.
    -   Vous pouvez avoir un multiplexeur pour traiter les petites clés.
    -   Vous pouvez définir différentes valeurs pour les délais de connexion et recommencez la logique pour chaque ConnectionMultiplexer que vous utilisez.
    -   Définir le `ClientName` propriété sur chaque multiplexeur à l’aide des tests de diagnostic.
    -   Par conséquent plus rationalisée de latence par `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Les clients de cache Redis puis-je utiliser ?

Un des grands avantages de Redis est qu’il n’y a de nombreux clients, prise en charge de nombreux langages de développement différents. Pour connaître la liste des clients, reportez-vous à la section [Redis de clients](http://redis.io/clients). Pour des didacticiels qui couvrent plusieurs langues et clients, voir [comment utiliser le Cache de Redis d’Azure](cache-dotnet-how-to-use-azure-redis-cache.md) et cliquez sur la langue souhaitée à partir du sélecteur de langue dans la partie supérieure de l’article.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Y a-t-il un émulateur local pour Cache de Redis Azure ?

Il n’existe aucun émulateur local pour Cache de Redis d’Azure, mais vous pouvez exécuter la version MSOpenTech de redis-server.exe à partir de la [Redis des outils de ligne de commande](https://github.com/MSOpenTech/redis/releases/) sur votre ordinateur local et s’y connecter afin d’obtenir une expérience similaire à un émulateur de cache local, comme illustré dans l’exemple suivant.


    private static Lazy<ConnectionMultiplexer>
        lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Vous pouvez éventuellement configurer un fichier [redis.conf](http://redis.io/topics/config) pour mieux correspondent aux [paramètres de cache par défaut](cache-configure.md#default-redis-server-configuration) pour votre Cache de Redis Azure en ligne si vous le souhaitez.

<a name="cache-commands"></a>
### <a name="how-can-i-run-redis-commands"></a>Comment puis-je exécuter des commandes de Redis ?

Vous pouvez utiliser les commandes répertoriées à la [Redis des commandes](http://redis.io/commands#) , à l’exception des commandes répertoriées à la [Redis commandes non prises en charge dans le Cache de Redis Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Vous disposez de plusieurs options pour exécuter des commandes de Redis.

-   Si vous avez un cache Standard ou Premium, vous pouvez exécuter les commandes de Redis à l’aide de la [Console de Redis](cache-configure.md#redis-console). Cela offre un moyen sécurisé d’exécuter des commandes de Redis dans le portail Azure.
-   Vous pouvez également utiliser les outils de ligne de commande Redis. Pour les utiliser, effectuez les opérations suivantes.
-   Téléchargez les [Outils de ligne de commande de Redis](https://github.com/MSOpenTech/redis/releases/).
-   Se connecter au cache à l’aide de `redis-cli.exe`. Passez le point de terminaison du cache à l’aide de que basculer le -h et la clé à l’aide de - un comme indiqué dans l’exemple suivant.
-   `redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  - Notez que les outils de ligne de commande Redis ne fonctionnent pas avec le port SSL, mais vous pouvez utiliser un utilitaire tel que `stunnel` connecter en toute sécurité les outils pour le port SSL en suivant les instructions dans le billet de blog [Annonce ASP.NET Session State Provider pour Redis version d’évaluation](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

<a name="cache-reference"></a>
### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Pourquoi le Cache de Redis Azure n’a pas une référence de bibliothèque de classes MSDN comme les autres services Azure ?

Cache de Redis Microsoft Azure est basée sur la populaire open source Redis le Cache et sont accessibles par un large éventail de [Redis des clients](http://redis.io/clients) qui sont disponibles pour nombreux langages de programmation. Chaque client a sa propre API qui effectue des appels à l’instance de cache Redis à l’aide de [commandes de Redis](http://redis.io/commands).

Dans la mesure où chaque client est différent, il n’existe pas une référence de classe centralisée sur MSDN ; au lieu de cela, chaque client conserve sa propre documentation de référence. En plus de la documentation de référence, il existe plusieurs didacticiels montrant comment commencer avec Azure Redis de Cache à l’aide de différentes langues et clients du cache. Pour accéder à ces didacticiels, voir [comment utiliser le Cache de Redis d’Azure](cache-dotnet-how-to-use-azure-redis-cache.md) , puis cliquez sur la langue souhaitée à partir du sélecteur de langue dans la partie supérieure de l’article.


### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Puis-je utiliser le Cache de Redis Azure comme un cache de session PHP ?

Oui, pour utiliser le Cache de Redis Azure comme un cache de session PHP, spécifier la chaîne de connexion à votre instance de Cache Redis d’Azure dans `session.save_path`.

>[AZURE.IMPORTANT] Lors de l’utilisation du Cache de Redis Azure comme un cache de session PHP, vous devez les URL coder la clé de sécurité utilisée pour se connecter au cache, comme illustré dans l’exemple suivant.
>
>`session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
>Si la clé n’est pas codé en URL, une exception semblable au suivant peut s’afficher :`Failed to parse session.save_path`

Pour plus d’informations sur l’utilisation d’un cache de session PHP avec le client PhpRedis Redis le Cache, consultez le [Gestionnaire de Session de PHP](https://github.com/phpredis/phpredis#php-session-handler).



<a name="cache-ssl"></a>
### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Quand dois-je pour activer le port non-SSL pour se connecter à Redis ?

Redis serveur ne supporte pas SSL prêts à l’emploi, contrairement à Cache Redis d’Azure. Si vous vous connectez à un Cache de Redis d’Azure et que votre client prend en charge SSL, comme StackExchange.Redis, vous devez utiliser SSL.

Notez que le port non-SSL est désactivé par défaut pour les nouvelles instances de Cache Redis d’Azure. Si votre client ne prend pas en charge SSL, vous devez activer le port non-SSL en suivant les instructions de la section [ports d’accès](cache-configure.md#access-ports) de l’article [configurer un cache dans le Cache de Redis Azure](cache-configure.md) .

Redis des outils tels que `redis-cli` ne fonctionnent pas avec le port SSL, mais vous pouvez utiliser un utilitaire tel que `stunnel` connecter en toute sécurité les outils pour le port SSL en suivant les instructions dans le billet de blog [Annonce ASP.NET Session State Provider pour Redis version d’évaluation](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

Pour obtenir des instructions sur le téléchargement des outils Redis, consultez la [Comment puis-je exécuter des commandes de Redis ?](#cache-commands) section.



### <a name="what-are-some-production-best-practices"></a>Quelles sont certaines meilleures pratiques de production ?

-   [Méthodes conseillées pour la StackExchange.Redis](#stackexchangeredis-best-practices)
-   [Concepts de configuration et d’utilisation](#configuration-and-concepts)
-   [Les tests de performances](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Méthodes conseillées pour la StackExchange.Redis

-   La valeur `AbortConnect` sur false, puis laissez le ConnectionMultiplexer vous reconnecter automatiquement. [Pour plus d’informations, voir ici](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
-   Réutiliser le ConnectionMultiplexer - ne pas créer un nouveau pour chaque demande. Le `Lazy<ConnectionMultiplexer>` modèle [présenté ici](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) est fortement recommandé.
-   Redis de fonctionne mieux avec des valeurs plus petites, donc pensez à dégainer les données plus grandes en plusieurs clés. Dans [cette discussion de Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 Ko est considéré comme « grande taille ». Lisez [cet article](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) pour un problème d’exemple qui peut être provoqué par des valeurs élevées.
-   Configurer vos [paramètres de pool de threads](#important-details-about-threadpool-growth) pour éviter les délais d’attente.
-   Utilisez au moins la connectTimeout par défaut de 5 secondes. Ceci lui StackExchange.Redis un délai suffisant pour rétablir la connexion, dans le cas d’un spots de réseau.
-   N’oubliez pas des coûts de performances associés à différentes opérations que vous exécutez. Par exemple, le `KEYS` commande est une opération o (n) et doivent être évitée. Le [site de redis.io](http://redis.io/commands/) a des détails autour de la complexité du temps pour chaque opération pris en charge. Cliquez sur chaque commande afin de voir la complexité de chaque opération.

#### <a name="configuration-and-concepts"></a>Concepts de configuration et d’utilisation

-   Utilisez le Standard ou le niveau de prime pour les systèmes de Production. La couche de base est un système à noeud unique avec aucune réplication des données et pas de SLA. En outre, utilisez au moins un cache de C1. C0 caches sont vraiment destinées à des scénarios de développement/test simple.
-   N’oubliez pas que Redis est un magasin de données **En mémoire** . Lisez [cet article](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) afin que vous ayez connaissance des scénarios où la perte de données peut se produire.
-   Développer votre système tel qu’il peut traiter [en raison d’une application de correctifs et de basculement](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)l’option connexion les blips.

#### <a name="performance-testing"></a>Les tests de performances

-   Démarrer à l’aide de `redis-benchmark.exe` pour avoir une idée de débit possible avant d’écrire votre propre performance tests. Notez que redis-test d’évaluation ne prend pas charge SSL, vous allez ont pour [Activer le port Non-SSL via le portail Azure](cache-configure.md#access-ports) avant d’exécuter le test. Pour obtenir des exemples, consultez [Comment puis-je évaluer et tester les performances de mon cache ?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   Le client utilisé pour le test de machine virtuelle doit être dans la même région que votre instance de cache Redis.
-   Nous recommandons d’utiliser des séries de machine virtuelle Dv2 pour votre client qu’ils ont de meilleur matériel et vous donne les meilleurs résultats.
-   Assurez-vous que votre client VM, vous choisissez a au moins autant de calcul et de bande passante comme cache que vous testez. 
-   Activer VRSS sur la machine client, si vous êtes sous Windows. [Pour plus d’informations, voir ici](https://technet.microsoft.com/library/dn383582.aspx).
-   Instances de Redis de niveau Premium seront ont mieux réseau latence et le débit car ils sont en cours d’exécution meilleur matériel pour processeur et réseau.

<a name="cache-redis-commands"></a>
### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quelles sont les considérations lors de l’utilisation des commandes courantes de Redis ?

-   Vous ne devez pas exécuter certaines commandes Redis qui prennent un certain temps sans comprendre l’impact de ces commandes.
    -   Par exemple, n’exécutez pas la commande de [clés](http://redis.io/commands/keys) dans la production qu’il peut prendre beaucoup de temps à retourner en fonction du nombre de clés. Redis est un serveur à thread unique, et il traite les commandes une à la fois. Si vous avez d’autres commandes émises après les clés, elles aura pas lieu tant que Redis traite la commande de clés. Le [site de redis.io](http://redis.io/commands/) a des détails autour de la complexité du temps pour chaque opération pris en charge. Cliquez sur chaque commande afin de voir la complexité de chaque opération.
-   Les tailles de clé - dois-je utiliser clé/valeurs de petites ou grandes clé/valeurs ? En général, cela dépend du scénario. Si votre scénario exige des clés plus longues puis vous pouvez ajuster le ConnectionTimeout et tentatives et ajuster votre logique des nouvelles tentatives. Du point de vue Redis server, avec des valeurs inférieures sont observés pour de meilleures performances.
-   Cela ne signifie pas que vous ne peut pas stocker des valeurs plus élevées dans Redis ; Il se peut que vous devez tenir compte des considérations suivantes. Latences sera plus élevés. Si vous avez un ensemble de données qui sont plus grandes et qui est plus petite, vous pouvez utiliser plusieurs instances de ConnectionMultiplexer, chacun configuré avec un autre jeu de valeurs de délai d’expiration et réessayer, comme décrit dans la section précédente [que faire les options de configuration StackExchange.Redis](#cache-configuration) .



<a name="cache-benchmarking"></a>
### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Comment puis-je évaluer et tester les performances de mon cache ?

-   [Activer les diagnostics de la mémoire cache](cache-how-to-monitor.md#enable-cache-diagnostics) afin de pouvoir le [Moniteur de](cache-how-to-monitor.md) l’état de santé de votre cache. Vous pouvez afficher les mesures dans le portail Azure, et vous pouvez également [télécharger et consulter](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) à l’aide d’outils de votre choix.
-   Vous pouvez utiliser redis-benchmark.exe pour le test de charge de votre serveur Redis.
-   Assurez-vous que la charge client et le cache de la Redis de test sont dans la même région.
-   Utiliser les redis-cli.exe et surveiller le cache à l’aide de la commande INFO.
-   Si la charge est à l’origine de la fragmentation de mémoire haute puis vous devez évoluer vers un cache de taille supérieure.
-   Pour obtenir des instructions sur le téléchargement des outils Redis, consultez la [Comment puis-je exécuter des commandes de Redis ?](#cache-commands) section.

Voici un exemple d’utilisation de redis-benchmark.exe. Pour obtenir des résultats précis, exécutez cette commande à partir d’un ordinateur virtuel dans la même région que votre cache.

-   Demandes de pipeline la valeur test à l’aide d’une charge utile de 1 k

    redis-benchmark.exe, -h **yourcache**. redis.cache.windows.net - **yourAccesskey** -t défini - n 1000000 -d 1 024 - P 50
    
-   Les requêtes à l’aide d’une charge utile de 1 k test canalisée en rafale GET. 
    Remarque : Exécutez le jeu test indiqué ci-dessus pour remplir le cache
    
    redis-benchmark.exe, -h **yourcache**. redis.cache.windows.net - **yourAccesskey** -t GET -n 1000000 - d 1 024 - P 50

<a name="threadpool"></a>
### <a name="important-details-about-threadpool-growth"></a>Détails importants sur la croissance de pool de threads

Le pool de threads CLR a deux types de threads - « Travailleur » et « Port de terminaison d’e/s » (alias IOCP) de threads. 

-   Threads de travail sont utilisés lorsque pour des tâches comme le traitement de `Task.Run(…)` ou `ThreadPool.QueueUserWorkItem(…)` méthodes. Ces threads sont également utilisés par les différents composants dans le CLR lorsque le travail doit se produire sur un thread d’arrière-plan.
-   Threads IOCP sont utilisés lorsque les e/s asynchrone se produit (par exemple, la lecture à partir du réseau). 

Le pool de threads fournit des nouveaux threads de travail ou threads de terminaison d’e/s à la demande (sans toute régulation) jusqu'à ce qu’il atteigne le paramètre « Minimum » pour chaque type de thread. Par défaut, le nombre minimal de threads est défini pour le nombre de processeurs sur un système. 

Une fois que le nombre d’existant (occupé) threads de correspondances dans que le « » nombre de threads, le pool de threads sera accélérateur la fréquence à laquelle est injecte nouveaux threads à un thread par 500 millisecondes. Cela signifie que si votre système reçoit une rafale de travail qui ont besoin d’un thread IOCP, qu’il traitera ce travail très rapidement. Toutefois, si la transmission en rafale de travail est supérieure au paramètre de « Minimum » configuré, il y aura quelque temps dans la partie du travail de traitement que le pool de threads attend qu’une des deux situations suivantes se produit.

1. Un thread existant est libre de traiter le travail.
1. Aucun thread existant ne devient disponible pour 500 ms, un nouveau thread est créé.

En fait, cela signifie que lorsque le nombre de threads occupés est supérieur à Min threads, vous sont probablement payer un retard de 500 ms avant que le trafic réseau est traité par l’application. En outre, il est important de noter que lorsqu’un thread existant reste inactif pendant plus de 15 secondes (selon ce que je me souviens), il sera nettoyé et ce cycle de croissance et de retrait peut répéter.

Si nous examiner un exemple de message d’erreur à partir de StackExchange.Redis (build 1.0.450 ou version ultérieure), vous verrez qu’elle imprime à présent des statistiques de pool de threads (voir IOCP et travail détails ci-dessous).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
    IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Dans l’exemple ci-dessus, vous pouvez voir que la thread de IOCP il y a 6 threads occupés et le système est configuré pour autoriser les 4 threads minimales. Dans ce cas, le client serait probablement connaissent des retards de 500 ms deux car 6 4 >.

Notez que StackExchange.Redis pouvez atteinte des délais d’expiration si la croissance de threads IOCP ou de travailleur obtient accélérée.

### <a name="recommendation"></a>Recommandation

Compte tenu de ces informations, nous recommandons fortement que les clients définir la valeur de la configuration minimale pour les threads de travail et d’IOCP sur une valeur supérieure à la valeur par défaut. Nous ne pouvons fournir des conseils universelle sur ce que cette valeur doit être dans la mesure où la valeur correcte pour une application sera trop haut/bas d’une autre application. Ce paramètre peut également altérer les performances des autres parties des applications complexes, afin que chaque client doit régler ce paramètre à leurs besoins spécifiques. Un bon point de départ est 200 ou 300, puis tester et ajuster en fonction des besoins.

Comment faire pour configurer ce paramètre :

-   Dans ASP.NET, utilisez le [paramètre de configuration « minIoThreads »][] , sous la `<processModel>` élément de configuration dans le fichier web.config. Si vous exécutez à l’intérieur de sites Web d’Azure, ce paramètre n’est pas exposé via les options de configuration. Toutefois, vous devez être toujours en mesure de définir par programmation (voir ci-dessous), à partir de votre méthode Application_Start dans global.asax.cs.

> **Remarque importante :** la valeur spécifiée dans cet élément de configuration est un paramètre *par cœur* . Par exemple, si vous avez une machine 4 principale et que vous souhaitez que votre paramètre minIOThreads 200 lors de l’exécution, vous utilisez `<processModel minIoThreads="50"/>`.

-   En dehors d’ASP.NET, utilisez l' [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API.

<a name="server-gc"></a>
### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Activer le catalogue global du serveur obtenir plus de débit sur le client lors de l’utilisation de StackExchange.Redis

L’activation du serveur GC peut optimiser le client et fournir de meilleures performances et le débit lors de l’utilisation de StackExchange.Redis. Pour plus d’informations sur le serveur de catalogue global et comment l’activer, consultez les articles suivants.

-   [Pour activer le serveur GC](https://msdn.microsoft.com/library/ms229357.aspx)
-   [Principes de base du Garbage Collection](https://msdn.microsoft.com/library/ee787088.aspx)
-   [Performances et le garbage Collection](https://msdn.microsoft.com/library/ee851764.aspx)







<a name="cache-monitor"></a>
### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Comment surveiller la santé et les performances de mon cache ?

Les instances de Cache de Redis Microsoft Azure peuvent être surveillées dans [Azure portal](https://portal.azure.com). Vous pouvez statistiques épingler des graphiques de mesures pour le Startboard, personnaliser la plage de date et d’heure des graphiques de contrôle, ajouter et supprimer des mesures à partir des diagrammes et définir des alertes lorsque certaines conditions sont remplies. Pour plus d’informations, reportez-vous à la section [Cache de Redis moniteur Azure](cache-how-to-monitor.md).

La section **Support + la résolution des problèmes** de la lame de Redis de Cache de **paramètres** contient également plusieurs outils pour surveiller et dépanner vos caches. 

-   **Résolution des problèmes** fournit des informations sur les problèmes courants et leurs solutions.
-   **Les journaux d’audit** fournit des informations sur les actions effectuées sur le cache. Vous pouvez également utiliser le filtrage pour développer cette vue pour inclure d’autres ressources.
-   **Santé des ressources** assure le suivi de vos ressources et vous indique s’il s’exécute comme prévu. Pour plus d’informations sur le service de santé ressource d’Azure, consultez [vue d’ensemble de la santé de ressource d’Azure](../resource-health/resource-health-overview.md).
-   **Nouvelle demande d’assistance** fournit des options pour ouvrir une demande de support pour votre cache.

Ces outils permettent de surveiller la santé de vos instances de Cache Redis d’Azure et vous aider à gérer vos applications de mise en cache. Pour plus d’informations, consultez [paramètres de dépannage et de Support](cache-configure.md#support-amp-troubleshooting-settings).

### <a name="my-cache-diagnostics-storage-account-settings-changed-what-happened"></a>Cache de diagnostic stockage paramètres de mon compte modifiés, pourquoi ?

Caches dans la même région et d’abonnement partagent les mêmes paramètres de stockage de diagnostics, et si la configuration est modifiée (diagnostic activé/désactivé ou modification du compte de stockage), elle s’applique à tous les caches de l’abonnement qui se trouvent dans cette région. Si les paramètres de diagnostic pour votre cache ont changé, vérifiez si les paramètres de diagnostic pour un autre cache même abonnement et région ont changé. L’une consiste à vérifier pour afficher les journaux d’audit pour votre cache pour un `Write DiagnosticSettings` événement. Pour plus d’informations sur l’utilisation des journaux d’audit, reportez-vous à la section [affichage des événements et des journaux d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md) et [Auditer les opérations effectuées avec le Gestionnaire de ressources](../resource-group-audit.md). Pour plus d’informations sur la surveillance des événements de Cache Redis d’Azure, consultez [les alertes et les opérations](cache-how-to-monitor.md#operations-and-alerts).

### <a name="why-is-diagnostics-enabled-for-some-new-caches-but-not-others"></a>Pourquoi les tests de diagnostic est activé pour certains nouveaux caches mais pas d’autres ?

Caches dans la même région et d’abonnement partagent les mêmes paramètres de stockage de diagnostics. Si vous créez un nouveau cache dans la même région et d’abonnement comme cache une autre qui a activé des diagnostics, tests de diagnostic est activé sur le nouveau cache à l’aide des mêmes paramètres.


<a name="cache-timeouts"></a>
### <a name="why-am-i-seeing-timeouts"></a>Pourquoi affiche-t-il des délais d’attente ?

Délais d’expiration se produisent dans le client qui vous permet de communiquer avec Redis. La plupart du temps Redis serveur n’expire pas. Lorsqu’une commande est envoyée au serveur Redis, la commande est la file d’attente et Redis server finalement capte la commande et l’exécute. Toutefois le client peut expirer au cours de ce processus et si c’est le cas une exception est déclenché sur le côté d’appel. Pour plus d’informations sur la résolution des problèmes de délai d’attente, consultez [résolution des problèmes du côté Client](cache-how-to-troubleshoot.md#client-side-troubleshooting) et les [exceptions de délai d’attente StackExchange.Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).


<a name="cache-disconnect"></a>
### <a name="why-was-my-client-disconnected-from-the-cache"></a>Pourquoi mon client a été déconnectée du cache ?

Voici une raison commune pour une déconnexion de cache.

-   Causes du côté client
    -   L’application cliente a été redéployée.
    -   L’application client exécuté une opération de mise à l’échelle.
        -   Pour les Services en nuage ou applications Web, cela peut être dû à la mise à l’échelle.
    -   Modification de la couche réseau côté client.
    -   Erreurs transitoires s’est produite dans le client ou dans les nœuds de réseau entre le client et le serveur.
    -   Les limites de bande passante a été atteint.
    -   Processeur de manière intensive des opérations a pris trop de temps.
-   Causes du côté serveur
    -   Ce cache standard offre, le service de Cache de Redis Azure exécutée par un basculement à partir du nœud principal vers le nœud secondaire.
    -   Azure a correction de l’instance dans laquelle le cache a été déployé
        -   Il peut s’agir de mises à jour du serveur Redis ou la maintenance générale de machine virtuelle.







### <a name="which-azure-cache-offering-is-right-for-me"></a>L’offre de Cache d’Azure est pour moi ?

>[AZURE.IMPORTANT]En fonction de l’année dernière [annonce](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), service Service de Cache géré Azure et d’Azure dans le rôle Cache est déclassé le 30 novembre 2016. Notre recommandation est d’utiliser le [Cache de Redis d’Azure](https://azure.microsoft.com/services/cache/). Pour plus d’informations sur la migration, consultez [migrer à partir de Service managé de Cache dans le Cache de Redis d’Azure](cache-migrate-to-redis.md).

### <a name="azure-redis-cache"></a>Cache d’Azure Redis
Cache de Redis Azure est généralement disponible dans des tailles jusqu'à 53 Go et les a un SLA de 99,9 % de disponibilité. La nouvelle [couche de prime](cache-premium-tier-intro.md) propose des tailles jusqu'à 530 Go et prise en charge de la gestion de clusters, VNET et la persistance, avec un contrat SLA de 99,9 %.

Cache de Redis Azure offre aux clients la possibilité d’utiliser un cache de Redis dédié, sécurisé, géré par Microsoft. Avec cette offre, vous devez exploiter l’ensemble riche des fonctionnalités et l’écosystème fourni par Redis et fiables d’hébergement et de contrôle de Microsoft.

À la différence des caches traditionnels qui traitent uniquement des paires clé-valeur, Redis est populaire pour ses types de données hautement performantes. Redis également prise en charge de l’exécution des opérations atomiques sur ces types, comme l’ajout à une chaîne ; incrémenter la valeur d’un hachage ; en exécutant un push d’une liste ; informatique ensemble intersection, union et différence ; ou le membre avec un classement plus élevé dans un jeu trié. Les autres fonctionnalités incluent la prise en charge des transactions, pub/sub, Lua scripting, de clés avec une limite de time-to-live et les paramètres de configuration à apporter Redis se comportent davantage comme un cache traditionnel.

Un autre aspect clé pour la réussite de Redis est l’écosystème éclatantes, sain source ouverte construit autour de lui. Ceci se reflète dans l’ensemble diversifié de clients de Redis disponibles dans plusieurs langues. Cela permet qu’il soit utilisé par presque toute la charge de travail que vous créeriez dans Azure. 

Pour plus d’informations sur la mise en route avec Cache Redis d’Azure, consultez [comment mettre en Cache de Redis utilisation Azure](cache-dotnet-how-to-use-azure-redis-cache.md) et [documentation du Cache Redis d’Azure](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="managed-cache-service"></a>Service géré de Cache
[Managed service est configuré pour être retirée le 30 novembre 2016 de Cache.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### <a name="in-role-cache"></a>Dans le rôle Cache
[Dans le rôle Cache est définie pour être retirée le 30 novembre 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





[paramètre de configuration « minIoThreads »]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
