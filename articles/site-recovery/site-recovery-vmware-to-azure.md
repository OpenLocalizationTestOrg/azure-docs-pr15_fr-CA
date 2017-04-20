<properties
    pageTitle="Répliquer les machines virtuelles VMware et des serveurs physiques vers Azure avec récupération de Site Azure dans le portail Azure | Microsoft Azure"
    description="Décrit comment déployer la récupération de Site Azure pour orchestrer la réplication, le basculement et la récupération des locaux des machines virtuelles VMware et des serveurs physiques Linux/Windows Azure à l’aide du portail Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>Répliquer les machines virtuelles VMware et les machines physiques vers Azure avec récupération de Site Azure via le portail Azure

> [AZURE.SELECTOR]
- [Azure Portal](site-recovery-vmware-to-azure.md)
- [Azure classique](site-recovery-vmware-to-azure-classic.md)
- [Azure classique (hérité)](site-recovery-vmware-to-azure-classic-legacy.md)

Bienvenue dans restauration du Site Azure ! Utilisez cet article si vous souhaitez répliquer les machines virtuelles VMware sur site ou les serveurs physiques Windows/Linux vers Azure à l’aide de la récupération de Site Azure dans le portail Azure.

> [AZURE.NOTE] Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et l’utilisation des ressources : Azure Resource Manager (ARM) et classique. Azure dispose également de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement.

Restauration du site portail Azure fournit un certain nombre de nouvelles fonctionnalités :

- Les services Azure sauvegarde et récupération de Site Azure sont combinés en un seul coffre-fort de Services de récupération afin que vous pouvez configurer et gérer la continuité d’activité et de reprise après sinistre (BCDR) à partir d’un emplacement unique. Dans le tableau de bord unifiée, vous pouvez surveiller et gérer les opérations sur vos sites locaux et le nuage public Azure.
- Les utilisateurs disposant d’un abonnement Azure mis en service avec le programme fournisseur de solutions de Cloud (CSP) peuvent désormais gérer les opérations de récupération de Site dans le portail Azure.
- Restauration du site portail Azure peut répliquer des machines à des comptes de stockage ARM. Lors du basculement, récupération de Site crée ARM sur des ordinateurs virtuels dans Azure.
- Récupération de site continue de prendre en charge les comptes de stockage classique de la réplication. Lors du basculement, récupération de Site crée des machines virtuelles à l’aide du modèle classique.

Après avoir lu cette publication de l’article de tous les commentaires en bas dans les commentaires de Disqus. Poser des questions techniques sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Les organisations ont besoin d’une stratégie BCDR qui détermine comment les applications, les charges de travail et les données restent en cours d’exécution et disponible pendant les temps d’arrêt planifiés et non planifiés et récupérer dès que possible dans des conditions normales de fonctionnement. Votre stratégie de BCDR doit conserver les données d’entreprise d’et s’assurer que les charges de travail restent disponibles en permanence en cas de reprise après sinistre.

Récupération de site est un service Azure qui participe à votre stratégie de BCDR par l’orchestration de réplication de serveurs physiques des locaux et des ordinateurs virtuels vers le nuage (Azure) ou à un centre de données secondaire. Lorsque des pannes se produisent dans votre emplacement principal, vous basculent vers l’emplacement secondaire afin que les applications et charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’il est retourné à un fonctionnement normal. Pour en savoir plus [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md)

Cet article fournit toutes les informations dont vous avez besoin de répliquer sur site ordinateurs virtuels VMware et des serveurs physiques Linux/Windows Azure. Il inclut une présentation de l’architecture, des informations et des étapes de déploiement pour la configuration d’Azure, des serveurs sur site, les paramètres de réplication et planification de la capacité de planification. Une fois que vous avez configuré l’infrastructure que vous pouvez activer la réplication sur les ordinateurs que vous voulez protéger et vérifier que le basculement fonctionne.

## <a name="business-advantages"></a>Avantages commerciaux

- Récupération de site fournit une protection hors site pour les grosses charges de travail et les applications qui s’exécutent sur des ordinateurs virtuels VMware et des serveurs physiques.
- Le portail de Services de récupération fournit un emplacement unique pour configurer, gérer et contrôler la réplication, de basculement et de restauration.
- Récupération de site peut découvrir automatiquement les ordinateurs virtuels VMware en ajouté à vSphere hôtes.
- Vous pouvez facilement exécuter des basculements de votre infrastructure sur site d’Azure et de retour arrière (restauration) d’Azure pour serveurs VMware VM dans votre site local.
- Vous pouvez activer le multi-VM et créer des groupes de réplication afin que les charges de travail applicatives hiérarchisé sur plusieurs machines répliquer, en même temps. Tous les ordinateurs d’un groupe de réplication ont des points de récupération sur incident cohérents et cohérente à l’échelle de l’application lorsqu’ils basculent. Pour le basculement, vous pouvez rassembler plusieurs machines dans les plans de récupération afin que les charges de travail applicatives hiérarchisé basculent ensemble.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

### <a name="windows64-bit-only"></a>Windows (64 bits uniquement)
- Windows Server 2008 R2 SP1 +
- Windows Server 2012
- Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (64 bits uniquement)
- Red Hat Enterprise Linux 6.7, 7.1, 7.2
- CentOS 6.5, 6.6, 6.7, 7.0, 7.1, 7.2
- Oracle Enterprise Linux 6.4, 6.5 exécute le noyau compatible de Red Hat ou incassable entreprise noyau version 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Architecture de scénario

Voici les composants du scénario :

- **Serveur de configuration**: un ordinateur local qui coordonne les communications et gère les processus de réplication et de récupération de données. Sur cet ordinateur, vous allez exécuter un fichier d’installation unique pour installer le serveur de configuration et de ces composants supplémentaires :
    - **Serveur de traitement**: sert de passerelle de réplication. Il reçoit des données de réplication à partir des machines d’origine protégée, il optimise avec la mise en cache, de compression et de cryptage et envoie au stockage Azure. Il gère l’installation par diffusion du service de mobilité pour les ordinateurs protégés et vous effectue la découverte automatique des ordinateurs virtuels VMware. Le serveur de traitement par défaut est installé sur le serveur de configuration. Vous pouvez déployer des serveurs de traitement autonome supplémentaire pour faire évoluer votre déploiement.
    - **Maître du serveur cible**: gère les données de réplication au cours de la restauration à partir d’Azure.

- **Service de la mobilité**: ce composant est déployé sur chaque ordinateur (VMware VM ou serveur physique) que vous souhaitez répliquer sur Azure. Il capture les écritures de données sur la machine et les transmet au serveur de traitement.
- **Azure**: vous n’avez pas besoin de créer les ordinateurs virtuels d’Azure pour gérer la réplication et la reprise sur Azure.  Vous avez besoin d’un abonnement Azure, un compte de stockage Azure pour stocker les données répliquées et un réseau virtuel Azure afin que les ordinateurs virtuels d’Azure sont connectés à un réseau après le basculement. Le compte de stockage et réseau doivent être dans la même région, comme le coffre-fort de Services de récupération.
- **Retour arrière**: lorsque vous êtes prêt à échouer d’Azure sur votre site local après un basculement, vous devez créer une VM Azure comme un serveur de traitement temporaire. Vous pouvez le supprimer une fois la restauration terminée. De la restauration, vous devez également une connexion de réseau privé virtuel (ou Azure ExpressRoute) entre votre site local et le réseau d’Azure dans lequel se trouvent vos ordinateurs virtuels d’Azure. Si le trafic de retour arrière est important, vous devrez également définir un masque dédié serveur cible ordinateur local. Le serveur cible principal par défaut en cours d’exécution sur le serveur de configuration peut servir pour le trafic plus clair.


Le graphique illustre l’interaction de ces composants.

