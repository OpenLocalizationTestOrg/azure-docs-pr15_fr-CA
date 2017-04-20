<properties 
    pageTitle="Introduction à la couche de prime de Cache Azure Redis | Microsoft Azure" 
    description="Apprenez à créer et gérer la persistance de Redis, Redis la gestion de clusters et prise en charge VNET pour vos instances de Cache Redis d’Azure niveau Premium" 
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

# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Introduction à la couche de prime de Cache Redis Azure
Cache de Redis Azure est un cache distribué, géré qui vous permet de créer des applications hautement évolutives et plus réactives en fournissant un accès très rapide à vos données. 

Le nouveau niveau de prime est une couche de prêt Enterprise, qui comprend toutes les fonctionnalités de la norme de couche et plus d’informations, telles que les meilleures performances des charges de travail plus importants, de reprise après sinistre, importation/exportation et une sécurité renforcée. Continuez la lecture pour en savoir plus sur les fonctionnalités supplémentaires de la couche de cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Meilleures performances par rapport à la couche Standard ou Basic
**De meilleures performances sur la norme de base ou niveau.** Caches dans le niveau de prime sont déployés sur le matériel qui possède des processeurs plus rapides et offre de meilleures performances par rapport à la base ou la couche Standard. Les Caches de niveau Premium ont un débit plus élevé et faible latence. 

**Débit pour le Cache de même taille est plus élevé dans la prime par rapport à la couche Standard.** Par exemple, le débit d’un P4 de Go de 53 cache de (Premium) est de 250 demandes par seconde que 150 K pour C6 (Standard).

