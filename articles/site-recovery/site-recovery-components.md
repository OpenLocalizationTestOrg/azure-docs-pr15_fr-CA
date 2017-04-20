<properties
    pageTitle="Comment fonctionne la récupération de Site ? | Microsoft Azure"
    description="Cet article fournit une vue d’ensemble de l’architecture de récupération de Site"
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
    ms.topic="get-started-article"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="how-does-azure-site-recovery-work"></a>Comment fonctionne la récupération de Site Azure ?

Lisez cet article pour comprendre l’architecture sous-jacente du service de récupération de Site Azure et les composants qui fonctionnent. 

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Vue d’ensemble

Les organisations ont besoin d’une stratégie BCDR qui détermine comment les applications, les charges de travail et les données restent en cours d’exécution et disponible pendant les temps d’arrêt planifiés et non planifiés et récupérer dès que possible dans des conditions normales de fonctionnement. Votre stratégie de BCDR doit conserver les données d’entreprise d’et s’assurer que les charges de travail restent disponibles en permanence en cas de reprise après sinistre. 

Récupération de site est un service Azure qui participe à votre stratégie de BCDR par l’orchestration de réplication de serveurs physiques des locaux et des ordinateurs virtuels vers le nuage (Azure) ou à un centre de données secondaire. Lorsque des pannes se produisent dans votre emplacement principal, vous basculent vers l’emplacement secondaire afin que les applications et charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’il est retourné à un fonctionnement normal. Pour en savoir plus [ce qu’est la récupération de Site ?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Restauration du site portail Azure

Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et l’utilisation des ressources : le modèle de gestionnaire de ressources Azure et le modèle de gestion de services classique. Azure dispose également de deux portails – [Azure portal classique](https://manage.windowsazure.com/) qui prend en charge le modèle de déploiement classique et le [portail Azure](https://portal.azure.com) avec prise en charge pour les deux modèles de déploiement.

Récupération de site est disponible dans le portail classique et le portail Azure. Dans le portail classique Azure vous pouvez prendre en charge de récupération de Site avec le modèle d’administration classique de services. Dans le portail Azure vous pouvez prendre en charge le modèle classique ou le déploiement du modèle de ressource. [Pour en savoir plus](site-recovery-overview.md#site-recovery-in-the-azure-portal) sur le déploiement avec le portail Azure.

Les informations contenues dans cet article s’applique aux déploiements de portails classiques et Azure. Différences sont indiquées le cas échéant.

## <a name="deployment-scenarios"></a>Scénarios de déploiement

Récupération de site peut être déployée pour orchestrer la réplication dans un certain nombre de scénarios :

- **Les machines virtuelles VMware de répliquer**: vous pouvez répliquer les machines virtuelles VMware sur site vers Azure, ou à un centre de données secondaire.
- - **Répliquer les machines physiques**: vous pouvez répliquer les machines physiques exécutant Windows ou Linux à Azure ou à un centre de données secondaire. Le processus de réplication des machines physiques est presque la même que le processus de réplication des ordinateurs virtuels VMware
- **Répliquer des ordinateurs virtuels Hyper-V (sans VMM)**: vous pouvez répliquer des ordinateurs virtuels Hyper-V qui ne sont pas gérés par VMM pour Azure.
- **Répliquer des ordinateurs virtuels Hyper-V managées dans des nuages de System Center VMM**: vous pouvez répliquer les machines virtuelles de locaux Hyper-V en cours d’exécution sur les serveurs des hôtes Hyper-V dans les nuages VMM à Azure ou à un centre de données secondaire. Vous pouvez répliquer à l’aide du standard de Hyper-V Replica ou réplication SAN.
- **Migration de machines virtuelles**: vous pouvez utiliser le Site récupération pour [migrer des machines virtuelles de IaaS Azure](site-recovery-migrate-azure-to-azure.md) entre les régions ou pour [migrer des instances de l’AWS Windows](site-recovery-migrate-aws-to-azure.md) Azure IaaS VMs. Actuellement, seule migration est pris en charge qui signifie que vous pouvez basculer vers ces ordinateurs virtuels, mais vous ne pouvez pas les restauré.

Récupération de site peut répliquer la plupart des applications s’exécutant sur ces ordinateurs virtuels et les serveurs physiques. Vous pouvez obtenir un intégral résumé des applications prises en charge dans [les charges de travail reprise de Site Azure peut protéger ?](site-recovery-workload.md)


## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Réplication vers Azure : VMware machines virtuelles ou des serveurs physiques Windows/Linux

Il existe plusieurs façons de répliquer les machines virtuelles de VMware avec récupération de Site.

- **L’utilisation du portail Azure**-lors du déploiement de récupération de Site dans le portail Azure vous pouvez échouer sur les ordinateurs virtuels pour le stockage de gestionnaire de service classique ou au Gestionnaire de ressources. Réplication des machines virtuelles de VMware dans le portail Azure apporte un certain nombre d’avantages, notamment la possibilité de répliquer vers classique ou de stockage du Gestionnaire de ressources dans Azure. [En savoir plus](site-recovery-vmware-to-azure.md).
- **L’utilisation du portail classique**-vous pouvez déployer la récupération de Site dans le portail classique à l’aide d’une expérience améliorée. Cette option doit être utilisée pour tous les nouveaux déploiements dans le portail classique. Dans ce déploiement vous pouvez échouer uniquement sur les machines virtuelles au stockage classique dans Azure et non au stockage du Gestionnaire de ressources. [En savoir plus](site-recovery-vmware-to-azure-classic.md). Il existe également une [expérience existant](site-recovery-vmware-to-azure-classic-legacy.md) pour la configuration de la réplication VMware dans le portail classique. Cela ne devrait pas être utilisé pour les nouveaux déploiements.  Si vous avez déjà déployé à l’aide de l’expérience hérités [en savoir plus sur la migration](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) au déploiement amélioré.



Contraintes architecturales pour le déploiement de récupération de Site pour répliquer les serveurs VMware VM/physiques dans le portail Azure ou l’Azure portal classique (amélioré) sont similaires, avec quelques différences :

- Si vous déployez dans Azure portal, vous pouvez répliquer au stockage basé sur le Gestionnaire de ressources et utiliser le Gestionnaire de ressources réseaux pour connecter les ordinateurs virtuels d’Azure après le basculement.
- Lorsque vous déployez dans le portail Azure les deux LRS et stockage GRS est pris en charge. Dans le portail classique GRS est requis.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.


Voici ce dont vous aurez besoin :

- **Compte Azure**: vous devez disposer d’un compte Microsoft Azure.
- **Stockage Azure**: vous devez posséder un compte de stockage Azure pour stocker les données répliquées. Vous pouvez utiliser un compte classique ou un compte de gestionnaire de ressources de stockage. Le compte peut être LRS ou GRS lorsque vous déployez dans le portail Azure. Les données répliquées sont stockées dans le stockage Azure et d’Azure VM sont lancé lors du basculement. 
- **Réseau Azure**: vous aurez besoin d’un réseau virtuel Azure qui se connectera à Azure VM lorsqu’elles sont créées lors du basculement. Dans le portail Azure, ils peuvent être créés dans le modèle de gestionnaire de service classique ou dans le modèle de gestionnaire de ressources de réseaux.
- **Serveur de configuration sur site**: vous aurez besoin d’un ordinateur local Windows Server 2012 R2 qui exécute le serveur de configuration et d’autres composants de la récupération de Site. Si vous utilisez la réplication VMware VM, ce doit être une VM VMware hautement disponible. Si vous souhaitez répliquer les serveurs physiques, l’ordinateur peut être physique. Ces composants de récupération de Site seront installés sur l’ordinateur :
    - **Serveur de configuration**: coordonne la communication entre votre environnement local et les Azure et gère la réplication des données et récupération.
    - **Serveur de traitement**: sert de passerelle de réplication. Il reçoit des données de réplication à partir des machines d’origine protégée, il optimise avec la mise en cache, de compression et de cryptage et envoie les données vers le stockage Azure. Il gère l’installation par diffusion du service de mobilité pour les ordinateurs protégés et vous effectue la découverte automatique des ordinateurs virtuels VMware. À mesure que votre déploiement augmente, vous pouvez ajouter des serveurs de plus de processus dédié distinct pour gérer l’augmentation du volume du trafic de réplication.
    - **Maître du serveur cible**: gère les données de réplication au cours de la restauration à partir d’Azure. 
- **Ordinateurs virtuels VMware ou les serveurs physiques pour répliquer**: chaque machine que vous voulez répliquer vers Azure devez du composant service de mobilité. Ce service capture les écritures de données sur la machine et les transmet au serveur de traitement. Ce composant peut être installé manuellement, ou peut être poussé et installé automatiquement par le serveur de traitement lorsque vous activez la réplication pour une machine.
- **serveur d’hôtes/vCenter vSPhere**: vous aurez besoin d’un ou plusieurs serveurs hôtes vSphere ordinateurs virtuels VMware en cours d’exécution. Nous vous recommandons de déployer un serveur vCenter pour gérer ces hôtes.
- **Retour arrière**: Voici ce dont vous avez besoin :
    - **La restauration automatique vers physique n’est pas pris en charge**: cela signifie que si vous basculez sur des serveurs physiques vers Azure et que vous souhaitez puis restauré, vous devez basculer vers une VM VMware. Vous ne pouvez pas restauré sur un serveur physique. Vous aurez besoin d’un Azure VM vers, et si vous n’avez pas déployer le serveur de configuration sous la forme d’une VM VMware vous aurez besoin configurer un serveur cible principal distinct comme une VM VMware. Ceci est nécessaire parce que le serveur maître cible interagit et s’attache à stockage VMware afin de restaurer les disques à une VM de VMware.
    - - **Serveur temporaire dans Azure**: Si vous souhaitez revenir à partir d’Azure d’échouer après le basculement, vous devez définir un Azure VM configuré comme un serveur de traitement, pour gérer la réplication à partir d’Azure. Vous pouvez supprimer cet ordinateur virtuel une fois la restauration terminée.
    - **Connexion VPN**: de la restauration, vous aurez besoin d’une connexion VPN (ou le ExpressRoute d’Azure) configurer à partir du réseau Azure sur le site local.
    - **Distincts sur site principal serveur cible**: le serveur cible principal sur site gère le retour arrière. Le serveur cible principale est installé par défaut sur le serveur d’administration, mais si vous êtes échouent en plus grand volume du trafic vous devez configurer un serveur cible principale de locaux séparés à cet effet.

**Architecture générale**

![Amélioré](./media/site-recovery-components/arch-enhanced.png)

**Composants de déploiement**

![Amélioré](./media/site-recovery-components/arch-enhanced2.png)

**Retour arrière**

![Restauration améliorée](./media/site-recovery-components/enhanced-failback.png)


- [En savoir plus](site-recovery-vmware-to-azure.md#azure-prerequisites) sur la configuration requise pour le déploiement de portail Azure.
- [En savoir plus](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sur les exigences de déploiement améliorées dans le portail classique.
- [En savoir plus](site-recovery-failback-azure-to-vmware.md) sur la restauration dans le portail de Auzre.
- [En savoir plus](site-recovery-failback-azure-to-vmware-clas- [Learn more](site-recovery-failback-azure-to-vmware-classic.md) about failback in the Auzre portal.sic.md) sur la restauration du portail classique.

## <a name="replicate-to-azure-hyper-v-vms-not-managed-by-vmm"></a>Réplication vers Azure : ordinateurs virtuels Hyper-V en non gérés par VMM

Vous pouvez répliquer des ordinateurs virtuels Hyper-V qui ne sont pas gérés par System Center VMM pour Azure avec récupération de Site comme suit :

- **L’utilisation du portail Azure**-lors du déploiement de récupération de Site dans le portail Azure vous pouvez échouer sur les ordinateurs virtuels vers le stockage classique ou au Gestionnaire de ressources. [En savoir plus](site-recovery-hyper-v-site-to-azure.md).
- **L’utilisation du portail classique**-vous pouvez déployer la récupération de Site dans le portail classique. Dans ce déploiement vous pouvez échouer uniquement sur les machines virtuelles au stockage classique dans Azure et non au stockage du Gestionnaire de ressources. [En savoir plus](site-recovery-hyper-v-site-to-azure-classic.md).

L’architecture pour les déploiements est similaire, à ceci près que :

- Si vous déployez dans Azure portal, vous pouvez répliquer au stockage du Gestionnaire de ressources et utiliser le Gestionnaire de ressources réseaux pour connecter les ordinateurs virtuels d’Azure après le basculement.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.

Voici ce dont vous aurez besoin :

- **Compte Azure**: vous devez disposer d’un compte Microsoft Azure.
- **Stockage Azure**: vous devez posséder un compte de stockage Azure pour stocker les données répliquées. Dans le portail Azure, vous pouvez utiliser un compte classique ou un compte de gestionnaire de ressources de stockage. Dans le portail classique, vous pouvez utiliser uniquement un compte classique. Les données répliquées sont stockées dans le stockage Azure et d’Azure VM sont créés lorsqu’un basculement se produit.
- **Réseau Azure**: vous aurez besoin d’un réseau d’Azure qui se connectera à Azure VM lorsqu’elles sont créées après le basculement. 
- **Hôte Hyper-v**: vous aurez besoin d’au moins un serveur hôte de Windows Server 2012 R2 Hyper-V. Au cours du déploiement de la récupération de Site, vous allez installer le fournisseur de récupération de Site Azure et l’agent de Services de récupération de Microsoft Azure sur l’hôte.
- **Ordinateurs virtuels Hyper-V**: vous aurez besoin d’un ou plusieurs des ordinateurs virtuels sur le serveur hôte Hyper-V. Fournisseur de récupération de Site Azure et l’agent de Services de récupération Azure sur l’hôte Hyper-V au cours du déploiement de la récupération de Site. Le fournisseur de coordonnées et orchestre la réplication avec le service de récupération de Site sur internet. L’agent gère les données de réplication de données sur HTTPS 443. Communications à la fois le fournisseur et l’agent sont sécurisés et chiffrés. Les données répliquées dans le stockage Azure sont également cryptées.

**Architecture générale**

![Site de Hyper-V vers Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


- [En savoir plus](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) sur la configuration requise pour le déploiement de portail Azure.
- [En savoir plus](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) sur la configuration requise pour le déploiement de portail classique.



## <a name="replicate-to-azure-hyper-v-vms-managed-by-vmm"></a>Réplication vers Azure : ordinateurs virtuels Hyper-V en géré par VMM

Vous pouvez répliquer des ordinateurs virtuels de Hyper-V dans les nuages VMM sur Azure avec récupération de Site comme suit :

- **L’utilisation du portail Azure**-lors du déploiement de récupération de Site dans le portail Azure vous pouvez échouer sur les ordinateurs virtuels vers le stockage classique ou au Gestionnaire de ressources. [En savoir plus](site-recovery-vmm-to-azure.md).
- **L’utilisation du portail classique**-vous pouvez déployer la récupération de Site dans le portail classique. Dans ce déploiement vous pouvez échouer uniquement sur les machines virtuelles au stockage classique dans Azure et non au stockage du Gestionnaire de ressources. [En savoir plus](site-recovery-vmm-to-azure-classic.md).

L’architecture pour les déploiements est similaire, à ceci près que :

- Si vous déployez dans Azure portal, vous pouvez répliquer au stockage basé sur le Gestionnaire de ressources et utiliser le Gestionnaire de ressources réseaux pour connecter les ordinateurs virtuels d’Azure après le basculement.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.


Voici ce dont vous aurez besoin :

- **Compte Azure**: vous devez disposer d’un compte Microsoft Azure.
- **Stockage Azure**: vous devez posséder un compte de stockage Azure pour stocker les données répliquées. Dans le portail Azure, vous pouvez utiliser un compte classique ou un compte de gestionnaire de ressources de stockage. Dans le portail classique, vous pouvez utiliser uniquement un compte classique. Les données répliquées sont stockées dans le stockage Azure et d’Azure VM sont créés lorsqu’un basculement se produit.
- **Réseau Azure**: vous aurez besoin configurer le mappage du réseau afin que les ordinateurs virtuels d’Azure sont connectés à des réseaux appropriées lorsqu’elles sont créées après le basculement. 
- **Serveur VMM**: vous avez besoin d’un ou plusieurs serveurs VMM sur site s’exécutant sur System Center 2012 R2 et configuré avec un ou plusieurs nuages privés. Si vous déployez dans le Azure portail vous aurez besoin de logique et de réseaux de machine virtuelle vous pouvez configurer le mappage réseau. Dans le portail classique, cela est facultatif.  Un réseau de la machine virtuelle doit être lié à un réseau logique qui est associé avec le nuage.
- **Hôte Hyper-v**: vous aurez besoin d’un ou plusieurs serveurs hôtes Windows Server 2012 R2 Hyper-V dans le nuage VMM.
- **Ordinateurs virtuels Hyper-V**: vous aurez besoin d’un ou plusieurs des ordinateurs virtuels sur le serveur hôte Hyper-V.

**Architecture générale**

![VMM vers Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

- [En savoir plus](site-recovery-vmm-to-azure.md#azure-requirements) sur la configuration requise pour le déploiement de portail Azure.
- [En savoir plus](site-recovery-vmm-to-azure-classic.md#before-you-start) sur la configuration requise pour le déploiement de portail classique.




## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Réplication sur un site secondaire : VMware machines virtuelles ou des serveurs physiques 

Pour répliquer des ordinateurs virtuels VMware ou les serveurs physiques vers un site secondaire en tant que téléchargement InMage Scout qui est inclus dans l’abonnement Azure récupération de Site. Il peut être téléchargé à partir du portail Azure, ou à partir du portail classique Azure. 

Vous configurez les serveurs de composants dans chaque site (configuration, processus, cible principal) et installez l’Agent unifiée sur les ordinateurs que vous souhaitez répliquer. Après la réplication initiale l’agent sur chaque ordinateur envoie les modifications de réplication delta sur le serveur de traitement. Le serveur de traitement optimise les données et les transfère vers le serveur cible principal sur le site secondaire. Le serveur de configuration gère le processus de réplication.

Voici ce dont vous avez besoin :

**Compte Azure**: déploiement de ce scénario à l’aide de InMage Scout. Pour l’obtenir, vous devez un abonnement Azure. Après avoir créé un coffre-fort de récupération de Site vous Scout d’InMage de télécharger et installez les dernières mises à jour pour configurer le déploiement.
**Serveur de traitement (site principal)**: définir le composant du processus serveur sur votre site principal pour gérer la mise en cache, de compression et d’optimisation des données. Il gère également l’installation par diffusion de l’Agent unifiée aux ordinateurs que vous souhaitez protéger. 
**VMware ESX/ESXi et vCenter server (site principal)**: Si vous vous protégez des ordinateurs virtuels VMware vous aurez besoin d’un hyperviseur VMware équivalente de l’objet/ESXi et éventuellement de gérer les hyperviseurs VMware vCenter server.
- **Serveurs de machines virtuelles/physiques (site principal)**: serveurs physiques en machines virtuelles de VMware ou Windows/Linux à protéger est nécessitent à l’Agent unifiée est installé. L’Agent unifiée est également installé sur les ordinateurs faisant office de serveur de la cible principale. L’agent agit comme un fournisseur de la communication entre tous les composants. 
- - **Serveur de configuration (site secondaire)**: le serveur de configuration est le premier composant que vous installez, et il est installé sur le site secondaire pour gérer, configurer et contrôler votre déploiement, à l’aide du site Web de gestion ou de la console vContinuum. Il est uniquement un serveur de configuration unique dans un déploiement, et il doit être installé sur un ordinateur exécutant Windows Server 2012 R2.
- **vContinuum serveur (site secondaire)**: il est installé dans le même emplacement (site secondaire) que le serveur de configuration. Il fournit une console de gestion et de surveillance de votre environnement protégé. Dans une installation par défaut, le serveur vContinuum est le premier serveur de la cible principale et doté de l’Agent unifiée.
- **Le serveur cible (site secondaire) maître**: le serveur cible principale conserve des données répliquées. Il reçoit les données du serveur de traitement, crée une machine de réplica dans le site secondaire et conserve les points de rétention des données. Le nombre de serveurs cible principale dont vous avez besoin dépend le nombre d’ordinateurs que vous utilisez la protection. Si vous souhaitez basculer sur le site principal vous aurez besoin d’un serveur cible principal il y trop. 

**Architecture générale**

![VMware pour VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="replicate-to-a-secondary-site-hyper-v-vms-managed-by-vmm"></a>Réplication sur un site secondaire : ordinateurs virtuels Hyper-V en géré par VMM


Vous pouvez répliquer des ordinateurs virtuels Hyper-V qui sont gérés par System Center VMM sur un centre de données secondaire avec récupération de Site comme suit :

- **L’utilisation du portail Azure**-lors du déploiement de récupération de Site dans le portail Azure. [En savoir plus](site-recovery-hyper-v-site-to-azure.md).
- **L’utilisation du portail classique**-vous pouvez déployer la récupération de Site dans le portail classique. [En savoir plus](site-recovery-hyper-v-site-to-azure-classic.md).

L’architecture pour les déploiements est similaire, à ceci près que :

- Si vous déployez dans Azure portal, vous devez définir le mappage de réseau. Ceci est facultatif dans le portail classique.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.
- - Si vous déployez dans le Azure portail classique [mappage du stockage](site-recovery-storage-mapping.md) est disponible.

Voici ce dont vous aurez besoin :

- **Compte Azure**: vous devez disposer d’un compte Microsoft Azure.
- **Serveur VMM**: nous vous recommandons un serveur VMM sur le site principal et un sur le site secondaire, chacun contenant au moins un cloud privé de VMM. Le serveur doit être en cours d’exécution au moins System Center 2012 SP1 avec les dernières mises à jour et connecté à internet. Le profil de fonctionnalité de Hyper-V des nuages doivent être défini. Vous allez installer le fournisseur de récupération de Site Azure sur le serveur VMM. Le fournisseur de coordonnées et orchestre la réplication avec le service de récupération de Site sur internet. Les communications entre le fournisseur et Azure sont sécurisées et cryptées.
- **Hyper-V server**: les serveurs hôtes Hyper-V doivent se trouver dans les nuages VMM principales et secondaires. L’hôte de serveurs doivent être en cours d’exécution au moins Windows Server 2012 avec les dernières mises à jour installé et connecté à internet. Données sont répliquées entre les serveurs hôtes Hyper-V principales et secondaires sur le LAN ou VPN à l’aide de l’authentification Kerberos ou des certificats.  
- **Les machines protégé**: le serveur hôte source Hyper-V doit avoir au moins un ordinateur virtuel que vous souhaitez protéger.

**Architecture générale**

![Local à local](./media/site-recovery-components/arch-onprem-onprem.png)


- [En savoir plus](site-recovery-vmm-to-vmm.md#azure-prerequisites) sur les exigences de déploiement dans le portail Azure.
- - [En savoir plus](site-recovery-vmm-to-vmm-classic.md#before-you-start) sur les exigences de déploiement dans Azure portal classique.




## <a name="replicate-to-a-secondary-site-with-san-replication-hyper-v-vms-managed-by-vmm"></a>Réplication sur un site secondaire avec la réplication SAN : ordinateurs virtuels Hyper-V en géré par VMM

Vous pouvez répliquer des ordinateurs virtuels Hyper-V en gérés dans des nuages VMM sur un site secondaire à l’aide de la réplication SAN via le portail classique Azure. Ce scénario n’est pas actuellement pris en charge dans le nouveau portail Azure. 

Dans ce scénario lors du déploiement de la récupération de Site, vous allez installer le fournisseur de récupération de Site Azure sur les serveurs VMM. Le fournisseur de coordonnées et orchestre la réplication avec le service de récupération de Site sur internet. Données sont répliquées entre les baies de stockage principal et secondaire à l’aide de la réplication synchrone de SAN.

Voici ce dont vous aurez besoin :

**Compte Azure**: vous aurez besoin d’un abonnement Azure
- **Baie de stockage SAN**: une [prise en charge de la baie de stockage SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) géré par le serveur principal de VMM. Le SAN partage une infrastructure réseau avec une autre baie de stockage SAN dans le site secondaire.
- **Serveur VMM**: nous vous recommandons un serveur VMM sur le site principal et un sur le site secondaire, chacun contenant au moins un cloud privé de VMM. Le serveur doit être en cours d’exécution au moins System Center 2012 SP1 avec les dernières mises à jour et connecté à internet. Le profil de fonctionnalité de Hyper-V des nuages doivent être défini.
- **Hyper-V server**: les serveurs hôtes Hyper-V qui se trouve dans les nuages VMM principales et secondaires. L’hôte de serveurs doivent être en cours d’exécution au moins Windows Server 2012 avec les dernières mises à jour installé et connecté à internet.
- **Les machines protégé**: le serveur hôte source Hyper-V doit avoir au moins un ordinateur virtuel que vous souhaitez protéger.

**Architecture de réplication SAN**

![Réplication SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

[En savoir plus](site-recovery-vmm-san.md#before-you-start) sur les exigences de déploiement.
### <a name="on-premises"></a>Sur site



## <a name="hyper-v-protection-lifecycle"></a>Cycle de vie de protection de Hyper-V

Ce flux de travail montre le processus de protection, de réplication et de basculement des ordinateurs virtuels Hyper-V. 

1. **Activer la protection**: vous paramétrez le coffre-fort de la récupération de Site, configurer les paramètres de réplication pour un nuage VMM ou Hyper-V et activer la protection pour les machines virtuelles. Un travail appelé **Activation de la Protection** est établie et peut être surveillé dans l’onglet **tâches** . La tâche vérifie que l’ordinateur satisfait aux conditions requises et appelle ensuite la méthode [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) qui configure la réplication vers Azure avec les paramètres que vous avez configurés. La tâche **Activer la protection** appelle également la méthode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) pour initialiser une réplication complète de la machine virtuelle.
2. **La réplication initiale**: prise d’un instantané d’ordinateur virtuel et disques durs virtuels sont répliquées un par un jusqu'à ce qu’ils sont tous copiés vers Azure ou au centre de données secondaire. Le temps nécessaire pour effectuer ceci dépend de la taille de la mémoire virtuelle, la bande passante réseau et la méthode de réplication initiale. Si les modifications du disque se produisent pendant la réplication initiale le moniteur de réplication du réplica Hyper-V effectue le suivi de ces modifications en tant que journaux de réplication Hyper-V (.hrl) qui se trouvent dans le même dossier que les disques. Chaque disque possède un fichier de .hrl associés qui peuvent être envoyé vers le stockage secondaire. Notez que les fichiers journaux et de capture instantanée consomment des ressources de disque pendant la réplication initiale. Une fois la réplication initiale l’instantané d’ordinateur virtuel est supprimé et les modifications de disque delta dans le journal sont synchronisées et fusionnées.
3. **Protection de Finalize**: réplication initiale après la tâche de **protection de Finalize** configure réseau et autres paramètres de réplication après afin que l’ordinateur virtuel est protégé. Si vous effectuez une réplication vers Azure, vous devrez modifier les paramètres de l’ordinateur virtuel afin qu’il soit prêt pour le basculement. À ce stade, vous pouvez exécuter un test de basculement pour vérifier que tout fonctionne comme prévu.
4. **Réplication**: après la réplication initiale synchronisation delta commence, selon les paramètres de réplication. 
    - **Échec de la réplication**: si la réplication delta échoue, une réplication complète serait coûteuse en termes de bande passante ou l’heure, puis resynchronisation se produit. Pour exemple, si les fichiers .hrl atteint 50 % de la taille du disque la machine virtuelle sera marqué pour la resynchronisation. Resynchronisation réduit la quantité de données envoyées par le calcul des totaux de contrôle des ordinateurs virtuels source et cible et en l’envoyant uniquement le fichier delta. Une fois la resynchronisation terminée la réplication delta va reprendre. Par défaut, resynchronisation est planifiée pour s’exécuter automatiquement en dehors des heures de bureau, mais vous pouvez le resynchroniser manuellement un ordinateur virtuel.
    - **Erreur de réplication**: si une erreur de réplication se produit est une réitération intégré. Si elle est une erreur irrécupérable telle qu’une erreur d’authentification ou d’autorisation, ou une machine de réplica est dans un état non valide, aucun nouvelle tentative ne va être tentée. Si elle est une erreur récupérable comme une erreur de réseau ou la mémoire/espace disque insuffisant, puis une nouvelle tentative se produit avec l’augmentation des intervalles entre les tentatives (1, 2, 4, 8, 10 et puis toutes les 30 minutes).
4. **Basculements planifiées/imprévues**: vous pouvez exécuter les basculements planifiés ou non planifiés en fonction des besoins. Si vous exécutez un basculement planifié puis la source VMs sont arrêtés pour éviter toute perte de données. Après avoir créé les ordinateurs virtuels de réplica, ils sont placés dans une validation en état d’attente. Vous devez les valider pour terminer le basculement, sauf si vous utilisez la réplication avec le SAN, dans ce cas, commit est automatique. Une fois le site principal est en cours d’exécution la restauration automatique peut se produire. Si vous avez répliqué vers Azure inverse, la réplication est automatique. Dans le cas contraire vous lancer la réplication inverse manuellement.
 

![flux de travail](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Étapes suivantes

[Préparer pour le déploiement](site-recovery-best-practices.md)