![architecture](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figure 1 : VMware/physiques vers Azure**

## <a name="azure-prerequisites"></a>Conditions préalables Azure

Voici ce que vous devrez dans Azure déployer ce scénario.

**Condition préalable** | **Détails**
--- | ---
**Compte Azure**| Vous devez disposer d’un compte [Microsoft Azure](http://azure.microsoft.com/) . Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification de la récupération de Site.
**Stockage Azure** | Les données répliquées sont stockées dans le stockage Azure et d’Azure VM sont créés lorsqu’un basculement se produit. <br/><br/>Pour stocker des données vous aurez besoin d’un compte de stockage standard ou premium dans la même région que le coffre-fort de Services de récupération.<br/><br/>Vous pouvez utiliser un compte de stockage LRS ou GRS. Nous vous recommandons de GRS afin que les données sont résilientes si une panne régionale se produit, ou si la région principale ne peut pas être récupérée. [En savoir plus](../storage/storage-redundancy.md).<br/><br/> [Stockage de prime](../storage/storage-premium-storage.md) est généralement utilisé pour les machines virtuelles qui ont besoin d’un performances d’e/s élevées et une faible latence pour les charges de travail intensives hôte d’e/s.<br/><br/> Si vous souhaitez utiliser un compte premium pour stocker des données répliquées, vous devez également un compte de stockage standard pour stocker les journaux de réplication qui capturent les modifications en cours pour les données locales.<br/><br/> Notez que les comptes de stockage créés dans le portail Azure ne peut être déplacés entre des groupes de ressources. Également une protection pour les comptes de stockage premium dans l’Inde centrale et du Sud de l’Inde est actuellement pas pris en charge.<br/><br/> [En savoir plus sur](../storage/storage-introduction.md) Stockage Azure.
**Réseau Azure** | Vous aurez besoin d’un réseau virtuel Azure qui se connectera à Azure VM lors du basculement. Le réseau virtuel Azure doit être dans la même région, comme le coffre-fort de Services de récupération.
**Restauration à partir d’Azure** | Vous aurez besoin d’un fichier temporaire de serveur défini comme un Azure VM. Vous pouvez le créer lorsque vous êtes prêt à basculer et à supprimer après que l’échec précédent est terminée.<br/><br/> Pour faire échouer précédent vous devrez une connexion VPN (ou Azure ExpressRoute) à partir du réseau Azure le site local.

## <a name="configuration-server--scale-out-process-prerequisites"></a>Serveur de configuration / évoluer les conditions préalables du processus

Vous allez configurer un ordinateur local comme serveur de configuration / cloisonnée de serveur de traitement.

**Condition préalable** | **Détails**
--- | ---
**Serveur de configuration**| Vous avez besoin d’un local ordinateur physique ou virtuel Windows Server 2012 R2 en cours d’exécution. Tous les composants de la récupération de Site local sont installés sur cet ordinateur.<br/><br/>Pour la réplication VMware VM, nous vous recommandons de que déployer le serveur comme un VM VMware hautement disponible. Si vous utilisez la réplication de machines physiques l’ordinateur peut être un serveur physique.<br/><br/> Retour arrière vers le site local d’Azure est toujours aux machines virtuelles de VMware que si vous n’avez pas sur les ordinateurs virtuels ou des serveurs physiques. Si vous ne déployez pas le serveur de configuration sous la forme d’une VM VMware, vous devez configurer un serveur cible principal distinct comme une VM VMware à recevoir du trafic de retour arrière.<br/><br/>Si le serveur est un VM VMware, le type de carte réseau doit être VMXNET3. Si vous utilisez un autre type de carte réseau, vous devez installer une [mise à jour de VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) sur le serveur vSphere 5.5.<br/><br/>Le serveur doit disposer d’une adresse IP statique.<br/><br/>Le serveur ne doit pas être un contrôleur de domaine.<br/><br/>Le nom d’hôte du serveur doit être de 15 caractères ou moins.<br/><br/>Le système d’exploitation doit être uniquement en anglais.<br/><br/> Vous devez installer VMware vSphere PowerCLI 6.0. sur le serveur de configuration.<br/><br/>Le serveur de configuration nécessite un accès internet. Accès sortant est nécessaire comme suit :<br/><br/>Accès temporaire sur HTTP 80 lors de l’installation des composants de récupération de Site (à télécharger MySQL)<br/><br/>Accès sortant continue sur HTTPS 443 pour la gestion de la réplication<br/><br/>Accès sortant continue sur HTTPS 9443 le trafic de réplication (ce port peut être modifié)<br/><br/>Le serveur doit également l’accès aux URL suivantes afin qu’il peut se connecter pour Azure : *. hypervrecoverymanager.windowsazure.com ; *. AccessControl.Windows.NET ; *. backup.windowsazure.com ; *. BLOB.Core.Windows.NET ; *. store.core.windows.net<br/><br/>Si vous avez des règles de pare-feu basé sur l’adresse IP sur le serveur, vérifiez que les règles autorisent les communications vers Azure. Vous devez autoriser les [Plages d’IP Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le protocole HTTPS (443).<br/><br/>Autoriser les plages d’adresses IP pour la région Azure de votre abonnement et ouest des États-Unis.<br/><br/>Autoriser cette URL pour le téléchargement de MySQL :.http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## <a name="vmware-vcentervsphere-host-prerequisites"></a>Conditions préalables de vCenter/vSphere hôte VMware

**Condition préalable** | **Détails**
--- | ---
**vSphere**| Vous avez besoin d’un ou plusieurs les hyperviseurs VMware vSphere.<br/><br/>Hyperviseurs doivent être en cours d’exécution vSphere version 6.0, 5.5 ou 5.1 avec les dernières mises à jour.<br/><br/>Il est recommandé que les serveurs vCenter vSphere hôtes se trouvent dans le même réseau que le serveur de traitement (il s’agira le réseau sur lequel se trouve le serveur de configuration, sauf si vous avez configuré un serveur dédié).
**vCenter** | Nous vous recommandons de déployer un serveur de vCenter VMware pour gérer vos hôtes vSphere. Il doit être exécuté vCenter version 6.0 ou 5.5 avec les dernières mises à jour.<br/><br/>Notez que la récupération de Site ne prend pas en charge le nouveau vCenter et vSphere 6.0 fonctionnalités croisent telle que vCenter vMotion et DRS de stockage volumes virtuels. Prise en charge de récupération de site est limité aux fonctionnalités qui étaient également disponibles dans la version 5.5.


## <a name="protected-machine-prerequisites"></a>Conditions préalables d’ordinateur protégé

**Condition préalable** | **Détails**
--- | ---
**(VMware VM) sur site** | Machines virtuelles de VMware à protéger doit avoir les outils VMware installé et en cours d’exécution.<br/><br/> Ordinateurs à protéger doivent être conformes avec [conditions préalables Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) pour la création d’ordinateurs virtuels d’Azure.<br/><br/>Capacité de chaque disque sur les ordinateurs protégés ne doit pas être supérieure à 1 023 go. Une machine virtuelle peut avoir jusqu'à 64 disques (donc jusqu'à 64 To). <br/><br/>Minimum de 2 Go d’espace disponible sur le lecteur d’installation pour l’installation des composants.<br/><br/>Protection des machines virtuelles avec disques chiffrés n’est pas pris en charge.<br/><br/>Partagé invité disque clusters ne sont pas pris en charge.<br/><br/>**20004 de port** doit être ouvert sur le pare-feu local de la machine virtuelle protégée, si vous souhaitez activer **la cohérence des applications**.<br/><br/>Les ordinateurs qui ont unifié Extensible Firmware Interface (UEFI) / Extensible Firmware Interface(EFI) démarrage n’est pas pris en charge.<br/><br/>Noms de machine doivent contenir entre 1 et 63 des caractères (lettres, chiffres et traits d’union). Le nom doit commencer par une lettre ou un nombre et se terminer par une lettre ou un chiffre. Après avoir activé la réplication pour un ordinateur, vous pouvez modifier le nom d’Azure.<br/><br/>Si la source de machine virtuelle a l’association de cartes réseau, il est converti à une seule carte réseau après le basculement vers Azure.<br/><br/>Si les ordinateurs virtuels protégés ont un disque iSCSI récupération de Site convertit ensuite le disque iSCSI VM protégé dans un fichier de disque dur virtuel lorsque l’ordinateur virtuel bascule vers Azure. Si la cible iSCSI peut être atteint par la machine virtuelle d’Azure puis il s’y connecter et voir essentiellement de deux disques, le disque dur sur l’ordinateur virtuel d’Azure et le disque iSCSI de source. Dans ce cas, vous devez vous déconnecter de la cible iSCSI qui s’affiche sur l’ordinateur virtuel d’Azure.
**Les ordinateurs Windows (physique ou VMware)** | L’ordinateur doit exécuter un système d’exploitation 64 bits pris en charge : Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2 avec au moins SP1.<br/><br/> Le système d’exploitation doit être installé sur le lecteur C:\. Le disque du système d’exploitation doit être un disque de base Windows et non dynamique. Le disque de données peut être dynamique.<br/><br/>Récupération de site prend en charge les ordinateurs virtuels avec un disque RDM. Lors du retour arrière, récupération de Site va réutiliser le disque RDM si le disque de machine virtuelle et RDM source d’origine est disponible. Si elles ne sont pas disponibles, lors du retour arrière récupération de Site crée un nouveau fichier VMDK pour chaque disque.
**Ordinateurs Linux** (phyical ou VMware)|  Vous aurez besoin d’un système d’exploitation 64 bits pris en charge : 6.7,7.1,7.2 de Red Hat Enterprise Linux ; CentOS 6.5, 6.6,6.7,7.0,7.1,7.2 ; Oracle Enterprise Linux 6.4, 6.5 exécute le noyau compatible de Red Hat ou incassable entreprise noyau version 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>les fichiers/etc/hosts sur les ordinateurs protégés doivent contenir des entrées qui mappent le nom d’hôte local pour les adresses IP associées à toutes les cartes réseau.<br/><br/>Si vous souhaitez vous connecter à un ordinateur virtuel Azure exécutant Linux après le basculement à l’aide d’un SSH client (ssh), assurez-vous que le service de Shell sécurisé sur l’ordinateur protégé est défini pour démarrer automatiquement au démarrage du système, et que les règles de pare-feu autorisent un ssh connexion à ce dernier.<br/><br/>Nom d’hôte, les points de montage, les noms de périphériques et chemins d’accès de système de Linux et les noms de fichier (par exemple, / etc /, / usr) doivent exister en anglais uniquement.<br/><br/>Protection ne peut être activée que pour les machines Linux avec le stockage suivant : fichier système (EXT3, ETX4, ReiserFS, XFS) ; Multipath-périphérique logiciel Mappeur (multipath)) ; Le Gestionnaire de volumes : (LVM2). Serveurs physiques avec le stockage HP CCISS contrôleur ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur le Service Pack 3 de SUSE Linux Enterprise Server 11.<br/><br/>Récupération de site prend en charge les ordinateurs virtuels avec un disque RDM.  Lors du retour arrière pour Linux, récupération de Site ne réutiliser le disque RDM. Au lieu de cela, il crée un nouveau fichier VMDK pour chaque disque RDM correspondant.<br/><br/>Assurez-vous que vous définissez le paramètre disk.enableUUID=true dans les paramètres de configuration de la machine virtuelle dans VMware. Créez l’entrée si elle n’existe pas. Il est nécessaire de fournir un UUID cohérent pour le VMDK afin qu’il monte correctement. Ajout de ce paramètre garantit également que les modifications delta uniquement sont transférées en local lors de la restauration et pas une réplication complète.
**Service de la mobilité** |  **Windows**: vous allez pour diffuser automatiquement le service de la mobilité pour les ordinateurs virtuels exécutant Windows, vous devez fournir un compte d’administrateur (administrateur local sur l’ordinateur Windows) afin que le serveur peut effectuer une installation par diffusion.<br/><br/>**Linux**: pour diffuser automatiquement le service de la mobilité pour les ordinateurs virtuels exécutant Linux, vous devez créer un compte qui peut être utilisé par le serveur de traitement pour effectuer une installation par diffusion.<br/><br/> Par défaut, tous les disques sur un ordinateur sont répliquées. Pour [exclure un disque de la réplication](#exclude-disks-from-replication), le service de la mobilité doit être installé manuellement sur l’ordinateur avant d’activer la réplication.<br/>

## <a name="prepare-for-deployment"></a>Préparer pour le déploiement

Pour vous préparer pour le déploiement, vous devez :

1. [Configurer un réseau Azure](#set-up-an-azure-network) dans lequel Azure VM se trouve lorsqu’ils vous lancés après le basculement. En outre, de la restauration vous devez configurer une connexion VPN (ou Azure ExpressRoute) à partir du réseau Azure votre site local.
2. [Configurer un compte de stockage Azure](#set-up-an-azure-storage-account) pour les données répliquées.
3. [Préparer un compte](#prepare-an-account-for-automatic-discovery) sur le serveur de vCenter ou vSphere héberge afin que la récupération de Site peut détecter automatiquement les machines virtuelles VMware qui sont ajoutés.
4. [Préparer le serveur de configuration](#prepare-the-configuration-server) pour vous assurer qu’il peut accéder aux URL requis et installer vSphere PowerCLI 6.0.


### <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

- Le réseau doit être dans la même région Azure que celui dans lequel vous allez déployer le coffre-fort de Services de récupération.
- Selon le modèle de ressource que vous souhaitez utiliser pour basculé Azure VM, vous définirez le réseau Azure dans [mode ARM](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Pour l’échec d’Azure à votre site de VMware sur site, vous devez une connexion VPN (ou une connexion Azure ExpressRoute) à partir du réseau d’Azure dans lequel les ordinateurs virtuels de Azure répliquée se trouvent, au réseau local dans lequel se trouve le serveur de configuration.
- [Obtenir des informations sur](../vpn-gateway/vpn-gateway-site-to-site-create.md) le déploiement de prise en charge des modèles pour les connexions VPN site à site et comment [configurer une connexion](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network).
- Vous pouvez également configurer les [ExpressRoute d’Azure](../expressroute/expressroute-introduction.md). [En savoir plus](../expressroute/expressroute-howto-vnet-portal-classic.md) sur la configuration d’un réseau d’Azure avec ExpressRoute.

> [AZURE.NOTE] [Migration de réseaux](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les réseaux utilisés pour le déploiement de récupération de Site.

### <a name="set-up-an-azure-storage-account"></a>Configurer un compte de stockage Azure

- Vous aurez besoin d’une norme ou un compte de stockage Azure premium pour contenir les données répliquées vers Azure. Le compte doit être dans la même région, comme le coffre-fort de Services de récupération. Selon le modèle de ressource que vous souhaitez utiliser pour basculé Azure VM, vous allez configurer un compte en [mode ARM](../storage/storage-create-storage-account.md) ou [en mode classique](../storage/storage-create-storage-account-classic-portal.md).
- Si vous utilisez un compte premium pour les données répliquées, que vous devez créer un compte standard supplémentaire pour stocker les journaux de réplication qui capturent les modifications en cours pour les données locales.  

> [AZURE.NOTE] [Migration des comptes de stockage](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

### <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique

Le serveur de processus de récupération de Site peut découvrir automatiquement les ordinateurs virtuels VMware vSphere hôtes ou sur un serveur de vCenter qui gère les hôtes. Pour exécuter automatique découverte d’informations d’identification de récupération de Site qui peut accéder au serveur VMware. Ce n’est pas pertinent si vous utilisez la réplication uniquement des ordinateurs physiques.

1. Pour utiliser un compte dédié pour la découverte automatique créer un rôle (par exemple, Azure_Site_Recovery) au niveau du vCenter avec les [autorisations requises](#vmware-account-permissions).
2. Créer un nouvel utilisateur sur le serveur hôte ou vCenter de vSphere et attribuer le rôle à l’utilisateur. Plus tard vous allez informer récupération de Site ces informations d’identification afin qu’il puisse effectuer la découverte automatique.

    >[AZURE.NOTE] Un compte d’utilisateur à un rôle en lecture seule vCenter peut s’exécuter de basculement sur incident, mais ne peut pas arrêter des machines d’origine protégée. Si vous souhaitez arrêter ces machines, vous devez le rôle [Azure_Site_Recovery](#vmware-account-permissions) . Si vous êtes uniquement migration des ordinateurs virtuels VMware vers Azure et n’est pas nécessaire à la restauration puis le rôle en lecture seule est suffisant.

### <a name="prepare-the-configuration-server"></a>Préparer le serveur de configuration

1.  Assurez-vous que l’ordinateur que vous utilisez pour le serveur de configuration est conforme à la [Configuration requise](#configuration-server-prerequisites). En particulier, assurez-vous que l’ordinateur est connecté à internet avec ces paramètres :

    - Autoriser l’accès à ces URL : *. hypervrecoverymanager.windowsazure.com ; *. AccessControl.Windows.NET ; *. backup.windowsazure.com ; *. BLOB.Core.Windows.NET ; *. store.core.windows.net
    - Autoriser l’accès à [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) pour télécharger MySQL.
    - Autoriser la communication de pare-feu vers Azure avec les [plages IP de centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le protocole HTTPS (443).

2.  Téléchargez et installez [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) sur le serveur de configuration. (Actuellement de PowerCLI les autres versions ne sont pas pris en charge, y compris les versions R de la version 6.0.)


## <a name="create-a-recovery-services-vault"></a>Créer un coffre-fort de Services de récupération

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **gestion** > **sauvegarde et restauration de Site (OMS)**. Vous pouvez également cliquer sur **Parcourir** > **En chambre forte des Services de récupération** > **Ajouter**.

    ![Nouveau coffre-fort](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. Dans la zone **nom** , spécifiez un nom convivial pour identifier le coffre-fort. Si vous avez plus d’un abonnement, sélectionnez un d'entre eux.
4. [Créer un nouveau groupe de ressources](../resource-group-template-deploy-portal.md) ou sélectionnez-en une existante. Permet de spécifier une région Azure. Machines seront répliqués à cette région. Notez que le stockage Azure et les réseaux utilisés pour la récupération de Site devra être dans la même région. Pour vérifier les régions pris en charge consultez disponibilité géographique dans les [Détails de la tarification de récupération de Site de Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si vous souhaitez accéder rapidement à la chambre forte du tableau de bord, cliquez sur **fixer au tableau de bord** et puis cliquez sur **créer**.

    ![Nouveau coffre-fort](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

Le nouveau coffre-fort s’affiche sur le **tableau de bord** > **toutes les ressources**et sur les principaux **Services de récupération des coffres-forts** lame.

## <a name="getting-started"></a>Mise en route

Récupération de site fournit une expérience mise en route est conçu pour vous aider et exécuter aussi rapidement que possible. Il vérifie les conditions préalables et vous guide à travers les étapes que vous devez obtenir la récupération de Site déployé.

Vous devez sélectionner le type d’ordinateurs que vous souhaitez répliquer, et dans lequel vous souhaitez répliquer sur. Permet de paramétrer l’infrastructure, y compris des serveurs sur site, paramètres d’Azure, règles de réplication et planification de la capacité. Une fois l’infrastructure en place vous activez la réplication pour les machines virtuelles et des serveurs physiques. Vous pouvez ensuite exécuter les basculements de machines spécifiques, ou créer des plans de récupération basculer sur plusieurs machines.

Commencer la mise en route, choisissez comment vous souhaitez déployer la récupération de Site. Le flux de mise en route change légèrement en fonction de vos besoins en matière de réplication.


## <a name="step-1-choose-your-protection-goals"></a>Étape 1 : Choisissez vos objectifs de protection

Sélectionnez ce que vous voulez répliquer et dans lequel vous souhaitez répliquer sur.

1. La lame de **Services de récupération des coffres-forts** Sélectionnez votre coffre-fort et cliquez sur **paramètres**.
2. Dans les **paramètres** > **Mise en route** cliquez sur **Restauration du Site** > **étape 1 : préparation de l’Infrastructure** > **objectif de Protection**.

    ![Choisissez des objectifs](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. Dans **l’objectif de Protection** sélectionner **Sur Azure**et sélectionnez **Oui, avec l’hyperviseur VMware vSphere**. Puis cliquez sur **OK**.

    ![Choisissez des objectifs](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Étape 2 : Configurer l’environnement source

Configurer le serveur de la configuration et l’enregistrer dans le coffre-fort de Services de récupération. Si vous utilisez la réplication VMware VM spécifier le compte de VMware que vous utilisez pour la découverte automatique.

1. Cliquez sur **étape 1 : préparer l’Infrastructure** > **Source**. Dans **source de préparation** si vous ne possédez pas un serveur de configuration cliquez sur **+ Configuration serveur** pour ajouter un.

    ![Configurer la source](./media/site-recovery-vmware-to-azure/set-source1.png)

2. Dans le chèque de lame de **Serveur d’ajouter** ce **Serveur de Configuration** s’affiche dans le **type de serveur**.
3. Avant de configurer le serveur de configuration vérifier les [conditions préalables](#configuration-server-prerequisites). Dans une vérification particulière que l’ordinateur peut accéder à l’URL requises.
4.  Téléchargez le fichier d’installation le programme d’installation de Site récupération unifiée.
5.  Télécharger la clé d’inscription de coffre-fort. Vous devez cela lorsque vous exécutez le programme d’installation unifié. La clé est valide pendant 5 jours une fois que vous le générez.

    ![Configurer la source](./media/site-recovery-vmware-to-azure/set-source2.png)

6.  Sur l’ordinateur que vous utilisez comme serveur de configuration, installation unifiée pour installer le serveur de configuration, le serveur de traitement et le serveur cible principale.


### <a name="run-site-recovery-unified-setup"></a>Site d’exécution récupération unifiée d’installation

1.  Exécutez le fichier installation unifiée.
2.  Dans **avant de commencer** , sélectionnez **installer le serveur de configuration et d’un serveur de traitement**.

    ![Avant de commencer](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. Dans la **Licence de logiciel de tierce partie** , cliquez sur **J’accepte** pour télécharger et installer MySQL.

    ![Troisième = logiciels de tiers](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. Dans **l’enregistrement** Parcourir et sélectionnez la clé d’inscription que vous avez téléchargé à partir de la chambre forte.

    ![Inscription](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. Dans **Les paramètres Internet** , spécifiez comment le fournisseur en cours d’exécution sur le serveur de configuration se connectera à la récupération de Site Azure via internet.

    - Si vous souhaitez vous connecter avec le proxy qui est actuellement configuré sur l’ordinateur, sélectionnez **se connecter avec les paramètres de serveur proxy existants**.
    - Si vous souhaitez que le fournisseur pour vous connecter directement, sélectionnez **se connecter directement sans proxy**.
    - Si le proxy existant requiert une authentification, ou que vous souhaitez utiliser un proxy personnalisé pour la connexion du fournisseur, sélectionnez **se connecter avec les paramètres de proxy personnalisés**.
        - Si vous utilisez un proxy personnalisé, que vous devez spécifier les informations d’identification, le port et l’adresse
        - Si vous utilisez un serveur proxy vous avez devez déjà autorisé les URL décrites dans les [conditions préalables](#configuration-server-prerequisites).

    ![Pare-feu](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. Dans **Prerequisites Check** , le programme d’installation s’exécute une vérification pour s’assurer que l’installation peut exécuter. Si un message d’avertissement apparaît à propos de la **vérification de la synchronisation de temps globale** Vérifiez que l’heure de l’horloge système (paramètres de**Date et heure** ) est le même que le fuseau horaire.

    ![Conditions préalables](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. **Configuration de MySQL** de créer les informations d’identification pour vous connecter à l’instance de serveur MySQL qui est installé.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. Dans Sélectionner des **Détails d’environnement** si vous allez répliquer des ordinateurs virtuels VMware. Dans ce cas, le programme d’installation vérifie que PowerCLI 6.0 est installé.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. Dans, sélectionnez **L’emplacement d’installation** où vous souhaitez installer les fichiers binaires et de stocker le cache. Vous pouvez sélectionner un lecteur disposant d’au moins 5 Go de stockage disponible, mais nous vous recommandons d’un lecteur de cache au moins 600 Go d’espace libre.

    ![Emplacement d’installation](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. **Sélection du réseau** permet de spécifier le port d’écoute (carte réseau et le port SSL) sur lequel le serveur de configuration envoient et reçoivent des données de réplication. Vous pouvez modifier la valeur par défaut du port (9443). En plus de ce port, le port 443 sera utilisé par un serveur web qui orchestre les opérations de réplication. 443 ne doit pas être utilisée pour recevoir le trafic de réplication.


    ![Sélection du réseau](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  En **Résumé** , examinez les informations et cliquez sur **installer**. Lorsque l’installation est terminée, un mot de passe est généré. Vous en aurez besoin lorsque vous activez la réplication donc copier et conservez-le dans un endroit sûr.

    ![Résumé](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  La fin de l’inscription du serveur est affichée dans les **paramètres** > lame de**serveurs** dans la chambre forte.



#### <a name="run-setup-from-the-command-line"></a>Exécutez le programme d’installation à partir de la ligne de commande

Vous pouvez configurer le serveur de configuration à partir de la ligne de commande :

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Paramètres :

- / ServerMode : obligatoire. Spécifie si les serveurs de la configuration et les processus doivent être installés, ou seulement le serveur de traitement. Valeurs d’entrée : CS, PS.
- InstallLocation : obligatoire. Le dossier dans lequel les composants sont installés.
- / MySQLCredsFilePath. Obligatoire. Le chemin d’accès du fichier dans lequel sont stockées les informations d’identification serveur MySQL. Le fichier doit être dans ce format :
    - [MySQLCredentials]
    - MySQLRootPassword = "<Password>"
    - MySQLUserPassword = "<Password>"
- / VaultCredsFilePath. Obligatoire. L’emplacement du fichier d’informations d’identification de coffre-fort
- / EnvType. Obligatoire. Le type d’installation. Valeurs : VMware, NonVMware
- / PSIP et /CSIP. Obligatoire. L’adresse IP du serveur et du serveur de configuration.
- / PassphraseFilePath. Obligatoire. L’emplacement du fichier de mot de passe.
- / BypassProxy. Facultatif. Indique que le serveur de configuration se connecte sur Azure sans proxy.
- / ProxySettingsFilePath. Facultatif. Paramètres de proxy (le proxy par défaut requiert l’authentification, ou un proxy personnalisé). Le fichier doit être dans ce format :
    - [ProxySettings]
    - ProxyAuthentication = « Oui/non »
    - Proxy IP = « adresse IP > »
    - ProxyPort = "<Port>"
    - ProxyUserName = »<User Name>"
    - ProxyPassword = »<Password>"
- DataTransferSecurePort. Facultatif. Numéro de port à utiliser pour les données de réplication.
- SkipSpaceCheck. Facultatif. Ignorer la vérification de l’espace pour le cache.
- AcceptThirdpartyEULA. Obligatoire. Indicateur implique l’acceptation du CLUF de tierce partie.
- ShowThirdpartyEULA. Obligatoire. Affiche le CLUF de tiers. S’il est fourni en tant qu’entrée, tous les autres paramètres sont ignorés.

### <a name="add-the-vmware-account-used-for-automatic-discovery"></a>Ajoutez le compte de VMware utilisé pour la découverte automatique

 Lorsque vous avez préparé pour le déploiement, vous devez avoir [créé un compte de VMware](#prepare-an-account-for-automatic-discovery) et récupération de Site peut utiliser pour la découverte automatique. Ajoutez ce compte comme suit :

1. Ouvrez **CSPSConfigtool.exe**. Il est disponible sous la forme d’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
2. Cliquez sur **Gérer les comptes** > **Ajouter compte**.

    ![Ajouter compte](./media/site-recovery-vmware-to-azure/credentials1.png)

3. Dans **Détail du compte** , ajoutez le compte qui sera utilisé pour la découverte automatique. Notez qu’il peut prendre 15 minutes ou davantage pour le nom du compte apparaît dans le portail. Pour mettre à jour immédiatement, cliquez sur **Serveurs de Configuration** > nom du serveur > **Actualiser le serveur**.

    ![Détails](./media/site-recovery-vmware-to-azure/credentials2.png)

### <a name="connect-to-vsphere-hosts-and-vcenter-servers"></a>Se connecter aux hôtes de vSphere et serveurs vCenter

Si vous utilisez la réplication ordinateurs virtuels VMware se connecter aux hôtes de vSphere et serveurs vCenter.

1. Vérifiez que le serveur de configuration dispose d’un accès réseau et les hôtes de vSphere serveurs vCenter.
2. Cliquez sur **préparer infrastructure** > **Source**. Dans **source de préparation** sélectionnez le serveur de configuration et cliquez sur **+ vCenter** pour ajouter un serveur hôte ou vCenter de vSphere.
3. Dans **vCenter d’ajouter** , spécifiez un nom convivial pour le serveur hôte ou vCenter de vSphere et spécifiez l’adresse IP ou le nom de domaine complet du serveur. Conservez le port 443, à moins que vos serveurs VMware sont configurés pour écouter les demandes sur un port différent. Sélectionnez ensuite le compte qui sera utilisé pour se connecter au serveur de VMware. Cliquez sur **OK**.

    ![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

    >[AZURE.NOTE] Si vous ajoutez le serveur de vCenter ou l’hôte de vSphere avec un compte qui ne dispose pas des privilèges d’administrateur sur le serveur hôte ou vCenter, puis assurez-vous que le compte dispose de ces privilèges activés : Datacenter, magasin de données, dossier, hôte, réseau, ressources, Virtual machine, vSphere distribués commutateur. En outre, le serveur vCenter nécessite le privilège de vues de stockage.

Récupération de site se connecte à des serveurs en utilisant les paramètres que vous avez spécifié et découvre les ordinateurs virtuels VMware.

## <a name="step-3-set-up-the-target-environment"></a>Étape 3 : Définir l’environnement cible

Vérifiez que vous disposez d’un compte de stockage pour la réplication et un réseau Azure auquel Azure VM se connectera après le basculement.

1.  Cliquez sur **préparer infrastructure** > **cible** et sélectionnez l’abonnement Azure que vous souhaitez utiliser.
2.  Spécifiez le modèle de déploiement que vous souhaitez utiliser pour les machines virtuelles après le basculement.
3.  Récupération de site vérifie que vous disposez d’un ou plusieurs comptes de stockage Azure compatible et des réseaux.

    ![Cible](./media/site-recovery-vmware-to-azure/gs-target.png)

4.  Si vous n’avez pas créé un compte de stockage et que vous souhaitez en créer un à l’aide de ARM cliquez sur **+ compte de stockage** pour faire qu’inline.  Sur la lame de **créer de compte de stockage** , spécifiez un nom de compte, un type, un abonnement et un emplacement. Le compte doit être dans la même région, comme le coffre-fort de Services de récupération.

    ![Stockage](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

    Notez que :

    - Si vous souhaitez créer un compte de stockage à l’aide du modèle classique vous procéderez dans Azure portal. [Pour en savoir plus](../storage/storage-create-storage-account-classic-portal.md)
    - Si vous utilisez un compte de stockage de prime pour les données répliquées, pour que vous devez paramétrer un compte de stockage standard supplémentaire réplication de magasin ouvre une session qui changent en cours de capture les données sur site.

    > [AZURE.NOTE] Protection pour les comptes de stockage premium dans l’Inde centrale et du Sud de l’Inde n’est actuellement pas pris en charge.

4.  Sélectionnez un réseau Azure. Si vous n’avez pas créé un réseau et que vous souhaitez le faire à l’aide de ARM cliquez sur **+ réseau** à faire dont inline. Sur la lame de **créer des réseaux virtuels** , spécifiez un nom de réseau, plage d’adresses, détails du sous-réseau, abonnement et l’emplacement. Le réseau doit être dans le même emplacement que le coffre-fort de Services de récupération.

    ![Réseau](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

    Si vous souhaitez créer un réseau utilisant le modèle classique vous procéderez dans Azure portal. [En savoir plus](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## <a name="step-4-set-up-replication-settings"></a>Étape 4 : Définir des paramètres de réplication

1. Pour créer une nouvelle réplication de stratégie, cliquez sur **préparer infrastructure** > **Les paramètres de réplication** > **+ créer et associer**.
2. **Créer** et associer stratégie, spécifiez un nom de stratégie.
3. Dans **seuil RPO**: spécifier la limite de la RPO. Alertes seront générées lors de la réplication continue dépasse cette limite.
5. Dans la **rétention de point de récupération**, spécifier en heures combien de temps la fenêtre de rétention pour chaque point de récupération. Ordinateurs protégés peuvent être récupérés à n’importe quel point dans une fenêtre. Jusqu'à rétention de 24 heures est pris en charge pour les machines répliquées vers le stockage de la prime.
6. La **fréquence des snapshots cohérents au niveau de l’application**, spécifiez la fréquence (en minutes) seront créés contenant des snapshots cohérents avec l’application des points de récupération.
7. Lorsque vous créez une stratégie de réplication, par défaut une stratégie correspondante est automatiquement créée pour la restauration automatique. Pour exemple, si la stratégie de réplication est **rep-stratégie** puis la stratégie de restauration seront **rep-politique-restauration automatique**. Cette stratégie n’est pas utilisée tant que vous initiez un retour arrière.  
8. Cliquez sur **OK** pour créer la stratégie.

    ![Stratégie de réplication](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. Lorsque vous créez une nouvelle stratégie, il est automatiquement associé au serveur de configuration. Cliquez sur **OK**.

    ![Stratégie de réplication](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## <a name="step-5-capacity-planning"></a>Étape 5 : Planification de la capacité

Maintenant que vous avez votre basic infrastructure configuration vous pouvez réfléchir à la planification de la capacité et déterminer si vous avez besoin de ressources supplémentaires.

Récupération de site fournit un planificateur de capacités pour vous aider à allouer les ressources de votre environnement source, composants de restauration de site, mise en réseau et de stockage. Vous pouvez exécuter le Planificateur de mode rapide pour les estimations basées sur un nombre moyen de machines virtuelles, des disques et du stockage, ou en mode détaillé, dans laquelle vous allez saisir les chiffres du niveau de charge de travail. Avant de commencer à vous devrez :

- Rassembler des informations sur votre environnement de réplication, y compris les machines virtuelles, des disques par les ordinateurs virtuels et stockage par disque.
- Estimer le taux de modification (évolution du) quotidiennes que vous avez pour les données répliquées. Vous pouvez utiliser la [solution matérielle-logicielle de planification de la capacité vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) pour vous aider à effectuer cette opération.

1.  Cliquez sur **Télécharger** pour télécharger l’outil, puis exécutez-le. [Lire l’article](site-recovery-capacity-planner.md) accompagnant l’outil.
2.  Lorsque vous avez terminé sélectionnez **Oui** **vous avez terminé la planification des capacités ?**

    ![Planification de la capacité](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

Le tableau suivant illustre un certain nombre de points pour vous aider à planifier la capacité de ce scénario.


**Composant** | **Détails**
--- | --- | ---
**Réplication** | **Maximum quotidiennement modifier taux**— un ordinateur protégé peut utiliser uniquement un serveur de traitement et un serveur unique peut gérer un quotidien jusqu'à 2 To de taux de change. 2 To est donc que le plus de données quotidiennes modifier taux qui est pris en charge pour un ordinateur protégé.<br/><br/> **Le débit maximal**, un ordinateur répliqué peut appartenir à un compte de stockage dans Azure. Un compte de stockage standard peut gérer un maximum de 20 000 requêtes par seconde, et nous vous conseillons de conserver le nombre des Ops ES/s sur une machine source à 20 000. Pour l’exemple si vous disposez d’une machine source avec 5 disques et chaque disque génère des 120 IOPS (8 Ko la taille) la source il sera dans le Azure par la limite d’e/s de disque de 500. Le nombre de comptes de stockage requises = total source Ops ES/s/20000.
**Serveur de configuration** | Le serveur de configuration doit être en mesure de gérer la capacité journalière de taux de change entre toutes les charges de travail en cours d’exécution sur les ordinateurs protégés et doit être suffisamment de bande passante pour la réplication continue des données de stockage Azure.<br/><br/> Comme meilleure pratique, nous recommandons que le serveur de configuration se trouver sur le même réseau et un segment de réseau local que les ordinateurs que vous souhaitez protéger. Il peut se trouver sur un réseau différent, mais vous souhaitez protéger des machines doivent avoir L3 visibilité sur votre réseau pour qu’il.<br/><br/> Les recommandations de taille pour le serveur de configuration sont résumées dans le tableau ci-dessous.
**Serveur de traitement** | Le premier serveur de processus est installé par défaut sur le serveur de configuration. Vous pouvez déployer des serveurs de traitement supplémentaires pour faire évoluer votre environnement. Notez que :<br/><br/> Le serveur de traitement reçoit des données de réplication à partir d’ordinateurs protégés et l’optimise avec la mise en cache, de compression et de chiffrement avant de l’envoyer vers Azure. L’ordinateur serveur de processus doit avoir suffisamment de ressources pour effectuer ces tâches.<br/><br/> Le serveur utilise le cache disque. Nous vous recommandons un disque cache distinct de 600 Go ou plus pour gérer les modifications apportées aux données stockées en cas de goulot d’étranglement du réseau ou de panne.

### <a name="size-recommendations-for-the-configuration-server"></a>Recommandations de taille pour le serveur de configuration

**UNITÉ CENTRALE** | **Mémoire** | **Taille du cache disque** | **Taux de modification des données** | **Ordinateurs protégés**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 noyaux de @ 2.5 GHz) | 16 GO | 300 GO | 500 Go ou moins | Réplication de moins de 100 ordinateurs.
12 vCPUs (2 sockets * 6 cœurs @ 2.5 GHz) | 18 GO | 600 GO | 500 Go à 1 to | Répliquer entre 100-150 ordinateurs.
16 vCPUs (2 sockets * 8 cœurs @ 2.5 GHz) | 32 GO | 1 TO | 1 To à 2 To | Répliquer entre 150 et 200 ordinateurs.
Déployer un autre serveur de traitement | | | > 2 To | Déployer des serveurs de traitement supplémentaires si vous utilisez la réplication de plus de 200 ordinateurs, ou si les données quotidiennes modifier taux dépasse 2 To.

Où :

- Chaque ordinateur source est configuré avec 3 disques de 100 Go.
- Nous avons utilisé les évaluation des performances de stockage de 8 disques durs SAS 10 k tr/min avec RAID 10 pour les mesures de disque cache.

### <a name="size-recommendations-for-the-process-server"></a>Recommandations de taille pour le serveur de traitement

Si vous avez besoin protéger plus de 200 ordinateurs ou taux de modification quotidien est supérieure à 2 To, vous pouvez ajouter des serveurs de traitement supplémentaires pour gérer la charge de réplication. Pour mettre à l’échelle les vous pouvez :

- Augmenter le nombre de serveurs de configuration. Par exemple, vous pouvez protéger jusqu'à 400 machines avec deux serveurs de configuration.
- Ajouter des serveurs de traitement supplémentaires et les utiliser pour gérer le trafic au lieu de (ou en plus) du serveur de configuration.

Ce tableau décrit un scénario dans lequel :

- Vous n’envisagez pas d’utiliser le serveur de configuration sous la forme d’un serveur de traitement.
- Vous avez configuré un serveur de processus supplémentaires.
- Vous avez protégés ordinateurs virtuels configurés pour utiliser le serveur de traitement supplémentaires.
- Chaque ordinateur protégé source est configuré avec trois disques de 100 Go.

**Serveur de configuration** | **Serveur de traitement supplémentaires**| **Taille du cache disque** | **Taux de modification des données** | **Ordinateurs protégés**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 noyaux de @ 2.5 GHz), 16 Go de mémoire | 4 vCPUs (2 sockets * 2 noyaux @ 2.5 GHz), 8 Go de mémoire | 300 GO | 250 Go ou moins | Répliquer les machines inférieur ou égal à 85.
8 vCPUs (2 sockets * 4 noyaux de @ 2.5 GHz), 16 Go de mémoire | 8 vCPUs (2 sockets * 4 noyaux de @ 2.5 GHz), 12 Go de mémoire | 600 GO | De 250 Go à 1 to | Répliquer entre 85-150 ordinateurs.
12 vCPUs (2 sockets * 6 cœurs @ 2.5 GHz), 18 Go de mémoire | 12 vCPUs (2 sockets * 6 cœurs @ 2.5 GHz) 24 Go de mémoire | 1 TO | 1 To à 2 To | Répliquer entre des machines de 150-225.


La façon dont vous mettre à l’échelle vos serveurs est dépend de vos préférences pour une échelle des ou évoluer de modèle.  Vous évoluez en déployant plusieurs configuration haut de gamme et les serveurs de traitement, ou évoluer par le déploiement de plusieurs serveurs avec moins de ressources. Par exemple : Si vous avez besoin protéger les 220 machines peut procéder à une des opérations suivantes :

- Configurer le serveur de configuration 12vCPU, 18 Go de mémoire, un serveur de traitement supplémentaires 12vCPU, 24 Go de mémoire et configurer des ordinateurs protégés pour utiliser uniquement le serveur de processus supplémentaires.
- Vous pouvez également configurer deux serveurs de configuration (2 x 8vCPU, 16 Go de RAM) et deux serveurs de processus supplémentaires (1 x 8vCPU) et 4vCPU x 1 pour gérer les ordinateurs (220) 135 + 85 et configurer des ordinateurs protégés pour utiliser uniquement les serveurs de processus supplémentaires.

[Suivez les instructions](#deploy-additional-process-servers) pour configurer un serveur de processus supplémentaires.

### <a name="network-bandwidth-considerations"></a>Considérations relatives à la bande passante réseau

Vous pouvez utiliser l’outil capacity planner pour calculer la bande passante que nécessaire pour la réplication (réplication initiale puis delta). Vous disposez de plusieurs options pour contrôler la quantité d’utilisation de la bande passante pour la réplication :

- **Limiter la bande passante**: le trafic de VMware qui réplique vers Azure passe par un certain serveur de traitement. Vous pouvez limiter la bande passante sur les ordinateurs qui exécutent en tant que serveurs de traitement.
- **Influence de la bande passante**: vous pouvez influencer la bande passante utilisée pour la réplication à l’aide de deux clés de Registre :
    - La valeur de Registre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** Spécifie le nombre de threads qui sont utilisés pour le transfert des données (réplication initiale ou delta) d’un disque. Une valeur plus élevée augmente la bande passante utilisée pour la réplication.
    - Le **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** Spécifie le nombre de threads utilisé pour le transfert de données pendant la restauration.

#### <a name="throttle-bandwidth"></a>Limiter la bande passante

1. Ouvrez le composant logiciel enfichable MMC de Microsoft Azure sauvegarde sur l’ordinateur jouant le rôle du serveur de traitement. Par défaut, un raccourci pour Microsoft Azure Backup est disponible sur le bureau ou dans C:\Program Files\Microsoft Azure récupération Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.

    ![Limiter la bande passante](./media/site-recovery-vmware-to-azure/throttle1.png)

3. Sur l’onglet **régulation** sélectionnez **Activer l’utilisation de la bande passante internet de limitation pour les opérations de sauvegarde**et définir les limites pour le travail et non liés au travail heures. Les plages valides sont comprises entre 512 Kbits/s et 102 Mbps par seconde.

    ![Limiter la bande passante](./media/site-recovery-vmware-to-azure/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting-NoThrottle** indique qu’aucune limitation n’est requis.


#### <a name="influence-network-bandwidth"></a>Influence de la bande passante réseau

1. Dans le Registre, accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
    - Pour influer sur le trafic de la bande passante sur un disque de réplication, modifiez la valeur de **UploadThreadsPerVM**, ou créer la clé si elle n’existe pas.
    - Pour influer sur la bande passante pour le trafic de retour arrière d’Azure, modifiez la valeur **DownloadThreadsPerVM**.
2. La valeur par défaut est 4. Dans un réseau « overprovisioned », ces clés de Registre doivent être modifiés à partir des valeurs par défaut. La valeur maximale est 32. Surveiller le trafic afin d’optimiser la valeur.

## <a name="step-6-replicate-applications"></a>Étape 6 : Les applications de réplication

Assurez-vous que les ordinateurs que vous souhaitez répliquer sont prêt pour l’installation du service de mobilité et puis activer la réplication.

### <a name="install-the-mobility-service"></a>Installer le service de la mobilité

La première étape de l’activation de la protection de machines virtuelles et des serveurs physiques est d’installer le service de la mobilité. Vous pouvez effectuer cela de deux façons :

- **Commande de serveur de processus**: lorsque vous activez la réplication sur un ordinateur, distribuer et installer le composant service de mobilité du serveur de traitement. Notez que l’installation par diffusion ne se produit pas si les ordinateurs sont en cours d’exécution une version à distance-date de fin du composant.
- **Transmission de l’entreprise**: installe automatiquement le composant à l’aide de votre processus de type Pousser entreprise comme WSUS ou de System Center Configuration Manager ou [d’Automation d’Azure et configuration de l’état de souhaité](./site-recovery-automate-mobility-service-install.md). Définir le serveur de configuration avant de le faire.
- **Installation manuelle**: installer le composant de manuellement sur chaque ordinateur que vous souhaitez répliquer. Définir le serveur de configuration avant de le faire.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Préparer pour la distribution automatique sur les machines Windows

Voici comment préparer des machines Windows afin que le service de la mobilité peut être installé automatiquement par le serveur de traitement.

1.  Créer un compte qui peut être utilisé par le serveur de traitement pour accéder à l’ordinateur. Le compte doit disposer des privilèges d’administrateur (local ou domaine) et qu’il est utilisé uniquement pour l’installation par diffusion.

    >[AZURE.NOTE] Si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle de l’accès utilisateur à distance sur l’ordinateur local. Pour ce faire, dans le Registre sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, ajoutez l’entrée DWORD LocalAccountTokenFilterPolicy avec une valeur de 1. Pour ajouter l’entrée de Registre à partir d’un type CLI **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  Le pare-feu Windows de l’ordinateur que vous souhaitez protéger, sélectionnez **Autoriser une application ou une fonctionnalité via le pare-feu**. Activer le **partage de fichiers et imprimantes** et **Windows Management Instrumentation**. Pour les ordinateurs qui appartiennent à un domaine, vous pouvez configurer les paramètres de pare-feu avec un objet de stratégie de groupe.

    ![Paramètres de pare-feu](./media/site-recovery-vmware-to-azure/mobility1.png)

2. Ajoutez le compte que vous avez créé :

    - Ouvrez **cspsconfigtool**. Il est disponible sous la forme d’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
    - Dans l’onglet **Gérer les comptes** , cliquez sur **Ajouter un compte**.
    - Ajoutez le compte que vous avez créé. Après avoir ajouté le compte, vous devrez fournir les informations d’identification lorsque vous activez la réplication pour une machine.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Préparer pour la distribution automatique sur des serveurs Linux

1.  Assurez-vous que l’ordinateur Linux que vous souhaitez protéger est pris en charge comme décrit dans les [conditions préalables d’ordinateur protégé](#protected-machine-prerequisites). Vérifiez qu’il existe la connectivité entre l’ordinateur Linux et le serveur de traitement du réseau.

2.  Créer un compte qui peut être utilisé par le serveur de traitement pour accéder à l’ordinateur. Le compte doit être un utilisateur root sur le serveur source Linux, et il est utilisé uniquement pour l’installation par diffusion.

    - Ouvrez **cspsconfigtool**. Il est disponible sous la forme d’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
    - Dans l’onglet **Gérer les comptes** , cliquez sur **Ajouter un compte**.
    - Ajoutez le compte que vous avez créé. Après avoir ajouté le compte, vous devrez fournir les informations d’identification lorsque vous activez la réplication pour une machine.

3.  Vérifiez que le fichier/etc/hosts sur le serveur Linux source contienne des entrées qui mappent le nom d’hôte local pour les adresses IP associées à toutes les cartes réseau.
4.  Installation des dernière openssh, openssh-server, packages d’openssl sur l’ordinateur que vous souhaitez répliquer.
5.  Assurez-vous que SSH est activé et en cours d’exécution sur le port 22.
6.  Activer l’authentification de mot de passe et le sous-système SFTP dans le fichier sshd_config comme suit :

    - Ouvrez une session en tant que racine.
    - Dans le fichier /etc/ssh/sshd_config de fichier, recherchez la ligne qui commence par **PasswordAuthentication**.
    - Décommentez la ligne et remplacez la valeur **Aucun** à **Oui**.
    - Recherchez la ligne qui commence par le **sous-système** et ne commentez pas la ligne.

        ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Installer le Service de la mobilité manuellement

Les programmes d’installation sont disponibles sur le serveur de Configuration dans les **fichiers de C:\Program (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

Système d’exploitation source | Fichier d’installation de service de mobilité
--- | ---
Windows Server (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-mobility-service-on-a-windows-server"></a>Installer le Service de la mobilité sur un serveur Windows


1. Téléchargez et exécutez le programme d’installation approprié.
2. Dans **avant de commencer** , sélectionnez **service de la mobilité**.

    ![Service de la mobilité](./media/site-recovery-vmware-to-azure/mobility3.png)

3. Dans les **Détails de Configuration de serveur** , spécifiez l’adresse IP du serveur de la configuration et le mot de passe qui a été généré lorsque vous avez exécuté le programme d’installation unifié. Vous pouvez récupérer le mot de passe en exécutant : ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – v** sur le serveur de configuration.

    ![Service de la mobilité](./media/site-recovery-vmware-to-azure/mobility6.png)

4. Dans **L’emplacement d’installation** , laissez le paramètre par défaut et cliquez sur **suivant** pour commencer l’installation.
5. En **Cours de l’Installation de** surveiller l’installation et redémarrez l’ordinateur si vous y êtes invité. Après l’installation du service peut prendre environ 15 minutes pour le statut de mise à jour dans le portail.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Installer le Service de la mobilité sur un serveur Windows à l’aide de l’invite de commande

1. Copiez le programme d’installation dans un dossier local (par exemple C:\Temp) sur le serveur que vous souhaitez protéger. Le programme d’installation peut être trouvé sur le serveur de Configuration sous le **[emplacement d’installation] \home\svsystems\pushinstallsvc\repository**. Le package pour les systèmes d’exploitation Windows possède un nom semblable à Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. **Renommez** ce fichier MobilitySvcInstaller.exe
3. Exécutez la commande suivante pour extraire le programme d’installation MSI </br>

        C:\> cd C:\Temp
        C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted
        C:\Temp> cd Extracted
        C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>

#####<a name="full-command-line-syntax"></a>Syntaxe de ligne de commande complet

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Paramètres**

- **/Role :** Obligatoire. Spécifie si le service de la mobilité doit être installé. Agent de valeurs d’entrée | MasterTarget
- **/InstallLocation :** Obligatoire. Spécifie l’emplacement d’installation du service.
- **/PassphraseFilePath :** Obligatoire. Le mot de passe du serveur de configuration.
- **/LogFilePath :** Obligatoire. Emplacement où les fichiers journaux d’installation doivent être créés.



#### <a name="uninstall-mobility-service-manually"></a>Désinstaller manuellement des services de mobilité

Service de mobilité peut être désinstallé à l’aide de l’ajouter supprimer un programme à partir du Panneau de configuration ou à l’aide de la ligne de commande.

La commande pour désinstaller le Service de la mobilité à l’aide de la ligne de commande est

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### <a name="install-mobility-service-on-a-linux-server-using-command-line"></a>Installer le Service de la mobilité sur un serveur Linux à l’aide de la ligne de commande

1. Copiez l’archive tar approprié en fonction du tableau ci-dessus sur l’ordinateur Linux que vous souhaitez répliquer.
2. Ouvrez un programme de shell et extraire l’archive tar compressé à un chemin d’accès local en exécutant :`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Créer un fichier passphrase.txt dans le répertoire local dans lequel vous avez extrait le contenu de l’archive tar. Faire cette permet de copier le mot de passe à partir de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase sur le serveur de configuration et enregistrez-le dans passphrase.txt en exécutant *`echo <passphrase> >passphrase.txt`* dans le shell.
4. Installer le service de la mobilité en exécutant *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Spécifiez l’adresse IP interne du serveur de configuration et assurez-vous que le port 443 est sélectionné. Après l’installation du service peut prendre environ 15 minutes pour le statut de mise à jour dans le portail.

**Vous pouvez également installer à partir de la ligne de commande**:

1. Copier le mot de passe à partir de C:\Program Files (x86) les \InMage Systems\private\connection sur le serveur de configuration et l’enregistrer en tant que « passphrase.txt » sur le serveur de configuration. Puis exécutez ces commandes. Dans notre exemple, l’adresse IP du serveur configuration est 104.40.75.37 et le port HTTPS doit être 443 :

Pour installer sur un serveur de production :

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Pour installer sur le serveur cible principale :


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### <a name="enable-replication"></a>Activer la réplication

#### <a name="before-you-start"></a>Avant de commencer

Si vous utilisez la réplication VMware virtual machines Notez les points suivants :

- Ordinateurs virtuels VMware sont découverts toutes les 15 minutes et peut prendre 15 minutes ou plus pour qu’ils apparaissent dans le portail après la découverte. De la même façon la détection peut prendre 15 minutes ou plus lorsque vous ajoutez un nouvel hôte serveur ou vSphere vCenter.
- Modifications de l’environnement sur l’ordinateur virtuel (par exemple, l’installation des outils VMware) peuvent également prendre 15 minutes ou davantage pour être mis à jour dans le portail.
- Vous pouvez vérifier l’heure de la dernière découverte pour les machines virtuelles de VMware dans le champ **Dernier Contact à** pour l’hôte de serveur/vSphere vCenter sur la **Configuration de serveurs** lame.
- Pour ajouter des ordinateurs pour la réplication sans attendre la découverte planifiée, sélectionnez le serveur de configuration (ne cliquez pas dessus) et cliquez sur le bouton **Actualiser** .
- Lorsque vous activez la réplication, si l’ordinateur est préparé le serveur automatiquement d’installe le service de la mobilité dessus.

#### <a name="exclude-disks-from-replication"></a>Exclure les disques de la réplication

Lorsque vous activez la réplication, tous les disques sur un ordinateur sont répliquées par défaut. Vous pouvez exclure les disques de la réplication. Par exemple, vous pouvez souhaiter pas répliquer les disques avec des données temporaires, ou données qui a mis à jour chaque fois qu’une machine ou redémarrage de l’application (par exemple pagefile.sys ou tempdb de SQL Server). Si vous souhaitez exclure les disques Notez que :

- Vous pouvez exclure uniquement les disques qui ont déjà installé le service de mobilité. Vous devez [installer manuellement le service de la mobilité](#install-the-mobility-service-manually) , car le service de la mobilité est installé uniquement à l’aide du mécanisme de transmission après que la réplication est activée.
- Seuls les disques de base peuvent être exclus de la réplication. Vous ne pouvez pas exclure des OS ou des disques dynamiques.
- Une fois la réplication activée Impossible d’ajouter ou supprimer des disques pour la réplication. Si vous voulez ajouter ou exclure un disque, que vous devez désactiver la protection de l’ordinateur et puis de le réactiver.
- Si vous excluez un disque qui est nécessaire au fonctionnement d’une application, après le basculement vers Azure vous devrez créer manuellement dans Azure afin que l’application répliquée peut s’exécuter. Sinon, vous pouvez intégrer Azure automation dans un plan de récupération pour créer le disque au cours du basculement de la machine.
- Sauvegarder disques que vous créez manuellement dans Azure va échouer. Par exemple si vous échouer sur trois disques et créez deux directement dans Azure, les cinq va échouer précédent. Vous ne pouvez pas exclure créés manuellement à partir de la restauration de disques.

**Activer la réplication comme suit**:

1. Cliquez sur **étape 2 : application de répliquer** > **Source**. Après avoir activé la réplication pour la première fois que vous cliquerez sur **+ répliquer** dans le coffre-fort pour activer la réplication pour d’autres ordinateurs.
2. De la lame de **Source** > **Source** sélectionnez le serveur de configuration.
3. Dans **type de Machine** , sélectionnez **ordinateurs virtuels** ou **Ordinateurs physiques**.
4. Sélectionnez le serveur vCenter qui gère l’hôte vSphere dans **vCenter/vSphere Hypervisor** , ou sélectionnez l’hôte. Ce paramètre n’est pas pertinent si vous utilisez la réplication de machines physiques.
5. Sélectionnez le serveur de traitement. Si vous n’avez pas créé de tous les serveurs des processus supplémentaires, il s’agit du nom du serveur de configuration. Puis cliquez sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Dans **cible** sélectionnez l’abonnement de stockage en chambre forte et dans le **modèle de déploiement de Post-basculement** , sélectionnez le modèle que vous souhaitez utiliser dans Azure après basculement sur incident (gestion classique ou de ressources).
7. Sélectionnez le compte de stockage Azure que vous allez utiliser pour la réplication des données. Notez que :

    - Vous pouvez sélectionner une prime ou un compte de stockage standard. Si vous sélectionnez un compte premium, vous devez spécifier un compte de stockage standard supplémentaire pour les journaux de réplication en cours. Les comptes doivent être dans la même région, comme le coffre-fort de Services de récupération.
    - Si vous souhaitez utiliser un compte de stockage autre que celles vous avez vous pouvez [en créer un](#set-up-an-azure-storage-account). Pour créer un espace de stockage compte en utilisant le modèle ARM, cliquez sur **Créer nouveau**. Si vous souhaitez créer un compte de stockage en utilisant le modèle classique, vous ferez que [dans le portail Azure](../storage/storage-create-storage-account-classic-portal.md).

8. Sélectionnez le réseau Azure et le sous-réseau auquel Azure VM va se connecter lorsqu’ils vous lancés après le basculement. Le réseau doit être dans la même région, comme le coffre-fort de Services de récupération. Sélectionnez **configurer pour les ordinateurs sélectionnés** pour appliquer le paramètre de réseau à tous les ordinateurs, que vous sélectionnez pour la protection. Sélectionnez **configurer ultérieurement** pour sélectionner le réseau Azure par machine. Si vous n’avez pas un réseau, vous devez en [créer un](#set-up-an-azure-network). Pour créer un réseau en utilisant le modèle ARM, cliquez sur **Créer nouveau**. Si vous souhaitez créer un réseau utilisant le modèle classique, vous ferez ce [portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Le cas échéant, sélectionnez un sous-réseau. Puis cliquez sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. Dans des **Machines virtuelles** > **Sélectionnez machines virtuelles** cliquez sur et sélectionnez chaque ordinateur que vous souhaitez répliquer. Vous ne pouvez sélectionner que les machines pour lesquelles la réplication peut être activée. Puis cliquez sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. Dans les **Propriétés** > **Propriétés de configuration**, sélectionnez le compte qui sera utilisé automatiquement par le serveur de traitement pour installer le service de la mobilité sur l’ordinateur. Par défaut, tous les disques sont répliqués. Cliquez sur **Tous les disques** et désactivez les disques que vous ne voulez pas répliquer. Puis cliquez sur **OK**. Vous pouvez définir des propriétés supplémentaires ultérieurement.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. Dans les **paramètres de réplication** > **configurer les paramètres de réplication** vérifier que la stratégie de réplication correcte est sélectionnée. Vous pouvez modifier les paramètres de stratégie de réplication dans les **paramètres** > **règles de réplication** > nom de la stratégie > **Modifier les paramètres**. Les modifications que vous appliquez à une stratégie seront appliquées les machines de réplication et le nouveau.

12. Activer **la cohérence de Multi-machine virtuelle** si vous souhaitez rassembler les ordinateurs dans un groupe de réplication et spécifiez un nom pour le groupe. Puis cliquez sur **OK**. Notez que :

    - Les machines dans la réplication de regroupent répliquer et partagez des points de récupération sur incident-cohérente et homogène si ils basculent.
    - Nous vous conseillons de recueillir les ordinateurs virtuels et les serveurs physiques entre eux afin qu’ils reflètent vos charges de travail. Cohérence de multi-VM activation peut avoir un impact sur les performances de la charge de travail et ne doit être utilisée que si les ordinateurs la même charge de travail et que vous avez besoin de la cohérence.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. Cliquez sur **Activer la réplication**. Vous pouvez suivre la progression de la tâche **d’Activation de la Protection** dans les **paramètres** > **travaux** > **Tâches de récupération de Site**. Après l’exécution de la tâche de **Protection de finaliser** l’ordinateur est prêt pour le basculement.

> [AZURE.NOTE] Si l’ordinateur est prêt pour l’installation par diffusion que sera installé le composant de service de mobilité lorsque la protection est activée. Une fois le composant installé sur l’ordinateur de qu'une tâche de protection démarre et échoue. Vous devez redémarrer manuellement chaque ordinateur après la défaillance. Après le redémarrage la tâche de protection commence à nouveau et la réplication initiale se produit.

### <a name="view-and-manage-vm-properties"></a>Permet d’afficher et de gérer les propriétés de la machine virtuelle

Nous vous recommandons de vérifier les propriétés de l’ordinateur source. N’oubliez pas que le nom d’Azure VM doit être conforme aux [exigences d’Azure VM](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Cliquez sur **paramètres** > **les éléments répliqué** > et sélectionnez l’ordinateur. La lame **Essentials** affiche des informations sur les paramètres des ordinateurs et l’état.

2. Dans **Propriétés** vous pouvez afficher des informations de réplication et le basculement de la machine virtuelle.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. Dans le **calcul et le réseau** > **Propriétés de calcul** vous pouvez spécifier la taille du nom et de la cible Azure VM. Modifier le nom pour satisfaire aux exigences Azure si vous avez besoin.
Vous pouvez également afficher et ajouter des informations sur le réseau cible, le sous-réseau et l’adresse IP qui sera affecté à la machine virtuelle d’Azure. Notez les points suivants :

    - Vous pouvez définir l’adresse IP cible. Si vous ne fournissez pas une adresse, l’échec sur l’ordinateur utilise DHCP. Si vous définissez une adresse qui n’est pas disponible lors du basculement, le basculement ne fonctionnera pas. La même adresse IP cible peut servir pour le test de basculement si l’adresse est disponible dans le réseau de basculement test.
    - Le nombre de cartes réseau est déterminée par la taille que vous spécifiez pour l’ordinateur virtuel de la cible, comme suit :

        - Si le nombre de cartes réseau sur l’ordinateur source est inférieur ou égal au nombre de cartes autorisé pour la taille de l’ordinateur cible, la cible ont le même nombre de cartes comme source.
        - Si le nombre de cartes sur l’ordinateur virtuel source dépasse le nombre autorisé de la taille cible puis le maximum de la taille cible sera utilisé.
        - Par exemple, si une machine source possède deux cartes réseau et la taille de la machine cible prend en charge quatre, l’ordinateur cible aura deux cartes. Si l’ordinateur source dispose de deux cartes, mais la taille cible prise en charge prend uniquement en charge un l’ordinateur cible aura qu’une seule carte.     
    - Si l’ordinateur virtuel possède plusieurs cartes réseau qu’ils seront tous se connecter au même réseau.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. Dans les **disques** vous pouvez voir le système d’exploitation et les disques de données sur l’ordinateur virtuel qui sera répliquée.


## <a name="step-7-test-the-deployment"></a>Étape 7 : Tester le déploiement

Pour tester le déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle ou un plan de récupération qui contient un ou plusieurs ordinateurs virtuels.


### <a name="prepare-for-failover"></a>Préparer pour le basculement

- Pour exécuter un test de basculement, nous vous recommandons de créer un nouveau réseau Azure qui a isolé de votre réseau de production Azure (il s’agit de comportement par défaut lorsque vous créez un nouveau réseau dans Azure). [En savoir plus](site-recovery-failover.md#run-a-test-failover) sur l’exécution du test de basculement.
- Pour obtenir les meilleures performances lorsque vous basculer sur Azure, installez l’Agent Azure sur l’ordinateur protégé. Il rend le démarrage plus rapide et simplifie le dépannage. Installez l’agent [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Pour tester votre déploiement, vous devez une infrastructure pour la machine répliquée à fonctionner comme prévu. Si vous souhaitez tester Active Directory et DNS, vous pouvez créer un ordinateur virtuel comme un contrôleur de domaine dans DNS et répliquer ces paramètres sur Azure à l’aide de la récupération de Site Azure. Lire en plusieurs [Considérations de basculement test pour Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Assurez-vous que le serveur de configuration est en cours d’exécution. Basculement échoue dans le cas contraire.
- Si vous avez exclu des disques de la réplication, vous devrez créer ces disquettes manuellement dans Azure après basculement afin que l’application s’exécute comme prévu.
- Si vous souhaitez exécuter un basculement non planifié et non d’un test de basculement, notez les points suivants :

    - Si possible vous devez arrêter les ordinateurs principales avant d’exécuter un basculement non planifié. Cela garantit que vous n’avez pas les ordinateurs source et de réplica en cours d’exécution en même temps. Si vous utilisez la réplication VMware VM vous pouvez spécifier que la restauration du Site fasse un maximum d’efforts pour arrêter les ordinateurs source. Selon l’état du site principal, cela peut ou peut ne pas fonctionne. Récupération de Site n’offre pas cette option si vous utilisez la réplication des serveurs physiques.
    - Lorsque vous exécutez un basculement non planifié il cesse de réplication des données à partir d’ordinateurs principales afin que n’importe quel delta de données ne seront pas transférée après qu’un basculement non planifié commence. En outre si vous exécutez un basculement non planifié sur un plan de récupération il s’exécutera jusqu'à la fin, même si une erreur se produit.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer pour se connecter aux ordinateurs virtuels d’Azure après basculement

Si vous souhaitez vous connecter aux ordinateurs virtuels Azure en utilisant le protocole RDP après le basculement, assurez-vous que vous effectuez les opérations suivantes :

**Sur l’ordinateur local avant le basculement**:

- Pour un accès via internet activent le protocole RDP, assurez-vous que les règles de protocoles TCP et UDP sont ajoutées pour le **Public**et que RDP est autorisé dans le **Pare-feu Windows** -> **les applications autorisées et fonctionnalités** pour tous les profils.
- Pour l’accès via une connexion de site à site activent le protocole RDP sur l’ordinateur et vous assurer que le protocole RDP est autorisé dans le **Pare-feu Windows** -> **les applications autorisées et fonctionnalités** pour les réseaux **privé** et de **domaine** .
- Installation de l' [agent d’Azure VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) sur l’ordinateur local.
- [Installer manuellement le service de la mobilité](#install-the-mobility-service-manually) sur les machines au lieu d’utiliser le serveur de traitement pour pousser le service automatiquement. C’est parce que l’installation par diffusion se produit uniquement après que l’ordinateur est activée pour la réplication.
- Assurez-vous que l’option stratégie de SAN du système d’exploitation est définie sur OnlineAll. [Pour en savoir plus]( https://support.microsoft.com/kb/3031135)
- Désactiver le service IPSec avant d’exécuter le basculement.

**Sur l’Azure VM après basculement sur incident**:

- Ajouter un point de terminaison public pour le protocole RDP (le port 3389) et spécifiez les informations d’identification pour la connexion.
- Assurez-vous que vous n’avez pas les stratégies de domaine qui vous empêchent de vous connecter à un ordinateur virtuel à l’aide d’une adresse publique.
- Essayez de vous connecter. Si vous ne pouvez pas vous connecter Vérifiez que l’ordinateur virtuel est en cours d’exécution. Pour plus de conseils de dépannage de lire cet [article](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


Si vous souhaitez accéder à un ordinateur virtuel de Azure exécutant Linux après le basculement à l’aide d’un SSH client (ssh), effectuez les opérations suivantes :

**Sur l’ordinateur local avant le basculement**:

- Assurez-vous que le service de Shell sécurisé sur l’ordinateur virtuel d’Azure est défini pour démarrer automatiquement au démarrage du système.
- Vérifiez que les règles de pare-feu autorisent une connexion SSH.

**Sur l’Azure VM après basculement sur incident**:

- Les règles du groupe de sécurité réseau sur l’échec de la machine virtuelle et le sous-réseau Azure auquel il est connecté est nécessaire autoriser les connexions entrantes sur le port SSH.
- Un point de terminaison public doit être créé pour autoriser les connexions entrantes sur le port SSH (port TCP 22 par défaut).
- Si la machine virtuelle est accessible via une connexion VPN (Express itinéraire ou VPN de site à site) le client peut être utilisé pour se connecter directement à la machine virtuelle via SSH.

**Sur l’Azure Windows/Linux VM après basculement sur incident**:

Si vous avez associé l’ordinateur virtuel ou le sous-réseau auquel l’ordinateur appartient à un groupe de sécurité réseau, assurez-vous que le groupe de sécurité du réseau a une règle de trafic sortant pour autoriser HTTP/HTTPS. Assurez-vous également que le serveur DNS du réseau pour les machines virtuelles est mise en échec de plus est configuré correctement. Sinon le basculement peut expirer avec erreur-« Tâche PreFailoverWorkflow WaitForScriptExecutionTask a expiré ». Pour comprendre cela en détail, reportez-vous à section de récupération dans [guide de dépannage et de surveillance](site-recovery-monitoring-and-troubleshooting.md#recovery).

## <a name="run-a-test-failover"></a>Exécutez un test de basculement

1. Basculer sur un seul ordinateur, dans les **paramètres** > **Répliquées des éléments**, cliquez sur la machine virtuelle > icône **+ basculement de Test** .

    ![Basculement de test](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. Pour basculer d’un plan de récupération, dans les **paramètres** > de**Plans de reprise**, droit le plan > **Test de basculement**. Pour créer une récupération envisagez de [suivre ces instructions](site-recovery-create-recovery-plans.md).

3. Dans le **Test de basculement** , sélectionnez réseau Azure auquel Azure VM sera connecté après basculement.
4. Cliquez sur **OK** pour lancer le basculement. Vous pouvez suivre la progression en cliquant sur la machine virtuelle pour ouvrir ses propriétés ou sur le projet de **Test de basculement** dans le nom de coffre-fort > **paramètres** > **travaux** > **tâches de récupération de Site**.
5. Lorsque le basculement atteint l’état **de test terminée** , effectuez les opérations suivantes :

    1. Permet d’afficher la machine virtuelle de réplica dans le portail Azure. Vérifiez que l’ordinateur virtuel démarre correctement.
    2. Si vous avez défini pour les ordinateurs virtuels de l’accès à votre réseau local, vous pouvez lancer une connexion Bureau à distance sur l’ordinateur virtuel.
    3. Cliquez sur **tester par terminé** pour terminer.

        ![Basculement de test](./media/site-recovery-vmware-to-azure/test-failover6.png)


    4. Cliquez sur **Notes** pour enregistrer et enregistrer les observations associées avec le basculement de test.
    5. Cliquez sur **le test de basculement est achevé** nettoie automatiquement l’environnement de test. Après cela, le basculement de test affiche un état **terminé** .
    6.  À ce stade, tous les éléments ou ordinateurs virtuels créés automatiquement lors de la récupération de Site pendant le basculement du test sont supprimés. Tous les éléments supplémentaires que vous avez créé pour le test de basculement ne sont pas supprimés.

    > [AZURE.NOTE] Si un basculement sur incident test continue de plus de deux semaines, il est rempli en vigueur.


6. Une fois le basculement terminé vous devez également être en mesure de voir le réplica Azure machine apparaît dans le portail Azure > **Machines virtuelles**. Il se peut que vous devez vous assurer que la machine virtuelle est la taille appropriée, auquel il est connecté au réseau approprié, et qu’il s’exécute.
7. Si vous [prêt pour les connexions après basculement](#prepare-to-connect-to-azure-vms-after-failover) vous devez être en mesure de se connecter à la machine virtuelle d’Azure.

## <a name="monitor-your-deployment"></a>Surveillez votre déploiement

Voici comment vous pouvez contrôler les paramètres de configuration, l’état et la santé pour votre déploiement de récupération de Site :

1. Cliquez sur le nom de coffre-fort pour accéder à **l’éducation** du tableau de bord. Dans ce tableau de bord, vous pouvez travaux restauration du Site, état de la réplication, les plans de récupération, l’intégrité du serveur et événements.  Vous pouvez personnaliser Essentials pour afficher les carreaux et les dispositions qui sont plus utiles, y compris l’état des autres coffres-forts de sauvegarde et de récupération de Site.<br>
![Essentials](./media/site-recovery-vmware-to-azure/essentials.png)

2. Dans la mosaïque de **la santé** , vous pouvez surveiller des serveurs de site (serveurs VMM ou configuration) qui rencontrent le problème et les événements déclenchés par la récupération de Site au cours des 24 dernières heures.
3. Vous pouvez gérer et contrôler la réplication dans **Réplication des éléments**, des **Plans de reprise**, et les **Tâches de récupération de Site** dispose en mosaïque. Vous pouvez approfondir les travaux dans les **paramètres** -> **travaux** -> **Tâches de récupération de Site**.


## <a name="deploy-additional-process-servers"></a>Déployer des serveurs de traitement supplémentaires

Si vous avez à faire évoluer votre déploiement au-delà de 200 machines d’origine ou d’une évolution du total journalier de plus de 2 To, vous aurez besoin de serveurs supplémentaires pour gérer le volume de trafic.

Vérifiez les [recommandations de taille pour les serveurs de traitement](#size-recommendations-for-the-process-server) et suivez ces instructions pour définir le serveur de traitement. Après avoir configuré le serveur, vous allez migrer des machines d’origine pour l’utiliser.

### <a name="install-an-additional-process-server"></a>Installer un serveur de processus supplémentaires

1. Dans les **paramètres** > **serveurs de récupération de Site** , cliquez sur le serveur de configuration > **serveur de traitement**.

    ![Ajoutez le serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. Dans **Type de serveur** , cliquez sur **le serveur de traitement (sur site)**.

    ![Ajoutez le serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Téléchargez le fichier d’installation Site récupération unifiée et exécutez-le pour installer le serveur de traitement et de l’enregistrer dans le coffre-fort.
4. Dans **avant de commencer** , sélectionnez **Ajouter des serveurs supplémentaires de processus à l’échelle du déploiement**.
5. Terminez l’Assistant de la même manière que vous l’avez fait lorsque vous [Configurez](#step-2-set-up-the-source-environment) le serveur de configuration.

    ![Ajoutez le serveur de traitement](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. Dans les **Détails de Configuration de serveur** , spécifiez l’adresse IP du serveur de configuration, et le mot de passe. Pour obtenir le mot de passe exécuter ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** sur le serveur de configuration.

    ![Ajoutez le serveur de traitement](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migration des ordinateurs pour utiliser le nouveau serveur de processus

1. Dans les **paramètres** > **serveurs de récupération de Site** , cliquez sur le serveur de configuration et puis développez **serveurs de traitement**.

    ![Mise à jour du serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. Sélectionnez le serveur de processus en cours d’utilisation, puis cliquez sur **Basculer**.

    ![Mise à jour du serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. Dans **Sélectionnez le serveur du processus cible**, sélectionnez le nouveau serveur de processus que vous souhaitez utiliser, puis sélectionnez les ordinateurs virtuels qui gère le nouveau serveur de processus. Cliquez sur l’icône d’informations pour obtenir des informations sur le serveur. Pour vous aider à prendre des décisions de chargement, l’espace moyen nécessaire à la réplication de chaque ordinateur virtuel sélectionné vers le nouveau serveur de processus s’affiche. Cliquez sur la case à cocher pour démarrer la réplication vers le nouveau serveur de processus.

## <a name="vmware-account-permissions"></a>Autorisations de compte de VMware

Le serveur de traitement peut découvrir automatiquement les ordinateurs virtuels sur un serveur vCenter. Pour exécuter une découverte automatique vous devrez [définir un rôle (Azure_Site_Recovery)](#prepare-an-account-for-automatic-discovery) permettre la récupération de Site accéder au serveur VMware. Notez que si vous devez uniquement migrer des machines VMware vers Azure et n’est nécessaire à la restauration automatique d’Azure, vous pouvez définir un rôle en lecture seule est suffisant. Les autorisations de rôle requis sont résumées dans le tableau suivant.

**Rôle** | **Détails** | **Autorisations**
--- | --- | ---
Rôle de Azure_Site_Recovery | Découverte de VMware VM |Attribuer ces privilèges pour le serveur du centre-v :<br/><br/>Magasin de données -> Allocate espace, parcourir le magasin de données, les opérations de fichier de niveau faible., suppression du fichier, mise à jour des fichiers de machine virtuelle<br/><br/>Réseau -> attribuer de réseau<br/><br/>Ressources -> machine virtuelle d’affecter au pool de ressources, migration de machine virtuelle hors tension, la migration de machines virtuelles sous tension<br/><br/>Tâches -> créer une tâche, la tâche de mise à jour<br/><br/>Machine virtuelle -> Configuration<br/><br/>Machine virtuelle -> interagir -> répondre à la question, périphérique connexion., configurer un CD, disquettes configurer, alimentation hors tension sur, installer les outils VMware<br/><br/>Machine virtuelle -> stock -> Annuler l’inscription de la créer, Registre,<br/><br/>Machine virtuelle -> Provisioning -> Autoriser télécharger la machine virtuelle, de téléchargement des fichiers de machine virtuelle d’autoriser<br/><br/>Machine virtuelle -> Snapshots -> Supprimer les snapshots
rôle d’utilisateur vCenter | Découverte de VMware VM/basculement sans arrêt de la machine virtuelle de source | Attribuer ces privilèges pour le serveur du centre-v :<br/><br/>Objet de centre de données -> propager vers l’objet enfant, rôle = lecture seule <br/><br/>L’utilisateur est affecté au niveau du centre de données et a donc accès à tous les objets dans le centre de données.  Si vous souhaitez restreindre l’accès, affecter le rôle **Aucun accès** à l’objet de **propager aux enfants** pour les objets enfants (vSphere hôtes, magasins de données, ordinateurs virtuels et réseaux).
rôle d’utilisateur vCenter | Basculement et restauration automatique | Attribuer ces privilèges pour le serveur du centre-v :<br/><br/>Objet de centre de données – les propager vers l’objet enfant, rôle = Azure_Site_Recovery<br/><br/>L’utilisateur est affecté au niveau du centre de données et a donc accès à tous les objets dans le centre de données.  Si vous souhaitez restreindre l’accès, attribuer le rôle de **pas d’accès** avec la **propager à un objet enfant** à l’objet enfant (vSphere hôtes, magasins de données, ordinateurs virtuels et réseaux).  
## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](site-recovery-failover.md) sur les différents types de basculement.
- [En savoir plus sur la restauration automatique](site-recovery-failback-azure-to-vmware.md) pour mettre votre a échoué sur des ordinateurs exécutant dans Azure à votre environnement sur site.

## <a name="third-party-software-notices-and-information"></a>Les informations et les avis de logiciels tiers

Ne pas traduire ou localiser

Le logiciel et le microprogramme utilisé dans le produit ou service Microsoft repose sur ou incorpore des matières à partir des projets répertoriés ci-dessous (collectivement, « Code tiers »).  Microsoft est l’auteur de pas d’origine du Code tiers.  La notice de copyright d’origine et de la licence, dans lesquelles Microsoft a reçu un tel Code de tierce partie, sont définis ci-dessous.

Les informations contenues dans la Section A sont en ce qui concerne les composants tiers Code des projets répertoriés ci-dessous. Ces licences et ces informations sont fournies à titre d’information uniquement.  Ce Code de tierce partie est en cours relicensed pour vous par Microsoft sous les termes de licence de logiciel Microsoft pour le produit ou service Microsoft.  

Les informations dans la Section B sont concernant des composants de Code de tiers qui sont en cours mis à votre disposition par Microsoft sous les termes de la licence d’origine.

Vous trouverez le fichier complet sur le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft réserve tous les droits non expressément concédés par les présentes, que ce soit par implication, estoppel ou autrement.
