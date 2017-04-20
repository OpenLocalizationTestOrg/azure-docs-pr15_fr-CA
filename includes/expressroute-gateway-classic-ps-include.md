Vous devez créer tout d’abord, un VNet et un sous-réseau de la passerelle avant d’effectuer les tâches suivantes. Voir l’article [configurer un réseau virtuel à l’aide du portail classique](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) pour plus d’informations.   

## <a name="add-a-gateway"></a>Ajouter une passerelle

La commande ci-dessous permet de créer une passerelle. N’oubliez pas de remplacer toutes les valeurs de votre propre.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Vérifier que la passerelle a été créée.

Utilisez la commande suivante pour vérifier que la passerelle a été créée. Cette commande récupère également l’ID de passerelle, ce dont vous avez besoin pour d’autres opérations.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Redimensionner une passerelle

Il existe un certain nombre de [Références SKU de passerelle](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Vous pouvez utiliser la commande suivante pour modifier la référence SKU de la passerelle à tout moment.

>[AZURE.IMPORTANT] Cette commande ne fonctionne pas pour la passerelle des UltraPerformance. Pour modifier votre passerelle vers une passerelle UltraPerformance, supprimez d’abord la passerelle ExpressRoute existante et puis créer une nouvelle passerelle UltraPerformance. Pour déclasser votre passerelle d’une passerelle UltraPerformance, supprimez d’abord la passerelle UltraPerformance et puis créer une nouvelle passerelle. 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Supprimer une passerelle

Utilisez la commande ci-dessous pour supprimer une passerelle

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>