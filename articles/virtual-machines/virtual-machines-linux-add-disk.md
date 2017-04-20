<properties
    pageTitle="Ajouter un disque à Linux VM | Microsoft Azure"
    description="Apprenez à ajouter un disque permanent à votre VM Linux"
    keywords="machine virtuelle de Linux, ajoutez le disque de la ressource"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.topic="article"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="add-a-disk-to-a-linux-vm"></a>Ajouter un disque à une VM Linux

Cet article explique comment joindre un disque permanent de votre machine virtuelle afin que vous puissiez conserver vos données : même si votre ordinateur virtuel est configuré en raison de maintenance ou de redimensionnement. Pour ajouter un disque, vous devez [La CLI Azure](../xplat-cli-install.md) configuré en mode de gestionnaire de ressources (`azure config mode arm`).  

## <a name="quick-commands"></a>Commandes rapides

Dans les exemples suivants de la commande, remplacez les valeurs entre &lt; et &gt; avec les valeurs de votre propre environnement.

```bash
azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## <a name="attach-a-disk"></a>Connectez un disque

Attachement d’un nouveau disque est rapide. Type de `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` pouvez créer et joindre un nouveau disque Go pour votre machine virtuelle. Si vous n’identifiez pas explicitement d’un compte de stockage, les disques que vous créez sont placé dans le même compte de stockage où se trouve le disque de votre système d’exploitation.  Il doit ressembler à ce qui suit :

```bash
azure vm disk attach-new myuniquegroupname myuniquevmname 5
```

Sortie

```bash
info:    Executing command vm disk attach-new
+ Looking up the VM "myuniquevmname"
info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
+ Updating VM "myuniquevmname"
info:    vm disk attach-new command OK
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Se connecter à la VM Linux pour monter le nouveau disque

> [AZURE.NOTE] Cette rubrique se connecte à un ordinateur virtuel à l’aide de noms d’utilisateurs et mots de passe. Pour utiliser des paires de clés publiques et privées pour communiquer avec votre machine virtuelle, voir [comment utiliser SSH avec Linux sur Azure](virtual-machines-linux-mac-create-ssh-keys.md). Vous pouvez modifier la connexion **SSH** de machines virtuelles créées avec les `azure vm quick-create` commande à l’aide de la `azure vm reset-access` commande pour réinitialiser complètement les accès **SSH** , ajouter ou supprimer les utilisateurs ou ajouter des fichiers de clés publiques pour sécuriser l’accès.

Vous avez besoin de SSH dans votre Azure VM à la partition, format et monter votre nouveau disque afin que votre VM Linux peut l’utiliser. Si vous n’êtes pas familier avec connexion avec **ssh**, la commande prend la forme `ssh <username>@<FQDNofAzureVM> -p <the ssh port>`et présente l’aspect suivant :

```bash
ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
```

Sortie

```bash
The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myuniquevmname:~$
```

Maintenant que vous êtes connecté à votre ordinateur virtuel, vous êtes prêt à attacher un disque.  Tout d’abord rechercher le disque, à l’aide de `dmesg | grep SCSI` (la méthode que vous utilisez pour découvrir votre nouveau disque peut varier). Dans ce cas, il se présente comme :

```bash
dmesg | grep SCSI
```

Sortie

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

et dans le cas de cette rubrique, le `sdc` disque est celle que nous. Désormais partitionner le disque avec `sudo fdisk /dev/sdc` , en supposant que dans votre cas le disque a été `sdc`et un disque principal sur la partition 1 et d’accepter les valeurs par défaut.

```bash
sudo fdisk /dev/sdc
```

Sortie

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Créer la partition en tapant `p` à l’invite de commandes :

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Et écrire un système de fichiers sur la partition à l’aide de la commande **mkfs** , spécifiant le type de votre système de fichiers et le nom du périphérique. Dans cette rubrique, nous utilisons `ext4` et `/dev/sdc1` à partir du haut :

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Sortie

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Maintenant nous créer un répertoire pour monter le système de fichiers à l’aide de `mkdir`:

```bash
sudo mkdir /datadrive
```

Et vous montez le répertoire à l’aide de `mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

Le disque de données est maintenant prêt à utiliser en tant que `/datadrive`.

```bash
ls
```

Sortie

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

Pour vérifier que le lecteur est remonté automatiquement après un redémarrage, il doit être ajouté au fichier/etc/fstab. En outre, il est recommandé que l’UUID (Universally Unique IDentifier) est utilisé dans/etc/fstab pour faire référence à l’unité plutôt que le nom du périphérique (par exemple `/dev/sdc1`). Si le système d’exploitation détecte une erreur de disque lors de l’amorçage, à l’aide de l’UUID évite le disque incorrect est monté à un emplacement donné. Autres disques de données serait ensuite être affectés les mêmes ID de périphériques. Pour rechercher l’UUID du nouveau lecteur, utilisez l’utilitaire **blkid de** :

```bash
sudo -i blkid
```

La sortie ressemble à ce qui suit :

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

>[AZURE.NOTE] Mal en modifiant le **fichier/etc/fstab** pourrait entraîner l’amorçage du système. Cas de doute, reportez-vous à la documentation de la distribution pour plus d’informations sur comment éditer correctement ce fichier. Il est également conseillé de créer une sauvegarde du fichier/etc/fstab avant de le modifier.

Ensuite, ouvrez le **fichier/etc/fstab** dans un éditeur de texte :

```bash
sudo vi /etc/fstab
```

Dans cet exemple, nous utilisons la valeur UUID pour le nouveau périphérique **/dev/sdc1** qui a été créé dans les étapes précédentes et le point de montage **/datadrive**. Ajoutez la ligne suivante à la fin du **fichier/etc/fstab** :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

>[AZURE.NOTE] Suppression d’un disque de données ultérieurement sans modifier fstab pourrait l’ordinateur virtuel Échec d’amorçage. La plupart des distributions fournissent la `nofail` et/ou `nobootwait` les options fstab. Ces options permettent à un système de démarrer même si le disque ne parvient pas à monter au moment du démarrage. Pour plus d’informations sur ces paramètres, consultez la documentation de votre distribution.

>[AZURE.NOTE] L’option **nofail** permet de s’assurer que la machine virtuelle démarre même si le système de fichiers est endommagé ou que le disque n’existe pas au moment du démarrage. Sans cette option, vous pouvez rencontrer le problème comme décrit dans [Impossible SSH à la machine virtuelle de Linux en raison d’erreurs FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)


### <a name="trimunmap-support-for-linux-in-azure"></a>Prise en charge TRIM/annuler le mappage de Linux dans Azure
Certains noyaux Linux prennent en charge les opérations de découpage/DÉMAPPER pour supprimer les blocs sur le disque. Il s’agit principalement de stockage standard pour informer Azure et pages supprimées ne sont plus valide et peuvent être ignorés. Cela permet de gagner coût si vous créez des fichiers volumineux et les supprimez.

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

- N’oubliez pas que votre nouveau disque n’est pas disponible pour la machine virtuelle si elle redémarre sauf si vous écrivez ces informations dans le fichier [fstab](http://en.wikipedia.org/wiki/Fstab) .
- Pour garantir que votre VM Linux est correctement configuré, passez en revue les recommandations [d’optimiser les performances de votre ordinateur Linux](virtual-machines-linux-optimization.md) .
- Développer votre capacité de stockage en ajoutant des disques supplémentaires et la [configuration de RAID](virtual-machines-linux-configure-raid.md) pour augmenter les performances.
