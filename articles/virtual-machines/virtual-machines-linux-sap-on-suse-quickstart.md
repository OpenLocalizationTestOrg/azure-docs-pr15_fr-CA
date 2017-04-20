<properties
   pageTitle="Test de SAP NetWeaver sur les ordinateurs virtuels de Microsoft Azure SUSE Linux | Microsoft Azure"
   description="Test de SAP NetWeaver sur les ordinateurs virtuels de Microsoft Azure SUSE Linux"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>NetWeaver SAP en cours d’exécution sur les ordinateurs virtuels de Microsoft Azure SUSE Linux


Cet article décrit les différents éléments à prendre en compte lorsque vous exécutez SAP NetWeaver sur les machines virtuelles Microsoft Azure SUSE Linux (VMs). À partir de 2016 de 19 mai SAP NetWeaver est officiellement pris en charge sur les ordinateurs virtuels de Linux SUSE sur Azure. Vous trouverez tous les détails concernant les versions de Linux, les versions du noyau SAP, etc. dans SAP Remarque 1928533 « Applications SAP sur Azure : produits pris en charge et les types d’Azure VM ».
La documentation supplémentaire sur SAP sur Linux VM est disponible ici : [À l’aide de SAP sur Linux les machines virtuelles (VM)](virtual-machines-linux-sap-get-started.md).


Les informations suivantes devraient vous aider à éviter certains pièges potentiels.

## <a name="suse-images-on-azure-for-running-sap"></a>Images SUSE sur Azure pour l’exécution de SAP

