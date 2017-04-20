<properties
   pageTitle="Comment faire pour configurer le protocole BGP sur Azure les passerelles VPN à l’aide du Gestionnaire de ressources Azure et PowerShell | Microsoft Azure"
   description="Cet article vous guide dans le processus de configuration BGP avec Azure des passerelles VPN à l’aide du Gestionnaire de ressources Azure et PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="yushwang"/>

# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Comment faire pour configurer le protocole BGP sur Azure les passerelles VPN à l’aide du Gestionnaire de ressources Azure et PowerShell

Cet article vous guide tout au long de la procédure pour activer le protocole BGP sur une connexion VPN de Site à Site (S2S) de coexistence et une connexion VNet à VNet à l’aide du Gestionnaire de ressources du modèle de déploiement et PowerShell.


**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-bgp"></a>À propos de BGP

BGP est le protocole de routage standard couramment dans Internet pour échanger des informations de routage et de l’accessibilité entre deux ou plusieurs réseaux. BGP permet des passerelles VPN Azure et vos périphériques VPN sur site, appelés BGP homologues ou voisins, d’échanger les « route » qui vous informe sur la disponibilité et l’accessibilité de ces préfixes à traverser les passerelles ou routeurs impliqués les deux passerelles. BGP permettent également d’acheminement de transit entre plusieurs réseaux par propagation des itinéraires qu'une passerelle BGP apprend à partir d’un homologue BGP pour tous les autres pairs BGP.

Consultez [Vue d’ensemble du protocole BGP avec des passerelles VPN Azure](./vpn-gateway-bgp-overview.md) pour plus d’informations sur les avantages du protocole BGP et à comprendre les exigences techniques et les considérations d’utilisation BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Mise en route avec BGP sur les passerelles VPN d’Azure

Cet article vous guidera à travers les étapes à effectuer les tâches suivantes :

