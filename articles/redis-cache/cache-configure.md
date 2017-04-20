<properties 
    pageTitle="Comment faire pour configurer le Cache de Redis Azure | Microsoft Azure"
    description="Comprendre la configuration de Redis par défaut pour le Cache de Redis Azure et apprenez à configurer vos instances de Cache Redis d’Azure"
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
    ms.date="08/25/2016"
    ms.author="sdanie" />

# <a name="how-to-configure-azure-redis-cache"></a>Comment faire pour configurer le Cache de Redis Azure

Cette rubrique décrit comment consulter et mettre à jour la configuration pour vos instances de Cache Redis d’Azure et couvre la configuration par défaut du serveur Redis pour les instances de Cache Redis d’Azure.

>[AZURE.NOTE] Pour plus d’informations sur la configuration et l’utilisation des fonctionnalités de cache premium, consultez [comment configurer la persistance d’un Cache de Redis prime Azure](cache-how-to-premium-persistence.md), [Comment faire pour configurer la mise en cluster pour un Cache de Redis prime Azure](cache-how-to-premium-clustering.md)et [comment configurer la prise en charge de réseau virtuel pour un Cache de Redis prime Azure](cache-how-to-premium-vnet.md).

## <a name="configure-redis-cache-settings"></a>Configurer les paramètres de cache Redis

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Cache de Redis Azure fournit les paramètres suivants sur la lame de **paramètres** .

![Redis les paramètres du Cache](./media/cache-configure/redis-cache-settings.png)



