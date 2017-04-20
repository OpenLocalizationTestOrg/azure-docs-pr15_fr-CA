<properties
   pageTitle="Créer un équilibreur de charge interne dans le Gestionnaire de ressources à l’aide de PowerShell | Microsoft Azure"
   description="Apprenez à créer un équilibreur de charge interne à l’aide de PowerShell dans le Gestionnaire de ressources"
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

# <a name="create-an-internal-load-balancer-using-powershell"></a>Créer un équilibreur de charge interne à l’aide de PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


Les étapes suivantes expliquent comment créer un équilibreur de charge interne à l’aide du Gestionnaire de ressources Azure avec PowerShell. Avec le Gestionnaire de ressources d’Azure, les éléments pour créer un équilibreur de charge interne sont configurés individuellement et ensuite combinés pour créer un équilibreur de charge.

Vous devez créer et configurer des objets suivants pour déployer un équilibreur de charge :

- Avant la configuration IP de fin - va configurer l’adresse IP privée pour le trafic réseau entrant
- Pool d’adresses back-end - allez configurer les interfaces réseau qui recevront le trafic d’équilibrage de la charge en provenance de pool IP de front-end
- Équilibrage de règles - source et la configuration de voie locale de l’équilibreur de charge.
- Sondes - configure la sonde d’état de santé pour les instances de la Machine virtuelle.
- NAT règles de trafic entrant - configure les règles de port pour accéder directement à une des instances de la Machine virtuelle.

Vous pouvez obtenir plus d’informations sur la charge des composants équilibreur avec le Gestionnaire de ressources Azure à [Azure Resource Manager prend en charge de l’équilibrage de la charge](load-balancer-arm.md).

Les étapes suivantes expliquent comment configurer un équilibreur de charge entre les deux machines virtuelles.


## <a name="setup-powershell-to-use-resource-manager"></a>Le programme d’installation de PowerShell pour utiliser le Gestionnaire de ressources

Assurez-vous que vous disposez de la dernière version de production du module Azure pour PowerShell et que le programme d’installation de PowerShell correctement accéder à votre abonnement Azure.

### <a name="step-1"></a>Étape 1

        Login-AzureRmAccount

### <a name="step-2"></a>Étape 2

Vérifier les abonnements pour le compte

        Get-AzureRmSubscription

Vous devez authentifier vos informations d’identification.<BR>

### <a name="step-3"></a>Étape 3

Choisir vos abonnements Azure à utiliser. <BR>


        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="create-resource-group-for-load-balancer"></a>Créer un groupe de ressources pour l’équilibrage de la charge

### <a name="step-4"></a>Étape 4

Créer un nouveau groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Le Gestionnaire de ressources Azure requiert que tous les groupes de ressources spécifient un emplacement. Il est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Assurez-vous que toutes les commandes pour créer un équilibreur de charge utilise le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « NRP-RG » et l’emplacement « Ouest nous ».

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>Créez le réseau virtuel et une adresse IP privée pour le groupe IP de front-end


### <a name="step-1"></a>Étape 1

Crée un sous-réseau pour le réseau virtuel et l’assigne à la variable $backendSubnet

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Créer un réseau virtuel :

    $vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Crée le réseau virtuel et ajoute le sous-réseau être lb-sous-réseau au réseau virtuel NRPVNet et l’assigne à la variable $vnet



## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>Créer le pool d’IP Front end et back-end pool d’adresses

Configuration d’un pool d’IP front-end du entrant charge équilibrage réseau du trafic et back-end pool d’adresses à la charge de réception équilibre le trafic.

### <a name="step-1"></a>Étape 1

Créer un pool d’IP front-end à l’aide de l’adresse IP privée 10.0.2.5 pour le 10.0.2.0/24 sous-réseau qui sera le point de terminaison du trafic réseau entrant.

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### <a name="step-2"></a>étape 2

Configurer un pool d’adresses back-end utilisé pour recevoir le trafic entrant à partir du pool de front-end IP :

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>Créer des règles de livres, des règles NAT, sonde et l’équilibrage de charge

Après avoir créé le pool IP front-end et le back-end pool d’adresses, vous devez créer les règles qui appartient à la ressource d’équilibrage de la charge :

### <a name="step-1"></a>Étape 1

    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

     $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


L’exemple ci-dessus crée les éléments suivants :

