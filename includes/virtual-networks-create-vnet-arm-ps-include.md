## <a name="how-to-create-a-vnet-using-powershell"></a>Comment créer un VNet à l’aide de PowerShell
Pour créer un VNet à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Comment faire pour installer et configurer le PowerShell Azure](../articles/powershell-install-configure.md) et suivez les instructions jusqu'à la fin pour signer dans Azure et sélectionnez votre abonnement.
    
2. Si nécessaire, créez un nouveau groupe de ressources, comme indiqué ci-dessous. Pour notre scénario, créez un groupe de ressource nommé *TestRG*. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](../articles/resource-group-overview.md).

        New-AzureRmResourceGroup -Name TestRG -Location centralus

    Sortie attendue :
    
        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG   

3. Créer un nouveau VNet nommée *TestVNet*, comme illustré ci-dessous.

        New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
            -AddressPrefix 192.168.0.0/16 -Location centralus   
        
    Sortie attendue :

        Name                : TestVNet
        ResourceGroupName   : TestRG
        Location            : centralus
        Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState       : Succeeded
        Tags                    : 
        AddressSpace            : {
                                   "AddressPrefixes": [
                                     "192.168.0.0/16"
                                   ]
                                 }
        DhcpOptions             : {}
        Subnets                 : []
        VirtualNetworkPeerings  : []

4. Stocker l’objet de réseau virtuel dans une variable, comme indiqué ci-dessous.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    
    >[AZURE.TIP] Vous pouvez combiner les étapes 3 et 4 en exécutant **$vnet = New-AzureRmVirtualNetwork - ResourceGroupName TestRG-AddressPrefix - TestVNet de nom 192.168.0.0/16-centralus de l’emplacement**.

5. Ajouter un sous-réseau à la nouvelle variable VNet, comme illustré ci-dessous.

        Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
            -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
        
    Sortie attendue :

        Name                : TestVNet
        ResourceGroupName   : TestRG
        Location            : centralus
        Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState   : Succeeded
        Tags                :
        AddressSpace        : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions         : {}
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24"
                                  }
                                ]
        VirtualNetworkPeerings  : []

6. Répétez l’étape 5 ci-dessus pour chaque sous-réseau que vous souhaitez créer. La commande suivante crée le sous-réseau *back-end* pour notre scénario.

        Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
            -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

7. Bien que la création de sous-réseaux, ils n’existent actuellement dans la variable locale est utilisée pour récupérer le VNet que vous créez à l’étape 4 ci-dessus. Pour enregistrer les modifications dans Azure, exécutez l’applet de commande **Set-AzureRmVirtualNetwork** , comme illustré ci-dessous.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet 
        
    Sortie attendue :

        Name                : TestVNet
        ResourceGroupName   : TestRG
        Location            : centralus
        Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState   : Succeeded
        Tags                :
        AddressSpace        : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions         : {
                                  "DnsServers": []
                                }
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  },
                                  {
                                    "Name": "BackEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                    "AddressPrefix": "192.168.2.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  }
                                ]
        VirtualNetworkPeerings : []
