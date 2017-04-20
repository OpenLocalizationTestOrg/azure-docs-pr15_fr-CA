<properties
   pageTitle="Créer un réseau virtuel doté d’une connexion VPN de Site à Site à l’aide du Gestionnaire de ressources Azure et PowerShell | Microsoft Azure"
   description="Cet article vous guide dans la création d’un VNet en utilisant le modèle de déploiement du Gestionnaire de ressources et de se connecter à votre réseau local sur site à l’aide d’une connexion de passerelle VPN de S2S."
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

# <a name="create-a-vnet-with-a-site-to-site-connection-using-powershell"></a>Créer un VNet avec une connexion de Site à Site à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Responsable de ressources - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Standard - portail classique](vpn-gateway-site-to-site-create.md)

Cet article vous guide dans le processus de création d’un réseau virtuel et une connexion de passerelle VPN de Site à Site sur votre réseau local en utilisant le modèle de déploiement du Gestionnaire de ressources Azure. Les connexions de site à Site peuvent être utilisées pour la coexistence et hybride configurations.

![Diagramme de site à Site] (./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "site à site") 


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modèles de déploiement et les méthodes pour les connexions de Site à Site

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant montre les modèles de déploiement actuellement disponibles et les méthodes pour les configurations de Site à Site. Lorsqu’un article avec des étapes de configuration n’est disponible, nous attacher directement à partir de cette table. 

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurations supplémentaires

