
Pour configurer votre périphérique VPN, vous devez l’adresse IP publique de la passerelle réseau virtuel pour configurer votre périphérique VPN sur site. Travailler avec le fabricant de votre périphérique pour les informations de configuration spécifiques et de configurer votre périphérique. Voir les [Périphériques VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) pour plus d’informations sur les périphériques VPN qui fonctionnent bien avec Azure.

Pour rechercher l’adresse IP publique de votre passerelle de réseau virtuel à l’aide de PowerShell, utiliser l’exemple suivant :

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

Vous pouvez également afficher l’adresse IP publique de votre passerelle de réseau virtuel à l’aide du portail Azure. Accédez à des **passerelles de réseau virtuel**, puis cliquez sur le nom de votre passerelle.