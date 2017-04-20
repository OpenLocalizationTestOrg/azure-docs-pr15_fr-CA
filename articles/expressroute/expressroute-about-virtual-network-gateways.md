<properties 
   pageTitle="Sur les passerelles de réseau virtuel ExpressRoute | Microsoft Azure"
   description="Obtenir des informations sur les passerelles de réseau virtuel pour ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="about-virtual-network-gateways-for-expressroute"></a>Sur les passerelles de réseau virtuel pour ExpressRoute


Une passerelle réseau virtuel est utilisée pour envoyer le trafic réseau entre les réseaux virtuels Azure et des emplacements sur le site. Lorsque vous configurez une connexion ExpressRoute, vous devez créer et configurer une passerelle de réseau virtuel et une connexion de passerelle réseau virtuel.

Lorsque vous créez une passerelle réseau virtuel, vous spécifiez plusieurs paramètres. Un des paramètres requis spécifie si la passerelle sera utilisée pour le trafic ExpressRoute ou VPN de Site à Site. Dans le modèle de déploiement Gestionnaire de ressources, le paramètre est «-GatewayType ».

Lorsque le trafic réseau est envoyé sur une connexion privée dédiée, vous utilisez le type de passerelle 'ExpressRoute'. Cela est également appelé une passerelle ExpressRoute. Lorsque le trafic réseau est envoyé crypté sur Internet, vous utilisez le type de passerelle « Vpn ». Cela est appelé une passerelle VPN. Site à Site, Site-à-Point et VNet à VNet connexions utilisent une passerelle VPN. 

Chaque réseau virtuel peut avoir qu’une seule passerelle réseau virtuel par type de passerelle. Par exemple, vous pouvez avoir passerelle d’un réseau virtuel qui utilise le GatewayType - Vpn et une autre qui utilise le GatewayType - ExpressRoute. Cet article se concentre sur la passerelle de réseau virtuel ExpressRoute.

## <a name="gwsku"></a>Références SKU de passerelle

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Si vous souhaitez mettre à niveau votre passerelle vers une passerelle plus puissante SKU, dans la plupart des cas, vous pouvez utiliser l’applet de commande PowerShell de « Resize-AzureRmVirtualNetworkGateway ». Cela fonctionnera pour les mises à niveau Standard et ultraperformante SKU. Toutefois, pour mettre à niveau le SKU UltraPerformance, vous devez recréer la passerelle.

###  <a name="aggthroughput"></a>Estimation du débit total par passerelle SKU


Le tableau suivant montre les types de la passerelle et le débit total estimé. Ce tableau s’applique aux modèles de déploiement classiques et le Gestionnaire de ressources.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)] 


## <a name="resources"></a>Les applets de commande PowerShell et les autres API

Pour les ressources techniques supplémentaires en matière de syntaxe spécifique lors de l’utilisation des API de repos et des applets de commande PowerShell pour les configurations de passerelle de réseau virtuel, consultez les pages suivantes :

|**Classique** | **Gestionnaire de ressources**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les configurations de connexion disponibles, reportez-vous à la section [Vue d’ensemble de ExpressRoute](expressroute-introduction.md) . 







 
