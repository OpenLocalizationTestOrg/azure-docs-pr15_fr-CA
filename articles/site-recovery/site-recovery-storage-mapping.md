<properties
    pageTitle="Mapper le stockage Azure Site récupération pour la réplication d’ordinateur virtuel Hyper-V entre les centres de données sur site | Microsoft Azure"
    description="Préparation de mappage de stockage pour la réplication d’ordinateur virtuel Hyper-V entre deux centres de données local avec récupération de Site Azure."
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
    ms.workload="storage-backup-recovery"
    ms.date="07/06/2016"
    ms.author="raynew"/>


# <a name="prepare-storage-mapping-for-hyper-v-virtual-machine-replication-between-two-on-premises-datacenters-with-azure-site-recovery"></a>Préparation de mappage de stockage pour la réplication d’ordinateur virtuel Hyper-V entre deux centres de données local avec récupération de Site Azure


Récupération de Site Azure contribue à votre stratégie d’entreprise après incident et de continuité d’activité (BCDR) de récupération par l’orchestration de réplication, le basculement et la récupération de machines virtuelles et des serveurs physiques. Cet article décrit le mappage de stockage, qui vous aide à vous rendre optimale utiliser de stockage lorsque vous utilisez la restauration du Site pour répliquer des ordinateurs virtuels Hyper-V entre les centres de données deux locaux VMM.

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Mappage du stockage est pertinente uniquement lorsque vous utilisez la réplication ordinateurs virtuels Hyper-V qui se trouvent dans des nuages VMM à partir d’un centre de données principal à un centre de données secondaire à l’aide de la réplication SAN ou Hyper-V Replica, comme suit :


- **Sur site pour la réplication locale avec Hyper-V Replica)** — Vous définissez stockage mappage par mappage de classifications de stockage sur une source et VMM serveurs ciblez pour effectuer les opérations suivantes :

    - **Stockage de cible d’identification pour les machines virtuelles de réplica**— ordinateurs virtuels seront répliqués vers une cible de stockage (SMB partager ou (CSVs) des volumes partagés de cluster) que vous choisissez.
    - **Placement des machines virtuelles réplica**: mappage du stockage est utilisé pour placer optimale des machines virtuelles de réplicas sur les serveurs hôtes Hyper-V. Machines virtuelles de réplica sera placés sur les hôtes pouvant accéder à la classification de stockage mappé.
    - **Aucun mappage de stockage**, si vous ne configurez pas le mappage de stockage, ordinateurs virtuels seront répliqués vers l’emplacement de stockage par défaut spécifié sur le serveur hôte Hyper-V associé avec la machine virtuelle de réplica.

- **Sur le site pour la réplication locale avec SAN**— vous définissez stockage mappage par mappage de pools de baies de stockage sur une source et VMM serveurs cibles.
    - **Pool de spécifier**— Spécifie le pool de stockage secondaire reçoit des données de réplication à partir du pool principal.
    - **Identifier les pools de stockage cible**— permet de s’assurer que les LUN d’un groupe de réplication source est répliqués vers le pool de stockage cible mappé de votre choix.

## <a name="set-up-storage-classifications-for-hyper-v-replication"></a>Paramétrer des classifications de stockage pour la réplication de Hyper-V

Lorsque vous utilisez Hyper-V Replica pour répliquer avec récupération de Site, vous mappez entre classifications de stockage sur les serveurs VMM source et cible, ou sur un seul serveur VMM si deux sites sont gérés par le même serveur VMM. Notez que :

