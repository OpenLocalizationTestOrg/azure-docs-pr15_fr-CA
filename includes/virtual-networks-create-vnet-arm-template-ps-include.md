## <a name="deploy-the-arm-template-by-using-powershell"></a>Déployer le modèle ARM à l’aide de PowerShell

Pour déployer le modèle ARM que vous avez téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Comment faire pour installer et configurer le PowerShell Azure](../articles/powershell-install-configure.md) et suivez les instructions jusqu'à la fin pour signer dans Azure et sélectionnez votre abonnement.

3. Si nécessaire, exécutez les **`New-AzureRmResourceGroup`** applet de commande pour créer un nouveau groupe de ressources. La commande suivante crée un groupe de ressources nommé *TestRG* dans la zone *Centrale des Etats-Unis* azure. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](../articles/resource-group-overview.md).

        New-AzureRmResourceGroup -Name TestRG -Location centralus
        
    Voici la sortie attendue de la commande ci-dessus :

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

4. Exécuter le **`New-AzureRmResourceGroupDeployment`** applet de commande pour déployer la nouvelle VNet à l’aide du modèle et le paramètre de fichiers téléchargés et de modifier les propriétés.

        New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
            -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
            
    Voici la sortie attendue de la commande ci-dessus :
        
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : 8/14/2015 9:40:00 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
        
        Outputs           :

5. Exécuter le **`Get-AzureRmVirtualNetwork`** applet de commande pour afficher les propriétés de la nouvelle VNet, comme illustré ci-dessous.


        Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
        
    Voici la sortie attendue de la commande ci-dessus :
        
        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]