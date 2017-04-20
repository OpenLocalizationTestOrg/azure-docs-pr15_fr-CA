<properties
    pageTitle="Migrer des machines virtuelles de Azure IaaS à partir d’une seule région Azure à un autre avec la récupération de Site | Microsoft Azure"
    description="Récupération de Site Azure permet de migrer des machines virtuelles de Azure IaaS à partir d’une seule région Azure vers un autre."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="raynew"/>

#  <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrer des machines virtuelles de Azure IaaS entre régions Azure avec récupération de Site Azure

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans restauration du Site Azure ! Utilisez cet article si vous souhaitez effectuer la migration entre les régions Azure Azure VM. Avant de commencer, notez que :

- Azure dispose de deux modèles de déploiement différentes pour la création et l’utilisation des ressources : le Gestionnaire de ressources Azure et classique. Azure dispose également de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement. Les étapes de base pour la migration sont les mêmes si vous configurez une récupération de Site dans le Gestionnaire de ressources ou dans le standard. Toutefois, les instructions de l’interface utilisateur et les captures d’écran dans cet article sont pertinents pour le portail Azure.
- **Actuellement vous ne pouvez migrer à partir d’une région à l’autre. Vous pouvez basculer des ordinateurs virtuels à partir d’une seule région Azure à un autre, mais vous ne pouvez pas restauré les à nouveau.**
- Les instructions de migration dans cet article sont basées sur les instructions relatives à la réplication d’une machine physique à Azure. Elle inclut des liens vers les étapes [répliquer des ordinateurs virtuels VMware ou des serveurs physiques vers Azure](site-recovery-vmware-to-azure.md), qui décrit comment répliquer un serveur physique dans le portail Azure.
- Si vous configurez des restauration du Site portail classique, suivez les instructions détaillées dans [cet article](site-recovery-vmware-to-azure-classic.md). **Vous ne devez plus utiliser** les instructions de cet [article hérité](site-recovery-vmware-to-azure-classic-legacy.md).

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites"></a>Conditions préalables

Voici ce dont vous avez besoin pour ce déploiement :

- **Serveur de configuration**: une VM sur site Windows Server 2012 R2 qui joue le rôle du serveur de configuration en cours d’exécution. Vous installez également les autres composants de récupération de Site (y compris le serveur de traitement et un serveur cible maître) sur cet ordinateur virtuel. Plus à [l’architecture de scénario](site-recovery-vmware-to-azure.md#scenario-architecture) et les [conditions préalables de configuration serveur](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Machines virtuelles de IaaS**: le VMs vous souhaitez migrer. Vous migrez ces ordinateurs virtuels en les traitant comme des machines physiques.

## <a name="deployment-steps"></a>Étapes de déploiement

Cette section décrit les étapes de déploiement dans le nouveau portail Azure. Si vous avez besoin de ces étapes de déploiement pour la récupération de Site dans le portail classique, reportez-vous à [cet article](site-recovery-vmware-to-azure-classic.md).

1. [Créer un coffre-fort](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Déployer une configuration de serveur](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Une fois que vous avez déployé le serveur de configuration, vérifiez qu’il peut communiquer avec les ordinateurs virtuels que vous souhaitez faire migrer.
4. [Configurer les paramètres de réplication](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Créer une stratégie de réplication et les affecter au serveur de configuration.
5. [Installer le service de la mobilité](site-recovery-vmware-to-azure.md#step-6-replication-application). Chaque ordinateur virtuel que vous souhaitez protéger doit être installé le service de mobilité. Ce service envoie des données vers le serveur de traitement. Le service de la mobilité peut installé manuellement ou poussé et installé automatiquement par le serveur lorsque la protection de la machine virtuelle est activée. Règles de pare-feu sur les ordinateurs virtuels que vous voulez migrer doivent être configurés pour autoriser l’installation par diffusion de ce service.
6. [Activer la réplication](site-recovery-vmware-to-azure.md#enable-replication). Activer la réplication pour les ordinateurs virtuels que vous souhaitez migrer. Vous pouvez découvrir les ordinateurs virtuels IaaS que vous souhaitez migrer vers Azure à l’aide de l’adresse IP privée des ordinateurs virtuels. Pour trouver cette adresse sur le tableau de bord de machine virtuelle dans Azure. Lorsque vous activez la réplication, vous définissez le type d’ordinateur pour les ordinateurs virtuels comme des machines physiques.
7. [Exécuter un basculement non planifié](site-recovery-failover.md#run-an-unplanned-failover). Après que la réplication initiale est terminée, vous pouvez exécuter un basculement non planifié à partir d’une seule région Azure à un autre. Si vous le souhaitez, vous pouvez créer un plan de récupération et exécuter un basculement non planifié, pour faire migrer des machines virtuelles entre les régions. [En savoir plus](site-recovery-create-recovery-plans.md) sur les plans de reprise.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les autres scénarios de réplication de [Quel est la récupération de Site Azure ?](site-recovery-overview.md)
