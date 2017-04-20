<properties
   pageTitle="Conseils techniques : restauration sur site vers Azure | Microsoft Azure"
   description="L’article sur la compréhension et la récupération de la conception de systèmes d’infrastructure sur site vers Azure"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a>Guide technique de résilience Azure : restauration sur site vers Azure

Azure fournit un ensemble complet de services d’activation de l’extension d’un centre de données local vers Azure à des fins de récupération hautes disponibilité et reprise après sinistre :

* __Mise en réseau__: avec un réseau privé virtuel, vous étendez en toute sécurité votre réseau local dans le nuage.
* __Calculer__: les clients à l’aide de Hyper-V sur site peuvent « Soulevez et MAJ » ordinateurs virtuels existants (VMs) pour Azure.
* __Stockage__: StorSimple étend votre système de fichiers pour le stockage Azure. Le service de sauvegarde Azure permet la sauvegarde des fichiers et des bases de données SQL Azure Storage.
* __Réplication de la base de données__: avec SQL Server 2014 (ou ultérieur) groupes de disponibilité, vous pouvez implémenter la haute disponibilité et reprise après sinistre pour vos données sur site.

##<a name="networking"></a>Mise en réseau

Vous pouvez utiliser le réseau virtuel d’Azure pour créer une section isolée de façon logique dans Azure et connectez-vous en toute sécurité à votre centre de données sur site ou à un seul ordinateur client à l’aide d’une connexion IPsec. Avec un réseau virtuel, vous pouvez tirer parti de l’infrastructure évolutive, à la demande dans Azure, tout en offrant une connectivité vers les applications et les données sur site, y compris les systèmes s’exécutant sur Windows Server et grands systèmes UNIX. Pour plus d’informations, consultez la rubrique [Azure réseau documentation](../virtual-network/virtual-networks-overview.md) .

##<a name="compute"></a>Calculer

