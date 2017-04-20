<properties
    pageTitle="Différentes façons de créer une VM Linux | Microsoft Azure"
    description="Découvrez les différentes façons de créer une machine virtuelle Linux sur Azure, notamment des liens vers des outils et des didacticiels pour chaque méthode."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Différentes façons de créer une machine virtuelle Linux dans Azure

Vous avez la possibilité dans Azure pour créer une machine de virtuelle (VM) Linux à l’aide des outils et des flux de travail à l’aise pour vous. Cet article résume ces différences et les exemples pour créer vos ordinateurs virtuels de Linux.


## <a name="azure-cli"></a>CLI Azure 

La CLI Azure est disponible sur plusieurs plates-formes via un package npm, packages fournis par distro ou conteneur de Docker. Vous pouvez en savoir plus sur la [façon d’installer et de configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md). Les didacticiels suivants fournissent des exemples sur l’utilisation de la CLI d’Azure. Lisez chaque article pour plus de détails sur les commandes de démarrage rapide CLI affichées :

- [Créer une VM Linux à partir de la CLI Azure de développement et de test](virtual-machines-linux-quick-create-cli.md)
    - L’exemple suivant crée une VM CoreOS à l’aide d’une clé publique nommée `azure_id_rsa.pub`:

    ```bash
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
        --image-urn CoreOS
    ```

- [Créer une VM Linux sécurisés à l’aide d’un modèle d’Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
    - L’exemple suivant crée un ordinateur virtuel à l’aide d’un modèle stocké sur GitHub :

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

- [Créer un environnement Linux complet à l’aide de la CLI d’Azure](virtual-machines-linux-create-cli-complete.md)
    - Inclut la création d’un équilibreur de charge et de plusieurs ordinateurs virtuels dans un jeu de disponibilité.

- [Ajouter un disque à une VM Linux](virtual-machines-linux-add-disk.md)
    - L’exemple suivant ajoute un disque de 5 Go à une VM existante nommée `TestVM`:

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
        --size-in-GB 5
    ```

## <a name="azure-portal"></a>Azure portal

Le [portail Azure](https://portal.azure.com) vous permet de créer rapidement un ordinateur virtuel dans la mesure où il n’y a rien à installer sur votre système. Le portail Azure permet de créer la machine virtuelle :

- [Créer une VM Linux via le portail Azure](virtual-machines-linux-quick-create-portal.md) 
- [Connectez un disque à l’aide du portail Azure](virtual-machines-linux-attach-disk-portal.md)


## <a name="operating-system-and-image-choices"></a>Système d’exploitation et des choix d’images
Lorsque vous créez un ordinateur virtuel, vous choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Azure et ses partenaires proposent de nombreuses images, notamment les applications et les outils préinstallés. Ou encore, télécharger une de vos propres images (voir [la section suivante](#use-your-own-image)).

### <a name="azure-images"></a>Images Azure
Utiliser les `azure vm image` les commandes CLI pour voir ce qui est disponible par publisher, distro release et des versions.

La liste des éditeurs disponibles comme suit :

```bash
azure vm image list-publishers --location WestUS
```

La liste des produits disponibles (offres) d’un éditeur donné comme suit :

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Liste des références SKU disponibles (versions distro) d’une offre donnée comme suit :

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

La liste de toutes les images disponibles pour une version donnée suit :

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Pour plus d’exemples sur l’exploration et l’utilisation d’images disponibles, consultez [Naviguer et sélectionnez machine virtuelle Azure des images avec la CLI d’Azure](virtual-machines-linux-cli-ps-findimage.md).

La `azure vm quick-create` et `azure vm create` commandes ont des alias, vous pouvez utiliser pour accéder rapidement à la distros plus courantes et leurs versions les plus récentes. Il est souvent plus rapide que la spécification de l’éditeur, offre, SKU et version chaque fois que vous créez un ordinateur virtuel de l’utilisation d’alias :

| Alias     | Publisher | Offre        | POINT DE STOCK         | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | dernière version  |
| CoreOS    | CoreOS    | CoreOS       | Stable      | dernière version  |
| Debian    | credativ  | Debian       | 8           | dernière version  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | dernière version  |
| RHEL      | Redhat    | RHEL         | 7.2         | dernière version  |
| SLES      | SLES      | SLES         | 12-SP1      | dernière version  |
| UbuntuLTS | Canonique | UbuntuServer | 14.04.4-LTS | dernière version  |

### <a name="use-your-own-image"></a>Utiliser votre propre image

Si vous avez besoin des personnalisations spécifiques, vous pouvez utiliser une image selon un existant Azure VM en *capturant* cet ordinateur virtuel. Vous pouvez également télécharger une image créée sur site. Pour plus d’informations sur la prise en charge de distros et comment utiliser vos propres images, consultez les articles suivants :

- [Azure visé distributions](virtual-machines-linux-endorsed-distros.md)

- [Pour les distributions de non approuvée](virtual-machines-linux-create-upload-generic.md)

- [La capture d’une machine virtuelle de Linux en tant que gestionnaire de ressources du modèle](virtual-machines-linux-capture-image.md).
    - Démarrage rapide exemples de commandes pour capturer un ordinateur virtuel existant :

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## <a name="next-steps"></a>Étapes suivantes

- Créer une VM Linux à partir du [portail](virtual-machines-linux-quick-create-portal.md), avec l' [interface CLI](virtual-machines-linux-quick-create-cli.md), ou à l’aide d’un [modèle de gestionnaire de ressources Azure](virtual-machines-linux-cli-deploy-templates.md).

- Après avoir créé une VM Linux, [Ajouter un disque de données](virtual-machines-linux-add-disk.md).

- Étapes à suivre pour [Réinitialiser un mot de passe ou des clés SSH et de gérer des utilisateurs](virtual-machines-linux-using-vmaccess-extension.md)
