<properties
    pageTitle="Introduction à Linux dans Azure | Microsoft Azure"
    description="Obtenir des informations sur l’utilisation d’ordinateurs virtuels Linux sur Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
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

#<a name="introduction-to-linux-on-azure"></a>Introduction à Linux sur Azure

Cette rubrique fournit une vue d’ensemble de certains aspects de l’utilisation d’ordinateurs virtuels Linux dans le nuage Azure. Déploiement d’une machine virtuelle Linux est un processus simple à l’aide d’une image de la galerie.


## <a name="authentication-usernames-passwords-and-ssh-keys"></a>Authentification : Noms d’utilisateur, mots de passe et clés SSH

Lorsque vous créez une machine virtuelle de Linux via le portail classique Azure, vous êtes invité à fournir un nom d’utilisateur, mot de passe ou une clé publique SSH. Le choix d’un nom d’utilisateur pour le déploiement d’une machine virtuelle de Linux sur Azure est soumis à la contrainte suivante : noms de comptes système (UID < 100) est déjà présent dans l’ordinateur virtuel ne sont pas autorisés, « racine » par exemple.


 - Reportez-vous à la section [créer un ordinateur virtuel exécutant Linux](virtual-machines-linux-quick-create-cli.md)
 - Apprendre [à utiliser le protocole SSH avec Linux sur Azure](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="obtaining-superuser-privileges-using-sudo"></a>Obtention des privilèges de superutilisateur à l’aide de`sudo`

Le compte d’utilisateur qui est spécifié au cours du déploiement d’instance de machine virtuelle sur Azure est un compte privilégié. Ce compte est configuré par l’Agent de Linux Azure pour être en mesure d’élever des privilèges au niveau racine (compte superutilisateur) à l’aide du `sudo` utilitaire. Une fois connecté à l’aide de ce compte d’utilisateur, vous ne pourrez pas exécuter des commandes en tant que racine à l’aide de la syntaxe de commande

    # sudo <COMMAND>

Vous pouvez éventuellement obtenir un shell de racine à l’aide de **sudo -s**.

- Reportez-vous à la section [utilisation des privilèges racine sur les machines virtuelles de Linux dans Azure](virtual-machines-linux-use-root-privileges.md)


## <a name="firewall-configuration"></a>Configuration du pare-feu

Azure fournit un filtre de paquets entrants qui limite la connectivité aux ports spécifiés dans le portail classique Azure. Par défaut, le seul port autorisé est SSH. Vous pouvez ouvrir l’accès à des ports supplémentaires sur votre ordinateur virtuel de Linux en configurant des points de terminaison dans Azure portal classique :

 - Voir : [comment configurer des points de terminaison à une Machine virtuelle](virtual-machines-windows-classic-setup-endpoints.md)

Les images dans la galerie d’Azure Linux n’activez pas le pare-feu *iptables* par défaut. Si vous le souhaitez, le pare-feu peut être configuré pour fournir un filtrage supplémentaire.


## <a name="hostname-changes"></a>Modifications de nom d’hôte

Lorsque vous déployez initialement une instance d’une image de Linux, vous devez fournir un nom d’hôte de l’ordinateur virtuel. Une fois que l’ordinateur virtuel est en cours d’exécution, ce nom d’hôte est publiée sur les serveurs DNS de plate-forme afin que plusieurs ordinateurs virtuels sont connectés entre eux puisse effectuer des recherches d’adresses IP à l’aide de noms d’hôtes.

Si les modifications de nom d’hôte sont souhaitées après qu’un ordinateur virtuel a été déployé, utilisez la commande

    # sudo hostname <newname>

L’Agent de Linux Azure inclut une fonctionnalité pour détecter automatiquement ce changement de nom et de configurer correctement l’ordinateur virtuel pour conserver cette modification et publier cette modification vers les serveurs DNS de plate-forme.

 - [Guide de l’utilisateur Azure Agent de Linux](virtual-machines-linux-agent-user-guide.md)

### <a name="cloud-init"></a>Nuage-Init.
Images **Ubuntu** et **CoreOS** utilisent nuage-init sur Azure, qui fournit des fonctions supplémentaires pour démarrer un ordinateur virtuel.

 - [Comment faire pour injecter des données personnalisées](virtual-machines-windows-classic-inject-custom-data.md)
 - [Données personnalisées et nuage-Init sur Microsoft Azure](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [Créer des Partitions de Swap Azure à l’aide de nuage-Init.](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [Comment utiliser CoreOS sur Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="virtual-machine-image-capture"></a>Capture d’Image de Machine virtuelle

Azure offre la possibilité de capturer l’état d’un ordinateur virtuel existant en une image qui peut ensuite être utilisée pour déployer les instances de l’ordinateur virtuel supplémentaire. L’Agent de Linux Azure peut être utilisée pour restaurer certains de la personnalisation qui a été effectuée pendant le processus de déploiement. Vous pouvez suivre les étapes ci-dessous pour une machine virtuelle sous la forme d’une image de capture :

1. Exécutez **waagent-annulation** pour annuler la mise en service de personnalisation. Ou **waagent-annulation + utilisateur** si vous le souhaitez, supprimer le compte d’utilisateur spécifié au cours de la mise en service et toutes les données associées.

2. Arrêt bas/hors tension l’ordinateur virtuel.

3. Cliquez sur *Capture* dans Azure portal classique, ou utiliser les outils de Powershell ou l’interface CLI pour capturer l’ordinateur virtuel en tant qu’image.

 - Voir : [Comment faire pour capturer une Machine virtuelle de Linux à utiliser en tant que modèle](virtual-machines-linux-classic-capture-image.md)


## <a name="attaching-disks"></a>Connexion de disques

Chaque machine virtuelle possède une temporaire local *disque de la ressource* associée. Étant donné que les données sur un disque de ressource ne peuvent être durables entre les redémarrages, il est souvent utilisé par les applications et les processus en cours d’exécution sur l’ordinateur virtuel pour le stockage transitoire et **temporaire** de données. Il est également utilisé pour stocker la page ou pour permuter les fichiers du système d’exploitation.

Sous Linux, le disque de ressources est généralement géré par l’Agent de Linux Azure et automatiquement monté sur **/mnt/resource** (ou **/mnt/mnt** sur Ubuntu images).


>[AZURE.NOTE] Notez que le disque de ressources est un disque **temporaire** et peut-être être supprimé et reformaté lors du redémarrage de la machine virtuelle.

Sous Linux, le disque de données peut être nommé par le noyau `/dev/sdc`, et les utilisateurs devront partitionner, formater et monter cette ressource. Ce sujet est traité étape par étape dans le didacticiel : [comment attacher un disque de données à une Machine virtuelle](virtual-machines-linux-classic-attach-disk.md).

 - **Voir aussi :** [Configurez le logiciel RAID sous Linux](virtual-machines-linux-configure-raid.md)  &  [Configurer le Gestionnaire de volume logique sur un ordinateur virtuel Linux Azure](virtual-machines-linux-configure-lvm.md)

