<properties
    pageTitle="Capturer une image de machine virtuelle à partir de généralisée Azure VM | Microsoft Azure"
    description="Découvrez comment capturer une image de machine virtuelle à partir d’une machine virtuelle Azure généralisée créées dans le modèle de déploiement du Gestionnaire de ressources"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>

# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Comment faire pour capturer une image de machine virtuelle à partir d’un généralisée Azure VM


Cet article vous indique comment utiliser PowerShell d’Azure pour créer une image d’un ordinateur virtuel de Azure généralisée. Vous pouvez ensuite utiliser l’image pour créer une autre VM. L’image inclut le disque du système d’exploitation et les disques de données associés à l’ordinateur virtuel. L’image n’inclut pas les ressources de réseau virtuel, vous devez configurer les ressources lorsque vous créez la nouvelle machine virtuelle. 


## <a name="prerequisites"></a>Conditions préalables

- Vous devez au préalable avoir [généralisé de la machine virtuelle](virtual-machines-windows-generalize-vhd.md). Généralisation d’un ordinateur virtuel supprime toutes vos informations de compte personnelles, entre autres choses et prépare l’ordinateur pour être utilisés en tant qu’image.

- Vous devez avoir Azure PowerShell version 1.0.x ou ultérieure. Si vous ne l’avez pas déjà installé PowerShell, lisez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour les étapes de l’installation.


## <a name="log-in-to-azure-powershell"></a>Connexion à PowerShell Azure

1. Ouvrez Azure PowerShell et vous connecter à votre compte Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    Une fenêtre contextuelle s’ouvre pour vous permettre d’entrer vos informations d’identification de compte Azure.

2. Obtenir l’ID d’abonnement pour vos abonnements disponibles.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Définir l’abonnement approprié à l’aide de l’ID d’abonnement.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Désallouer la machine virtuelle et de définir l’état généralisé       

1. Libérer les ressources de la machine virtuelle.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```

    L' *état* de la machine virtuelle dans le portail Azure change de **arrêté** arrêté **(libérés)**.

2. Définir l’état de la machine virtuelle à **Generalized**. 

    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```

3. Vérifiez l’état de la machine virtuelle. La section **OSState/généralisée** pour l’ordinateur virtuel doit avoir le **DisplayStatus** défini sur **l’ordinateur virtuel est généralisée**.  

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Créer l’image 

1. Copiez l’image de machine virtuelle pour le conteneur de stockage de destination à l’aide de cette commande. L’image est créée dans le même compte de stockage que la machine virtuelle d’origine. Le `-Path` paramètre enregistre une copie du modèle JSON localement. Le `-DestinationContainerName` paramètre est le nom du conteneur vers lequel vous souhaitez stocker vos images. Si le conteneur n’existe pas, il est créé pour vous.

    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```

    Vous pouvez obtenir l’URL d’une image à partir du modèle de fichier JSON. Accédez aux **ressources** > **storageProfile** > **osDisk** > **image** > section**d’uri** pour le chemin d’accès complet de votre image. L’URL de l’image ressemble à : `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
    
    Vous pouvez également vérifier l’URI dans le portail. L’image est copiée dans un conteneur nommé **système** dans votre compte de stockage. 


## <a name="next-steps"></a>Étapes suivantes

- Maintenant, vous pouvez [créer un ordinateur virtuel à partir de l’image](virtual-machines-windows-create-vm-generalized.md).

