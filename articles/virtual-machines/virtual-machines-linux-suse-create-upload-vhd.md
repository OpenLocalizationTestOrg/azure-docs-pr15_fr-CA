<properties
    pageTitle="Créer et télécharger un disque dur virtuel de Linux SUSE dans Azure"
    description="Apprenez à créer et télécharger un Azure disque dur virtuel (VHD) qui contient un système d’exploitation SUSE Linux."
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
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Préparer un ordinateur virtuel SLES ou openSUSE pour Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Conditions préalables ##

Cet article suppose que vous avez déjà installé d’openSUSE système d’exploitation Linux SUSE sur un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd, par exemple une solution de virtualisation telles que Hyper-V. Pour obtenir des instructions, consultez [installation du rôle Hyper-V et configurer une Machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / notes d’installation openSUSE

- Consultez également [Les Notes d’Installation Linux générales](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour plus de conseils sur la préparation de Linux pour Azure.

- Le format VHDX n’est pas pris en charge dans Azure, uniquement **fixe disque dur virtuel**.  Vous pouvez convertir le disque au format de disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l’applet de commande convert-disque dur virtuel.

- Lors de l’installation du système Linux, il est recommandé que vous utilisez des partitions standard plutôt que LVM (souvent par défaut pour la plupart des installations). Cela évitera LVM nom est en conflit avec les machines virtuelles de cloné, particulièrement si un disque du système d’exploitation a besoin doit être jointe à une autre VM pour le dépannage. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur les disques de données si vous préférez.

- Ne configurez pas une partition de swap sur le disque du système d’exploitation. L’agent de Linux peut être configuré pour créer un fichier d’échange sur le disque de ressource temporaire.  Vous trouverez plus d’informations à ce sujet dans les étapes suivantes.

- Tous les disques durs virtuels doivent avoir des tailles qui sont des multiples de 1 Mo.


## <a name="use-suse-studio"></a>Utilisez SUSE Studio
[SUSE Studio](http://www.susestudio.com) peut facilement créer et gérer vos images SLES et openSUSE pour Azure et Hyper-V. Il s’agit de l’approche recommandée pour la personnalisation de vos propres images SLES et openSUSE.

Comme alternative à la création de votre propre disque dur virtuel, SUSE publie également des images BYOS (mettre votre propre abonnement) pour SLES à [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).


## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Préparer le Service Pack 4 SUSE Linux Enterprise Server 11 ##

1. Dans le volet central du Gestionnaire Hyper-V, sélectionnez l’ordinateur virtuel.

2. Cliquez sur **se connecter** pour ouvrir la fenêtre de la machine virtuelle.

3. Enregistrez votre système SUSE Linux Enterprise pour lui permettre de télécharger des mises à jour et installer des packages.

4. Mise à jour du système avec les derniers correctifs :

        # sudo zypper update

5. Installer l’Agent de Linux Azure à partir du référentiel SLES :

        # sudo zypper install WALinuxAgent

6. Vérifier si waagent est défini sur « on » dans la commande chkconfig et dans le cas contraire, l’activer pour autostart :
               
        # sudo chkconfig waagent on

7. Vérifiez si le service de waagent est en cours d’exécution et dans le cas contraire, démarrez-le : 

        # sudo service waagent start
                
8. Modifier la ligne de démarrage du noyau de votre configuration de grub pour inclure des paramètres de noyau supplémentaires pour Azure. Faire de cette ouverture « / boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Cela garantira que tous les messages de la console sont envoyées sur le premier port série, qui peut aider à Azure prise en charge avec les problèmes de débogage.

9. Confirmez que /boot/grub/menu.lst et/etc/fstab référencent à la fois le disque à l’aide de son UUID (par uuid) au lieu de l’ID du disque (par id). 

    Le disque UUID
    
        # ls /dev/disk/by-uuid/

    Si /dev/disk/by-id est utilisé, de mettre à jour la fstab /boot/grub/menu.lst et/etc/avec la valeur appropriée par uuid

    Avant modification
    
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1

    Après modification
    
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

10. Modifier les règles udev pour éviter de générer des règles statiques pour les interfaces Ethernet. Ces règles peuvent provoquer des problèmes lors du clonage d’un ordinateur virtuel dans Microsoft Azure ou Hyper-v :

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

11. Il est recommandé de modifier le fichier « / etc/sysconfig/network/dhcp » et modifiez le `DHCLIENT_SET_HOSTNAME` paramètre à ce qui suit :

        DHCLIENT_SET_HOSTNAME="no"

12. Dans « / etc/sudoers », commenter, ou supprimez les lignes suivantes, si elles existent :

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

13. Assurez-vous que le serveur SSH est installé et configuré pour démarrer à l’amorçage.  Il s’agit de la valeur par défaut.

14. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace de swap en utilisant le disque de ressource locale qui est attaché à l’ordinateur virtuel après avoir déployé sur Azure. Notez que le disque de ressources local est un disque *temporaire* et doit être vidé lorsque la machine virtuelle est arrêtée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifier les paramètres suivants dans la /etc/waagent.conf de manière appropriée :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est prêt à être téléchargé vers Azure.


----------

## <a name="prepare-opensuse-131"></a>Préparer openSUSE 13.1 + ##

1. Dans le volet central du Gestionnaire Hyper-V, sélectionnez l’ordinateur virtuel.

2. Cliquez sur **se connecter** pour ouvrir la fenêtre de la machine virtuelle.

3. Sur l’interpréteur de commandes, exécutez la commande '`zypper lr`'. Si cette commande renvoie une sortie semblable à la suivante, puis les référentiels sont configurés comme prévu, aucun ajustement n’est nécessaire (Notez que les numéros de version peuvent varier) :

        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

    Si la commande ne renvoie « Aucun référentiel définies... » Utilisez ensuite les commandes suivantes pour ajouter ces pensions :

        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

    Vous pouvez ensuite vérifier les référentiels ont été ajoutés en exécutant la commande '`zypper lr`' à nouveau. Dans le cas où un des référentiels de mise à jour appropriée n’est pas activé, activez-la avec la commande suivante :

        # sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Mise à jour du noyau vers la dernière version disponible :

        # sudo zypper up kernel-default

    Ou mettre à jour le système avec tous les correctifs les plus récents :

        # sudo zypper update

5.  Installez l’Agent Linux Azure.

        # sudo zypper install WALinuxAgent

6.  Modifier la ligne de démarrage du noyau de votre configuration de grub pour inclure des paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez « / boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Cela garantira que tous les messages de la console sont envoyées sur le premier port série, qui peut aider à Azure prise en charge avec les problèmes de débogage. Supprimez en outre, les paramètres suivants à partir de la ligne de démarrage du noyau, si elles existent :

        libata.atapi_enabled=0 reserve=0x1f0,0x8

7.  Il est recommandé de modifier le fichier « / etc/sysconfig/network/dhcp » et modifiez le `DHCLIENT_SET_HOSTNAME` paramètre à ce qui suit :

        DHCLIENT_SET_HOSTNAME="no"

8.  **Important :** Dans « / etc/sudoers », commenter, ou supprimez les lignes suivantes, si elles existent :

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  Assurez-vous que le serveur SSH est installé et configuré pour démarrer à l’amorçage.  Il s’agit de la valeur par défaut.

10. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace de swap en utilisant le disque de ressource locale qui est attaché à l’ordinateur virtuel après avoir déployé sur Azure. Notez que le disque de ressources local est un disque *temporaire* et doit être vidé lorsque la machine virtuelle est arrêtée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifier les paramètres suivants dans la /etc/waagent.conf de manière appropriée :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. Assurez-vous que l’Agent de Linux Azure s’exécute au démarrage :

        # sudo systemctl enable waagent.service

13. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est prêt à être téléchargé vers Azure.

## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel de SUSE Linux pour créer de nouvelles machines virtuelles dans Azure. Si c’est la première fois que vous téléchargez le fichier .vhd sur Azure, consultez les étapes 2 et 3 dans [Création et chargement d’un disque dur virtuel qui contient le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).
