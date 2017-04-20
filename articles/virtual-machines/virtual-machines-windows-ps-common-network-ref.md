<properties
    pageTitle="Commandes de réseau commun PowerShell pour les machines virtuelles | Microsoft Azure"
    description="Des commandes PowerShell courants pour vous aider à démarrer la création d’un réseau virtuel et ses ressources associées pour les machines virtuelles."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="common-network-azure-powershell-commands-for-vms"></a>Commandes de PowerShell d’Azure réseau courantes pour les machines virtuelles

Si vous souhaitez créer un ordinateur virtuel, vous devez créer un [réseau virtuel](../virtual-network/virtual-networks-overview.md) ou savoir sur un réseau virtuel dans lequel la machine virtuelle peut être ajoutée. En règle générale, lorsque vous créez un ordinateur virtuel, vous devez également envisager de créer les ressources décrites dans cet article.

Pour plus d’informations sur l’installation de la version la plus récente de PowerShell d’Azure, en sélectionnant votre abonnement et votre compte d’ouverture de session, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

## <a name="create-network-resources"></a>Créer des ressources de réseau

Tâche | Commande 
-------------- | -------------------------
Créer des configurations de sous-réseau | $subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -nom « subnet_name » AddressPrefix - XX. X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig-nom « subnet_name » AddressPrefix - XX. X.X.X/XX<BR><BR>Un réseau typique peut avoir un sous-réseau pour un [équilibrage de la charge de face à internet](../load-balancer/load-balancer-internet-overview.md) et un sous-réseau distinct pour un [équilibreur de charge interne](../load-balancer/load-balancer-internal-overview.md). |
Créer un réseau virtuel | $vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -nom_groupe_ressource « nom « virtual_network_name » - ResourceGroupName »-emplacement « location_name » AddressPrefix - XX. X.X.X/XX-sous-réseau $subnet1, $subnet2
Test pour un nom de domaine unique | [Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) - DomainQualifiedName « nom_domaine »-emplacement « location_name »<BR><BR>Vous pouvez spécifier un nom de domaine DNS d’une [ressource d’IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md), qui crée un mappage pour domainname.location.cloudapp.azure.com à l’adresse IP publique dans les serveurs DNS d’Azure gérés. Le nom peut contenir uniquement des lettres, des chiffres et des traits d’union. Le premier et le dernier caractère doit être une lettre ou le numéro et le nom de domaine doivent être uniques dans son emplacement d’Azure. Si la valeur retournée est **True** , le nom proposé est globalement unique.
Créer une adresse IP publique | $pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -DomainNameLabel-« nom_groupe_ressource » nom « ip_address_name » - ResourceGroupName « nom_domaine »-emplacement « location_name » - AllocationMethod dynamique<BR><BR>L’adresse IP publique utilise le nom de domaine que vous déjà Testez et êtes utilisé par la configuration de la partie frontale de l’équilibreur de charge.
Créer la configuration IP d’un site Web frontal | $frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -nom « frontend_ip_name » PublicIpAddress - $pip<BR><BR>La configuration de site Web frontal inclut l’adresse IP publique que vous avez créé précédemment pour le trafic réseau entrant.
Créer un pool d’adresses back-end | $beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -nom « backend_pool_name »<BR><BR>Fournit des adresses internes pour le back-end de l’équilibreur de charge sont accessibles via une interface réseau.
Créer une sonde | $healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -nom « probe_name » - RequestPath 'HealthProbe.aspx'-protocole http-Port 80 - IntervalInSeconds 15 ProbeCount - 2<BR><BR>Contient des sondes de santé permettant de vérifier la disponibilité des machines virtuelles des instances dans le pool d’adresses back-end.
Créer une règle d’équilibrage de la charge | $lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -nom HTTP - FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-sonde $healthProbe-80 de protocole Tcp - FrontendPort BackendPort - 80<BR><BR>Contient des règles qui affectent un port public de l’équilibreur de charge à un port dans le pool d’adresses back-end.
Créer une règle NAT entrante | $inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -nom « nom_règle » FrontendIpConfiguration - $frontendIP-3441 de protocole TCP - FrontendPort BackendPort - 3389<BR><BR>Contient des règles de mappage d’un port public de l’équilibreur de charge à un port pour une machine virtuelle spécifique dans le pool d’adresses back-end.
Créer un équilibreur de charge | $loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) - ResourceGroupName « nom_groupe_ressource »-nom « load_balancer_name »-emplacement « location_name » FrontendIpConfiguration - $frontendIP - InboundNatRule $inboundNATRule LoadBalancingRule - $lbRule - BackendAddressPool $beAddressPool-sonde $healthProbe
Créez une interface réseau | $nic1 = [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) - ResourceGroupName « nom_groupe_ressource »-nom « Nom_interface_réseau »-emplacement « location_name » PrivateIpAddress - XX. X.x.X-sous-réseau subnet2 LoadBalancerBackendAddressPool - $loadBalancer.BackendAddressPools[0] LoadBalancerInboundNatRule - $loadBalancer.InboundNatRules[0]<BR><BR>Créez une interface réseau à l’aide de l’adresse IP publique et le sous-réseau de réseau virtuel que vous avez créé précédemment.
    
