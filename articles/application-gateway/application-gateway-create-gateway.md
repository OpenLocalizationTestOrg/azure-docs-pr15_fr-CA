<properties
   pageTitle="Créer, démarrer ou supprimer une passerelle d’application | Microsoft Azure"
   description="Cette page fournit des instructions pour créer, configurer, démarrer et supprimer une passerelle d’application Azure"
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

# <a name="create-start-or-delete-an-application-gateway"></a>Créer, démarrer ou supprimer une passerelle d’application

> [AZURE.SELECTOR]
- [Azure Portal](application-gateway-create-gateway-portal.md)
- [Azure PowerShell de gestionnaire de ressources](application-gateway-create-gateway-arm.md)
- [Azure PowerShell classique](application-gateway-create-gateway.md)
- [Modèle de gestionnaire de ressources Azure](application-gateway-create-gateway-arm-template.md)
- [CLI Azure](application-gateway-create-gateway-cli.md)

Passerelle d’Application Azure est un équilibreur de charge de couche 7. Il fournit le basculement, routage des performances des requêtes HTTP entre différents serveurs, qu’ils soient sur le nuage ou sur site. Passerelle d’application fournit de nombreuses fonctionnalités de contrôleur de livraison d’applications (ADC), notamment l’équilibrage de charge HTTP, l’affinité de cookie de session, le déchargement de Secure Sockets Layer (SSL), les sondes d’état personnalisé, prise en charge de plusieurs sites et bien d’autres. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez [Vue d’ensemble de la passerelle Application](application-gateway-introduction.md)

Cet article vous guide tout au long de la procédure pour créer, configurer, démarrer et supprimer une passerelle d’application.

## <a name="before-you-begin"></a>Avant de commencer

1. À l’aide de Web Platform Installer, installez la version la plus récente des applets de commande PowerShell d’Azure. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Si vous avez un réseau virtuel existant, sélectionnez un sous-réseau vide existant ou créer un sous-réseau de votre réseau virtuel existant uniquement pour une utilisation par la passerelle d’application. Vous ne pouvez pas déployer la passerelle d’application à un autre réseau virtuel que les ressources que vous voulez déployer derrière la passerelle d’application, sauf si l’homologation de vnet est utilisé. Pour en savoir plus, visitez [Vnet homologation](../virtual-network/virtual-network-peering-overview.md)
3. Vérifiez que vous disposez d’un réseau virtuel de travail avec un sous-réseau valide. Assurez-vous qu’aucun machines virtuelles ou des déploiements dans le cloud n’utilisent le sous-réseau. La passerelle d’application lui-même doit être dans un sous-réseau de réseau virtuel.
3. Les serveurs que vous configurez pour utiliser la passerelle d’application doivent exister ou ont affectés de leurs points de terminaison créés dans le réseau virtuel ou avec un IP public/VIP.

## <a name="what-is-required-to-create-an-application-gateway"></a>Ce qui est nécessaire pour créer une passerelle d’application ?

Lorsque vous utilisez la commande **New-AzureApplicationGateway** pour créer la passerelle d’application, aucune configuration n’est définie à ce stade et que la ressource nouvellement créée sont configurés à l’aide de XML ou un objet de configuration.

Les valeurs sont :

- **Pool de serveurs back-end :** La liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent soit appartenir au sous-réseau de réseau virtuel ou doivent être un IP public/VIP.
- **Paramètres de pool de serveurs back-end :** Chaque pool dispose de paramètres comme port, le protocole et l’affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port front-end :** Ce port est le port public qui est ouvert sur la passerelle d’application. Le trafic atteint ce port et qui obtient ensuite redirigé vers l’un des serveurs principaux.
- **Récepteur :** L’écouteur a un port front-end, un protocole (Http ou Https, ces valeurs sont sensibles à la casse) et le nom du certificat SSL (si le décharge de la configuration de SSL).
- **Règle :** La règle lie l’écouteur et le pool de serveur principal et définit le pool de serveur back-end le trafic doit être dirigé vers lorsqu’il rencontre un écouteur particulier.

## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

Pour créer une passerelle d’application :

1. Créer une ressource d’application passerelle.
2. Créer un fichier XML de configuration ou d’un objet de configuration.
3. Valider la configuration de la ressource de passerelle d’application nouvellement créé.

>[AZURE.NOTE] Si vous avez besoin configurer une sonde personnalisée pour votre passerelle d’application, voir [Création d’une passerelle d’application avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-classic-ps.md). Consultez les [sondes personnalisé et le contrôle d’état](application-gateway-probe-overview.md) pour plus d’informations.