- [Partie 1 - activer BGP sur votre passerelle VPN d’Azure](#enablebgp)

- [Partie 2 : établir une connexion de coexistence avec BGP](#crossprembgp)

- [Partie 3 - d’établir une connexion VNet à VNet avec BGP](#v2vbgp)

Chaque partie des instructions forme un bloc de construction de base permettant de BGP dans la connectivité réseau. Si vous terminez toutes les trois parties, vous allez générer la topologie, comme illustré dans le diagramme suivant :

![Topologie BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Vous pouvez combiner ensemble pour créer un réseau de transit plus complexes et multiples d’espoir, qui répondent à vos besoins.

## <a name ="enablebgp"></a>Partie 1 - configurer BGP sur la passerelle VPN Azure

Les étapes de configuration suivantes va configurer les paramètres BGP de la passerelle VPN d’Azure comme illustré dans le diagramme suivant :

![Passerelle de protocole BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Avant de commencer

- Vérifiez que vous disposez d’un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
    
- Vous devez installer les applets de commande PowerShell de gestionnaire de ressources de Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .

### <a name="step-1---create-and-configure-vnet1"></a>Étape 1 : créer et configurer des VNet1 

#### <a name="1-declare-your-variables"></a>1. déclarer vos variables

Pour cet exercice, nous allons commencer en déclarant des variables. L’exemple suivant déclare les variables à l’aide de valeurs pour cet exercice. Veillez à remplacer les valeurs par votre propre lors de la configuration pour la production. Vous pouvez utiliser ces variables si vous exécutez les étapes pour vous familiariser avec ce type de configuration. Modifiez les variables, puis copiez et collez dans votre console PowerShell.

    $Sub1          = "Replace_With_Your_Subcription_Name"
    $RG1           = "TestBGPRG1"
    $Location1     = "East US"
    $VNetName1     = "TestVNet1"
    $FESubName1    = "FrontEnd"
    $BESubName1    = "Backend"
    $GWSubName1    = "GatewaySubnet"
    $VNetPrefix11  = "10.11.0.0/16"
    $VNetPrefix12  = "10.12.0.0/16"
    $FESubPrefix1  = "10.11.0.0/24"
    $BESubPrefix1  = "10.12.0.0/24"
    $GWSubPrefix1  = "10.12.255.0/27"
    $VNet1ASN      = 65010
    $DNS1          = "8.8.8.8"
    $GWName1       = "VNet1GW"
    $GWIPName1     = "VNet1GWIP"
    $GWIPconfName1 = "gwipconf1"
    $Connection12  = "VNet1toVNet2"
    $Connection15  = "VNet1toSite5"

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Connectez-vous à votre abonnement et créer un nouveau groupe de ressources

Vérifiez que vous passez en mode de PowerShell pour utiliser les applets de commande du Gestionnaire de ressources. Pour plus d’informations, reportez-vous [à l’aide de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

Ouvrez la console PowerShell et vous connecter à votre compte. Pour vous aider à vous connecter, utilisez l’exemple suivant :

    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $Sub1
    New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### <a name="3-create-testvnet1"></a>3. création de TestVNet1

L’exemple ci-dessous crée un réseau virtuel appelé TestVNet1 et trois sous-réseaux, un GatewaySubnet appelé, un frontal appelée et une seule appelée back-end. Lors de la substitution de valeurs, il est important de toujours nommer votre sous-réseau passerelle spécifiquement GatewaySubnet. Si vous lui quelque chose d’autre, la création de votre passerelle échouera. 

    $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

    New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Étape 2 : créer la passerelle VPN pour TestVNet1 avec des paramètres de protocole BGP

#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Créez la configuration IP et de sous-réseau

Demander une adresse IP publique à allouer à la passerelle que vous allez créer pour votre VNet. Vous allez également définir le sous-réseau et les configurations IP requises. 

    $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    
    $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Créez la passerelle VPN avec le numéro en tant que

Créez la passerelle réseau virtuel pour TestVNet1. Notez que le protocole BGP nécessite une passerelle VPN basée sur l’itinéraire, ainsi que le paramètre addition, - Asn, pour définir l’ASN (comme nombre) pour TestVNet1. Création d’une passerelle peut prendre un certain temps (30 minutes ou plus).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. obtenir l’adresse IP d’homologue Azure BGP

Lorsque la passerelle est créée, vous devez obtenir l’adresse IP d’homologue BGP sur la passerelle de VPN Azure. Cette adresse est nécessaire pour configurer la passerelle VPN Azure comme un homologue du protocole BGP pour vos périphériques VPN sur site.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet1gw.BgpSettingsText

La dernière commande affichera les configurations BGP correspondantes sur la passerelle de VPN Azure ; par exemple :

    $vnet1gw.BgpSettingsText
    {
        "Asn": 65010,
        "BgpPeeringAddress": "10.12.255.30",
        "PeerWeight": 0
    }

Une fois la passerelle est créée, vous pouvez utiliser cette passerelle pour établir la connexion de coexistence ou VNet-à-VNet avec BGP. Les sections suivantes vous guident à travers les étapes pour terminer l’exercice.

## <a name ="crossprembbgp"></a>Partie 2 : établir une connexion de coexistence avec BGP

Pour établir une connexion entre sites, vous devez créer une passerelle réseau Local pour représenter votre périphérique VPN local et une connexion à la passerelle VPN d’Azure de se connecter à la passerelle du réseau local. La différence entre les instructions de cet article est les propriétés supplémentaires requises pour spécifier les paramètres de configuration BGP.

![BGP pour la coexistence](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Avant de continuer, vérifiez que vous avez terminé la [partie 1](#enablebgp) de cet exercice.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Étape 1 : créer et configurer la passerelle du réseau local

#### <a name="1-declare-your-variables"></a>1. déclarer vos variables

Cet exercice va continuer à créer la configuration illustrée dans le diagramme. Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

    $RG5           = "TestBGPRG5"
    $Location5     = "East US 2"
    $LNGName5      = "Site5"
    $LNGPrefix50   = "10.52.255.254/32"
    $LNGIP5        = "Your_VPN_Device_IP"
    $LNGASN5       = 65050
    $BGPPeerIP5    = "10.52.255.254"

Quelques éléments à noter en ce qui concerne les paramètres de passerelle du réseau local :

- La passerelle du réseau local peut être dans la même ou de différente de l’emplacement et le groupe de ressources comme la passerelle VPN. Cet exemple montre comment les affiche dans les différents groupes de ressources dans des emplacements différents.

- Le préfixe minimum que vous devez déclarer pour la passerelle du réseau local est l’adresse de l’hôte de votre adresse IP homologue de BGP sur l’appareil VPN. Dans ce cas, il s’agit d’un /32 le préfixe de « 10.52.255.254/32 ».

- Pour rappel, vous devez utiliser différents APE BGP entre votre des réseaux locaux et les VNet d’Azure. Si elles sont identiques, vous devez modifier votre ASN VNet si votre périphérique VPN sur site utilise déjà l’ASN de pair avec d’autres voisins BGP.
    
Avant de continuer, vérifiez que vous êtes toujours connecté à 1 de l’abonnement.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Créez la passerelle du réseau local de Site5

Veillez à créer le groupe de ressources si elle n’est créée, avant de créer la passerelle du réseau local. Notez les deux paramètres supplémentaires pour la passerelle du réseau local : Asn et BgpPeerAddress.

    New-AzureRmResourceGroup -Name $RG5 -Location $Location5

    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Étape 2 : connexion de la passerelle de VNet et de la passerelle du réseau local

#### <a name="1-get-the-two-gateways"></a>1. Obtenez les deux passerelles

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
        $lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. créer le TestVNet1 à Site5 connexion

Dans cette étape, vous allez créer la connexion à partir de TestVNet1 à Site5. Vous devez spécifier «-EnableBGP $True » pour activer le protocole BGP pour cette connexion. Comme indiqué plus haut, il est possible des connexions BGP et de non-BGP pour la même passerelle VPN Azure à. À moins que le protocole BGP est activée dans la propriété de connexion, Azure n’activera pas BGP pour cette connexion même si les paramètres de protocole BGP sont déjà configurés sur les deux passerelles.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True


L’exemple ci-dessous répertorie les paramètres que vous entrez dans la section de configuration BGP dans votre périphérique VPN sur site pour cet exercice :

    - Site5 ASN : 65050
    - Site5 BGP IP : 10.52.255.254
    - Annoncer les préfixes : (par exemple) 10.51.0.0/16 et 10.52.0.0/16
    - Azure VNet ASN : 65010
    - VNet Azure BGP IP : 10.12.255.30
    - Itinéraire statique : ajouter un itinéraire pour 10.12.255.30/32, avec le saut suivant est l’interface de tunnel VPN sur votre périphérique.
    - eBGP sauts multiples : Vérifiez que l’option « sauts multiples » eBGP est activée sur votre périphérique si nécessaire

La connexion doit être établie au bout de quelques minutes, et la session d’homologation BGP démarrera une fois établie la connexion IPsec.
 
## <a name ="v2vbgp"></a>Partie 3 - d’établir une connexion VNet à VNet avec BGP

Cette section ajoute une connexion VNet-à-VNet avec BGP, comme illustré dans le schéma ci-dessous. 

![BGP pour VNet-de-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Les instructions ci-dessous continuent à partir de la procédure précédente ci-dessus. Vous devez terminer [partie I](#enablebgp) pour créer et configurer des TestVNet1 et la passerelle VPN avec BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Étape 1 - créer des TestVNet2 et la passerelle VPN

Il est important de s’assurer que l’espace d’adressage IP du nouveau réseau virtuel, TestVNet2, ne se chevauche pas avec n’importe lequel de vos plages VNet.

Dans cet exemple, les réseaux virtuels appartiennent à la même abonnement. Vous pouvez configurer les connexions de VNet à VNet entre les différents abonnements ; consultez pour [configurer une connexion de VNet à VNet](./vpn-gateway-vnet-vnet-rm-ps.md) pour en savoir plus. Veillez à ajouter le «-EnableBgp $True » lors de la création de connexions pour activer le protocole BGP.

#### <a name="1-declare-your-variables"></a>1. déclarer vos variables

Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

    $RG2           = "TestBGPRG2"
    $Location2     = "West US"
    $VNetName2     = "TestVNet2"
    $FESubName2    = "FrontEnd"
    $BESubName2    = "Backend"
    $GWSubName2    = "GatewaySubnet"
    $VNetPrefix21  = "10.21.0.0/16"
    $VNetPrefix22  = "10.22.0.0/16"
    $FESubPrefix2  = "10.21.0.0/24"
    $BESubPrefix2  = "10.22.0.0/24"
    $GWSubPrefix2  = "10.22.255.0/27"
    $VNet2ASN      = 65020
    $DNS2          = "8.8.8.8"
    $GWName2       = "VNet2GW"
    $GWIPName2     = "VNet2GWIP"
    $GWIPconfName2 = "gwipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. création de TestVNet2 dans le nouveau groupe de ressources

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2
    
    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Créez la passerelle VPN pour TestVNet2 avec des paramètres de protocole BGP

Demander une adresse IP publique à allouer à la passerelle que vous allez créer pour votre VNet. Vous allez également définir le sous-réseau et les configurations IP requises. 

    $gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2

Créez la passerelle VPN avec le nombre en tant que. Notez que vous devez substituer la valeur par défaut ASN sur vos passerelles VPN d’Azure. Les APE pour la VNets connectés doivent être différents pour activer le protocole BGP et acheminement de transit.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Étape 2 : se connecter les passerelles TestVNet1 et TestVNet2

Dans cet exemple, les deux passerelles sont dans le même abonnement. Vous pouvez effectuer cette opération dans la même session de PowerShell.

#### <a name="1-get-both-gateways"></a>1. Obtenez les deux passerelles.

Veillez à vous connecterez et connectez à 1 de l’abonnement.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. créer deux connexions.

Dans cette étape, vous allez créer la connexion entre TestVNet1 et TestVNet2 et la connexion à partir de TestVNet2 à TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Veillez à activer BGP pour les deux connexions.

Une fois ces étapes effectuées, la connexion va établir dans quelques minutes, et la session d’homologation BGP sera une fois la connexion VNet-à-VNet est terminée.

Si vous avez effectué toutes les trois parties de cet exercice, vous avez sera établi une topologie de réseau comme indiqué ci-dessous :

![BGP pour VNet-de-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Étapes suivantes

Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à des réseaux virtuels. Étapes, consultez [créer un ordinateur virtuel](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .

