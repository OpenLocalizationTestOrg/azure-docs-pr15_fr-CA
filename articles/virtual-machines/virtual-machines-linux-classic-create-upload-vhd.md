<properties
    pageTitle="Créer et télécharger un VHD Linux | Microsoft Azure"
    description="Créer et télécharger un disque dur virtuel (VHD) Azure avec le modèle classique de déploiement qui contient le système d’exploitation Linux."
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
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Création et chargement d’un disque dur virtuel qui contient le système d’exploitation Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Vous pouvez également [télécharger une image de disque personnalisé à l’aide du Gestionnaire de ressources Azure](virtual-machines-linux-upload-vhd.md).

Cet article vous montre comment créer et télécharger un disque dur virtuel (VHD), vous pouvez l’utiliser comme votre propre image pour créer des ordinateurs virtuels dans Azure. Apprenez à préparer le système d’exploitation qui vous permet de créer plusieurs machines virtuelles basées sur cette image. 

>  [AZURE.NOTE] Si vous disposez d’un certain temps, veuillez nous aider à améliorer la documentation Azure VM de Linux en prenant cette [enquête rapide](https://aka.ms/linuxdocsurvey) de vos expériences. Chaque réponse nous permet de vous aident à rendre votre travail.

## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous disposez des éléments suivants :

- **Système d’exploitation Linux dans un fichier .vhd** , vous avez installé une [distribution Linux d’Azure par l’éditeur](virtual-machines-linux-endorsed-distros.md) (ou voir [pour les distributions de non approuvé](virtual-machines-linux-create-upload-generic.md)) à un disque virtuel dans le format de disque dur virtuel. Il existe plusieurs outils pour créer une machine virtuelle et le disque dur virtuel :
    - Installer et configurer les [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), en veillant à utiliser un disque dur virtuel en tant que votre format d’image. Si nécessaire, vous pouvez [convertir une image](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) en utilisant `qemu-img convert`.
    - Vous pouvez également utiliser Hyper-V [sur Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [sur Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Le nouveau format VHDX n’est pas pris en charge dans Azure. Lorsque vous créez un ordinateur virtuel, spécifiez un format VHD. Si nécessaire, vous pouvez convertir des disques VHDX de disque dur virtuel à l’aide de [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou le [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) applet de commande PowerShell. En outre, Azure ne supporte pas télécharger des disques durs virtuels dynamiques, vous devez convertir ces disques pour les disques durs virtuels statiques avant de le télécharger. Vous pouvez utiliser des outils tels que les [Utilitaires de disque dur virtuel Azure pour aller](https://github.com/Microsoft/azure-vhd-utils-for-go) à convertir des disques dynamiques pendant le processus de téléchargement vers Azure.

- **Interface de ligne de commande de azure** - installation de dernière [Interface de ligne de commande de Azure](../virtual-machines-command-line-tools.md) pour télécharger le disque dur virtuel.

<a id="prepimage"> </a>
## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Étape 1 : Préparation de l’image à télécharger

Azure prend en charge les différentes distributions de Linux (voir [Visé de Distributions](virtual-machines-linux-endorsed-distros.md)). Les articles suivants vous guident tout au long de comment préparer les différentes distributions de Linux qui sont pris en charge sur Azure. Après avoir terminé les étapes décrites dans les guides suivants, fournis ici une fois que vous avez un fichier de disque dur virtuel est prêt à télécharger vers Azure :

- **[Distributions de base centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Autres - Distributions Non approuvée](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] La plateforme Azure SLA s’applique aux machines virtuelles exécutant le système d’exploitation Linux uniquement lorsqu’une des distributions visées est utilisée avec les détails de configuration, comme indiqué dans la section « Prise en charge des Versions » sous [Linux sur les Distributions de Azure-Endorsed](virtual-machines-linux-endorsed-distros.md). Toutes les distributions de Linux dans la galerie d’image Azure sont les distributions visées à la configuration requise.

Consultez également les **[Notes d’Installation de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** pour obtenir des conseils sur la préparation d’images de Linux pour Azure plus générales.


<a id="connect"> </a>
## <a name="step-2-prepare-the-connection-to-azure"></a>Étape 2 : Préparer la connexion à Azure

Vérifiez que vous utilisez la CLI d’Azure dans le modèle de déploiement classique (`azure config mode asm`), puis connectez-vous à votre compte :

```
azure login
```


<a id="upload"> </a>
## <a name="step-3-upload-the-image-to-azure"></a>Étape 3 : Télécharger l’image vers Azure

Vous avez besoin pour charger votre fichier de disque dur virtuel pour un compte de stockage. Vous pouvez soit en choisir un compte de stockage existant ou de [créer un nouveau](../storage/storage-create-storage-account.md).

Utilisez la CLI Azure à télécharger l’image à l’aide de la commande suivante :

```bash
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

Dans l’exemple précédent :

- **BlobStorageURL** est l’URL pour le compte de stockage que vous souhaitez utiliser
- **YourImagesFolder** est le conteneur dans le stockage blob où vous souhaitez stocker vos images
- **VHDName** est l’étiquette qui apparaît dans le portail pour identifier le disque dur virtuel.
- **PathToVHDFile** est le chemin d’accès complet et le nom du fichier .vhd sur votre ordinateur.

Vous trouverez ci-dessous un exemple complet :

```bash
azure vm image create UbuntuLTS `
    --blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
    --os Linux /home/ahmet/UbuntuLTS.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Étape 4 : Créer un ordinateur virtuel à partir de l’image
Vous créez une machine virtuelle à l’aide de `azure vm create` de la même manière, comme un ordinateur virtuel standard. Spécifiez le nom que vous avez donné à votre image à l’étape précédente. Dans l’exemple suivant, nous utilisons le nom d’image **UbuntuLTS** donné à l’étape précédente :

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "DeployedUbuntu" UbuntuLTS
```

Pour créer vos propres ordinateurs virtuels, fournissez votre propre nom d’utilisateur + mot de passe emplacement, nom DNS et nom de l’image.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [référence CLI d’Azure pour le modèle de déploiement classique d’Azure](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload
