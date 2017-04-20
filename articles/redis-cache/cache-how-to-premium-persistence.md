<properties 
    pageTitle="Comment faire pour configurer la persistance des données pour un Cache de Redis prime Azure" 
    description="Découvrez comment configurer et gérer des vos instances de Cache Redis d’Azure niveau Premium de persistance des données" 
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
    ms.date="09/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Comment faire pour configurer la persistance des données pour un Cache de Redis prime Azure

Cache de Redis Azure a des offres de cache différents qui offrent une grande souplesse dans le choix de la taille du cache et de fonctionnalités, y compris la nouvelle couche de prime.

Le niveau de prime de Cache de Redis d’Azure inclut des fonctionnalités telles que le clustering, la persistance et la prise en charge de réseau virtuel. Cet article décrit comment configurer la persistance d’une instance de Cache Redis d’Azure premium.

Pour plus d’informations sur d’autres fonctionnalités de cache premium, consultez [Introduction à la couche de prime de Cache Redis Azure](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Quelle est la persistance des données ?
Persistance de redis vous permet rendre persistantes les données stockées dans Redis. Vous pouvez également prendre des instantanés et sauvegarder les données, vous pouvez charger dans le cas d’une défaillance matérielle. Il s’agit d’un énorme avantage niveau Basic ou Standard où toutes les données sont stockées dans la mémoire et il peut y avoir des pertes potentielles de données en cas de panne où les noeuds de Cache sont en panne. 

Cache de Redis Azure offre persistance Redis en utilisant le [modèle RDB](http://redis.io/topics/persistence), où les données sont stockées dans un compte de stockage Azure. Lors de la persistance est configuré, le Cache de Redis Azure persiste un instantané du cache Redis dans un format binaire de Redis sur le disque selon une fréquence de sauvegarde configurable. Si un événement catastrophique se produit qui désactive le principal et le réplica de cache, le cache est reconstruit à l’aide de l’instantané le plus récent.

Persistance peut être configuré à partir de la blade de **Nouveau Cache Redis** lors de la création du cache et sur la lame de **paramètres** pour les caches de prime existante.

## <a name="create-a-premium-cache"></a>Créer un cache de prime

Pour créer un cache et configurer la persistance, connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur **Nouveau**->**données + stockage**>**Redis le Cache**.

![Créer un Cache de Redis][redis-cache-new-cache-menu]

Pour configurer la persistance, tout d’abord sélectionner une caches de **prime** de la lame de **Choisir votre niveau de tarification** .

![Choisissez votre niveau de tarification][redis-cache-premium-pricing-tier]

Une fois une prime de niveau de prix est activée, cliquez sur **Redis de persistance**.

![Redis de persistance][redis-cache-persistence]

Les étapes décrites dans la section suivante décrivent comment configurer la persistance de la Redis sur votre nouveau cache de prime. Une fois que la persistance de Redis est configuré, cliquez sur **créer** pour créer votre nouveau cache premium avec persistance de Redis.

## <a name="configure-redis-persistence"></a>Configurer la persistance de la Redis

Redis persistance est configuré sur la lame **Redis la persistance des données** . Pour les caches de nouveau, cette blade est accessible pendant la création du cache, comme décrit dans la section précédente. Pour les caches existants, la lame **Redis la persistance des données** est accessible à partir de la blade de **paramètres** pour votre cache.

![Paramètres de redis][redis-cache-settings]

Pour activer la persistance de Redis, cliquez sur **activé** pour activer la sauvegarde RDB (Redis de base de données). Pour désactiver la persistance Redis sur un cache de prime précédemment activés, cliquez sur **désactivé**.

Pour configurer l’intervalle de sauvegarde, sélectionnez une **Fréquence de sauvegarde** dans la liste déroulante. Les choix incluent ** **15 Minutes**, 30 minutes**, **60 minutes**, **6 heures**, **12 heures**et **24 heures**. Cet intervalle commence à compter une fois que l’opération de sauvegarde précédente s’est terminée correctement et lorsqu’il s’écoule une nouvelle sauvegarde est lancée.

Cliquez sur pour sélectionner le compte de stockage à utiliser un **Compte de stockage** et choisissez la **clé primaire** ou **clé secondaire** à utiliser dans la liste déroulante **Clé de stockage** . Vous devez choisir un compte de stockage dans la même région, comme le cache, et un compte de **Stockage de la prime** est recommandé, car le stockage de prime a un débit plus élevé. 

>[AZURE.IMPORTANT] Si la clé de stockage de votre compte de persistance est régénérée, vous devez rechoose la clé de votre choix dans la liste déroulante **Clé de stockage** .

![Redis de persistance][redis-cache-persistence-selected]

Cliquez sur **OK** pour enregistrer la configuration de persistance.

Une fois que l’expiration de l’intervalle de fréquence de sauvegarde de la sauvegarde suivante (ou la première sauvegarde de nouveaux caches) est lancée.



## <a name="persistence-faq"></a>Forum aux questions de persistance

La liste suivante contient les réponses aux questions fréquemment posées sur la persistance de Cache Redis d’Azure.

-   [Puis-je activer la persistance sur un cache créé précédemment ?](#can-i-enable-persistence-on-a-previously-created-cache)
-   [Puis-je modifier la fréquence de sauvegarde après avoir créé le cache ?](#can-i-change-the-backup-frequency-after-i-create-the-cache)
-   [Pourquoi si j’ai une fréquence de sauvegarde de 60 minutes est supérieure à 60 minutes entre les sauvegardes ?](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
-   [Que se passe-t-il pour les anciennes sauvegardes lors d’une nouvelle sauvegarde ?](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
-   [Que se passe-t-il si j’ai mis à l’échelle d’une taille différente et restauration d’une sauvegarde effectuée avant l’opération de mise à l’échelle ?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Puis-je activer la persistance sur un cache créé précédemment ?

Oui, Redis persistance peut être configuré à la fois lors de la création du cache et les caches de prime existante.

### <a name="can-i-change-the-backup-frequency-after-i-create-the-cache"></a>Puis-je modifier la fréquence de sauvegarde après avoir créé le cache ?

Oui, vous pouvez modifier la fréquence de sauvegarde sur la lame **Redis la persistance des données** . Pour obtenir des instructions, reportez-vous à la section [configurer Redis de persistance](#configure-redis-persistence).

### <a name="why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Pourquoi si j’ai une fréquence de sauvegarde de 60 minutes est supérieure à 60 minutes entre les sauvegardes ?

L’intervalle de fréquence de sauvegarde ne démarre pas tant que le processus de sauvegarde précédent est terminée. Si la fréquence de sauvegarde est de 60 minutes et qu’il prend un processus de sauvegarde 15 minutes avec succès, la sauvegarde suivante ne démarre que 75 minutes après l’heure de début de la sauvegarde précédente.

### <a name="what-happens-to-the-old-backups-when-a-new-backup-is-made"></a>Que se passe-t-il pour les anciennes sauvegardes lors d’une nouvelle sauvegarde ?

Toutes les sauvegardes à l’exception la plus récente sont supprimés automatiquement. Cette suppression n’ait pas lieu immédiatement, mais les anciennes sauvegardes ne sont pas conservées indéfiniment.

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Que se passe-t-il si j’ai mis à l’échelle d’une taille différente et restauration d’une sauvegarde effectuée avant l’opération de mise à l’échelle ?

-   Si vous avez mis à l’échelle pour une plus grande taille, il n’existe aucun impact.
-   Si vous avez mis à l’échelle à une taille plus petite et que vous avez un personnalisé de [bases de données](cache-configure.md#databases) qui est supérieure à la [limitent de bases de données](cache-configure.md#databases) pour votre nouvelle taille, dans les bases de données n’est pas être restauré. Pour plus d’informations, reportez-vous à la section [est mes bases de données configuration affectées au cours de la mise à l’échelle ?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
-   Si vous avez mis à l’échelle à une taille plus petite, et il n’est pas suffisamment d’espace la plus petite taille pour contenir toutes les données à partir de la dernière sauvegarde, de clés de suppression au cours du processus de restauration, en général à l’aide de la stratégie d’éviction [allkeys-lru](http://redis.io/topics/lru-cache) .

## <a name="next-steps"></a>Étapes suivantes
Apprenez à utiliser les fonctionnalités de cache plus premium.

-   [Introduction à la couche de prime de Cache Redis Azure](cache-premium-tier-intro.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
