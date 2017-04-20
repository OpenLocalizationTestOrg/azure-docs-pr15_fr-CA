<properties
    pageTitle="Abonnement Microsoft Azure et des limites de Service, des Quotas et des contraintes"
    description="Fournit une liste des commune abonnement Azure et limites de service, des quotas et des contraintes. Cela inclut des informations sur la façon d’augmenter les limites ainsi que les valeurs maximales."
    services=""
    documentationCenter=""
    authors="rothja"
    manager="jeffreyg"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="btardif"/>

# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Abonnement Azure et des limites de service, des quotas et des contraintes

## <a name="overview"></a>Vue d’ensemble

Ce document spécifie certaines limites généralement Microsoft Azure. Ceci ne couvre pas tous les services Azure actuellement. Au fil du temps, ces limites seront développés et mis à jour pour couvrir plus de la plate-forme.

Consultez [Vue d’ensemble de la tarification Azure](https://azure.microsoft.com/pricing/) pour en savoir plus sur la tarification Azure. Vous pouvez estimer vos coûts à l’aide de la [Calculatrice de tarification](https://azure.microsoft.com/pricing/calculator/) ou en visitant la page de détails de tarification pour un service (par exemple, [Machines virtuelles de Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)).

> [AZURE.NOTE] Si vous souhaitez augmenter la limite au-dessus de la **Limite de la valeur par défaut**, vous pouvez [Ouvrir une demande de support client en ligne sans frais](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Les limites ne peut pas être augmentés au-dessus de la valeur **Limite maximale** dans les tableaux ci-dessous. S’il n’existe aucune colonne **Limite maximale** , la ressource spécifiée n’a pas de limites réglables.

## <a name="limits-and-the-azure-resource-manager"></a>Limites et le Gestionnaire de ressources Azure

Il est désormais possible de combiner plusieurs ressources dans Azure à un seul groupe de ressource Azure. Lorsque vous utilisez des groupes de ressources, limites qui étaient une fois globales gérés localement au niveau régional avec le Gestionnaire de ressources Azure. Pour plus d’informations sur les groupes de ressources Azure, consultez [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md).

Dans les limites ci-dessous, une nouvelle table a été ajoutée pour refléter toutes les différences dans les limites lors de l’utilisation du Gestionnaire de ressources Azure. Par exemple, il existe une table de **La limite d’abonnements** et d’une table de **La limite d’abonnements - Azure le Gestionnaire de ressources** . Lorsqu’une limite s’applique à ces deux scénarios, il apparaît uniquement dans la première table. Sauf indication contraire, les limites sont globales dans toutes les régions.

> [AZURE.NOTE] Il est important de souligner que les quotas pour les ressources dans les groupes de ressources Azure sont accessibles par votre abonnement par région et non pas par abonnement, comme les quotas de gestion de service. Nous allons utiliser les quotas de base à titre d’exemple. Si vous avez besoin demander une augmentation des quotas avec prise en charge de cœurs, vous devez décider combien cœurs que vous souhaitez utiliser dans les régions, puis effectuez une demande de quotas de base Azure ressources groupe spécifique pour les montants et les régions que vous le souhaitez. Par conséquent, si vous avez besoin utiliser les 30 cœurs en Europe de l’ouest pour exécuter votre application ; Vous devez demander spécifiquement des 30 noyaux en Europe de l’ouest. Mais vous n’aurez pas un quota de base augmenter dans n’importe quelle autre région : Europe de l’ouest seulement disposeront du quota de la 30-core.
<!-- -->
En conséquence, vous pouvez trouver utile d’envisager de décider ce que vos quotas de groupe de ressources Azure doivent être pour votre charge de travail dans une région d’un et demander ce montant dans chaque région dans laquelle vous envisagez de déploiement. Reportez-vous à la [résolution des problèmes de déploiement](./resource-manager-common-deployment-errors.md) pour obtenir de l’aide découvrir vos quotas en cours pour des régions spécifiques.

## <a name="service-specific-limits"></a>Limites de service spécifique

- [Active Directory](#active-directory-limits)
- [Gestion de l’API](#api-management-limits)
- [Service d’application](#app-service-limits)
- [Passerelle d’application](#application-gateway-limits)
- [Aperçu de l’application](#application-insights-limits)
- [Automation](#automation-limits)
- [Cache d’Azure Redis](#azure-redis-cache-limits)
- [RemoteApp Azure](#azure-remoteapp-limits)
- [Sauvegarde](#backup-limits)
- [Traitement par lots](#batch-limits)
- [Services BizTalk](#biztalk-services-limits)
- [PAIE CANADA-FICHIER](#cdn-limits)
- [Services en nuage](#cloud-services-limits)
- [Fabrique de données](#data-factory-limits)
- [Données lac Analytique](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [Concentrateurs d’événement](#event-hubs-limits)
- [Concentrateur de IoT](#iot-hub-limits)
- [Chambre forte de clé](#key-vault-limits)
- [Services de support](#media-services-limits)
- [Engagement mobile](#mobile-engagement-limits)
- [Services mobiles](#mobile-services-limits)
- [Surveillance](#monitoring-limits)
- [Plusieurs facteurs d’authentification](#multi-factor-authentication)
- [Mise en réseau](#networking-limits)
- [Service de notification de concentrateur](#notification-hub-service-limits)
- [Conseils opérationnels](#operational-insights-limits)
- [Groupe de ressources](#resource-group-limits)
- [Planificateur](#scheduler-limits)
- [Recherche](#search-limits)
- [Bus des services](#service-bus-limits)
- [Récupération de site](#site-recovery-limits)
- [Base de données SQL](#sql-database-limits)
- [Stockage](#storage-limits)
- [StorSimple système](#storsimple-system-limits)
- [Analytique de flux](#stream-analytics-limits)
- [Abonnement](#subscription-limits)
- [Traffic Manager](#traffic-manager-limits)
- [Ordinateurs virtuels](#virtual-machines-limits)
- [Jeux d’échelle de Machine virtuelle](#virtual-machine-scale-sets-limits)


### <a name="subscription-limits"></a>Limites de l’abonnement
#### <a name="subscription-limits"></a>Limites de l’abonnement
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limites de l’abonnement - Azure le Gestionnaire de ressources

Les limites suivantes s’appliquent lorsque vous utilisez le Gestionnaire de ressources Azure et Azure les groupes de ressources. Limites qui n’ont pas changé avec le Gestionnaire de ressources Azure ne sont pas répertoriés ci-dessous. Reportez-vous au tableau ci-dessus pour ces limites.

Pour plus d’informations sur la gestion des limites sur les demandes du Gestionnaire de ressources, consultez le [Gestionnaire de ressources de la limitation de requêtes](resource-manager-request-limits.md).

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### <a name="resource-group-limits"></a>Limites des groupes de ressources

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limites des Machines virtuelles
#### <a name="virtual-machine-limits"></a>Limites de la Machine virtuelle
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites des Machines virtuelles - Azure le Gestionnaire de ressources

Les limites suivantes s’appliquent lorsque vous utilisez le Gestionnaire de ressources Azure et Azure les groupes de ressources. Limites qui n’ont pas changé avec le Gestionnaire de ressources Azure ne sont pas répertoriés ci-dessous. Reportez-vous au tableau ci-dessus pour ces limites.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Machine virtuelle échelle définit les limites

[AZURE.INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>Limites de mise en réseau

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limites de mise en réseau
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Limite de la passerelle d’application

[AZURE.INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="traffic-manager-limits"></a>Limites du Gestionnaire de trafic

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limites DNS

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Limites de stockage

Pour plus de détails sur les limites de compte de stockage, reportez-vous à la section [des cibles de performances et de l’évolutivité du stockage Azure](../articles/storage/storage-scalability-targets.md).

#### <a name="storage-service-limits"></a>Limites du Service de stockage

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### <a name="virtual-machine-disk-limits"></a>Limites de disque de Machine virtuelle

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Pour plus d’informations, reportez-vous à la section [formats de machine virtuelle](../articles/virtual-machines/virtual-machines-linux-sizes.md) .

**Comptes de stockage standard**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Comptes de stockage Premium**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>Limites de fournisseur de ressources de stockage

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### <a name="cloud-services-limits"></a>Limites de Services cloud

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### <a name="app-service-limits"></a>Limites du Service de l’application
Les limites de Service d’application suivantes incluent des limites pour les applications Web, applications Mobile, applications d’API et logique d’applications.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limites du planificateur

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limites du traitement par lots

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###<a name="biztalk-services-limits"></a>Limites des Services BizTalk
Le tableau suivant indique les limites pour les Services de Biztalk Azure.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### <a name="documentdb-limits"></a>DocumentDB limites

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

Quotas sont répertoriés avec un astérisque (*) [peut être ajusté en contactant le support Azure](./documentdb/documentdb-increase-limits.md).

### <a name="mobile-engagement-limits"></a>Limites d’Engagement de Mobile

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### <a name="search-limits"></a>Limites de la recherche

Niveaux de tarification détermine les capacités et les limites de votre service de recherche. Les niveaux comprennent :

- *Libre* service de mutualisée, partagé avec d’autres abonnés Azure, conçus pour l’évaluation et les projets de développement de petite taille.
- *Base* fournit les ressources informatiques dédiés pour les charges de travail de production à une échelle inférieure, avec jusqu'à trois répliques pour les charges de travail de requête hautement disponible.
- *Standard (S1, S2, S3, S3 haute densité)* est de plus grandes charges de travail. Plusieurs niveaux existent dans la couche standard afin que vous puissiez choisir une configuration de ressources pour des scénarios spécifiques.

**Limites par abonnement**

[AZURE.INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limites par le service de recherche**

[AZURE.INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Pour des informations plus précises sur les autres limites, notamment la taille du document, de requêtes par seconde, les clés, les demandes et les réponses, consultez [limites de Service de recherche d’Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limites de Media Services

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limites CDN

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limites de Services mobiles

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>Surveillance des limites

[AZURE.INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Service de notification de concentrateur limite

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limites de concentrateurs d’événement

[AZURE.INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limites de Bus des services

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Limites de concentrateur de IoT

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Limite de la fabrique de données

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limites de données Analytique du lac
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="stream-analytics-limits"></a>Limite de flux Analytique
[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory limite

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### <a name="azure-remoteapp-limits"></a>Limites de RemoteApp Azure

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>Système de StorSimple limite

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### <a name="operational-insights-limits"></a>Limites des perspectives opérationnelles

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limites de sauvegarde

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Limite de récupération de site

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Limites de perspectives d’applications

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limites de la gestion de l’API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Limite de Cache de Redis Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limites de stockage en chambre forte de clé

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Plusieurs facteurs d’authentification
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limites de l’Automation
[AZURE.INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Limites de la base de données SQL

Pour les limites de la base de données SQL, consultez [Les limites des ressources de base de données SQL](sql-database/sql-database-resource-limits.md).

## <a name="see-also"></a>Voir aussi

[Présentation des augmentations et limites Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Machine virtuelle et les tailles de Service Cloud pour Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Tailles des Services en nuage](cloud-services/cloud-services-sizes-specs.md)
