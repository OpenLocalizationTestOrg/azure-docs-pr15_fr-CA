<properties
    pageTitle="Optimisation de votre machine virtuelle Linux sur Azure | Microsoft Azure"
    description="Découvrez des conseils d’optimisation pour vous assurer que vous avez configuré votre Linux VM pour des performances optimales sur Azure"
    keywords="machine virtuelle Linux, machine virtuelle linux, ubuntu virtual machine" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="optimize-your-linux-vm-on-azure"></a>Optimiser votre machine virtuelle Linux sur Azure

Création d’une machine virtuelle de Linux (VM) est facile à réaliser à partir de la ligne de commande ou à partir du portail. Ce didacticiel vous montre comment Assurez-vous que vous l’avez configuré pour optimiser ses performances sur la plate-forme Microsoft Azure. Cette rubrique utilise une machine virtuelle d’Ubuntu Server, mais vous pouvez également créer la machine virtuelle de Linux à l’aide de [vos propres images en tant que modèles](virtual-machines-linux-create-upload-generic.md).  

## <a name="prerequisites"></a>Conditions préalables

Cette rubrique suppose que vous déjà disposez d’un abonnement d’Azure ([inscription gratuite](https://azure.microsoft.com/pricing/free-trial/)), [installé l’interface CLI d’Azure](../xplat-cli-install.md) et avez déjà mis en service un ordinateur virtuel dans votre abonnement Azure. Avant de faire quoi que ce soit avec Azure – vous devez authentifier à votre abonnement. Pour ce faire, avec l’interface CLI d’Azure, il suffit de taper `azure login` pour démarrer le processus interactif. 

## <a name="azure-os-disk"></a>Azure disque du système d’exploitation

Une fois que vous créez une Linux Vm dans Azure, il a deux disques qui lui est associés. / dev/sda est votre disque OS, /dev/sdb votre disque temporaire.  N’utilisez pas le disque du système d’exploitation principal (/ dev/sda) pour quoi que ce soit, à l’exception du système d’exploitation telle qu’elle est optimisée pour les temps de démarrage VM rapides et ne fournit pas de bonnes performances pour vos charges de travail. Vous souhaitez joindre un ou plusieurs disques de votre machine virtuelle pour obtenir persistant et optimiser le stockage de vos données. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Ajout de disques pour les cibles de la taille et les performances 

En fonction de la taille de la mémoire virtuelle, vous pouvez joindre jusqu'à 16 disques supplémentaires dans une série A, 32 disques sur une imprimante de la série D, et machine de 64 disques sur une imprimante de la série G - chaque jusqu'à 1 To de taille. Vous ajoutez des disques supplémentaires selon les besoins par votre espace et les exigences d’e/s. Chaque disque dispose d’une cible de performance de 500 Ops ES/s pour le stockage Standard et 5000 Ops ES/s par disque pour le stockage de la prime.  Pour plus d’informations sur les disques de stockage de la prime, reportez-vous à [prime stockage : stockage hautes performances pour les machines virtuelles d’Azure](../storage/storage-premium-storage.md)

Pour obtenir l’IOps plus élevées sur les disques de stockage de prime où leurs paramètres de cache ont été définies à « ReadOnly » ou « None », vous devez désactiver les « barrières » lors du montage du système de fichiers sous Linux. Vous n’avez besoin d’obstacles parce que l’écriture sur les disques de stockage Premium sauvegardées est durables pour ces paramètres de cache.

- Si vous utilisez **reiserFS**, désactivez les obstacles à l’aide de l’option de montage « barrière = none » (pour activer entraves, utilisez « barrière = vidage »)
- Si vous utilisez **ext3/ext4**, désactivez les obstacles à l’aide de l’option de montage « barrière = 0 » (pour activer entraves, utilisez « barrière = 1")
- Si vous utilisez **XFS**, désactivez les obstacles à l’aide de l’option de montage « nobarrier » (pour activer entraves, utilisez l’option « barrière »)

## <a name="storage-account-considerations"></a>Considérations relatives au compte de stockage

Lorsque vous créez votre VM Linux dans Azure, veillez à que vous joignez des disques à partir de comptes de stockage résidant dans la même région que votre machine virtuelle afin de garantir de proximité et de minimiser la latence du réseau.  Chaque compte de stockage Standard possède un maximum de 20 k IOps et une capacité de taille de 500 To.  Cette procédure fonctionne à environ 40 disques très utilisés, y compris le disque du système d’exploitation et les disques de données que vous créez. Pour les comptes de stockage de la prime, il n’y a aucune limite maximale Ops ES/s, mais il existe une limite de taille de 32 To. 

Lors de la gestion des e/s des charges de travail haute et vous avez choisi Standard de stockage de vos disques, vous devrez fractionner les disques entre plusieurs comptes de stockage afin de vous assurer que vous n’avez pas atteint la limite de IOps de 20 000 pour les comptes de stockage Standard. Votre ordinateur virtuel peut contenir un mélange de disques de sur les comptes de stockage différentes et des types de compte de stockage pour atteindre votre configuration optimale. 

## <a name="your-vm-temporary-drive"></a>Votre lecteur VM temporaire

Par défaut lorsque vous créez un ordinateur virtuel, Azure vous apporte un disque du système d’exploitation (/ dev/sda) et un disque temporaire (/ dev/sdb).  Tous les disques supplémentaires que vous ajoutez apparaissent en tant que /dev/sdc, /dev/sdd, /dev/sde et ainsi de suite. Toutes les données de votre disque temporaire (/ dev/sdb) ne sont pas durables et peuvent être perdues si des événements spécifiques, comme la machine virtuelle redimensionnement, redéploiement, ou maintenance force un redémarrage de votre ordinateur virtuel.  La taille et le type de votre disque temporaire est liée à la taille de mémoire virtuelle que vous avez choisi au moment du déploiement. Dans le cas de la taille de la prime VMs (série DS, G et DS_V2) du répertoire temporaire est sauvegardé par un SSD local pour augmenter les performances de jusqu'à 48 k IOps. 

## <a name="linux-swap-file"></a>Fichier d’échange de Linux

Si votre Azure VM est une image Ubuntu ou CoreOS, vous pouvez utiliser CustomData pour envoyer un nuage-config de nuage-init. Si vous avez [téléchargé une image personnalisée de Linux](virtual-machines-linux-upload-vhd.md) qui utilise le nuage-init, vous également configurez des partitions de remplacement à l’aide de nuage-init.

Sur les Images de nuage Ubuntu, vous devez utiliser init-nuage pour configurer la partition de swap. Consultez la page suivante sur le wiki Ubuntu pour plus de détails : [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Pour les images sans prise en charge du nuage-init, images d’ordinateur virtuel déployés à partir du marché Azure ont un Agent de Linux VM intégré avec le système d’exploitation. Cet agent permet à l’ordinateur virtuel interagir avec les différents services Azure. En supposant que vous avez déployé une image standard depuis le site Marketplace d’Azure, vous devez suivre la procédure suivante pour configurer correctement les paramètres de votre fichier swap Linux :

Localisez et modifiez les deux entrées dans le fichier **/etc/waagent.conf** . Ils contrôlent l’existence d’un fichier d’échange dédié et de la taille du fichier d’échange. Les paramètres que vous souhaitez pour modifier sont `ResourceDisk.EnableSwap=N` et`ResourceDisk.SwapSizeMB=0` 

Vous devez les modifier pour les éléments suivants :

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size en Mo pour satisfaire vos besoins} 

Une fois que vous avez apporté la modification, vous devez redémarrez le waagent ou votre VM Linux pour refléter ces modifications.  Vous savez que les modifications ont été implémentées et un fichier d’échange a été créé lorsque vous utilisez la `free` commande pour afficher l’espace libre. L’exemple suivant a un fichier d’échange de 512 Mo créé lors de la modification du fichier waagent.conf.

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algorithme de planification d’e/s pour le stockage de la prime

Avec le 2.6.18 noyau Linux, e/s de l’algorithme de planification a été modifiée à partir de la date d’échéance à CFQ (algorithme de file d’attente complètement juste) par défaut. Pour les modèles d’e/s de l’accès aléatoire, il est négligeable dans les différences de performances entre les CFQ et date d’échéance.  Pour les disques SSD où le modèle d’e/s disque est essentiellement séquentiel, commutation-retour vers l’algorithme de commande NOOP ou date d’échéance peut obtenir de meilleures performances d’e/s.

### <a name="view-the-current-io-scheduler"></a>Afficher le planificateur actuel d’e/s

Utilisez la commande suivante :  

    admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Vous verrez à la suite de sortie, ce qui indique au planificateur actuel.  

    noop [deadline] cfq

###<a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Changer le périphérique actuel (/ dev/sda) d’e/s de l’algorithme de planification

Utilisez les commandes suivantes :  

    azureuser@mylinuxvm:~$ sudo su -
    root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mylinuxvm:~# update-grub

>[AZURE.NOTE] Ce paramétrage pour/dev/sda seul n’est pas utile. Il doit être défini sur tous les disques de données où les e/s séquentielles domine le modèle d’e/s.  

Vous devez voir la sortie suivante, qui indique que grub.cfg a été reconstruite avec succès et que le planificateur par défaut a été mis à jour NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Pour la famille de distribution Redhat, vous devez uniquement la commande suivante :   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="using-software-raid-to-achieve-higher-iops"></a>À l’aide du logiciel RAID atteindre plus I / Ops

Si vos charges de travail nécessitent plus d’IOps que celles d’un seul disque, vous devez utiliser une configuration de RAID logiciel de plusieurs disques. Car Azure effectue déjà la résilience de disque au niveau du fabric local, vous atteignez le plus haut niveau de performances d’une configuration de l’entrelacement RAID-0.  Vous devez configurer et créer des disques dans l’environnement Azure et les joindre à votre VM Linux avant le partitionnement, le formatage et les lecteurs de montage.  Vous trouverez plus de détails sur la configuration d’un programme d’installation de logiciel RAID sur votre VM Linux dans azure dans le document de **[Configuration de RAID logiciel sous Linux](virtual-machines-linux-configure-raid.md)** .


## <a name="next-steps"></a>Étapes suivantes

N’oubliez pas que, comme avec toutes les discussions d’optimisation, vous devez effectuer des tests avant et après chaque modification pour mesurer l’impact de la modification.  L’optimisation est un processus étape par étape qui aura des résultats différents sur des ordinateurs différents dans votre environnement.  Ce qui fonctionne pour une configuration peut ne pas fonctionne pour les autres.

Quelques liens utiles vers des ressources supplémentaires : 

- [: Premium hautes performances de stockage pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage.md)
- [Guide de l’utilisateur Azure Agent de Linux](virtual-machines-linux-agent-user-guide.md)
- [Optimisation des performances de MySQL sur Linux Azure VM](virtual-machines-linux-classic-optimize-mysql.md)
- [Configurez le logiciel RAID sous Linux](virtual-machines-linux-configure-raid.md)