- Lorsque le mappage est configuré correctement et que la réplication est activée, disque dur virtuel d’un ordinateur virtuel à l’emplacement principal seront répliqué vers le stockage dans l’emplacement cible mappé.
- Classifications de stockage doivent être disponibles pour les groupes d’hôtes situés dans des nuages de source et cible.
- Classifications n’est pas nécessaire d’avoir le même type de stockage. Par exemple, vous pouvez mapper une classification de source qui contient les partages SMB pour une classification de cible qui contient CSVs.
- Plus à la [façon de créer des classifications de stockage dans VMM](https://technet.microsoft.com/library/gg610685.aspx).

## <a name="example"></a>Exemple

Si les catégories sont correctement configurés dans VMM lorsque vous sélectionnez la source et la cible serveur VMM lors du mappage de stockage, les classifications de la source et la cible seront affiche. Voici un exemple de partages de fichiers de stockage et de secteurs d’une organisation grâce à deux emplacements à New York et de Chicago.

**Emplacement** | **Serveur VMM** | **Partage de fichiers (source)** | **Classification (source)** | **Mappé à** | **Partage de fichiers (cible)**
---|---|--- |---|---|---
New York | VMM_Source| SourceShare1 | GOLD | GOLD_TARGET | TargetShare1
 |  | SourceShare2 | ARGENT | SILVER_TARGET | TargetShare2
 | | SourceShare3 | BRONZE | BRONZE_TARGET | TargetShare3
Chicago | VMM_Target |  | GOLD_TARGET | Non mappé |
| | | SILVER_TARGET | Non mappé |
 | | | BRONZE_TARGET | Non mappé

Vous configurez ces sous l’onglet **Stockage de serveur** dans la page des **ressources** du portail récupération de Site.

![Configurer le mappage du stockage](./media/site-recovery-storage-mapping/storage-mapping1.png)

Dans cet exemple :
- Lorsqu’un un ordinateur virtuel de réplica est créé pour toutes les machines virtuelles sur un stockage GOLD (SourceShare1), il sera répliqué sur un stockage GOLD_TARGET (TargetShare1).
- Lorsqu’une machine virtuelle de réplica est créée pour toutes les machines virtuelles sur le stockage d’argent (SourceShare2), il sera répliqué sur un stockage SILVER_TARGET (TargetShare2) et ainsi de suite.

Les partages de fichiers réels et les classifications affectées dans VMM s’affichent dans la capture d’écran suivante.

![Classifications de stockage dans VMM](./media/site-recovery-storage-mapping/storage-mapping2.png)

## <a name="multiple-storage-locations"></a>Plusieurs emplacements de stockage

Si le classement cible est affecté à plusieurs partages SMB ou CSVs, l’emplacement de stockage optimal est sélectionné automatiquement lorsque la machine virtuelle est protégée. Si aucun stockage cible appropriée n’est disponible avec le classement spécifié, l’emplacement de stockage par défaut spécifié sur l’hôte Hyper-V permet de placer les disques durs virtuels de réplica.

Le tableau suivant affiche la classification de stockage et des volumes partagés en cluster sont configurés dans notre exemple.

**Emplacement** | **Classification** | **Stockage associé**
---|---|---
New York | GOLD | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p>
 | ARGENT | <p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p>
Chicago | GOLD_TARGET | <p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p>
 | SILVER_TARGET| <p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p>

Ce tableau résume le comportement lorsque vous activez la protection des machines virtuelles (VM1 - VM5) dans cet exemple d’environnement.

**Machine virtuelle** | **Stockage source** | **Classification de la source** | **Stockage de cibles mappés**
---|---|---|---
VM1 | C:\ClusterStorage\SourceVolume1 | GOLD | <p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Les deux GOLD_TARGET</p>
ORDINATEUR VIRTUEL 2 | \\FileServer\SourceShare1 | GOLD | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Les deux GOLD_TARGET</p>
VM3 | C:\ClusterStorage\SourceVolume2 | ARGENT | <p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p>
VM4 | \FileServer\SourceShare2 | ARGENT |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Les deux SILVER_TARGET</p>
VM5 | C:\ClusterStorage\SourceVolume3 | N/A | Aucun mappage, l’emplacement de stockage par défaut de l’hôte Hyper-V est utilisé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une meilleure compréhension de mappage de stockage, [Soyez prêt à déployer la récupération de Site Azure](site-recovery-best-practices.md).
