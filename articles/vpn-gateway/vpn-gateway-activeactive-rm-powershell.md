<properties
   pageTitle="Comment faire pour configurer des connexions VPN de S2S actif avec les passerelles VPN Azure à l’aide du Gestionnaire de ressources Azure et PowerShell | Microsoft Azure"
   description="Cet article vous guide dans le processus de configuration des connexions de l’actif avec les passerelles VPN Azure à l’aide du Gestionnaire de ressources Azure et PowerShell."
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
   ms.date="09/26/2016"
   ms.author="yushwang"/>

# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Configurer les connexions VPN de S2S actif avec les passerelles VPN Azure à l’aide du Gestionnaire de ressources Azure et PowerShell

Cet article vous guide tout au long de la procédure de création de coexistence actif-actif et les connexions de VNet-à-VNet à l’aide du Gestionnaire de ressources du modèle de déploiement et PowerShell.


**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-highly-available-cross-premises-connections"></a>À propos des connexions de coexistence hautement disponible

Pour atteindre une haute disponibilité pour la coexistence et la connectivité des VNet-à-VNet, vous devez déployer plusieurs passerelles VPN et établir plusieurs connexions parallèles entre vos réseaux et vos Azure. Pour une vue d’ensemble des options de connectivité et de la topologie, consultez le [hautement disponible coexistence et connectivité de VNet-à-VNet](./vpn-gateway-highlyavailable.md) .

Cet article fournit des instructions pour configurer une connexion VPN de coexistence actif-actif et actif des connexion entre deux réseaux virtuels :

