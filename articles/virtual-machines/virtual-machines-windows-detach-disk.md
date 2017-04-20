<properties
    pageTitle="Détacher un disque de données à partir d’un ordinateur virtuel de Windows | Microsoft Azure"
    description="Apprenez à détacher un disque de données à partir d’un ordinateur virtuel dans Azure en utilisant le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>



# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Comment faire pour détacher un disque de données à partir d’une machine virtuelle Windows


Lorsque vous avez un disque de données qui est associé à une machine virtuelle, vous pouvez facilement la détacher. Cela supprime le disque de l’ordinateur virtuel, mais ne le supprime pas du stockage. 

> [AZURE.WARNING] Si vous détachez un disque, qu'il n’est pas automatiquement supprimé. Si vous avez souscrit au stockage de prime, vous continuerez à encourir des frais de stockage pour le disque. Pour plus d’informations, reportez-vous à la [tarification et facturation lors de l’utilisation du stockage de la prime](../storage/storage-premium-storage.md#pricing-and-billing). 

Si vous souhaitez réutiliser les données existantes sur le disque, vous pouvez joindre de nouveau au même ordinateur virtuel, ou d’un autre.  


## <a name="detach-a-data-disk-using-the-portal"></a>Détacher un disque de données à l’aide du portail

1. Dans le portail concentrateur, sélectionnez **Machines virtuelles**.

2. Sélectionnez l’ordinateur virtuel qui possède le disque de données que vous souhaitez détacher, puis que vous cliquez sur **tous les paramètres**.

3. La lame de **paramètres** , sélectionnez les **disques**.

4. De la lame de **disques** , sélectionnez le disque de données que vous souhaitez détacher.

5. De la lame pour le disque de données, cliquez sur **Détacher**.


    ![Capture d’écran affichant le bouton détacher.](./media/virtual-machines-windows-detach-disk/detach-disk.png)

Le disque reste dans le stockage, mais n’est plus attaché à une machine virtuelle.


## <a name="detach-a-data-disk-using-powershell"></a>Détacher un disque de données à l’aide de PowerShell

Dans cet exemple, la première commande Obtient l’ordinateur virtuel nommé **MyVM07** dans le groupe de ressources **RG11** à l’aide de l’applet de commande Get-AzureRmVM. La commande stocke la machine virtuelle dans la variable **$VirtualMachine** . 

La deuxième commande supprime le disque de données nommé DataDisk3 à partir de l’ordinateur virtuel. 

La commande finale met à jour l’état de la machine virtuelle pour terminer le processus de suppression du disque de données.

    $VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
    Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
    Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


Pour plus d’informations, consultez [Supprimer-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez réutiliser le disque de données, vous pouvez uniquement [attacher une autre VM](virtual-machines-windows-attach-disk-portal.md)
