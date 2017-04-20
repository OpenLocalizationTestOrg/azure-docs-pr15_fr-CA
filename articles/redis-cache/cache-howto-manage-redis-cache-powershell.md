<properties
    pageTitle="Gérer le Cache Redis Azure avec PowerShell Azure | Microsoft Azure"
    description="Découvrez comment effectuer des tâches administratives pour le Cache de Redis Azure à l’aide de PowerShell d’Azure."
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
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Gérer le Cache Redis Azure avec PowerShell Azure

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [CLI Azure](cache-manage-cli.md)

Cette rubrique vous montre comment effectuer des tâches courantes telles que la création, mise à jour et adapter vos instances de Cache Redis d’Azure, régénérer les touches d’accès rapide et comment afficher des informations sur vos caches. Pour obtenir une liste complète des applets de commande PowerShell de Cache Redis Azure, reportez-vous à la section [applets de commande de Cache Redis d’Azure](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)][modèle classique](../resource-manager-deployment-model.md#classic-deployment-characteristics).

## <a name="prerequisites"></a>Conditions préalables

Si vous avez déjà installé PowerShell d’Azure, vous devez disposer d’Azure PowerShell version 1.0.0 ou version ultérieure. Vous pouvez vérifier la version de PowerShell Azure que vous avez installés à l’aide de cette commande à l’invite de commande PowerShell d’Azure.

    Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Tout d’abord, vous devez vous connecter à Azure avec cette commande.

    Login-AzureRmAccount

Spécifiez l’adresse e-mail de votre compte Azure et son mot de passe dans la boîte de dialogue de connexion de le Microsoft Azure.

Ensuite, si vous avez plusieurs abonnements Azure, vous devez définir votre abonnement Azure. Pour afficher la liste de vos abonnements actuels, exécutez la commande suivante.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Pour spécifier l’abonnement, exécutez la commande suivante. Dans l’exemple suivant, le nom de l’abonnement est `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Avant de pouvoir utiliser Windows PowerShell avec le Gestionnaire de ressources Azure, vous devez les éléments suivants :

- Windows PowerShell, Version 3.0 ou 4.0. Pour rechercher la version de Windows PowerShell, tapez :`$PSVersionTable` et vérifiez la valeur de `PSVersion` est 3.0 ou 4.0. Pour installer une version compatible, consultez [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Pour obtenir une aide détaillée pour toute applet de commande que vous consultez dans ce didacticiel, utilisez l’applet de commande Get-Help.

    Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l’aide pour le `New-AzureRmRedisCache` type d’applet de commande :

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-azure-government-cloud-or-azure-china-cloud"></a>Comment se connecter au nuage du gouvernement Azure ou de nuage de Chine Azure

Par défaut l’Azure environnement est `AzureCloud`, qui représente l’instance du nuage d’Azure global. Pour vous connecter à une instance différente, utilisez la `Add-AzureRmAccount` avec la `-Environment` ou -`EnvironmentName` commutateur de ligne de commande avec l’environnement désiré ou le nom de l’environnement.

Pour voir la liste des environnements disponibles, exécutez le `Get-AzureRmEnvironment` applet de commande.

### <a name="to-connect-to-the-azure-government-cloud"></a>Pour se connecter au nuage gouvernement Azure

Pour vous connecter dans le nuage de gouvernement d’Azure, utilisez une des commandes suivantes.

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

ou

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Pour créer un cache dans le nuage de gouvernement d’Azure, utilisez un des emplacements suivants.

-   Virginie de USGov
-   USGov Iowa

Pour plus d’informations sur le nuage de gouvernement Azure, consultez [Microsoft Azure gouvernement](https://azure.microsoft.com/features/gov/) et [Guide de développeur de Microsoft Azure gouvernement](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Pour vous connecter dans le nuage de Chine Azure

Pour vous connecter dans le nuage de Chine d’Azure, utilisez une des commandes suivantes.

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

ou

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Pour créer un cache dans le nuage de Chine d’Azure, utilisez un des emplacements suivants.

-   Chine orientale
-   Nord de la Chine

Pour plus d’informations sur le nuage de Chine Azure, consultez [AzureChinaCloud pour Azure exploité par 21Vianet en Chine](http://www.windowsazure.cn/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Propriétés utilisées pour PowerShell de Cache Redis Azure

Le tableau suivant contient des propriétés et des descriptions pour les paramètres couramment utilisés lors de la création et la gestion de vos instances de Cache Redis d’Azure à l’aide de PowerShell d’Azure.

| Paramètre          | Description                                                                                                                                                                                                        | Par défaut  |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Nom               | Nom du cache                                                                                                                                                                                                  |          |
| Emplacement           | Emplacement du cache                                                                                                                                                                                              |          |
| ResourceGroupName  | Nom de groupe de ressources dans lequel créer le cache                                                                                                                                                                   |          |
| Taille               | La taille du cache. Les valeurs valides sont : P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 Mo, 1 Go, 2.5 Go, 6 Go, 13 Go, Go 26, 53 Go                                                                     | 1 GO      |
| ShardCount         | Le nombre de milieu des fragments pour créer lors de la création d’un cache premium avec activation des clusters. Les valeurs valides sont : 1, 2, 3, 4, 5, 6, 7, 8, 9, 10                                                                                                      |          |
| POINT DE STOCK                | Spécifie la référence SKU du cache. Les valeurs valides sont : basique, Standard, Premium                                                                                                                                         | Standard |
| RedisConfiguration | Spécifie les paramètres de configuration de Redis. Pour plus d’informations sur chaque paramètre, consultez le tableau suivant de [Propriétés de RedisConfiguration](#redisconfiguration-properties) . |          |
| EnableNonSslPort   | Indique si le port non-SSL est activé.                                                                                                                                                                     | False    |
| MaxMemoryPolicy    | Ce paramètre a été désapprouvé - utilisez RedisConfiguration à la place.                                                                                                                                              |          |
| StaticIP           | Lors de l’hébergement de votre cache dans un VNET, spécifie une adresse IP unique dans le sous-réseau pour le cache. Si n’est fourni, une est choisie pour vous à partir du sous-réseau.                                                                                                                     |          |
| Sous-réseau             | Lors de l’hébergement de votre cache dans un VNET, spécifie le nom du sous-réseau dans lequel déployer le cache.                                                                                                                  |          |
| VirtualNetwork     | Lors de l’hébergement de votre cache dans un VNET, spécifie l’ID de ressource de la VNET dans lequel vous souhaitez déployer le cache.                                                                                                             |          |
| Type de clé            | Spécifie la touche d’accès rapide à régénérer lors du renouvellement des touches d’accès rapide. Les valeurs valides sont : principal, secondaire |  |                                                                                                                                                                                                              |          |


### <a name="redisconfiguration-properties"></a>Propriétés de RedisConfiguration

| Propriété                      | Description                                                                                                          | Niveaux de prix       |
|-------------------------------|----------------------------------------------------------------------------------------------------------------------|---------------------|
| RDB-sauvegarde-activée            | Si la [persistance des données de Redis](cache-how-to-premium-persistence.md) est activé                                     | Premium uniquement        |
| RDB-stockage-chaîne de connexion | La chaîne de connexion pour le compte de stockage pour la [persistance des données de Redis](cache-how-to-premium-persistence.md)       | Premium uniquement        |
| fréquence de sauvegarde RDB          | Fréquence de sauvegarde pour la [persistance des données de Redis](cache-how-to-premium-persistence.md)                               | Premium uniquement        |
| MaxMemory-réservé            | Configure la [mémoire réservée](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) pour les processus non cache | Standard et Premium |
| MaxMemory-stratégie              | Configure la [stratégie d’éviction](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) pour le cache           | Tous les niveaux de prix   |
| avertir-keyspace-événements        | Configure des [notifications de keyspace](cache-configure.md#keyspace-notifications-advanced-settings)                     | Standard et Premium |
| hachage-max-ziplist-écritures      | Configure l' [Optimisation de la mémoire](http://redis.io/topics/memory-optimization) pour les types de données d’agrégation          | Standard et Premium |
| max-ziplist-valeur de hachage        | Configure l' [Optimisation de la mémoire](http://redis.io/topics/memory-optimization) pour les types de données d’agrégation          | Standard et Premium |
| max-intset-entrées        | Configure l' [Optimisation de la mémoire](http://redis.io/topics/memory-optimization) pour les types de données d’agrégation          | Standard et Premium |
| zset-max-ziplist-écritures      | Configure l' [Optimisation de la mémoire](http://redis.io/topics/memory-optimization) pour les types de données d’agrégation          | Standard et Premium |
| valeur-ziplist-max-zset        | Configure l' [Optimisation de la mémoire](http://redis.io/topics/memory-optimization) pour les types de données d’agrégation          | Standard et Premium |
| bases de données                     | Configure le nombre de bases de données. Cette propriété peut être configurée uniquement lors de la création du cache.                          | Standard et Premium |

## <a name="to-create-a-redis-cache"></a>Pour créer un Cache Redis

Les nouvelles instances de Cache Redis d’Azure sont créés à l’aide de l’applet de commande [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

>[AZURE.IMPORTANT] Le portail de la première fois que vous créez un cache Redis dans un abonnement via le portail Azure, inscrit le `Microsoft.Cache` espace de noms pour cet abonnement. Si vous essayez de créer le premier cache Redis pour un abonnement à l’aide de PowerShell, vous devez d’abord inscrire cet espace de noms à l’aide de la commande suivante : dans le cas contraire applets de commande telles que `New-AzureRmRedisCache` et `Get-AzureRmRedisCache` échoue.
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

Pour afficher la liste des paramètres disponibles et leurs descriptions de `New-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed
    
    NAME
        New-AzureRmRedisCache
    
    SYNOPSIS
        Creates a new redis cache.
    
    
    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]
    
    
    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.
    
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to create.
    
        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.
    
        -Location <String>
            Location in which to create the redis cache.
    
        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}
    
        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Pour créer un cache avec les paramètres par défaut, exécutez la commande suivante.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, et `Location` sont les paramètres requis, mais les autres sont facultatifs et ont des valeurs par défaut. Exécution de la commande précédente crée une instance Standard SKU Azure Redis Cache avec le nom spécifié, un emplacement et un groupe de ressources, ce qui est de 1 Go de taille avec le port non-SSL désactivé.