Si vous souhaitez connecter entre eux les VNets, mais que vous ne créez pas d’une connexion à un emplacement local, voir la rubrique [Configure une connexion VNet à VNet](vpn-gateway-vnet-vnet-rm-ps.md). Si vous souhaitez ajouter une connexion de Site à Site à un VNet qui dispose déjà d’une connexion, consultez [Ajouter une connexion de S2S d’un VNet avec une connexion de passerelle VPN existante](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments suivants avant de commencer la configuration.

- Un périphérique VPN compatible et une personne est en mesure de le configurer. [Sur les périphériques VPN](vpn-gateway-about-vpn-devices.md), reportez-vous à la section. Si vous n’êtes pas familiarisé avec la configuration de votre périphérique VPN, ou que vous ne connaissez pas l’adresse IP des plages qui se trouvent dans vos locaux sur configuration du réseau, vous devez coordonner avec une personne qui pourra vous fournir ces informations.

- Adresse IP publique tourné vers l’extérieur pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
    
- Un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
    
- La version la plus récente de cmdlets PowerShell du Gestionnaire de ressources Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .


## <a name="Login"></a>1. Connectez-vous à votre abonnement 

Vérifiez que vous passez en mode de PowerShell pour utiliser les applets de commande du Gestionnaire de ressources. Pour plus d’informations, reportez-vous [à l’aide de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

Ouvrez la console PowerShell et vous connecter à votre compte. Pour vous aider à vous connecter, utilisez l’exemple suivant :

    Login-AzureRmAccount

Vérifiez les abonnements pour le compte.

    Get-AzureRmSubscription 

Spécifier l’abonnement que vous souhaitez utiliser.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="VNet"></a>2. Créez un réseau virtuel et un sous-réseau de passerelle

Les exemples utilisent un sous-réseau de passerelle de /28. S’il est possible de créer un sous-réseau passerelle aussi petit que /29, nous vous conseillons de créer un sous-réseau plus grand qui inclut plusieurs adresses en sélectionnant au moins /28 ou /27. Ainsi, pour un nombre d’adresses suffisant pour s’adapter à des configurations supplémentaires possibles que vous pouvez par la suite.

Si vous disposez déjà d’un réseau virtuel à un sous-réseau de passerelle est 29 ou plus, vous pouvez passer pour [Ajouter la passerelle de votre réseau local](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Pour créer un réseau virtuel et un sous-réseau de passerelle

L’exemple suivant permet de créer un réseau virtuel et un sous-réseau de la passerelle. Remplacez les valeurs pour votre propre. 

Tout d’abord, créez un groupe de ressources :
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Ensuite, créez votre réseau virtuel. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas un des espaces d’adressage que vous avez sur votre réseau local.

L’exemple suivant crée un réseau virtuel appelé *testvnet* et deux sous-réseaux, appelé un *GatewaySubnet* et l’autre appelé *Subnet1*. Il est important de créer un sous-réseau nommé spécifiquement *GatewaySubnet*. Si vous lui quelque chose d’autre, la configuration de votre connexion échouera. 

Définissez les variables.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Créer le VNet.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Pour ajouter un sous-réseau de passerelle à un réseau virtuel que vous avez déjà créé

Cette étape est requise uniquement si vous devez ajouter un sous-réseau de passerelle à un VNet que vous avez créé précédemment.

Vous pouvez créer votre sous-réseau de passerelle à l’aide de l’exemple suivant. Veillez à nommer le sous-réseau passerelle 'GatewaySubnet'. Si vous lui quelque chose d’autre, vous créez un sous-réseau, mais Azure ne traite comme un sous-réseau de la passerelle.

Définissez les variables.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Créer le sous-réseau de passerelle.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Définir la configuration. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"> </a>ajouter votre passerelle du réseau local

Dans un réseau virtuel, la passerelle du réseau local fait généralement référence à l’emplacement local. Vous nommez le site par lequel Azure peut faire et également spécifier le préfixe d’espace d’adresse de la passerelle du réseau local. 

Azure utilise le préfixe d’adresse IP spécifié pour identifier le trafic à envoyer à votre magasin local. Cela signifie que vous devez spécifier chaque préfixe d’adresse que vous souhaitez associer à la passerelle de votre réseau local. Vous pouvez facilement mettre à jour ces préfixes si votre réseau local change. 

Lorsque vous utilisez les exemples de PowerShell, notez les points suivants :
    
- Le *GatewayIPAddress* est l’adresse IP de votre périphérique VPN sur site. Votre périphérique VPN ne peut pas se trouver derrière un NAT. 
- *AddressPrefix* est votre espace d’adressage sur site.

Pour ajouter une passerelle réseau local avec un préfixe d’adresse unique :

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Pour ajouter une passerelle réseau local avec plusieurs préfixes d’adresse :

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Pour modifier des préfixes d’adresse IP de la passerelle du réseau local

Les préfixes de passerelle de votre réseau local changent parfois. Les étapes à que suivre pour modifier votre préfixes d’adresses IP dépendant de si vous avez créé une connexion de passerelle VPN. Reportez-vous à la section de [préfixes d’adresses IP de modifier une passerelle de réseau local](#modify) de cet article.


## <a name="PublicIP"></a>4. demande une adresse IP publique pour la passerelle VPN

Ensuite, demandez une adresse IP publique à allouer à la passerelle VPN de VNet d’Azure. Il ne s’agit pas de la même adresse IP qui est assignée à votre périphérique VPN ; au lieu de cela, il est affecté à la passerelle VPN d’Azure lui-même. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. Elle est affectée dynamiquement à votre passerelle. Vous utilisez cette adresse IP lors de la configuration de votre périphérique VPN local pour se connecter à la passerelle.

La passerelle VPN d’Azure pour le modèle de déploiement du Gestionnaire de ressources actuellement prend uniquement en charge les adresses IP publiques à l’aide de la méthode de ventilation dynamique. Toutefois, cela ne signifie pas que l’adresse IP change. Le seul moment où les modifications d’adresses IP VPN d’Azure passerelle est lorsque la passerelle est supprimée et recréée. Ne modifie pas l’adresse IP publique de passerelle sur le redimensionnement, la réinitialisation ou autres maintenance/mises à niveau internes de la passerelle VPN d’Azure.

Utilisez l’exemple PowerShell suivant :

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="GatewayIPConfig"></a>5. créer la configuration d’adressage IP gateway

La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. L’exemple suivant permet de créer la configuration de votre passerelle.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## <a name="CreateGateway"></a>6. création de la passerelle réseau virtuel

Dans cette étape, vous créez la passerelle réseau virtuel. Création d’une passerelle peut prendre du temps pour terminer. Souvent de 45 minutes ou plus. 

Utilisez les valeurs suivantes :

- Le *GatewayType -* pour une configuration de Site à Site est *Vpn*. Le type de passerelle est toujours spécifique à la configuration que vous implémentez. Par exemple, les autres configurations de passerelle peuvent nécessiter - GatewayType ExpressRoute. 

- Le *-VpnType* peut être *RouteBased* (également appelé une passerelle dynamique dans la documentation) ou *PolicyBased* (également appelé une passerelle statique dans la documentation). Pour plus d’informations sur les types de passerelle VPN, reportez-vous à la section [Sur les passerelles VPN](vpn-gateway-about-vpngateways.md#vpntype).
- Le *GatewaySku -* peut être *basique*, *Standard*ou *bien*.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="ConfigureVPNDevice"></a>7. Configurez votre périphérique VPN

À ce stade, vous devez l’adresse IP publique de la passerelle réseau virtuel pour configurer votre périphérique VPN sur site. Travailler avec le fabricant de votre périphérique pour les informations de configuration spécifiques. Vous pouvez consulter les [Périphériques VPN](vpn-gateway-about-vpn-devices.md) pour plus d’informations.

Pour rechercher l’adresse IP publique de votre passerelle de réseau virtuel, utilisez l’exemple suivant :

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="CreateConnection"></a>8. créer la connexion VPN

Ensuite, créez la connexion VPN de Site à Site entre votre passerelle de réseau virtuel et sur l’appareil VPN. Veillez à remplacer les valeurs de votre propre. La clé partagée doit correspondre à la valeur que vous avez utilisé pour votre configuration de périphérique VPN. Notez que la `-ConnectionType` de Site à Site *IPsec*. 

Définissez les variables.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Créer la connexion.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Après un bref délai, la connexion sera établi. 

## <a name="toverify"></a>Pour vérifier une connexion VPN

Il existe différentes manières de vérifier votre connexion VPN.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Pour modifier des préfixes d’adresse IP pour une passerelle de réseau local

Si vous devez modifier les préfixes de la passerelle du réseau local, utilisez les instructions suivantes. Deux ensembles d’instructions sont fournis. Les instructions que vous choisissez dépendant de si vous avez déjà créé votre connexion passerelle. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Pour modifier l’adresse IP de passerelle pour une passerelle de réseau local

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez ajouter des ordinateurs virtuels à vos réseaux virtuels. Étapes, consultez [créer un ordinateur virtuel](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .

- Pour plus d’informations sur BGP, consultez la [Vue d’ensemble du protocole BGP](vpn-gateway-bgp-overview.md) et [comment configurer le protocole BGP](vpn-gateway-bgp-resource-manager-ps.md).

