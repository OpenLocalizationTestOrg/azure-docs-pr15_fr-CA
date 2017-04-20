<properties
   pageTitle="Déployer des machines virtuelles de carte d’interface réseau multiples à l’aide de PowerShell dans le Gestionnaire de ressources | Microsoft Azure"
   description="Apprenez à déployer des ordinateurs virtuels de carte d’interface réseau multiples à l’aide de PowerShell dans le Gestionnaire de ressources"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-using-powershell"></a>Déployer des machines virtuelles de carte d’interface réseau multiples à l’aide de PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Actuellement, vous ne peut pas avoir des ordinateurs virtuels avec une seule carte réseau et les ordinateurs virtuels avec plusieurs cartes réseau dans le même jeu de disponibilité. Par conséquent, vous devez mettre en œuvre les serveurs principaux dans un groupe de ressources différent de tous les autres composants. Les étapes ci-dessous utilisent un groupe de ressource nommé *IaaSStory* pour le groupe de ressources principal et *IaaSStory-back-end* pour les serveurs principaux.

## <a name="prerequisites"></a>Conditions préalables

Avant de déployer les serveurs principaux, vous devez déployer le groupe de ressources principal avec toutes les ressources nécessaires pour ce scénario. Pour déployer ces ressources, suivez les étapes ci-dessous.

