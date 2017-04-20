<properties
    pageTitle="Connectez un disque à une VM Linux | Microsoft Azure"
    description="Découvrez comment attacher un disque de données à une machine virtuelle Azure exécutant Linux et de l’initialiser afin qu’il soit prêt à l’emploi."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="iainfou"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Comment attacher un disque de données pour une Machine virtuelle Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Voir comment [attacher un disque de données en utilisant le modèle de déploiement du Gestionnaire de ressources](virtual-machines-linux-add-disk.md).

Vous pouvez associer les disques vides et les disques qui contiennent des données dans vos ordinateurs virtuels d’Azure. Les deux types de disques sont des fichiers .vhd qui résident dans un compte de stockage Azure. Comme avec l’ajout d’un disque sur une machine Linux, après avoir attaché le disque vous devez initialiser et à mettre en forme afin qu’il soit prêt à l’emploi. Cet article explique l’attachement de disques vides et disques contenant déjà des données de vos ordinateurs virtuels, ainsi que comment initialiser, puis formater un nouveau disque.

> [AZURE.NOTE]Il est recommandé d’utiliser un ou plusieurs disques distincts pour stocker les données d’un ordinateur virtuel. Lorsque vous créez une machine virtuelle Azure, il possède un disque du système d’exploitation et un disque temporaire. **N’utilisez pas le disque temporaire pour stocker les données persistantes.** Comme son nom l’indique, il fournit un stockage temporaire uniquement. Il ne propose aucune redondance ou la sauvegarde, car il ne réside pas dans le stockage Azure.
> Le disque temporaire est généralement géré par l’Agent de Linux Azure et automatiquement monté sur **/mnt/resource** (ou **/mnt/mnt** sur Ubuntu images). D’autre part, un disque de données peut être nommé par le noyau Linux quelque chose comme `/dev/sdc`, et vous devez partitionner, formater et monter cette ressource. Consultez le [Guide de l’utilisateur l’Agent Azure Linux] [ Agent] pour plus de détails.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Initialiser un nouveau disque de données sous Linux

1. SSH à votre machine virtuelle. Pour plus d’informations, voir [Comment faire pour ouvrir une session sur un ordinateur virtuel exécutant Linux][Logon].

