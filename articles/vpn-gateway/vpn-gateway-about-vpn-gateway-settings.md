<properties 
   pageTitle="À propos des paramètres de passerelle VPN pour les passerelles réseau virtuel | Microsoft Azure"
   description="Obtenir des informations sur les paramètres de passerelle VPN de réseau virtuel d’Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway-settings"></a>À propos des paramètres de la passerelle VPN

Une solution de connexion VPN passerelle repose sur la configuration de plusieurs ressources afin d’envoyer du trafic réseau entre les réseaux virtuels et des emplacements sur le site. Chaque ressource contient des paramètres configurables. La combinaison des ressources et des paramètres détermine le résultat de la connexion.

Les sections de cet article traitent les ressources et les paramètres relatifs à une passerelle VPN dans le modèle de déploiement du **Gestionnaire de ressources** . Il peut s’avérer utile d’afficher les configurations disponibles à l’aide de diagrammes de topologie de connexion. Vous pouvez trouver les descriptions et les diagrammes de la topologie pour chaque solution de connexion dans l’article [Sur la passerelle de VPN](vpn-gateway-about-vpngateways.md) . 

## <a name="gwtype"></a>Types de passerelle

Chaque réseau virtuel ne peut avoir qu’une seule passerelle réseau virtuel de chaque type. Lorsque vous créez une passerelle réseau virtuel, vous devez vous assurer que le type de passerelle est correct pour votre configuration.

GatewayType - les valeurs disponibles sont : 

- VPN
- ExpressRoute

Une passerelle VPN requiert le `-GatewayType` *Vpn*.  

Exemple :

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased
 

## <a name="gwsku"></a>Références SKU de passerelle


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configuring-the-gateway-sku"></a>Configuration de la passerelle SKU

**Spécification de la référence SKU de la passerelle dans le portail Azure**

Si vous utilisez le portail Azure pour créer une passerelle de réseau virtuel du Gestionnaire de ressources, vous pouvez sélectionner la référence SKU de la passerelle à l’aide de la liste déroulante. Les options avec que vous sont présentées correspondent au type de passerelle et type VPN que vous sélectionnez.

Par exemple, si vous sélectionnez le type de passerelle « VPN » et du type « stratégie basées sur VPN », vous consultez uniquement la référence 'Base' car c’est le seul SKU disponible pour les réseaux privés virtuels de PolicyBased. Si vous sélectionnez 'Basée sur un itinéraire', vous pouvez sélectionner de Basic, Standard et ultraperformante SKU. 


**Spécification de la référence SKU de la passerelle à l’aide de PowerShell**


L’exemple suivant de PowerShell Spécifie le `-GatewaySku` comme *Standard*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

**Modification d’une référence SKU de la passerelle**

Si vous souhaitez mettre à niveau votre passerelle SKU vers une version plus puissante (Basic Standard à bien), vous pouvez utiliser la `Resize-AzureRmVirtualNetworkGateway` applet de commande PowerShell. Vous pouvez également configurer la passerelle taille de point de stock à l’aide de cette applet de commande.

L’exemple de PowerShell suivant montre une passerelle SKU redimensionnée à bien.

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>Estimation du type et le débit total par passerelle SKU

Le tableau suivant montre les types de la passerelle et le débit total estimé. Ce tableau s’applique aux modèles de déploiement classiques et le Gestionnaire de ressources.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 


## <a name="connectiontype"></a>Types de connexion

Dans le modèle de déploiement Gestionnaire de ressources, chaque configuration nécessite un type de connexion de passerelle réseau virtuel spécifique. Valeurs de PowerShell le Gestionnaire de ressources disponibles pour `-ConnectionType` sont :

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

Dans l’exemple suivant de PowerShell, nous créons une connexion S2S qui requiert le type de connexion *IPsec*.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Types VPN

Lorsque vous créez la passerelle réseau virtuel pour une configuration de passerelle VPN, vous devez spécifier un type VPN. Le type VPN que vous choisissez dépend de la topologie de connexion que vous souhaitez créer. Par exemple, une connexion P2S nécessite un type RouteBased VPN. Un type VPN peut également influer sur le matériel que vous utilisez. Les configurations de S2S nécessitent un périphérique VPN. Certains périphériques VPN prennent uniquement en charge un certain type VPN.

