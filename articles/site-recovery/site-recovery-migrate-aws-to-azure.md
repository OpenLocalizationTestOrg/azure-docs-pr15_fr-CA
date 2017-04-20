<properties
    pageTitle="Migrer des machines virtuelles Windows d’Amazon Web Services vers Azure avec récupération de Site | Microsoft Azure"
    description="Cet article décrit comment migrer des machines virtuelles de Windows Azure à l’aide de la récupération de Site Azure exécutant dans Amazon Web Services (AWA)."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="08/22/2016"
    ms.author="raynew"/>

#  <a name="migrate-windows-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrer des machines virtuelles de Windows dans Amazon Web Services (AWS) pour Azure avec récupération de Site Azure

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans restauration du Site Azure. Cet article permet de migrer des instances de Windows en cours d’exécution dans AWS vers Azure avec récupération de Site. Avant de commencer, notez que :

- Azure dispose de deux modèles de déploiement différentes pour la création et l’utilisation des ressources : le Gestionnaire de ressources Azure et classique. Azure dispose également de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement. Les étapes de base pour la migration sont les mêmes si vous configurez une récupération de Site dans le Gestionnaire de ressources ou dans le standard. Toutefois, les instructions de l’interface utilisateur et les captures d’écran dans cet article sont pertinents pour le portail Azure.
- **Actuellement vous ne pouvez migrer à partir de l’AWS vers Azure. Vous pouvez basculer des machines virtuelles à partir de l’AWS vers Azure, mais vous ne pouvez pas restauré les à nouveau. Il n’y a pas de réplication en cours.**
- Les instructions de migration dans cet article sont basées sur les instructions relatives à la réplication d’une machine physique à Azure. Elle inclut des liens vers les étapes [répliquer des ordinateurs virtuels VMware ou des serveurs physiques vers Azure](site-recovery-vmware-to-azure.md), qui décrit comment répliquer un serveur physique dans le portail Azure.
- Si vous configurez des restauration du Site portail classique, suivez les instructions détaillées dans [cet article](site-recovery-vmware-to-azure-classic.md). **Vous ne devez plus utiliser** les instructions de cet [article hérité](site-recovery-vmware-to-azure-classic-legacy.md).

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## <a name="prerequisites"></a>Conditions préalables

Voici ce dont vous avez besoin pour ce déploiement

- **Serveur de configuration**: une VM sur site Windows Server 2012 R2 qui joue le rôle du serveur de configuration en cours d’exécution. Vous installez également les autres composants de récupération de Site (y compris le serveur de traitement et un serveur cible maître) sur cet ordinateur virtuel. Plus à [l’architecture de scénario](site-recovery-vmware-to-azure.md#scenario-architecture) et les [conditions préalables de configuration serveur](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Les instances EC2 VM**: les instances en cours d’exécution Windows que vous souhaitez migrer.

## <a name="deployment-steps"></a>Étapes de déploiement

Cette section décrit les étapes de déploiement dans le nouveau portail Azure. Si vous avez besoin de ces étapes de déploiement pour la récupération de Site dans le portail classique, reportez-vous à [cet article](site-recovery-vmware-to-azure-classic.md).

1. [Créer un coffre-fort](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Déployer une configuration de serveur](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Une fois que vous avez déployé le serveur de configuration, valider qu’il peut communiquer avec les ordinateurs virtuels que vous souhaitez faire migrer.
4. [Configurer les paramètres de réplication](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Créer une stratégie de réplication et les affecter au serveur de configuration.
5. [Installer le service de la mobilité](site-recovery-vmware-to-azure.md#step-6-replication-application). Chaque ordinateur virtuel que vous souhaitez protéger doit être installé le service de mobilité. Ce service envoie des données vers le serveur de traitement. Le service de la mobilité peut installé manuellement ou poussé et installé automatiquement par le serveur lorsque la protection de la machine virtuelle est activée. Sur les instances EC2 que vous souhaitez faire migrer des règles de pare-feu doivent être configurés pour autoriser l’installation par diffusion de ce service. Le groupe de sécurité pour les instances EC2 doit avoir les règles suivantes :

    ![règles de pare-feu](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Activer la réplication](site-recovery-vmware-to-azure.md#enable-replication). Activer la réplication pour les ordinateurs virtuels que vous souhaitez migrer. Vous pouvez découvrir les instances EC2 à l’aide d’adresses IP privées, auquel vous pouvez accéder à partir de la console EC2.
7. [Exécuter un basculement non planifié](site-recovery-failover.md#run-an-unplanned-failover). Après que la réplication initiale est terminée, vous pouvez exécuter un basculement non planifié à partir de l’AWS vers Azure pour chaque machine virtuelle. Si vous le souhaitez, vous pouvez créer un plan de récupération et exécuter un basculement non planifié, pour migrer des machines virtuelles de AWS vers Azure. [En savoir plus](site-recovery-create-recovery-plans.md) sur les plans de reprise.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les autres scénarios de réplication de [Quel est la récupération de Site Azure ?](site-recovery-overview.md)
