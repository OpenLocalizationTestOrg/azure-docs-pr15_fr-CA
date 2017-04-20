Pour modifier l’adresse IP de la passerelle, utilisez le `New-AzureRmVirtualNetworkGatewayConnection` applet de commande. Tant que vous conservez le nom de la passerelle du réseau local exactement la même que le nom existant, remplace les paramètres. À ce stade, l’applet de commande « Set » ne gère pas la modification de l’adresse IP de passerelle.

### <a name="gwipnoconnection"></a>Comment modifier l’adresse IP de passerelle - aucune connexion de passerelle

Pour mettre à jour l’adresse IP de passerelle de la passerelle du réseau local qui ne dispose pas encore une connexion, utilisez l’exemple ci-dessous. Vous pouvez également mettre à jour les préfixes d’adresse en même temps. Les paramètres que vous spécifiez remplace les paramètres existants. Veillez à utiliser le nom existant de la passerelle de votre réseau local. Si vous ne le faites pas, vous créerez une nouvelle passerelle du réseau local, ne pas remplacer l’existant.

Utilisez l’exemple suivant, en remplaçant les valeurs de votre propre.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Comment modifier l’adresse IP de passerelle - passerelle connexion existante

Si une passerelle connexion existe déjà, vous devez tout d’abord supprimer la connexion. Ensuite, vous pouvez modifier l’adresse IP de la passerelle et recréer une nouvelle connexion. Ainsi, un temps d’inactivité de votre connexion VPN.


>[AZURE.IMPORTANT] Ne supprimez pas la passerelle VPN. Si vous procédez ainsi, vous devrez passer en revue les étapes permettant de recréer, ainsi que de reconfigurer votre routeur local avec l’adresse IP qui sera affecté à la passerelle nouvellement créée.
 

1. Supprimer la connexion. Vous pouvez trouver le nom de votre connexion à l’aide de la `Get-AzureRmVirtualNetworkGatewayConnection` applet de commande.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName

2. Modifiez la valeur de GatewayIpAddress. Vous pouvez également modifier votre préfixes d’adresse pour l’instant, si nécessaire. Notez que cela remplacera les paramètres de passerelle réseau local existant. Utilisez le nom existant de votre passerelle de réseau local lors de la modification pour que les paramètres remplaceront. Si vous ne le faites pas, vous créerez une nouvelle passerelle du réseau local, ne modifiant ne pas l’existant.

        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName

3. Créer la connexion. Dans cet exemple, nous allons configurer un type de connexion IPsec. Lorsque vous recréez votre connexion, utilisez le type de connexion qui est spécifié pour votre configuration. Pour les types de connexion supplémentaires, consultez la page de [l’applet de commande PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Pour obtenir le nom de VirtualNetworkGateway, vous pouvez exécuter la `Get-AzureRmVirtualNetworkGateway` applet de commande.

    Définissez les variables :

        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName

    Créez la connexion :
    
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

