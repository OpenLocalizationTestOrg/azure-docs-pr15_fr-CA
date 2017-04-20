<properties 
   pageTitle="Déployer un ordinateur virtuel avec une adresse IP statique publique dans le Gestionnaire de ressources à l’aide de PowerShell | Microsoft Azure"
   description="Apprenez à déployer des ordinateurs virtuels avec une adresse IP statique publique dans le Gestionnaire de ressources à l’aide de PowerShell"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-powershell"></a>Déployer un ordinateur virtuel avec une adresse IP statique publique à l’aide de PowerShell

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="step-1---start-your-script"></a>Étape 1 : démarrer le script

Vous pouvez télécharger le script PowerShell complet utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Suivez les étapes ci-dessous pour modifier le script fonctionne dans votre environnement.

1. Modifier les valeurs des variables suivantes basées sur les valeurs que vous souhaitez utiliser pour votre déploiement. Les valeurs inférieures à mapper sur le scénario utilisé dans ce document.

        # Set variables resource group
        $rgName                = "IaaSStory"
        $location              = "West US"
        
        # Set variables for VNet
        $vnetName              = "WTestVNet"
        $vnetPrefix            = "192.168.0.0/16"
        $subnetName            = "FrontEnd"
        $subnetPrefix          = "192.168.1.0/24"
        
        # Set variables for storage
        $stdStorageAccountName = "iaasstorystorage"
        
        # Set variables for VM
        $vmSize                = "Standard_A1"
        $diskSize              = 127
        $publisher             = "MicrosoftWindowsServer"
        $offer                 = "WindowsServer"
        $sku                   = "2012-R2-Datacenter"
        $version               = "latest"
        $vmName                = "WEB1"
        $osDiskName            = "osdisk"
        $nicName               = "NICWEB1"
        $privateIPAddress      = "192.168.1.101"
        $pipName               = "PIPWEB1"
        $dnsName               = "iaasstoryws1"

## <a name="step-2---create-the-necessary-resources-for-your-vm"></a>Étape 2 : créer les ressources nécessaires pour votre machine virtuelle

Avant de créer un ordinateur virtuel, vous avez besoin d’un groupe de ressources, VNet, adresse IP publique et carte d’interface réseau à utiliser par la machine virtuelle.

1. Créer un nouveau groupe de ressources.

        New-AzureRmResourceGroup -Name $rgName -Location $location
        
2. Créer le VNet et le sous-réseau.

        $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
            -AddressPrefix $vnetPrefix -Location $location   
        
        Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
            -VirtualNetwork $vnet -AddressPrefix $subnetPrefix
        
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet 

3. Créez la ressource IP publique. 

        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
            -AllocationMethod Static -DomainNameLabel $dnsName -Location $location

4. Créer l’interface réseau (NIC) pour la machine virtuelle dans le sous-réseau créé ci-dessus, avec l’adresse IP publique. Notez la première applet de commande récupérant la VNet Azure, cela est nécessaire dans la mesure où un **Jeu-AzureRmVirtualNetwork** a été exécuté pour modifier la VNet existante.

        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
            -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
            -PublicIpAddress $pip

5. Créer un compte de stockage pour héberger le lecteur du système d’exploitation de la machine virtuelle.

        $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
            -ResourceGroupName $rgName -Type Standard_LRS -Location $location

## <a name="step-3---create-the-vm"></a>Étape 3 : créer la machine virtuelle 

Maintenant que toutes les ressources nécessaires sont en place, vous pouvez créer une nouvelle machine virtuelle.

1. Créez l’objet de configuration de la machine virtuelle.

        $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize 

1. Obtenir des informations d’identification pour le compte d’administrateur local de machine virtuelle.

        $cred = Get-Credential -Message "Type the name and password for the local administrator account."

2. Créez un objet de configuration de machine virtuelle.

        $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
            -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

3. Définir l’image de système d’exploitation de la machine virtuelle.

        $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
            -Offer $offer -Skus $sku -Version $version

4. Configurez le disque du système d’exploitation.

        $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
        $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage

5. Ajoutez la carte d’interface réseau de la machine virtuelle.

        $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary

6. Créer la machine virtuelle.

        New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location

7. Enregistrez le fichier de script.

## <a name="step-4---run-the-script"></a>Étape 4 - exécution du script

Après l’apport des modifications requises et comprendre le script afficher au-dessus, exécutez le script. 

1. À partir d’une console PowerShell ou PowerShell ISE, exécutez le script ci-dessus.
2. La sortie ci-dessous doit s’afficher après quelques minutes.

        ResourceGroupName : IaaSStory
        Location          : westus
        ProvisioningState : Succeeded
        Tags              : 
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory
                
        AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
        DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
        Subnets           : {FrontEnd}
        ProvisioningState : Succeeded
        AddressSpaceText  : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptionsText   : {}
        SubnetsText       : [
                              {
                                "Name": "FrontEnd",
                                "AddressPrefix": "192.168.1.0/24"
                              }
                            ]
        ResourceGroupName : IaaSStory
        Location          : westus
        ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Tag               : {}
        TagsTable         : 
        Name              : WTestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
        
        AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
        DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
        Subnets           : {FrontEnd}
        ProvisioningState : Succeeded
        AddressSpaceText  : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptionsText   : {
                              "DnsServers": []
                            }
        SubnetsText       : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "ProvisioningState": "Succeeded"
                              }
                            ]
        ResourceGroupName : IaaSStory
        Location          : westus
        ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Tag               : {}
        TagsTable         : 
        Name              : WTestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
                
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Status              : Succeeded
        StatusCode          : OK
        Output              : 
        StartTime           : 1/12/2016 12:57:56 PM -08:00
        EndTime             : 1/12/2016 12:59:13 PM -08:00
        Error               : 
        ErrorText           : 

   