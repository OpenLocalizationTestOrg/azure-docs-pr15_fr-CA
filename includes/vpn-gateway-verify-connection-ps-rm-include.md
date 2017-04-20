Vous pouvez vérifier que votre connexion a réussi à l’aide de la `Get-AzureRmVirtualNetworkGatewayConnection` applet de commande, avec ou sans `-Debug`. 

1. Utilisez l’exemple d’applet de commande suivant, configuration des valeurs correspondant à votre propre. Si vous y êtes invité, sélectionnez 'A' pour exécuter 'All'. Dans l’exemple, `-Name` fait référence au nom de la connexion que vous avez créé et que vous souhaitez tester.

        Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. Une fois terminé, l’applet de commande permet d’afficher les valeurs. Dans l’exemple ci-dessous, l’état de la connexion indique « Connecté », et vous pouvez voir les octets entrant et sortant.

        Body:
        {
          "name": "MyGWConnection",
          "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
          "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
            "virtualNetworkGateway1": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
        teways/vnetgw1"
            },
            "localNetworkGateway2": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
        ways/LocalSite"
            },
            "connectionType": "IPsec",
            "routingWeight": 10,
            "sharedKey": "abc123",
            "connectionStatus": "Connected",
            "ingressBytesTransferred": 33509044,
            "egressBytesTransferred": 4142431
          }