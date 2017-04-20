<properties 
    pageTitle="Comment faire pour configurer la prise en charge de réseau virtuel pour un Cache de Redis prime Azure | Microsoft Azure" 
    description="Découvrez comment créer et gérer la prise en charge de réseau virtuel pour vos instances de Cache Redis d’Azure niveau Premium" 
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
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Comment configurer la prise en charge de réseau virtuel pour un Cache de Redis prime Azure
Cache de Redis Azure a des offres de cache différents qui offrent une grande souplesse dans le choix de la taille du cache et de fonctionnalités, y compris la nouvelle couche de prime.

Les fonctionnalités de niveau premium Cache Redis d’Azure incluent le clustering, la persistance et la prise en charge de réseau virtuel (VNet). Un VNet est un réseau privé dans le nuage. Lorsqu’une instance de Cache Redis d’Azure est configurée avec un VNet, il n’est pas adressable publiquement et ne sont accessibles qu’à partir des machines virtuelles et les applications de la VNet. Cet article décrit comment configurer la prise en charge de réseau virtuel pour une instance de Cache Redis d’Azure premium.

>[AZURE.NOTE] Cache de Redis Azure prend en charge les deux classique et ARM VNets.

Pour plus d’informations sur d’autres fonctionnalités de cache premium, consultez [Introduction à la couche de prime de Cache Redis Azure](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Pourquoi VNet ?
Déploiement [d’Azure, réseau virtuel (VNet)](https://azure.microsoft.com/services/virtual-network/) fournit une sécurité renforcée et l’isolement pour votre Cache Redis de Windows Azure, ainsi que des sous-réseaux, stratégies de contrôle d’accès et d’autres fonctionnalités restreindre davantage l’accès au Cache de Redis d’Azure.

## <a name="virtual-network-support"></a>Prise en charge de réseau virtuel
Prise en charge du réseau (VNet) virtuel est configuré sur la lame de **Nouveau Cache Redis** lors de la création du cache. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Une fois que vous avez sélectionné une prime de niveau de prix, vous pouvez configurer les VNet de Cache Redis Azure integration en sélectionnant un VNet qui se trouve dans le même abonnement et le même emplacement que votre cache. Pour utiliser un nouveau VNet, tout d’abord le créer en suivant les étapes de [Création d’un réseau virtuel via le portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [créer un réseau virtuel (classic) via le portail d’Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) , puis retournez à la blade de **Nouveau Cache Redis** pour créer et configurer le cache de la prime.

Pour configurer le VNet pour votre nouveau cache, cliquez sur la lame de **Nouveau Cache Redis** **Réseau virtuel** et sélectionnez le VNet désiré dans la liste déroulante.

![Réseau virtuel][redis-cache-vnet]

Sélectionnez le sous-réseau souhaité dans la liste déroulante de **sous-réseau** et spécifiez **l’adresse IP statique**souhaité. Si vous utilisez un classique VNet le champ de **l’adresse IP statique** est facultatif, et si aucun n’est spécifié, le choix est du sous-réseau sélectionné.

>[AZURE.IMPORTANT] Lorsque vous déployez un Cache Redis d’Azure à une VNet ARM, le cache doit être dans un sous-réseau dédié qui ne contient aucune autre ressource à l’exception des instances de Cache Redis d’Azure. Si une tentative est faite pour déployer un Cache Redis d’Azure à une VNet ARM à un sous-réseau qui contient d’autres ressources, le déploiement échoue.

![Réseau virtuel][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] Les quatre premières adresses d’un sous-réseau sont réservés et ne peuvent pas être utilisés. Pour plus d’informations, consultez [existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Une fois que le cache est créé, vous pouvez afficher la configuration de la VNet en cliquant sur **Réseau virtuel** à partir de la blade de **paramètres** .

![Réseau virtuel][redis-cache-vnet-info]


Pour vous connecter à votre instance de cache Azure Redis lors de l’utilisation d’un VNet, spécifiez le nom d’hôte de votre cache dans la chaîne de connexion comme indiqué dans l’exemple suivant.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>VNet de Cache Azure Redis Forum aux questions

La liste suivante contient les réponses aux questions fréquemment posées à propos de la mise à l’échelle du Cache Redis d’Azure.

-   [Quels sont certains problèmes de configuration courants avec Cache Redis d’Azure et VNets ?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-   [Puis-je utiliser VNets avec un cache de base ou standard ?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-   [Pourquoi créer un cache Redis échoue dans certains sous-réseaux, mais pas d’autres ?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
-   [Toutes les fonctionnalités de cache fonctionnent lors de l’hébergement d’un cache dans un VNET ?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)


## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Quels sont certains problèmes de configuration courants avec Cache Redis d’Azure et VNets ?

Lorsque le Cache de Redis Azure est hébergé dans un VNet, les ports dans le tableau suivant sont utilisées. Si ces ports sont bloqués, le cache peut ne pas fonctionne correctement. Un ou plusieurs de ces ports bloqués est le problème le plus courant une configuration incorrecte lors de l’utilisation du Cache de Redis d’Azure dans un VNet.

| Port (s)     | Direction        | Protocole de transport | Objectif                                                                           | Adresse IP distante                           |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443     | Sortant         | TCP                | Redis de dépendances sur Azure Storage/infrastructure à clé publique (Internet)                                | *                                   |
| 53          | Sortant         | TCP/UDP            | Redis de dépendances sur DNS (Internet/VNet)                                         | *                                   |
| 6379, 6380  | Trafic entrant          | TCP                | Communication client à Redis, l’équilibrage de charge Azure                               | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 8443        | Entrant et sortant | TCP                | Détail d’implémentation de Redis                                                   | VIRTUAL_NETWORK                     |
| 8500        | Trafic entrant          | TCP/UDP            | L’équilibrage de la charge Azure                                                              | AZURE_LOADBALANCER                  |
| 10221-10231 | Entrant et sortant | TCP                | Détail d’implémentation pour Redis (peuvent restreindre le point de terminaison distant à VIRTUAL_NETWORK) | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 13000-13999 | Trafic entrant          | TCP                | Communication client à Redis Clusters, l’équilibrage de charge Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 15000-15999 | Trafic entrant          | TCP                | Communication client à Redis Clusters, l’équilibrage de charge Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 16001       | Trafic entrant          | TCP/UDP            | L’équilibrage de la charge Azure                                                              | AZURE_LOADBALANCER                  |
| 20226       | + Entrant sortant | TCP                | Détail d’implémentation pour les Clusters de Redis                                          | VIRTUAL_NETWORK                     |


Il existe des exigences en matière de connectivité réseau pour Azure Redis Cache qui ne peuvent pas être satisfaites au départ dans un réseau virtuel. Cache de Redis Azure requiert tous les éléments suivants afin de fonctionner correctement lorsqu’il est utilisé au sein d’un réseau virtuel.

-  Connectivité de réseau sortante pour les points de terminaison du stockage Azure dans le monde entier. Cela inclut des points de terminaison que qui se trouve dans la même région que l’instance de Cache Redis d’Azure, ainsi que les points de terminaison de stockage situés dans les **autres** régions Azure. Résoudre les points de terminaison de stockage Azure sous les domaines DNS suivants : *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*et *file.core.windows.net*. 
-  Connectivité de réseau sortante pour *ocsp.msocsp.com*, *mscrl.microsoft.com* et *crl.microsoft.com*. Cette connectivité est nécessaire pour prendre en charge les fonctionnalités SSL.
-  La configuration de DNS pour le réseau virtuel doit être capable de résoudre tous les points de terminaison et les domaines mentionnés dans les points précédents. Ces exigences DNS peuvent être satisfaites en garantissant une infrastructure DNS valide est configurée et gérée pour l’ordinateur virtuel.



### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Puis-je utiliser VNets avec un cache de base ou standard ?

VNets utilisable uniquement avec des mémoires cache de prime.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Pourquoi créer un cache Redis échoue dans certains sous-réseaux, mais pas d’autres ?

Si vous déployez un Cache Redis d’Azure à une VNet ARM, le cache doit être dans un sous-réseau dédié qui ne contient aucun autre type de ressource. Si une tentative est faite pour déployer un Cache Redis d’Azure à un sous-réseau ARM VNet qui contient d’autres ressources, le déploiement échoue. Vous devez supprimer les ressources existantes, à l’intérieur du sous-réseau avant de pouvoir créer un cache de Redis.

Vous pouvez déployer plusieurs types de ressources à un VNet classique tant que vous disposez de suffisamment d’adresses IP disponibles.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Toutes les fonctionnalités de cache fonctionnent lors de l’hébergement d’un cache dans un VNET ?

Lorsque le cache est la partie d’un VNET, seuls les clients dans la VNET peuvent accéder au cache. Par conséquent, les fonctionnalités de gestion de cache suivantes ne fonctionnent pas pour l’instant.

-   Redis Console - Redis la Console utilise le client de redis-cli.exe hébergé sur des ordinateurs virtuels qui ne font pas partie de votre VNET, il est impossible de se connecter à votre cache.


## <a name="use-expressroute-with-azure-redis-cache"></a>Utiliser ExpressRoute avec Azure Redis de Cache

Les clients peuvent connecter un circuit de [ExpressRoute d’Azure](https://azure.microsoft.com/services/expressroute/) à leur infrastructure réseau virtuel, étendant ainsi leur réseau local vers Azure. 

Par défaut, un circuit de ExpressRoute nouvellement créé publie un itinéraire par défaut qui permet la connectivité Internet sortante. Avec cette configuration, les applications clientes sont en mesure de se connecter aux autres points de terminaison Azure, y compris le Cache Redis d’Azure.

Cependant, une configuration de client commun est de définir leur propre itinéraire par défaut (0.0.0.0/0) qui force le trafic Internet sortant à la place de flux local. Ce flux de trafic invariablement des sauts de connectivité avec Cache Redis d’Azure, car le trafic sortant est bloqué sur site ou NAT serait sur un nombre d’adresses qui ne fonctionnent plus avec différents points de terminaison Azure non reconnaissable.

La solution consiste à définir un (ou plusieurs) défini par l’utilisateur des itinéraires (UDRs) sur le sous-réseau contenant le Cache Redis d’Azure. Un UDR définit les itinéraires de sous-réseau spécifique qui seront respectées au lieu de l’itinéraire par défaut.

Si possible, il est recommandé d’utiliser la configuration suivante :

- La configuration de ExpressRoute publie 0.0.0.0/0 et par défaut force tunnels tout le trafic sortant sur site.
- Le UDR appliquée au sous-réseau contenant le Cache Redis Azure définit 0.0.0.0/0 avec un type de saut suivant de l’Internet (un exemple de cela est plu bas dans cet article).

L’effet combiné de ces étapes est que le niveau du sous-réseau UDR a priorité sur la ExpressRoute forcé tunneling, garantissant ainsi un accès Internet sortant du cache Redis d’Azure.

Bien que la connexion à une instance de Cache Redis d’Azure à partir d’un site sur application à l’aide de ExpressRoute n’est pas un scénario d’utilisation classique en raison des raisons de performances (pour des performances optimales Azure Redis Cache que les clients doivent être dans la même région comme Cache Redis Azure), dans ce scénario, que le chemin d’accès réseau sortant ne peut pas transiter via des proxys d’entreprise internes, ni qu’il peut être acheminés via le tunnel local de force. Passe alors l’adresse NAT efficace du trafic réseau sortant à partir du Cache de Redis d’Azure. Modification de l’adresse NAT d’un Cache de Redis Azure trafic réseau sortant de l’instance entraîne des problèmes de connectivité à la plupart des points de terminaison répertoriés ci-dessus. Ainsi, les tentatives de création de Cache Redis d’Azure a échoué.

**IMPORTANT :**  Les itinéraires définis dans une UDR **doit** être suffisamment ont la priorité sur les itinéraires annoncés par la configuration de ExpressRoute. L’exemple suivant utilise la plage d’adresses 0.0.0.0/0 large et en tant que tel peut potentiellement être accidentellement substituée par les annonces de routage à l’aide de plages d’adresses plus spécifiques.

**Très IMPORTANT :**  Cache de Redis Azure n’est pas pris en charge avec des configurations de ExpressRoute ce **incorrectement entre-annoncer les itinéraires à partir du chemin d’homologation public pour le chemin d’accès d’homologation privé**. Configurations de ExpressRoute ayant un public peering configuré, recevra les annonces de routage de Microsoft sur un vaste ensemble de plages d’adresses IP de Microsoft Azure. Si ces plages d’adresses sont incorrectement cross-publiés sur le chemin d’accès d’homologation privé, le résultat final est que tous les paquets réseau sortante du sous-réseau de l’instance de Cache Redis d’Azure sont incorrectement force-en tunnel à l’infrastructure de réseau local d’un client. Ce flux de réseau sauts de Cache Redis d’Azure. La solution à ce problème consiste à arrêter des itinéraires de cross-publicité à partir du chemin d’homologation public pour le chemin d’accès d’homologation privé.

Informations générales sur les itinéraires définis par l’utilisateur sont disponibles dans cette [vue d’ensemble](../virtual-network/virtual-networks-udr-overview.md). 

Pour plus d’informations sur ExpressRoute, reportez-vous à la section [présentation technique de ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>Étapes suivantes
Apprenez à utiliser les fonctionnalités de cache plus premium.

-   [Introduction à la couche de prime de Cache Redis Azure](cache-premium-tier-intro.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

