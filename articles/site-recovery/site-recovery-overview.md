<properties
    pageTitle="Quelle est la récupération de Site ? | Microsoft Azure"
    description="Fournit une vue d’ensemble du service de récupération de Site Azure et résume les scénarios de déploiement."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/13/2016"
    ms.author="raynew"/>

#  <a name="what-is-site-recovery"></a>Quelle est la récupération de Site ?

Bienvenue dans la récupération de Site Azure ! Cet article fournit une vue d’ensemble rapide du service Restauration du Site, et comment il contribue à votre entreprise.

Votre organisation a besoin d’une continuité d’activité et de la stratégie de rétablissement (BCDR) qui conservent des applications, des charges de travail et données sécurisées et disponibles pendant les interruptions de service planifiées et non planifiées et récupère dès que possible dans des conditions normales de fonctionnement. Récupération de site est un service Azure qui participe à cette stratégie.

Récupération de site orchestre la réplication des charges de travail des serveurs physiques sur site et les ordinateurs virtuels en cours d’exécution. Vous pouvez répliquer des serveurs et des ordinateurs virtuels à partir d’un centre de données principal vers le nuage (Azure), ou à un centre de données secondaire. Lorsque des pannes se produisent dans le site principal, vous basculent vers le site secondaire pour maintenir les charges de travail et les applications accessibles et disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’il est retourné à un fonctionnement normal.

## <a name="site-recovery-in-the-azure-portal"></a>Restauration du site portail Azure

Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et l’utilisation des ressources. Le modèle de gestionnaire de ressources Azure et le modèle de gestion de services classique. Azure dispose également de deux portails – [Azure portal classique](https://manage.windowsazure.com/) qui prend en charge le modèle de déploiement classique et le [portail Azure](https://portal.azure.com) prend en charge le standard et de modèles du Gestionnaire de ressources.

- Récupération de site est disponible dans le portail classique et le portail Azure.
- Dans le portail Azure classique, vous pouvez prendre en charge de récupération de Site avec le modèle d’administration classique de services.
- Dans le portail Azure, vous pouvez prendre en charge le modèle classique ou déploiements du Gestionnaire de ressources. 

Les informations contenues dans cet article s’applique aux déploiements de portails classiques et Azure. Différences sont indiquées le cas échéant.


## <a name="why-deploy-site-recovery"></a>Pourquoi déployer récupération de Site ?

Voici ce que la récupération de Site peut faire pour votre entreprise :

- **Simplifier le BCDR**, vous pouvez gérer la réplication, le basculement et la restauration de plusieurs charges de travail dans un seul emplacement dans le portail Azure. Récupération de site orchestre la réplication et le basculement sur incident mais n’intercepter vos données d’application ou dispose d’informations à ce sujet.
- **Réplication flexible de fournir**, à l’aide de la récupération de Site, vous pouvez répliquer des charges de travail en cours d’exécution sur les ordinateurs virtuels pris en charge de Hyper-V, machines virtuelles de VMware et des serveurs physiques Windows/Linux.
- **Faciliter la réplication des tests**: récupération de Site fournit des basculements tests pour prendre en charge des exercices de récupération après sinistre, sans affecter les environnements de production.
- **Basculement et la restauration**, vous pouvez exécuter les basculements planifiés pour les interruptions prévues avec une perte de données zéro ou basculement non planifié avec perte de données minimale (en fonction de la fréquence de réplication) pour les sinistres. Après le basculement, vous pouvez la restauration automatique à vos sites principaux. Récupération de site fournit des plans de récupération qui peuvent inclure des scripts et des classeurs d’automation Azure, afin que vous pouvez personnaliser le basculement et la reprise d’applications multicouches.
- **Éliminer un centre de données secondaire**, vous pouvez répliquer des charges de travail Azure plutôt que sur un site secondaire. Cela évite le coût et la complexité de la gestion d’un centre de données secondaire. Les données répliquées sont stockées dans le stockage Azure, avec tous les la résilience fournit. Machines virtuelles sont créées avec les données répliquées lors du basculement.
- **Intégration avec les technologies existantes de BCDR**, récupération de Site s’intègre avec d’autres fonctionnalités BCDR. Par exemple, vous pouvez utiliser la récupération de Site pour protéger le serveur principal de SQL Server de charges de travail d’entreprise, y compris la prise en charge native de SQL Server AlwaysOn, pour gérer le basculement des groupes de disponibilité.

## <a name="what-can-i-replicate"></a>Que puis-je répliquer ?

Voici un résumé de ce que vous pouvez répliquer à l’aide de la récupération de Site.

![Local à local](./media/site-recovery-overview/asr-overview-graphic.png)

**RÉPLIQUER** | **RÉPLIQUER VERS** 
---|---
Charges de travail s’exécutant sur des machines virtuelles de VMware sur site | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secondaire](site-recovery-vmware-to-vmware.md)
Charges de travail s’exécutant sur des ordinateurs virtuels Hyper-V de locaux gérés dans les nuages VMM  | [Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Site secondaire](site-recovery-vmm-to-vmm.md) 
La charge de travail en cours d’exécution sur des ordinateurs virtuels Hyper-V de locaux gérés dans des nuages VMM, avec le stockage SAN|  [Site secondaire](site-recovery-vmm-san.md)
Charges de travail s’exécutant sur des ordinateurs virtuels Hyper-V sur place, sans VMM | [Azure](site-recovery-hyper-v-site-to-azure.md)
Charges de travail s’exécutant sur des serveurs Windows/Linux physiques sur site | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secondaire](site-recovery-vmware-to-vmware.md)


## <a name="what-workloads-can-i-protect"></a>Les charges de travail puis-je protéger ?

Récupération de site permet de BCDR multi-applications, afin que les charges de travail et les applications continuent de fonctionner de manière cohérente lorsque des pannes se produisent. Fournit de la récupération de site :

- **Des snapshots cohérents avec l’application**: Machines répliquent à l’aide de snapshots cohérents au niveau application, pour les applications simples ou à plusieurs niveaux. Outre la capture des données sur disque, des snapshots cohérents avec l’application capture capture toutes les données en mémoire et toutes les transactions en cours.
- **Réplication près synchrone**: récupération de Site fournit la fréquence de réplication est aussi faible que 30 secondes pour Hyper-V et la réplication continue pour VMware.
- **Plans de reprise souples**— vous pouvez créer et personnaliser des plans de récupération avec des scripts externes et des actions manuelles. Intégration avec les procédures opérationnelles d’Azure Automation permettent de récupérer une pile d’ensemble de l’application d’un simple clic.
- **Intégration avec SQL Server AlwaysOn**: vous pouvez gérer le basculement des groupes de disponibilité dans les plans de récupération récupération de Site.
- **Bibliothèque d’Automation**: une riche bibliothèque d’Automation d’Azure propose des scripts prêt pour la production, spécifiques à l’application qui peuvent être téléchargés et intégrées à la récupération de Site.
- **Gestion de réseau simple**, gestion de réseau avancée dans la récupération de Site et Azure simplifie les exigences des applications réseau, y compris la réservation des adresses IP, configuration des programmes d’équilibrage de charge et l’intégration d’Azure Traffic Manager pour les basculements de réseau efficace.


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus de [les charges de travail reprise de Site peut protéger ?](site-recovery-workload.md)
- Pour en savoir plus sur l’architecture de récupération de Site dans [Site récupération fonctionnement ?](site-recovery-components.md)
 
