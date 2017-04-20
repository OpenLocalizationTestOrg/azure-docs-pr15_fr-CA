<properties
   pageTitle="Créer, démarrer ou supprimer une passerelle d’application à l’aide du Gestionnaire de ressources Azure | Microsoft Azure"
   description="Cette page fournit des instructions pour créer, configurer, démarrer et supprimer une passerelle d’application Azure à l’aide du Gestionnaire de ressources Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Créer, démarrer ou supprimer une passerelle d’application à l’aide du Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Azure Portal](application-gateway-create-gateway-portal.md)
- [Azure PowerShell de gestionnaire de ressources](application-gateway-create-gateway-arm.md)
- [Azure PowerShell classique](application-gateway-create-gateway.md)
- [Modèle de gestionnaire de ressources Azure](application-gateway-create-gateway-arm-template.md)
- [CLI Azure](application-gateway-create-gateway-cli.md)

Passerelle d’Application Azure est un équilibreur de charge de couche 7. Il fournit le basculement, routage des performances des requêtes HTTP entre différents serveurs, qu’ils soient sur le nuage ou sur site. Passerelle d’application fournit de nombreuses fonctionnalités de contrôleur de livraison d’applications (ADC), notamment l’équilibrage de charge HTTP, l’affinité de cookie de session, le déchargement de Secure Sockets Layer (SSL), les sondes d’état personnalisé, prise en charge de plusieurs sites et bien d’autres. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez [Vue d’ensemble de la passerelle Application](application-gateway-introduction.md)

Cet article vous guide tout au long de la procédure pour créer, configurer, démarrer et supprimer une passerelle d’application.

>[AZURE.IMPORTANT] Avant de travailler avec les ressources d’Azure, il est important de comprendre que Azure dispose actuellement de deux modèles de déploiement : le Gestionnaire de ressources et classique. Assurez-vous de comprendre les [Outils et les modèles de déploiement](../azure-classic-rm.md) avant de travailler avec n’importe quelle ressource Azure. Vous pouvez afficher la documentation pour les différents outils en cliquant sur les onglets en haut de cet article. Ce document décrit la création d’une passerelle d’application à l’aide du Gestionnaire de ressources Azure. Pour utiliser la version classique, passez à [Création d’un déploiement classique de passerelle d’application à l’aide de PowerShell](application-gateway-create-gateway.md).


## <a name="before-you-begin"></a>Avant de commencer

1. À l’aide de Web Platform Installer, installez la version la plus récente des applets de commande PowerShell d’Azure. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Si vous avez un réseau virtuel existant, sélectionnez un sous-réseau vide existant ou créer un sous-réseau de votre réseau virtuel existant uniquement pour une utilisation par la passerelle d’application. Vous ne pouvez pas déployer la passerelle d’application à un autre réseau virtuel que les ressources que vous voulez déployer derrière la passerelle d’application.
3. Les serveurs que vous configurez pour utiliser la passerelle d’application doivent exister ou ont affectés de leurs points de terminaison créés dans le réseau virtuel ou avec un IP public/VIP.

## <a name="what-is-required-to-create-an-application-gateway"></a>Ce qui est nécessaire pour créer une passerelle d’application ?

- **Pool de serveurs back-end :** La liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent soit appartenir au sous-réseau de réseau virtuel ou doivent être un IP public/VIP.
- **Paramètres de pool de serveurs back-end :** Chaque pool dispose de paramètres comme port, le protocole et l’affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port front-end :** Ce port est le port public qui est ouvert sur la passerelle d’application. Le trafic atteint ce port et qui obtient ensuite redirigé vers l’un des serveurs principaux.
- **Récepteur :** L’écouteur a un port front-end, un protocole (Http ou Https, ces valeurs sont sensibles à la casse) et le nom du certificat SSL (si le décharge de la configuration de SSL).
- **Règle :** La règle lie l’écouteur, le pool de serveur principal et définit le pool de serveur back-end le trafic doit être dirigé vers lorsqu’il rencontre un écouteur particulier.

## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

La différence entre l’utilisation classique d’Azure et d’Azure le Gestionnaire de ressources est l’ordre dans lequel vous créez la passerelle d’application et les éléments qui doivent être configurés.

Avec le Gestionnaire de ressources, tous les éléments qui composent une passerelle d’application configurés individuellement et mettez ensuite pour créer la ressource de passerelle d’application.

Voici les étapes nécessaires à la création d’une passerelle d’application.

## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour le Gestionnaire de ressources

