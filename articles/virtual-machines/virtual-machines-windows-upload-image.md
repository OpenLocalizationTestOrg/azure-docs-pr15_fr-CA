<properties
    pageTitle="Télécharger un disque dur virtuel de Windows pour le Gestionnaire de ressources | Microsoft Azure"
    description="Découvrez comment télécharger une Windows machine virtuelle VHD sur site Azure, en utilisant le modèle de déploiement du Gestionnaire de ressources. Vous pouvez télécharger un disque dur virtuel à partir soit un généralisée ou une machine virtuelle spécifique."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Télécharger un disque dur virtuel à partir d’un ordinateur virtuel de locaux vers Azure de Windows 


Cet article vous montre comment créer et télécharger un disque dur virtuel Windows (disque dur virtuel) à utiliser lors de la création d’une machine virtuelle d’Azure. Vous pouvez télécharger un disque dur virtuel à partir d’une machine virtuelle généralisée ou une machine virtuelle spécifique. 

Pour plus d’informations sur les disques et les disques durs virtuels dans Azure, voir [a propos des disques et des disques durs virtuels pour les machines virtuelles](virtual-machines-linux-about-disks-vhds.md).


## <a name="prepare-the-vm"></a>Préparer la machine virtuelle 

Vous pouvez télécharger les VHD spécialisés et généralisées dans Azure. Chaque type nécessite que vous préparez avant de démarrer la machine virtuelle.

- **Généralisée de disque dur virtuel** - un disque dur virtuel généralisé a toutes les informations de votre compte personnel à l’aide de Sysprep. Si vous avez l’intention d’utiliser le disque dur virtuel en tant qu’image pour créer de nouvelles machines virtuelles à partir de, vous devez :
    - [Préparer un disque dur virtuel de Windows pour télécharger vers Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 
    - [Generalize l’ordinateur virtuel à l’aide de Sysprep](virtual-machines-windows-generalize-vhd.md). 

- **VHD spécialisé** - un disque dur virtuel spécialisé gère les comptes d’utilisateurs, d’applications et d’autres données sur l’état à partir de votre ordinateur virtuel d’origine. Si vous envisagez d’utiliser le disque dur virtuel en tant que-consiste à créer un nouvel ordinateur virtuel, assurez-vous que les étapes suivantes sont effectuées. 
    - [Préparer un disque dur virtuel de Windows pour télécharger vers Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). **Pas** de généraliser la machine virtuelle à l’aide de Sysprep.
    - Supprimer tous les outils de virtualisation invité et les agents installés sur la machine virtuelle (c'est-à-dire les outils VMware).
    - Assurez-vous que l’ordinateur virtuel est configuré pour extraire son adresse IP et les paramètres DNS via DHCP. Cela garantit que le serveur obtient une adresse IP dans le VNet lors de son démarrage. 

## <a name="log-in-to-azure"></a>Ouvrez une session Azure

Si vous ne disposez pas de PowerShell version 1.4 ou supérieur installé, lire [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

1. Ouvrez Azure PowerShell et vous connecter à votre compte Azure. Une fenêtre contextuelle s’ouvre pour vous permettre d’entrer vos informations d’identification de compte Azure.

    ```powershell
    Login-AzureRmAccount
    ```


2. Obtenir l’ID d’abonnement pour vos abonnements disponibles.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Définir l’abonnement approprié à l’aide de l’ID d’abonnement. Remplacer `<subscriptionID>` avec l’ID de l’abonnement approprié.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obtenir le compte de stockage

Vous avez besoin d’un compte de stockage dans Azure pour stocker l’image chargée de la machine virtuelle. Vous pouvez utiliser un compte de stockage existant ou créez-en un nouveau. 

Pour afficher les comptes de stockage disponibles, tapez :

```powershell
Get-AzureRmStorageAccount
```

Si vous souhaitez utiliser un compte de stockage existant, passez à la section de [téléchargement de l’image de machine virtuelle](#upload-the-vm-vhd-to-your-storage-account) .

Si vous devez créer un compte de stockage, procédez comme suit :

1. Vous avez besoin du nom du groupe de ressources où le compte de stockage doit être créé. Pour trouver tous les groupes de ressources qui se trouvent dans votre abonnement, tapez :

    ```powershell
    Get-AzureRmResourceGroup
    ```

Pour créer un groupe de ressources nommé **myResourceGroup** dans la région de **l’Ouest des États-Unis** , tapez :

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Créer un compte de stockage nommé **mystorageaccount** dans ce groupe de ressources à l’aide de l’applet de commande [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) :

    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" -SkuName "Standard_LRS" -Kind "Storage"
    ```
            
    Les valeurs valides pour - SkuName sont :

    - **Standard_LRS** - stockage redondant localement. 
    - **Standard_ZRS** - stockage redondant de Zone.
    - **Standard_GRS** - stockage redondant de géo. 
    - **Standard_RAGRS** - stockage redondant géographique de l’accès en lecture. 
    - **Premium_LRS** - stockage localement redondant de prime. 



## <a name="upload-the-vhd-to-your-storage-account"></a>Télécharger le disque dur virtuel à votre compte de stockage

L’applet de commande [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) permet de télécharger l’image sur un conteneur dans votre compte de stockage. Cet exemple transfère le fichier **myVHD.vhd** à partir de `"C:\Users\Public\Documents\Virtual hard disks\"` à un stockage compte nommé **mystorageaccount** , dans le groupe de ressources **myResourceGroup** . Le fichier sera placé dans le conteneur nommé **mycontainer** et le nouveau nom de fichier sera **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


En cas de réussite, vous obtenez une réponse qui ressemble à ceci :

```
  C:\> Add-AzureRmVhd -ResourceGroupName myResourceGroup -Destination https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
  MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
  MD5 hash calculation is completed.
  Elapsed time for the operation: 00:03:35
  Creating new page blob of size 53687091712...
  Elapsed time for upload: 01:12:49

  LocalFilePath           DestinationUri
  -------------           --------------
  C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

En fonction de votre connexion réseau et la taille de votre fichier de disque dur virtuel, cette commande peut prendre un certain temps


## <a name="next-steps"></a>Étapes suivantes

- [Créer un ordinateur virtuel dans Azure à partir d’un disque dur virtuel généralisé](virtual-machines-windows-create-vm-generalized.md)
- [Créer un ordinateur virtuel dans Azure à partir d’un disque dur virtuel spécialisé](virtual-machines-windows-create-vm-specialized.md) en le joignant comme un disque du système d’exploitation lorsque vous créez une nouvelle machine virtuelle.


