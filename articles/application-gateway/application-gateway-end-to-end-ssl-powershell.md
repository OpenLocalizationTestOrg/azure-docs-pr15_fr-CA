<properties
    pageTitle="Configurer la stratégie de SSL et SSL de bout en bout avec la passerelle d’Application | Microsoft Azure"
    description="Cet article explique comment configurer SSL de bout en bout avec la passerelle d’Application à l’aide d’Azure Resource Manager PowerShell"
    services="application-gateway"
    documentationCenter="na"
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

# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>Configurer la stratégie de SSL et SSL de bout en bout avec la passerelle d’Application à l’aide de PowerShell

## <a name="overview"></a>Vue d’ensemble

Passerelle d’application prend en charge le cryptage de bout en bout du trafic. Passerelle d’application effectue cette opération en mettant fin à la connexion SSL au niveau de la passerelle d’application. La passerelle puis applique les règles de routage pour le trafic, crypte de nouveau le paquet et transfère le paquet vers le back-end approprié selon les règles de routage définies. Toute réponse du serveur web passe par le même processus à l’utilisateur final.

Une autre fonctionnalité de cette passerelle d’application prend en charge est la désactivation de certaines versions du protocole SSL. Passerelle d’application prend en charge la désactivation de la version suivante du protocole ; TLSv1.0, TLSv1.1 et TLSv1.2.

> [AZURE.NOTE] SSL 2.0 et SSL 3.0 sont désactivées par défaut et ne peut pas être activé. Ils sont considérés comme non sécurisée et ne sont pas capables d’être utilisées avec la passerelle d’Application

![image du scénario][scenario]

## <a name="scenario"></a>Scénario

Dans ce scénario, vous allez apprendre à créer une passerelle d’application à l’aide de SSL de bout en bout à l’aide de PowerShell.

Ce scénario est :

- Créer un groupe de ressources nommé « rg-appgw »
- Créer un réseau virtuel nommé « appgwvnet » avec un bloc CIDR réservé de 10.0.0.0/16.
- Créez deux sous-réseaux appelé « appgwsubnet » et « appsubnet ».
- Créer une passerelle petite application prenant en charge le cryptage SSL de bout en bout qui désactive certains protocoles SSL.

## <a name="before-you-begin"></a>Avant de commencer

Pour configurer SSL de bout en bout avec une passerelle d’application, un certificat est requis pour la passerelle et les certificats sont nécessaires pour les serveurs principaux. Le certificat de passerelle est utilisé pour crypter et décrypter le trafic envoyé à l’aide de SSL. Le certificat de passerelle doit être au format d’échange d’informations personnelles (pfx). Ce format de fichier permet d’exporter la clé privée qui est requis par la passerelle d’application pour effectuer le cryptage et le décryptage du trafic.

Pour le cryptage ssl de bout en bout, le serveur principal doit être autorisés avec la passerelle d’application. Pour ce faire, vous devez télécharger le certificat public de le serveurs principaux pour la passerelle d’application. Cela garantit que la passerelle d’application communique uniquement avec les instances de back-end connus. Cela permet de sécuriser davantage la communication de bout en bout.

Ce processus est décrit dans les étapes suivantes :

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Cette section vous guide dans le processus de création d’un groupe de ressources, qui contient de la passerelle d’application.

### <a name="step-1"></a>Étape 1

Ouvrez une session votre compte Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Étape 2

Sélectionnez l’abonnement à utiliser pour ce scénario.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Étape 3

Créer un groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle d’application

L’exemple suivant crée un réseau virtuel et deux sous-réseaux. Un sous-réseau est utilisé pour contenir la passerelle d’application. Le second sous-réseau est utilisé pour les serveurs principaux qui héberge l’application web.

### <a name="step-1"></a>Étape 1

Affectez une plage d’adresses pour le sous-réseau est utilisé pour la passerelle d’Application lui-même.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Les sous-réseaux configurés pour la passerelle d’application doivent être dimensionnées correctement. Une passerelle d’application peut être configurée pour un maximum de 10 instances. Chaque instance prend 1 adresse IP du sous-réseau. Trop petit d’un sous-réseau peut nuire à la mise à l’échelle une passerelle d’application.

### <a name="step-2"></a>Étape 2

Affectez une plage d’adresses à utiliser pour le pool d’adresses back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>Étape 3

Créez un réseau virtuel avec les sous-réseaux définis dans les étapes précédentes.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>Étape 4

Récupérer la ressource du réseau virtuel et les ressources de sous-réseau à utiliser dans les étapes suivantes :

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Créer une adresse IP publique pour la configuration frontale

Créez une ressource IP publique à utiliser pour la passerelle d’application. Cette adresse IP publique est utilisée une étape suivante.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Passerelle d’application ne gère pas l’utilisation d’une adresse IP publique créée avec un nom de domaine défini. Uniquement une adresse IP publique avec une étiquette de domaine créé dynamiquement est pris en charge. Si vous avez besoin d’un nom dns convivial pour la passerelle d’application, il est recommandé d’utiliser un enregistrement cname en tant qu’alias.

## <a name="create-an-application-gateway-configuration-object"></a>Créer un objet de configuration d’application passerelle

Vous devez configurer tous les éléments de configuration avant de créer la passerelle d’application. Les étapes suivantes créent les éléments de configuration qui sont nécessaires pour une ressource d’application passerelle.

### <a name="step-1"></a>Étape 1

Créer une configuration d’application passerelle IP, ce paramètre configure le sous-réseau, la passerelle d’application utilise. Lors du démarrage de la passerelle d’application, il sélectionne une adresse IP du sous-réseau configuré et achemine le trafic réseau vers les adresses IP dans le pool d’IP back-end. Gardez à l’esprit que chaque instance prend une adresse IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>Étape 2

