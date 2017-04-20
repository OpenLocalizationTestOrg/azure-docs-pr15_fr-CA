<properties
    pageTitle="Réinitialiser l’accès sur Azure VM de Linux à l’aide de le VMAccess Extension | Microsoft Azure"
    description="Réinitialiser l’accès sur Azure VM de Linux à l’aide de le VMAccess Extension."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension"></a>Gérer les utilisateurs, SSH et vérifier ou de réparer des disques sur Azure VM de Linux à l’aide de le VMAccess Extension

Cet article vous montre comment utiliser l’Extension de VMAcesss de Azure pour vérifier ou réparer un disque, réinitialiser l’accès des utilisateurs, gérer les comptes d’utilisateur ou réinitialisez la configuration SSHD sous Linux. L’article nécessite :

- un compte Azure ([Obtenez un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).

- la [CLI d’Azure](../xplat-cli-install.md) connecté avec `azure login`.

- le mode de gestionnaire de ressources Azure Azure CLI _doit se trouver dans_ `azure config mode arm`.

## <a name="quick-commands"></a>Commandes rapides

Il existe deux façons d’utiliser VMAccess sur vos ordinateurs virtuels de Linux :

- À l’aide de la CLI d’Azure et les paramètres requis.
- À l’aide de fichiers raw pour JSON VMAccess traite, puis d’agir sur.

Pour la section de commande rapide, nous allons utiliser la CLI Azure `azure vm reset-access` méthode. Dans les exemples suivants de la commande, remplacez les valeurs qui contiennent « exemple » avec les valeurs de votre propre environnement.

## <a name="create-a-resource-group-and-linux-vm"></a>Créer un groupe de ressources et de la machine virtuelle de Linux

```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Créer un ordinateur virtuel Debian

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-g myResourceGroup \
-l westus \
-y Linux \
-n myVM \
-Q Debian
```

## <a name="reset-root-password"></a>Réinitialiser le mot de passe racine

Pour réinitialiser le mot de passe racine :

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u root \
-p myNewPassword
```

## <a name="ssh-key-reset"></a>Réinitialisation de clé SSH

Pour réinitialiser le code SSH d’un utilisateur non racine :

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Créer un utilisateur

Pour créer un utilisateur :

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-p myAdminUserPassword
```

## <a name="remove-a-user"></a>Supprimer un utilisateur

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-R myRemovedUser
```

## <a name="reset-sshd"></a>Réinitialisation SSHD

Pour réinitialiser la configuration SSHD :

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM
-r
```


## <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé

### <a name="vmaccess-defined"></a>VMAccess défini par :

Le disque de votre VM Linux est affichant les erreurs. Vous d’une certaine manière réinitialisez le mot de passe racine pour votre VM Linux ou supprimé par inadvertance votre clé privée SSH. Dans ce cas à l’époque du centre de données, vous devrez lecteur il et puis ouvrez le KVM d’accéder à la console du serveur. Considérez l’extension Azure VMAccess comme ce commutateur KVM vous permettant d’accéder à la console pour réinitialiser l’accès vers Linux ou effectuer la maintenance au niveau du disque.

Pour la procédure pas à pas détaillées, nous allons utiliser la forme longue de VMAccess, qui utilise les fichiers raw de JSON.  Ces fichiers VMAccess JSON peuvent également être appelées à partir de modèles Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Pour vérifier ou réparer le disque d’une VM Linux à l’aide de VMAccess

À l’aide de VMAccess, vous pouvez effectuer une commande fsck exécutée sur le disque sous votre VM Linux.  Vous pouvez également effectuer une vérification du disque et une réparation du disque à l’aide d’un VMAccess.

Pour vérifier et réparer le disque Utilisez ce script VMAccess :

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Pour réinitialiser l’accès des utilisateurs à Linux à l’aide de VMAccess

Si vous avez perdu l’accès à la racine sur votre VM Linux, vous pouvez lancer un script VMAccess pour réinitialiser le mot de passe racine.

Pour réinitialiser le mot de passe racine, utilisez ce script VMAccess :

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword",   
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

Pour réinitialiser le code SSH d’un utilisateur non racine, utilisez ce script VMAccess :

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM",   
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>À l’aide de VMAccess pour gérer les comptes utilisateur sous Linux

VMAccess est un script Python qui peut être utilisé pour gérer les utilisateurs sur votre VM Linux sans connexion et à l’aide de sudo ou le compte de la racine.

Pour créer un utilisateur, utilisez ce script VMAccess :

`create_new_user.json`

```json
{
"username":"myNewUser",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
"password":"myNewUserPassword",
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Pour supprimer un utilisateur, utilisez ce script VMAccess :

`remove_user.json`

```json
{
"remove_user":"myDeletedUser",
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>À l’aide de VMAccess pour réinitialiser la configuration SSHD

Si vous apportez des modifications à la configuration SSHD d’ordinateurs virtuels Linux et que vous fermez la connexion SSH avant de vérifier les modifications, vous ne puissent pas SSH'ing dans.  VMAccess peut être utilisé pour rétablir la configuration SSHD une bonne configuration connue sans être connecté via SSH.

Pour réinitialiser la configuration SSHD Utilisez ce script VMAccess :

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Étapes suivantes

Mise à jour de Linux, à l’aide d’Extensions de VMAccess Azure est une méthode pour apporter des modifications sur une VM Linux en cours d’exécution.  Vous pouvez également utiliser des outils tels qu’init-nuage et modèles d’Azure pour modifier votre VM Linux au démarrage.

[À propos des fonctionnalités et extensions de machine virtuelle](virtual-machines-linux-extensions-features.md)

[Création de modèles de gestionnaire de ressources Azure avec extensions de Linux VM](virtual-machines-linux-extensions-authoring-templates.md)

[Pour personnaliser une VM Linux lors de la création à l’aide de nuage-init.](virtual-machines-linux-using-cloud-init.md)
