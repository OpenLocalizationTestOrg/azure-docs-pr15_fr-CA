<properties
   pageTitle="Créer et configurer une passerelle d’application avec un équilibreur de charge interne (ILB) à l’aide du Gestionnaire de ressources Azure | Microsoft Azure"
   description="Cette page fournit des instructions pour créer, configurer, démarrer et supprimer une passerelle d’application Azure avec équilibreur de charge interne (ILB) pour le Gestionnaire de ressources Azure"
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
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Créer une passerelle d’application avec un équilibreur de charge interne (ILB) à l’aide du Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Étapes classiques Azure](application-gateway-ilb.md)
- [Étapes du Gestionnaire de ressources PowerShell](application-gateway-ilb-arm.md)

Passerelle d’Application Azure peut être configuré avec une adresse IP virtuelle pour Internet ou un point de terminaison interne qui n’est pas exposé à Internet, également connu sous le nom une extrémité équilibreur (ILB) de charge interne. La configuration de la passerelle avec un ILB est utile pour les applications de métier internes qui ne sont pas exposées à Internet. Il est également utile pour les services et les niveaux au sein d’une application multicouche qui se trouvent dans une limite de sécurité qui n’est pas exposée à Internet mais nécessitent tout de même alternée chargement adhésivité de session, de distribution ou de résiliation de Secure Sockets Layer (SSL).

Cet article vous guide tout au long de la procédure de configuration d’une passerelle d’application avec un ILB.

## <a name="before-you-begin"></a>Avant de commencer

1. À l’aide de Web Platform Installer, installez la version la plus récente des applets de commande PowerShell d’Azure. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Vous créez un réseau virtuel et un sous-réseau pour la passerelle d’Application. Assurez-vous qu’aucun machines virtuelles ou des déploiements dans le cloud n’utilisent le sous-réseau. Passerelle d’application lui-même doit être dans un sous-réseau de réseau virtuel.
3. Les serveurs que vous configurez pour utiliser la passerelle d’application doivent exister ou ont affectés de leurs points de terminaison créés dans le réseau virtuel ou avec un IP public/VIP.

## <a name="what-is-required-to-create-an-application-gateway"></a>Ce qui est nécessaire pour créer une passerelle d’application ?


- **Pool de serveurs back-end :** La liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir soit au réseau virtuel, mais dans un sous-réseau différent pour la passerelle d’application ou doivent être un IP public/VIP.
- **Paramètres de pool de serveurs back-end :** Chaque pool dispose de paramètres comme port, le protocole et l’affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port front-end :** Ce port est le port public qui est ouvert sur la passerelle d’application. Le trafic atteint ce port et qui obtient ensuite redirigé vers l’un des serveurs principaux.
- **Récepteur :** L’écouteur a un port front-end, un protocole (Http ou Https, qui sont sensibles à la casse) et le nom du certificat SSL (si le décharge de la configuration de SSL).
- **Règle :** La règle lie l’écouteur et le pool de serveur principal et définit le pool de serveur back-end le trafic doit être dirigé vers lorsqu’il rencontre un écouteur particulier. Actuellement, seule la règle de *base* est prise en charge. La règle de *base* est la répartition de la charge cyclique.



## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

La différence entre l’utilisation classique d’Azure et d’Azure le Gestionnaire de ressources est l’ordre dans lequel vous créez la passerelle d’application et les éléments qui doivent être configurés.
Avec le Gestionnaire de ressources, tous les éléments qui composent une passerelle d’application est configuré individuellement et mettez ensuite pour créer la ressource de passerelle d’application.


Voici les étapes nécessaires à la création d’une passerelle d’application :

1. Créer un groupe de ressources pour le Gestionnaire de ressources
2. Créer un réseau virtuel et un sous-réseau pour la passerelle d’application
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

Créer un nouveau groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Le Gestionnaire de ressources Azure requiert que tous les groupes de ressources spécifient un emplacement. Il est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Assurez-vous que toutes les commandes pour créer une passerelle d’application utilise le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-rg » et l’emplacement « Ouest nous ».

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle d’application

L’exemple suivant montre comment créer un réseau virtuel à l’aide du Gestionnaire de ressources :

### <a name="step-1"></a>Étape 1

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Cela affecte le 10.0.0.0/24 plage adresse à une variable de sous-réseau à utiliser pour créer un réseau virtuel.

