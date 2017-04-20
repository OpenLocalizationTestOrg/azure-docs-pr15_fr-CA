<properties
   pageTitle="Créer à un équilibreur de charge pour Internet dans le Gestionnaire de ressources à l’aide de PowerShell | Microsoft Azure"
   description="Apprenez à créer un équilibreur de charge faisant face à Internet dans le Gestionnaire de ressources à l’aide de PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
   ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="get-started"></a>Création d’un équilibreur de charge faisant face à Internet dans le Gestionnaire de ressources à l’aide de PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [apprendre à créer un équilibreur de charge faisant face à Internet en utilisant le modèle de déploiement classique](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Déploiement de la solution à l’aide de PowerShell d’Azure

Les procédures suivantes expliquent comment créer un équilibreur de charge via Internet à l’aide du Gestionnaire de ressources Azure avec PowerShell. Avec le Gestionnaire de ressources d’Azure, chaque ressource est créé et configuré individuellement et mettez ensuite ensemble pour créer un équilibreur de charge.

Vous devez créer et configurer des objets suivants pour déployer un équilibreur de charge :

- Configuration de IP front-end : contient les adresses IP (PIP) publiques pour le trafic réseau entrant.
- Pool d’adresses de back-end : contient les interfaces réseau (cartes réseau) pour les ordinateurs virtuels recevoir le trafic réseau à partir de l’équilibreur de charge.
- Règles d’équilibrage de la charge : contient des règles permettant de mapper un port public de l’équilibreur de charge à un port dans le pool d’adresses de back-end.
- NAT règles de trafic entrant : contient des règles permettant de mapper un port public de l’équilibreur de charge à un port pour une machine virtuelle spécifique dans le pool d’adresses de back-end.
- Sondes : contient des sondes de santé permettant de vérifier la disponibilité des instances de machine virtuelle dans le pool d’adresses de back-end.

Pour plus d’informations, consultez [Le Gestionnaire de ressources Azure prise en charge de l’équilibrage de la charge](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurer PowerShell pour utiliser le Gestionnaire de ressources

Assurez-vous que vous avez la dernière version de production du module Gestionnaire de ressources Azure pour PowerShell :

1. Se connecter à Azure.

        Login-AzureRmAccount

    Entrez vos informations d’identification lorsque vous y êtes invité.

2. Vérifiez les abonnements pour le compte.

        Get-AzureRmSubscription

3. Choisir vos abonnements Azure à utiliser.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Créer un groupe de ressources. (Ignorez cette étape si vous utilisez un groupe de ressources existant).

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Créer un réseau virtuel et une adresse IP publique pour le pool IP front-end

1. Créer un sous-réseau et un réseau virtuel.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Créer une Azure publique ressource adresse IP, nommée **PublicIP**, pour être utilisé par un pool frontal d’IP avec le nom DNS **loadbalancernrp.westus.cloudapp.azure.com**. La commande suivante utilise le type de ventilation statique.

        $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp

    >[AZURE.IMPORTANT]L’équilibreur de charge utilise le nom de domaine de l’adresse IP publique en tant que préfixe pour son nom de domaine complet. Cela est différent du modèle de déploiement classique, qui utilise le service de cloud comme nom de domaine complet de l’équilibreur de charge.
    >Dans cet exemple, le nom de domaine complet est **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Créer un pool d’IP front-end et un pool d’adresses de back-end

1. Créez un pool IP frontaux nommé **Frontend-LB** qui utilise la ressource **PublicIp** .

        $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP

2. Créer un pool d’adresses principal nommé **livres-back-end**.

        $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Créer des règles NAT, une règle d’équilibrage de la charge, une sonde et un équilibreur de charge

Cet exemple crée les éléments suivants :

- Une règle TAR pour traduire tout le trafic entrant sur le port 3441 au port 3389
- Une règle TAR pour traduire tout le trafic entrant sur le port 3442 au port 3389
- Une règle de sonde pour vérifier l’état de santé sur une page nommée **HealthProbe.aspx**
- Une règle d’équilibrage de la charge pour équilibrer le trafic entrant sur le port 80 au port 80 sur les adresses dans le pool de back-end
- Un équilibreur de charge qui utilise tous ces objets

Suivez ces étapes :

1. Créer des règles NAT.

        $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

        $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

2. Créer une sonde de santé. Il existe deux façons de configurer une sonde :

    Sonde HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    Sonde TCP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

3. Créer une règle d’équilibrage de la charge.

        $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

4. Créer l’équilibreur de charge à l’aide d’objets créés précédemment.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## <a name="create-nics"></a>Créer des cartes d’interface réseau

Créer des interfaces de réseau (ou modifier une existante), puis associez les règles NAT, les règles d’équilibrage de la charge et les sondes :

1. Obtenir le réseau virtuel et un sous-réseau de réseau virtuel, dans lequel les cartes réseau doivent être créés.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Créer une carte d’interface réseau nommé **livres-nic1-être**et l’associer à la première règle NAT et le pool d’adresses de back-end de premier (et unique).

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

3. Créer une carte d’interface réseau nommé **être lb-carte réseau 2**et l’associer à la deuxième règle NAT et le pool d’adresses de back-end de premier (et unique).

        $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

4. Vérifiez les cartes réseau.

        $backendnic1

    Sortie attendue :

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Utilisez le `Add-AzureRmVMNetworkInterface` applet de commande pour affecter les cartes réseau pour ordinateurs virtuels différents.

## <a name="create-a-virtual-machine"></a>Créer un ordinateur virtuel

Pour obtenir des instructions sur la création d’une machine virtuelle et l’affectation d’une carte réseau, reportez-vous à la section [créer un Azure VM à l’aide de PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Ajoutez l’interface réseau pour l’équilibrage de charge

1. Récupérer l’équilibreur de charge d’Azure.

    Charger les ressources d’équilibrage de la charge dans une variable (si vous n’avez pas fait). La variable est appelée **$lb**. Utilisez le même nom de la ressource d’équilibrage de la charge que vous avez créé précédemment.

        $lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

2. Configuration de charge back-end à une variable.

        $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

3. Charger l’interface de réseau déjà créés dans une variable. Le nom de la variable est **$nic**. Le nom de l’interface réseau est le même que celui de l’exemple précédent.

        $nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

4. Modifier la configuration du back-end sur l’interface réseau.

        $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

5. Enregistrer l’objet interface réseau.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Après l’ajout d’une interface réseau dans le pool de back-end d’équilibrage de la charge, il commence à recevoir le trafic réseau basée sur les règles d’équilibrage de la charge pour cette ressource d’équilibrage de la charge.

## <a name="update-an-existing-load-balancer"></a>Mise à jour d’un équilibreur de charge existant

1. À l’aide de l’équilibrage de charge à partir de l’exemple précédent, affecter un objet d’équilibreur de charge pour la variable **$slb** à l’aide de `Get-AzureLoadBalancer`.

        $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

2. Dans l’exemple suivant, vous ajoutez une règle NAT entrante--en utilisant le port 81 dans le pool frontal et port 8181 pour le pool principal--à un équilibreur de charge existant.

        $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP

3. Enregistrer la nouvelle configuration à l’aide de `Set-AzureLoadBalancer`.

        $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Supprimer un équilibreur de charge

Utilisez la commande `Remove-AzureLoadBalancer` supprimer un équilibreur de charge de créé précédemment nommé **NRP-LB** dans un groupe de ressources appelé **NRP-RG**.

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Vous pouvez utiliser le commutateur facultatif **-Force** pour éviter l’invite pour la suppression.

## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un équilibreur de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)