- Règle NAT qui passe tout le trafic entrant au port 3441 au port 3389.
- une deuxième règle NAT qui passe tout le trafic entrant au port 3442 au port 3389.
- une règle d’équilibrage de la charge qui chargera équilibrer le trafic de tout trafic entrant sur le port public 80 au port local 80 dans le pool d’adresses back-end.
- une règle de sonde qui vérifie l’état de santé de chemin d’accès « HealthProbe.aspx »



### <a name="step-2"></a>Étape 2

Créer l’équilibreur de charge additionnant tous les objets (règles NAT, les règles d’équilibrage de la charge, les configurations de sonde) :

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe


## <a name="create-network-interfaces"></a>Créer des interfaces de réseau

Après avoir créé l’équilibreur de charge interne, vous devez définir les interfaces réseau recevront le trafic d’équilibrage de la charge, règles NAT et sonde entrant. Dans ce cas, l’interface réseau est configurée individuellement et peut être affecté à une machine virtuelle plus tard.


### <a name="step-1"></a>Étape 1


Obtenir le réseau virtuel des ressources et sous-réseau pour créer des interfaces de réseau :

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet


Cette étape crée une interface de réseau qui appartiennent au pool de back-end d’équilibrage de la charge et associer la première règle NAT pour RDP pour cette interface réseau :

    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### <a name="step-2"></a>Étape 2

Créez une seconde interface réseau appelée LB-Nic2-être :

Cette étape crée une deuxième interface réseau, affectation vers le même pool de back-end d’équilibrage de la charge et l’association de la deuxième règle NAT créé pour RDP :

     $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

Le résultat final affiche le texte suivant :

    $backendnic1

Sortie attendue :

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
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
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>Étape 3

Pour affecter la carte réseau à un ordinateur virtuel, utilisez la commande Add-AzureRmVMNetworkInterface.

Vous trouverez les instructions étape par étape pour créer un ordinateur virtuel et l’attribuer à une carte réseau de la documentation : [créer un Azure VM à l’aide de PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

Si vous disposez déjà d’un ordinateur virtuel créé, vous pouvez ajouter l’interface de réseau avec les étapes suivantes :

#### <a name="step-1"></a>Étape 1

Charger les ressources d’équilibrage de la charge dans une variable (si vous n’avez pas fait). La variable utilisée est appelée $lb et utilisez les mêmes noms de la ressource d’équilibrage de la charge créé ci-dessus.

    $lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### <a name="step-2"></a>Étape 2

Charger la configuration du serveur principal à une variable.

    $backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### <a name="step-3"></a>Étape 3

Charger l’interface de réseau déjà créés dans une variable. le nom de la variable utilisé est la carte de réseau $. Le nom de l’interface réseau utilisé est celle de l’exemple ci-dessus.

    $nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### <a name="step-4"></a>Étape 4

Modifier la configuration du back-end sur l’interface réseau.

    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### <a name="step-5"></a>Étape 5

Enregistrer l’objet interface réseau.

    Set-AzureRmNetworkInterface -NetworkInterface $nic

Après l’ajout d’une interface réseau dans le pool de back-end d’équilibrage de la charge, il commence à recevoir le trafic réseau basée sur la règles de cette ressource d’équilibrage de la charge d’équilibrage de la charge.

## <a name="update-an-existing-load-balancer"></a>Mise à jour d’un équilibreur de charge existant


### <a name="step-1"></a>Étape 1

Affecter des objet d’équilibrage de la charge à l’aide de l’équilibrage de charge à partir de l’exemple ci-dessus, à la variable $slb à l’aide de Get-AzureRmLoadBalancer

    $slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### <a name="step-2"></a>Étape 2

Dans l’exemple suivant, vous allez ajouter une nouvelle règle de trafic entrant de NAT pour le pool de back-end pour un équilibrage de la charge existante à l’aide de port 81 dans le front-end et le port 8181

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### <a name="step-3"></a>Étape 3

Enregistrer la nouvelle configuration à l’aide de Set-AzureLoadBalancer

    $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Supprimer un équilibreur de charge

La commande Remove-AzureRmLoadBalancer permet de supprimer un équilibreur de charge de créé précédemment nommé « NRP-LB » dans un groupe de ressources appelé « NRP-RG »

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Vous pouvez utiliser le commutateur - Force pour éviter l’invite pour la suppression.



## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)