- [Partie 1 : créer et configurer la passerelle VPN d’Azure en mode actif-actif](#aagateway)

- [Partie 2 - établir des connexions entre sites actif-actif](#aacrossprem)

- [Partie 3 - établir des connexions de VNet à VNet actif](#aav2v)

- [Partie 4 - mise à jour de la passerelle existant entre actif et active-de réserve](#aaupdate)

Vous pouvez combiner ensemble pour créer une topologie de réseau plus complexes et hautement disponible qui répond à vos besoins.

>[AZURE.IMPORTANT] Veuillez noter que le mode actif / actif ne fonctionne que dans HighPerformance SKU


## <a name ="aagateway"></a>Partie 1 : créer et configurer des passerelles VPN actif-actif

Les étapes suivantes configurera votre passerelle VPN d’Azure dans les modes de l’actif. Les principales différences entre les passerelles actif-actif et actif-de réserve :

- Vous devez créer deux configurations IP de la passerelle avec deux adresses IP publiques
- Vous devez définir l’indicateur EnableActiveActiveFeature
- La référence SKU de la passerelle doit être bien

Les autres propriétés sont les mêmes que les passerelles non actif. 

### <a name="before-you-begin"></a>Avant de commencer

- Vérifiez que vous disposez d’un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
    
- Vous devez installer les applets de commande PowerShell de gestionnaire de ressources de Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .

### <a name="step-1---create-and-configure-vnet1"></a>Étape 1 : créer et configurer des VNet1

#### <a name="1-declare-your-variables"></a>1. déclarer vos variables

Pour cet exercice, nous allons commencer en déclarant des variables. L’exemple suivant déclare les variables à l’aide de valeurs pour cet exercice. Veillez à remplacer les valeurs par votre propre lors de la configuration pour la production. Vous pouvez utiliser ces variables si vous exécutez les étapes pour vous familiariser avec ce type de configuration. Modifiez les variables, puis copiez et collez dans votre console PowerShell.

    $Sub1          = "Ross"
    $RG1           = "TestAARG1"
    $Location1     = "West US"
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
    $GW1IPName1    = "VNet1GWIP1"
    $GW1IPName2    = "VNet1GWIP2"
    $GW1IPconf1    = "gw1ipconf1"
    $GW1IPconf2    = "gw1ipconf2"
    $Connection12  = "VNet1toVNet2"
    $Connection151 = "VNet1toSite5_1"
    $Connection152 = "VNet1toSite5_2"

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

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Étape 2 : créer la passerelle VPN pour TestVNet1 avec mode actif-actif

#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Créez les adresses IP et la passerelle public configurations IP

Demander les deux adresses IP publiques à allouer à la passerelle que vous allez créer pour votre VNet. Vous allez également définir le sous-réseau et les configurations IP requises. 

    $gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $gw1pip2    = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

    $vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    $gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Créez la passerelle VPN avec configuration actif-actif

Créez la passerelle réseau virtuel pour TestVNet1. Notez qu’il existe deux entrées GatewayIpConfig, et que l’indicateur EnableActiveActiveFeature est défini. En mode actif-actif nécessite une passerelle VPN basée sur un itinéraire de HighPerformance SKU. Création d’une passerelle peut prendre un certain temps (30 minutes ou plus).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN -EnableActiveActiveFeature -Debug

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. obtenir les adresses IP publiques de passerelle et de l’adresse IP du poste BGP

Lorsque la passerelle est créée, vous devez obtenir l’adresse IP d’homologue BGP sur la passerelle de VPN Azure. Cette adresse est nécessaire pour configurer la passerelle VPN Azure comme un homologue du protocole BGP pour vos périphériques VPN sur site.

    $gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
    $gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

Utiliser les applets de commande suivantes pour afficher les deux adresses IP publiques alloués pour votre passerelle VPN et leurs adresses IP d’homologue BGP correspondantes pour chaque instance de passerelle :

    PS D:\> $gw1pip1.IpAddress
    40.112.190.5

    PS D:\> $gw1pip2.IpAddress
    138.91.156.129

    PS D:\> $vnet1gw.BgpSettingsText
    {
      "Asn": 65010,
      "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
      "PeerWeight": 0
    }

Traite de l’ordre de l’adresse IP publique pour les instances de la passerelle et les adresses d’homologation BGP correspondante sont identiques. Dans cet exemple, l’ordinateur virtuel avec une adresse IP publique de 40.112.190.5 de passerelle utilise 10.12.255.4 comme son adresse d’homologation BGP, et utilise la passerelle avec 138.91.156.129 10.12.255.5. Cette information est nécessaire lorsque vous définissez des périphériques VPN locaux dans se connecter à la passerelle de l’actif. La passerelle est illustrée dans le schéma ci-dessous avec toutes les adresses :

![passerelle d’actif-actif](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Lorsque la passerelle est créée, vous pouvez utiliser cette passerelle à établir entre sites actif-actif ou VNet-à-VNet. Les sections suivantes vous guident à travers les étapes pour terminer l’exercice.


## <a name ="aacrossprem"></a>Partie 2 : établir une connexion entre sites actif-actif

Pour établir une connexion entre sites, vous devez créer une passerelle réseau Local pour représenter votre périphérique VPN local et une connexion à la passerelle VPN d’Azure de se connecter à la passerelle du réseau local. Dans cet exemple, la passerelle VPN d’Azure est en mode actif-actif. Par conséquent, même s’il existe un seul local le périphérique VPN (passerelle réseau local) et les ressources d’une seule connexion, les deux instances de passerelle VPN de Azure va établir des tunnels VPN de S2S avec le périphérique local.

Avant de continuer, vérifiez que vous avez terminé la [partie 1](#aagateway) de cet exercice.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Étape 1 : créer et configurer la passerelle du réseau local

#### <a name="1-declare-your-variables"></a>1. déclarer vos variables

Cet exercice va continuer à créer la configuration illustrée dans le diagramme. Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

    $RG5           = "TestAARG5"
    $Location5     = "West US"
    $LNGName51     = "Site5_1"
    $LNGPrefix51   = "10.52.255.253/32"
    $LNGIP51       = "131.107.72.22"
    $LNGASN5       = 65050
    $BGPPeerIP51   = "10.52.255.253"

Quelques éléments à noter en ce qui concerne les paramètres de passerelle du réseau local :

- La passerelle du réseau local peut être dans la même ou de différente de l’emplacement et le groupe de ressources comme la passerelle VPN. Cet exemple montre les différents groupes de ressources, mais dans le même emplacement d’Azure.

- S’il existe un seul périphérique VPN sur site comme indiqué ci-dessus, la connexion de l’actif peut fonctionner avec ou sans les protocole BGP. Cet exemple utilise le protocole BGP pour la connexion de coexistence.

- Si le protocole BGP est activé, le préfixe, que vous devez déclarer pour la passerelle du réseau local est l’adresse de l’hôte de votre adresse IP homologue de BGP sur l’appareil VPN. Dans ce cas, il s’agit d’un /32 le préfixe de « 10.52.255.253/32 ».

- Pour rappel, vous devez utiliser différents APE BGP entre votre des réseaux locaux et les VNet d’Azure. Si elles sont identiques, vous devez modifier votre ASN VNet si votre périphérique VPN local utilise déjà l’ASN de pair avec d’autres voisins BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Créez la passerelle du réseau local de Site5
    
Avant de continuer, vérifiez que vous êtes toujours connecté à 1 de l’abonnement. S’il n’est pas encore créé de créer le groupe de ressources.

    New-AzureRmResourceGroup       -Name $RG5 -Location $Location5
    New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Étape 2 : connexion de la passerelle de VNet et de la passerelle du réseau local

#### <a name="1-get-the-two-gateways"></a>1. Obtenez les deux passerelles

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
    $lng5gw1 = Get-AzureRmLocalNetworkGateway   -Name $LNGName51 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. créer le TestVNet1 à Site5 connexion

Dans cette étape, vous allez créer la connexion à partir de TestVNet1 à Site5_1 avec « EnableBGP » est défini sur $True.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN et BGP les paramètres de votre périphérique VPN sur site

L’exemple ci-dessous répertorie les paramètres que vous entrez dans la section de configuration BGP dans votre périphérique VPN sur site pour cet exercice :

    - Site5 ASN : 65050
    - Site5 BGP IP : 10.52.255.253
    - Annoncer les préfixes : (par exemple) 10.51.0.0/16 et 10.52.0.0/16
    - Azure VNet ASN : 65010
    - Azure VNet BGP IP 1 : 10.12.255.4 tunnel à 40.112.190.5
    - Azure VNet BGP IP 2 : 10.12.255.5 tunnel à 138.91.156.129
    - Itinéraires statiques : Destination 10.12.255.4/32, nexthop le tunnel VPN de l’interface pour 40.112.190.5 Destination 10.12.255.5/32, nexthop le tunnel VPN de l’interface de 138.91.156.129
    - eBGP sauts multiples : Vérifiez que l’option « sauts multiples » eBGP est activée sur votre périphérique si nécessaire

La connexion doit être établie au bout de quelques minutes, et la session d’homologation BGP démarrera une fois établie la connexion IPsec. Cet exemple a configuré jusqu’ici pas qu’un seul périphérique VPN local, qui en résulte dans le diagramme ci-dessous :

![actif-actif-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Étape 3 : connecter deux périphériques VPN de site à la passerelle VPN actif-actif

Si vous avez deux périphériques VPN sur le même réseau local, vous pouvez obtenir la redondance double en vous connectant à la passerelle VPN d’Azure pour le deuxième périphérique VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. créer la deuxième passerelle du réseau local de Site5

Notez que l’adresse IP de la passerelle, préfixe d’adresse et adresse d’homologation BGP pour la deuxième passerelle du réseau local ne doivent pas chevaucher avec la passerelle du réseau local précédentes pour le même réseau local. 

    $LNGName52     = "Site5_2"
    $LNGPrefix52   = "10.52.255.254/32"
    $LNGIP52       = "131.107.72.23"
    $BGPPeerIP52   = "10.52.255.254"

    New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
 
#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Connectez la deuxième passerelle du réseau local et la passerelle VNet

Créer la connexion entre TestVNet1 et Site5_2 avec « EnableBGP » défini sur $True

    $lng5gw2 = Get-AzureRmLocalNetworkGateway   -Name $LNGName52 -ResourceGroupName $RG5

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. paramètres VPN et de BGP pour votre deuxième périphérique VPN sur site

De même, sous listes les paramètres vous entrerez dans le deuxième périphérique VPN :

    - Site5 ASN : 65050
    - Site5 BGP IP : 10.52.255.254
    - Annoncer les préfixes : (par exemple) 10.51.0.0/16 et 10.52.0.0/16
    - Azure VNet ASN : 65010
    - Azure VNet BGP IP 1 : 10.12.255.4 tunnel à 40.112.190.5
    - Azure VNet BGP IP 2 : 10.12.255.5 tunnel à 138.91.156.129
    - Itinéraires statiques : Destination 10.12.255.4/32, nexthop le tunnel VPN de l’interface pour 40.112.190.5 Destination 10.12.255.5/32, nexthop le tunnel VPN de l’interface de 138.91.156.129
    - eBGP sauts multiples : Vérifiez que l’option « sauts multiples » eBGP est activée sur votre périphérique si nécessaire

Une fois la connexion (tunnels) sont établis, vous disposez de deux périphériques VPN redondants et tunnels reliant le réseau des locaux et Azure :

![Double-redondance-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)


## <a name ="aav2v"></a>Partie 3 - d’établir une connexion de VNet à VNet actif

Cette section crée une connexion de VNet à VNet actif avec BGP. 

Les instructions ci-dessous continuent à partir de la procédure précédente ci-dessus. Vous devez terminer la [partie 1](#aagateway) , pour créer et configurer des TestVNet1 et la passerelle VPN avec BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Étape 1 - créer des TestVNet2 et la passerelle VPN

Il est important de s’assurer que l’espace d’adressage IP du nouveau réseau virtuel, TestVNet2, ne se chevauche pas avec n’importe lequel de vos plages VNet.

Dans cet exemple, les réseaux virtuels appartiennent à la même abonnement. Vous pouvez définir des connexions de VNet-à-VNet entre différents abonnements ; consultez pour [configurer une connexion de VNet à VNet](./vpn-gateway-vnet-vnet-rm-ps.md) pour en savoir plus. Veillez à ajouter le «-EnableBgp $True » lors de la création de connexions pour activer le protocole BGP.

#### <a name="1-declare-your-variables"></a>1. déclarer vos variables

Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

    $RG2           = "TestAARG2"
    $Location2     = "East US"
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
    $GW2IPName1    = "VNet2GWIP1"
    $GW2IPconf1    = "gw2ipconf1"
    $GW2IPName2    = "VNet2GWIP2"
    $GW2IPconf2    = "gw2ipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. création de TestVNet2 dans le nouveau groupe de ressources

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2

    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. création de la passerelle VPN actif pour TestVNet2

Demander les deux adresses IP publiques à allouer à la passerelle que vous allez créer pour votre VNet. Vous allez également définir le sous-réseau et les configurations IP requises. 

    $gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
    $gw2pip2    = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
    $gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2

Créez la passerelle VPN avec le nombre en tant qu’et de l’indicateur « EnableActiveActiveFeature ». Notez que vous devez substituer la valeur par défaut ASN sur vos passerelles VPN d’Azure. Les APE pour la VNets connectés doivent être différents pour activer le protocole BGP et acheminement de transit.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet2ASN -EnableActiveActiveFeature

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Étape 2 : se connecter les passerelles TestVNet1 et TestVNet2

Dans cet exemple, les deux passerelles sont dans le même abonnement. Vous pouvez effectuer cette opération dans la même session de PowerShell.

#### <a name="1-get-both-gateways"></a>1. Obtenez les deux passerelles.

Assurez-vous de vous connecter et connectez à 1 de l’abonnement.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. créer deux connexions.

Dans cette étape, vous allez créer la connexion entre TestVNet1 et TestVNet2 et la connexion à partir de TestVNet2 à TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Veillez à activer BGP pour les deux connexions.

Une fois ces étapes, la connexion va être établir dans quelques minutes et le protocole BGP homologation session seront de la fin de la connexion VNet à VNet avec redondance double :

![actif-actif-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Partie 4 - mise à jour de la passerelle existant entre actif et active-de réserve

La dernière section décrit comment vous pouvez configurer une passerelle VPN d’Azure existante à partir d’active-de réserve en mode actif-actif, et inversement.

>[AZURE.IMPORTANT] Veuillez noter que le mode actif / actif ne fonctionne que dans HighPerformance SKU

### <a name="configure-an-active-standby-gateway-to-active-active-gateway"></a>Configuration d’une passerelle active-de réserve à la passerelle de l’actif

#### <a name="1-gateway-parameters"></a>1. paramètres de la passerelle

L’exemple suivant convertit une passerelle active-de réserve une passerelle actif. Vous devez créer une autre adresse IP publique, puis ajouter une seconde configuration IP de la passerelle. Ci-dessous montre les paramètres utilisés :

    $GWName     = "TestVNetAA1GW"
    $VNetName   = "TestVNetAA1"
    $RG         = "TestVPNActiveActive01"
    $GWIPName2  = "gwpip2"
    $GWIPconf2  = "gw1ipconf2"

    $vnet       = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
    $subnet     = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $location   = $gw.Location

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. création de l’adresse IP publique, puis ajoutez la seconde configuration IP de passerelle

    $gwpip2     = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
    Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2 

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Activez le mode actif-actif et mise à jour de la passerelle

Vous devez définir l’objet passerelle dans PowerShell pour déclencher la mise à jour réelle. La référence de l’objet passerelle doit également être modifiée à bien dans la mesure où il a été créé précédemment en Standard.

    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance

Cette mise à jour peut prendre 30 à 45 minutes.

### <a name="configure-an-active-active-gateway-to-active-standby-gateway"></a>Configuration d’une passerelle d’actif à la passerelle de secours actif

#### <a name="1-gateway-parameters"></a>1. paramètres de la passerelle

Utilisez les mêmes paramètres que ci-dessus, obtenir le nom de la configuration d’IP que vous souhaitez supprimer.

    $GWName     = "TestVNetAA1GW"
    $RG         = "TestVPNActiveActive01"

    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $ipconfname = $gw.IpConfigurations[1].Name

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Retirez la configuration IP de la passerelle et de désactiver le mode actif-actif

De même, vous devez définir l’objet passerelle dans PowerShell pour déclencher la mise à jour réelle.

    Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature

Cette mise à jour peut prendre jusqu'à 30 à 45 minutes.


## <a name="next-steps"></a>Étapes suivantes

Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à des réseaux virtuels. Étapes, consultez [créer un ordinateur virtuel](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .

