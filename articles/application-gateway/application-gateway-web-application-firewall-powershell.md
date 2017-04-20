<properties
   pageTitle="Configurer des pare-feu pour applications Web sur la passerelle d’Application nouveau ou existant | Microsoft Azure"
   description="Cet article fournit des conseils sur la façon de démarrer sur une passerelle d’application nouveau ou existant à l’aide de pare-feu d’application web."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurer le pare-feu pour applications Web sur une passerelle d’Application nouveau ou existant

> [AZURE.SELECTOR]
- [Azure portal](application-gateway-web-application-firewall-portal.md)
- [Azure PowerShell de gestionnaire de ressources](application-gateway-web-application-firewall-powershell.md)

Le pare-feu d’application web (WAF) dans Azure Application Gateway protège les applications web contre les attaques courantes sur le web comme SQL injection, les attaques de script entre sites et détournements de session.

Passerelle d’Application Azure est un équilibreur de charge de couche 7. Il fournit le basculement, routage des performances des requêtes HTTP entre différents serveurs, qu’ils soient sur le nuage ou sur site. L’application fournit de nombreuses fonctionnalités de contrôleur de livraison d’applications (ADC), notamment l’équilibrage de charge HTTP, l’affinité de cookie de session, déchargement de Secure Sockets Layer (SSL), les sondes d’état personnalisé, prise en charge de plusieurs sites et bien d’autres. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez Vue d’ensemble de la passerelle Application

