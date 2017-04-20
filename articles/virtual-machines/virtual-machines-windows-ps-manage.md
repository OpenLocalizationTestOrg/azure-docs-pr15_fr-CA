<properties
    pageTitle="Gérer des ordinateurs virtuels à l’aide du Gestionnaire de ressources et PowerShell | Microsoft Azure"
    description="Gérer des ordinateurs virtuels à l’aide du Gestionnaire de ressources Azure et PowerShell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>Gérer des ordinateurs virtuels de Azure à l’aide du Gestionnaire de ressources et PowerShell

## <a name="install-azure-powershell"></a>Installer PowerShell Azure
 
Pour plus d’informations sur l’installation de la version la plus récente de PowerShell d’Azure, en sélectionnant votre abonnement et votre compte d’ouverture de session, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

## <a name="set-variables"></a>Jeu de variables

Toutes les commandes dans l’article requièrent le nom du groupe de ressources où se trouve l’ordinateur virtuel et le nom de l’ordinateur virtuel à gérer. Remplacez la valeur de **$rgName** par le nom du groupe de ressources qui contient l’ordinateur virtuel. Remplacez la valeur de **$vmName** par le nom de la machine virtuelle. Créer les variables.

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>Afficher des informations sur une machine virtuelle

Obtenir les informations de l’ordinateur virtuel.
  
    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Il renvoie quelque chose comme dans l’exemple suivant :

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## <a name="stop-a-virtual-machine"></a>Arrêter une machine virtuelle

Arrêtez l’ordinateur virtuel en cours d’exécution.

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Vous êtes invité à confirmer :

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
        
Entrez **Y** pour arrêter la machine virtuelle.

Après quelques minutes, il renvoie quelque chose comme dans l’exemple :

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>Démarrage d’une machine virtuelle

Démarrez l’ordinateur virtuel s’il est arrêté.

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Après quelques minutes, il renvoie quelque chose comme dans l’exemple :

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

Si vous souhaitez redémarrer un ordinateur virtuel qui est déjà en cours d’exécution, utilisez **Restart-AzureRmVM** décrit suivant.

## <a name="restart-a-virtual-machine"></a>Redémarrer une machine virtuelle

Redémarrez l’ordinateur virtuel en cours d’exécution.

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Il renvoie quelque chose comme dans l’exemple suivant :

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>Supprimer un ordinateur virtuel

Supprimer l’ordinateur virtuel.  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [AZURE.NOTE] Vous pouvez utiliser le **-Force** paramètre pour ignorer l’invite de confirmation.

Si vous n’avez pas utilisé le paramètre - Force, vous êtes invité à confirmer :

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Il renvoie quelque chose comme dans l’exemple suivant :

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>Mise à jour d’une machine virtuelle

Cet exemple montre comment mettre à jour la taille de la machine virtuelle.
        
    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    
Il renvoie quelque chose comme dans l’exemple suivant :

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK
                              
Pour une liste des tailles disponibles pour une machine virtuelle, reportez-vous à la section [tailles pour les machines virtuelles dans Azure](virtual-machines-windows-sizes.md) .

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Ajouter un disque de données à une machine virtuelle

Cet exemple montre comment ajouter un disque de données à un ordinateur virtuel existant.

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

Le disque que vous ajoutez n’est pas initialisé. Pour initialiser le disque, vous pouvez vous connecter à elle et utiliser Gestion des disques. Si vous avez installé WinRM et un certificat dessus lors de sa création, vous pouvez utiliser PowerShell distant d’initialiser le disque. Vous pouvez également utiliser une extension de script personnalisé : 

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

Le fichier de script peut contenir quelque chose comme ce code pour initialiser les disques :

    $disks = Get-Disk |   Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { ([char]$_) }
    $count = 0
    $labels = @("data1","data2")

    foreach($d in $disks) {
        $driveLetter = $letters[$count].ToString()
        $d | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] `
            -Confirm:$false -Force 
        $count++
    }

## <a name="next-steps"></a>Étapes suivantes

S’il y a des problèmes avec un déploiement, vous ressemble à des [déploiements de groupe de ressources de résolution des problèmes avec Azure portal](../resource-manager-troubleshoot-deployments-portal.md)
