<properties 
   pageTitle="Configurer le tunneling forcée pour les connexions de Site à Site en utilisant le modèle de déploiement du Gestionnaire de ressources | Microsoft Azure"
   description="Comment rediriger ou « force » du trafic Internet lié à votre magasin local."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurer un tunneling forcé en utilisant le modèle de déploiement du Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [PowerShell - classique](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Gestionnaire de ressources](vpn-gateway-forced-tunneling-rm.md)

Tunneling forcée vous permet de rediriger ou « force » tout trafic Internet lié aux revenir à votre emplacement sur site via un tunnel VPN de Site à Site d’inspection et d’audit. Il s’agit d’une exigence de sécurité critiques pour la plupart des entreprises informatiques les stratégies.

Sans le tunneling forcé, trafic Internet lié à partir de vos ordinateurs virtuels dans Azure sera toujours parcourir à partir de l’infrastructure de réseau Azure directement à Internet, sans l’option vous permet d’inspecter ou de vérifier le trafic. Accès Internet non autorisés susceptibles d’entraîner à la divulgation d’informations ou d’autres types de violations de la sécurité

Cet article vous guide dans le processus de configuration forcée de tunnel pour les réseaux virtuels créés à l’aide du modèle de déploiement du Gestionnaire de ressources.

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modèles de déploiement et des outils pour le tunneling de force**

Une connexion forcée de tunnel peut être configurée pour le modèle de déploiement classique et le modèle de déploiement du Gestionnaire de ressources. Reportez-vous au tableau suivant pour plus d’informations. Nous mettons à jour cette table comme de nouveaux articles, les nouveaux modèles de déploiement et des outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de la table.

[AZURE.INCLUDE [vpn-gateway-table-forced-tunneling](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="about-forced-tunneling"></a>Environ forcé de tunneling


Le diagramme suivant illustre comment forcé tunneling. 

![Forcé de Tunneling](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

Dans l’exemple ci-dessus, la partie frontale sous-réseau n’est pas forcé par tunnel. Les charges de travail dans le sous-réseau frontal peuvent continuer à accepter et à répondre directement aux demandes des clients à partir d’Internet. Les sous-réseaux de milieu de gamme et back-end sont forcés par tunnel. Toutes les connexions sortantes à partir de ces deux sous-réseaux à Internet seront forcées ou redirigées vers un site local via parmi les tunnels VPN de S2S.

Cela vous permet de restreindre et contrôler l’accès à Internet à partir de vos machines virtuelles ou cloud services dans Azure, tout en continuant à activer votre architecture à plusieurs niveaux service requis. Vous pouvez également appliquer tunneling forcée pour les réseaux virtuels tout s’il n’y aucune des charges de travail avec accès Internet de vos réseaux virtuels.

## <a name="requirements-and-considerations"></a>Configuration requise et remarques

Tunneling forcée dans Azure est configuré par le biais des itinéraires réseau virtuel défini par l’utilisateur. Rediriger le trafic d’un site local est exprimée sous la forme d’un itinéraire par défaut vers la passerelle VPN d’Azure. Pour plus d’informations sur le routage de défini par l’utilisateur et de réseaux virtuels, consultez [itinéraires et transmission IP défini par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md).

- Chaque sous-réseau du réseau virtuel a une table de routage du système intégré. La table de routage système comporte trois groupes d’itinéraires suivants :

    - **Route de VNet local :** Directement à la destination de machines virtuelles sur le même réseau virtuel
    
    - **Les itinéraires sur le site :** À la passerelle VPN d’Azure
    
    - **Gamme par défaut :** Directement à Internet. Paquets destinés à des adresses IP privées non couverts par les deux itinéraires précédentes seront supprimées.

-  Cette procédure utilise les itinéraires définis par l’utilisateur (UDR) pour créer une table de routage pour ajouter un itinéraire par défaut, puis associer la table de routage à votre sous-réseau de VNet pour permettre le tunneling forcé sur ces sous-réseaux.

- Doit être associé à un VNet qui a une passerelle VPN basée sur un itinéraire de tunneling forcée. Vous devez définir un « site par défaut « parmi les sites locaux de coexistence connecté au réseau virtuel.

- ExpressRoute forcé tunneling via ce mécanisme n’est pas configurée, mais au lieu de cela, est activé par un itinéraire par défaut via les sessions d’homologation ExpressRoute BGP de publicité. Consultez la [Documentation de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) pour plus d’informations.

## <a name="configuration-overview"></a>Vue d’ensemble de la configuration

La procédure suivante permet de créer un groupe de ressources et un VNet. Vous allez ensuite créer une passerelle VPN et configurer le tunneling forcée. Dans cette procédure, le réseau virtuel « Multiniveau-VNet » a 3 sous-réseaux : *frontal*, *milieu de gamme*et *back-end*, avec 4 coexistence connexions : *DefaultSiteHQ*et 3 *Branches*.

Les étapes de la procédure le *DefaultSiteHQ* en tant que la connexion de site par défaut pour forcé de tunneling et configurer le milieu de gamme et les sous-réseaux back-end à utiliser forcé de tunneling.

    
## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments suivants avant de commencer la configuration.

- Un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Vous devez installer la dernière version de cmdlets PowerShell de gestionnaire de ressources Azure (1.0 ou ultérieurs). Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .


## <a name="configure-forced-tunneling"></a>Configurer le tunneling de force

1. Dans la console PowerShell, connectez-vous à votre compte Azure. Cette applet de commande vous demande les informations d’identification de connexion pour votre compte Azure. Après la connexion, il télécharge les paramètres de votre compte afin qu’elles soient disponibles pour Azure PowerShell.

        Login-AzureRmAccount 

2. Obtenir la liste de vos abonnements Azure.

        Get-AzureRmSubscription

2. Spécifier l’abonnement que vous souhaitez utiliser. 

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
        
3. Créer un groupe de ressources.

        New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Créer un réseau virtuel et spécifiez les sous-réseaux. 

        $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
        $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
        $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
        $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
        $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Créer les passerelles de réseau local.

        $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
        $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
        $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
        $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
        
6. Créer la table de routage et une règle d’itinéraire.

        New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
        $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
        Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
        Set-AzureRmRouteTable -RouteTable $rt


7. Associer la table d’itinéraires vers les sous-réseaux du milieu de gamme et back-end.

        $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
        Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

8. Créez la passerelle avec un site par défaut. Cette étape prend un certain temps, parfois de 45 minutes ou plus, dans la mesure où vous créez et configurez la passerelle.<br> Le `-GatewayDefaultSite` est le paramètre d’applet de commande qui permet la configuration de routage forcée de travailler, par conséquent, prenez soin de configurer ce paramètre correctement. Ce paramètre est disponible dans PowerShell 1.0 ou version ultérieure.

        $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
        $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
        New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

9. Établir des connexions VPN de Site à Site.

        $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
        $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
        $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
        $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
        $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

        Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
        



