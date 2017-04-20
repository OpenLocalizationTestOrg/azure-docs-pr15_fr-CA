<properties
    pageTitle="Créer et télécharger une image personnalisée de Linux | Microsoft Azure"
    description="Créer et télécharger un disque dur virtuel (VHD) pour Azure avec une image de Linux personnalisée en utilisant le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

# <a name="upload-and-create-a-linux-vm-from-custom-disk-image"></a>Télécharger et de créer une VM Linux à partir de l’image de disque personnalisé

Cet article vous montre comment télécharger un disque dur virtuel (VHD) pour Azure en utilisant le modèle de déploiement du Gestionnaire de ressources et de créer des ordinateurs virtuels de Linux à partir de cette image personnalisée. Cette fonctionnalité vous permet d’installer et configurer un distro de Linux à vos besoins et ensuite utiliser ce disque dur virtuel pour créer rapidement des machines virtuelles Azure (VM).

## <a name="quick-commands"></a>Commandes rapides
Si vous avez besoin effectuer rapidement les tâches, les détails suivants de la section de la base de commandes pour télécharger une machine virtuelle pour Azure. Informations plus détaillées et contexte pour chaque étape vous pouvez trouver le reste du document, en [commençant ici](#requirements).

Assurez-vous que vous disposez de [L’infrastructure du langage commun Azure](../xplat-cli-install.md) connecté et en utilisant le mode du Gestionnaire de ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacer les noms de paramètre d’exemple par vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `mystorageaccount`, et `myimages`.

Tout d’abord, créez un groupe de ressources. L’exemple suivant crée un groupe de ressources appelé `myResourceGroup` dans les `WestUs` emplacement :

```bash
azure group create myResourceGroup --location "WestUS"
```

Créer un compte de stockage pour stocker vos disques virtuels. L’exemple suivant crée un compte de stockage nommé `mystorageaccount`:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Liste des touches d’accès rapide de votre compte de stockage. Prenez note des `key1`:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Créer un conteneur dans votre compte de stockage à l’aide de la clé de stockage que vous avez obtenu. L’exemple suivant crée un conteneur nommé `myimages` à l’aide de la valeur de clé de stockage à partir de `key1`:

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Enfin, télécharger votre disque dur virtuel sur le conteneur que vous avez créé. Spécifiez le chemin d’accès local sur votre disque dur virtuel sous `/path/to/disk/mydisk.vhd`:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Vous pouvez maintenant créer un ordinateur virtuel à partir de votre téléchargé disque virtuel [à l’aide d’un modèle de gestionnaire de ressources](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Vous pouvez également utiliser la CLI en spécifiant l’URI de votre disque (`--image-urn`). L’exemple suivant crée un ordinateur virtuel nommé `myVM` en utilisant le disque virtuel téléchargés précédemment :

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

Le compte de stockage de destination doit être identique à celui dans lequel vous avez téléchargé votre disque virtuel. Vous devez également spécifier, ou demande de réponse pour tous les autres paramètres requis par le `azure vm create` commande tels que le réseau virtuel, adresse IP publique, nom d’utilisateur et les clés SSH. Vous pouvez en savoir plus sur les [paramètres du Gestionnaire de ressources du CLI disponibles](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Configuration requise
Pour effectuer les étapes suivantes, vous devez :

- Le **système d’exploitation Linux est installé dans un fichier .vhd** - installer une [distribution Linux d’Azure par l’éditeur](virtual-machines-linux-endorsed-distros.md) (ou voir [pour les distributions de non approuvée](virtual-machines-linux-create-upload-generic.md)) à un disque virtuel dans le format de disque dur virtuel. Il existe plusieurs outils pour créer une machine virtuelle et le disque dur virtuel :
    - Installer et configurer les [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), en veillant à utiliser un disque dur virtuel en tant que votre format d’image. Si nécessaire, vous pouvez [convertir une image](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) en utilisant `qemu-img convert`.
    - Vous pouvez également utiliser Hyper-V [sur Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [sur Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Le nouveau format VHDX n’est pas pris en charge dans Azure. Lorsque vous créez un ordinateur virtuel, spécifiez un format VHD. Si nécessaire, vous pouvez convertir des disques VHDX de disque dur virtuel à l’aide de [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou le [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) applet de commande PowerShell. En outre, Azure ne supporte pas télécharger des disques durs virtuels dynamiques, vous devez convertir ces disques pour les disques durs virtuels statiques avant de le télécharger. Vous pouvez utiliser des outils tels que les [Utilitaires de disque dur virtuel Azure pour aller](https://github.com/Microsoft/azure-vhd-utils-for-go) à convertir des disques dynamiques pendant le processus de téléchargement vers Azure.

- Ordinateurs virtuels créés à partir de votre image personnalisée doivent résider dans le même compte de stockage que l’image elle-même
    - Créer un compte de stockage et un conteneur pour stocker votre image personnalisée et la création de machines virtuelles
    - Une fois que vous avez créé tous vos ordinateurs virtuels, vous pouvez supprimer en toute sécurité votre image

Assurez-vous que vous disposez de [L’infrastructure du langage commun Azure](../xplat-cli-install.md) connecté et en utilisant le mode du Gestionnaire de ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacer les noms de paramètre d’exemple par vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `mystorageaccount`, et `myimages`.


<a id="prepimage"> </a>
## <a name="prepare-the-image-to-be-uploaded"></a>Préparer l’image à télécharger

Azure prend en charge les différentes distributions de Linux (voir [Visé de Distributions](virtual-machines-linux-endorsed-distros.md)). Les articles suivants de vous guident tout au long de la préparation des différentes distributions de Linux qui sont pris en charge sur Azure :

- **[Distributions de base centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Autres - Distributions Non approuvée](virtual-machines-linux-create-upload-generic.md)**

Consultez également les **[Notes d’Installation de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** pour obtenir des conseils sur la préparation d’images de Linux pour Azure plus générales.

> [AZURE.NOTE] La [plateforme Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) s’applique aux ordinateurs virtuels exécutant Linux uniquement lorsqu’une des distributions visées est utilisée avec les détails de configuration, comme indiqué dans la section « Prise en charge des Versions » sous [Linux sur les Distributions de Azure-Endorsed](virtual-machines-linux-endorsed-distros.md).


## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Groupes de ressources regrouper logiquement toutes les ressources pour prendre en charge de vos machines virtuelles, telles que le réseau virtuel et le stockage Azure. Apprenez-en plus sur les [groupes de ressources Azure ici](../azure-resource-manager/resource-group-overview.md). Avant de télécharger votre image disque personnalisé et de créer des ordinateurs virtuels, vous devez d’abord créer un groupe de ressources. 

L’exemple suivant crée un groupe de ressources appelé `myResourceGroup` dans les `WestUS` emplacement :

```bash
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Créer un compte de stockage
Ordinateurs virtuels sont stockés en tant que BLOB de page au sein d’un compte de stockage. Apprenez-en plus sur le [stockage de blob Azure ici](../storage/storage-introduction.md#blob-storage). Vous créez un compte de stockage pour vos ordinateurs virtuels et les images de disques personnalisées. Les ordinateurs virtuels que vous créez à partir de votre image de disque personnalisées doivent être dans le même compte de stockage que cette image.

L’exemple suivant crée un compte de stockage nommé `mystorageaccount` dans le groupe de ressources créé précédemment :

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Liste les touches de compte de stockage
Azure génère deux clés de 512 bits accès pour chaque compte de stockage. Ces touches d’accès rapide sont utilisés lors de l’authentification pour le compte de stockage, par exemple pour effectuer des opérations d’écriture. En savoir plus sur la [gestion de l’accès au stockage ici](../storage/storage-create-storage-account.md#manage-your-storage-account). Vous pouvez afficher les touches d’accès rapide avec le `azure storage account keys list` commande.

Afficher les touches d’accès pour le compte de stockage que vous avez créé :

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

La sortie est similaire à :

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Prenez note des `key1` car vous devrez l’utiliser pour interagir avec votre compte de stockage dans les étapes suivantes.

## <a name="create-a-storage-container"></a>Créer un conteneur de stockage
De la même façon que vous créez des répertoires différents pour organiser logiquement de votre système de fichiers local, vous créez des conteneurs dans un compte de stockage pour organiser vos images et vos disques virtuels. Un compte de stockage peut contenir un nombre quelconque de conteneurs. 

L’exemple suivant crée un conteneur nommé `myimages`, qui spécifie la touche d’accès rapide obtenu à l’étape précédente (`key1`) :

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Télécharger le VHD
Maintenant vous pouvez télécharger en fait de votre image de disque personnalisé. Comme avec tous les disques virtuels utilisés par les ordinateurs virtuels, vous charger et stockez l’image de disque personnalisé sous la forme d’un blob de la page.

Spécifier votre clé d’accès, le conteneur que vous avez créé dans l’étape précédente, puis le chemin d’accès à l’image de disque personnalisé sur votre ordinateur local :

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Créer un ordinateur virtuel à partir d’une image personnalisée
Lorsque vous créez des ordinateurs virtuels à partir de votre image de disque personnalisée, spécifiez l’URI de l’image de disque. Assurez-vous que les correspondances de compte de stockage de destination où se trouve l’image de disque personnalisé. Vous pouvez créer votre machine virtuelle en utilisant le modèle CLI d’Azure ou le Gestionnaire de ressources JSON.


### <a name="create-a-vm-using-the-azure-cli"></a>Créer un ordinateur virtuel à l’aide de la CLI d’Azure
Vous spécifiez le `--image-urn` paramètre avec le `azure vm create` commande pour pointer vers votre image de disque personnalisé. Assurez-vous que `--storage-account-name` correspond au compte de stockage où se trouve l’image de disque personnalisé. Vous n’avez pas à utiliser le même conteneur que l’image de disque personnalisés pour stocker vos ordinateurs virtuels. Veillez à créer tous les conteneurs supplémentaires de la même façon que les étapes précédentes avant de télécharger vos images de disques personnalisées.

L’exemple suivant crée un ordinateur virtuel nommé `myVM` à partir de votre image de disque personnalisé :

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Vous devez toujours spécifier, ou demande de réponse pour tous les autres paramètres requis par le `azure vm create` commande tels que le réseau virtuel, adresse IP publique, nom d’utilisateur et les clés SSH. Apprenez-en plus sur les [paramètres du Gestionnaire de ressources du CLI disponibles](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Créer un ordinateur virtuel à l’aide d’un modèle JSON
Azure modèles du Gestionnaire de ressources sont des fichiers JavaScript Objet Notation (JSON) qui définissent l’environnement que vous souhaitez créer. Les modèles sont répartis à des fournisseurs de ressources différents tels que compute ou de réseau. Vous pouvez utiliser les modèles existants ou écrire les vôtres. Apprenez-en plus sur [à l’aide du Gestionnaire de ressources et de modèles](../azure-resource-manager/resource-group-overview.md).

Dans le `Microsoft.Compute/virtualMachines` fournisseur de votre modèle, vous avez un `storageProfile` le nœud qui contient les détails de configuration pour votre ordinateur virtuel. Les deux principaux paramètres à modifier sont la `image` et `vhd` URI qui pointe vers votre image de disque personnalisé et le disque virtuel de votre nouveau VM. Vous trouverez ci-dessous un exemple de la JSON pour l’utilisation d’une image de disque personnalisé :

```bash
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Vous pouvez utiliser [ce modèle existant pour créer un ordinateur virtuel à partir d’une image personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou en savoir plus sur la [Création de vos propres modèles Azure le Gestionnaire de ressources](../resource-group-authoring-templates.md). 

Une fois que vous avez un modèle configuré, vous créez vos ordinateurs virtuels à l’aide de la `azure group deployment create` commande. Spécifiez l’URI de votre modèle de JSON avec le `--template-uri` paramètre :

```bash
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Si vous avez un fichier JSON stocké localement sur votre ordinateur, vous pouvez utiliser la `--template-file` paramètre à la place :

```bash
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Étapes suivantes
Après avoir préparé et téléchargés de votre disque virtuel personnalisé, vous pouvez en savoir plus sur [l’utilisation de modèles et le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md). Vous pouvez également [Ajouter un disque de données](virtual-machines-linux-add-disk.md) pour vos nouveaux ordinateurs virtuels. Si vous avez des applications qui s’exécutent sur vos ordinateurs virtuels dont vous avez besoin pour accéder, veillez à [Ouvrir les ports et les points de terminaison](virtual-machines-linux-nsg-quickstart.md).