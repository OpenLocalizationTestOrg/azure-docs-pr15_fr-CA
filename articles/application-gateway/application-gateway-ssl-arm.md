<properties
   pageTitle="Configuration d’une passerelle d’application pour le déchargement SSL à l’aide du Gestionnaire de ressources Azure | Microsoft Azure"
   description="Cette page fournit des instructions pour créer une passerelle d’application avec SSL de déchargement à l’aide du Gestionnaire de ressources Azure"
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
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configuration d’une passerelle d’application pour le déchargement SSL à l’aide du Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
-[Azure Portal](application-gateway-ssl-portal.md)
-[Azure PowerShell de gestionnaire de ressources](application-gateway-ssl-arm.md)
-[Azure PowerShell classique](application-gateway-ssl.md)

 Passerelle d’Application Azure peut être configuré pour mettre fin à la session de Secure Sockets Layer (SSL) au niveau de la passerelle afin d’éviter les coûteuses tâches de décryptage SSL pour se produire au niveau de la batterie de serveurs web. Déchargement SSL simplifie également la configuration du serveur frontal et la gestion de l’application web.

## <a name="before-you-begin"></a>Avant de commencer

1. À l’aide de Web Platform Installer, installez la version la plus récente des applets de commande PowerShell d’Azure. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Vous créez un réseau virtuel et un sous-réseau pour la passerelle d’application. Assurez-vous qu’aucun machines virtuelles ou des déploiements dans le cloud n’utilisent le sous-réseau. Passerelle d’application lui-même doit être dans un sous-réseau de réseau virtuel.
3. Les serveurs que vous configurez pour utiliser la passerelle d’application doivent exister ou ont affectés de leurs points de terminaison créés dans le réseau virtuel ou avec un IP public/VIP.

## <a name="what-is-required-to-create-an-application-gateway"></a>Ce qui est nécessaire pour créer une passerelle d’application ?


- **Pool de serveurs back-end :** La liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent soit appartenir au sous-réseau de réseau virtuel ou doivent être un IP public/VIP.
- **Paramètres de pool de serveurs back-end :** Chaque pool dispose de paramètres comme port, le protocole et l’affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port front-end :** Ce port est le port public qui est ouvert sur la passerelle d’application. Le trafic atteint ce port et qui obtient ensuite redirigé vers l’un des serveurs principaux.
- **Récepteur :** L’écouteur a un port front-end, un protocole (Http ou Https, ces paramètres sont sensibles à la casse) et le nom du certificat SSL (si le décharge de la configuration de SSL).
- **Règle :** La règle lie l’écouteur et le pool de serveur principal et définit le pool de serveur back-end le trafic doit être dirigé vers lorsqu’il rencontre un écouteur particulier. Actuellement, seule la règle de *base* est prise en charge. La règle de *base* est la répartition de la charge cyclique.

**Notes de configuration supplémentaires**

Pour la configuration de certificats SSL, il doit passer le protocole **HttpListener** à *Https* (sensible à la casse). L’élément **réactivant** est ajouté à **HttpListener** avec la valeur de la variable configuré pour le certificat SSL. Le port frontal doit être mis à jour à 443.

**À activer l’affinité de basée sur les cookies**: une passerelle d’application peut être configurée pour s’assurer qu’une demande à partir d’une session cliente est toujours dirigée vers la même VM dans la batterie de serveurs web. Ce scénario est fait par injection d’un cookie de session qui permet à la passerelle diriger le trafic de manière appropriée. Pour activer l’affinité de basée sur les cookies, définissez **CookieBasedAffinity** sur *activé* dans l’élément **BackendHttpSettings** .


## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

La différence entre l’utilisation du modèle de déploiement classique d’Azure et Azure le Gestionnaire de ressources est la commande que vous créez une passerelle d’application et les éléments qui doivent être configurés.

Avec le Gestionnaire de ressources, tous les composants d’une passerelle d’application sont configurés individuellement et mettez ensuite ensemble pour créer une ressource d’application passerelle.


Voici les étapes nécessaires à la création d’une passerelle d’application :

1. Créer un groupe de ressources pour le Gestionnaire de ressources
2. Créer des réseaux virtuels, du sous-réseau et adresse IP publique pour la passerelle d’application
3. Créer un objet de configuration d’application passerelle
4. Créer une ressource d’application passerelle


## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour le Gestionnaire de ressources

