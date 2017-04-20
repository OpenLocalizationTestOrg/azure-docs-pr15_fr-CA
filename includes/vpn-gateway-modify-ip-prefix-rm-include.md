### <a name="noconnection"></a>Comment faire pour ajouter ou supprimer des préfixes - aucune connexion de passerelle

- **Pour ajouter** des préfixes d’adresses supplémentaires à un local réseau passerelle que vous avez créé, mais qui ne pas encore disposer d’une connexion de passerelle, utilisez l’exemple ci-dessous. Veillez à modifier les valeurs de votre propre.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- **Pour supprimer** un préfixe d’adresse d’une passerelle de réseau local qui n’a pas une connexion VPN, utilisez l’exemple ci-dessous. Abandonnez les préfixes que vous n’avez plus besoin. Dans cet exemple, nous devons préfixe n’est plus de 20.0.0.0/24 (à partir de l’exemple précédent), nous mettrons à jour local réseau passerelle et exclure ce préfixe.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Comment faire pour ajouter ou supprimer des préfixes - passerelle connexion existante

Si vous avez créé votre connexion passerelle et que vous souhaitez ajouter ou supprimer les préfixes d’adresse IP contenues dans la passerelle de votre réseau local, vous devez effectuer les étapes suivantes dans l’ordre. Ainsi, un temps d’inactivité de votre connexion VPN. Lors de la mise à jour de votre préfixes, vous allez tout d’abord supprimer la connexion, modifier les préfixes et puis créer une nouvelle connexion. Dans les exemples ci-dessous, veillez à modifier les valeurs de votre propre.

>[AZURE.IMPORTANT] Ne supprimez pas la passerelle VPN. Si vous procédez ainsi, vous devrez passer en revue les étapes permettant de recréer, ainsi que de reconfigurer votre routeur local avec les nouveaux paramètres.
 
1. Supprimer la connexion.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. Modifier les préfixes d’adresse de la passerelle du réseau local.

    Définissez la variable pour le LocalNetworkGateway.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

    Modifier les préfixes.

        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Créer la connexion. Dans cet exemple, nous allons configurer un type de connexion IPsec. Lorsque vous recréez votre connexion, utilisez le type de connexion qui est spécifié pour votre configuration. Pour les types de connexion supplémentaires, consultez la page de [l’applet de commande PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .

    Définissez la variable pour le VirtualNetworkGateway.

        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

    Créer la connexion. Notez que cet exemple utilise la variable $local que vous avez défini à l’étape précédente.


        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'
