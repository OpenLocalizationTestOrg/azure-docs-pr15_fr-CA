<properties 
    pageTitle="Comment administrer le Cache de Redis Azure | Microsoft Azure"
    description="Apprendre à effectuer les tâches d’administration telles que les mises à jour de redémarrage et de planification pour le Cache de Redis Azure"
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
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="how-to-administer-azure-redis-cache"></a>Comment administrer le Cache Redis d’Azure

Cette rubrique décrit comment effectuer des tâches d’administration telles que le redémarrage et la planification des mises à jour pour vos instances de Cache Redis d’Azure.

>[AZURE.IMPORTANT] Les paramètres et les fonctionnalités décrites dans cet article sont uniquement disponibles pour les caches de niveau Premium.


## <a name="administration-settings"></a>Paramètres d’administration

Les paramètres de Cache de Redis Azure **Administration** vous autorisent à exécuter les tâches administratives suivantes pour votre cache de prime. Pour accéder aux paramètres d’administration, cliquez sur **tous les paramètres** ou les **paramètres** de la lame de Redis de Cache et de la faire défiler à la section **Administration** de la lame de **paramètres** .

![Administration](./media/cache-administration/redis-cache-administration.png)

-   [Redémarrage](#reboot)
-   [Mises à jour de la planification](#schedule-updates)

## <a name="reboot"></a>Redémarrage

La lame de **redémarrage** vous permet de redémarrer un ou plusieurs nœuds de votre cache. Cela vous permet de tester votre application pour la résilience en cas de panne.

![Redémarrage](./media/cache-administration/redis-cache-reboot.png)

Si vous avez un cache premium avec activation des clusters, vous pouvez sélectionner les milieu des fragments du cache pour redémarrer.

![Redémarrage](./media/cache-administration/redis-cache-reboot-cluster.png)

Pour redémarrer un ou plusieurs nœuds de votre cache, sélectionnez les nœuds souhaités, puis cliquez sur **redémarrer**. Si vous avez un cache premium avec activation des clusters, sélectionnez le shard(s) à redémarrer puis cliquez sur **redémarrer**. Après quelques minutes, le redémarrage des nœuds sélectionnés et sont de reconnecter quelques minutes plus tard.

L’impact sur les applications clientes varie selon l’ou les noeuds que vous le redémarrez.

-   **Maître** - lorsque le nœud principal est redémarré, le Cache de Redis de Azure bascule sur le nœud du réplica et le promeut à maître. Au cours de ce basculement, il peut y avoir un court intervalle dans lequel les connexions peuvent échouer dans le cache.
-   **Esclave** - lorsque le nœud esclave est redémarré, il n’y a généralement aucun impact sur les clients de cache.
-   **Les deux maîtres et esclaves** - lorsque les deux nœuds du cache sont redémarrés, toutes les données sont perdues dans le cache et les connexions de l’échec du cache jusqu'à ce que le nœud principal est remis en ligne. Si vous avez configuré la [persistance des données](cache-how-to-premium-persistence.md), la sauvegarde la plus récente est restaurée lorsque le cache est remis en ligne. Notez que toutes les écritures de cache qui ont eu lieu après la dernière sauvegarde sont perdus.
-   **Un ou plusieurs nœuds d’un cache premium avec activation des clusters** - lorsque vous redémarrez l’ou les noeuds d’un cache premium avec activation des clusters, le comportement est le même que lorsque vous redémarrez un ou plusieurs nœuds d’un cache non mis en cluster.


>[AZURE.IMPORTANT] Redémarrage est uniquement disponible pour les caches de niveau Premium.

## <a name="reboot-faq"></a>Redémarrez le Forum aux questions

-   [Nœud qui dois-je redémarrer pour tester mon application ?](#which-node-should-i-reboot-to-test-my-application)
-   [Est-il possible de redémarrer le cache pour effacer les connexions client ?](#can-i-reboot-the-cache-to-clear-client-connections)
-   [Va perdre des données à partir de mon cache si faire un redémarrage ?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-   [Est-il possible de redémarrer mon cache à l’aide de PowerShell, CLI ou autres outils de gestion ?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-   [Quelle tarification niveaux permet à la fonctionnalité de redémarrage ?](#what-pricing-tiers-can-use-the-reboot-functionality)


### <a name="which-node-should-i-reboot-to-test-my-application"></a>Nœud qui dois-je redémarrer pour tester mon application ?

Pour tester la résistance de votre application contre les défaillances de nœud principal de votre cache, redémarrez le nœud **principal** . Pour tester la résistance de votre application contre les défaillances de nœud secondaire, redémarrez le nœud de **l’esclave** . Pour tester la résistance de votre application contre une panne totale du cache, redémarrez **les deux** nœuds.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Est-il possible de redémarrer le cache pour effacer les connexions client ?

Oui, si vous redémarrez le cache toutes les connexions clientes sont effacées. Cette option est utile dans le cas où toutes les connexions clientes sont utilisées, par exemple en raison d’une erreur logique ou d’un bogue dans l’application cliente. Chaque niveau de tarification a différentes [limites de connexion de client](cache-configure.md#default-redis-server-configuration) pour les différentes tailles, et une fois que ces limites sont atteintes, aucuns plus de connexions client ne sont acceptées. Redémarrer le cache fournit un moyen de désactiver toutes les connexions clientes.

>[AZURE.IMPORTANT] Si vos connexions client sont épuisées en raison d’une erreur logique ou d’un bogue dans votre code client, notez que StackExchange.Redis se reconnectera automatiquement une fois que le nœud Redis est remis en ligne. Si le problème sous-jacent n’est pas résolu, les connexions client continue à être utilisé.

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Va perdre des données à partir de mon cache si faire un redémarrage ?

Si vous redémarrez les nœuds à la fois le **maître** et **esclave** toutes les données dans le cache (ou ce partagé Si vous utilisez un cache premium avec activation des clusters) sont perdues. Si vous avez configuré la [persistance des données](cache-how-to-premium-persistence.md), la sauvegarde la plus récente est restaurée lorsque le cache est remis en ligne. Notez que toutes les écritures de cache ayant eu lieu après la sauvegarde sont perdues.

Si vous redémarrez simplement un des nœuds, les données ne sont pas généralement perdues, mais peut toujours être. Par exemple, si le nœud principal est redémarré et un cache d’écriture est en cours, les données dans le cache d’écriture est perdu. Un autre scénario de perte de données serait si vous redémarrez un nœud et que l’autre nœud intervient à tomber en panne en raison d’une défaillance en même temps. Pour plus d’informations sur les causes possibles de fuites de données, reportez-vous à la section [que sont devenus mes données dans Redis ?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md).

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Est-il possible de redémarrer mon cache à l’aide de PowerShell, CLI ou autres outils de gestion ?

Oui, les instructions voir [redémarrer un cache Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache)pour PowerShell.

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Quelle tarification niveaux permet à la fonctionnalité de redémarrage ?

Redémarrage est disponible uniquement dans la prime, niveau de tarification.

## <a name="schedule-updates"></a>Mises à jour de la planification

La lame de **mises à jour de la planification** vous permet de désigner une fenêtre de maintenance pour votre cache. Lorsque la fenêtre de maintenance est spécifiée, les mises à jour du serveur Redis sont effectuées au cours de cette fenêtre. Notez que la fenêtre de maintenance s’applique uniquement aux Redis des mises à jour du serveur et pas pour tout Azure mises à jour ou met à jour le système d’exploitation des ordinateurs virtuels qui hébergent le cache.

![Mises à jour de la planification](./media/cache-administration/redis-schedule-updates.png)

Pour spécifier une fenêtre de maintenance, vérifier les jours de votre choix et spécifier l’heure de début de fenêtre de maintenance pour chaque jour et cliquez sur **OK**. Notez que la durée des fenêtres de maintenance est l’heure UTC. 

>[AZURE.NOTE] La fenêtre de gestion par défaut pour les mises à jour est de 5 heures. Cette valeur n’est pas configurable à partir du portail Azure, mais vous pouvez le configurer à l’aide de PowerShell le `MaintenanceWindow` paramètres de l’applet de commande [New-AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx) . Pour plus d’informations, consultez [puis-je gérer la fonctionnalité mises à jour planifiées à l’aide de PowerShell, CLI ou autres outils de gestion ?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

## <a name="schedule-updates-faq"></a>Planifier des mises à jour de forum aux questions

-   [Lorsque les mises à jour produisent-elles si je n’utilise pas la fonctionnalité de planification de mises à jour ?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-   [Quel type de mises à jour sont effectuées au cours de la fenêtre de maintenance planifiée ?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-   [Can I gérés à l’aide de PowerShell, CLI ou autres outils de gestion de mises à jour planifiées ?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
-   [Quelle tarification niveaux peut utiliser la fonctionnalité de mises à jour de planification ?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Lorsque les mises à jour produisent-elles si je n’utilise pas la fonctionnalité de planification de mises à jour ?

Si vous ne spécifiez pas une fenêtre de maintenance, les mises à jour peuvent être effectuées à tout moment.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Quel type de mises à jour sont effectuées au cours de la fenêtre de maintenance planifiée ?

Redis uniquement les mises à jour sont effectuées au cours de la fenêtre de maintenance planifiée de serveur. La fenêtre de maintenance ne s’applique pas aux mises à jour Azure ou mises à jour pour le système d’exploitation de l’ordinateur virtuel.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Can I gérés à l’aide de PowerShell, CLI ou autres outils de gestion de mises à jour planifiées ?

Oui, vous pouvez gérer vos mises à jour planifiées à l’aide des applets de commande PowerShell suivante.

-   [Get-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763835.aspx)
-   [Nouvelle-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763834.aspx)
-   [Nouvelle-AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx)
-   [Supprimer-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763837.aspx)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Quelle tarification niveaux peut utiliser la fonctionnalité de mises à jour de planification ?

Mises à jour de la planification est uniquement disponible dans la prime, niveau de tarification.

## <a name="next-steps"></a>Étapes suivantes

-   Explorez plus de fonctionnalités de [niveau de prime de Cache de Redis d’Azure](cache-premium-tier-intro.md) .





