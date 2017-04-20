<properties
    pageTitle="Matrice de support de récupération de Site Azure | Microsoft Azure"
    description="Résume les composants et les systèmes d’exploitation pris en charge pour la récupération de Site Azure"
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

# <a name="azure-site-recovery-support-matrix"></a>Matrice de support de récupération de Site Azure

Cet article présente les systèmes d’exploitation pris en charge et les composants pour les déploiements de récupération de Site Azure. Une liste de conditions préalables et les composants pris en charge est disponible pour chaque scénario de déploiement dans chaque l’article déploiement correspondant et le présent document récapitule les.

## <a name="supported-operating-systems-for-virtualization-servers"></a>Systèmes d’exploitation pris en charge pour les serveurs de virtualisation


**Source** | **Cible** | **Système d’exploitation hôte**
---|---|---|--- 
**Hôtes Hyper-V (sans VMM)** | Azure | Windows Server 2012 R2 avec les dernières mises à jour
**Hôtes Hyper-V (avec VMM)** | Azure | Windows Server 2012 R2 avec les dernières mises à jour
**Hôtes Hyper-V (avec VMM)** | Site secondaire de VMM | Au moins Windows Server 2012 avec les dernières mises à jour
**VMware hôtes/vCenter** | Azure | vCenter, 5.5 ou 6.0 (prise en charge des 5.5 fonctions uniquement) <br/><br/> vSphere 5.1 avec les dernières mises à jour, 5.5 ou 6.0
**VMware hôtes/vCenter** | Site secondaire de VMware | vCenter, 5.5 ou 6.0 (prise en charge des 5.5 fonctions uniquement) <br/><br/> vSphere 5.1 avec les dernières mises à jour, 5.5 ou 6.0

## <a name="supported-requirements-for-replicated-machines"></a>Configurations prises en charge pour les ordinateurs répliqués

**Source** | **Ce qui est répliqué** | **Cible** | **Système d’exploitation hôte**
---|---|---|--- 
**Ordinateurs virtuels Hyper-V** | Les charges de travail | Azure | N’importe quel système d’exploitation d’invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Ordinateurs virtuels doivent répondre aux [exigences d’Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Machines virtuelles de Hyper-V (avec VMM)** | Les charges de travail | Azure | N’importe quel système d’exploitation d’invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Ordinateurs virtuels doivent répondre aux [exigences d’Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Machines virtuelles de Hyper-V (avec VMM)** | Les charges de travail | Site secondaire de VMM | N’importe quel système d’exploitation d’invité [pris en charge par Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)
**Ordinateurs virtuels VMware** | Les charges de travail en cours d’exécution sur la machine virtuelle de Windows | Azure | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1<br/><br/> Ordinateurs virtuels doivent répondre aux [exigences d’Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Ordinateurs virtuels VMware** | Les charges de travail s’exécutant sur Linux VM | Azure | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécute le noyau compatible de Red Hat ou incassable entreprise noyau version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Espace de stockage requis : fichier système (EXT3, ETX4, ReiserFS, XFS) ; Multipath-périphérique logiciel Mappeur (multipath)) ; Le Gestionnaire de volumes : (LVM2). Serveurs physiques avec le stockage HP CCISS contrôleur ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur le Service Pack 3 de SUSE Linux Enterprise Server 11.<br/><br/> Ordinateurs virtuels doivent répondre aux [exigences d’Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Ordinateurs virtuels VMware** | Les charges de travail en cours d’exécution sur la machine virtuelle de Windows | Site secondaire de VMware | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1
**Ordinateurs virtuels VMware** | Les charges de travail s’exécutant sur Linux VM | Site secondaire de VMware | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécute le noyau compatible de Red Hat ou incassable entreprise noyau version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Espace de stockage requis : fichier système (EXT3, ETX4, ReiserFS, XFS) ; Multipath-périphérique logiciel Mappeur (multipath)) ; Le Gestionnaire de volumes : (LVM2). Serveurs physiques avec le stockage HP CCISS contrôleur ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur le Service Pack 3 de SUSE Linux Enterprise Server 11.
**Serveurs physiques** | Les charges de travail s’exécutant sous Windows | Azure | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 avec au moins SP1
**Serveurs physiques** | Les charges de travail en cours d’exécution sous Linux | Azure | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> CentOS 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécute le noyau compatible de Red Hat ou incassable entreprise noyau version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Espace de stockage requis : fichier système (EXT3, ETX4, ReiserFS, XFS) ; Multipath-périphérique logiciel Mappeur (multipath)) ; Le Gestionnaire de volumes : (LVM2). Serveurs physiques avec le stockage HP CCISS contrôleur ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur le Service Pack 3 de SUSE Linux Enterprise Server 11.
**Serveurs physiques** | Les charges de travail s’exécutant sous Windows | Site secondaire | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 avec au moins SP1
**Serveurs physiques** | Les charges de travail en cours d’exécution sous Linux | Site secondaire | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> CentOS 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécute le noyau compatible de Red Hat ou incassable entreprise noyau version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Espace de stockage requis : fichier système (EXT3, ETX4, ReiserFS, XFS) ; Multipath-périphérique logiciel Mappeur (multipath)) ; Le Gestionnaire de volumes : (LVM2). Serveurs physiques avec le stockage HP CCISS contrôleur ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur le Service Pack 3 de SUSE Linux Enterprise Server 11.


## <a name="provider-versions"></a>Versions du fournisseur

**Nom** | **Description** | **Version la plus récente** | **Prise en charge** | **Détails**
---|---|---|---| ---
**Fournisseur de récupération de Site Azure** | Coordonne les communications entre des serveurs sur site et site d’Azure/secondaire <br/><br/> Installé sur les serveurs VMM sur site ou les serveurs Hyper-V, si aucun serveur VMM | 5.1.1700 (disponible à partir du portail) | [Fonctions et correctifs les plus récents](https://support.microsoft.com/kb/3155002)
**Récupération de Site Azure unifiée d’installation (VMware pour Azure)** | Coordonne les communications entre des serveurs sur site VMware et Azure <br/><br/> Installé sur des serveurs VMware sur site | 9.3.4246.1 (disponible à partir du portail) | [Fonctions et correctifs les plus récents](https://support.microsoft.com/kb/3155002)
**Service de la mobilité** | Coordonne la réplication entre des serveurs VMware serveurs/physique sur site et site d’Azure/secondaire | NA (disponible à partir du portail) | Installé sur chaque serveur physique que vous souhaitez répliquer ou de VMware VM. **Agent des Services de récupération Microsoft Azure (MARS)** | Coordonne la réplication entre des ordinateurs virtuels Hyper-V et Azure<br/><br/> Installé sur des serveurs Hyper-V sur site (avec ou sans serveur VMM) | 2.0.8689.0 (disponible à partir du portail) | Cet agent est utilisé par la restauration du Site Azure et d’Azure sauvegarde). [En savoir plus] (https://support.microsoft.com/en-us/kb/2997692)

## <a name="next-steps"></a>Étapes suivantes

[Préparer pour le déploiement](site-recovery-best-practices.md)

