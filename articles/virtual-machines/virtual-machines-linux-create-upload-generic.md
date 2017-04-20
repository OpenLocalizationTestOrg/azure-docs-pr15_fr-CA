<properties
    pageTitle="Créer et télécharger un VHD Linux dans Azure"
    description="Apprenez à créer et télécharger un Azure disque dur virtuel (VHD) qui contient un système d’exploitation Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="szark"/>

# <a name="information-for-non-endorsed-distributions"></a>Pour les Distributions de Non approuvée #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**Important**: l’Azure platform SLA s’applique aux machines virtuelles s’exécutant le système d’exploitation Linux uniquement lorsque [visé distributions](virtual-machines-linux-endorsed-distros.md) est utilisé. Toutes les distributions Linux sont fournies dans la galerie d’image Azure sont les distributions visées à la configuration requise.

- [Linux sur Azure - visé Distributions](virtual-machines-linux-endorsed-distros.md)
- [Prise en charge de Linux des images dans Microsoft Azure](https://support.microsoft.com/kb/2941892)

Toutes les distributions en cours d’exécution sur Azure devra répondre à un certain nombre de conditions requises pour avoir une chance de s’exécuter correctement sur la plate-forme.  Cet article n’est pas complète car chaque distribution est différente ; et c’est tout à fait possible que même si vous répondre à tous les critères ci-dessous vous devrez considérablement optimiser votre système Linux pour vous assurer qu’il s’exécute correctement sur la plate-forme.

C’est pour cette raison que nous vous conseillons de démarrer avec l’un de nos [Linux sur les Distributions de visé Azure](virtual-machines-linux-endorsed-distros.md) lorsque cela est possible. Les articles suivants vous guidera tout au long de la préparation les diverses distributions Linux visées qui sont pris en charge sur Azure :

- **[Distributions de base centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**

Le reste de cet article se concentrera sur les lignes directrices pour l’exécution de votre distribution Linux sur Azure.


## <a name="general-linux-installation-notes"></a>Notes d’Installation Linux général ##

- Le format VHDX n’est pas pris en charge dans Azure, uniquement **fixe disque dur virtuel**.  Vous pouvez convertir le disque au format de disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l’applet de commande convert-disque dur virtuel. Si vous utilisez VirtualBox, cela signifie la sélection de **taille fixe** et non de la valeur par défaut attribuée de manière dynamique lors de la création du disque.

- Lors de l’installation du système Linux, il est *recommandé* d’utiliser les partitions standard plutôt que LVM (souvent par défaut pour la plupart des installations). Cela évitera LVM nom est en conflit avec les machines virtuelles de cloné, particulièrement si un disque du système d’exploitation a besoin doit être jointe à une autre VM identique pour le dépannage. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur des disques de données.

- Prise en charge du noyau pour le montage de systèmes de fichiers UDF est requis. Au premier démarrage sur Azure, la configuration de mise en service est passée de Linux VM via les supports formatés UDF qui sont associés à l’invité. L’agent d’Azure Linux doit être en mesure de monter le système de fichiers UDF pour lire sa configuration et configurer l’ordinateur virtuel.

- Les versions du noyau Linux sous 2.6.37 ne supportent pas NUMA sur Hyper-V avec une taille de mémoire virtuelle supérieure. Ce problème principalement les impacts anciennes distributions à l’aide de l’en amont noyau de Red Hat 2.6.32 et a été corrigé dans RHEL 6.6 (noyau-2.6.32-504). Les systèmes exécutant les noyaux personnalisés datant de plus de 2.6.37 ou noyaux basée sur RHEL qui sont plus anciens que 2.6.32-504 devez définir le paramètre de démarrage `numa=off` sur le noyau de la ligne de commande dans grub.conf. Pour plus d’informations, consultez Red Hat [436883 de la base de connaissances](https://access.redhat.com/solutions/436883).

- Ne configurez pas une partition de swap sur le disque du système d’exploitation. L’agent de Linux peut être configuré pour créer un fichier d’échange sur le disque de ressource temporaire.  Vous trouverez plus d’informations à ce sujet dans les étapes suivantes.

- Tous les disques durs virtuels doivent avoir des tailles qui sont des multiples de 1 Mo.


### <a name="installing-linux-without-hyper-v"></a>L’installation de Linux sans Hyper-V ###

Dans certains cas, les programmes d’installation de Linux ne comprennent pas les pilotes pour Hyper-V le ramdisk initial (initrd ou initramfs), sauf si elle détecte qu’il s’exécute un un environnement Hyper-V.  Lorsque vous utilisez un système de virtualisation différentes (par exemple, Virtualbox, KVM, etc.) pour préparer votre image de Linux, vous devrez peut-être régénérer l’initrd pour vous assurer qu’au moins le `hv_vmbus` et `hv_storvsc` modules de noyau sont disponibles sur le disque virtuel initial.  Il s’agit d’un problème connu au moins sur les systèmes basés sur la distribution Red Hat en amont.

Le mécanisme pour la reconstruction de l’image initrd ou initramfs peut varier en fonction de la distribution. Vérifiez la documentation de votre distribution ou prise en charge de la procédure appropriée.  Voici par exemple comment reconstruire l’initrd à l’aide de la `mkinitrd` utilitaire :

Tout d’abord, sauvegardez l’image initrd existante :

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Ensuite, régénérez l’initrd avec la `hv_vmbus` et `hv_storvsc` modules de noyau :

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Disques durs virtuels redimensionnement ###

Les images de disque dur virtuel sur Azure doivent avoir une taille virtuelle alignée à 1 Mo.  En règle générale, les disques durs virtuels créés à l’aide de Hyper-V doivent déjà être correctement alignés.  Si le disque dur virtuel n’est pas aligné correctement vous pouvez recevoir un message d’erreur semblable au suivant lorsque vous essayez de créer une *image* à partir de votre disque dur virtuel :

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Pour résoudre ce problème, vous pouvez redimensionner la machine virtuelle à l’aide de la console du Gestionnaire Hyper-V ou l’applet de commande Powershell de [Redimensionnement-disque dur virtuel](http://technet.microsoft.com/library/hh848535.aspx) .  Si vous n’exécutez pas dans un environnement Windows il est recommandé d’utiliser des qemu-img à convertir (si nécessaire) et redimensionner le disque dur virtuel.

> [AZURE.NOTE] Il existe un problème connu dans les versions qemu-img > = 2.2.1 qui se traduit par un disque dur virtuel incorrectement mis en forme. Le problème a été corrigé dans QEMU 2.6. Il est recommandé d’utiliser qemu-img 2.2.0 ou inférieur, ou mettre à jour vers 2.6 ou version ultérieure. Référence : https://bugs.launchpad.net/qemu/+bug/1490611.


 1. Redimensionnement du disque dur virtuel utilisant directement des outils tels que `qemu-img` ou `vbox-manage` peut entraîner un disque dur virtuel non démarrable.  Donc il est recommandé pour la première conversion du disque dur virtuel à une image de disque brute.  Si l’image de machine virtuelle a déjà été créé sous forme d’image de disque brut (la valeur par défaut pour certains hyperviseurs par exemple KVM) vous pouvez ignorer cette étape :

        # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Calculer la taille de l’image de disque pour vous assurer que la taille virtuelle est alignée à 1 Mo.  Le script du shell bash suivantes peut aider à cela.  Le script utilise "`qemu-img info`" pour déterminer la taille virtuelle de l’image de disque, puis calcule la taille et le 1 Mo suivant :

        rawdisk="MyLinuxVM.raw"
        vhddisk="MyLinuxVM.vhd"

        MB=$((1024*1024))
        size=$(qemu-img info -f raw --output json "$rawdisk" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        rounded_size=$((($size/$MB + 1)*$MB))
        echo "Rounded Size = $rounded_size"

 3. Redimensionner un disque brut à l’aide de $rounded_size tel que défini dans le script ci-dessus :

        # qemu-img resize MyLinuxVM.raw $rounded_size

 4. Maintenant, convertissez le disque brut vers un disque dur virtuel de taille fixe :

        # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## <a name="linux-kernel-requirements"></a>Exigences du noyau Linux ##

Les pilotes Linux Integration Services (LIS) pour Hyper-V et Azure sont fournis directement au noyau Linux en amont. De nombreuses distributions incluant une version de noyau Linux récente (c'est-à-dire 3.x) va disposer de ces pilotes déjà, ou que vous fournissez des versions utilisées avec leurs noyaux.  Ces pilotes sont constamment mis à jour dans le noyau en amont avec les nouveaux correctifs et fonctionnalités, lorsque cela est possible, il est recommandé d’exécuter un [visé de distribution](virtual-machines-linux-endorsed-distros.md) qui contiendra ces correctifs et les mises à jour.

Si vous exécutez une variante de Red Hat Enterprise Linux versions **6.0 à 6.3**, vous devrez installer les derniers pilotes LIS pour Hyper-V. Les pilotes se trouvent [à cet emplacement](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). RHEL **6.4 +** (et dérivés) les LIS pilotes sont déjà inclus dans le noyau, et donc aucun package d’installation supplémentaires n’est nécessaires à l’exécution de ces systèmes sur Azure.

Si un noyau personnalisé est requis, il est recommandé d’utiliser une version plus récente de noyau (c'est-à-dire **3,8 +**). Pour ces distributions ou les fournisseurs qui gèrent leur propres du noyau, des efforts seront requis pour backport régulièrement les pilotes LIS du noyau en amont à votre noyau personnalisé.  Même si vous exécutez déjà une version de noyau relativement récente, il est fortement recommandé pour assurer le suivi de tous les correctifs en amont dans les backport et les pilotes LIS ceux en fonction des besoins. L’emplacement des fichiers de code source du pilote LIS est disponible dans le fichier [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) dans l’arborescence source du noyau Linux :

    F:  arch/x86/include/asm/mshyperv.h
    F:  arch/x86/include/uapi/asm/hyperv.h
    F:  arch/x86/kernel/cpu/mshyperv.c
    F:  drivers/hid/hid-hyperv.c
    F:  drivers/hv/
    F:  drivers/input/serio/hyperv-keyboard.c
    F:  drivers/net/hyperv/
    F:  drivers/scsi/storvsc_drv.c
    F:  drivers/video/fbdev/hyperv_fb.c
    F:  include/linux/hyperv.h
    F:  tools/hv/

Au minimum, l’absence des correctifs suivants sont connus pour provoquer des problèmes sur Azure, et donc il doivent être inclus dans le noyau. Cette liste n’est en aucun cas exhaustive ou terminé pour toutes les distributions :

- [ata_piix : différer des disques pour les pilotes Hyper-V par défaut](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc : pour les paquets en transit dans le chemin d’accès de réinitialisation du compte](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc : éviter l’utilisation de WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc : désactiver l’écriture même RAID et les pilotes de carte hôte virtuel](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc : correctif de déréférencement de pointeur NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc : échecs de mémoire tampon d’anneau risque de gel d’e/s](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
- [scsi_sysfs : protéger contre l’exécution de double de __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)


## <a name="the-azure-linux-agent"></a>L’Agent de Linux Azure ##

L' [Agent de Linux Azure](virtual-machines-linux-agent-user-guide.md) (waagent) est requis à correctement configurer une machine virtuelle de Linux dans Azure. Vous pouvez obtenir la version la plus récente, les problèmes des fichiers ou envoyer des demandes d’extraction à [mis en pension de GitHub Agent de Linux](https://github.com/Azure/WALinuxAgent).

- L’agent de Linux est publié sous la licence Apache 2.0. De nombreuses distributions fournissent déjà tr/min ou deb packages pour l’agent, et donc dans certains cas cela peut être installé et mis à jour avec peu d’efforts.

- L’Agent de Linux Azure nécessite Python v2.6 +.

- L’agent requiert également le module python-pyasn1. La plupart des distributions fournissent sous la forme d’un package distinct qui peut être installé.

- Dans certains cas l’Agent de Linux Azure n’est peut-être pas compatible avec NetworkManager. Nombre des packages RPM/Deb fournies par les distributions de configurer NetworkManager comme un conflit pour le package de la waagent et ainsi désinstallera NetworkManager lorsque vous installez le package de l’agent de Linux.


## <a name="general-linux-system-requirements"></a>Exigences générales du système Linux ##

- Modifier la ligne de démarrage du noyau dans GRUB ou GRUB2 pour inclure les paramètres suivants. Cela garantit également tous les messages de la console sont envoyées sur le premier port série, qui peut aider à Azure prise en charge avec les problèmes de débogage :

        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300

    Cela garantit également tous les messages de la console sont envoyées sur le premier port série, qui peut aider à Azure prise en charge avec les problèmes de débogage.

    Outre ce qui précède, il est recommandé de *Supprimer* les paramètres suivants s’ils existent :

        rhgb quiet crashkernel=auto

    Graphique et silencieux de démarrage ne sont pas utiles dans un environnement de nuage dans lequel nous souhaitons tous les journaux à envoyer au port série.

    Le `crashkernel` peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible de l’ordinateur virtuel de 128 Mo ou plus, qui peut être problématique sur la plus petite taille de machine virtuelle.

- Installation de l’Agent de Linux Azure

    L’Agent de Linux Azure est requis pour la mise en service d’une image de Linux sur Azure.  De nombreuses distributions fournissent l’agent sous la forme d’un package RPM ou Deb (le package est généralement appelé 'WALinuxAgent' ou 'walinuxagent').  L’agent peut également être installé manuellement en suivant les étapes décrites dans le [Guide de l’Agent de Linux](virtual-machines-linux-agent-user-guide.md).

- Assurez-vous que le serveur SSH est installé et configuré pour démarrer à l’amorçage.  Il s’agit de la valeur par défaut.

- Ne créez pas d’espace d’échange sur le disque du système d’exploitation

    L’Agent de Linux Azure peut configurer automatiquement l’espace de swap en utilisant le disque de ressource locale qui est attaché à l’ordinateur virtuel après avoir déployé sur Azure. Notez que le disque de ressources local est un disque *temporaire* et doit être vidé lorsque la machine virtuelle est arrêtée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifier les paramètres suivants dans la /etc/waagent.conf de manière appropriée :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- En tant que dernière étape, exécutez les commandes suivantes pour deprovision la machine virtuelle :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

    >[AZURE.NOTE] Sur Virtualbox, vous pouvez voir le message d’erreur suivant après l’exécution de ' waagent-force - annulation ' : `[Errno 5] Input/output error`. Ce message d’erreur n’est pas critique et peut être ignoré.

- Vous devrez puis arrêtez l’ordinateur virtuel et télécharger le disque dur virtuel sur Azure.
