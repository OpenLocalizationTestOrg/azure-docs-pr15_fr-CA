<properties 
   pageTitle="Comment définir une adresse IP privée à dans le Gestionnaire de ressources Azure à l’aide de PowerShell | Microsoft Azure"
   description="Présentation statique des adresses IP privées et comment les gérer dans le Gestionnaire de ressources Azure à l’aide de PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-in-resource-manager-by-using-powershell"></a>Comment faire pour définir une adresse IP privée à dans le Gestionnaire de ressources à l’aide de PowerShell

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement classique](virtual-networks-static-private-ip-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

L’exemple PowerShell commandes ci-dessous s’attendent à un environnement simple déjà créé en fonction du scénario ci-dessus. Si vous souhaitez exécuter les commandes tels qu’ils sont affichés dans ce document, d’abord créer l’environnement de test décrit dans [Création d’un vnet](virtual-networks-create-vnet-arm-ps.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Comment spécifier une adresse IP privée lors de la création d’une machine virtuelle
Pour créer un ordinateur virtuel nommé *DNS01* dans le sous-réseau *frontal* d’un VNet nommé *TestVNet* avec un IP statique privée de *192.168.1.101*, suivez les étapes ci-dessous :

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Permet de définir des variables pour le compte de stockage, emplacement, groupe de ressources et informations d’identification à utiliser. Vous devez entrer un nom d’utilisateur et le mot de passe pour l’ordinateur virtuel. Le groupe de comptes et de ressources de stockage doit déjà exister.

        $stName = "vnetstorage"
        $locName = "Central US"
        $rgName = "TestRG"
        $cred = Get-Credential -Message "Type the name and password of the local administrator account."

3. Récupérer le réseau virtuel et le sous-réseau que vous souhaitez créer la machine virtuelle dans.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet  
        $subnet = $vnet.Subnets[0].Id

4. Si nécessaire, créez une adresse IP publique pour accéder à la machine virtuelle à partir d’Internet.

        $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

5. Créer une carte d’interface réseau à l’aide de l’adresse IP privée statique que vous souhaitez affecter à la machine virtuelle. Vérifiez l’adresse IP de la plage de sous-réseau que vous ajoutez la machine virtuelle pour. Il s’agit de l’étape principale pour cet article, où vous avez défini l’IP privée statique.

        $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.1.101

6. Créer la machine virtuelle à l’aide de la carte d’interface réseau créé ci-dessus.

        $vm = New-AzureRmVMConfig -VMName DNS01 -VMSize "Standard_A1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName DNS01  -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm 

    Sortie attendue :

        EndTime             : 9/8/2015 2:32:09 PM -07:00
        Error               : 
        Output              : 
        StartTime           : 9/8/2015 2:27:42 PM -07:00
        Status              : Succeeded
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        StatusCode          : OK 


## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Comment faire pour extraire des informations statiques d’adresse IP privées pour un ordinateur virtuel
Pour afficher les informations d’adresse IP privées statiques pour la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell suivante et observer les valeurs de *PrivateIpAddress* et de *PrivateIpAllocationMethod*:

    Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG

Sortie attendue :

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
                           stNIC
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
                           ines/DNS01"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
                           terfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Static",
                               "Subnet": {
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtual
                           Networks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicI
                           PAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Comment faire pour supprimer une adresse IP privée statique à partir d’un ordinateur virtuel
Pour supprimer l’adresse IP privée statique ajouté à la machine virtuelle dans le script ci-dessus, exécutez les commandes PowerShell suivantes :
    
    $nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
    $nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
    Set-AzureRmNetworkInterface -NetworkInterface $nic

Sortie attendue :

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
                           stNIC
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
                           ines/WindowsVM"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
                           terfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Dynamic",
                               "Subnet": {
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtual
                           Networks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicI
                           PAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Comment faire pour ajouter une adresse IP privée pour un ordinateur virtuel existant
Pour ajouter un mappage statique privée adresse IP de la machine virtuelle créée à l’aide de ci-dessus, exécutez le script de commande suivante :

    $nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
    $nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
    $nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
    Set-AzureRmNetworkInterface -NetworkInterface $nic

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les adresses [IP publique réservées](virtual-networks-reserved-public-ip.md) .
- Obtenir des informations sur les adresses [IP publiques (ILPIP) au niveau de l’instance](virtual-networks-instance-level-public-ip.md) .
- Consultez les [API de reste IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).