Pour l’exécution de SAP NetWeaver sur Azure, utilisez uniquement SUSE Linux Enterprise Server SLES 12 (SPx) - Voir aussi note SAP n° 1928533. Une image particulière de SUSE est sur le marché d’Azure (« SLES 11 SP3 pour la licence d’accès client SAP »), mais ce n’est pas conçu pour une utilisation générale. N’utilisez pas cette image car celle-ci est réservée pour la solution de [Cloud de SAP bibliothèque SAP solution matérielle-logicielle](https://cal.sap.com/) .  

Vous devez utiliser le Gestionnaire de ressources Azure pour tous les nouveaux tests et installations sur Azure. Pour rechercher des images de SUSE SLES et les versions à l’aide de PowerShell d’Azure ou l’interface de ligne de commande (CLI) Azure, utilisez les commandes suivantes. Vous pouvez ensuite utiliser la sortie, par exemple, pour définir l’image du système d’exploitation dans un modèle JSON pour le déploiement d’un ordinateur virtuel de nouveau SUSE Linux.
Ces commandes PowerShell sont valides pour Azure PowerShell version 1.0.1 et versions ultérieures.

* Recherchez les éditeurs existants, y compris SUSE :

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Recherchez les offres existantes de SUSE :

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Rechercher des offres de SUSE SLES :

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Recherchez une version spécifique d’un SKU SLES :

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>L’installation de WALinuxAgent dans une machine virtuelle de SUSE

L’agent appelé WALinuxAgent fait partie des images SLES sur le marché d’Azure. Pour plus d’informations sur l’installation manuellement (par exemple, lorsque vous téléchargez un disque dur virtuel SLES OS (VHD) sur site), voir :

- [OpenSUSE] (http://software.opensuse.org/package/WALinuxAgent)

- [Azure] (virtuel-machines-linux-visé-distros.md)

- [SUSE] (https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP « Amélioration de la surveillance »

SAP « Amélioration de la surveillance » est indispensable à l’exécution de SAP sur Azure obligatoire. Vérifiez les détails dans SAP Remarque 2191498 « SAP sur Linux avec Azure : amélioré Surveillance ».

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Attacher des disques de données Azure à une machine virtuelle Linux de Azure

Vous ne devez jamais les disques de données Azure de montage d’une machine virtuelle Linux de Azure à l’aide de l’ID de périphérique. Au lieu de cela, utilisez l’identificateur unique universel (UUID). Soyez prudent lorsque vous utilisez des outils graphiques pour les disques de données Azure de montage, par exemple. Vérifiez les entrées dans/etc/fstab.

Le problème avec l’ID de périphérique est qu’il peut changer, et puis l’Azure VM peut se bloquer dans le processus de démarrage. Pour atténuer le problème, vous pouvez ajouter le paramètre nofail dans/etc/fstab. Mais soyez prudent avec nofail, car les applications peuvent utiliser le point de montage comme avant et peuvent écrire dans le système de fichiers racine en cas d’un disque de données Azure externes n’a pas été monté lors du démarrage.

La seule exception à montage via l’UUID est y attacher un disque du système d’exploitation pour résoudre ce problème, comme décrit dans la section suivante.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Résolution des problèmes liés à une VM SUSE qui n’est pas plus accessible

Il peut exister des situations où une VM SUSE sur Azure se bloque dans le processus de démarrage (par exemple, avec une erreur liée au montage des disques). Vous pouvez vérifier ce problème à l’aide de la fonctionnalité de diagnostic boot v2 d’ordinateurs virtuels Azure dans le portail Azure. Pour plus d’informations, reportez-vous à la section [diagnostic Boot] (https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Une pour résoudre le problème consiste à attacher le disque du système d’exploitation de la machine virtuelle endommagée à une autre VM SUSE sur Azure. Puis apportez les modifications appropriées comme modifier/etc/fstab ou suppression des règles de réseau udev, comme décrit dans la section suivante.

Il est important de prendre en compte. Déploiement de plusieurs machines virtuelles de SUSE à partir de la même image d’Azure Marketplace (par exemple, SLES 11 SP4) entraîne le disque du système d’exploitation doit toujours être monté par le même UUID. Par conséquent, à l’aide de l’UUID d’attacher un disque du système d’exploitation à partir d’une machine virtuelle différente qui a été déployée à l’aide de la même image d’Azure Marketplace entraîne deux UUID identiques. Cela provoque des problèmes et peut signifier que l’ordinateur virtuel destiné à la résolution des problèmes en fait démarre à partir du disque du système d’exploitation attaché et endommagé au lieu de celle d’origine.

Il existe deux manières d’éviter ceci :

* Utiliser une autre image d’Azure Marketplace pour la machine virtuelle la résolution des problèmes (par exemple, SLES 11 SPx au lieu de SLES 12).
* Ne pas attacher du disque du système d’exploitation endommagé à partir d’une autre VM à l’aide d’UUID--utiliser autre chose.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Télécharger une VM SUSE sur site vers Azure

Pour une description des étapes à télécharger une VM SUSE sur site vers Azure, reportez-vous à la section [préparer un ordinateur virtuel SLES ou openSUSE pour Azure] (virtual-machines-linux-suse-create-upload-vhd.md).

Si vous souhaitez télécharger une machine virtuelle sans l’étape d’annulation à la fin (par exemple, pour conserver une installation existante de SAP, ainsi que le nom d’hôte), vérifiez les éléments suivants :

* Assurez-vous que le disque du système d’exploitation est monté à l’aide des UUID et pas l’ID du périphérique. Modification à l’UUID dans/etc/fstab n’est pas suffisante pour le disque du système d’exploitation. En outre, n’oubliez pas d’adapter le chargeur de démarrage par le biais de YaST ou en modifiant des /boot/grub/menu.lst.
* Si vous utilisez le format VHDX pour le disque du système d’exploitation SUSE et convertissez en disque dur virtuel de téléchargement vers Azure, il est très probable que le périphérique réseau passent d’eth0 et eth1. Pour éviter des problèmes lorsque vous utilisez démarrage sur Azure ultérieurement, rétablir eth0 comme décrit dans [de fixation eth0 dans VMware de 11 cloné SLES] (https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

En plus de ce qui est décrit dans l’article, nous vous conseillons de supprimer ceci :

   /lib/udev/rules.d/75-persistent-NET-Generator.Rules

Vous pouvez également installer l’Agent de Linux Azure (waagent) pour vous aider à éviter d’éventuels problèmes, tant qu’il n’y a pas plusieurs cartes réseau.

## <a name="deploying-a-suse-vm-on-azure"></a>Déploiement d’une machine virtuelle SUSE sur Azure

Vous devez créer de nouvelles machines virtuelles de SUSE à l’aide de fichiers de modèle JSON dans le nouveau modèle du Gestionnaire de ressources Azure. Après avoir créé le fichier de modèle JSON, vous pouvez déployer l’ordinateur virtuel à l’aide de la commande CLI suivante au lieu de PowerShell :

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Pour plus d’informations sur les fichiers de modèle JSON, consultez [modèles de création d’un gestionnaire de ressources Azure] (ressource-groupe-création-templates.md) et (modèles quickstart Azure) (https://azure.microsoft.com/documentation/templates/).

Pour plus d’informations sur Azure Resource Manager et la CLI, voir [utiliser la CLI Azure pour Mac, Linux et Windows avec le Gestionnaire de ressources Azure] (xplat-cli-azure-ressource-manager.md).

## <a name="sap-license-and-hardware-key"></a>Clé de licence et de matériel SAP

Pour obtenir la certification SAP-Azure officielle, un nouveau mécanisme a été introduit pour calculer la clé matérielle SAP qui est utilisée pour la licence SAP. Le noyau SAP devait s’adapter aux recours à cette. Anciennes versions du noyau SAP pour Linux n’incluent pas cette modification du code. Par conséquent, dans certains cas (par exemple, Azure VM redimensionnement), la modification de la clé matérielle SAP et la licence SAP a été de ne plus être valides. Cela est résolu dans les dernières noyaux Linux de SAP. Pour plus d’informations consultez la note SAP n° 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Package sapconf SUSE / adm réglé

SUSE fournit un package appelé « sapconf » qui gère un ensemble de paramètres spécifiques à SAP. Pour plus d’informations sur ce que fait ce package et comment les installer et à utiliser, reportez-vous à la section [pour préparer un SUSE Linux Enterprise Server d’exécuter des systèmes SAP à l’aide de sapconf] (https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) et [quelle est sapconf ou comment préparer un serveur SUSE Linux Enterprise Server pour l’exécution des systèmes SAP ?] (http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

En attendant, il existe un nouvel outil qui remplace la sapconf - réglé-adm. Un peut trouver plus d’informations sur cet outil en suivant les deux liens ci-dessous.

La documentation sur les adm réglé profil sap-hana SLES est disponible [ici](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

Systèmes de réglage pour les charges de travail SAP avec adm réglé - vous pouvez trouver [ici](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) dans le chapitre 6.2


## <a name="nfs-share-in-distributed-sap-installations"></a>Dans les installations SAP distribuées de partage NFS

Si vous avez une installation distribuée--par exemple, dans lequel vous souhaitez installer la base de données et les serveurs d’applications SAP dans des machines virtuelles séparées, vous pouvez partager le répertoire /sapmnt via le système NFS (Network File). Si vous avez des problèmes avec les étapes d’installation après avoir créé le partage NFS pour /sapmnt, vérifiez si « no_root_squash » est défini pour le partage.

## <a name="logical-volumes"></a>Volumes logiques

Dans le passé si nécessaires sur plusieurs disques de données Azure (par exemple, pour la base de données SAP), un volume logique de grande taille, il était recommandé d’utiliser mdadm comme lvm n’a pas été complètement validé encore sur Azure. Pour savoir comment configurer RAID Linux sur Azure à l’aide de mdadm, consultez [configuration logicielle RAID sous Linux](virtual-machines-linux-configure-raid.md). Dans le même temps à partir du début de mai 2016 également lvm est entièrement pris en charge sur Azure et peut être utilisée comme alternative à le mdadm. Pour plus d’informations concernant le lvm sur Azure consultez [Configurer le LVM sur un ordinateur virtuel de Linux dans Azure](virtual-machines-linux-configure-lvm.md).


## <a name="azure-suse-repository"></a>Référentiel SUSE Azure

Si vous avez un problème avec l’accès au référentiel Azure SUSE standard, vous pouvez utiliser une commande simple pour le réinitialiser. Cela peut se produire si vous créez une image du système d’exploitation privée dans une région Azure puis copiez l’image vers une autre région dans lequel vous souhaitez déployer de nouvelles machines virtuelles basées sur cette image du système d’exploitation privée. Il suffit d’exécuter la commande suivante à l’intérieur de la machine virtuelle :

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Bureau de GNOME

Si vous souhaitez le bureau Gnome permet d’installer un système complet de démonstration SAP à l’intérieur d’un ordinateur virtuel unique, y compris une interface graphique de SAP, navigateur et la console de gestion SAP--utilisent cette indication pour l’installer sur les images d’Azure SLES :

   Pour SLES 11 :

   ```
   zypper in -t pattern gnome
   ```

   Pour SLES 12 :

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Prise en charge SAP pour Oracle sous Linux dans le nuage

Il existe une restriction de prise en charge d’Oracle sous Linux dans des environnements virtualisés. Bien que ce ne soit pas une rubrique spécifique d’Azure, il est important de comprendre. SAP ne pas prend en charge Oracle SUSE ou Red Hat dans un cloud public comme Azure. Pour discuter de cette rubrique, contactez Oracle directement.