Pour créer un cache de prime, spécifiez une taille de P1 (6 Go à 60 Go), P2 (13 à 130 Go), P3 (26 à 260 Go), ou P4 (53 à 530 Go). Pour activer la mise en cluster, spécifiez un nombre de partagé à l’aide de la `ShardCount` paramètre. L’exemple suivant crée un cache de prime de P1 avec 3 milieu des fragments. Un cache de prime P1 est de 6 Go de taille, et étant donné que nous avons spécifié le milieu des trois fragments la taille totale est de 18 Go (3 x 6 Go).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Pour spécifier des valeurs pour le `RedisConfiguration` paramètre, inclure les valeurs à l’intérieur de `{}` en tant que clé/valeur comme paires `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. L’exemple suivant crée un cache standard de 1 Go avec `allkeys-random` maxmemory stratégie et keyspace les notifications configurées avec `KEA`. Pour plus d’informations, consultez [notifications de Keyspace (paramètres avancés)](cache-configure.md#keyspace-notifications-advanced-settings) et [-stratégie Maxmemory et réservés maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>
## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Pour configurer les paramètre lors de la création du cache des bases de données

Le `databases` peut être configuré uniquement lors de la création du cache. L’exemple suivant crée une prime P3 cache (Go 26) avec 48 bases de données à l’aide de l’applet de commande [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Pour plus d’informations sur le `databases` propriété, reportez-vous à la section [configuration du serveur par défaut du Cache Redis les Azure](cache-configure.md#default-redis-server-configuration). Pour plus d’informations sur la création d’un cache à l’aide de l’applet de commande [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) , consultez la section précédente [pour créer un Cache de Redis](#to-create-a-redis-cache) .

## <a name="to-update-a-redis-cache"></a>Mettre à jour un cache Redis

Azure instances de Cache de Redis sont mis à jour à l’aide de l’applet de commande [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) .

Pour afficher la liste des paramètres disponibles et leurs descriptions de `Set-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed
    
    NAME
        Set-AzureRmRedisCache
    
    SYNOPSIS
        Set redis cache updatable parameters.
    
    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]
    
    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to update.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Le `Set-AzureRmRedisCache` applet de commande peut être utilisé pour mettre à jour des propriétés telles que `Size`, `Sku`, `EnableNonSslPort`et `RedisConfiguration` valeurs. 

