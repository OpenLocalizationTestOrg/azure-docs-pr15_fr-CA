<properties
    pageTitle="Modification d’un groupe de disponibilité de machines virtuelles | Microsoft Azure"
    description="Apprenez à modifier la disponibilité pour vos ordinateurs virtuels à l’aide de PowerShell d’Azure et le modèle de déploiement du Gestionnaire de ressources."
    keywords=""
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="drewm"/>



# <a name="change-the-availability-set-for-a-windows-vm"></a>Modification de la disponibilité pour une machine virtuelle de Windows

Les étapes suivantes décrivent comment modifier l’ensemble de la disponibilité d’un ordinateur virtuel à l’aide de PowerShell d’Azure. Une machine virtuelle ne peut être ajoutée à une disponibilité définie lors de sa création. Afin de modifier la disponibilité, vous devez supprimer et recréer l’ordinateur virtuel. 

## <a name="change-the-availability-set-using-powershell"></a>Modification de la disponibilité à l’aide de PowerShell

1. Capturer les détails clés suivants à partir de l’ordinateur virtuel à modifier.

    Nom de la machine virtuelle
    
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <Name-of-resource-group> -Name <name-of-VM>
    $vm.Name
    ```
 
    Taille de mémoire virtuelle
    
    ```powershell
    $vm.HardwareProfile.VmSize
    ```

    Interface réseau principale du réseau et les interfaces de composants réseau facultatifs s’ils existent sur l’ordinateur virtuel
    
    ```powershell
    $vm.NetworkProfile.NetworkInterfaces[0].Id
    ```

    Profil de disque du système d’exploitation

    ```powershell
    $vm.StorageProfile.OsDisk.OsType
    $vm.StorageProfile.OsDisk.Name
    $vm.StorageProfile.OsDisk.Vhd.Uri
    ```

    Profils de disque pour chaque disque de données 
    
    ```powershell
    $vm.StorageProfile.DataDisks[<index>].Lun
    $vm.StorageProfile.DataDisks[<index>].Vhd.Uri
    ```

    Extensions VM installées 
    
    ```powershell
    $vm.Extensions
    ```

2. Supprimer la machine virtuelle sans supprimer les disques ou les interfaces réseau.

    ```powershell
    Remove-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName> 
    ```

3. Créer la disponibilité si elle n’existe pas

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName <resourceGroupName> -Name <availabilitySetName> -Location "<location>" 
    ```

4. Recréer l’ordinateur virtuel à l’aide de la nouvelle série de disponibilité

    ```powershell
    $vm2 = New-AzureRmVMConfig -VMName <VM-name> -VMSize <vm-size> -AvailabilitySetId <availability-set-id>

    Set-AzureRmVMOSDisk -CreateOption "Attach" -VM <vmConfig> -VhdUri <osDiskURI> -Name <osDiskName> [-Windows | -Linux]

    Add-AzureRmVMNetworkInterface -VM <vmConfig> -Id  <nicId> 

    New-AzureRmVM -ResourceGroupName <resourceGroupName> -Location <location> -VM <vmConfig>
    ``` 

5. Ajouter des extensions et des disques de données. Pour plus d’informations, consultez [Attacher des disques de données de machine virtuelle](virtual-machines-windows-attach-disk-portal.md) et les [Exemples de Configuration d’Extension](virtual-machines-windows-extensions-configuration-samples.md). Les extensions et les disques de données peuvent être ajoutées à la machine virtuelle à l’aide de PowerShell ou Azure CLI.

## <a name="example-script"></a>Exemple de Script

Le script suivant fournit un exemple de rassembler les informations requises, la suppression de la machine virtuelle d’origine et puis de le recréer dans un nouvel ensemble de disponibilité.

```powershell
    #set variables
    $rg = "demo-resource-group"
    $vmName = "demo-vm"
    $newAvailSetName = "demo-as"
    $outFile = "C:\temp\outfile.txt"

    #Get VM Details
    $OriginalVM = get-azurermvm -ResourceGroupName $rg -Name $vmName

    #Output VM details to file
    "VM Name: " | Out-File -FilePath $outFile 
    $OriginalVM.Name | Out-File -FilePath $outFile -Append

    "Extensions: " | Out-File -FilePath $outFile -Append
    $OriginalVM.Extensions | Out-File -FilePath $outFile -Append

    "VMSize: " | Out-File -FilePath $outFile -Append
    $OriginalVM.HardwareProfile.VmSize | Out-File -FilePath $outFile -Append

    "NIC: " | Out-File -FilePath $outFile -Append
    $OriginalVM.NetworkProfile.NetworkInterfaces[0].Id | Out-File -FilePath $outFile -Append

    "OSType: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.OsType | Out-File -FilePath $outFile -Append

    "OS Disk: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.Vhd.Uri | Out-File -FilePath $outFile -Append

    if ($OriginalVM.StorageProfile.DataDisks) {
    "Data Disk(s): " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.DataDisks | Out-File -FilePath $outFile -Append
    }

    #Remove the original VM
    Remove-AzureRmVM -ResourceGroupName $rg -Name $vmName

    #Create new availability set if it does not exist
    $availSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -ErrorAction Ignore
    if (-Not $availSet) {
    $availset = New-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -Location $OriginalVM.Location
    }

    #Create the basic configuration for the replacement VM
    $newVM = New-AzureRmVMConfig -VMName $OriginalVM.Name -VMSize $OriginalVM.HardwareProfile.VmSize -AvailabilitySetId $availSet.Id
    Set-AzureRmVMOSDisk -VM $NewVM -VhdUri $OriginalVM.StorageProfile.OsDisk.Vhd.Uri  -Name $OriginalVM.Name -CreateOption Attach -Windows

    #Add Data Disks
    foreach ($disk in $OriginalVM.StorageProfile.DataDisks ) { 
    Add-AzureRmVMDataDisk -VM $newVM -Name $disk.Name -VhdUri $disk.Vhd.Uri -Caching $disk.Caching -Lun $disk.Lun -CreateOption Attach -DiskSizeInGB $disk.DiskSizeGB
    }

    #Add NIC(s)
    foreach ($nic in $OriginalVM.NetworkInterfaceIDs) {
        Add-AzureRmVMNetworkInterface -VM $NewVM -Id $nic
    }

    #Create the VM
    New-AzureRmVM -ResourceGroupName $rg -Location $OriginalVM.Location -VM $NewVM -DisableBginfoExtension
```

## <a name="next-steps"></a>Étapes suivantes

Ajouter un stockage supplémentaire pour votre ordinateur virtuel en ajoutant un [disque de données](virtual-machines-windows-attach-disk-portal.md).

