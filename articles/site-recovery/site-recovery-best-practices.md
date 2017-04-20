<properties
    pageTitle="Préparer pour le déploiement de la récupération de Site | Microsoft Azure"
    description="Cet article explique comment se préparer à déployer la réplication avec récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="prepare-for-azure-site-recovery-deployment"></a>Préparer pour le déploiement de la récupération de Site Azure

Lisez cet article pour une vue d’ensemble de haut niveau des exigences de déploiement pour chaque scénario de réplication prises en charge par le service de récupération de Site Azure. Après avoir lu les conditions générales pour chaque scénario, vous pouvez lier à des détails de déploiement spécifiques pour chaque déploiement.

Après avoir lu cette publication de l’article, des commentaires ou des questions au bas de l’article ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Les organisations ont besoin d’une stratégie BCDR qui détermine comment les applications, les charges de travail et les données restent en cours d’exécution et disponible pendant les temps d’arrêt planifiés et non planifiés et récupérer dès que possible dans des conditions normales de fonctionnement. Votre stratégie de BCDR doit conserver les données d’entreprise d’et s’assurer que les charges de travail restent disponibles en permanence en cas de reprise après sinistre. 

Récupération de site est un service Azure qui participe à votre stratégie de BCDR par l’orchestration de réplication de serveurs physiques des locaux et des ordinateurs virtuels vers le nuage (Azure) ou à un centre de données secondaire. Lorsque des pannes se produisent dans votre emplacement principal, vous basculent vers l’emplacement secondaire afin que les applications et charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’il est retourné à un fonctionnement normal. Pour en savoir plus [ce qu’est la récupération de Site ?](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>Sélectionnez votre modèle de déploiement

Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et l’utilisation des ressources : le modèle de gestionnaire de ressources Azure et le modèle de gestion de services classique. Azure dispose également de deux portails – [Azure portal classique](https://manage.windowsazure.com/) qui prend en charge le modèle de déploiement classique et le [portail Azure](https://ms.portal.azure.com/) avec prise en charge pour les deux modèles de déploiement.

Récupération de site est disponible dans le portail classique et le portail Azure. Dans le portail classique Azure vous pouvez prendre en charge de récupération de Site avec le modèle d’administration classique de services. Dans le portail Azure vous pouvez prendre en charge le modèle classique ou déploiements du Gestionnaire de ressources. [Pour en savoir plus](site-recovery-overview.md#site-recovery-in-the-azure-portal) sur le déploiement avec le portail Azure.

Lorsque vous choisissez une note de modèle de déploiement qui :

- Nous vous recommandons de qu'utiliser le [portail Azure](https://portal.azure.com) et le modèle du Gestionnaire de ressources lorsque cela est possible.
- Récupération de site fournit un plus simple et plus intuitive route expérience dans Azure portal.
- Utilisation du portail Azure, vous pouvez répliquer les machines classique et de stockage du Gestionnaire de ressources dans Azure. En outre, dans le portail Azure, vous pouvez utiliser des comptes de stockage LRS ou GRS.
- Le portail Azure combine les services de sauvegarde et de restauration de Site dans un coffre-fort de Services de récupération unique afin que vous pouvez définir et gérer les services BCDR à partir d’un emplacement unique.
- Les utilisateurs disposant d’un abonnement Azure mis en service avec le programme fournisseur de solutions de Cloud (CSP) peuvent désormais gérer les opérations de récupération de Site dans le portail Azure.
- Réplication des machines virtuelles de VMware ou des machines physiques vers Azure dans le portail Azure fournit un certain nombre de nouvelles fonctionnalités, y compris la prise en charge pour le stockage de la prime et la capacité à des disques spécifiques à exclure de la réplication.


## <a name="select-your-deployment-scenario"></a>Sélection de votre scénario de déploiement

**Déploiement** | **Détails** | **Azure portal** | **Portail classique** | **PowerShell**
---|---|---|---|---
**Machines virtuelles de VMware pour Azure** | Répliquer les machines virtuelles de VMware pour stockage Azure | Dans l’Azure portal ordinateurs virtuels peut basculer sur classique ou de stockage du Gestionnaire de ressources<br/><br/> Déploiement du [portail Azure](site-recovery-vmware-to-azure.md) fournit une expérience de déploiement rationalisé et un certain nombre d’avantages. | Dans le portail classique Azure, vous pouvez déployer le [modèle amélioré](site-recovery-vmware-to-azure-classic.md) et basculer vers un stockage Azure classique.<br/><br/> Il existe également un modèle hérité qui ne doit pas être utilisé pour les nouveaux déploiements. |  PowerShell n’est pas pris en charge actuellement.
**Machines virtuelles de Hyper-V vers Azure** | Hyper-V aux VM stockage Azure. Ordinateurs virtuels peuvent être sur des hôtes Hyper-V managées dans des nuages de System Center VMM, ou sans VMM. | Dans l’Azure portal ordinateurs virtuels peut basculer classique ou de stockage du Gestionnaire de ressources.<br/><br/> Le portail Azure offre une expérience de déploiement rationalisé. [En savoir plus](site-recovery-vmm-to-azure.md) sur la réplication des machines virtuelles de Hyper-V dans les nuages VMM. [En savoir plus](site-recovery-hyper-v-site-to-azure.md) sur la réplication des machines virtuelles de Hyper-V (sans VMM).| Dans le portail Azure classique vous pouvez basculer ordinateurs virtuels vers un stockage Azure classique | Déploiement de PowerShell est pris en charge.
**Serveurs physiques vers Azure** | Répliquer les serveurs Windows/Linux physiques de stockage Azure | Dans l’Azure les serveurs de portail peuvent basculer vers classique ou de stockage du Gestionnaire de ressources.<br/><br/> Déploiement du [portail Azure](site-recovery-vmware-to-azure.md) fournit une expérience de déploiement rationalisé et un certain nombre d’avantages. | Dans le portail classique Azure, vous pouvez déployer le [modèle amélioré](site-recovery-vmware-to-azure-classic.md) et basculer vers un stockage Azure classique.<br/><br/> Il existe également un modèle hérité qui ne doit pas être utilisé pour les nouveaux déploiements. | Déploiement de PowerShell n’est pas pris en charge actuellement.
**Serveurs VMware VM/physique vers le site secondaire* | Répliquer les machines virtuelles de VMware ou des serveurs physiques Windows/Linux sur un site secondaire. [En savoir plus et télécharger](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) le guide de l’utilisateur InMage Scout. | N’est pas disponible dans le portail Azure | Dans le portail classique vous allez télécharger InMage Scout d’un coffre de récupération de Site. | Déploiement de PowerShell n’est pas pris en charge actuellement
**Machines virtuelles de Hyper-V sur un site secondaire** | Répliquer les machines virtuelles de Hyper-V dans les nuages VMM à un nuage secondaire | Dans [Azure portal](site-recovery-vmm-to-vmm.md) , vous pouvez répliquer des VM Hyper-V dans les nuages VMM sur un site secondaire à l’aide de Hyper-V Replica uniquement. SAN n’est pas pris en charge actuellement. | Dans Azure portal classique vous pouvez répliquer les VM Hyper-V dans les nuages VMM sur un site secondaire à l’aide de [Hyper-V Replica](site-recovery-vmm-to-vmm-classic.md) ou [réplication SAN](site-recovery-vmm-san.md) | Déploiement de PowerShell est pris en charge.



## <a name="check-what-you-need-for-deployment"></a>Vérifiez que vous avez besoin pour le déploiement

### <a name="replicate-to-azure"></a>Répliquer vers Azure

**Exigence** | **Détails** 
---|---
**Compte Azure** | Vous devez disposer d’un compte [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification de la récupération de Site.
**Stockage Azure** | Les données répliquées sont stockées dans le stockage Azure et d’Azure VM sont créés lorsqu’un basculement se produit. Pour répliquer sur Azure, vous aurez besoin d’un [compte de stockage Azure](../storage/storage-introduction.md).<br/><br/>Si vous déployez restauration du Site portail classique, vous aurez besoin d’un ou plusieurs [comptes de stockage GRS standard](..storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> Si vous déployez dans Azure portal, vous pouvez utiliser stockage GRS ou LRS.<br/><br/> Si vous utilisez la réplication ordinateurs virtuels VMware ou les serveurs physiques dans le stockage Azure premium portail est pris en charge. Notez que si vous utilisez un compte de stockage premium vous devez également un compte de stockage standard pour stocker les journaux de réplication qui capturent les modifications en cours pour les données locales. [Stockage de prime](../storage/storage-premium-storage.md) est généralement utilisé pour les machines virtuelles qui ont besoin d’un performances d’e/s élevées et une faible latence pour les charges de travail intensives hôte d’e/s.<br/><br/> Si vous souhaitez utiliser un compte premium pour stocker des données répliquées, vous devez également un compte de stockage standard pour stocker les journaux de réplication qui capturent les modifications en cours pour les données locales.
**Réseau Azure** | Pour répliquer sur Azure, vous aurez besoin d’un réseau d’Azure qui se connectera à Azure VM lorsqu’elles sont créées après le basculement.<br/><br/> Si vous déployez dans le portail classique, vous utiliserez un réseau classique. Si vous déployez dans Azure portal, vous pouvez utiliser un classique ou un gestionnaire de ressources réseau.<br/><br/> Le réseau doit être dans la même région de la chambre forte.
**Mappage réseau (VMM pour Azure)** | Si vous utilisez la réplication à partir de VMM vers Azure, [mappage réseau](site-recovery-network-mapping.md) garantit que Azure VM sont connectés à des réseaux corrects après le basculement.<br/><br/> Pour configurer le mappage réseau, vous devrez configurer les réseaux de la machine virtuelle dans le portail VMM.
**Sur site** | **Ordinateurs virtuels VMware**: vous aurez besoin d’un ordinateur local exécutant les composants de récupération de Site, VMware vSphere hôtes/vCenter server et les ordinateurs virtuels que vous souhaitez répliquer. [Pour en savoir plus](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).<br/><br/> **Serveurs physiques**: Si vous utilisez la réplication des serveurs physiques, vous aurez besoin un ordinateurs locaux qui exécutent des composants de récupération de Site et serveurs physiques que vous souhaitez répliquer. [Pour en savoir plus](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Si vous souhaitez que soit [restauré](site-recovery-failback-azure-to-vmware.md) après le basculement vers Azure vous devez une infrastructure VMware à le faire.<br/><br/> **Ordinateurs virtuels Hyper-V**: Si vous souhaitez répliquer les machines virtuelles de Hyper-V dans les nuages VMM vous aurez besoin d’un serveur VMM et trouvent les hôtes Hyper-V sur les ordinateurs virtuels que vous souhaitez protéger. [Pour en savoir plus](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Si vous souhaitez répliquer les machines virtuelles de Hyper-V sans VMM, vous devez les hôtes Hyper-V sur lequel se trouvent les ordinateurs virtuels. [Pour en savoir plus](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites).
**Ordinateurs protégés** | Les machines protégées qui seront répliqués vers Azure doivent satisfaire aux [conditions préalables Azure](#azure-virtual-machine-requirements) décrits ci-dessous.


### <a name="replicate-to-a-secondary-site"></a>Répliquer sur un site secondaire

**Exigence** | **Détails** 
---|---
**Compte Azure** | Vous devez disposer d’un compte [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification de la récupération de Site.
**Sur site** | **Ordinateurs virtuels VMware**: du site principal vous aurez besoin d’un serveur de traitement pour la mise en cache, de compression et de cryptage des données de réplication avant de l’envoyer sur le site secondaire. Dans le site secondaire, vous allez installer un serveur de configuration pour gérer et surveiller le déploiement et un serveur cible principale. Nous vous recommandons également d’un serveur vContinuum pour faciliter la gestion. En outre, vous devez au moins les hôtes VMware vSphere et éventuellement un serveur vCenter. [Pour en savoir plus](site-recovery-vmware-to-vmware.md)<br/><br/> **Machines virtuelles de Hyper-V dans les nuages VMM**: vous aurez besoin de configurer des serveurs VMM et hôtes Hyper-V qui contient les ordinateurs virtuels que vous souhaitez répliquer. [Pour en savoir plus](site-recovery-vmm-to-vmm.md#on-premises-prerequisites).
**Mappage réseau (VMM à VMM)** | Si vous utilisez la réplication à partir de VMM à VMM, [mappage réseau](site-recovery-network-mapping.md) garantit que réplica VMs sont connectés aux réseaux appropriés après basculement sur incident et sont placés de façon optimale sur les hôtes Hyper-V. Pour configurer le mappage réseau, vous devrez configurer les réseaux de la machine virtuelle sur vos serveurs VMM.
**Mappage du stockage** | Si vous utilisez la réplication à partir de VMM à VMM vous pouvez éventuellement définir le [mappage du stockage](site-recovery-storage-mapping.md) pour spécifier la cible de stockage pour les machines virtuelles dupliquées. Mappage de stockage peut être définie pour la réplication à la fois Hyper-V Replica et SAN.<br/><br/> Mappage de stockage n’est pas actuellement pris en charge dans le portail Azure.


## <a name="verify-url-access"></a>Vérifier l’accès de l’URL

Assurez-vous que ces URL est accessibles à partir de serveurs.

**URL** | **VMM à VMM** | **VMM vers Azure** | **Hyper-V vers Azure** | **VMware pour Azure**
---|---|---|---|---
*. accesscontrol.windows.net | Autoriser | Autoriser | Autoriser | Autoriser
*. backup.windowsazure.com | Non requis | Autoriser | Autoriser | Autoriser
*. hypervrecoverymanager.windowsazure.com | Autoriser | Autoriser | Autoriser | Autoriser
*. store.core.windows.net | Autoriser | Autoriser | Autoriser | Autoriser
*. blob.core.windows.net | Non requis | Autoriser | Autoriser | Autoriser
https://www.msftncsi.com/ncsi.txt | Autoriser | Autoriser | Autoriser | Autoriser
https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | Non requis | Non requis | Non requis | Autoriser

## <a name="azure-virtual-machine-requirements"></a>Ordinateurs virtuels Azure

Vous pouvez déployer la récupération de Site pour répliquer des ordinateurs virtuels et les serveurs physiques exécutant un système d’exploitation pris en charge par Azure. Cela inclut la plupart des versions de Windows et Linux. Vous devez vous assurer que les ordinateurs virtuels que vous souhaitez protéger sont conformes aux exigences Azure sur site.


**Fonctionnalité** | **Configuration requise** | **Détails**
---|---|---
Hôte Hyper-V | Doit être en cours d’exécution Windows Server 2012 R2 | Vérification des conditions préalables échouent si le système d’exploitation pris en charge
Hyperviseur de VMware  | Systèmes d’exploitation pris en charge | [Configuration minimale requise](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Système d’exploitation invité | Hyper-V pour réplication Azure : récupération de Site prend en charge tous les systèmes d’exploitation [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Pour VMware et la réplication de serveur physique : vérifier les [conditions préalables](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) de Windows et Linux | Vérification des conditions requises va échouer si non pris en charge.
Architecture de système d’exploitation invité | 64 bits | Vérification des conditions requises va échouer si non pris en charge
Taille du disque système d’exploitation |  Jusqu'à 1 023 Go | Vérification des conditions requises va échouer si non pris en charge
Nombre de disques de système d’exploitation | 1 | Vérification des conditions requises va échouer si non pris en charge.
Nombre de disques de données | inférieur ou égal à 16 (valeur maximale est fonction de la taille de la machine virtuelle en cours de création. 16 = XL) | Vérification des conditions requises va échouer si non pris en charge
Taille de disque dur virtuel de disque de données | Jusqu'à 1023 Go | Vérification des conditions requises va échouer si non pris en charge
Cartes réseau | Plusieurs cartes sont pris en charge. |
Adresse IP statique | Prise en charge | Si l’ordinateur virtuel principal est à l’aide d’une adresse IP statique, vous pouvez spécifier l’adresse IP statique pour l’ordinateur virtuel qui sera créé dans Azure. Notez qu’une adresse IP statique pour une machine virtuelle de linux s’exécutant sur Hyper-v n’est pas pris en charge.
disque iSCSI | Non pris en charge | Vérification des conditions requises va échouer si non pris en charge
Disque dur virtuel partagé | Non pris en charge | Vérification des conditions requises va échouer si non pris en charge
Disque FC | Non pris en charge | Vérification des conditions requises va échouer si non pris en charge
Format de disque dur| DISQUE DUR VIRTUEL <br/><br/> VHDX | Bien que VHDX n’est pas actuellement pris en charge dans Azure, récupération de Site convertit automatiquement en VHDX disque dur virtuel lorsque vous basculer sur Azure. Lorsque vous ne parvenez pas à en local les machines virtuelles continuent d’utiliser le format VHDX.
BitLocker | Non pris en charge | BitLocker doit être désactivé avant de protéger un ordinateur virtuel.
Nom de la machine virtuelle| Entre 1 et 63 caractères. Limité aux lettres, des chiffres et des traits d’union. Doit commencer et se terminer par une lettre ou un chiffre | Mettre à jour la valeur dans les propriétés de la machine virtuelle dans la récupération de Site
Type de machine virtuelle | <p>Génération 1</p> <p>Génération 2 - Windows</p> |  Machine virtuelle de génération 2 avec le type de disque du système d’exploitation du disque de base qui inclut des volumes de données de 1 ou 2 avec le format de disque comme VHDX qui est inférieure à 300 Go est pris en charge. Machines virtuelles de Linux génération 2 ne sont pas pris en charge. [En savoir plus](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## <a name="optimizing-your-deployment"></a>Optimisation de votre déploiement

Utilisez les conseils suivants pour vous aider à optimiser et à faire évoluer votre déploiement.

- **Taille de volume de système d’exploitation**: lorsque vous répliquez un ordinateur virtuel vers Azure le volume du système d’exploitation doit être inférieure à 1 To. Si vous avez plusieurs volumes que cela vous pouvez déplacer manuellement les sur un autre disque avant de commencer le déploiement.
- **Taille de disque de données**: Si vous effectuez une réplication vers Azure peut avoir jusqu'à 32 disques de données sur une machine virtuelle, chacun avec un maximum de 1 To. Vous pouvez efficacement répliquer et basculer sur une machine virtuelle de ~ 32 To.
- **Limites de plan de restauration**: restauration du Site peut s’adapter à des milliers de machines virtuelles. Les plans de reconstitution sont conçus en tant que modèle pour les applications qui doivent basculer entre eux afin de nous limiter le nombre d’ordinateurs dans un plan de récupération à 50.
- **Limites de service Azure**: abonnement Azure Every est livré avec un ensemble de limites par défaut sur les cœurs, etc. de services en nuage. Nous vous conseillons d’exécuter un test de basculement pour valider la disponibilité des ressources dans votre abonnement. Vous pouvez modifier ces limites via la prise en charge Azure.
- **Planification de la capacité**: à propos de la [planification de la capacité](site-recovery-capacity-planner.md) de lecture pour la récupération de Site.
- **Bande passante de réplication**: Si vous êtes à court de bande passante de réplication Notez que :
    - **ExpressRoute**: récupération de Site fonctionne avec les optimiseurs Azure ExpressRoute et WAN comme Riverbed. [Pour en savoir plus](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sur ExpressRoute.
    - **Le trafic de réplication**: récupération de Site utilise effectue une réplication initiale active en utilisant uniquement les blocs de données et pas le disque dur virtuel entier. Seules les modifications sont répliquées au cours de la réplication en cours.
    - **Le trafic réseau**: vous pouvez contrôler le trafic de réseau utilisé pour la réplication par configuration de [QoS de Windows](https://technet.microsoft.com/library/hh967468.aspx) avec une stratégie basée sur l’adresse IP de destination et le port.  En outre, si vous utilisez la réplication pour la récupération de Site Azure à l’aide de l’agent de sauvegarde d’Azure vous pouvez configurer la régulation pour cet agent. [Pour en savoir plus](https://support.microsoft.com/kb/3056159).
- **RTO**: pour la mesure de l’objectif de temps de récupération (RTO) vous pouvez vous attendre à la récupération de Site nous vous recommandons de procéder à un basculement sur incident test et afficher les tâches de récupération de Site pour analyser combien de temps il permet d’effectuer les opérations. Si vous êtes le basculement vers Azure, le meilleur RTO nous recommandons que vous automatisez toutes les actions manuelles grâce à l’intégration avec les plans d’automatisation et de récupération Azure.
- **RPO**: récupération de Site prend en charge un objectif de point de récupération proche synchrone (RPO) lorsque vous répliquez vers Azure. Cela suppose une bande passante suffisante entre votre centre de données et les Azure.


##<a name="service-urls"></a>URL de service
Assurez-vous que ces URL est accessibles à partir du serveur


**URL** | **VMM à VMM** | **VMM vers Azure** | **Site de Hyper-V vers Azure** | **VMware pour Azure**
---|---|---|---|---
 \*. accesscontrol.windows.net | Accès requis  | Accès requis  | Accès requis  | Accès requis
 \*. backup.windowsazure.com |  | Accès requis  | Accès requis  | Accès requis
 \*. hypervrecoverymanager.windowsazure.com | Accès requis  | Accès requis  | Accès requis  | Accès requis
 \*. store.core.windows.net | Accès requis  | Accès requis  | Accès requis  | Accès requis
 \*. blob.core.windows.net |  | Accès requis  | Accès requis  | Accès requis
 https://www.msftncsi.com/ncsi.txt | Accès requis  | Accès requis  | Accès requis  | Accès requis
 https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | | | | Accès requis


## <a name="next-steps"></a>Étapes suivantes

Après la formation et de comparaison des exigences en matière de déploiement général, vous pouvez lire les conditions requises détaillées et démarrer le déploiement de chaque scénario.

- [Répliquer les machines virtuelles VMware vers Azure](site-recovery-vmware-to-azure-classic.md)
- [Répliquer les serveurs physiques vers Azure](site-recovery-vmware-to-azure-classic.md)
- [Répliquer nuages VMM serveur Hyper-V vers Azure](site-recovery-vmm-to-azure.md)
- [Répliquer les machines virtuelles de Hyper-V (sans VMM) pour Azure](site-recovery-hyper-v-site-to-azure.md)
- [Répliquer des ordinateurs virtuels Hyper-V sur un site secondaire](site-recovery-vmm-to-vmm.md)
- [Répliquer des ordinateurs virtuels Hyper-V sur un site secondaire avec SAN](site-recovery-vmm-san.md)
- [Répliquer des ordinateurs virtuels Hyper-V avec un seul serveur VMM](site-recovery-single-vmm.md)