Pour plus d’informations sur la taille, le débit et la bande passante avec des mémoires cache de prime, reportez-vous à la section [FAQ de Cache Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis la persistance des données
Le niveau de prime vous permet de faire persister les données du cache dans un compte de stockage Azure. Dans un cache Basique/Standard toutes les données sont stockées dans la mémoire. En cas d’infrastructure sous-jacente problèmes peuvent être des pertes potentielles de données. Nous recommandons d’utiliser la fonctionnalité de persistance des données de Redis dans le niveau de prime pour accroître la résilience contre la perte de données. Cache de Redis Azure offre RDB et UNEDE (prochainement) options de [persistance de Redis](http://redis.io/topics/persistence). 

Pour obtenir des instructions sur la configuration de persistance, consultez [comment configurer la persistance d’un Cache de Redis prime Azure](cache-how-to-premium-persistence.md).

##<a name="redis-cluster"></a>Redis le cluster
Si vous souhaitez créer plus de 53 Go des caches ou aux données de partagé sur plusieurs nœuds de Redis, vous pouvez utiliser Redis qui est disponible dans le niveau de prime de clustering. Chaque nœud se compose d’une paire de cache principal/réplicas gérée par Azure pour une haute disponibilité. 

**Redis mise en clusters vous donne le débit et l’échelle maximale.** Débit augmente de façon linéaire en augmentant le nombre de milieu des fragments (nœuds) dans le cluster. Par exemple. Si vous créez un cluster P4 de 10 milieu des fragments, puis le débit disponible est 250K * 10 = 2,5 millions de requêtes par seconde. Veuillez consulter le [Forum aux questions Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) pour plus de détails sur la taille, le débit et la bande passante avec des mémoires cache de prime.

Pour vous familiariser avec la gestion de clusters, consultez [comment configurer la mise en cluster pour un Cache de Redis prime Azure](cache-how-to-premium-clustering.md).

##<a name="enhanced-security-and-isolation"></a>Isolation et une sécurité renforcée

Caches créés dans la couche de base ou Standard sont accessibles sur l’internet public. Accès au Cache est restreint en fonction de la touche d’accès rapide. Avec le niveau de prime, vous pouvez encore vérifier que seuls les clients au sein d’un réseau spécifié peuvent accéder au Cache. Vous pouvez déployer des Redis le Cache dans un [Réseau virtuel d’Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Vous pouvez utiliser toutes les fonctionnalités de VNet, tels que des sous-réseaux, des stratégies de contrôle d’accès, et autres fonctionnalités pour davantage limitent l’accès à Redis.

Pour plus d’informations, consultez [comment configurer la prise en charge de réseau virtuel pour un Cache de Redis prime Azure](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importation/exportation

Importation/exportation est une opération de gestion de données de Cache Redis d’Azure qui vous permet d’importer des données dans le Cache de Redis Azure ou exporter des données à partir du Cache de Redis Azure par l’importation et l’exportation d’une capture instantanée de base de données de Cache Redis (RDB) à partir d’un cache de prime à un blob de page dans un compte de stockage Azure. Cela vous permet de migrer entre les différentes instances de Cache Redis d’Azure ou de remplir le cache avec les données avant de l’utiliser.

Importation permet de mettre les fichiers RDB compatibles Redis à partir de n’importe quel serveur de Redis en cours d’exécution dans un nuage ou l’environnement, y compris les Redis en cours d’exécution sur n’importe quel fournisseur de nuage tels que Amazon Web Services, Windows ou Linux. Importation de données est un moyen simple de créer un cache de données prédéfinies. Pendant le processus d’importation, Cache de Redis Azure charge les fichiers RDB du stockage Azure en mémoire et insère ensuite les clés dans le cache.

Exporter vous permet d’exporter les données stockées dans le Cache de Redis Azure pour Redis les fichiers RDB compatibles. Vous pouvez utiliser cette fonction pour déplacer des données d’une instance de Cache Redis d’Azure à un autre ou vers un autre serveur Redis. Pendant le processus d’exportation, un fichier temporaire est créé sur l’ordinateur virtuel qui héberge l’instance de serveur de Cache de Redis d’Azure, et le fichier est téléchargé vers le compte de stockage désigné. Une fois l’opération d’exportation terminée avec soit un état de réussite ou d’échec, le fichier temporaire est supprimé.

Pour plus d’informations, consultez [comment importer des données dans et exporter les données du Cache de Redis d’Azure](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Redémarrage

Le niveau de prime vous permet de redémarrer un ou plusieurs nœuds de votre cache à la demande. Cela vous permet de tester votre application pour la résilience en cas de défaillance. Vous pouvez redémarrer les nœuds suivants.

-   Nœud maître de votre cache
-   Noeud esclave de votre cache
-   Nœuds de maître et esclave de votre cache
-   Lorsque vous utilisez un cache premium avec le clustering, vous pouvez redémarrer le maître, esclave ou les deux nœuds pour chaque milieu des fragments dans le cache

Pour plus d’informations, reportez-vous à la section [FAQ de redémarrer](cache-administration.md#reboot-faq)et de [redémarrer](cache-administration.md#reboot) .

## <a name="schedule-updates"></a>Mises à jour de la planification

La fonctionnalité mises à jour planifiées vous permet de désigner une fenêtre de maintenance pour votre cache. Lorsque la fenêtre de maintenance est spécifiée, les mises à jour du serveur Redis sont effectuées au cours de cette fenêtre. Pour désigner une fenêtre de maintenance, sélectionnez les jours de votre choix, puis spécifiez la maintenance fenêtre Démarrer heure pour chaque jour. Notez que la durée des fenêtres de maintenance est l’heure UTC. 

Pour plus d’informations, reportez-vous à la section [Forum aux questions sur](cache-administration.md#schedule-updates-faq) [mises à jour de la planification](cache-administration.md#schedule-updates) et planification.

>[AZURE.NOTE] Redis uniquement les mises à jour sont effectuées au cours de la fenêtre de maintenance planifiée de serveur. La fenêtre de maintenance ne s’applique pas aux mises à jour Azure ou mises à jour pour le système d’exploitation de l’ordinateur virtuel.

## <a name="to-scale-to-the-premium-tier"></a>Mise à l’échelle vers le niveau de prime

Pour faire évoluer vers le niveau de prime, il suffit de choisir parmi les niveaux de la prime dans la lame de **changement de niveau de tarification** . Vous pouvez également mettre à l’échelle votre cache avec le niveau de prime à l’aide de PowerShell et CLI. Pour obtenir des instructions détaillées, voir [comment mettre en Cache de Redis échelle Azure](cache-how-to-scale.md) et [Comment faire pour automatiser une opération de mise à l’échelle](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Étapes suivantes

Créer un cache et explorez les nouvelles fonctionnalités de niveau premium.

-   [Comment faire pour configurer la persistance d’un Cache de Redis prime Azure](cache-how-to-premium-persistence.md)
-   [Comment faire pour configurer la prise en charge de réseau virtuel pour un Cache de Redis prime Azure](cache-how-to-premium-vnet.md)
-   [Comment faire pour configurer la mise en cluster pour un Cache de Redis prime Azure](cache-how-to-premium-clustering.md)
-   [Comment faire pour importer et exporter des données à partir du Cache de Redis Azure](cache-how-to-import-export-data.md)
-   [Comment administrer le Cache Redis d’Azure](cache-administration.md)
  

