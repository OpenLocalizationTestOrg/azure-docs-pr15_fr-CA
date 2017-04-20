<properties
    pageTitle="Préparer le disque dur virtuel de Debian Linux | Microsoft Azure"
    description="Apprenez à créer des 7 Debian & 8 fichiers de disque dur virtuel pour le déploiement dans Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>



# <a name="prepare-a-debian-vhd-for-azure"></a>Préparer un disque dur virtuel Debian pour Azure

## <a name="prerequisites"></a>Conditions préalables
Cette section suppose que vous avez déjà installé un système d’exploitation de Debian Linux à partir d’un fichier .iso téléchargé à partir du [site Web de Debian](https://www.debian.org/distrib/) sur un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd ; Hyper-V n'est qu’un exemple. Pour obtenir des instructions à l’aide de Hyper-V, voir [installer le rôle Hyper-V et configurer une Machine virtuelle](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="installation-notes"></a>Notes d’installation

- Consultez également [Les Notes d’Installation Linux générales](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour plus de conseils sur la préparation de Linux pour Azure.
- Le nouveau format VHDX n’est pas pris en charge dans Azure. Vous pouvez convertir le disque au format de disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l’applet de commande **convert-disque dur virtuel** .
- Lors de l’installation du système Linux, il est recommandé que vous utilisez des partitions standard plutôt que LVM (souvent par défaut pour la plupart des installations). Cela évitera LVM nom est en conflit avec les machines virtuelles de cloné, particulièrement si un disque du système d’exploitation a besoin doit être jointe à une autre VM pour le dépannage. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur les disques de données si vous préférez.
- Ne configurez pas une partition de swap sur le disque du système d’exploitation. L’agent Azure Linux peut être configuré pour créer un fichier d’échange sur le disque de ressource temporaire. Vous trouverez plus d’informations à ce sujet dans les étapes suivantes.
- Tous les disques durs virtuels doivent avoir des tailles qui sont des multiples de 1 Mo.


## <a name="use-azure-manage-to-create-debian-vhds"></a>Permet de gérer Azure pour créer des disques durs virtuels Debian

Il existe des outils permettant de générer des disques durs virtuels Debian pour Azure, tels que le [azure-gérer](https://github.com/credativ/azure-manage) scripts à partir de [credativ](http://www.credativ.com/). Il s’agit de l’approche recommandée par rapport à la création d’une image à partir de zéro. Par exemple, pour créer un disque dur virtuel 8 Debian, exécutez les commandes suivantes pour télécharger azure gérer (et dépendances) et exécutez le script azure_build_image :

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Préparer manuellement un disque dur virtuel Debian

1. Dans le Gestionnaire Hyper-V, sélectionnez l’ordinateur virtuel.

2. Cliquez sur **se connecter** pour ouvrir une fenêtre de console pour la machine virtuelle.

3. Commentez la ligne pour la **deb cdrom** dans `/etc/apt/source.list` si vous configurez la machine virtuelle par rapport à un fichier ISO.

4. Modifier la `/etc/default/grub` et modifiez le paramètre **GRUB_CMDLINE_LINUX** comme suit pour inclure des paramètres de noyau supplémentaires pour Azure.

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"

5. Reconstruire le grub et exécutez :

        # sudo update-grub

6. Ajouter les référentiels Azure de Debian à /etc/apt/sources.list pour Debian 7 ou 8 :

    **Debian 7.x « Wheezy »**

        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


    **Debian 8.x « Jessica »**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Installer l’Agent Linux Azure :

        # sudo apt-get update
        # sudo apt-get install waagent

8. 7 Debian, il est nécessaire pour exécuter le noyau 3.16 à partir du référentiel de wheezy-backports. Tout d’abord créer un fichier nommé /etc/apt/preferences.d/linux.pref avec le contenu suivant :

        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500

    Puis exécutez « sudo apt-get install linux-image-amd64 » pour installer le nouveau noyau.

8. Deprovision de la machine virtuelle et les préparer pour la mise en service sur Azure et exécuter :

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Cliquez sur **Action** -> Arrêter vers le bas dans le Gestionnaire Hyper-V. Votre VHD Linux est prêt à être téléchargé vers Azure.


## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à utiliser votre disque dur virtuel de Debian pour créer de nouvelles machines virtuelles dans Azure. Si c’est la première fois que vous téléchargez le fichier .vhd sur Azure, consultez les étapes 2 et 3 dans [Création et chargement d’un disque dur virtuel qui contient le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).
