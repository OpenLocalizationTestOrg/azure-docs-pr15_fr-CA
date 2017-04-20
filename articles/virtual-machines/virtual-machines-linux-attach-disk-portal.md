<properties
    pageTitle="Connectez un disque de données à une VM Linux | Microsoft Azure"
    description="Comment joindre un disque de données nouveau ou existant à une VM de Linux dans le portail Azure en utilisant le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cynthn"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Comment attacher un disque de données à une VM de Linux dans le portail Azure

Cet article vous montre comment attacher des disques à la fois nouveaux et existants à une machine virtuelle de Linux via le portail Azure. Vous pouvez également [attacher un disque de données à un ordinateur virtuel Windows Azure portail](virtual-machines-windows-attach-disk-portal.md). Avant de le faire, passez en revue les conseils suivants :

- La taille de l’ordinateur virtuel contrôle le nombre des disques de données que vous pouvez joindre. Pour plus d’informations, reportez-vous à la section [tailles pour les machines virtuelles](virtual-machines-linux-sizes.md).
- Pour utiliser le stockage de prime, vous aurez besoin d’une machine virtuelle série DS ou de la série GS. Vous pouvez utiliser des disques à partir de comptes de stockage à la fois Standard et Premium avec ces ordinateurs virtuels. Stockage de prime est disponible dans certaines régions. Pour plus d’informations, consultez [prime stockage : stockage hautes performances pour les charges de travail de Machine virtuelle Azure](../storage/storage-premium-storage.md).
- Des disques connectés à des ordinateurs virtuels sont en fait des fichiers .vhd dans un compte de stockage Azure. Pour plus de détails, voir [a propos des disques et des disques durs virtuels pour les machines virtuelles](virtual-machines-linux-about-disks-vhds.md).
- Pour un nouveau disque, vous n’avez pas besoin de créer tout d’abord, car Azure qu’il crée lorsque vous l’attachez.
- Pour un disque existant, le fichier .vhd doit être disponible dans un compte de stockage Azure. Vous pouvez utiliser un .vhd déjà, si elle n’est pas connectée à un autre ordinateur virtuel ou le téléchargement de fichiers de votre propre .vhd pour le compte de stockage.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois que le disque est ajouté, vous devez préparer son utilisation. Reportez-vous à la section dans le système d’exploitation de la machine virtuelle : « Comment : initialiser un nouveau disque de données sous Linux » dans cet [article](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux).