Si vous utilisez Hyper-V sur site, vous pouvez « Soulevez et MAJ » existant de machines virtuelles vers Azure et exécutant Windows Server 2012 (ou ultérieur), sans apporter de modifications à la machine virtuelle ou de la machine virtuelle de conversion des formats de fournisseurs de services. Pour plus d’informations, voir [a propos des disques et des disques durs virtuels pour les machines virtuelles Azure](../virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##<a name="azure-site-recovery"></a>Récupération de Site Azure

Si vous souhaitez que de reprise après sinistre en tant que service (DRaaS), Azure assure la [Restauration de Site d’Azure](https://azure.microsoft.com/services/site-recovery/). Récupération de Site Azure offre une protection complète pour les serveurs VMware, Hyper-V et physiques. Avec la récupération de Site d’Azure, vous pouvez utiliser un autre serveur local ou Azure comme votre site de récupération. Pour plus d’informations sur la récupération de Site d’Azure, consultez la [documentation de récupération de Site Azure](https://azure.microsoft.com/documentation/services/site-recovery/).

##<a name="storage"></a>Stockage

Il existe plusieurs options pour l’utilisation d’Azure comme un site de sauvegarde pour les données locales.

###<a name="storsimple"></a>StorSimple

StorSimple en toute sécurité et en toute transparence intègre le stockage en nuage pour applications sur site. Il offre également une solution unique qui offre une haute performance hiérarchisé local et stockage en nuage, l’archivage de live, protection des données basées sur le nuage et de reprise après sinistre. Pour plus d’informations, consultez la [page du produit StorSimple](https://azure.microsoft.com/services/storsimple/).

###<a name="azure-backup"></a>Sauvegarde Azure

Azure sauvegarde sauvegardes cloud à l’aide des outils familiers de sauvegarde dans Windows Server 2012 (ou ultérieurs), Windows Server 2012 Essentials (ou version ultérieure) et de System Center 2012 Data Protection Manager (ou version ultérieure). Ces outils fournissent un flux de travail pour la gestion de la sauvegarde qui est indépendante de l’emplacement de stockage des sauvegardes, si un disque local ou un stockage Azure. Une fois que les données sont sauvegardées dans le nuage, les utilisateurs autorisés peuvent facilement récupérer des sauvegardes à n’importe quel serveur.

Avec des sauvegardes incrémentielles, uniquement les modifications apportées aux fichiers sont transférées vers le nuage. Ainsi, pour utiliser l’espace de stockage, réduire la consommation de bande passante et prend en charge la récupération de point-à-temps de plusieurs versions des données efficacement. Vous pouvez également choisir d’utiliser des fonctionnalités supplémentaires, telles que les stratégies de rétention des données, la compression des données et d’accélération de transfert de données. À l’aide d’Azure comme emplacement de sauvegarde a l’avantage évident que les sauvegardes sont automatiquement « hors site ». Cela permet d’éliminer les exigences supplémentaires pour sécuriser et protéger les supports de sauvegarde sur site.

Pour plus d’informations, consultez [qu’est la sauvegarde d’Azure ?](../backup/backup-introduction-to-azure-backup.md) et de [Configurer la sauvegarde de données DPM Azure](https://technet.microsoft.com/library/jj728752.aspx).

##<a name="database"></a>Base de données

Vous pouvez avoir une solution de reprise après sinistre pour vos bases de données SQL Server dans un environnement informatique hybride à l’aide de groupes de disponibilité AlwaysOn, la mise en miroir de base de données, l’envoi de journaux et sauvegarde et restauration avec le stockage des objets Blob Azure. Toutes ces solutions utilisent SQL Server en cours d’exécution sur Azure Virtual Machines.

Groupes de disponibilité AlwaysOn peut être utilisés dans un environnement informatique hybride où les duplications de base de données existent à la fois sur site et dans le cloud. Ceci est illustré dans le diagramme suivant.

![Groupes de disponibilité AlwaysOn SQL Server dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

La mise en miroir de base de données peut également s’étendre sur des serveurs en local et le nuage dans une installation basée sur un certificat. Le diagramme suivant illustre ce concept.

![La mise en miroir de base de données SQL Server dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

Envoi de journaux peut être utilisé pour synchroniser une base de données locale avec une base de données SQL Server sur une machine virtuelle Azure.

![Envoi de journaux SQL Server dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

Enfin, vous pouvez sauvegarder une base de données local directement pour le stockage des objets Blob Azure.

![Sauvegarde de SQL Server vers le stockage Azure Blob dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

Pour plus d’informations, reportez-vous à la section [haute disponibilité et reprise après sinistre de SQL Server sur des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) et de [sauvegarde et restauration de SQL Server sur des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).

##<a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a>Listes de vérification pour la restauration sur site de Microsoft Azure

###<a name="networking"></a>Mise en réseau

  1. Consultez la section mise en réseau de ce document.
  2. Réseau virtuel permet de connecter en toute sécurité sur site vers le nuage.

###<a name="compute"></a>Calculer

  1. Consultez la section Compute de ce document.
  2. Déplacer des machines virtuelles entre Hyper-V et Azure.

###<a name="storage"></a>Stockage

  1. Consultez la section de stockage de ce document.
  2. Tirer parti des services de StorSimple pour l’utilisation de stockage en nuage.
  3. Utiliser le service de sauvegarde d’Azure.

###<a name="database"></a>Base de données

  1. Consultez la section de la base de données de ce document.
  2. Envisagez l’utilisation de SQL Server sur Azure VM en tant que la sauvegarde.
  3. Paramétrer des groupes de disponibilité AlwaysOn.
  4. Configurer la mise en miroir de base de données basée sur un certificat.
  5. Utiliser l’envoi de journaux.
  6. Sauvegardez les bases de données locales pour le stockage des objets Blob Azure.

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série de [conseils techniques de résilience Azure](./resiliency-technical-guidance.md). Le prochain article de cette série est la [récupération à partir d’une suppression accidentelle ou de corruption des données](./resiliency-technical-guidance-recovery-data-corruption.md).