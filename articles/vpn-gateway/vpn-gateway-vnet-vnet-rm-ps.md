<properties
   pageTitle="Se connecter VNets Azure avec la passerelle VPN et PowerShell | Microsoft Azure"
   description="Cet article vous guide dans le processus de connexion des réseaux virtuels entre eux en utilisant le Gestionnaire de ressources Azure et PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-vnet-to-vnet-connection-for-resource-manager-using-powershell"></a>Configurer une connexion de VNet-à-VNet pour le Gestionnaire de ressources à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Responsable de ressources - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Standard - portail classique](virtual-networks-configure-vnet-to-vnet-connection.md)

Cet article vous guide tout au long de la procédure pour créer une connexion entre les VNets dans le modèle de gestionnaire de ressources de déploiement à l’aide de passerelle VPN. Les réseaux virtuels peuvent être dans les régions identiques ou différentes et à partir d’abonnements identiques ou différents.


![diagramme de V2V](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modèles de déploiement et les méthodes pour les connexions de VNet-à-VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant montre les modèles de déploiement actuellement disponibles et les méthodes pour les configurations VNet-à-VNet. Lorsqu’un article avec des étapes de configuration n’est disponible, nous attacher directement à partir de cette table.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>Homologation de VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="about-vnet-to-vnet-connections"></a>À propos des connexions de VNet-à-VNet

Connexion d’un réseau virtuel à un autre réseau virtuel (VNet à VNet) est similaire à la connexion d’un VNet à un emplacement de site local. Les deux types de connectivité permet de fournir un tunnel sécurisé à l’aide de IPsec/IKE une passerelle VPN d’Azure. Le VNets que vous vous connectez peut être dans différentes régions. Ou, dans les différentes souscriptions. Vous pouvez même combiner communication VNet-à-VNet avec des configurations sur plusieurs sites. Cela vous permet de vous établissez une coexistence des topologies de réseau qui combinent dotée de la connectivité réseau entre virtuels, comme illustré dans le diagramme suivant :


![À propos des connexions](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### <a name="why-connect-virtual-networks"></a>Pourquoi connecter des réseaux virtuels ?

Voulez-vous vous connecter des réseaux virtuels pour les raisons suivantes :

- **Redondance entre région géographique et géo-présence**
    - Vous pouvez configurer votre propre géo-réplication ou de synchronisation avec une connectivité sécurisée sans passer par les points de terminaison faisant face à Internet.
    - Vous pouvez définir avec Azure Traffic Manager et équilibrage de la charge, la charge de travail hautement disponible avec redondance géographique dans plusieurs régions Azure. Un exemple consiste à paramétrer SQL toujours sur des groupes de disponibilité répartissant sur plusieurs régions Azure.

- **Applications n-tier régionale avec isolation ou limite d’administration**
    - Dans la même région, vous pouvez configurer des applications multicouches avec plusieurs réseaux virtuels reliés entre eux en raison d’isolement ou les besoins administratifs.


### <a name="vnet-to-vnet-faq"></a>VNet à VNet FAQ

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


## <a name="which-set-of-steps-should-i-use"></a>Le jeu d’étapes dois-je utiliser ?

Dans cet article, vous voyez deux ensembles différents d’étapes. Un d’ensemble d’étapes de [VNets qui se trouvent dans le même abonnement](#samesub)et l’autre pour [les VNets qui se trouvent dans les différentes souscriptions](#difsub). La principale différence entre les jeux est que vous pouvez créer et configurer tous les réseaux virtuels et ressources de la passerelle dans la même session de PowerShell.

Les étapes décrites dans cet article utilisent des variables qui sont déclarées au début de chaque section. Si vous travaillez déjà avec VNets existante, modifiez les variables afin de refléter les paramètres dans votre propre environnement. 

![Les deux connexions.](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


## <a name="samesub"></a>Comment se connecter VNets qui se trouvent dans le même abonnement

![diagramme de V2V](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>Avant de commencer
    
Avant de commencer, vous devez installer les applets de commande PowerShell de gestionnaire de ressources de Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .

### <a name="Step1"></a>Étape 1 : planifiez vos plages d’adresses IP


Dans les étapes suivantes, nous créons deux réseaux virtuels ainsi que leurs sous-réseaux de passerelle respectives et les configurations. Nous créons ensuite une connexion VPN entre les deux VNets. Il est important de planifier les plages d’adresses IP pour la configuration de votre réseau. Gardez à l’esprit que vous devez vous assurer qu’aucun de vos VNet des plages ou des plages de réseau local se chevauchent en aucune façon.

Nous utilisons les valeurs suivantes dans les exemples :

**Valeurs pour TestVNet1 :**

- VNet nom : TestVNet1
- Groupe de ressources : TestRG1
- Emplacement : Américain de l'
- TestVNet1 : 10.11.0.0/16 & 10.12.0.0/16
- Site Web frontal : 10.11.0.0/24
- Back-end : 10.12.0.0/24
- GatewaySubnet : 10.12.255.0/27
- Serveur DNS : 8.8.8.8
- Nom de la passerelle : VNet1GW
- IP publique : VNet1GWIP
- VPNType : RouteBased
- Connection(1to4) : VNet1toVNet4
- Connection(1to5) : VNet1toVNet5
- ConnectionType : VNet2VNet


**Valeurs pour TestVNet4 :**

- VNet nom : TestVNet4
- TestVNet2 : 10.41.0.0/16 & 10.42.0.0/16
- Site Web frontal : 10.41.0.0/24
- Back-end : 10.42.0.0/24
- GatewaySubnet : 10.42.255.0/27
- Groupe de ressources : TestRG4
- Emplacement : États-Unis Ouest
- Serveur DNS : 8.8.8.8
- Nom de la passerelle : VNet4GW
- IP publique : VNet4GWIP
- VPNType : RouteBased
- Connexion : VNet4toVNet1
- ConnectionType : VNet2VNet



### <a name="Step2"></a>Étape 2 : créer et configurer des TestVNet1

1. Déclarez vos variables

    Commencez par déclarer des variables. Cet exemple déclare les variables à l’aide de valeurs pour cet exercice. Dans la plupart des cas, vous devez remplacer les valeurs par les vôtres. Toutefois, vous pouvez utiliser ces variables si vous exécutez les étapes pour vous familiariser avec ce type de configuration. Modifiez les variables si nécessaire, puis copier et les coller dans votre console PowerShell.

        $Sub1 = "Replace_With_Your_Subcription_Name"
        $RG1 = "TestRG1"
        $Location1 = "East US"
        $VNetName1 = "TestVNet1"
        $FESubName1 = "FrontEnd"
        $BESubName1 = "Backend"
        $GWSubName1 = "GatewaySubnet"
        $VNetPrefix11 = "10.11.0.0/16"
        $VNetPrefix12 = "10.12.0.0/16"
        $FESubPrefix1 = "10.11.0.0/24"
        $BESubPrefix1 = "10.12.0.0/24"
        $GWSubPrefix1 = "10.12.255.0/27"
        $DNS1 = "8.8.8.8"
        $GWName1 = "VNet1GW"
        $GWIPName1 = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14 = "VNet1toVNet4"
        $Connection15 = "VNet1toVNet5"

2. Se connecter à votre abonnement

    Basculer en mode PowerShell pour utiliser les applets de commande du Gestionnaire de ressources. Ouvrez la console PowerShell et vous connecter à votre compte. Pour vous aider à vous connecter, utilisez l’exemple suivant :

        Login-AzureRmAccount

    Vérifiez les abonnements pour le compte.

        Get-AzureRmSubscription 

    Spécifier l’abonnement que vous souhaitez utiliser.

        Select-AzureRmSubscription -SubscriptionName $Sub1

3. Créer un nouveau groupe de ressources

        New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Créer les configurations de sous-réseau pour TestVNet1

    Cet exemple crée un réseau virtuel appelé TestVNet1 et trois sous-réseaux, un GatewaySubnet appelé, un frontal appelée et une seule appelée back-end. Lors de la substitution de valeurs, il est important de toujours nommer votre sous-réseau passerelle spécifiquement GatewaySubnet. Si vous lui quelque chose d’autre, la création de votre passerelle échouera. 

    L’exemple suivant utilise les variables que vous avez définie précédemment. Dans cet exemple, le sous-réseau de passerelle est à l’aide d’un /27. S’il est possible de créer un sous-réseau passerelle aussi petit que /29, nous vous conseillons de créer un sous-réseau plus grand qui inclut plusieurs adresses en sélectionnant au moins /28 ou /27. Ainsi, pour un nombre d’adresses suffisant pour s’adapter à des configurations supplémentaires possibles que vous pouvez par la suite. 

        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Créer des TestVNet1

        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Demande une adresse IP publique

    Demander une adresse IP publique à allouer à la passerelle que vous allez créer pour votre VNet. Notez que le AllocationMethod est dynamique. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. Elle est affectée dynamiquement à votre passerelle. 

        $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AllocationMethod Dynamic

7. Créer la configuration de la passerelle

    La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. L’exemple permet de créer la configuration de votre passerelle. 

        $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
        -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Création de la passerelle pour TestVNet1

    Dans cette étape, vous créez la passerelle réseau virtuels pour votre TestVNet1. Les configurations de VNet à VNet nécessitent un RouteBased VpnType. Création d’une passerelle peut prendre un certain temps (45 minutes ou plus).

        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
        -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-3---create-and-configure-testvnet4"></a>Étape 3 : créer et configurer des TestVNet4

Une fois que vous avez configuré TestVNet1, créer des TestVNet4. Suivez les étapes ci-dessous, remplacez les valeurs avec vos propres lorsque cela est nécessaire. Cette étape peut être effectuée au sein de la même session PowerShell car il est dans le même abonnement.

1. Déclarez vos variables

    Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

        $RG4 = "TestRG4"
        $Location4 = "West US"
        $VnetName4 = "TestVNet4"
        $FESubName4 = "FrontEnd"
        $BESubName4 = "Backend"
        $GWSubName4 = "GatewaySubnet"
        $VnetPrefix41 = "10.41.0.0/16"
        $VnetPrefix42 = "10.42.0.0/16"
        $FESubPrefix4 = "10.41.0.0/24"
        $BESubPrefix4 = "10.42.0.0/24"
        $GWSubPrefix4 = "10.42.255.0/27"
        $DNS4 = "8.8.8.8"
        $GWName4 = "VNet4GW"
        $GWIPName4 = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41 = "VNet4toVNet1"

    Avant de continuer, assurez-vous que vous êtes toujours connecté à 1 de l’abonnement.

2. Créer un nouveau groupe de ressources

        New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Créer les configurations de sous-réseau pour TestVNet4

        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Créer des TestVNet4

        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Demande une adresse IP publique

        $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AllocationMethod Dynamic

6. Créer la configuration de la passerelle

        $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Création de la passerelle TestVNet4

        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
        -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-4---connect-the-gateways"></a>Étape 4 – les passerelles de se connecter

1. Obtenir les deux passerelles de réseau virtuel

    Dans cet exemple, car les deux passerelles sont dans le même abonnement, cette étape peut être effectuée dans la même session de PowerShell.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Créer le TestVNet1 à TestVNet4 connexion

    Dans cette étape, vous créez la connexion à partir de TestVNet1 à TestVNet4. Vous verrez une clé partagée est référencée dans les exemples. Vous pouvez utiliser vos propres valeurs pour la clé partagée. Il est important que la clé partagée doit correspondre pour les deux connexions. Création d’une connexion peut prendre du temps court pour terminer.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
        -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Créer le TestVNet4 à TestVNet1 connexion

    Cette étape est similaire à celle présentée ci-dessus, mais vous créez la connexion à partir de TestVNet4 à TestVNet1. Assurez-vous que les clés partagées correspondent.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
        -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

    La connexion doit être établie au bout de quelques minutes.

4. Vérifiez votre connexion. Reportez-vous à la section [comment vérifier votre connexion](#verify).


## <a name="difsub"></a>Comment se connecter VNets qui se trouvent dans les différentes souscriptions


![diagramme de V2V](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

Dans ce scénario, nous connecter TestVNet1 et TestVNet5. TestVNet1 et TestVNet5 se trouvent dans un autre abonnement. Les étapes de cette configuration ajoutent une connexion supplémentaire de VNet-à-VNet pour vous connecter de TestVNet1 à TestVNet5. 

La différence est que certaines des étapes de configuration doivent être effectuées dans une session PowerShell distincte dans le cadre de la deuxième abonnement. En particulier lorsque les deux abonnements appartiennent à des organisations différentes. 

Les instructions continuent à partir de la procédure précédente ci-dessus. Vous devez terminer [étape 1](#Step1) et [l’étape 2](#Step2) pour créer et configurer des TestVNet1 et la passerelle VPN pour TestVNet1. Lorsque vous avez terminé l’étape 1 et l’étape 2, passez à l’étape 5 pour créer TestVNet5.

### <a name="step-5---verify-the-additional-ip-address-ranges"></a>Étape 5 - Vérifiez que les plages d’adresses IP supplémentaires

Il est important de s’assurer que l’espace d’adressage IP du nouveau réseau virtuel, TestVNet5, ne se chevauche pas avec n’importe lequel de vos VNet des plages ou des plages de passerelle de réseau local. 

Dans cet exemple, les réseaux virtuels peuvent appartenir à des organisations différentes. Pour cet exercice, vous pouvez utiliser les valeurs suivantes pour le TestVNet5 :

**Valeurs pour TestVNet5 :**

- VNet nom : TestVNet5
- Groupe de ressources : TestRG5
- Emplacement : Japon orientale
- TestVNet5 : 10.51.0.0/16 & 10.52.0.0/16
- Site Web frontal : 10.51.0.0/24
- Back-end : 10.52.0.0/24
- GatewaySubnet : 10.52.255.0.0/27
- Serveur DNS : 8.8.8.8
- Nom de la passerelle : VNet5GW
- IP publique : VNet5GWIP
- VPNType : RouteBased
- Connexion : VNet5toVNet1
- ConnectionType : VNet2VNet

**Valeurs supplémentaires pour TestVNet1 :**

- Connexion : VNet1toVNet5


### <a name="step-6---create-and-configure-testvnet5"></a>Étape 6 - créer et configurer des TestVNet5

Cette étape doit être effectuée dans le contexte d’un nouvel abonnement. Cette partie peut être effectuée par l’administrateur dans une organisation différente qui est propriétaire de l’abonnement.

1. Déclarez vos variables

    Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

        $Sub5 = "Replace_With_the_New_Subcription_Name"
        $RG5 = "TestRG5"
        $Location5 = "Japan East"
        $VnetName5 = "TestVNet5"
        $FESubName5 = "FrontEnd"
        $BESubName5 = "Backend"
        $GWSubName5 = "GatewaySubnet"
        $VnetPrefix51 = "10.51.0.0/16"
        $VnetPrefix52 = "10.52.0.0/16"
        $FESubPrefix5 = "10.51.0.0/24"
        $BESubPrefix5 = "10.52.0.0/24"
        $GWSubPrefix5 = "10.52.255.0/27"
        $DNS5 = "8.8.8.8"
        $GWName5 = "VNet5GW"
        $GWIPName5 = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51 = "VNet5toVNet1"

2. Se connecter à l’abonnement 5

    Ouvrez la console PowerShell et vous connecter à votre compte. Pour vous aider à vous connecter, utilisez l’exemple suivant :

        Login-AzureRmAccount

    Vérifiez les abonnements pour le compte.

        Get-AzureRmSubscription 

    Spécifier l’abonnement que vous souhaitez utiliser.

        Select-AzureRmSubscription -SubscriptionName $Sub5

3. Créer un nouveau groupe de ressources

        New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. Créer les configurations de sous-réseau pour TestVNet4
    
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. Créer des TestVNet5

        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
        -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Demande une adresse IP publique

        $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
        -Location $Location5 -AllocationMethod Dynamic


7. Créer la configuration de la passerelle

        $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. Création de la passerelle TestVNet5

        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
        -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### <a name="step-7---connecting-the-gateways"></a>Étape 7 - connexion de passerelles

Dans cet exemple, car les passerelles sont les différents abonnements, nous avons divisé cette étape en deux sessions de PowerShell marquées comme [abonnement 1] et [5 abonnement].

1. **[Abonnement 1]** Obtenir la passerelle réseau virtuel pour 1 d’abonnement

    Assurez-vous de vous connecter et connectez à 1 de l’abonnement.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

    Copier la sortie des éléments suivants et les envoyer à l’administrateur de 5 d’abonnement par courrier électronique ou d’une autre méthode.

        $vnet1gw.Name
        $vnet1gw.Id

    Ces deux éléments ont des valeurs semblables à l’exemple de sortie suivant :

        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Abonnement 5]** Obtenir la passerelle réseau virtuel pour 5 d’abonnement

    Veillez à vous connecter et de se connectez à 5 de l’abonnement.

        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

    Copier la sortie des éléments suivants et les envoyer à l’administrateur de 1 d’abonnement par courrier électronique ou d’une autre méthode.

        $vnet5gw.Name
        $vnet5gw.Id

    Ces deux éléments ont des valeurs semblables à l’exemple de sortie suivant :

        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Abonnement 1]** Créer le TestVNet1 à TestVNet5 connexion

    Dans cette étape, vous créez la connexion à partir de TestVNet1 à TestVNet5. La différence est que $vnet5gw ne peut être obtenu directement, car il est dans un autre abonnement. Vous devez créer un nouvel objet PowerShell avec les valeurs transmises par abonnement 1 dans les étapes ci-dessus. Utilisez l’exemple ci-dessous. Remplacez le nom, Id et clé partagée par vos propres valeurs. Il est important que la clé partagée doit correspondre pour les deux connexions. Création d’une connexion peut prendre du temps court pour terminer.

    Assurez-vous que vous vous connectez à 1 de l’abonnement. 
    
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Abonnement 5]** Créer le TestVNet5 à TestVNet1 connexion

    Cette étape est similaire à celle présentée ci-dessus, mais vous créez la connexion à partir de TestVNet5 à TestVNet1. Le même processus de création d’un objet PowerShell basé sur les valeurs obtenues à partir de 1 d’abonnement s’applique ici également. Dans cette étape, assurez-vous que les clés partagées correspondent.

    Assurez-vous que vous vous connectez à 5 de l’abonnement.

        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="verify"></a>Comment faire pour vérifier une connexion


[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="next-steps"></a>Étapes suivantes

- Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à des réseaux virtuels. Étapes, consultez [créer un ordinateur virtuel](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
- Pour plus d’informations sur BGP, consultez la [Vue d’ensemble du protocole BGP](vpn-gateway-bgp-overview.md) et [comment configurer le protocole BGP](vpn-gateway-bgp-resource-manager-ps.md). 