Assurez-vous que vous utilisez la version la plus récente de PowerShell d’Azure. Plus d’infos est disponible à [L’aide de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1

Ouvrez une session Azure

    Login-AzureRmAccount

Vous êtes invité à authentifier vos informations d’identification.

### <a name="step-2"></a>Étape 2

Vérifiez les abonnements pour le compte.

    Get-AzureRmSubscription

### <a name="step-3"></a>Étape 3

Choisir vos abonnements Azure à utiliser.

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="step-4"></a>Étape 4

Créer un groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Le Gestionnaire de ressources Azure requiert que tous les groupes de ressources spécifient un emplacement. Cet emplacement est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Assurez-vous que toutes les commandes pour créer une passerelle d’application utilise le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG » et l’emplacement « Ouest nous ».

>[AZURE.NOTE] Si vous avez besoin configurer une sonde personnalisée pour votre passerelle d’application, voir [Création d’une passerelle d’application avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Consultez les [sondes personnalisé et le contrôle d’état](application-gateway-probe-overview.md) pour plus d’informations.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle d’application

L’exemple suivant montre comment créer un réseau virtuel à l’aide du Gestionnaire de ressources.

### <a name="step-1"></a>Étape 1

Assignez le 10.0.0.0/24 plage adresse à la variable de sous-réseau à utiliser pour créer un réseau virtuel.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Étape 2

Créer un réseau virtuel nommé « appgwvnet » dans la ressource groupe « appgw-rg » pour la région Ouest américaine avec le préfixe 10.0.0.0/16 de sous-réseau 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="step-3"></a>Étape 3

Affecter une variable de sous-réseau pour les étapes suivantes, laquelle créer une passerelle d’application.

    $subnet=$vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Créer une adresse IP publique pour la configuration frontale

Créez une ressource IP publique « publicIP01 » dans la ressource groupe « appgw-rg » pour la région Ouest.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object"></a>Créer un objet de configuration d’application passerelle

Vous devez configurer tous les éléments de configuration avant de créer la passerelle d’application. Les étapes suivantes créent les éléments de configuration qui sont nécessaires pour une ressource d’application passerelle.

### <a name="step-1"></a>Étape 1

Créer la configuration IP d’une passerelle d’application nommée « gatewayIP01 ». Lors du démarrage de la passerelle d’Application, il sélectionne une adresse IP du sous-réseau configuré et achemine le trafic réseau vers les adresses IP dans le pool d’IP back-end. Gardez à l’esprit que chaque instance prend une adresse IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

### <a name="step-2"></a>Étape 2

Configurer le pool d’adresses IP principal nommé « pool01 » avec des adresses IP « 134.170.185.46, 134.170.188.221,134.170.185.50 ». Ces adresses IP sont les adresses IP qui reçoivent du trafic réseau qui provient d’un point de terminaison IP front-end. Vous remplacez les adresses IP ci-dessus pour ajouter vos propres points de terminaison application IP adresse.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

### <a name="step-3"></a>Étape 3

Configurez le paramètre de passerelle d’application « poolsetting01 » pour le trafic réseau à charge équilibrée dans le pool principal.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Étape 4

Configurer le port IP frontal nommé « frontendport01 » pour le point de terminaison IP publique.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### <a name="step-5"></a>Étape 5

Créer la configuration IP frontale nommée « fipconfig01 » et associer l’adresse IP publique de la configuration IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-6"></a>Étape 6

Créer l’écouteur nom « listener01 » et associer le port frontal à la configuration IP front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-7"></a>Étape 7

Créer la règle de routage équilibrage de la charge nommée « rule01 », ce qui configure le comportement d’équilibrage de la charge.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-8"></a>Étape 8

Configurer la taille de l’instance de la passerelle d’application.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut de *GatewaySize* est moyen. Vous pouvez choisir entre Standard_Small, Standard_Medium et Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Créer une passerelle d’application à l’aide de New-AzureRmApplicationGateway

Créer une passerelle d’application avec tous les éléments de configuration à partir de la procédure précédente. Dans cet exemple, la passerelle d’application est appelée « appgwtest ».

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### <a name="step-9"></a>Étape 9

Récupérer des informations DNS et l’adresse IP virtuelle de la passerelle d’application à partir de la ressource IP publique attachée à la passerelle d’application.

    Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

## <a name="delete-an-application-gateway"></a>Supprimer une passerelle d’application

Pour supprimer une passerelle d’application, procédez comme suit :

### <a name="step-1"></a>Étape 1

Obtenir l’objet de passerelle d’application et l’associer à une variable « $getgw ».

    $getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Étape 2

**Stop-AzureRmApplicationGateway** permet d’arrêter la passerelle d’application.

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

Une fois la passerelle d’application dans un état arrêté, utilisez l’applet de commande **Remove-AzureRmApplicationGateway** pour supprimer le service.

    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

>[AZURE.NOTE] Le **-force** commutateur peut être utilisé pour supprimer le message de confirmation de suppression.

Pour vérifier que le service a été supprimé, vous pouvez utiliser l’applet de commande **Get-AzureRmApplicationGateway** . Cette étape n’est pas obligatoire.

    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

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

Si vous souhaitez configurer le déchargement SSL, voir [configurer une passerelle d’application pour SSL de déchargement](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle d’application à utiliser avec un équilibreur de charge interne, voir [Création d’une passerelle d’application avec un équilibreur de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d’informations sur des options d’équilibrage de charge en général, voir :

- [Équilibreur de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