L’article suivant indique comment [Ajouter des pare-feu d’application web vers une passerelle d’application existants](#add-web-application-firewall-to-an-existing-application-gateway) et de [créer une passerelle d’application qui utilise le pare-feu d’application web](#create-an-application-gateway-with-web-application-firewall).

![image du scénario][scenario]

## <a name="waf-configuration-differences"></a>Différences de configuration WAF

Si vous avez lu [créer une passerelle d’Application avec PowerShell](application-gateway-create-gateway-arm.md), vous comprenez les paramètres de point de stock pour configurer lors de la création d’une passerelle d’application. WAF fournit des paramètres supplémentaires pour définir lors de la configuration de la référence SKU sur une passerelle d’application. Il n’y a aucune des modifications que vous apportez sur la passerelle de l’application elle-même.

**SKU** - une passerelle d’application normal sans prise en charge de WAF **Standard\_Small**, **Standard\_support**, et **Standard\_grande** tailles. Avec l’introduction de WAF, il existe deux références supplémentaires, **WAF\_support** et **WAF\_grande**. WAF n’est pas pris en charge sur les passerelles d’application de petite taille.

**Niveau** - les valeurs disponibles sont **Standard** ou **WAF**. Lors de l’utilisation de pare-feu pour applications Web, **WAF** doivent être choisis.

**Mode** - ce paramètre est le mode de WAF. les valeurs autorisées sont la **détection** et **prévention**. Lorsque WAF est configuré en mode de détection, toutes les menaces sont stockées dans un fichier journal. En mode de prévention, toujours les événements sont enregistrés, mais l’attaquant reçoit une réponse non autorisé 403 à partir de la passerelle d’application.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Ajouter des pare-feu d’application web à une passerelle d’application existante

Assurez-vous que vous utilisez la version la plus récente de PowerShell d’Azure. Plus d’infos est disponible à [L’aide de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1

Ouvrez une session votre compte Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Étape 2

Sélectionnez l’abonnement à utiliser pour ce scénario.

    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Étape 3

Récupérer la passerelle que vous ajoutez au pare-feu pour applications Web.

    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"


### <a name="step-4"></a>Étape 4

Configurer la référence de pare-feu d’application web. Les tailles disponibles sont **WAF\_grande** et **WAF\_support**. Lors de l’utilisation de pare-feu pour applications web, la couche doit être **WAF**, la capacité doit être confirmée lors de la définition de la référence sku.

    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2

### <a name="step-5"></a>Étape 5

Configurer les paramètres WAF tel que défini dans l’exemple suivant :

Pour le paramètre **WafMode** , les valeurs disponibles sont la détection et la prévention.

    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention

### <a name="step-6"></a>Étape 6

Mise à jour de la passerelle d’application avec les paramètres définis dans l’étape précédente.

    Set-AzureRmApplicationGateway -ApplicationGateway $gw

Cette commande met à jour la passerelle d’application avec le pare-feu d’Application Web. Il est recommandé d’afficher les [Diagnostics de passerelle d’Application](application-gateway-diagnostics.md) pour comprendre comment afficher les journaux de la passerelle d’application. En raison de la nature de la sécurité de WAF, doivent être réexaminées régulièrement pour comprendre la position de la sécurité de vos applications web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Créer une passerelle d’Application avec le pare-feu pour applications Web

Les étapes suivantes vous guident tout au long de l’intégralité du processus du début à la fin de la création d’une passerelle d’Application avec le pare-feu pour applications Web.

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

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-4"></a>Étape 4

Créer un groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Le Gestionnaire de ressources Azure requiert que tous les groupes de ressources spécifient un emplacement. Cet emplacement est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Assurez-vous que toutes les commandes pour créer une passerelle d’application utilise le même groupe de ressources.

Dans l’exemple précédent, nous avons créé un groupe de ressources appelé « appgw-RG » et l’emplacement « Ouest nous ».

>[AZURE.NOTE] Si vous avez besoin configurer une sonde personnalisée pour votre passerelle d’application, voir [Création d’une passerelle d’application avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Consultez les [sondes personnalisé et le contrôle d’état](application-gateway-probe-overview.md) pour plus d’informations.

### <a name="step-5"></a>Étape 5

Affectez une plage d’adresses pour le sous-réseau est utilisé pour la passerelle d’Application lui-même.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Un sous-réseau pour une application doit avoir un minimum de 28 bits de masque. Cette valeur laisse 10 adresses disponibles dans le sous-réseau pour les instances de la passerelle d’Application. Avec un sous-réseau plus petit, vous est peut-être pas en mesure d’ajouter plusieurs instances de votre passerelle d’application dans le futur.

### <a name="step-6"></a>Étape 6

Affectez une plage d’adresses à utiliser pour le pool d’adresses back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-7"></a>Étape 7

Créer un réseau virtuel avec les sous-réseaux précédents dans le groupe de ressources créé à l’étape : [créer le groupe de ressources](#create-the-resource-group)

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-8"></a>Étape 8

Récupérer la ressource du réseau virtuel et les ressources de sous-réseau à utiliser dans les étapes suivantes :

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

### <a name="step-9"></a>Étape 9

Créez une ressource IP publique à utiliser pour la passerelle d’application. Cette adresse IP publique est utilisée dans une des étapes suivantes :

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Passerelle d’application ne gère pas l’utilisation d’une adresse IP publique créée avec un nom de domaine défini. Uniquement une adresse IP publique avec une étiquette de domaine créé dynamiquement est pris en charge. Si vous avez besoin d’un nom dns convivial pour la passerelle d’application, il est recommandé d’utiliser un enregistrement cname en tant qu’alias.

### <a name="step-10"></a>Étape 10

Vous devez configurer tous les éléments de configuration avant de créer la passerelle d’application. Les étapes suivantes créent les éléments de configuration qui sont nécessaires pour une ressource d’application passerelle.

Créer une configuration IP de la passerelle application, cela configure le sous-réseau, la passerelle d’Application utilise. Lors du démarrage de la passerelle d’Application, il sélectionne une adresse IP du sous-réseau configuré et achemine le trafic réseau vers les adresses IP dans le pool d’IP back-end. Gardez à l’esprit que chaque instance prend une adresse IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-11"></a>Étape 11

Configurer le pool d’adresses IP back-end avec les adresses IP des serveurs web principaux. Ces adresses IP sont les adresses IP qui reçoivent du trafic réseau qui provient d’un point de terminaison IP front-end. Vous remplacez les adresses IP suivantes pour ajouter vos propres points de terminaison application IP adresse.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

### <a name="step-12"></a>Étape 12

Télécharger le certificat à utiliser sur les ressources de pool du back-end ssl est activé.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

### <a name="step-13"></a>Étape 13

Les paramètres application passerelle http du back-end. Attribuer le certificat téléchargé à l’étape précédente pour les paramètres http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-14"></a>Étape 14

Configurer le port IP front-end pour le point de terminaison IP publique. Ce port est le port auquel se connecter aux utilisateurs finaux.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-15"></a>Étape 15

Créer une configuration IP front-end, ce paramètre mappe une adresse ip publique ou privée au serveur frontal de la passerelle d’application. L’étape suivante associe l’adresse IP publique à l’étape précédente avec la configuration IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-16"></a>Étape 16

Configurer le certificat pour la passerelle d’application. Ce certificat est utilisé pour déchiffrer et chiffrer le trafic sur la passerelle de l’application à nouveau.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

### <a name="step-17"></a>Étape 17

Créer l’écouteur HTTP pour la passerelle d’application. Attribuer le certificat ip front-end de configuration, le port et ssl à utiliser.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-18"></a>Étape 18

Créer une règle de routage de l’équilibreur de charge qui configure le comportement d’équilibrage de la charge. Dans cet exemple, une règle de base alternée est créée.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   
### <a name="step-19"></a>Étape 19

Configurer la taille de l’instance de la passerelle d’application.

    $sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

>[AZURE.NOTE]  Vous pouvez choisir entre **WAF\_support** et **WAF\_grande**, la couche lorsque l’aide WAF est toujours **WAF**. La capacité est un nombre compris entre 1 et 10.

### <a name="step-20"></a>Étape 20

Configurer le mode de WAF, les valeurs acceptables sont la **détection**et la **prévention** .

    $config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"

### <a name="step-21"></a>Étape 21

Créer une passerelle d’application avec tous les éléments de configuration à partir de la procédure précédente. Dans cet exemple, la passerelle d’application est appelée « appgwtest ».

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert

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

Apprenez à configurer la journalisation des diagnostics, pour consigner les événements détectés ou empêchés avec pare-feu pour applications Web en visitant les [Diagnostics de passerelle d’Application](application-gateway-diagnostics.md)


[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png