1. Accédez à [la page de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Dans la page de modèle, à droite du **groupe de ressources Parent**, cliquez sur **déployer pour Azure**.
3. Si nécessaire, modifiez les valeurs de paramètre, puis suivez les étapes dans le portail d’aperçu Azure pour déployer le groupe de ressources.

> [AZURE.IMPORTANT] Assurez-vous que votre nom de compte de stockage est uniques. Impossible d’avoir des noms de compte en double stockage dans Azure.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Déployer l’ordinateurs virtuels back-end

Les ordinateurs virtuels du back-end dépendent de la création des ressources répertoriées ci-dessous.

- **Compte de stockage pour les disques de données**. Pour améliorer les performances, les disques de données sur les serveurs de base de données utilise la technologie lecteur (SSD) à l’état solide, qui requiert un compte de stockage premium. Assurez-vous que l’emplacement Azure que vous déployez pour prendre en charge le stockage de la prime.
- **Cartes d’interface réseau**. Chaque machine virtuelle possède deux cartes réseau, une pour l’accès de la base de données et l’autre pour la gestion.
- **Disponibilité définie**. Tous les serveurs de base de données seront ajoutés à la disponibilité d’une unique valeur, pour assurer au moins un des ordinateurs virtuels est et en cours d’exécution lors de la maintenance.  

### <a name="step-1---start-your-script"></a>Étape 1 : démarrer le script

Vous pouvez télécharger le script PowerShell complet utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-ps.ps1). Suivez les étapes ci-dessous pour modifier le script fonctionne dans votre environnement.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Modifier les valeurs des variables ci-dessous en fonction de votre groupe de ressources existant déployé ci-dessus, dans les [conditions préalables](#Prerequisites).

        $existingRGName        = "IaaSStory"
        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"
        $remoteAccessNSGName   = "NSG-RemoteAccess"
        $stdStorageAccountName = "wtestvnetstoragestd"

2. Modifier les valeurs des variables suivantes basées sur les valeurs que vous souhaitez utiliser pour votre déploiement de serveur principal.

        $backendRGName         = "IaaSStory-Backend"
        $prmStorageAccountName = "wtestvnetstorageprm"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $publisher             = "MicrosoftSQLServer"
        $offer                 = "SQL2014SP1-WS2012R2"
        $sku                   = "Standard"
        $version               = "latest"
        $vmNamePrefix          = "DB"
        $osDiskPrefix          = "osdiskdb"
        $dataDiskPrefix        = "datadisk"
        $diskSize              = "120"  
        $nicNamePrefix         = "NICDB"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2

3. Récupérer les ressources existantes, requis pour le déploiement.

        $vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
        $backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
        $remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
        $stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Étape 2 : créer les ressources nécessaires pour vos ordinateurs virtuels

Vous devez créer un nouveau groupe de ressources, un compte de stockage pour les disques de données et de disponibilité pour tous les ordinateurs virtuels. Vous aussi devez les informations d’identification du compte administrateur local de chaque ordinateur virtuel. Pour créer ces ressources, exécutez les étapes suivantes.

1. Créer un nouveau groupe de ressources.

        New-AzureRmResourceGroup -Name $backendRGName -Location $location

2. Créer un nouveau compte de stockage de prime dans le groupe de ressources créé ci-dessus.

        $prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
            -ResourceGroupName $backendRGName -Type Premium_LRS -Location $location

3. Créer un nouveau jeu de disponibilité.

        $avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location

4. Obtenir des informations d’identification de compte à utiliser pour chaque machine virtuelle l’administrateur local.

        $cred = Get-Credential -Message "Type the name and password for the local administrator account."

### <a name="step-3---create-the-nics-and-backend-vms"></a>Étape 3 : créer les cartes d’interface réseau et d’un back-end de machines virtuelles

Vous devez utiliser une boucle pour créer autant de machines virtuelles que vous souhaitez et créez les cartes réseau et les ordinateurs virtuels nécessaires au sein de la boucle. Pour créer les cartes réseau et les ordinateurs virtuels, exécutez les étapes suivantes.

1. Démarrer une `for` en boucle pour répéter les commandes pour créer un ordinateur virtuel et deux cartes réseau autant de fois que nécessaire, en fonction de la valeur de la `$numberOfVMs` variable.

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Créez la carte d’interface réseau utilisée pour l’accès de la base de données.

            $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
            $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
            $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
                -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1

3. Créez la carte d’interface réseau utilisée pour l’accès distant. Notez la façon dont cette carte réseau a une NSG associé à.

            $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
            $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
            $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
                -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
                -NetworkSecurityGroupId $remoteAccessNSG.Id

4. Créer `vmConfig` objet.

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id

5. Créer deux disques de données par machine virtuelle. Notez que les disques de données sont dans le compte de stockage de prime créé précédemment.

            $dataDisk1Name = $vmName + "-" + $osDiskPrefix + "-1"    
            $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
            Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
                -VhdUri $data1VhdUri -CreateOption empty -Lun 0

            $dataDisk2Name = $vmName + "-" + $dataDiskPrefix + "-2"    
            $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
            Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
                -VhdUri $data2VhdUri -CreateOption empty -Lun 1

6. Configurer le système d’exploitation et de l’image à utiliser pour la machine virtuelle.

            $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
            $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version

7. Ajouter les deux cartes réseau créés ci-dessus à la `vmConfig` objet.

            $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
            $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id

8. Créez le disque du système d’exploitation et la machine virtuelle. Avis du `}` fin du `for` boucle.

            $osDiskName = $vmName + "-" + $osDiskSuffix
            $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
            $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
            New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
        }

### <a name="step-4---run-the-script"></a>Étape 4 - exécution du script

Maintenant que vous avez téléchargé et modifié le script en fonction de vos besoins, exécutez il générer un script pour créer des ordinateurs virtuels de base de données le back-end avec plusieurs cartes réseau.

1. Enregistrez votre script et exécutez-le à partir de l’invite de commande **PowerShell** ou **PowerShell ISE**. Vous verrez la sortie initiale, comme illustré ci-dessous.

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/IaaSStory-Backend

2. Après quelques minutes, remplissez l’invite d’informations d’identification, puis cliquez sur **OK**. La sortie ci-dessous représente un ordinateur virtuel unique. Avis de l’ensemble du processus a 8 minutes pour terminer.

        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0


        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           },
                                           {
                                             "Lun": 1,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-2",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0


        EndTime             : 10/30/2015 9:30:03 AM -08:00
        Error               :
        Output              :
        StartTime           : 10/30/2015 9:22:54 AM -08:00
        Status              : Succeeded
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        StatusCode          : OK