![Exemple de scénario][scenario]

### <a name="create-an-application-gateway-resource"></a>Créer une ressource d’application passerelle

Pour créer la passerelle, utilisez l’applet de commande **New-AzureApplicationGateway** , en remplaçant les valeurs avec vos propres. Facturation de la passerelle ne démarre pas à ce stade. Facturation commence dans une étape ultérieure, lorsque la passerelle est démarrée avec succès.

L’exemple suivant crée une passerelle d’application à l’aide d’un réseau virtuel appelé « testvnet1 » et un sous-réseau appelé « sous-réseau-1 ».


    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount*et *GatewaySize* sont des paramètres facultatifs.

Pour valider que la passerelle a été créée, vous pouvez utiliser l’applet de commande **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Stopped
    VirtualIPs    : {}
    DnsName       :

>[AZURE.NOTE]  La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut de *GatewaySize* est moyen. Vous pouvez choisir entre Small, Medium et Large.

*VirtualIPs* et *NomDNS* sont affichés comme vide parce que la passerelle n’a pas encore démarré. Ceux-ci sont créés une fois que la passerelle est en cours d’exécution.

## <a name="configure-the-application-gateway"></a>Configuration de la passerelle d’application

Vous pouvez configurer la passerelle d’application à l’aide de XML ou un objet de configuration.

## <a name="configure-the-application-gateway-by-using-xml"></a>Configurer la passerelle d’application à l’aide de XML

Dans l’exemple suivant, vous utilisez un fichier XML pour configurer tous les paramètres de passerelle d’application et de les valider à la ressource de passerelle d’application.  

### <a name="step-1"></a>Étape 1  

Copiez le texte suivant dans le bloc-notes.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>(name-of-your-frontend-port)</Name>
                <Port>(port number)</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>(name-of-your-backend-pool)</Name>
                <IPAddresses>
                    <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                    <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>(backend-setting-name-to-configure-rule)</Name>
                <Port>80</Port>
                <Protocol>[Http|Https]</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>(name-of-the-listener)</Name>
                <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
                <Protocol>[Http|Https]</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>(name-of-load-balancing-rule)</Name>
                <Type>basic</Type>
                <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
                <Listener>(name-of-the-listener)</Listener>
                <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>

Modifier les valeurs entre parenthèses pour les éléments de configuration. Enregistrez le fichier avec l’extension .xml.

>[AZURE.IMPORTANT] L’élément de protocole Http ou Https est sensible à la casse.

L’exemple suivant montre comment utiliser un fichier de configuration pour configurer la passerelle d’application. La charge de l’exemple équilibre le trafic HTTP sur le port 80 public et envoie le trafic réseau vers le back-end port 80 entre deux adresses IP.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>80</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>BackendPool1</Name>
                <IPAddresses>
                    <IPAddress>10.0.0.1</IPAddress>
                    <IPAddress>10.0.0.2</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>BackendSetting1</Name>
                <Port>80</Port>
                <Protocol>Http</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>HTTPListener1</Name>
                <FrontendPort>FrontendPort1</FrontendPort>
                <Protocol>Http</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>HttpLBRule1</Name>
                <Type>basic</Type>
                <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
                <Listener>HTTPListener1</Listener>
                <BackendAddressPool>BackendPool1</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


### <a name="step-2"></a>Étape 2

Ensuite, définissez la passerelle d’application. Utilisez l’applet de commande **Set-AzureApplicationGatewayConfig** avec un fichier XML de configuration.


    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>Configurer la passerelle d’application à l’aide d’un objet de configuration

L’exemple suivant montre comment configurer la passerelle d’application à l’aide d’objets de configuration. Tous les éléments de configuration doivent être configurés individuellement et ensuite ajoutés à un objet de configuration d’application passerelle. Après avoir créé l’objet de configuration, vous utilisez la commande **Set-AzureApplicationGateway** pour valider la configuration de la ressource de passerelle d’application créé précédemment.

>[AZURE.NOTE] Avant d’assigner une valeur à chaque objet de configuration, vous devez déclarer le type d’objet PowerShell utilise pour le stockage. La première ligne pour créer des éléments individuels définit les Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model (nom d’objet) sont utilisés.

### <a name="step-1"></a>Étape 1

Créer tous les éléments de configuration individuels.

Créer le front-end IP, comme indiqué dans l’exemple suivant.

    $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
    $fip.Name = "fip1"
    $fip.Type = "Private"
    $fip.StaticIPAddress = "10.0.0.5"

Créer le port front-end, comme illustré dans l’exemple suivant.

    $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
    $fep.Name = "fep1"
    $fep.Port = 80

