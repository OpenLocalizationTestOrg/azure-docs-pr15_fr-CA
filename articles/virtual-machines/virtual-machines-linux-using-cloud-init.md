<properties
    pageTitle="Pour personnaliser une VM Linux lors de la création à l’aide d’init-nuage | Microsoft Azure"
    description="À l’aide de nuage-init pour personnaliser une VM Linux lors de la création."
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
    ms.date="10/26/2016"
    ms.author="v-livech"
/>

# <a name="using-cloud-init-to-customize-a-linux-vm-during-creation"></a>Pour personnaliser une VM Linux lors de la création à l’aide de nuage-init.

Cet article explique comment créer un script init-nuage pour définir le nom d’hôte, les packages de mise à jour installée et de gérer des comptes d’utilisateurs.  Les scripts de nuage-init sont appelées lors de la création de la machine virtuelle à partir de la CLI d’Azure.  L’article nécessite :

- un compte Azure ([Obtenez un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).

- la [CLI d’Azure](../xplat-cli-install.md) connecté avec `azure login`.

- le mode de gestionnaire de ressources Azure Azure CLI _doit se trouver dans_ `azure config mode arm`.

## <a name="quick-commands"></a>Commandes rapides

Créer un script de nuage-init.txt qui définit le nom d’hôte, tous les packages de mises à jour et ajoute un utilisateur sudo Linux.

```bash
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Créer un groupe de ressources pour démarrer les ordinateurs virtuels dans.

```bash
azure group create myResourceGroup westus
```

Créer une VM Linux à l’aide de nuage-init pour le configurer au cours du démarrage.

```bash
azure vm create \
-g myResourceGroup \
-n myVM \
-l westus \
-y Linux \
-f myVMnic \
-F myVNet \
-P 10.0.0.0/22 \
-j mySubnet \
-k 10.0.0.0/24 \
-Q canonical:ubuntuserver:14.04.2-LTS:latest \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé

### <a name="introduction"></a>Introduction

Lorsque vous lancez une VM Linux nouvelle, vous obtenez une norme Linux VM à nothing personnalisée ou prête à vos besoins. [Nuage-init](https://cloudinit.readthedocs.org) est un moyen standard d’injecter des paramètres de script ou de configuration dans cette VM Linux comme il démarre pour la première fois.

Sur Azure, il existe un trois façons différentes pour apporter des modifications à une VM Linux qu’il est déployé ou démarré.

- Injecter des scripts à l’aide de nuage-init.
- Injecter des scripts à l’aide de [l’Extension de VMAccess](virtual-machines-linux-using-vmaccess-extension.md)d' Azure.
- Un modèle d’Azure à l’aide de nuage-init.
- Un modèle d’Azure à l’aide de [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Pour injecter des scripts à tout moment après l’amorçage :

- Pour exécuter directement des commandes SSH
- Injecter des scripts à l’aide de [l’Extension de VMAccess](virtual-machines-linux-using-vmaccess-extension.md)d' Azure, impérative ou dans un modèle d’Azure
- Outils de gestion de configuration telles que Ansible, sel, Chef et Marionnette.

>[AZURE.NOTE]: VMAccess Extension executes a script as root in the same way using SSH can.  However, using the VM extension enables several features that Azure offers that can be useful depending upon your scenario.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilité de nuage-init sur Azure VM création rapide les alias de l’image :

| Alias     | Publisher | Offre        | POINT DE STOCK         | Version | nuage-init. |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS    | OpenLogic | CentOS       | 7.2         | dernière version  | aucune         |
| CoreOS    | CoreOS    | CoreOS       | Stable      | dernière version  | Oui        |
| Debian    | credativ  | Debian       | 8           | dernière version  | aucune         |
| openSUSE  | SUSE      | openSUSE     | 13.2        | dernière version  | aucune         |
| RHEL      | Redhat    | RHEL         | 7.2         | dernière version  | aucune         |
| UbuntuLTS | Canonique | UbuntuServer | 14.04.4-LTS | dernière version  | Oui        |

Microsoft collabore avec ses partenaires pour obtenir du nuage-init inclus et de travailler sur les images qu’ils fournissent sur Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Ajout d’un script init-nuage pour la création de la machine virtuelle avec la CLI d’Azure

Pour lancer un script init-nuage lors de la création d’une machine virtuelle dans Azure, spécifiez le fichier init-nuage à l’aide de la CLI Azure `--custom-data` basculer.

Créer un groupe de ressources pour démarrer les ordinateurs virtuels dans.

```bash
azure group create myResourceGroup westus
```

Créer une VM Linux à l’aide de nuage-init pour le configurer au cours du démarrage.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Création d’un script init-nuage pour définir le nom d’hôte d’une VM Linux

Un des paramètres pour les VM Linux plus simple et le plus importants est le nom d’hôte. Nous pouvons facilement définir avec init-nuage de ce script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Exemple de script init-nuage nommé `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: myservername
```

Lors du démarrage initial de la machine virtuelle, ce script init-nuage définit le nom d’hôte `myservername`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_hostname.txt
```

Connexion et vérifiez que le nom d’hôte de la nouvelle machine virtuelle.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Création d’un script init-nuage pour mettre à jour de Linux

Pour la sécurité, vous souhaitez que votre Ubuntu VM en vue de mettre à jour le premier démarrage.  À l’aide de nuage-init, que nous pouvons le faire avec le script suivant, en fonction de la distribution Linux que vous utilisez.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Exemple de script init-nuage `cloud_config_apt_upgrade.txt` pour la famille Debian

```bash
#cloud-config
apt_upgrade: true
```

Une fois que Linux a démarré, tous les packages installés sont mis à jour `apt-get`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_apt_upgrade.txt
```

Connexion et vérifiez que tous les packages sont mis à jour.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Création d’un script init-nuage pour ajouter un utilisateur pour Linux

Une des premières tâches sur n’importe quel VM Linux nouvelle est d’ajouter un utilisateur pour vous-même ou pour éviter à l’aide de `root`. Clés SSH sont meilleures pratiques pour la sécurité et de facilité d’utilisation et ils sont ajoutés à la `~/.ssh/authorized_keys` fichier avec ce script init-nuage.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Exemple de script init-nuage `cloud_config_add_users.txt` de famille Debian

```bash
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Une fois que Linux a démarré, tous les utilisateurs répertoriés sont créés et ajoutés au groupe sudo.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_add_users.txt
```

Connexion et vérifiez que l’utilisateur nouvellement créé.

```bash
ssh myVM
cat /etc/group
```

Sortie

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Étapes suivantes

Nuage-init est devenu un moyen standard pour modifier votre VM Linux au démarrage. Azure dispose également d’extensions de machine virtuelle, qui vous permettent de modifier votre LinuxVM au démarrage ou pendant son exécution. Par exemple, vous pouvez utiliser la VMAccessExtension Azure pour restaurer les informations de SSH ou de l’utilisateur pendant l’exécution de la machine virtuelle. Avec init-nuage, vous devez redémarrer pour réinitialiser le mot de passe.

[À propos des fonctionnalités et extensions de machine virtuelle](virtual-machines-linux-extensions-features.md)

[Gérer les utilisateurs, SSH et vérifier ou de réparer des disques sur Azure VM de Linux à l’aide de le VMAccess Extension](virtual-machines-linux-using-vmaccess-extension.md)
