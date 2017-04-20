<properties
    pageTitle="Créer et télécharger un disque dur virtuel de Linux Ubuntu dans Azure"
    description="Apprenez à créer et télécharger un Azure disque dur virtuel (VHD) qui contient un système d’exploitation Ubuntu Linux."
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

# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Préparer un ordinateur virtuel de Ubuntu pour Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Images de nuage officiels Ubuntu
Ubuntu publie maintenant les disques durs virtuels Azure officiel de téléchargement à l’adresse [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Si vous avez besoin créer votre propre image d’Ubuntu spécialisé pour Azure, plutôt que d’utiliser la procédure manuelle ci-après il est recommandé de démarrer avec ces connu d’utilisation des disques durs virtuels et la personnaliser selon vos besoins. Les dernières versions de l’image peuvent toujours être trouvées à l’emplacement suivant :

 - 12.04 précise/Ubuntu : [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
 - 14.04/fidèle d’Ubuntu : [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 16.04/Xenial : [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)


## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous avez déjà installé un système d’exploitation Ubuntu Linux sur un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd, par exemple une solution de virtualisation telles que Hyper-V. Pour obtenir des instructions, consultez [installation du rôle Hyper-V et configurer une Machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

**Notes d’installation Ubuntu**

- Consultez également [Les Notes d’Installation Linux générales](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour plus de conseils sur la préparation de Linux pour Azure.
- Le format VHDX n’est pas pris en charge dans Azure, uniquement **fixe disque dur virtuel**.  Vous pouvez convertir le disque au format de disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l’applet de commande convert-disque dur virtuel.
- Lors de l’installation du système Linux, il est recommandé que vous utilisez des partitions standard plutôt que LVM (souvent par défaut pour la plupart des installations). Cela évitera LVM nom est en conflit avec les machines virtuelles de cloné, particulièrement si un disque du système d’exploitation a besoin doit être jointe à une autre VM pour le dépannage. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur les disques de données si vous préférez.
- Ne configurez pas une partition de swap sur le disque du système d’exploitation. L’agent de Linux peut être configuré pour créer un fichier d’échange sur le disque de ressource temporaire.  Vous trouverez plus d’informations à ce sujet dans les étapes suivantes.
- Tous les disques durs virtuels doivent avoir des tailles qui sont des multiples de 1 Mo.


## <a name="manual-steps"></a>Étapes manuelles

> [AZURE.NOTE] Avant de créer votre propre image d’Ubuntu personnalisée pour Azure, envisagez d’utiliser les images provenant de [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) à la place.


1. Dans le volet central du Gestionnaire Hyper-V, sélectionnez l’ordinateur virtuel.

2. Cliquez sur **se connecter** pour ouvrir la fenêtre de la machine virtuelle.

3.  Remplacez les référentiels en cours dans l’image à utiliser les pensions Azure d’Ubuntu. Les procédures varient légèrement selon la version d’Ubuntu.

    Avant de modifier /etc/apt/sources.list, il est recommandé d’effectuer une sauvegarde :

        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04 :

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04 :

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

4. Les images d’Ubuntu Azure suivez maintenant le noyau de l' *activation du matériel* (HOUE). Mettre à jour le système d’exploitation pour le noyau plus récent en exécutant les commandes suivantes :

    Ubuntu 12.04 :

        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 14.04 :

        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot


5. Modifier la ligne de démarrage du noyau de Grub inclure des paramètres de noyau supplémentaires pour Azure. Pour ce faire ouvrir « / etc/par défaut/grub » dans un éditeur de texte, recherchez la variable appelée `GRUB_CMDLINE_LINUX_DEFAULT` (ou de l’ajouter si nécessaire) et le modifier pour inclure les paramètres suivants :

        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Enregistrez et fermez ce fichier et exécutez «`sudo update-grub`». Ainsi, tous les messages de la console sont envoyées sur le premier port série, qui peut aider à Azure support technique avec les problèmes de débogage.

6.  Assurez-vous que le serveur SSH est installé et configuré pour démarrer à l’amorçage.  Il s’agit de la valeur par défaut.

7.  Installer l’Agent Linux Azure :

        # sudo apt-get update
        # sudo apt-get install walinuxagent

    Notez que l’installation de le `walinuxagent` package va supprimer la `NetworkManager` et `NetworkManager-gnome` packages, s’ils sont installés.

8.  Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est prêt à être téléchargé vers Azure.


## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel de Ubuntu Linux pour créer de nouvelles machines virtuelles dans Azure. Si c’est la première fois que vous téléchargez le fichier .vhd sur Azure, consultez les étapes 2 et 3 dans [Création et chargement d’un disque dur virtuel qui contient le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).

## <a name="references"></a>Références ##

Noyau de prise en charge (HOUE) du matériel Ubuntu :

- [http://blog.utlemming.org/2015/01/Ubuntu-1404-Azure-images-Now-Tracking.HTML](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
- [http://blog.utlemming.org/2015/02/1204-Azure-cloud-images-Now-Using-hwe.HTML](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)
