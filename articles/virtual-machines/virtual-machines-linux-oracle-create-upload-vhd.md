<properties
    pageTitle="Créer et télécharger un disque dur virtuel de Linux Oracle | Microsoft Azure"
    description="Apprenez à créer et télécharger un Azure disque dur virtuel (VHD) qui contient un système d’exploitation Linux d’Oracle."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Préparer un ordinateur virtuel de Oracle Linux pour Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Conditions préalables ##

Cet article suppose que vous avez déjà installé un système d’exploitation d’Oracle Linux sur un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd, par exemple une solution de virtualisation telles que Hyper-V. Pour obtenir des instructions, consultez [installation du rôle Hyper-V et configurer une Machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).


### <a name="oracle-linux-installation-notes"></a>Notes d’installation Oracle Linux

- Consultez également [Les Notes d’Installation Linux générales](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour plus de conseils sur la préparation de Linux pour Azure.

- Noyau de compatibles Red Hat d’Oracle et leur UEK3 (noyau d’entreprise incassable) sont tous deux pris en charge sur Hyper-V et Azure. Pour des résultats optimaux, veillez à mettre à jour le noyau plus récent lors de la préparation de votre disque dur virtuel de Linux Oracle.

- Les UEK2 d’Oracle n’est pas pris en charge sur Hyper-V et Azure car il n’inclut pas les pilotes nécessaires.

- Le format VHDX n’est pas pris en charge dans Azure, uniquement **fixe disque dur virtuel**.  Vous pouvez convertir le disque au format de disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l’applet de commande convert-disque dur virtuel.

- Lors de l’installation du système Linux, il est recommandé que vous utilisez des partitions standard plutôt que LVM (souvent par défaut pour la plupart des installations). Cela évitera LVM nom est en conflit avec les machines virtuelles de cloné, particulièrement si un disque du système d’exploitation a besoin doit être jointe à une autre VM pour le dépannage. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur les disques de données si vous préférez.

- NUMA n’est pas pris en charge pour les formats de machine virtuelle plus grandes en raison d’un bogue dans les versions de noyau Linux sous 2.6.37. Ce problème concerne principalement les distributions à l’aide de l’en amont noyau de Red Hat 2.6.32. Installation manuelle de l’agent d’Azure Linux (waagent) désactivera automatiquement NUMA dans la configuration de GRUB pour le noyau Linux. Vous trouverez plus d’informations à ce sujet dans les étapes suivantes.

- Ne configurez pas une partition de swap sur le disque du système d’exploitation. L’agent de Linux peut être configuré pour créer un fichier d’échange sur le disque de ressource temporaire.  Vous trouverez plus d’informations à ce sujet dans les étapes suivantes.

- Tous les disques durs virtuels doivent avoir des tailles qui sont des multiples de 1 Mo.

- Assurez-vous que le `Addons` référentiel est activé. Modifiez le fichier `/etc/yum.repo.d/public-yum-ol6.repo`(6 de Linux Oracle) ou `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) et modifiez la ligne `enabled=0` à `enabled=1` **[ol6_addons]** ou **[ol7_addons]** dans ce fichier.


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 + ##

Vous devez effectuer les étapes de configuration spécifique dans le système d’exploitation de l’ordinateur virtuel exécuté dans Azure.

1. Dans le volet central du Gestionnaire Hyper-V, sélectionnez l’ordinateur virtuel.

2. Cliquez sur **se connecter** pour ouvrir la fenêtre de la machine virtuelle.

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

        # chkconfig network on

8. Installer les python-pyasn1 en exécutant la commande suivante :

        # sudo yum install python-pyasn1

9.  Modifier la ligne de démarrage du noyau de votre configuration de grub pour inclure des paramètres de noyau supplémentaires pour Azure. Faire de cette ouverture « / boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Cela garantit également tous les messages de la console sont envoyées sur le premier port série, qui peut aider à Azure prise en charge avec les problèmes de débogage. Cela désactivera NUMA en raison d’un bogue du noyau de Red Hat compatible d’Oracle.

    En plus de ce qui précède, il est recommandé de *Supprimer* les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux de démarrage ne sont pas utiles dans un environnement de nuage dans lequel nous souhaitons tous les journaux à envoyer au port série.

    Le `crashkernel` peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible de l’ordinateur virtuel de 128 Mo ou plus, qui peut être problématique sur la plus petite taille de machine virtuelle.


10. Assurez-vous que le serveur SSH est installé et configuré pour démarrer à l’amorçage.  Il s’agit de la valeur par défaut.

11. Installation de l’Agent de Linux Azure en exécutant la commande suivante. La version la plus récente est 2.0.15.

        # sudo yum install WALinuxAgent

    Notez que l’installation du package WALinuxAgent supprimera le NetworkManager et packages de gnome-NetworkManager s’ils n’étaient pas déjà supprimés comme décrit à l’étape 2.

12. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace de swap en utilisant le disque de ressource locale qui est attaché à l’ordinateur virtuel après avoir déployé sur Azure. Notez que le disque de ressources local est un disque *temporaire* et doit être vidé lorsque la machine virtuelle est arrêtée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifier les paramètres suivants dans la /etc/waagent.conf de manière appropriée :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est prêt à être téléchargé vers Azure.


----------


## <a name="oracle-linux-70"></a>Oracle Linux 7.0 + ##

**Modifications dans Oracle Linux 7**

Préparation d’un ordinateur virtuel de Oracle Linux 7 pour Azure est très similaire à celui Oracle Linux 6, cependant, il existe plusieurs différences importantes à noter :

 - À la fois le noyau compatible Red Hat et UEK3 d’Oracle sont pris en charge dans Azure.  Le noyau UEK3 est recommandé.
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

5.  Modifier les règles udev pour éviter de générer des règles statiques pour les interfaces Ethernet. Ces règles peuvent provoquer des problèmes lors du clonage d’un ordinateur virtuel dans Microsoft Azure ou Hyper-v :

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Assurez-vous que le service réseau démarre au démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

7. Installez le package de python-pyasn1 en exécutant la commande suivante :

        # sudo yum install python-pyasn1

8.  Exécutez la commande suivante pour supprimer les métadonnées actuelles d’yum et installer les mises à jour :

        # sudo yum clean all
        # sudo yum -y update

9.  Modifier la ligne de démarrage du noyau de votre configuration de grub pour inclure des paramètres de noyau supplémentaires pour Azure. Pour ce faire ouvrir « / etc/par défaut/grub » dans un éditeur de texte et le modifier le `GRUB_CMDLINE_LINUX` paramètre, par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Cela garantit également tous les messages de la console sont envoyées sur le premier port série, qui peut aider à Azure prise en charge avec les problèmes de débogage. Il désactive également les conventions d’appellation OEL 7 nouvelles pour les cartes réseau. En plus de ce qui précède, il est recommandé de *Supprimer* les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux de démarrage ne sont pas utiles dans un environnement de nuage dans lequel nous souhaitons tous les journaux à envoyer au port série.

    Le `crashkernel` peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible de l’ordinateur virtuel de 128 Mo ou plus, qui peut être problématique sur la plus petite taille de machine virtuelle.


10. Une fois que vous avez terminé la modification « / etc/par défaut/grub » par ci-dessus, exécutez la commande suivante pour recréer la configuration de grub :

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11. Assurez-vous que le serveur SSH est installé et configuré pour démarrer à l’amorçage.  Il s’agit de la valeur par défaut.

12. Installation de l’Agent de Linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

13. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace de swap en utilisant le disque de ressource locale qui est attaché à l’ordinateur virtuel après avoir déployé sur Azure. Notez que le disque de ressources local est un disque *temporaire* et doit être vidé lorsque la machine virtuelle est arrêtée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifier les paramètres suivants dans la /etc/waagent.conf de manière appropriée :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est prêt à être téléchargé vers Azure.


## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre .vhd Oracle Linux pour créer de nouvelles machines virtuelles dans Azure. Si c’est la première fois que vous téléchargez le fichier .vhd sur Azure, consultez les étapes 2 et 3 dans [Création et chargement d’un disque dur virtuel qui contient le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).