La commande suivante met à jour la stratégie de maxmemory pour le Cache Redis nommé myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## <a name="to-scale-a-redis-cache"></a>Mise à l’échelle un cache Redis

`Set-AzureRmRedisCache`peut être utilisé pour mettre à l’échelle un cache Azure Redis instance lorsque la `Size`, `Sku`, ou `ShardCount` propriétés sont modifiées. 

>[AZURE.NOTE]Mise à l’échelle d’un cache à l’aide de PowerShell est les limites et les instructions en tant que la mise à l’échelle d’un cache à partir du portail Azure même. Vous pouvez mettre à l’échelle d’un niveau de tarification différent avec les restrictions suivantes.
>
>-  Vous ne peut pas mettre à l’échelle d’un niveau de prix supérieur vers un niveau de prix inférieur.
>    -    Vous ne pouvez pas mettre à l’échelle à partir d’un cache de **prime** à un **Standard** ou un cache de **base** .
>    -    Vous ne pouvez pas mettre à l’échelle d’un cache **Standard** à un cache de **base** .
>-  Vous pouvez mettre à l’échelle à partir d’un cache de **base** à un cache **Standard** , mais vous ne pouvez pas modifier la taille en même temps. Si vous avez besoin d’une taille différente, vous pouvez effectuer une opération de mise à l’échelle suivante pour obtenir la taille souhaitée.
>-  Vous ne peut pas mettre à l’échelle d’un cache de **base** directement à un cache de **prime** . Vous devez mettre à l’échelle de **base** **Standard** en une seule opération de mise à l’échelle, puis du **Standard** de **prime** dans une opération de mise à l’échelle suivante.
>-  Vous ne pouvez pas mettre à l’échelle d’une taille supérieure à la **C0 (250 Mo)** taille.
>
>Pour plus d’informations, consultez [comment mettre en Cache de Redis échelle Azure](cache-how-to-scale.md).