Créer une configuration IP front-end, ce paramètre mappe une adresse ip publique ou privée au serveur frontal de la passerelle d’application. L’étape suivante associe l’adresse IP publique à l’étape précédente avec la configuration IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>Étape 3

Configurer le pool d’adresses IP back-end avec les adresses IP des serveurs web principaux. Ces adresses IP sont les adresses IP qui reçoivent du trafic réseau qui provient d’un point de terminaison IP front-end. Vous remplacez les adresses IP suivantes pour ajouter vos propres points de terminaison application IP adresse.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] Un nom de domaine pleinement qualifié (FQDN) est également une valeur valide à la place d’une adresse ip pour les serveurs principaux à l’aide du commutateur - BackendFqdns.

### <a name="step-4"></a>Étape 4

Configurer le port IP front-end pour le point de terminaison IP publique. Ce port est le port auquel se connecter aux utilisateurs finaux.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>Étape 5

Configurer le certificat pour la passerelle d’application. Ce certificat est utilisé pour déchiffrer et chiffrer le trafic sur la passerelle de l’application à nouveau.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] Cet exemple configure le certificat utilisé pour la connexion SSL. Le certificat doit être au format .pfx et le mot de passe doit être comprise entre 4 et 12 caractères.

### <a name="step-6"></a>Étape 6

Créer l’écouteur HTTP pour la passerelle d’application. Attribuer le certificat ip front-end de configuration, le port et ssl à utiliser.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>Étape 7

Télécharger le certificat à utiliser sur les ressources de pool du back-end ssl est activé.

> [AZURE.NOTE] La sonde par défaut Obtient la clé publique à partir de la liaison SSL **par défaut** sur l’adresse IP du back-end et compare la valeur de clé publique qu’il reçoit vers la valeur de clé publique que vous fournissez ici. La clé publique récupérée peut ne pas être le site concerné à laquelle le trafic de flux **Si** vous utilisez les en-têtes d’hôte et SNI en back-end. En cas de doute, visitez https://127.0.0.1/ sur le back end à confirmer le certificat utilisé pour la liaison SSL **par défaut** . Dans cette section, utilisez la clé publique de cette demande. Si vous utilisez SNI et les en-têtes d’hôte sur les liaisons HTTPS et que vous ne recevez pas une réponse et un certificat à partir d’une demande de navigateur manuel à https://127.0.0.1/ sur le back-end, vous devez configurer une liaison de SSL par défaut sur le back-end. Si vous ne le faites pas, les sondes échouera et back-end être ne seront pas autorisés.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] Le certificat fourni dans cette étape doit être la clé publique du certificat pfx présent sur le serveur principal. Exportez le certificat (et non le certificat racine) installé sur le serveur principal. Région d’exécution limitée de format et de l’utiliser dans cette étape. Ce blocage étape le back-end avec la passerelle d’application.

### <a name="step-8"></a>Étape 8

Les paramètres application passerelle http du back-end. Attribuer le certificat téléchargé à l’étape précédente pour les paramètres http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>Étape 9

Créer une règle de routage de l’équilibreur de charge qui configure le comportement d’équilibrage de la charge. Dans cet exemple, une règle de base alternée est créée.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>Étape 10

Configurer la taille de l’instance de la passerelle d’application.  Les tailles disponibles sont **Standard\_Small**, **Standard\_support**, et **Standard\_grande**.  Pour la capacité, les valeurs disponibles sont 1 à 10.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] Vous pouvez choisir un nombre d’instances de 1 aux fins de test. Il est important de savoir que n’importe quel nombre d’instances sous deux instances n’est pas couvert par le contrat SLA et ne sont donc pas recommandées. Passerelles de petite taille doivent être utilisés pour les tests de développement et non à des fins de production.

### <a name="step-11"></a>Étape 11

Configurer la stratégie SSL à utiliser sur la passerelle de l’Application. Passerelle d’application prend en charge la capacité à désactiver certaines versions du protocole SSL.

Les valeurs suivantes sont une liste des versions de protocole peut être désactivé.

- **TLSv1_0**
- **TLSv1_1**
- **TLSv1_2**

L’exemple suivant désactive la TLSv1\_0.

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0

## <a name="create-the-application-gateway"></a>Créer la passerelle d’Application

Toutes les étapes ci-dessus, créez la passerelle d’Application. La création de la passerelle est un processus long.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>Désactiver des versions du protocole SSL sur une passerelle d’Application existante

Les étapes précédentes vous accédez par le biais de la création d’une application avec ssl de bout en bout et la désactivation de certaines versions du protocole SSL. L’exemple suivant désactive certaines stratégies SSL sur une passerelle d’application existant.

### <a name="step-1"></a>Étape 1

Récupérer la passerelle d’application pour mettre à jour.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>Étape 2

Définir une stratégie SSL. Dans l’exemple suivant, TLSv1.0 et TLSv1.1 sont désactivés.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1 -ApplicationGateway $gw

### <a name="step-3"></a>Étape 3

Enfin, mise à jour de la passerelle. Il est important de noter que cette dernière étape est une tâche de longue durée. Lorsqu’il est terminé, ssl de bout en bout est configurée sur la passerelle d’application.

    $gw | Set-AzureRmApplicationGateway

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

En savoir plus sur le renforcement de la sécurité de vos applications web avec Web Application pare-feu via la passerelle d’Application en consultant la [Vue d’ensemble du pare-feu d’Application Web](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png
