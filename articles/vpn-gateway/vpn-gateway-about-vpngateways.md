<properties 
   pageTitle="À propos de la passerelle VPN | Microsoft Azure"
   description="Obtenir des informations sur les connexions VPN passerelle pour les réseaux virtuels d’Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway"></a>À propos de la passerelle VPN


Une passerelle réseau virtuel est utilisée pour envoyer le trafic réseau entre les réseaux virtuels Azure et les emplacements locaux et également entre des réseaux virtuels dans Azure (VNet à VNet). Lorsque vous configurez une passerelle VPN, vous devez créer et configurer une passerelle de réseau virtuel et une connexion de passerelle réseau virtuel.

Dans le modèle de déploiement Gestionnaire de ressources, lorsque vous créez une ressource de passerelle réseau virtuel, vous spécifiez plusieurs paramètres. Un des paramètres requis est '-GatewayType'. Il existe deux types de passerelle réseau virtuel : Vpn et ExpressRoute. 

Lorsque le trafic réseau est envoyé sur une connexion privée dédiée, vous utilisez le type de passerelle 'ExpressRoute'. Cela est également appelé une passerelle ExpressRoute. Lorsque le trafic réseau est envoyé crypté via une connexion publique, vous utilisez le type de passerelle « Vpn ». Cela est appelé une passerelle VPN. Site à Site, Site-à-Point et VNet à VNet connexions utilisent une passerelle VPN.

Chaque réseau virtuel peut avoir qu’une seule passerelle réseau virtuel par type de passerelle. Par exemple, vous pouvez avoir passerelle d’un réseau virtuel qui utilise le GatewayType - ExpressRoute et une autre qui utilise GatewayType - Vpn. Cet article se concentre essentiellement sur la passerelle VPN. Pour plus d’informations sur ExpressRoute, consultez la [Vue d’ensemble technique de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Tarification

[AZURE.INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)] 


## <a name="gateway-skus"></a>Références SKU de passerelle

