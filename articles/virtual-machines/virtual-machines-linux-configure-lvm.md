<properties 
    pageTitle="Configurer LVM sur une machine virtuelle exécutant Linux | Microsoft Azure" 
    description="Découvrez comment configurer LVM sous Linux dans Azure." 
    services="virtual-machines-linux" 
    documentationCenter="na" 
    authors="szarkos"  
    manager="timlt" 
    editor="tysonn"
    tag="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="szark"/>


# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurer LVM sur un ordinateur virtuel Linux Azure

Ce document vous explique comment configurer le Gestionnaire de Volume logique (LVM) sur votre ordinateur virtuel Azure. Bien qu’il soit possible de configurer LVM sur tout disque connecté à la machine virtuelle, par défaut la plupart des images de nuage auront pas LVM configuré sur le disque du système d’exploitation. C’est pour éviter les problèmes avec les groupes de volumes en double si le disque du système d’exploitation est déjà associé à une autre VM de la distribution et le même type c'est-à-dire lors d’un scénario de récupération. Par conséquent, il est recommandé qu’ils utilisent le LVM sur les disques de données.


## <a name="linear-vs-striped-logical-volumes"></a>Linéaire par rapport à des volumes logiques agrégés par bande

LVM permet de combiner un certain nombre de disques physiques en un seul volume de stockage. Par défaut LVM crée généralement des volumes logiques linéaires, ce qui signifie que le stockage physique est concaténé. Dans ce cas les opérations de lecture/écriture en général uniquement recevront un seul disque. En revanche, nous pouvons aussi créer des volumes agrégés par bandes de logiques où les lectures et les écritures sont répartis sur plusieurs disques contenus dans le groupe de volumes (c'est-à-dire similaire à RAID 0). Pour des performances optimales, qu'il est probable que vous souhaiterez vos volumes logiques de répartition afin que les lectures et écritures utilisent tous vos disques de données attaché.

Ce document décrit comment combiner plusieurs disques de données dans un groupe de volumes unique, puis créez un volume logique. Les étapes ci-dessous sont quelque peu généralisées pour fonctionner avec la plupart des distributions. Dans la plupart des cas les utilitaires et les workflows de gestion LVM sur Azure ne sont pas fondamentalement différentes de celle des autres environnements. Comme d’habitude, Veuillez également consulter votre fournisseur Linux pour la documentation et les meilleures pratiques pour l’utilisation de LVM avec votre distribution particulière.


## <a name="attaching-data-disks"></a>Connexion de disques de données
Un sera généralement que vous voulez démarrer avec deux ou plusieurs disques de données vide lors de l’utilisation de LVM. En fonction de vos besoins en matière d’e/s, vous pouvez choisir de joindre des disques qui sont stockés dans notre stockage Standard, avec jusqu'à 500 IO/ps par disque ou de notre stockage Premium avec jusqu'à 5 000 e/s/ps par disque. Cet article va entrer dans les détails sur la façon de mettre en service et d’attacher des disques de données à une machine virtuelle Linux. Consultez le Microsoft Azure article [attacher un disque](virtual-machines-linux-add-disk.md) pour obtenir des instructions détaillées sur la façon d’attacher un disque de données vide à une machine virtuelle de Linux sur Azure.

## <a name="install-the-lvm-utilities"></a>Installez les utilitaires LVM.

- **Ubuntu**

        # sudo apt-get update
        # sudo apt-get install lvm2

- **RHEL, CentOS & Oracle Linux**

        # sudo yum install lvm2

- **SLES 12 et openSUSE**

        # sudo zypper install lvm2

- **SLES 11**

        # sudo zypper install lvm2

    Sur SLES11, vous devez également modifier les /etc/sysconfig/lvm et la valeur `LVM_ACTIVATED_ON_DISCOVERED` à « activer » :

        LVM_ACTIVATED_ON_DISCOVERED="enable" 


## <a name="configure-lvm"></a>Configurer LVM.
Dans ce guide, nous supposerons que vous avez attachée trois disques de données, que nous allons faire référence en tant que `/dev/sdc`, `/dev/sdd` et `/dev/sde`. Notez que ces stratégies peuvent toujours être les mêmes noms de chemin d’accès de votre ordinateur virtuel. Vous pouvez exécuter '`sudo fdisk -l`' ou une commande semblable à la liste de vos disques disponibles.

1. Préparer les volumes physiques :

        # sudo pvcreate /dev/sd[cde]
          Physical volume "/dev/sdc" successfully created
          Physical volume "/dev/sdd" successfully created
          Physical volume "/dev/sde" successfully created


2.  Créer un groupe de volumes. Dans cet exemple, nous appelons le volume groupe « données-vg01 » :

        # sudo vgcreate data-vg01 /dev/sd[cde]
          Volume group "data-vg01" successfully created


3. Créer les volumes logiques. La commande ci-dessous, nous crée un volume logique appelé « données-lv01 » pour couvrir le groupe de volumes entiers, mais notez qu’il est également possible de créer plusieurs volumes logiques du groupe de volumes.

        # sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
          Logical volume "data-lv01" created.


4. Formater le volume logique

        # sudo mkfs -t ext4 /dev/data-vg01/data-lv01

  >[AZURE.NOTE] Utilisation de SLES11 «-t ext3 » au lieu d’ext4. SLES11 prend uniquement en charge l’accès en lecture seule pour les systèmes de fichiers ext4.


## <a name="add-the-new-file-system-to-etcfstab"></a>Ajouter le nouveau système de fichiers dans/etc/fstab

**Attention :** Mal en modifiant le fichier/etc/fstab pourrait entraîner l’amorçage du système. Cas de doute, reportez-vous à la documentation de la distribution pour plus d’informations sur comment éditer correctement ce fichier. Il est également conseillé de créer une sauvegarde du fichier/etc/fstab avant de le modifier.

1. Créer le point de montage voulu pour votre nouveau système de fichiers, par exemple :

        # sudo mkdir /data


2. Recherchez le chemin d’accès du volume logique

        # lvdisplay
        --- Logical volume ---
        LV Path                /dev/data-vg01/data-lv01
        ....


3. Ouvrez/etc/fstab dans un éditeur de texte et ajoutez une entrée pour le nouveau système de fichiers, par exemple :

        /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2

    Puis, enregistrez et fermez/etc/fstab.


4. Vérifier que l’entrée/etc/fstab est correcte :

        # sudo mount -a

    Si cette commande génère un message d’erreur, veuillez vérifier la syntaxe dans le fichier/etc/fstab.

    Exécutez ensuite le `mount` commande pour garantir le système de fichiers est monté :

        # mount
        ......
        /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)


5. (Facultatif) Paramètres de démarrage de secours dans/etc/fstab

    De nombreuses distributions incluent soit le `nobootwait` ou `nofail` monter les paramètres qui peuvent être ajoutées au fichier/etc/fstab. Ces paramètres permettent des échecs lors du montage d’un système de fichiers particulier et permettent au système Linux continue à démarrer même si elle ne peut pas correctement monter le système de fichiers RAID. Reportez-vous à la documentation de votre distribution pour plus d’informations sur ces paramètres.

    Exemple (Ubuntu) :

        /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
