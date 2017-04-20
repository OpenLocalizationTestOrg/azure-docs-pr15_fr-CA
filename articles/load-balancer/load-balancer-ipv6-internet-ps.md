<properties
    pageTitle="Créer un Internet face à équilibrage de la charge avec IPv6 à l’aide de PowerShell pour le Gestionnaire de ressources | Microsoft Azure"
    description="Apprenez à créer un équilibreur de charge avec IPv6 à l’aide de PowerShell pour le Gestionnaire de ressources d’ouvert sur Internet"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="IPv6, équilibrage de la charge d’azure, double pile, adresse ip publique, ipv6 natif, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Démarrer la création d’un équilibreur de charge avec IPv6 à l’aide de PowerShell pour le Gestionnaire de ressources d’ouvert sur Internet

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [CLI Azure](./load-balancer-ipv6-internet-cli.md)
- [Modèle](./load-balancer-ipv6-internet-template.md)

Un équilibreur de charge Azure est un équilibreur de charge de couche-4 (TCP, UDP). L’équilibreur de charge fournit une haute disponibilité en répartissant le trafic entrant sur les instances de service en bon état dans les services en nuage ou de machines virtuelles dans un jeu d’équilibrage de la charge. Azure équilibreur de charge peut également présenter ces services sur plusieurs ports, plusieurs adresses IP ou les deux.

## <a name="example-deployment-scenario"></a>Exemple de scénario de déploiement

Le diagramme suivant illustre la solution déployée dans cet article d’équilibrage de la charge.

![Scénario d’équilibrage de la charge](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

Dans ce scénario, vous allez créer les ressources Azure suivantes :

- un équilibreur de charge faisant face à Internet IPv4 et une adresse IP publique de IPv6
- deux charge équilibrage de règles pour mapper la VIP public aux points de terminaison privés
- une disponibilité la valeur qui contient deux ordinateurs virtuels
- deux machines virtuelles (VM)
- une interface de réseau virtuel pour chaque ordinateur virtuel avec des adresses IPv4 et IPv6 affectées

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Déploiement de la solution à l’aide de PowerShell Azure

Les étapes suivantes indiquent comment créer un équilibreur de charge à l’aide du Gestionnaire de ressources Azure avec PowerShell d’ouvert sur Internet. Avec le Gestionnaire de ressources d’Azure, chaque ressource est créé et configuré individuellement, puis placer ensemble pour créer une ressource.

Pour déployer un équilibreur de charge, vous créez et configurez les objets suivants :

- La configuration IP front-end - contient les adresses IP publiques pour le trafic réseau entrant.
- Pool d’adresses principal - contient des interfaces réseau (cartes réseau) pour les ordinateurs virtuels recevoir le trafic réseau à partir de l’équilibreur de charge.
- Règles d’équilibrage de la charge - contient les règles de mappage d’un port public de l’équilibreur de charge au port dans le pool d’adresses de back-end.
- NAT règles de trafic entrant - contient les règles de mappage d’un port public de l’équilibreur de charge à un port pour une machine virtuelle spécifique dans le pool d’adresses de back-end.
- Sondes - contient les sondes de santé permettant de vérifier la disponibilité des machines virtuelles des instances dans le pool d’adresses de back-end.

Pour plus d’informations, consultez [Le Gestionnaire de ressources Azure prise en charge de l’équilibrage de la charge](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurer PowerShell pour utiliser le Gestionnaire de ressources

Assurez-vous que vous avez la dernière version de production du module Gestionnaire de ressources Azure pour PowerShell.

1. Signe dans Azure

        Login-AzureRmAccount

    Entrez vos informations d’identification lorsque vous y êtes invité.

2. Vérifier les abonnements pour le compte

        Get-AzureRmSubscription

3. Choisir vos abonnements Azure à utiliser.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Créer un groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Créer un réseau virtuel et une adresse IP publique pour le pool IP front-end

1. Créer un réseau virtuel à un sous-réseau.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        $vnet = New-AzureRmvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Créer adresse IP publique d’Azure ressources (PIP) pour le pool d’adresses IP front-end.

        $publicIPv4 = New-AzureRmPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
        $publicIPv6 = New-AzureRmPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6

    >[AZURE.IMPORTANT] L’équilibreur de charge utilise le nom de domaine de l’adresse IP publique en tant que préfixe pour son nom de domaine complet. Dans cet exemple, les noms de domaine complets sont *lbnrpipv4.westus.cloudapp.azure.com* et *lbnrpipv6.westus.cloudapp.azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Créer une configuration IP de front-end et Back-End d’un Pool d’adresses

1. Créer la configuration d’adresses frontal qui utilise les adresses IP publiques que vous avez créé.

        $FEIPConfigv4 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
        $FEIPConfigv6 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6

2. Créer des pools d’adresses de back-end.

        $backendpoolipv4 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
        $backendpoolipv6 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"


## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Créer des livres règles NAT règles, une sonde et un équilibreur de charge

Cet exemple crée les éléments suivants :

- une règle TAR pour traduire tout le trafic entrant sur le port 443 vers le port 4443
- une règle d’équilibrage de la charge pour équilibrer le trafic entrant sur le port 80 au port 80 sur les adresses dans le pool principal.
- une règle d’équilibrage de la charge pour permettre la connexion RDP pour les ordinateurs virtuels sur le port 3389.
- une règle de sonde pour vérifier l’état de santé sur une page nommée *HealthProbe.aspx* ou un service sur le port 8080
- un équilibreur de charge qui utilise tous ces objets

1. Créer des règles NAT.

        $inboundNATRule1v4 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
        $inboundNATRule1v6 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443

2. Créer une sonde de santé. Il existe deux façons de configurer une sonde :

    Sonde HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    ou sonde TCP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
        $RDPprobe = New-AzureRmLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2


    Pour cet exemple, nous allons utiliser les sondes TCP.

3. Créer une règle d’équilibrage de la charge.

        $lbrule1v4 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $lbrule1v6 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $RDPrule = New-AzureRmLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389

4. Créer l’équilibreur de charge à l’aide d’objets créés précédemment.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule

## <a name="create-nics-for-the-back-end-vms"></a>Créer des cartes d’interface réseau pour les ordinateurs virtuels du back-end

1. Obtenir le réseau virtuel et le sous-réseau de réseau virtuel, dans lequel les cartes réseau doivent être créés.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Créer des cartes d’interface réseau et les configurations IP pour les ordinateurs virtuels.

        $nic1IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
        $nic1IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
        $nic1 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

        $nic2IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
        $nic2IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
        $nic2 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Créer des ordinateurs virtuels et affecter les cartes réseau qui vient d’être créés.

Pour plus d’informations sur la création d’une machine virtuelle, consultez [créer et de préconfigurer un ordinateur virtuel de Windows avec le Gestionnaire de ressources et Azure PowerShell](..\virtual-machines\virtual-machines-windows-ps-create.md)

1. Créer un compte de la valeur de disponibilité et de stockage

        New-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
        $availabilitySet = Get-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
        New-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName $LRS
        $CreatedStorageAccount = Get-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'

2. Créer chaque machine virtuelle et affecter le précédent créé des cartes réseau

        $mySecureCredentials= Get-Credential -Message “Type the username and password of the local administrator account.”

        $vm1 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm1 = Set-AzureRmVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm1 = Set-AzureRmVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm1 = Add-AzureRmVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
        $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
        $vm1 = Set-AzureRmVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

        $vm2 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm2 = Set-AzureRmVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm2 = Set-AzureRmVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm2 = Add-AzureRmVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
        $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
        $vm2 = Set-AzureRmVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2

## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un équilibreur de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)
