<properties
    pageTitle="Préparation de mappage réseau pour la protection d’ordinateur virtuel Hyper-V avec VMM dans la récupération de Site Azure | Microsoft Azure"
    description="Configurez le mappage réseau pour la réplication d’ordinateur virtuel Hyper-V à partir d’un centre de données local vers Azure, ou sur un site secondaire."
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
    ms.date="10/05/2016"
    ms.author="raynew"/>


# <a name="prepare-network-mapping-for-hyper-v-virtual-machine-protection-with-vmm-in-azure-site-recovery"></a>Préparation de mappage réseau pour la protection d’ordinateur virtuel Hyper-V avec VMM dans Azure récupération de Site

Récupération de Site Azure contribue à votre stratégie d’entreprise après incident et de continuité d’activité (BCDR) de récupération par l’orchestration de réplication, le basculement et la récupération de machines virtuelles et des serveurs physiques.

Cet article décrit le mappage réseau, ce qui vous permet de vous configurez les paramètres réseau lorsque vous utilisez la restauration du Site pour répliquer des ordinateurs virtuels Hyper-V se trouve dans des nuages VMM entre deux centres de données en local ou entre un centre de données sur site et d’Azure. Notez que si vous êtes répliquer des ordinateurs virtuels Hyper-V sans un nuage VMM, ou répliquer des ordinateurs virtuels VMware ou les serveurs physiques, cet article n’est pas pertinent.

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Vue d’ensemble

Mappage réseau est utilisée lors de la récupération de Site Azure est déployée pour répliquer des ordinateurs virtuels Hyper-V sur Azure, ou à un centre de données secondaire, à l’aide de la réplication de Hyper-V Replica ou SAN.

- **Machines virtuelles de réplication Hyper-V dans des nuages VMM entre deux centres de données sur site**— réseau des cartes de mise en correspondance entre les réseaux de machine virtuelle sur un serveur VMM de source et machine virtuelle sur un serveur VMM de cible pour effectuer les opérations suivantes :

    - **Machines virtuelles de se connecter après basculement**— permet de s’assurer que machines virtuelles sera connectés aux réseaux appropriées après le basculement. La machine virtuelle de réplica sera être connectée au réseau cible qui est mappé sur le réseau source.
    - **Place réplica virtual machines sur les serveurs hôtes**, placez optimale des machines virtuelles de réplicas sur les serveurs hôtes Hyper-V. Machines virtuelles de réplica sera placés sur les hôtes peuvent accéder les réseaux mappés de la machine virtuelle.
    - **Aucun mappage réseau**: Si vous ne configurez pas le mappage de réseau, les machines virtuelles dupliquées n’est pas connectés à un réseau d’ordinateur virtuel après le basculement.

- **Machines virtuelles de réplication Hyper-V dans un local VMM cloud pour Azure**: réseau des cartes de mise en correspondance entre les réseaux de machine virtuelle sur le serveur VMM de source et la cible des réseaux Azure pour effectuer les opérations suivantes :
    - **Connecter des machines virtuelles après le basculement**, tous les ordinateurs où le basculement sur le même réseau peut se connecter entre eux, quel que soit le plan de récupération, ils sont en cours.
    - **Passerelle réseau**: si une passerelle réseau est configurée sur la cible de réseau Azure, machines virtuelles peuvent se connecter à d’autres ordinateurs virtuels de locaux.
    - **Aucun mappage réseau**: Si vous ne configurez pas mappage réseau, uniquement des ordinateurs virtuels dans le même plan de récupération que le basculement sera capable de se connecter à l’autre après basculement sur Azure.


## <a name="network-mapping-example"></a>Exemple de mappage réseau

Mappage de réseau peut être configuré entre les réseaux de machine virtuelle sur un seul serveur VMM si deux sites sont gérés par le même serveur ou sur deux serveurs VMM. Lorsque le mappage est configuré correctement et que la réplication est activée, une machine virtuelle à l’emplacement principal sera connectée à un réseau, et son réplica à l’emplacement cible sera connecté à son réseau mappé.

Si les réseaux ont été correctement configurés dans VMM, lorsque vous sélectionnez un réseau de machine virtuelle cible pendant le mappage réseau, les nuages VMM source qui utilisent le réseau de la machine virtuelle source seront affichera, ainsi que les réseaux VM de cibles disponibles sur les nuages cible qui sont utilisés pour la protection.