## <a name="get-information-about-network-resources"></a>Obtenir des informations sur les ressources réseau

Tâche | Commande 
-------------- | -------------------------
Liste des réseaux virtuels | [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) - ResourceGroupName « nom_groupe_ressource »<BR><BR>Répertorie tous les réseaux virtuels dans le groupe de ressources.
Obtenir des informations sur un réseau virtuel | Get-AzureRmVirtualNetwork-nom_groupe_ressource « nom « virtual_network_name » - ResourceGroupName »
Liste des sous-réseaux d’un réseau virtuel | Get-AzureRmVirtualNetwork-nommer « virtual_network_name » - ResourceGroupName « nom_groupe_ressource » & #124 ; Sélectionnez les sous-réseaux
Obtenir des informations sur un sous-réseau | [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -nom « subnet_name » VirtualNetwork - $vnet<BR><BR>Obtient des informations sur le sous-réseau du réseau virtuel spécifié. La valeur de $vnet représente l’objet renvoyé par Get-AzureRmVirtualNetwork.
Liste des adresses IP | [Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) - ResourceGroupName « nom_groupe_ressource »<BR><BR>Répertorie les adresses IP publiques dans le groupe de ressources.
Liste les équilibreurs de charge | [Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) - ResourceGroupName « nom_groupe_ressource »<BR><BR>Répertorie tous les équilibreurs de charge du groupe de ressources.
Interfaces réseau de liste | [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) - ResourceGroupName « nom_groupe_ressource »<BR><BR>Répertorie toutes les interfaces réseau dans le groupe de ressources.
Obtenir des informations sur une interface réseau | Get-AzureRmNetworkInterface-nom_groupe_ressource « nom « Nom_interface_réseau » - ResourceGroupName »<BR><BR>Obtient des informations sur une interface réseau spécifique.
Obtenir la configuration IP d’une interface réseau | [Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -nom « ipconfiguration_name » - NetworkInterface $nic<BR><BR>Obtient des informations sur la configuration IP de l’interface réseau spécifiée. La valeur de $nic représente l’objet renvoyé par Get-AzureRmNetworkInterface.

## <a name="manage-network-resources"></a>Gérer les ressources réseau

Tâche | Commande 
-------------- | -------------------------
Ajouter un sous-réseau à un réseau virtuel | [Ajouter-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) - AddressPrefix XX. X.X.X/XX-nom « subnet_name » VirtualNetwork - $vnet<BR><BR>Ajoute un sous-réseau à un réseau virtuel existant. La valeur de $vnet représente l’objet renvoyé par Get-AzureRmVirtualNetwork.
Supprimer un réseau virtuel | [Supprimer-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -nom_groupe_ressource « nom « virtual_network_name » - ResourceGroupName »<BR><BR>Supprime le réseau virtuel spécifié dans le groupe de ressources.
Supprimer une interface réseau | [Supprimer-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -nom_groupe_ressource « nom « Nom_interface_réseau » - ResourceGroupName »<BR><BR>Supprime l’interface réseau spécifiée dans le groupe de ressources.
Supprimer un équilibreur de charge | [Supprimer-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -nom_groupe_ressource « nom « load_balancer_name » - ResourceGroupName »<BR><BR>Supprime l’équilibreur de charge spécifié dans le groupe de ressources.
Supprimer une adresse IP publique | [Supprimer-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-nom_groupe_ressource « nom « ip_address_name » - ResourceGroupName »<BR><BR>Supprime l’adresse IP publique spécifiée dans le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

- Utilisation de l’interface réseau que vous venez de créer lorsque vous [créez un ordinateur virtuel](virtual-machines-windows-ps-create.md).
- Découvrez comment vous pouvez [créer un ordinateur virtuel avec plusieurs interfaces réseau](../virtual-network/virtual-networks-multiple-nics.md).