### <a name="step-2"></a>Étape 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Cette opération crée un réseau virtuel nommé « appgwvnet » dans la ressource groupe « appgw-rg » pour la région Ouest américaine avec le préfixe 10.0.0.0/16 de sous-réseau 10.0.0.0/24.

### <a name="step-3"></a>Étape 3

    $subnet = $vnet.subnets[0]

L’objet de sous-réseau est ainsi affecté à la variable $subnet pour les étapes suivantes.

## <a name="create-an-application-gateway-configuration-object"></a>Créer un objet de configuration d’application passerelle

### <a name="step-1"></a>Étape 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Cela crée la configuration IP d’une passerelle d’application nommée « gatewayIP01 ». Lors du démarrage de la passerelle d’Application, il sélectionne une adresse IP du sous-réseau configuré et route le trafic réseau vers les adresses IP dans le pool d’IP back-end. Gardez à l’esprit que chaque instance prend une adresse IP.


### <a name="step-2"></a>Étape 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Cela permet de configurer le pool d’adresses IP principal nommé « pool01 » avec des adresses IP « 134.170.185.46, 134.170.188.221,134.170.185.50 ». Ce sont les adresses IP qui reçoivent du trafic réseau qui provient d’un point de terminaison IP front-end. Vous remplacez les adresses IP ci-dessus pour ajouter vos propres points de terminaison application IP adresse.

### <a name="step-3"></a>Étape 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Cela configure le trafic de réseau application passerelle paramètre « poolsetting01 » pour la charge équilibrée dans le pool principal.

### <a name="step-4"></a>Étape 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Cela configure le port IP frontal nommé « frontendport01 » pour l’ILB.

### <a name="step-5"></a>Étape 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Cela crée la configuration IP front-end, appelée « fipconfig01 » et l’associe à un IP privée à partir du sous-réseau de réseau virtuel en cours.

### <a name="step-6"></a>Étape 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Cela crée l’écouteur appelé « listener01 » et associe le port frontal à la configuration IP front-end.

### <a name="step-7"></a>Étape 7

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Cette opération crée la règle de routage équilibrage de la charge appelée « rule01 », ce qui configure le comportement d’équilibrage de la charge.

### <a name="step-8"></a>Étape 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Cela permet de configurer la taille de l’instance de la passerelle d’application.

>[AZURE.NOTE]  La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut de *GatewaySize* est moyen. Vous pouvez choisir entre Standard_Small, Standard_Medium et Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Créer une passerelle d’application à l’aide de New-AzureApplicationGateway

Crée une passerelle d’application avec tous les éléments de configuration à partir de la procédure ci-dessus. Dans cet exemple, la passerelle d’application est appelée « appgwtest ».


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Cette opération crée une passerelle d’application avec tous les éléments de configuration à partir de la procédure ci-dessus. Dans l’exemple, la passerelle d’application est appelée « appgwtest ».


## <a name="delete-an-application-gateway"></a>Supprimer une passerelle d’application

Pour supprimer une passerelle d’application, vous devez effectuer les opérations suivantes dans l’ordre :

1. L’applet de commande **Stop-AzureRmApplicationGateway** permet d’arrêter la passerelle.
2. L’applet de commande **Remove-AzureRmApplicationGateway** permet de supprimer la passerelle.
3. Vérifiez que la passerelle a été supprimée à l’aide de l’applet de commande **Get-AzureApplicationGateway** .


### <a name="step-1"></a>Étape 1

Obtenir l’objet de passerelle d’application et l’associer à une variable « $getgw ».

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Étape 2

**Stop-AzureRmApplicationGateway** permet d’arrêter la passerelle d’application. Elle montre l’applet de commande **Stop-AzureRmApplicationGateway** sur la première ligne, suivie de la sortie.

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Une fois la passerelle d’application dans un état arrêté, utilisez l’applet de commande **Remove-AzureRmApplicationGateway** pour supprimer le service.


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] Le **-force** commutateur peut être utilisé pour supprimer le message de confirmation de suppression.


Pour vérifier que le service a été supprimé, vous pouvez utiliser l’applet de commande **Get-AzureRmApplicationGateway** . Cette étape n’est pas obligatoire.


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez configurer le déchargement SSL, voir [configurer une passerelle d’application pour SSL de déchargement](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle d’application à utiliser avec un ILB, voir [Création d’une passerelle d’application avec un équilibreur de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d’informations sur des options d’équilibrage de charge en général, voir :

- [Équilibreur de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