L’exemple suivant montre comment mettre à l’échelle un cache nommé `myCache` à un cache de 2,5 Go. Notez que cette commande fonctionne pour un Basic ou un cache Standard.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Une fois que cette commande est émise, l’état de la mémoire cache est renvoyé (semblable à l’appel `Get-AzureRmRedisCache`). Notez que les `ProvisioningState` est `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
    
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Lorsque l’opération de mise à l’échelle est terminée, le `ProvisioningState` devient `Succeeded`. Si vous devez effectuer une opération de mise à l’échelle suivante, comme la modification de base standard, puis en modifiant la taille, vous devez attendre jusqu'à ce que l’opération précédente est terminée ou que vous recevez une erreur semblable à la suivante.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Pour obtenir des informations sur un cache Redis

Vous pouvez récupérer des informations sur un cache à l’aide de l’applet de commande [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) .

Pour afficher la liste des paramètres disponibles et leurs descriptions de `Get-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed
    
    NAME
        Get-AzureRmRedisCache
    
    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.
    
    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.
    
        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
    
        If no parameters are given than it will return details about all caches the current subscription.
    
    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Pour renvoyer des informations sur tous les caches de l’abonnement en cours, exécutez `Get-AzureRmRedisCache` sans aucun paramètre.

    Get-AzureRmRedisCache

Pour renvoyer des informations sur tous les caches dans un groupe de ressources spécifiques, exécutez `Get-AzureRmRedisCache` avec la `ResourceGroupName` paramètre.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Pour renvoyer des informations sur un cache spécifique, exécutez `Get-AzureRmRedisCache` avec la `Name` paramètre contenant le nom du cache et la `ResourceGroupName` paramètre avec le groupe de ressources contenant ce cache.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Pour récupérer les touches d’accès rapide pour un cache Redis

Pour récupérer les touches d’accès rapide de votre cache, vous pouvez utiliser l’applet de commande [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) .

Pour afficher la liste des paramètres disponibles et leurs descriptions de `Get-AzureRmRedisCacheKey`, exécutez la commande suivante.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
    
    NAME
        Get-AzureRmRedisCacheKey
    
    SYNOPSIS
        Gets the accesskeys for the specified redis cache.
    
    
    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Pour récupérer les clés de votre cache, appelez le `Get-AzureRmRedisCacheKey` applet de commande et passez le nom de votre cache le nom du groupe de ressources contenant le cache.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Pour régénérer des touches d’accès rapide pour le cache de votre Redis

Pour régénérer les touches d’accès rapide de votre cache, vous pouvez utiliser l’applet de commande [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) .

Pour afficher la liste des paramètres disponibles et leurs descriptions de `New-AzureRmRedisCacheKey`, exécutez la commande suivante.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
    
    NAME
        New-AzureRmRedisCacheKey
    
    SYNOPSIS
        Regenerates the access key of a redis cache.
    
    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
    
    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
    
        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    
