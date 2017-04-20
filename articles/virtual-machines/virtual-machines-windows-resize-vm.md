<properties
    pageTitle="Redimensionner un Windows machine virtuelle | Microsoft Azure"
    description="Redimensionner une machine virtuelle de Windows créée dans le modèle de déploiement Gestionnaire de ressources, à l’aide de Powershell d’Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>

    
# <a name="resize-a-windows-vm"></a>Redimensionner une machine virtuelle de Windows

Cet article vous montre comment redimensionner une machine virtuelle Windows, créé dans le modèle de déploiement Gestionnaire de ressources à l’aide de Powershell d’Azure.

Après avoir créé une machine virtuelle (VM), vous pouvez mettre à l’échelle la machine virtuelle vers le haut ou vers le bas en modifiant la taille de la mémoire virtuelle. Dans certains cas, vous devez libérer tout d’abord la machine virtuelle. Cela peut se produire si la nouvelle taille n’est pas disponible sur le cluster de matériel qui héberge actuellement la machine virtuelle.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Redimensionner une VM Windows pas dans un jeu de disponibilité

1. Répertorie les tailles de VM sont disponibles sur le cluster de matériel dans lequel l’ordinateur virtuel est hébergé. 

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```

2. Si la taille souhaitée est répertoriée, exécutez les commandes suivantes pour redimensionner la machine virtuelle. Si la taille souhaitée n’est pas répertoriée, passez à l’étape 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Si la taille souhaitée n’est pas répertoriée, exécutez les commandes suivantes pour libérer de la mémoire virtuelle, redimensionner et redémarrez la machine virtuelle.

    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -VMName $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -VMName $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [AZURE.WARNING] Mises à jour toutes les adresses IP dynamiques affectées à la machine virtuelle de libérer de la mémoire virtuelle. Les disques du système d’exploitation et les données ne sont pas affectés. 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Redimensionner une machine virtuelle de Windows dans un jeu de disponibilité

Si la nouvelle taille d’un ordinateur virtuel dans un jeu de disponibilité n’est pas disponible sur le cluster de matériel qui héberge actuellement la machine virtuelle, puis tous les ordinateurs virtuels dans l’ensemble de la disponibilité devez être libérée pour redimensionner la machine virtuelle. Vous devrez également mettre à jour la taille des autres machines virtuelles en termes de disponibilité après une VM a été redimensionnée. Pour redimensionner un ordinateur virtuel dans un jeu de disponibilité, procédez comme suit.

1. Répertorie les tailles de VM sont disponibles sur le cluster de matériel dans lequel l’ordinateur virtuel est hébergé.

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```

2. Si la taille souhaitée est répertoriée, exécutez les commandes suivantes pour redimensionner la machine virtuelle. S’il n’est pas répertorié, passez à l’étape 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Si la taille souhaitée n’est pas répertoriée, continuez avec les étapes suivantes pour libérer tous les ordinateurs virtuels dans l’ensemble de la disponibilité, redimensionner des ordinateurs virtuels et les redémarrer.

4.  Arrêtez tous les ordinateurs virtuels dans l’ensemble de la disponibilité.

    ```powershell
    $rg = "<resourceGroupName>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
    } 
    ```
              
5.  Redimensionner et redémarrer les ordinateurs virtuels dans l’ensemble de la disponibilité.

    ```powershell
    $rg = "<resourceGroupName>"
    $newSize = "<newVmSize>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
        $vm.HardwareProfile.VmSize = $newSize
        Update-AzureRmVM -ResourceGroupName $rg -VM $vm
        Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

- Pour une évolutivité supplémentaire, exécuter plusieurs instances de la machine virtuelle et évoluer. Pour plus d’informations, consultez [mise à l’échelle les machines Windows dans une échelle de Machine virtuelle définie automatiquement](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).



