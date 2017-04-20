<properties
    pageTitle="Créer et télécharger un VHD Linux basée sur CentOS dans Azure"
    description="Apprenez à créer et télécharger un Azure disque dur virtuel (VHD) qui contient un système d’exploitation Linux basée sur CentOS."
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
    ms.date="05/09/2016"
    ms.author="szarkos"/>

# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Préparer un ordinateur virtuel basé sur CentOS pour Azure

- [Préparer un ordinateur virtuel de 6.x CentOS pour Azure](#centos-6x)
- [Préparer un ordinateur virtuel de CentOS 7.0 + pour Azure](#centos-70)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Conditions préalables ##

Cet article suppose que vous avez déjà installé un CentOS (ou dérivé similaire) système d’exploitation Linux sur un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd, par exemple une solution de virtualisation telles que Hyper-V. Pour obtenir des instructions, consultez [installation du rôle Hyper-V et configurer une Machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).


**Notes d’installation centOS**

- Consultez également [Les Notes d’Installation Linux générales](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour plus de conseils sur la préparation de Linux pour Azure.

- Le format VHDX n’est pas pris en charge dans Azure, uniquement **fixe disque dur virtuel**.  Vous pouvez convertir le disque au format de disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l’applet de commande convert-disque dur virtuel.

- Lors de l’installation du système Linux, il est recommandé que vous utilisez des partitions standard plutôt que LVM (souvent par défaut pour la plupart des installations). Cela évitera LVM nom est en conflit avec les machines virtuelles de cloné, particulièrement si un disque du système d’exploitation a besoin doit être jointe à une autre VM pour le dépannage.  [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur les disques de données si vous préférez.

- NUMA n’est pas pris en charge pour les formats de machine virtuelle plus grandes en raison d’un bogue dans les versions de noyau Linux sous 2.6.37. Ce problème concerne principalement les distributions à l’aide de l’en amont noyau de Red Hat 2.6.32. Installation manuelle de l’agent d’Azure Linux (waagent) désactivera automatiquement NUMA dans la configuration de GRUB pour le noyau Linux. Vous trouverez plus d’informations à ce sujet dans les étapes suivantes.

- Ne configurez pas une partition de swap sur le disque du système d’exploitation. L’agent de Linux peut être configuré pour créer un fichier d’échange sur le disque de ressource temporaire.  Vous trouverez plus d’informations à ce sujet dans les étapes suivantes.

- Tous les disques durs virtuels doivent avoir des tailles qui sont des multiples de 1 Mo.


## <a name="centos-6x"></a>CentOS 6.x ##

1. Dans le Gestionnaire Hyper-V, sélectionnez l’ordinateur virtuel.

2. Cliquez sur **se connecter** pour ouvrir une fenêtre de console pour la machine virtuelle.

3. Désinstaller NetworkManager en exécutant la commande suivante :

        # sudo rpm -e --nodeps NetworkManager

    **Remarque :** Si le package n’est pas déjà installé, cette commande échoue avec un message d’erreur. Ce comportement est normal.

4.  Créez un fichier nommé **réseau** dans le `/etc/sysconfig/` répertoire qui contient le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Créez un fichier nommé **ifcfg-eth0** dans le `/etc/sysconfig/network-scripts/` répertoire qui contient le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modifier les règles udev pour éviter de générer des règles statiques pour les interfaces Ethernet. Ces règles peuvent provoquer des problèmes lors du clonage d’un ordinateur virtuel dans Microsoft Azure ou Hyper-v :

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules


7. Assurez-vous que le service réseau démarre au démarrage en exécutant la commande suivante :

        # sudo chkconfig network on


8. **6.3 centOS uniquement**: installez les pilotes pour les Services d’intégration Linux (LIS).

    **Important : L’étape est uniquement valide pour CentOS 6.3 et antérieures.**  Dans CentOS 6.4 +, les Services d’intégration Linux sont *déjà disponibles dans le noyau standard*.

    - Suivez les instructions d’installation sur la [page de téléchargement de LIS](https://www.microsoft.com/en-us/download/details.aspx?id=46842) et installer le fichier RPM à votre image.  


9. Installez le package de python-pyasn1 en exécutant la commande suivante :

        # sudo yum install python-pyasn1

10. Si vous souhaitez utiliser les miroirs OpenLogic qui sont hébergés dans les centres de données Azure, puis remplacez le fichier /etc/yum.repos.d/CentOS-Base.repo avec les référentiels suivants.  Cela ajoutera également le référentiel **[openlogic]** qui inclut des packages pour l’agent Azure Linux :

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    **Remarque :** Le reste de ce guide suppose que vous utilisez au moins la mis en pension [openlogic], qui sera utilisé pour installer l’agent Azure Linux ci-dessous.


11. Ajoutez la ligne suivante à la /etc/yum.conf :

        http_caching=packages

    Et **sur le point 6.3 CentOS uniquement** , ajoutez la ligne suivante :

        exclude=kernel*

12. Désactiver le module d’yum « fastestmirror » en modifiant le fichier « / etc/yum/pluginconf.d/fastestmirror.conf » et sous `[main]`, tapez la commande suivante :

        set enabled=0

13. Exécutez la commande suivante pour supprimer les métadonnées d’yum en cours :

        # yum clean all

14. **Sur CentOS 6.3 uniquement**, mise à jour du noyau à l’aide de la commande suivante :

        # sudo yum --disableexcludes=all install kernel

15. Modifier la ligne de démarrage du noyau de votre configuration de grub pour inclure des paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez « / boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Cela garantit également tous les messages de la console sont envoyées sur le premier port série, qui peut aider à Azure prise en charge avec les problèmes de débogage. Cela désactivera NUMA en raison d’un bogue dans la version du noyau utilisée par CentOS 6.

    En plus de ce qui précède, il est recommandé de *Supprimer* les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux de démarrage ne sont pas utiles dans un environnement de nuage dans lequel nous souhaitons tous les journaux à envoyer au port série.

    Le `crashkernel` peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible de l’ordinateur virtuel de 128 Mo ou plus, qui peut être problématique sur la plus petite taille de machine virtuelle.


16. Assurez-vous que le serveur SSH est installé et configuré pour démarrer à l’amorçage.  Il s’agit de la valeur par défaut.

17. Installation de l’Agent de Linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent

    Notez que l’installation du package WALinuxAgent supprimera le NetworkManager et packages de gnome-NetworkManager s’ils n’étaient pas déjà supprimés comme décrit à l’étape 2.

18. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace de swap en utilisant le disque de ressource locale qui est attaché à l’ordinateur virtuel après avoir déployé sur Azure. Notez que le disque de ressources local est un disque *temporaire* et doit être vidé lorsque la machine virtuelle est arrêtée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifier les paramètres suivants dans la /etc/waagent.conf de manière appropriée :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

20. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est prêt à être téléchargé vers Azure.


----------


## <a name="centos-70"></a>CentOS 7.0 + ##

**Modifications dans CentOS 7 (et dérivés similaires)**

Préparation d’un ordinateur virtuel de CentOS 7 pour Azure est très similaire à 6 CentOS, cependant, il existe plusieurs différences importantes à noter :

 - Le package NetworkManager conflit avec l’agent Azure Linux. Ce package est installé par défaut et il est recommandé qu’il n’est pas supprimé.
 - GRUB2 est maintenant utilisé comme le chargeur de démarrage par défaut, la procédure de modification des paramètres de noyau a changé (voir ci-dessous).
 - XFS est désormais le système de fichiers par défaut. Le système de fichiers ext4 peut encore être utilisé si vous le souhaitez.


**Étapes de configuration**

1. Dans le Gestionnaire Hyper-V, sélectionnez l’ordinateur virtuel.

2. Cliquez sur **se connecter** pour ouvrir une fenêtre de console pour la machine virtuelle.

3.  Créez un fichier nommé **réseau** dans le `/etc/sysconfig/` répertoire qui contient le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Créez un fichier nommé **ifcfg-eth0** dans le `/etc/sysconfig/network-scripts/` répertoire qui contient le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modifier les règles udev pour éviter de générer des règles statiques pour les interfaces Ethernet. Ces règles peuvent provoquer des problèmes lors du clonage d’un ordinateur virtuel dans Microsoft Azure ou Hyper-v :

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Assurez-vous que le service réseau démarre au démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

7. Installez le package de python-pyasn1 en exécutant la commande suivante :

        # sudo yum install python-pyasn1

8. Si vous souhaitez utiliser les miroirs OpenLogic qui sont hébergés dans les centres de données Azure, puis remplacez le fichier /etc/yum.repos.d/CentOS-Base.repo avec les référentiels suivants.  Cela ajoutera également le référentiel **[openlogic]** qui inclut des packages pour l’agent Azure Linux :

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7



    **Remarque :** Le reste de ce guide suppose que vous utilisez au moins la mis en pension [openlogic], qui sera utilisé pour installer l’agent Azure Linux ci-dessous.

9.  Exécutez la commande suivante pour supprimer les métadonnées actuelles d’yum et installer les mises à jour :

        # sudo yum clean all
        # sudo yum -y update

10. Modifier la ligne de démarrage du noyau de votre configuration de grub pour inclure des paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrir « / etc/par défaut/grub » dans un éditeur de texte et le modifier le `GRUB_CMDLINE_LINUX` paramètre, par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Cela garantit également tous les messages de la console sont envoyées sur le premier port série, qui peut aider à Azure prise en charge avec les problèmes de débogage. Il désactive également les conventions d’appellation CentOS 7 nouvelles pour les cartes réseau. En plus de ce qui précède, il est recommandé de *Supprimer* les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux de démarrage ne sont pas utiles dans un environnement de nuage dans lequel nous souhaitons tous les journaux à envoyer au port série.

    Le `crashkernel` peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible de l’ordinateur virtuel de 128 Mo ou plus, qui peut être problématique sur la plus petite taille de machine virtuelle.

11. Une fois que vous avez terminé la modification « / etc/par défaut/grub » par ci-dessus, exécutez la commande suivante pour recréer la configuration de grub :

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12. Assurez-vous que le serveur SSH est installé et configuré pour démarrer à l’amorçage.  Il s’agit de la valeur par défaut.

13. **Uniquement si la création de l’image à partir de VMWare, VirtualBox ou KVM :** Ajouter des modules de Hyper-V dans initramfs :

    Modifier `/etc/dracut.conf`, ajouter du contenu :

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Reconstruire l’initramfs :

        # dracut –f -v

14. Installation de l’Agent de Linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

15. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace de swap en utilisant le disque de ressource locale qui est attaché à l’ordinateur virtuel après avoir déployé sur Azure. Notez que le disque de ressources local est un disque *temporaire* et doit être vidé lorsque la machine virtuelle est arrêtée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifier les paramètres suivants dans la /etc/waagent.conf de manière appropriée :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est prêt à être téléchargé vers Azure.

## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel de CentOS Linux pour créer de nouvelles machines virtuelles dans Azure. Si c’est la première fois que vous téléchargez le fichier .vhd sur Azure, consultez les étapes 2 et 3 dans [Création et chargement d’un disque dur virtuel qui contient le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).
