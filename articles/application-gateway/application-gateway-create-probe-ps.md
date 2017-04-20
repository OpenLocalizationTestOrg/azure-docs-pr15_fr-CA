<properties
   pageTitle="Créer une sonde personnalisée pour la passerelle d’Application à l’aide de PowerShell dans le Gestionnaire de ressources | Microsoft Azure"
   description="Apprenez à créer une sonde personnalisée pour la passerelle d’Application à l’aide de PowerShell dans le Gestionnaire de ressources"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Créer une sonde personnalisée pour la passerelle d’Application Azure à l’aide de PowerShell pour le Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Azure portal](application-gateway-create-probe-portal.md)
- [Azure PowerShell de gestionnaire de ressources](application-gateway-create-probe-ps.md)
- [Azure PowerShell classique](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](application-gateway-create-probe-classic-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>Étape 1

Connexion-AzureRmAccount permet de s’authentifier.

    Login-AzureRmAccount

### <a name="step-2"></a>Étape 2

Vérifiez les abonnements pour le compte.

    Get-AzureRmSubscription

### <a name="step-3"></a>Étape 3

Choisir vos abonnements Azure à utiliser. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Étape 4

Créer un groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Le Gestionnaire de ressources Azure requiert que tous les groupes de ressources spécifient un emplacement. Cet emplacement est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Assurez-vous que toutes les commandes pour créer une passerelle d’application utilisent le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG » et l’emplacement « Ouest nous ».

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle d’application

Les étapes suivantes créent un réseau virtuel et un sous-réseau pour la passerelle d’application.

### <a name="step-1"></a>Étape 1


Assignez le 10.0.0.0/24 plage adresse à une variable de sous-réseau à utiliser pour créer un réseau virtuel.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Étape 2

Créer un réseau virtuel nommé « appgwvnet » dans la ressource groupe « appgw-rg » pour la région Ouest américaine avec le préfixe 10.0.0.0/16 de sous-réseau 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### <a name="step-3"></a>Étape 3

Affecter une variable de sous-réseau pour les étapes suivantes, laquelle créer une passerelle d’application.

    $subnet = $vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Créer une adresse IP publique pour la configuration frontale


Créez une ressource IP publique « publicIP01 » dans la ressource groupe « appgw-rg » pour la région Ouest.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>Créer un objet de configuration d’application passerelle avec une sonde personnalisée

Permet de paramétrer tous les éléments de configuration avant de créer la passerelle d’application. Les étapes suivantes créent les éléments de configuration qui sont nécessaires pour une ressource d’application passerelle.

### <a name="step-1"></a>Étape 1

Créer la configuration IP d’une passerelle d’application nommée « gatewayIP01 ». Lors du démarrage de la passerelle d’Application, il sélectionne une adresse IP du sous-réseau configuré et route le trafic réseau vers les adresses IP dans le pool d’IP back-end. Gardez à l’esprit que chaque instance prend une adresse IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### <a name="step-2"></a>Étape 2


Configurer le pool d’adresses IP principal nommé « pool01 » avec des adresses IP « 134.170.185.46, 134.170.188.221,134.170.185.50 ». Ces valeurs sont les adresses IP qui reçoivent du trafic réseau qui provient d’un point de terminaison IP front-end. Vous remplacez les adresses IP ci-dessus pour ajouter vos propres points de terminaison application IP adresse.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### <a name="step-3"></a>Étape 3


La sonde personnalisé est configurée dans cette étape.

Les paramètres utilisés sont les suivants :

- **Intervalle** - configure les vérifications d’intervalle de sonde en secondes.
- **Délai d’attente** - définit le délai d’attente de sonde pour une vérification de la réponse HTTP.
- **Nom d’hôte et le chemin d’accès** - chemin d’accès URL complète qui est appelée par la passerelle d’Application afin de déterminer l’état de l’instance. Par exemple, si vous avez un site Web http://contoso.com/, la sonde personnalisée peut alors être configurée pour « http://contoso.com/path/custompath.htm » pour les vérifications de sondages d’avoir une réponse HTTP réussie.
- **UnhealthyThreshold** - le nombre d’échecs de réponse HTTP nécessaire pour marquer l’instance principaux comme *défectueux*.

<BR>

    $probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

### <a name="step-4"></a>Étape 4

Configurez le paramètre de passerelle d’application « poolsetting01 » pour le trafic dans le pool principal. Cette étape a également une configuration du délai d’attente de la réponse de pool du back-end pour une demande d’application de passerelle. Lorsqu’une réponse back-end atteint une limite de délai d’attente, la passerelle d’Application annule la demande. Cette valeur est différente dans un délai de sonde est uniquement pour la réponse de back-end tester les contrôles.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

### <a name="step-5"></a>Étape 5

Configurer le port IP frontal nommé « frontendport01 » pour le point de terminaison IP publique.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80

### <a name="step-6"></a>Étape 6

Créer la configuration IP frontale nommée « fipconfig01 » et associer l’adresse IP publique de la configuration IP front-end.


    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-7"></a>Étape 7

Créer l’écouteur nom « listener01 » et associer le port frontal à la configuration IP front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-8"></a>Étape 8

Créer la règle de routage équilibrage de la charge nommée « rule01 », ce qui configure le comportement d’équilibrage de la charge.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-9"></a>Étape 9

Configurer la taille de l’instance de la passerelle d’application.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut de *GatewaySize* est moyen. Vous pouvez choisir entre Standard_Small, Standard_Medium et Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Créer une passerelle d’application à l’aide de New-AzureRmApplicationGateway

Créer une passerelle d’application avec tous les éléments de configuration à partir de la procédure ci-dessus. Dans cet exemple, la passerelle d’application est appelée « appgwtest ».

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Ajouter une sonde à une passerelle d’application existante

Vous avez quatre étapes pour ajouter une sonde personnalisée à une passerelle d’application existant.

### <a name="step-1"></a>Étape 1

Charger la ressource de passerelle d’application dans une variable de PowerShell en utilisant **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Étape 2

Ajouter une sonde à la configuration existante de la passerelle.

    $getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


Dans l’exemple, la sonde personnalisé est configurée pour vérifier si URL path contoso.com/path/custompath.htm toutes les 30 secondes. Un seuil de délai d’attente de 120 secondes est configuré avec un nombre maximal de 8 demandes de sonde a échoué.

### <a name="step-3"></a>Étape 3

Ajouter de la sonde à la configuration de pool de back-end et le délai d’attente à l’aide de **-défini-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### <a name="step-4"></a>Étape 4

Enregistrer la configuration de la passerelle d’application à l’aide de **Set-AzureRmApplicationGateway**.

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Supprimer une sonde à partir d’une passerelle d’application existante

Voici la procédure pour supprimer une sonde personnalisée à partir d’une passerelle d’application existant.

### <a name="step-1"></a>Étape 1

Charger la ressource de passerelle d’application dans une variable de PowerShell en utilisant **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### <a name="step-2"></a>Étape 2

Supprimer la configuration de la sonde à partir de la passerelle d’application à l’aide de **AzureRmApplicationGatewayProbeConfig de la supprimer**.

    $getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### <a name="step-3"></a>Étape 3

Mise à jour du paramètre de pool de back-end à supprimer de la sonde et d’un paramètre de délai d’attente à l’aide de **-défini-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Étape 4

Enregistrer la configuration de la passerelle d’application à l’aide de **Set-AzureRmApplicationGateway**. 

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## <a name="get-application-gateway-dns-name"></a>Obtenir le nom de l’application passerelle DNS

Une fois la passerelle est créée, l’étape suivante consiste à configurer le front-end pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle d’application requiert un nom DNS attribué de manière dynamique, ce qui n’est pas convivial. Pour garantir aux utilisateurs finaux peut atteint la passerelle d’application un enregistrement CNAME peut servir pour pointer vers le point de terminaison public de la passerelle d’application. [Configuration d’un nom de domaine personnalisé pour dans Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Pour ce faire, récupérer les détails de la passerelle d’application et de ses associé IP ou le nom DNS à l’aide de l’élément PublicIPAddress attaché à la passerelle d’application. Nom DNS de la passerelle application doit être utilisé pour créer un enregistrement CNAME, qui désigne les applications web de deux à ce nom DNS. L’utilisation d’un des enregistrements n’est pas recommandée car l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle d’application.
    
    Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
        
    Name                     : publicIP01
    ResourceGroupName        : appgw-RG
    Location                 : westus
    Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
    Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
    ResourceGuid             : 00000000-0000-0000-0000-000000000000
    ProvisioningState        : Succeeded
    Tags                     : 
    PublicIpAllocationMethod : Dynamic
    IpAddress                : xx.xx.xxx.xx
    PublicIpAddressVersion   : IPv4
    IdleTimeoutInMinutes     : 4
    IpConfiguration          : {
                                 "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                               Configurations/frontend1"
                               }
    DnsSettings              : {
                                 "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                               }

## <a name="next-steps"></a>Étapes suivantes

Apprenez à configurer le déchargement SSL en visitant [Configurer le déchargement SSL](application-gateway-ssl-arm.md)

