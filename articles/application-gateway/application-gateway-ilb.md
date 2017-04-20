<properties 
   pageTitle="Créer et configurer une passerelle d’Application avec un équilibreur de charge interne (ILB) dans un réseau virtuel | Microsoft Azure"
   description="Cette page fournit des instructions pour configurer une passerelle d’Application Azure avec un point de terminaison avec équilibrage de charge interne"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/19/2016"
   ms.author="gwallace"/>

# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Créer une passerelle d’Application avec un équilibreur de charge interne (ILB)

> [AZURE.SELECTOR]
- [Étapes classiques Azure](application-gateway-ilb.md)
- [Étapes du Gestionnaire de ressources Powershell](application-gateway-ilb-arm.md)

Passerelle d’application peut être configuré avec une adresse IP virtuelle d’ouvert sur internet ou avec un point de terminaison interne non exposés à internet, également connu sous le point de terminaison équilibreur de charge interne (ILB). La configuration de la passerelle avec un ILB est utile pour les applications métier d’interne ne pas exposées à internet. Il est également utile pour les services/niveaux au sein d’une application multicouche, qui se situe dans une limite de sécurité ne pas exposée à internet, mais nécessitent tout de même la répartition de la charge alternée, adhésivité de session ou arrêt de SSL. Cet article vous guide tout au long de la procédure de configuration d’une passerelle d’application avec un ILB.

## <a name="before-you-begin"></a>Avant de commencer

1. Installez la version la plus récente des applets de commande PowerShell de Azure à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Vérifiez que vous disposez d’un réseau virtuel de travail avec un sous-réseau valide.
3. Vérifiez que vous disposez de serveurs principaux dans le réseau virtuel, soit avec un IP public/VIP affecté.


Pour créer une passerelle d’application, effectuez les opérations suivantes dans l’ordre indiqué. 