Voici un exemple pour illustrer ce mécanisme. Prenons une organisation avec deux emplacements à New York et de Chicago.

**Emplacement** | **Serveur VMM** | **Réseaux de machine virtuelle** | **Mappé à**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mappé à Chicago-VMNetwork1
 |  | VMNetwork2-NewYork | Non mappé
Chicago | Chicago-VMM| Chicago-VMNetwork1 | Mappé à VMNetwork1-NewYork
 | | Chicago-VMNetwork2 | Non mappé

Dans cet exemple :

- Lorsqu’une machine virtuelle de réplica est créée pour un ordinateur virtuel qui est connecté à VMNetwork1-NewYork, il sera connecté à Chicago-VMNetwork1.
- Lorsqu’une machine virtuelle de réplica est créée pour NewYork-VMNetwork2 ou VMNetwork2-Chicago, il ne pourra pas connecter à un réseau.

Voici comment nuages VMM paramétrés dans notre exemple d’entreprise et les réseaux logiques associées avec les nuages.

### <a name="cloud-protection-settings"></a>Paramètres de protection du nuage

**Nuage protégé** | **Protection de nuage** | **Réseau logique (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>Chicago-LogicalNetwork1</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>Chicago-LogicalNetwork1</p>

### <a name="logical-and-vm-network-settings"></a>Paramètres de réseau logique et de la machine virtuelle

**Emplacement** | **Réseau logique** | **Réseau de machine virtuelle associée**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | Chicago-LogicalNetwork1 | Chicago-VMNetwork1
 | LogicalNetwork2Chicago | Chicago-VMNetwork2

### <a name="target-networks"></a>Réseaux de cible

Basé sur ces paramètres, lorsque vous sélectionnez le réseau de la machine virtuelle cible, le tableau suivant montre les choix qui seront disponibles.

**Sélectionnez** | **Nuage protégé** | **Protection de nuage** | **Réseau de cibles disponible**
---|---|---|---
Chicago-VMNetwork1 | SilverCloud1 | SilverCloud2 | Disponible
 | GoldCloud1 | GoldCloud2 | Disponible
Chicago-VMNetwork2 | SilverCloud1 | SilverCloud2 | N’est pas disponible
 | GoldCloud1 | GoldCloud2 | Disponible



## <a name="multiple-subnets"></a>Plusieurs sous-réseaux

Si le réseau cible a plusieurs sous-réseaux et de ces sous-réseaux a le même nom que le sous-réseau sur lequel se trouve l’ordinateur virtuel source, l’ordinateur virtuel de réplica sera connecté à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, l’ordinateur virtuel va être connecté pour le premier sous-réseau dans le réseau.


### <a name="failback"></a>Retour arrière

Pour voir ce qui se passe en cas de retour arrière (réplication inverse), supposons que VMNetwork1-NewYork est mappé à VMNetwork1-Chicago, avec les paramètres suivants.


**Machine virtuelle** | **Connecté au réseau de la machine virtuelle**
---|---
VM1 | VMNetwork1-réseau
Ordinateur virtuel 2 (réplicas de VM1) | Chicago-VMNetwork1

Avec ces paramètres, passons en revue ce qui se passe dans les deux scénarios possibles.

**Scénario** | **Résultat**
---|---
Aucune modification dans les propriétés réseau de machine virtuelle-2 après le basculement. | Machine virtuelle-1 reste connecté au réseau de la source.
Les propriétés réseau de machine virtuelle-2 sont modifiés après le basculement et est déconnecté. | Mémoire virtuelle-1 est déconnecté.
Les propriétés réseau de machine virtuelle-2 sont modifiés après le basculement et est connecté à VMNetwork2-Chicago. | Si VMNetwork2-Chicago n’est pas mappée, VM-1 est déconnectés.
Mappage réseau de Chicago-VMNetwork1 est modifiée. | Mémoire virtuelle-1 sera connecté au réseau maintenant mappé à Chicago-VMNetwork1.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une meilleure compréhension de mappage réseau, [Démarrer avec le déploiement de la récupération de Site](site-recovery-best-practices.md).
