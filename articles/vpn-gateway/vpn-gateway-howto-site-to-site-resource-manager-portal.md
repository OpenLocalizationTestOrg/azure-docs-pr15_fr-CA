<properties
   pageTitle="Créer un réseau virtuel doté d’une connexion VPN de Site à Site à l’aide du Gestionnaire de ressources Azure et le portail Azure | Microsoft Azure"
   description="Comment créer le VNet en utilisant le modèle de déploiement du Gestionnaire de ressources et le connecter à votre revendeur local sur site à l’aide d’une connexion de passerelle VPN de S2S de réseau."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Créer un VNet avec une connexion de Site à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Responsable de ressources - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Standard - portail classique](vpn-gateway-site-to-site-create.md)


Cet article vous guide dans le processus de création d’un réseau virtuel et une connexion de passerelle VPN de Site à Site sur votre réseau local en utilisant le modèle de déploiement du Gestionnaire de ressources Azure et le portail Azure. Les connexions de site à Site peuvent être utilisées pour la coexistence et hybride configurations.

![Diagramme](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modèles de déploiement et les méthodes pour les connexions de Site à Site

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant montre les modèles de déploiement actuellement disponibles et les méthodes pour les configurations de Site à Site. Lorsqu’un article avec des étapes de configuration n’est disponible, nous attacher directement à partir de cette table.

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurations supplémentaires 

Si vous souhaitez connecter entre eux les VNets, mais que vous ne créez pas d’une connexion à un emplacement local, voir la rubrique [Configure une connexion VNet à VNet](vpn-gateway-vnet-vnet-rm-ps.md). Si vous souhaitez ajouter une connexion de Site à Site à un VNet qui dispose déjà d’une connexion, consultez [Ajouter une connexion de S2S d’un VNet avec une connexion de passerelle VPN existante](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments suivants avant de commencer votre configuration :

- Un périphérique VPN compatible et une personne est en mesure de le configurer. [Sur les périphériques VPN](vpn-gateway-about-vpn-devices.md), reportez-vous à la section. Si vous n’êtes pas familiarisé avec la configuration de votre périphérique VPN, ou que vous ne connaissez pas l’adresse IP des plages qui se trouvent dans vos locaux sur configuration du réseau, vous devez coordonner avec une personne qui pourra vous fournir ces informations.

- Adresse IP publique tourné vers l’extérieur pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
    
- Un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe d’un [compte gratuit](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Exemples de valeurs de configuration pour cet exercice


Lorsque vous utilisez ces étapes en guise d’exercice, vous pouvez utiliser les valeurs de configuration d’exemple :

- **VNet nom :** TestVNet1
- **L’espace d’adressage :** 10.11.0.0/16 et 10.12.0.0/16
- **Sous-réseaux :**
    - Site Web frontal : 10.11.0.0/24
    - Back-end : 10.12.0.0/24
    - GatewaySubnet : 10.12.255.0/27
- **Groupe de ressources :** TestRG1
- **Emplacement :** Les États-Unis
- **Serveur DNS :** 8.8.8.8
- **Nom de passerelle :** VNet1GW
- **Adresse IP publique :** VNet1GWIP
- **Type de VPN :** Basée sur un itinéraire
- **Type de connexion :** Site à site (IPsec)
- **Type de passerelle :** VPN
- **Nom de la passerelle réseau local :** Site2
- **Nom de connexion :** VNet1toSite2


## <a name="CreatVNet"></a>1. Créez un réseau virtuel 

Si vous avez déjà un VNet, vérifiez que les paramètres sont compatibles avec votre conception de passerelle VPN. Une attention particulière pour les sous-réseaux peuvent se superposer avec d’autres réseaux. Si vous disposez de chevauchement des sous-réseaux, votre connexion ne fonctionne pas correctement. Si votre VNet est configuré avec les paramètres corrects, vous pouvez commencer la procédure décrite dans la section [spécifier un serveur DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Pour créer un réseau virtuel

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. ajouter des sous-réseaux et l’espace d’adresses supplémentaires

Vous pouvez ajouter des sous-réseaux et espace d’adressage supplémentaires à votre VNet une fois qu’il a été créé.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3. Spécifiez un serveur DNS

### <a name="to-specify-a-dns-server"></a>Pour spécifier un serveur DNS

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>4. création d’un sous-réseau de passerelle

Avant de vous connecter à votre réseau virtuel à une passerelle, vous devez d’abord créer le sous-réseau de passerelle pour le réseau virtuel auquel vous voulez vous connecter. Dans la mesure du possible, il est préférable de créer un sous-réseau de passerelle à l’aide d’un bloc CIDR de /28 ou de /27 afin de fournir suffisamment d’adresses IP pour prendre en charge les besoins supplémentaires de configuration futures.

Si vous créez cette configuration en guise d’exercice, consultez ces [valeurs](#values) lors de la création de votre sous-réseau de passerelle.

### <a name="to-create-a-gateway-subnet"></a>Pour créer un sous-réseau de passerelle


[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. créer une passerelle réseau virtuel

Si vous créez cette configuration en guise d’exercice, vous pouvez faire référence aux [valeurs de configuration d’exemple](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Pour créer une passerelle réseau virtuel

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>6. création d’une passerelle de réseau local

La passerelle « réseau local » fait référence à votre magasin local. Nommez la passerelle du réseau local par lequel Azure peut faire. 

Si vous créez cette configuration en guise d’exercice, vous pouvez faire référence aux [valeurs de configuration d’exemple](#values).

### <a name="to-create-a-local-network-gateway"></a>Pour créer une passerelle de réseau local

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="VPNDevice"></a>7. Configurez votre périphérique VPN

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. créer une connexion VPN de Site à Site

Créer la connexion VPN de Site à Site entre votre passerelle de réseau virtuel et sur l’appareil VPN. Veillez à remplacer les valeurs de votre propre. La clé partagée doit correspondre à la valeur que vous avez utilisé pour votre configuration de périphérique VPN. 

Avant de commencer cette section, vérifiez que votre passerelle réseau virtuel et des passerelles de réseau local terminée. Si vous créez cette configuration en guise d’exercice, consultez ces [valeurs](#values) lors de la création de votre connexion.

### <a name="to-create-the-vpn-connection"></a>Pour créer la connexion VPN


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="VerifyConnection"></a>9. Vérifiez la connexion VPN

Vous pouvez vérifier votre connexion VPN dans le portail, soit à l’aide de PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes

- Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à des réseaux virtuels. Voir les ordinateurs virtuels [cursus](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) pour plus d’informations.

- Pour plus d’informations sur BGP, consultez la [Vue d’ensemble du protocole BGP](vpn-gateway-bgp-overview.md) et [comment configurer le protocole BGP](vpn-gateway-bgp-resource-manager-ps.md).