Assurez-vous que vous basculez en mode de PowerShell pour utiliser les applets de commande du Gestionnaire de ressources Azure. Plus d’infos est disponible à [L’aide de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1

    Login-AzureRmAccount

### <a name="step-2"></a>Étape 2

Vérifiez les abonnements pour le compte.

    Get-AzureRmSubscription

Vous êtes invité à authentifier vos informations d’identification.<BR>

### <a name="step-3"></a>Étape 3

Choisir vos abonnements Azure à utiliser. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Étape 4

Créer un groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Le Gestionnaire de ressources Azure requiert que tous les groupes de ressources spécifient un emplacement. Ce paramètre est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Assurez-vous que toutes les commandes pour créer une passerelle d’application utilise le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG » et l’emplacement « Ouest nous ».

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle d’application

L’exemple suivant montre comment créer un réseau virtuel à l’aide du Gestionnaire de ressources :

### <a name="step-1"></a>Étape 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Cet exemple assigne la 10.0.0.0/24 plage adresse à une variable de sous-réseau à utiliser pour créer un réseau virtuel.

### <a name="step-2"></a>Étape 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Cet exemple crée un réseau virtuel nommé « appgwvnet » dans la ressource groupe « appgw-rg » pour la région Ouest américaine avec le préfixe 10.0.0.0/16 de sous-réseau 10.0.0.0/24.

### <a name="step-3"></a>Étape 3

    $subnet = $vnet.Subnets[0]

Cet exemple affecte l’objet de sous-réseau à la variable $subnet pour les étapes suivantes.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Créer une adresse IP publique pour la configuration frontale

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Cet exemple crée une ressource IP publique « publicIP01 » dans la ressource groupe « appgw-rg » pour la région Ouest.


## <a name="create-an-application-gateway-configuration-object"></a>Créer un objet de configuration d’application passerelle

### <a name="step-1"></a>Étape 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Cet exemple crée la configuration IP d’une passerelle d’application nommée « gatewayIP01 ». Lors du démarrage de la passerelle d’Application, il sélectionne une adresse IP du sous-réseau configuré et route le trafic réseau vers les adresses IP dans le pool d’IP back-end. Gardez à l’esprit que chaque instance prend une adresse IP.

### <a name="step-2"></a>Étape 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Cet exemple configure le pool d’adresses IP principal nommé « pool01 » avec des adresses IP « 134.170.185.46, 134.170.188.221,134.170.185.50. » Ces valeurs sont les adresses IP qui reçoivent du trafic réseau qui provient d’un point de terminaison IP front-end. Remplacer les adresses IP à partir de l’exemple précédent avec les adresses IP des points de terminaison de votre application web.

### <a name="step-3"></a>Étape 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Cet exemple configure le paramètre de passerelle d’application « poolsetting01 » pour le trafic réseau à charge équilibrée dans le pool principal.

### <a name="step-4"></a>Étape 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Cet exemple configure le port IP frontal nommé « frontendport01 » pour le point de terminaison IP publique.

### <a name="step-5"></a>Étape 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Cet exemple configure le certificat utilisé pour la connexion SSL. Le certificat doit être au format .pfx et le mot de passe doit être comprise entre 4 et 12 caractères.

### <a name="step-6"></a>Étape 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Cet exemple crée la configuration IP frontale nommée « fipconfig01 » et associe l’adresse IP publique de la configuration IP front-end.

### <a name="step-7"></a>Étape 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Cet exemple crée le nom de port d’écoute « listener01 » et associe le port frontal à la configuration d’IP front-end et le certificat.

### <a name="step-8"></a>Étape 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Cet exemple crée la règle de routage équilibrage de la charge nommée « rule01 », ce qui configure le comportement d’équilibrage de la charge.

### <a name="step-9"></a>Étape 9

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Cet exemple configure la taille de l’instance de la passerelle d’application.

>[AZURE.NOTE]  La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut de *GatewaySize* est moyen. Vous pouvez choisir entre Standard_Small, Standard_Medium et Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Créer une passerelle d’application à l’aide de New-AzureApplicationGateway

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Cet exemple crée une passerelle d’application avec tous les éléments de configuration à partir de la procédure précédente. Dans l’exemple, la passerelle d’application est appelée « appgwtest ».

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

Si vous souhaitez configurer une passerelle d’application à utiliser avec un équilibreur de charge interne (ILB), voir [Création d’une passerelle d’application avec un équilibreur de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d’informations sur des options d’équilibrage de charge en général, voir :

- [Équilibreur de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