Le type VPN que vous sélectionnez doit satisfaire toutes les exigences de connexion de la solution à créer. Par exemple, si vous souhaitez créer une connexion de passerelle VPN de S2S et d’une connexion de passerelle VPN de P2S pour le même réseau virtuel, vous utiliseriez le type VPN *RouteBased* car P2S nécessite un type RouteBased VPN. Vous devez également vérifier que votre périphérique VPN pris en charge une connexion VPN de RouteBased. 

Une fois une passerelle réseau virtuel a été créée, vous ne pouvez pas modifier le type VPN. Vous devez supprimer la passerelle réseau virtuel et créer un nouveau. Il existe deux types VPN :

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


L’exemple suivant de PowerShell Spécifie le `-VpnType` en tant que *RouteBased*. Lorsque vous créez une passerelle, vous devez vous assurer que le VpnType - est correcte pour votre configuration. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Exigences de passerelle

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## <a name="gwsub"></a>Sous-réseau de passerelle

Pour configurer une passerelle de réseau virtuel, vous devez d’abord créer un sous-réseau de passerelle pour votre VNet. Le sous-réseau de passerelle doit être nommé *GatewaySubnet* pour fonctionner correctement. Ce nom permet de savoir que ce sous-réseau doit être utilisé pour la passerelle Azure.

La taille minimale de votre sous-réseau de la passerelle dépend entièrement de la configuration que vous souhaitez créer. Bien qu’il soit possible de créer un sous-réseau passerelle aussi petit que /29, nous vous recommandons de créer un sous-réseau de passerelle de /28 ou plus (/ 28, /27, /26, etc..). 

Création d’une plus grande taille de passerelle empêche l’exécution contre les limitations de taille de passerelle. Par exemple, vous peut-être ont créé une passerelle réseau virtuel avec une taille de sous-réseau passerelle /29 pour une connexion S2S. Vous souhaitez maintenant configurer un S2S/ExpressRoute coexister configuration. Cette configuration nécessite une taille minimale de passerelle sous-réseau /28. Pour créer votre configuration, vous devrez modifier le sous-réseau de passerelle pour s’adapter à la configuration minimale requise pour la connexion, qui est /28.

Le Gestionnaire de ressources PowerShell suivant montre un sous-réseau passerelle nommé GatewaySubnet. Vous pouvez voir que la notation CIDR spécifie un /27, qui permet de suffisamment d’adresses IP pour la plupart des configurations qui existent actuellement.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 


## <a name="lng"></a>Passerelles de réseau local

Lorsque vous créez une configuration de passerelle VPN, la passerelle du réseau local représente souvent votre magasin local. Dans le modèle de déploiement classique, la passerelle du réseau local a été appelée pour un Site Local. 

Vous nommez la passerelle du réseau local, l’adresse IP publique du périphérique VPN sur site et spécifiez les préfixes d’adresse qui sont trouvent dans l’emplacement local. Azure examine les préfixes d’adresse de destination pour le trafic réseau, consulte la configuration que vous avez spécifiée pour la passerelle de votre réseau local et achemine les paquets en conséquence. Vous spécifiez également des passerelles de réseau local de VNet-à-VNet les configurations qui utilisent une connexion de passerelle VPN.

L’exemple de PowerShell suivant crée une nouvelle passerelle du réseau local :

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Vous devez parfois modifier les paramètres de passerelle du réseau local. Par exemple, lorsque vous ajoutez ou modifiez la plage d’adresses, ou si l’adresse IP du périphérique VPN change. Pour un VNet classique, vous pouvez modifier ces paramètres dans le portail classique de la page réseaux locaux. Pour le Gestionnaire de ressources, reportez-vous à la section [paramètres de la passerelle réseau local modifier à l’aide de PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>Les applets de commande PowerShell et les autres API

Pour les ressources techniques supplémentaires en matière de syntaxe spécifique lors de l’utilisation des API de repos et des applets de commande PowerShell pour les configurations de passerelle VPN, consultez les pages suivantes :

|**Classique** | **Gestionnaire de ressources**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les configurations de connexion disponibles, reportez-vous à la section [Sur la passerelle de VPN](vpn-gateway-about-vpngateways.md) . 







 