-   [Support et dépannage](#support-amp-troubleshooting-settings)
-   [Paramètres généraux](#general-settings)
    -   [Propriétés](#properties)
    -   [Touches d’accès rapide](#access-keys)
    -   [Paramètres avancés](#advanced-settings)
    -   [Redis le Gestionnaire de Cache](#redis-cache-advisor)
-   [Paramètres d’échelle](#scale-settings)
    -   [Niveau de tarification](#pricing-tier)
    -   [Redis taille de cluster](#cluster-size)
-   [Paramètres de gestion de données](#data-management-settings)
    -   [Redis la persistance des données](#redis-data-persistence)
    -   [Importation/exportation](#importexport)
-   [Paramètres d’administration](#administration-settings)
    -   [Redémarrage](#reboot)
    -   [Mises à jour de la planification](#schedule-updates)
-   [Paramètres de diagnostic](#diagnostics-settings)
-   [Paramètres de réseau](#network-settings)
-   [Paramètres de gestion des ressources](#resource-management-settings)

## <a name="support--troubleshooting-settings"></a>Support et dépannage

Les paramètres dans la section **prise en charge + dépannage** vous fournissent des options pour la résolution des problèmes avec votre cache.

![Prise en charge + la résolution des problèmes](./media/cache-configure/redis-cache-support-troubleshooting.png)

Cliquez sur **diagnostiquer et résoudre les problèmes** à des problèmes courants et des stratégies permettant de les résoudre.

Cliquez sur **journal d’activité** pour afficher les actions effectuées sur le cache. Vous pouvez également utiliser le filtrage pour développer cette vue pour inclure d’autres ressources. Pour plus d’informations sur l’utilisation des journaux d’audit, reportez-vous à la section [affichage des événements et des journaux d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md) et [Auditer les opérations effectuées avec le Gestionnaire de ressources](../resource-group-audit.md). Pour plus d’informations sur la surveillance des événements de Cache Redis d’Azure, consultez [les alertes et les opérations](cache-how-to-monitor.md#operations-and-alerts).

**Santé des ressources** assure le suivi de vos ressources et vous indique s’il s’exécute comme prévu. Pour plus d’informations sur le service de santé ressource d’Azure, consultez [vue d’ensemble de la santé de ressource d’Azure](../resource-health/resource-health-overview.md).

>[AZURE.NOTE] Santé de la ressource est actuellement impossible d’établir un rapport sur l’état de santé Azure Redis d’instances de Cache hébergé dans un réseau virtuel. Pour plus d’informations, consultez [toutes les fonctionnalités de cache fonctionnent lors de l’hébergement d’un cache dans un VNET ?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

Cliquez sur **nouvelle demande d’assistance** pour ouvrir une demande de support pour votre cache.

## <a name="general-settings"></a>Paramètres généraux

Les paramètres dans la section **Général** vous permettent d’accéder et de configurer les paramètres suivants pour votre cache.

![Paramètres généraux](./media/cache-configure/redis-cache-general-settings.png)

-   [Propriétés](#properties)
-   [Touches d’accès rapide](#access-keys)
-   [Paramètres avancés](#advanced-settings)
-   [Redis le Gestionnaire de Cache](#redis-cache-advisor)

### <a name="properties"></a>Propriétés

Cliquez sur **Propriétés** pour afficher des informations sur votre cache, y compris les ports et le point de terminaison du cache.

![Redis des propriétés de Cache](./media/cache-configure/redis-cache-properties.png)

### <a name="access-keys"></a>Touches d’accès rapide

Cliquez sur **les touches d’accès** pour afficher ou régénérer les touches d’accès rapide pour votre cache. Ces clés sont utilisées ainsi que le nom d’hôte et les ports à partir de la blade de **Propriétés** par les clients se connectant à votre cache.

![Redis des touches d’accès Cache](./media/cache-configure/redis-cache-manage-keys.png)






### <a name="advanced-settings"></a>Paramètres avancés

Pour configurer les paramètres suivants sur la lame de **Paramètres avancés** .

-   [Ports d’accès](#access-ports)
-   [MaxMemory-stratégie et maxmemory-réservé](#maxmemory-policy-and-maxmemory-reserved)
-   [Notifications de Keyspace (paramètres avancées)](#keyspace-notifications-advanced-settings)


### <a name="access-ports"></a>Ports d’accès

Par défaut, l’accès non-SSL est désactivé pour les caches de nouveau. Pour activer le port non-SSL, cliquez sur **non** pour **Autoriser l’accès uniquement via SSL** sur la **lame de paramètres avancés** et cliquez sur **Enregistrer**.

![Ports d’accès Cache redis](./media/cache-configure/redis-cache-access-ports.png)

### <a name="maxmemory-policy-and-maxmemory-reserved"></a>MaxMemory-stratégie et maxmemory-réservé

Les paramètres **stratégie de Maxmemory** et **maxmemory réservés** sur la lame de **Paramètres avancés** configurent les stratégies de mémoire pour le cache. Le paramètre de **stratégie maxmemory** configure la stratégie d’éviction pour le cache et **réservés maxmemory** configure la mémoire réservée pour les processus non cache.

![Redis la stratégie de Cache Maxmemory](./media/cache-configure/redis-cache-maxmemory-policy.png)

**MaxMemory stratégie** vous permet de choisir parmi les stratégies d’expulsion suivant.

-   volatile-lru - il s’agit de la valeur par défaut.
-   AllKeys-lru
-   volatile aléatoire
-   AllKeys aléatoire
-   volatile-durée de vie
-   noeviction

Pour plus d’informations sur les stratégies de maxmemory, voir [stratégies d’expulsion](http://redis.io/topics/lru-cache#eviction-policies).

Le paramètre **réservé maxmemory** configure la quantité de mémoire en Mo est réservé aux opérations non cache telles que la réplication au cours du basculement. Il peut également être utilisé lorsque vous avez un taux de fragmentation élevé. Ce paramètre vous permet d’avoir une expérience de serveur de Redis plus cohérente lorsque varie en fonction de votre charge. Cette valeur doit être définie plus élevée pour les charges de travail qui sont en écriture lourds. Lorsque la mémoire est réservée pour des opérations telles qu’il n’est pas disponible pour le stockage des données en mémoire cache.

>[AZURE.IMPORTANT] Le paramètre **réservé maxmemory** est uniquement disponible pour Standard et Premium met en cache.

### <a name="keyspace-notifications-advanced-settings"></a>Notifications de Keyspace (paramètres avancées)

Redis keyspace notifications sont configurées sur la lame de **Paramètres avancés** . Notifications de Keyspace permettent aux clients de recevoir des notifications lorsque certains événements se produisent.

![Redis Cache des paramètres avancé](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] Notifications de Keyspace et le paramètre **Avertir-keyspace-événements** sont uniquement disponibles pour Standard et Premium met en cache.

Pour plus d’informations, consultez [Notifications de Keyspace Redis](http://redis.io/topics/notifications). Pour l’exemple de code, consultez le fichier [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) dans l’exemple [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis le Gestionnaire de Cache

La lame de **recommandations** affiche des recommandations pour votre cache. En fonctionnement normal, aucune recommandation ne s’affichent. 

![Recommandations](./media/cache-configure/redis-cache-no-recommendations.png)

Si des conditions se produisent pendant les opérations de votre cache de charge du serveur, la bande passante du réseau ou l’utilisation de mémoire haute, une alerte s’affiche sur la lame **Redis le Cache** .

![Recommandations](./media/cache-configure/redis-cache-recommendations-alert.png)

Vous trouverez des informations complémentaires sur la lame de **recommandations** .

![Recommandations](./media/cache-configure/redis-cache-recommendations.png)

Vous pouvez surveiller ces mesures sur les sections de [surveillance](cache-how-to-monitor.md#monitoring-charts) et les [graphiques de l’utilisation](cache-how-to-monitor.md#usage-charts) de la lame de **Redis de Cache** .

Chaque niveau de tarification a des limites différentes pour les connexions clientes, la mémoire et la bande passante. Si votre cache est proche de la capacité maximale de ces mesures sur une période prolongée, une recommandation est créée. Pour plus d’informations sur les mesures et les limites vérifiées par l’outil de **recommandations** , consultez le tableau suivant.

| Redis métrique de Cache      | Pour plus d’informations, consultez                                                  |
|-------------------------|---------------------------------------------------------------------------|
| Utilisation de la bande passante réseau | [Performances du cache - la bande passante disponible](cache-faq.md#cache-performance) |
| Clients connectés       | [Configuration du serveur par défaut Redis - maxclients](#maxclients)            |
| Charge du serveur             | [Graphiques d’utilisation - charge de serveur Redis](cache-how-to-monitor.md#usage-charts)  |
| Utilisation de la mémoire            | [Performances du cache - taille](cache-faq.md#cache-performance)                |

Pour mettre à niveau votre cache, cliquez sur **mettre à niveau maintenant** pour modifier le [niveau de prix](#pricing-tier) et mise à l’échelle votre cache. Pour plus d’informations sur le choix d’un niveau de tarification, consultez [offre de Cache de Redis et de la taille dois-je utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-settings"></a>Paramètres d’échelle

Les paramètres dans la section **échelle** vous permettent d’accéder et de configurer les paramètres suivants pour votre cache.

![Réseau](./media/cache-configure/redis-cache-scale.png)

-   [Niveau de tarification](#pricing-tier)
-   [Redis taille de cluster](#cluster-size)

### <a name="pricing-tier"></a>Niveau de tarification

Cliquez sur **niveau de tarification** pour afficher ou modifier le niveau de prix pour votre cache. Pour plus d’informations sur la mise à l’échelle, consultez [comment mettre en Cache de Redis échelle Azure](cache-how-to-scale.md).

![Redis de Cache de niveau de prix](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### <a name="redis-cluster-size"></a>Redis taille de Cluster

Cliquez sur la **Taille de Cluster Redis (aperçu)** pour modifier la taille de cluster pour une exécution cache premium avec activation des clusters.

>[AZURE.NOTE] Notez que lorsque le niveau de prime de Cache Redis Azure a été lancé pour la disponibilité générale, la fonction Redis la taille de Cluster est en cours d’aperçu.

![Redis taille de cluster](./media/cache-configure/redis-cache-redis-cluster-size.png)

Pour modifier la taille de cluster, utilisez le curseur ou tapez un nombre entre 1 et 10 dans la zone de texte **nombre de partagé** et cliquez sur **OK** pour enregistrer.

>[AZURE.IMPORTANT] Le clustering redis n’est disponible pour les caches de la prime. Pour plus d’informations, consultez [comment configurer la mise en cluster pour un Cache de Redis prime Azure](cache-how-to-premium-clustering.md).


## <a name="data-management-settings"></a>Paramètres de gestion de données

Les paramètres dans la section **gestion de données** vous permettent d’accéder et de configurer les paramètres suivants pour votre cache.

![Gestion des données](./media/cache-configure/redis-cache-data-management.png)

-   [Redis la persistance des données](#redis-data-persistence)
-   [Importation/exportation](#importexport)

### <a name="redis-data-persistence"></a>Redis la persistance des données

Cliquez sur **Redis la persistance des données** pour activer, désactiver ou configurer la persistance des données pour votre cache de prime.

![Redis la persistance des données](./media/cache-configure/redis-cache-persistence-settings.png)

Pour activer la persistance de Redis, cliquez sur **activé** pour activer la sauvegarde RDB (Redis de base de données). Pour désactiver la persistance de Redis, cliquez sur **désactivé**.

Pour configurer l’intervalle de sauvegarde, sélectionnez une **Fréquence de sauvegarde** dans la liste déroulante. Les choix incluent ** **15 Minutes**, 30 minutes**, **60 minutes**, **6 heures**, **12 heures**et **24 heures**. Cet intervalle commence à compter une fois que l’opération de sauvegarde précédente s’est terminée correctement et lorsqu’il s’écoule une nouvelle sauvegarde est lancée.

Cliquez sur pour sélectionner le compte de stockage à utiliser un **Compte de stockage** et choisissez la **clé primaire** ou **clé secondaire** à utiliser dans la liste déroulante **Clé de stockage** . Vous devez choisir un compte de stockage dans la même région, comme le cache, et un compte de **Stockage de la prime** est recommandé, car le stockage de prime a un débit plus élevé. Chaque fois que la clé de stockage de votre compte de persistance est régénérée, vous devez ré-choisir la clé souhaitée dans la liste déroulante **Clé de stockage** .

Cliquez sur **OK** pour enregistrer la configuration de persistance.

>[AZURE.IMPORTANT] Persistance des données redis est disponible uniquement pour les caches de prime. Pour plus d’informations, consultez [comment configurer la persistance d’un Cache de Redis prime Azure](cache-how-to-premium-persistence.md).

### <a name="importexport"></a>Importation/exportation

Importation/exportation est une opération de gestion de données de Cache Redis d’Azure qui vous permet d’importer des données dans le Cache de Redis Azure ou exporter des données à partir du Cache de Redis Azure par l’importation et l’exportation d’une capture instantanée de base de données de Cache Redis (RDB) à partir d’un cache de prime à un blob de page dans un compte de stockage Azure. Cela vous permet de migrer entre les différentes instances de Cache Redis d’Azure ou de remplir le cache avec les données avant de l’utiliser.

Importation permet de mettre les fichiers RDB compatibles Redis à partir de n’importe quel serveur de Redis en cours d’exécution dans un nuage ou l’environnement, y compris les Redis en cours d’exécution sur n’importe quel fournisseur de nuage tels que Amazon Web Services, Windows ou Linux. Importation de données est un moyen simple de créer un cache de données prédéfinies. Pendant le processus d’importation Cache Redis d’Azure charge les fichiers RDB du stockage Azure en mémoire et insère ensuite les clés dans le cache.

Exporter vous permet d’exporter les données stockées dans le Cache de Redis Azure pour Redis les fichiers RDB compatibles. Vous pouvez utiliser cette fonction pour déplacer des données d’une instance de Cache Redis d’Azure à un autre ou vers un autre serveur Redis. Pendant le processus d’exportation qu'un fichier temporaire est créé sur l’ordinateur virtuel qui héberge l’instance de serveur de Cache de Redis d’Azure, et le fichier est téléchargé vers le compte de stockage désigné. Une fois l’opération d’exportation terminée avec soit un état de réussite ou d’échec, le fichier temporaire est supprimé.

>[AZURE.IMPORTANT] Importation/exportation n’est disponible pour les caches de niveau Premium. Pour plus d’informations et des instructions, reportez-vous à la section [Importer et exporter des données dans le Cache de Redis Azure](cache-how-to-import-export-data.md).


## <a name="administration-settings"></a>Paramètres d’administration

Les paramètres dans la section **Administration** vous permettent d’effectuer les tâches administratives suivantes pour votre cache de prime. 

![Administration](./media/cache-configure/redis-cache-administration.png)

-   [Redémarrage](#reboot)
-   [Mises à jour de la planification](#schedule-updates)

>[AZURE.IMPORTANT] Les paramètres de cette section sont uniquement disponibles pour les caches de niveau Premium.

### <a name="reboot"></a>Redémarrage

La lame de **redémarrage** vous permet de redémarrer un ou plusieurs nœuds de votre cache. Cela vous permet de tester votre application pour la résilience en cas de panne.

![Redémarrage](./media/cache-configure/redis-cache-reboot.png)

Si vous avez un cache premium avec activation des clusters, vous pouvez sélectionner les milieu des fragments du cache pour redémarrer.

![Redémarrage](./media/cache-configure/redis-cache-reboot-cluster.png)

Pour redémarrer un ou plusieurs nœuds de votre cache, sélectionnez les nœuds souhaités, puis cliquez sur **redémarrer**. Si vous avez un cache premium avec activation des clusters, sélectionnez le shard(s) à redémarrer puis cliquez sur **redémarrer**. Après quelques minutes, le redémarrage des nœuds sélectionnés et sont de reconnecter quelques minutes plus tard.

>[AZURE.IMPORTANT] Redémarrage est uniquement disponible pour les caches de niveau Premium. Pour plus d’informations et des instructions, consultez [administration de Cache Redis d’Azure - redémarrer](cache-administration.md#reboot).

### <a name="schedule-updates"></a>Mises à jour de la planification

La lame de **mises à jour de la planification** vous permet de désigner une fenêtre de gestion des mises à jour Redis pour votre cache. 

>[AZURE.IMPORTANT] Notez que la fenêtre de maintenance s’applique uniquement aux Redis des mises à jour du serveur et pas pour tout Azure mises à jour ou met à jour le système d’exploitation des ordinateurs virtuels qui hébergent le cache.

![Mises à jour de la planification](./media/cache-configure/redis-schedule-updates.png)

Pour spécifier une fenêtre de maintenance, vérifier les jours de votre choix et spécifier l’heure de début de fenêtre de maintenance pour chaque jour et cliquez sur **OK**. Notez que la durée des fenêtres de maintenance est l’heure UTC. 

>[AZURE.IMPORTANT] Mises à jour de la planification est uniquement disponible pour les caches de niveau Premium. Pour plus d’informations et des instructions, consultez [administration de Cache Redis d’Azure - mises à jour de la planification](cache-administration.md#schedule-updates).

## <a name="diagnostics-settings"></a>Paramètres de diagnostic

La section **Diagnostics** vous permet de configurer des diagnostics pour votre Cache Redis.

![Tests de diagnostic](./media/cache-configure/redis-cache-diagnostics.png)

Pour [configurer le compte de stockage](cache-how-to-monitor.md#enable-cache-diagnostics) utilisé pour stocker des diagnostics de la mémoire cache, cliquez sur **Diagnostics** .

![Redis des Diagnostics de la mémoire Cache](./media/cache-configure/redis-cache-diagnostics-settings.png)

Pour [Afficher les mesures](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts) de votre cache et les **règles d’alerte** pour [configurer les règles d’alerte](cache-how-to-monitor.md#operations-and-alerts), cliquez sur **Redis des mesures** .

Pour plus d’informations sur les diagnostics de la mémoire Cache Redis d’Azure, voir [Comment faire pour surveiller le Cache Redis d’Azure](cache-how-to-monitor.md).


## <a name="network-settings"></a>Paramètres de réseau

Les paramètres dans la section **réseau** vous permettent d’accéder et de configurer les paramètres suivants pour votre cache.

![Réseau](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] Paramètres de réseau virtuel ne sont disponibles que pour les caches de prime qui ont été configurés avec prise en charge VNET lors de la création du cache. Pour plus d’informations sur la création d’un cache premium avec VNET prennent en charge et mise à jour ses paramètres, consultez [comment configurer Virtual réseau prise en charge pour un Cache de Redis prime Azure](cache-how-to-premium-vnet.md).

## <a name="resource-management-settings"></a>Paramètres de gestion des ressources

![Gestion des ressources](./media/cache-configure/redis-cache-resource-management.png)

La section **balises** vous permet d’organiser vos ressources. Pour plus d’informations, reportez-vous à la section [en utilisant des balises pour organiser vos ressources Azure](../resource-group-using-tags.md).

La section **verrouille** vous permet de verrouiller un abonnement, le groupe de ressources ou ressource pour empêcher d’autres utilisateurs de votre organisation accidentellement supprimer ou modifier des ressources critiques. Pour plus d’informations, consultez [ressources de verrouillage avec le Gestionnaire de ressources Azure](../resource-group-lock-resources.md).

La section **utilisateurs** prend en charge pour le contrôle d’accès basé sur les rôles (RBAC) dans le portail Azure pour aider les entreprises à répondre à leurs besoins de gestion de l’accès simple et précise. Pour plus d’informations, consultez [contrôle d’accès basé sur les rôles dans le portail Azure](../active-directory/role-based-access-control-configure.md).

Cliquez sur **Exporter le modèle** afin de créer et d’exporter un modèle de vos ressources déployées pour les déploiements futurs. Pour plus d’informations sur l’utilisation des modèles, voir [déployer des ressources avec le Gestionnaire de ressources Azure modèles](../resource-group-template-deploy.md).

## <a name="default-redis-server-configuration"></a>Configuration du serveur par défaut Redis

Les nouvelles instances de Cache Redis d’Azure sont configurés avec les valeurs de configuration par défaut Redis suivants.

>[AZURE.NOTE] Les paramètres de cette section ne peut pas être modifiés à l’aide de la `StackExchange.Redis.IServer.ConfigSet` méthode. Si cette méthode est appelée avec une des commandes de cette section, une exception semblable à la suivante est levée :  
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Des valeurs configurables, telles que **max-mémoire-policy**, sont configurables via les outils de gestion portail ou de la ligne de commande Azure Azure CLI ou PowerShell.

|Paramètre|Valeur par défaut|Description|
|---|---|---|
|bases de données|16|Le nombre de bases de données par défaut est de 16, mais vous pouvez configurer un nombre différent en fonction du niveau de prix. <sup>1</sup> la base de données par défaut est la base de données 0, vous pouvez sélectionner une autre sur une base par connexion à l’aide `connection.GetDatabase(dbid)` où dbid est un nombre compris entre `0` et `databases - 1`.|
|MaxClients|Dépend de la tarification de niveau<sup>2</sup>|Ceci est le nombre maximal de clients connectés autorisé en même temps. Une fois la limite atteinte Redis fermera toutes les connexions de nouvelles envoi d’une erreur « nombre maximal de clients atteint ».|
|MaxMemory-stratégie|lru-volatile|MaxMemory stratégie est le paramètre pour la Redis sélectionne les éléments à supprimer lorsque maxmemory (la taille du cache offre choisie lors de la création du cache) est atteinte. Avec mémoire Cache Azure Redis le paramètre par défaut est volatile lru, ce qui supprime les clés avec une expiration définie à l’aide d’un algorithme LRU. Ce paramètre peut être configuré dans le portail Azure. Pour plus d’informations, consultez [stratégie Maxmemory et réservés maxmemory](#maxmemory-policy-and-maxmemory-reserved).|
|MaxMemory-exemples|3|LRU et les algorithmes de durée de vie minimales ne sont pas des algorithmes précis mais rapprocher des algorithmes (pour économiser de la mémoire), afin de pouvoir ainsi que la taille de l’échantillon pour vérifier. Par exemple pour défaut Redis vérifie trois clés et choisir celle qui a été moins récemment utilisé.|
|limite de temps LUA|5 000|Max les temps d’exécution d’un script Lua en millisecondes. Si la durée d’exécution maximale est atteinte Redis enregistre un script est toujours en exécution une fois que le nombre maximal autorisé de temps et commence à répondre à des requêtes avec une erreur.|
|limite d’événements LUA|500|Il s’agit de la taille maximale de la file d’attente des événements de script.|
|client-sortie-tampon-limit normalclient-sortie--limite de tampon pubsub|0 0 Mo 032 60 de 8 Mo|Les limites de mémoire tampon de sortie de client permet de forcer la déconnexion des clients qui ne sont pas lecture de données à partir du serveur suffisamment rapide pour une raison quelconque (une raison courante est qu’un client Pub/Sub ne peut pas consommer les messages aussi rapides que l’éditeur peut produire les). Pour plus d’informations, consultez [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<a name="databases"></a>
<sup>1</sup> La limite de `databases` est différent pour chaque Cache Azure Redis au niveau de prix et peut être définie lors de la création du cache. Si aucune `databases` paramètre est spécifié lors de la création du cache, la valeur par défaut est 16.

-   Base et Standard
    -   C0 (250 Mo) mémoire cache - jusqu'à 16 bases de données
    -   C1 (1 Go) mémoire cache - jusqu'à 16 bases de données
    -   C2 (2,5 Go) mémoire cache - jusqu'à 16 bases de données
    -   C3 (6 Go) cache - jusqu'à 16 bases de données
    -   C4 (13 Go) mémoire cache - jusqu'à 32 bases de données
    -   C5 (26 Go) mémoire cache - jusqu'à 48 bases de données
    -   C6 (53 Go) mémoire cache - jusqu'à 64 bases de données
-   Caches de prime
    -   P1 (6 Go à 60 Go) - jusqu'à 16 bases de données
    -   P2 (13 à 130 Go) - jusqu'à 32 bases de données
    -   P3 (26 à 260 Go) - jusqu'à 48 bases de données
    -   P4 (53 à 530 Go) - jusqu'à 64 bases de données
    -   Tous les caches de prime avec cluster Redis activé - Redis cluster prend uniquement en charge l’utilisation de la base de données 0 afin que la `databases` limite de n’importe quel cache premium avec cluster Redis activé est effectivement 1 et la commande [Select](http://redis.io/commands/select) n’est pas autorisée. Pour plus d’informations, consultez [dois-je apporter des modifications à mon application cliente pour utiliser le clustering ?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)


>[AZURE.NOTE] Le `databases` paramètre peut être configuré uniquement lors de la création du cache et uniquement à l’aide de PowerShell, CLI ou autres clients de gestion. Pour obtenir un exemple de configuration de `databases` lors de la création du cache à l’aide de PowerShell, consultez [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).


<a name="maxclients"></a>
<sup>2</sup> `maxclients` est différent pour chaque Cache Azure Redis au niveau de prix.

-   Base et Standard
    -   C0 (250 Mo) de mémoire cache - jusqu'à 256 connexions
    -   C1 (1 Go) mémoire cache - jusqu'à 1 000 connexions
    -   C2 les cache (2,5 Go) - jusqu'à 2 000 connexions
    -   C3 (6 Go) cache - jusqu'à 5 000 connexions
    -   C4 (13 Go) mémoire cache - jusqu'à 10 000 connexions
    -   C5 (26 Go) mémoire cache - jusqu'à 15 000 connexions
    -   C6 (53 Go) mémoire cache - jusqu'à 20 000 connexions
-   Caches de prime
    -   P1 (6 Go à 60 Go) - jusqu'à 7 500 connexions
    -   P2 (13 à 130 Go) - jusqu'à 15 000 connexions
    -   P3 (26 à 260 Go) - jusqu'à 30 000 connexions
    -   P4 (53 à 530 Go) - jusqu'à 40 000 connexions

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis les commandes non prises en charge dans le Cache de Redis Azure

>[AZURE.IMPORTANT] Étant donné que la configuration et la gestion d’instances de Cache Redis d’Azure est géré par Microsoft, les commandes suivantes sont désactivées. Si vous tentez d’appeler vous recevrez un message d’erreur semblable à `"(error) ERR unknown command"`.
>
>-  BGREWRITEAOF
>-  BGSAVE
>-  CONFIG
>-  DÉBOGAGE
>-  EFFECTUER LA MIGRATION
>-  ENREGISTRER
>-  ARRÊT
>-  SLAVEOF
>-  CLUSTER - écriture sont désactivées, mais en lecture seule de Cluster ont des commandes de Cluster.

Pour plus d’informations sur les commandes Redis, voir [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Redis la console

Vous pouvez en toute sécurité exécuter des commandes pour vos instances de Cache Redis d’Azure à l’aide de la **Console de Redis**, qui est disponible pour le Standard et les caches de la prime.

>[AZURE.IMPORTANT] La Console Redis ne fonctionne pas avec VNET, la gestion de clusters et les bases de données autres que 0. 
>
>-  [VNET](cache-how-to-premium-vnet.md) - lorsque votre cache fait partie d’un VNET, seuls les clients dans la VNET peuvent accéder au cache. Étant donné que la Console Redis utilise le client de redis-cli.exe hébergé sur des ordinateurs virtuels qui ne font pas partie de votre VNET, il ne peut pas se connecter à votre cache.
>-  [Gestion de clusters](cache-how-to-premium-clustering.md) - Redis de la Console utilise le client de redis-cli.exe qui ne prend en charge la gestion de clusters pour le moment. L’utilitaire redis-cli dans la branche [instable](http://redis.io/download) du référentiel Redis à GitHub implémente la prise en charge de base lors du démarrage avec le `-c` basculer. Pour plus d’informations reportez-vous à [jouer avec le cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) sur [http://redis.io](http://redis.io) dans l' [Redis du didacticiel de cluster](http://redis.io/topics/cluster-tutorial).
>-  La Console Redis permet une connexion à la base de données 0 chaque fois que vous soumettez une commande. Vous ne pouvez pas utiliser le `SELECT` pour sélectionner une autre base de données, car la base de données est réinitialisée à 0 à chaque commande. Pour plus d’informations sur l’exécution des commandes de Redis, y compris la modification à une autre base de données, consultez [Comment puis-je exécuter des commandes de Redis ?](cache-faq.md#how-can-i-run-redis-commands)

Pour accéder à la Console Redis, cliquez sur **la Console** du serveur lame **Redis le Cache** .

![Redis la console](./media/cache-configure/redis-console-menu.png)

Pour exécuter des commandes sur votre instance de cache, tapez simplement la commande souhaitée dans la console.

![Redis la console](./media/cache-configure/redis-console.png)

Pour obtenir une liste de commandes Redis sont désactivées pour le Cache de Redis d’Azure, reportez-vous à la section [Redis des commandes non prises en charge dans le Cache de Redis Azure](#redis-commands-not-supported-in-azure-redis-cache) précédente. Pour plus d’informations sur les commandes Redis, voir [http://redis.io/commands](http://redis.io/commands). 

## <a name="move-your-cache-to-a-new-subscription"></a>Déplacer le cache vers un nouvel abonnement

Vous pouvez déplacer le cache vers un nouvel abonnement en cliquant sur **déplacer**.

![Déplacer le Cache de le Redis](./media/cache-configure/redis-cache-move.png)

Pour plus d’informations sur le déplacement des ressources à partir d’un groupe de ressources vers un autre et d’un abonnement à un autre, voir [déplacer des ressources vers le nouveau groupe de ressources ou d’abonnement](../resource-group-move-resources.md).

## <a name="next-steps"></a>Étapes suivantes
-   Pour plus d’informations sur l’utilisation des commandes de Redis, consultez [Comment puis-je exécuter des commandes de Redis ?](cache-faq.md#how-can-i-run-redis-commands).