[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Pour plus d’informations sur les références SKU de passerelle, consultez [Références SKU de passerelle](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Estimation du débit total par point de stock

Le tableau suivant montre les types de la passerelle et le débit total estimé. Ce tableau s’applique aux modèles de déploiement classiques et le Gestionnaire de ressources.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="configuring-a-vpn-gateway"></a>Configurez une passerelle VPN

Lorsque vous configurez une passerelle VPN, les instructions que vous utilisez dépendent du modèle de déploiement que vous avez utilisé pour créer votre réseau virtuel. Par exemple, si vous avez créé votre VNet en utilisant le modèle de déploiement classique, vous utilisez les instructions pour le modèle de déploiement classiques et les instructions pour créer et configurer les paramètres de la passerelle VPN. Pour plus d’informations sur les modèles de déploiement, consultez [Présentation du Gestionnaire de ressources et de modèles de déploiement classiques](../resource-manager-deployment-model.md).

Une connexion de passerelle VPN s’appuie sur plusieurs ressources qui sont configurés avec des paramètres spécifiques. La plupart des ressources peut être configurée séparément, bien qu’ils doivent être configurés dans un certain ordre dans certains cas. Vous pouvez démarrer à la création et la configuration des ressources à l’aide d’un outil de configuration, tels que le portail Azure. Vous pouvez ensuite décidez ultérieurement de basculer vers un autre outil, tels que PowerShell, configurer des ressources supplémentaires ou pour modifier des ressources existantes, le cas échéant. Actuellement, vous ne pouvez pas configurer de chaque ressource et le paramètre de ressource dans le portail Azure. Les instructions fournies dans les articles pour chaque topologie de connexion spécifient lorsqu’un outil de configuration spécifique est nécessaire. Pour plus d’informations sur les ressources individuelles et les paramètres de la passerelle VPN, reportez-vous à la section [paramètres sur la passerelle de VPN](vpn-gateway-about-vpn-gateway-settings.md).

Les sections suivantes contiennent des tableaux qui répertorient :

- modèle de déploiement disponibles
- outils de configuration disponible
- liens directement à un article, s’il est disponible

Utilisez les diagrammes et les descriptions pour aider à sélectionner la topologie de connexion pour répondre à vos besoins. Les diagrammes affichent les topologies de base principal, mais il est possible de créer des configurations plus complexes à l’aide de diagrammes à titre indicatif.

## <a name="site-to-site-and-multi-site"></a>Site à Site et plusieurs sites

### <a name="site-to-site"></a>Site à Site

Une connexion de passerelle VPN de Site à Site (S2S) est une connexion via un tunnel VPN d’IPsec/IKE (IKEv1 ou IKEv2). Ce type de connexion requiert un périphérique VPN situé sur site qui a une adresse IP publique qui lui est assignée et qu’il ne se trouve pas derrière un NAT. Connexions de S2S peuvent être utilisées pour la coexistence et hybride configurations.   

![S2S connexion] (./media/vpn-gateway-about-vpngateways/demos2s.png "site à site")


### <a name="multi-site"></a>Plusieurs sites

Vous pouvez créer et configurer une connexion de passerelle VPN entre plusieurs réseaux locaux et de votre VNet. Lorsque vous travaillez avec plusieurs connexions, vous devez utiliser un type de RouteBased VPN (passerelle dynamique pour VNets classique). Un VNet ne peut avoir qu’une seule passerelle VPN, toutes les connexions via la passerelle partagent la bande passante disponible. Ceci est souvent appelé une connexion « multisite ».
 

![Connexion de plusieurs sites] (./media/vpn-gateway-about-vpngateways/demomulti.png "plusieurs sites")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modèles de déploiement et les méthodes de Site à Site et plusieurs sites

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## <a name="vnet-to-vnet"></a>VNet-à-VNet

Connexion d’un réseau virtuel à un autre réseau virtuel (VNet à VNet) est similaire à la connexion d’un VNet à un emplacement de site local. Les deux types de connectivité permet de fournir un tunnel sécurisé à l’aide de IPsec/IKE une passerelle VPN. Vous pouvez même combiner communication VNet-à-VNet avec les configurations de la connexion de plusieurs sites. Cela vous permet d’établir des topologies de réseau qui associent la connectivité de coexistence avec une connectivité réseau entre virtuel.

Le VNets que vous connectez peut être :

- dans les régions identiques ou différentes
- dans les abonnements identiques ou différents 
- dans les mêmes modèles de déploiement différentes


![VNet VNet connexion] (./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-à-vnet")

#### <a name="connections-between-deployment-models"></a>Connexions entre les modèles de déploiement

Azure dispose actuellement de deux modèles de déploiement : classique et Gestionnaire de ressources. Si vous avez utilisé Azure pendant un certain temps, vous avez probablement Azure VM et les rôles d’instance en cours d’exécution dans un VNet classique. Vos machines virtuelles plus récentes et les instances de rôle peuvent être exécuté dans un VNet créé dans le Gestionnaire de ressources. Vous pouvez créer une connexion entre le VNets pour permettre les ressources dans un VNet de communiquer directement avec les ressources d’un autre.

#### <a name="vnet-peering"></a>Homologation de VNet

Vous ne pourrez pas utiliser VNet homologation pour créer votre connexion, dans la mesure où votre réseau virtuel répond à certaines exigences. Homologation de VNet n’utilise pas une passerelle réseau virtuel. Pour plus d’informations, consultez [peering de VNet](../virtual-network/virtual-network-peering-overview.md).


### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modèles de déploiement et méthodes pour VNet-de-VNet

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## <a name="point-to-site"></a>Point-à-Site

Une connexion de passerelle VPN de Point-à-Site (P2S) vous permet de créer une connexion sécurisée à votre réseau virtuel à partir d’un ordinateur client spécifique. P2S est une connexion VPN sur SSTP (Secure Socket Tunneling Protocol). P2S les connexions ne nécessitent pas un périphérique VPN ou utiliser une adresse IP accessible au public. Vous établissez la connexion VPN en le démarrant à partir de l’ordinateur client. Cette solution est utile lorsque vous souhaitez vous connecter à vos VNet à partir d’un emplacement distant, par exemple depuis le domicile ou une conférence, ou lorsque vous n’avez que quelques clients qui doivent se connecter à un VNet. Les connexions de P2S peuvent être utilisées conjointement avec S2S les connexions via la passerelle VPN même, sous réserve que toutes les exigences de configuration pour les deux connexions sont compatibles.


Connexion de ![point-à-site] (./media/vpn-gateway-about-vpngateways/demop2s.png "point-à-site")

### <a name="deployment-models-and-methods-for-point-to-site"></a>Modèles de déploiement et les méthodes de Point-à-Site

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


## <a name="expressroute"></a>ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Dans une connexion ExpressRoute, une passerelle réseau virtuel est configurée avec le type de passerelle « ExpressRoute » plutôt que « Vpn ». Pour plus d’informations sur ExpressRoute, consultez la [vue d’ensemble technique de ExpressRoute](../expressroute/expressroute-introduction.md).


## <a name="site-to-site-and-expressroute-coexisting-connections"></a>Coexistence pour les connexions site à Site et ExpressRoute

ExpressRoute est une connexion directe, dédiée à partir de votre réseau étendu (et pas sur l’Internet public) aux Services de Microsoft, y compris d’Azure. Site à Site VPN le trafic transite crypté sur le réseau Internet public. La possibilité de configurer des connexions VPN de Site à Site et ExpressRoute pour le même réseau virtuel présente plusieurs avantages.

Vous pouvez configurer un VPN de Site à Site sous la forme d’un chemin d’accès de basculement sécurisé pour ExpressRoute, ou utiliser des connexions VPN de Site à Site pour se connecter à des sites qui ne sont pas partie de votre réseau, mais qui sont connectés par l’intermédiaire de ExpressRoute. Notez que cela nécessite deux passerelles de réseau virtuel pour le même réseau virtuel, un à l’aide de GatewayType - Vpn et l’autre à l’aide de GatewayType - ExpressRoute.


![Connexion de coexister] (./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")


### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Modèles de déploiement et les méthodes de S2S et de ExpressRoute

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## <a name="next-steps"></a>Étapes suivantes

Planifier la configuration de votre passerelle VPN. Reportez-vous à la section et la [conception et planification de passerelle VPN](vpn-gateway-plan-design.md) [connectant votre réseau local vers Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).








 