2. Ensuite, vous devez trouver l’identificateur de périphérique pour le disque de données d’initialisation. Il existe deux façons de procéder :

    un) Grep pour les périphériques SCSI dans les journaux, par exemple la commande suivante :

            $sudo grep SCSI /var/log/messages

    Pour les distributions Ubuntu récentes, vous devrez peut-être utiliser `sudo grep SCSI /var/log/syslog` car l’enregistrement à `/var/log/messages` peut être désactivé par défaut.

    Vous pouvez trouver l’identificateur de la dernière disquette de données qui a été ajoutée dans les messages qui sont affichent.

    ![Obtenir les messages de disque](./media/virtual-machines-linux-classic-attach-disk/scsidisklog.png)

    OU

    b) utilisation le `lsscsi` commande pour connaître l’id de périphérique. `lsscsi`peut être installé par une ou l’autre `yum install lsscsi` (base de distributions sur Red Hat) ou `apt-get install lsscsi` (base de distributions de Debian). Vous pouvez trouver le disque que vous cherchez par son **numéro d’unité logique**ou un _lun_ . Par exemple, les _lun_ pour les disques est facilement visible à partir de `azure vm disk list <virtual-machine>` en tant que :

            ~$ azure vm disk list TestVM
            info:    Executing command vm disk list
            + Fetching disk images
            + Getting virtual machines
            + Getting VM disks
            data:    Lun  Size(GB)  Blob-Name                         OS
            data:    ---  --------  --------------------------------  -----
            data:         30        TestVM-2645b8030676c8f8.vhd  Linux
            data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
            info:    vm disk list command OK

    Comparer ces données avec la sortie de `lsscsi` pour le même exemple de machine virtuelle :

            ops@TestVM:~$ lsscsi
            [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
            [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
            [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
            [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc

    Le dernier numéro du tuple dans chaque ligne est la _lun_. Voir `man lsscsi` pour plus d’informations.

3. À l’invite, tapez la commande suivante pour créer votre périphérique :

        $sudo fdisk /dev/sdc


4. Lorsque vous y êtes invité, tapez **n** pour créer une nouvelle partition.


    ![Créer le périphérique](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)

5. Lorsque vous y êtes invité, entrez **p** pour rendre la partition la partition principale. Tapez **1** pour faciliter la première partition et puis appuyez sur ENTRÉE pour accepter la valeur par défaut de la bouteille. Sur certains systèmes, il peut afficher les valeurs par défaut de la première et les dernières secteurs, au lieu de la bouteille. Vous pouvez choisir d’accepter ces paramètres par défaut.


    ![Créer la partition](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartdetails.png)



6. Tapez **p** pour afficher les détails sur le disque est partitionné en cours.


    ![Liste des informations de disque](./media/virtual-machines-linux-classic-attach-disk/fdiskpartitiondetails.png)



7. Tapez **w** pour écrire les paramètres pour le disque.


    ![Écrire les modifications du disque](./media/virtual-machines-linux-classic-attach-disk/fdiskwritedisk.png)

8. Vous pouvez désormais créer le système de fichiers sur la nouvelle partition. Ajouter le numéro de partition pour l’ID de périphérique (dans l’exemple suivant `/dev/sdc1`). L’exemple suivant crée une partition ext4 sur /dev/sdc1 :

        # sudo mkfs -t ext4 /dev/sdc1

    ![Créer le système de fichiers](./media/virtual-machines-linux-classic-attach-disk/mkfsext4.png)

    >[AZURE.NOTE] Les systèmes SuSE Linux Enterprise 11 prennent uniquement en charge l’accès en lecture seule pour les systèmes de fichiers ext4. Pour ces systèmes, il est recommandé au format du système de fichiers nouvelle ext3 plutôt qu’ext4.


9. Créez un répertoire pour monter le nouveau système de fichiers, comme suit :

        # sudo mkdir /datadrive


10. Enfin, vous pouvez monter le lecteur, comme suit :

        # sudo mount /dev/sdc1 /datadrive

    Le disque de données est maintenant prêt à être utilisé en tant que **/datadrive**.
    
    ![Créer le répertoire et le montage du disque](./media/virtual-machines-linux-classic-attach-disk/mkdirandmount.png)


11. Ajoutez le nouveau lecteur à/etc/fstab :

    Pour vérifier que le lecteur est remonté automatiquement après un redémarrage, il doit être ajouté au fichier/etc/fstab. En outre, il est recommandé que l’UUID (Universally Unique IDentifier) est utilisé dans/etc/fstab pour faire référence à l’unité plutôt que de simplement le nom du périphérique (c'est-à-dire /dev/sdc1). À l’aide de l’UUID évite le disque incorrect qui est monté sur un emplacement donné si le système d’exploitation détecte une erreur de disque lors du démarrage et les disques de données restants, puis assignées ces ID de périphérique. Pour trouver l’UUID du nouveau lecteur, vous pouvez utiliser l’utilitaire **blkid de** :

        # sudo -i blkid

    La sortie ressemble à ce qui suit :

        /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
        /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
        /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


    >[AZURE.NOTE] Mal en modifiant le **fichier/etc/fstab** pourrait entraîner l’amorçage du système. Cas de doute, reportez-vous à la documentation de la distribution pour plus d’informations sur comment éditer correctement ce fichier. Il est également conseillé de créer une sauvegarde du fichier/etc/fstab avant de le modifier.

    Ensuite, ouvrez le **fichier/etc/fstab** dans un éditeur de texte :

        # sudo vi /etc/fstab

    Dans cet exemple, nous utilisons la valeur UUID pour le nouveau périphérique **/dev/sdc1** qui a été créé dans les étapes précédentes et le point de montage **/datadrive**. Ajoutez la ligne suivante à la fin du **fichier/etc/fstab** :

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2

    Ou, sur les systèmes SuSE Linux, vous devrez utiliser un format légèrement différent :

        /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    
    >[AZURE.NOTE] Le `nofail` option permet de s’assurer que la machine virtuelle démarre même si le système de fichiers est endommagé ou que le disque n’existe pas au moment du démarrage. Sans cette option, vous pouvez rencontrer le problème comme décrit dans [Impossible SSH à la machine virtuelle de Linux en raison d’erreurs FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Vous pouvez à présent tester que le système de fichiers est monté correctement par le démontage et de réinstaller le système de fichiers, c'est-à-dire point de montage à l’aide de l’exemple `/datadrive` créé dans les étapes précédentes :

        # sudo umount /datadrive
        # sudo mount /datadrive

    Si le `mount` commande génère une erreur, vérifiez le fichier/etc/fstab pour la syntaxe correcte. Si des partitions ou des lecteurs de données sont créées, les entrer dans/etc/fstab ainsi séparément.

    Déverrouiller le lecteur à l’aide de cette commande :

        # sudo chmod go+w /datadrive

>[AZURE.NOTE] Suppression d’un disque de données par la suite sans modification fstab pourrait l’ordinateur virtuel Échec d’amorçage. S’il s’agit d’une occurrence courante, la plupart des distributions fournissent la `nofail` et/ou `nobootwait` options fstab qui permettent à un système de démarrer même si le disque ne parvient pas à monter au démarrage. Pour plus d’informations sur ces paramètres, consultez la documentation de votre distribution.

### <a name="trimunmap-support-for-linux-in-azure"></a>Prise en charge TRIM/annuler le mappage de Linux dans Azure
Certains noyaux Linux prennent en charge les opérations de découpage/DÉMAPPER pour supprimer les blocs sur le disque. Ces opérations sont principalement dans le stockage standard pour informer Azure et pages supprimées ne sont plus valide et peuvent être ignorés. En supprimant les pages peut enregistrer le coût si vous créez des fichiers volumineux et les supprimez.

Il existe deux façons d’activer l’assiette prise en charge dans votre VM Linux. Comme d’habitude, consultez votre distribution de l’approche recommandée :

- Utilisez le `discard` option de montage `/etc/fstab`, par exemple :

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- Vous pouvez également exécuter la `fstrim` de commande manuellement à partir de la ligne de commande, ou l’ajouter à votre crontab à exécuter régulièrement :

    **Ubuntu**

        # sudo apt-get install util-linux
        # sudo fstrim /datadrive

    **RHEL/CentOS**

        # sudo yum install util-linux
        # sudo fstrim /datadrive

## <a name="troubleshooting"></a>Résolution des problèmes
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]


## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en savoir plus sur l’utilisation de votre VM Linux dans les articles suivants :

- [Comment faire pour ouvrir une session sur un ordinateur virtuel exécutant Linux][Logon]

- [Comment faire pour détacher un disque à partir d’une machine virtuelle Linux](virtual-machines-linux-classic-detach-disk.md)

- [À l’aide de la CLI de Azure avec le modèle de déploiement standard](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-mac-create-ssh-keys.md