Pour régénérer la clé primaire ou secondaire de votre cache, appelez le `New-AzureRmRedisCacheKey` applet de commande et passez le nom de groupe de ressources et spécifiez `Primary` ou `Secondary` pour la `KeyType` paramètre. Dans l’exemple suivant, la clé d’accès secondaire pour un cache est régénérée.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
    
    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Pour supprimer un cache de Redis

Pour supprimer un cache de Redis, utilisez l’applet de commande [Remove-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) .

Pour afficher la liste des paramètres disponibles et leurs descriptions de `Remove-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
    
    NAME
        Remove-AzureRmRedisCache
    
    SYNOPSIS
        Remove redis cache if exists.
    
    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
    
    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.
    
        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.
    
        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.
    
        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Dans l’exemple suivant, le cache nommé `myCache` est supprimé.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Pour importer un cache Redis

Vous pouvez importer des données dans une instance de Cache Redis d’Azure à l’aide de la `Import-AzureRmRedisCache` applet de commande.

>[AZURE.IMPORTANT] Importation/exportation n’est disponible pour les caches de [niveau de prime](cache-premium-tier-intro.md) . Pour plus d’informations sur l’importation/exportation, reportez-vous à la section [Importer et exporter des données dans le Cache de Redis Azure](cache-how-to-import-export-data.md).

Pour afficher la liste des paramètres disponibles et leurs descriptions de `Import-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed
    
    NAME
        Import-AzureRmRedisCache
    
    SYNOPSIS
        Import data from blobs to Azure Redis Cache.
    
    
    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.
    
        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

La commande suivante importe des données à partir du blob spécifié par l’uri SAS dans le Cache de Redis Azure.


    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Pour exporter un cache Redis

Vous pouvez exporter des données à partir d’une instance de Cache Redis d’Azure à l’aide de la `Export-AzureRmRedisCache` applet de commande.

>[AZURE.IMPORTANT] Importation/exportation n’est disponible pour les caches de [niveau de prime](cache-premium-tier-intro.md) . Pour plus d’informations sur l’importation/exportation, reportez-vous à la section [Importer et exporter des données dans le Cache de Redis Azure](cache-how-to-import-export-data.md).

Pour afficher la liste des paramètres disponibles et leurs descriptions de `Export-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed
    
    NAME
        Export-AzureRmRedisCache
    
    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.
    
    
    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Prefix <String>
            Prefix to use for blob names.
    
        -Container <String>
            SAS url of container where data should be exported.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


La commande suivante exporte les données d’une instance de Cache Redis d’Azure dans le conteneur spécifié par l’uri SAS.


        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Pour redémarrer un cache Redis

Vous pouvez redémarrer votre instance de Cache Redis d’Azure à l’aide de la `Reset-AzureRmRedisCache` applet de commande.

>[AZURE.IMPORTANT] Redémarrage est uniquement disponible pour les caches de [niveau de prime](cache-premium-tier-intro.md) . Pour plus d’informations sur le redémarrage de votre cache, reportez-vous à la section [administration de Cache - redémarrer](cache-administration.md#reboot).

Pour afficher la liste des paramètres disponibles et leurs descriptions de `Reset-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed
    
    NAME
        Reset-AzureRmRedisCache
    
    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.
    
    
    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".
    
        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.
    
        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.
    
        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

La commande suivante redémarre les deux nœuds du cache spécifié.

    
        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force
    

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation de Windows PowerShell avec Azure, consultez les ressources suivantes :

- [Documentation d’applet de commande Redis le Cache Azure sur MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Applets de commande de gestionnaire de ressources Azure](http://go.microsoft.com/fwlink/?LinkID=394765): Apprenez à utiliser des applets de commande dans le module AzureResourceManager.
- [Des groupes de ressources de l’aide pour gérer vos ressources Azure](../resource-group-template-deploy-portal.md): Apprenez à créer et gérer des groupes de ressources dans le portail Azure.
- [Blog Azure](http://blogs.msdn.com/windowsazure): en savoir plus sur les nouvelles fonctionnalités dans Azure.
- [Blog de Windows PowerShell](http://blogs.msdn.com/powershell): en savoir plus sur les nouvelles fonctionnalités de Windows PowerShell.
- [Concours « hey, Scripting Guy ! » Blog](http://blogs.technet.com/b/heyscriptingguy/): obtenir de réelles trucs et astuces de la Communauté de Windows PowerShell.