Créer le pool de serveur principal.

 Définir les adresses IP qui sont ajoutés au pool de serveur principal, comme indiqué dans l’exemple suivant.


    $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
    $servers.Add("10.0.0.1")
    $servers.Add("10.0.0.2")

 Utilisez l’objet $server pour ajouter les valeurs à l’objet de regroupement de back-end ($pool).

    $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
    $pool.BackendServers = $servers
    $pool.Name = "pool1"

Créer le paramètre de pool de serveur principal.

    $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
    $setting.Name = "setting1"
    $setting.CookieBasedAffinity = "enabled"
    $setting.Port = 80
    $setting.Protocol = "http"

Créer le port d’écoute.

    $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
    $listener.Name = "listener1"
    $listener.FrontendPort = "fep1"
    $listener.FrontendIP = "fip1"
    $listener.Protocol = "http"
    $listener.SslCert = ""

Créez la règle.

    $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
    $rule.Name = "rule1"
    $rule.Type = "basic"
    $rule.BackendHttpSettings = "setting1"
    $rule.Listener = "listener1"
    $rule.BackendAddressPool = "pool1"

### <a name="step-2"></a>Étape 2

Affecter tous les éléments de configuration individuels à un objet de configuration de passerelle application ($appgwconfig).

Ajouter l’adresse IP front-end à la configuration.

    $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
    $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
    $appgwconfig.FrontendIPConfigurations.Add($fip)

Ajouter le port frontal à la configuration.

    $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
    $appgwconfig.FrontendPorts.Add($fep)

Ajouter le pool de serveur back-end à la configuration.

    $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
    $appgwconfig.BackendAddressPools.Add($pool)

Ajouter le paramètre de pool de back-end à la configuration.

    $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
    $appgwconfig.BackendHttpSettingsList.Add($setting)

Ajouter l’écouteur à la configuration.

    $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
    $appgwconfig.HttpListeners.Add($listener)

Ajouter la règle à la configuration.

    $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
    $appgwconfig.HttpLoadBalancingRules.Add($rule)

### <a name="step-3"></a>Étape 3

Validation de l’objet de configuration à la ressource de passerelle d’application à l’aide de **Set-AzureApplicationGatewayConfig**.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## <a name="start-the-gateway"></a>Démarrer la passerelle

Une fois la passerelle a été configurée, utilisez l’applet de commande **Start-AzureApplicationGateway** pour démarrer la passerelle. Facturation d’une passerelle d’application commence une fois la passerelle a été démarrée avec succès.

> [AZURE.NOTE] L’applet de commande **Start-AzureApplicationGateway** peut prendre jusqu'à 15 à 20 minutes pour terminer.

    Start-AzureApplicationGateway AppGwTest

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Vérifier l’état de la passerelle

L’applet de commande **Get-AzureApplicationGateway** permet de vérifier l’état de la passerelle. Si **Début-AzureApplicationGateway** a réussi à l’étape précédente, *état* doit être en cours d’exécution, et *Vip* et *NomDNS* doivent avoir des entrées valides.

L’exemple suivant montre une passerelle d’application est en cours d’exécution, vers le haut et prête à prendre le trafic à destination de `http://<generated-dns-name>.cloudapp.net`.

    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    Vip           : 138.91.170.26
    DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## <a name="delete-an-application-gateway"></a>Supprimer une passerelle d’application

Pour supprimer une passerelle d’application :

1. L’applet de commande **Stop-AzureApplicationGateway** permet d’arrêter la passerelle.
2. L’applet de commande **Remove-AzureApplicationGateway** permet de supprimer la passerelle.
3. Vérifiez que la passerelle a été supprimée à l’aide de l’applet de commande **Get-AzureApplicationGateway** .

L’exemple suivant montre l’applet de commande **Stop-AzureApplicationGateway** sur la première ligne, suivie de la sortie.

    Stop-AzureApplicationGateway AppGwTest

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Une fois la passerelle d’application dans un état arrêté, utilisez l’applet de commande **Remove-AzureApplicationGateway** pour supprimer le service.


    Remove-AzureApplicationGateway AppGwTest

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Pour vérifier que le service a été supprimé, vous pouvez utiliser l’applet de commande **Get-AzureApplicationGateway** . Cette étape n’est pas obligatoire.


    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez configurer le déchargement SSL, voir [configurer une passerelle d’application pour SSL de déchargement](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle d’application à utiliser avec un équilibreur de charge interne, voir [Création d’une passerelle d’application avec un équilibreur de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d’informations sur des options d’équilibrage de charge en général, voir :

- [Équilibreur de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png