1. [Créer une passerelle d’application](#create-a-new-application-gateway)
2. [Configuration de la passerelle](#configure-the-gateway)
3. [Définir la configuration de la passerelle](#set-the-gateway-configuration)
4. [Démarrer la passerelle](#start-the-gateway)
4. [Vérifiez que la passerelle](#verify-the-gateway-status)



## <a name="create-an-application-gateway"></a>Créer une passerelle d’application :

**Pour créer la passerelle**, utilisez le `New-AzureApplicationGateway` cmdlet, remplacer les valeurs par le vôtre. Notez que la facturation de la passerelle ne démarre pas à ce stade. Facturation commence dans une étape ultérieure, lorsque la passerelle est démarrée avec succès.

    PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Pour valider** que la passerelle a été créée, vous pouvez utiliser la `Get-AzureApplicationGateway` applet de commande. 

Dans l’exemple, la *Description*, *InstanceCount*et *GatewaySize* sont des paramètres facultatifs. La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut de *GatewaySize* est moyen. Petites et grandes sont d’autres valeurs disponibles. *VIP* et *NomDNS* sont affichés comme vide parce que la passerelle n’a pas encore démarré. Ceux-ci sont créés une fois que la passerelle est en cours d’exécution. 

    PS C:\> Get-AzureApplicationGateway AppGwTest

    VERBOSE: 4:39:39 PM - Begin Operation:
    Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
    Operation: Get-AzureApplicationGateway
    Name: AppGwTest 
    Description: 
    VnetName: testvnet1 
    Subnets: {Subnet-1} 
    InstanceCount: 2 
    GatewaySize: Medium 
    State: Stopped 
    VirtualIPs: 
    DnsName:


## <a name="configure-the-gateway"></a>Configuration de la passerelle

Configuration d’une passerelle d’application se compose de plusieurs valeurs. Les valeurs peuvent être liés entre eux pour construire la configuration.
 
Les valeurs sont :

- **Pool de serveur Backend :** La liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent soit appartenir au sous-réseau VNet, ou doivent être un IP public/VIP. 
- **Paramètres de pool de serveur Backend :** Chaque pool dispose de paramètres comme port, le protocole et l’affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Site Web frontal Port :** Ce port est le port public est ouvert sur la passerelle d’application. Le trafic atteint ce port et qui obtient ensuite redirigé vers l’un des serveurs principaux.
- **Récepteur :** L’écouteur a un port frontal, un protocole (Http ou Https, qui sont sensibles à la casse) et le nom du certificat SSL (si le décharge de la configuration de SSL). 
- **Règle :** La règle lie l’écouteur et le pool de serveur backend et définit le pool de serveur backend le trafic doit être dirigé vers lorsqu’il rencontre un écouteur particulier. Actuellement, seule la règle de *base* est prise en charge. La règle de *base* est la répartition de la charge cyclique.

Vous pouvez construire votre configuration en créant un objet de configuration, soit en utilisant un fichier XML de configuration. Pour construire votre configuration à l’aide d’un fichier XML de configuration, utilisez l’exemple ci-dessous.



Notez les points suivants :


- L’élément *FrontendIPConfigurations* décrit les détails ILB utiles pour la configuration de passerelle d’Application avec un ILB. 

- Le *Type* de Frontend IP doit être définie sur 'Private'

- Le *StaticIPAddress* doit être définie sur l’adresse IP souhaitée interne sur lequel la passerelle reçoit le trafic. Notez que l’élément *StaticIPAddress* est facultatif. Si la valeur n’est pas définie, une adresse IP interne disponible à partir du sous-réseau déployé est choisie. 

- La valeur de l’élément de *nom* spécifié dans *FrontendIPConfiguration* doit être utilisée dans l’élément de *FrontendIP* de la HTTPListener pour faire référence à la FrontendIPConfiguration.

 **Exemple de configuration XML**

 

        <?xml version="1.0" encoding="utf-8"?>
        <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
            <FrontendIPConfigurations>
                <FrontendIPConfiguration>
                    <Name>fip1</Name> 
                    <Type>Private</Type> 
                    <StaticIPAddress>10.0.0.10</StaticIPAddress> 
                </FrontendIPConfiguration>
            </FrontendIPConfigurations>
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
                    <FrontendIP>fip1</FrontendIP>
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
    


## <a name="set-the-gateway-configuration"></a>Définir la configuration de la passerelle

Ensuite, vous allez définir la passerelle d’application. Vous pouvez utiliser la `Set-AzureApplicationGatewayConfig` applet de commande avec un objet de configuration, ou avec un fichier XML de configuration. 

    PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="start-the-gateway"></a>Démarrer la passerelle

Une fois que la passerelle a été configurée, utilisez les `Start-AzureApplicationGateway` applet de commande pour démarrer la passerelle. Facturation d’une passerelle d’application commence une fois la passerelle a été démarrée avec succès. 


> [AZURE.NOTE] Le `Start-AzureApplicationGateway` applet de commande peut prendre jusqu'à 15-20 minutes. 
   
    PS C:\> Start-AzureApplicationGateway AppGwTest 

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Vérifier l’état de la passerelle

Utilisez le `Get-AzureApplicationGateway` applet de commande pour vérifier l’état de la passerelle. Si *Début-AzureApplicationGateway* a réussi à l’étape précédente, l’état doit être *en cours d’exécution*, et la Vip et NomDNS doivent avoir des entrées valides. Elle montre l’applet de commande sur la première ligne, suivie de la sortie. Dans cet exemple, la passerelle est en cours d’exécution et il est prête à prendre le trafic. 

> [AZURE.NOTE] La passerelle d’application est configurée pour accepter le trafic au niveau du point de terminaison ILB configuré de 10.0.0.10 dans cet exemple.

    PS C:\> Get-AzureApplicationGateway AppGwTest 

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   : 
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {10.0.0.10}
    DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## <a name="next-steps"></a>Étapes suivantes


Si vous souhaitez plus d’informations sur des options d’équilibrage de charge en général, voir :

- [Équilibreur de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
