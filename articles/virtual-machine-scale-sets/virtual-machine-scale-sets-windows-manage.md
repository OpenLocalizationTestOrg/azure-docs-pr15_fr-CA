<properties
    pageTitle="Gérer des ordinateurs virtuels dans un ensemble d’échelle de Machine virtuelle | Microsoft Azure"
    description="Gérer des ordinateurs virtuels dans une échelle de machine virtuelle à l’aide de PowerShell d’Azure."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Gérer des ordinateurs virtuels dans un ensemble d’échelle de machine virtuelle

Utiliser les tâches dans cet article pour gérer des ordinateurs virtuels dans votre jeu d’échelle de machine virtuelle.

La plupart des tâches qui impliquent la gestion d’une machine virtuelle dans un jeu d’échelle nécessite que vous connaissez l’ID de l’instance de l’ordinateur que vous souhaitez gérer. Vous pouvez utiliser [l’Explorateur de ressources Azure](https://resources.azure.com) pour rechercher l’ID d’instance d’un ordinateur virtuel dans un jeu d’échelle. Explorateur de ressources permet également de vérifier l’état des tâches que vous avez terminé.

Pour plus d’informations sur l’installation de la version la plus récente de PowerShell d’Azure, en sélectionnant votre abonnement et votre compte d’ouverture de session, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

## <a name="display-information-about-a-scale-set"></a>Afficher des informations sur un ensemble d’échelle

Vous pouvez obtenir des informations générales sur un ensemble d’échelle, qui est également désignée comme étant la vue d’instance. Ou bien, vous pouvez obtenir des informations plus spécifiques, telles que des informations sur les ressources dans l’ensemble de l’échelle.

Remplacez les valeurs entre guillemets avec le nom ou votre groupe de ressources et l’échelle définie et puis exécutez la commande :

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Il renvoie quelque chose comme ceci :

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded
    
Remplacez les valeurs entre guillemets par le nom de votre jeu de groupe et l’échelle des ressources. Remplacer *#* avec l’identificateur d’instance de l’ordinateur virtuel que vous souhaitez obtenir des informations sur et ensuite l’exécuter :

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Il renvoie quelque chose comme dans l’exemple suivant :

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded
        
## <a name="start-a-virtual-machine-in-a-scale-set"></a>Démarrer un ordinateur virtuel dans un ensemble d’échelle

Remplacez les valeurs entre guillemets par le nom de votre jeu de groupe et l’échelle des ressources. Remplacer *#* avec l’identificateur de l’ordinateur virtuel que vous souhaitez démarrer, puis exécutez-le :

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Dans l’Explorateur de ressources, nous pouvons voir que l’état de l’instance est **en cours d’exécution**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

Vous pouvez démarrer tous les ordinateurs virtuels dans l’échelle définie en n’utilisant ne pas le paramètre - InstanceId.
    
## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Arrêter un ordinateur virtuel dans un ensemble d’échelle

Remplacez les valeurs entre guillemets par le nom de votre jeu de groupe et l’échelle des ressources. Remplacer *#* avec l’identificateur de l’ordinateur virtuel que vous voulez arrêter, puis exécutez-le :

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Dans l’Explorateur de ressources, nous pouvons voir que l’état de l’instance est **libérée**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]
    
Pour arrêter une machine virtuelle et désallouer pas, utilisez le paramètre - StayProvisioned. Vous pouvez arrêter tous les ordinateurs virtuels dans le jeu en n’utilisant ne pas le paramètre - InstanceId.
    
## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Redémarrer une machine virtuelle dans un ensemble d’échelle

Remplacez les valeurs entre guillemets par le nom de votre groupe de ressources et de l’ensemble de l’échelle. Remplacer *#* avec l’identificateur de l’ordinateur virtuel que vous souhaitez redémarrer, puis exécutez-le :

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
Vous pouvez redémarrer tous les ordinateurs virtuels dans le jeu en n’utilisant ne pas le paramètre - InstanceId.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Supprimer un ordinateur virtuel à partir d’un ensemble d’échelle

Remplacez les valeurs entre guillemets par le nom de votre groupe de ressources et de l’ensemble de l’échelle. Remplacer *#* avec l’identificateur de l’ordinateur virtuel que vous souhaitez supprimer, puis exécutez-le :  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Vous pouvez supprimer l’ensemble d’échelle machine virtuelle à la fois en n’utilisant ne pas le paramètre - InstanceId.

## <a name="change-the-capacity-of-a-scale-set"></a>Modifier la capacité d’un ensemble d’échelle

Vous pouvez ajouter ou supprimer des ordinateurs virtuels par modification de la capacité de l’ensemble. Obtenir le jeu d’échelle que vous voulez modifier, définir la capacité de ce que vous souhaitez et mettez ensuite à jour l’échelle avec la nouvelle capacité. Dans ces commandes, remplacez les valeurs entre guillemets par le nom de votre groupe de ressources et de l’ensemble de l’échelle.

  $vmss = get-AzureRmVmss - ResourceGroupName « nom du groupe ressources » - VMScaleSetName « nom du jeu d’échelle » $vmss.sku.capacity = 5 ResourceGroupName - AzureRmVmss-mise à jour « ressource nom du groupe »-nom « échelle nom du jeu » VirtualMachineScaleSet - $vmss 

Si vous supprimez des ordinateurs virtuels à partir de l’ensemble de l’échelle, les ordinateurs virtuels avec les ID le plus élevés sont supprimées en premier.
