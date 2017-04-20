<properties
   pageTitle="Configuration d’une passerelle d’application pour le déchargement SSL en utilisant le déploiement classique | Microsoft Azure"
   description="Cet article fournit des instructions pour créer une passerelle d’application avec SSL de déchargement à l’aide du modèle de déploiement classique d’Azure."
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

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Configuration d’une passerelle d’application pour le déchargement SSL en utilisant le modèle de déploiement classique

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[PowerShell classique d’Azure](application-gateway-ssl.md)

Passerelle d’Application Azure peut être configuré pour mettre fin à la session de Secure Sockets Layer (SSL) au niveau de la passerelle afin d’éviter les coûteuses tâches de décryptage SSL pour se produire au niveau de la batterie de serveurs web. Déchargement SSL simplifie également la configuration du serveur frontal et la gestion de l’application web.

## <a name="before-you-begin"></a>Avant de commencer

1. À l’aide de Web Platform Installer, installez la version la plus récente des applets de commande PowerShell d’Azure. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Vérifiez que vous disposez d’un réseau virtuel de travail avec un sous-réseau valide. Assurez-vous qu’aucun machines virtuelles ou des déploiements dans le cloud n’utilisent le sous-réseau. La passerelle d’application lui-même doit être dans un sous-réseau de réseau virtuel.
3. Les serveurs que vous configurez pour utiliser la passerelle d’application doivent exister ou ont affectés de leurs points de terminaison créés dans le réseau virtuel ou avec un IP public/VIP.

Pour configurer le déchargement SSL sur une passerelle d’application, effectuez les opérations suivantes dans l’ordre indiqué :

1. [Créer une passerelle d’application](#create-an-application-gateway)
2. [Télécharger les certificats SSL](#upload-ssl-certificates)
3. [Configuration de la passerelle](#configure-the-gateway)
4. [Définir la configuration de la passerelle](#set-the-gateway-configuration)
5. [Démarrer la passerelle](#start-the-gateway)
6. [Vérifier l’état de la passerelle](#verify-the-gateway-status)


## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

Pour créer la passerelle, utilisez l’applet de commande **New-AzureApplicationGateway** , en remplaçant les valeurs avec vos propres. Facturation de la passerelle ne démarre pas à ce stade. Facturation commence dans une étape ultérieure, lorsque la passerelle est démarrée avec succès.

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Pour valider que la passerelle a été créée, vous pouvez utiliser l’applet de commande **Get-AzureApplicationGateway** .

Dans l’exemple, la *Description*, *InstanceCount*et *GatewaySize* sont des paramètres facultatifs. La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut de *GatewaySize* est moyen. Petites et grandes sont d’autres valeurs disponibles. *VirtualIPs* et *NomDNS* sont affichés comme vide parce que la passerelle n’a pas encore démarré. Ces valeurs sont créées une fois la passerelle est en cours d’exécution.

    Get-AzureApplicationGateway AppGwTest

## <a name="upload-ssl-certificates"></a>Télécharger les certificats SSL

Utilisez **Add-AzureApplicationGatewaySslCertificate** pour télécharger le certificat de serveur au format *pfx* à la passerelle d’application. Le nom du certificat est un nom d’utilisateur choisi et doit être unique au sein de la passerelle d’application. Ce certificat est référencé par ce nom dans toutes les opérations de gestion de certificat sur la passerelle d’application.

Cet exemple suivant montre l’applet de commande, remplacez les valeurs de l’exemple avec votre propre.

    Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>

Ensuite, validez le téléchargement du certificat. Utilisez l’applet de commande **Get-AzureApplicationGatewayCertificate** .

Elle montre l’applet de commande sur la première ligne, suivie de la sortie.

    Get-AzureApplicationGatewaySslCertificate AppGwTest

    VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
    VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
    Name           : SslCert
    SubjectName    : CN=gwcert.app.test.contoso.com
    Thumbprint     : AF5ADD77E160A01A6......EE48D1A
    ThumbprintAlgo : sha1RSA
    State..........: Provisioned

>[AZURE.NOTE] Le mot de passe du certificat doit être comprise entre 4 et 12 caractères, des lettres ou des nombres. Les caractères spéciaux ne sont pas acceptés.

## <a name="configure-the-gateway"></a>Configuration de la passerelle

Configuration d’une passerelle d’application se compose de plusieurs valeurs. Les valeurs peuvent être liés entre eux pour construire la configuration.

Les valeurs sont :

- **Pool de serveurs back-end :** La liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent soit appartenir au sous-réseau de réseau virtuel ou doivent être un IP public/VIP.
- **Paramètres de pool de serveurs back-end :** Chaque pool dispose de paramètres comme port, le protocole et l’affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port front-end :** Ce port est le port public qui est ouvert sur la passerelle d’application. Le trafic atteint ce port et qui obtient ensuite redirigé vers l’un des serveurs principaux.
- **Récepteur :** L’écouteur a un port front-end, un protocole (Http ou Https, ces valeurs sont sensibles à la casse) et le nom du certificat SSL (si le décharge de la configuration de SSL).
- **Règle :** La règle lie l’écouteur et le pool de serveur principal et définit le pool de serveur back-end le trafic doit être dirigé vers lorsqu’il rencontre un écouteur particulier. Actuellement, seule la règle de *base* est prise en charge. La règle de *base* est la répartition de la charge cyclique.

**Notes de configuration supplémentaires**

Pour la configuration de certificats SSL, il doit passer le protocole **HttpListener** à *Https* (sensible à la casse). L’élément **SslCert** est ajouté à **HttpListener** avec la valeur définie pour le même nom que celui utilisé dans le téléchargement de la précédente section de certificats SSL. Le port frontal doit être mis à jour à 443.

**À activer l’affinité de basée sur les cookies**: une passerelle d’application peut être configurée pour s’assurer qu’une demande à partir d’une session cliente est toujours dirigée vers la même VM dans la batterie de serveurs web. Ce scénario est fait par injection d’un cookie de session qui permet à la passerelle diriger le trafic de manière appropriée. Pour activer l’affinité de basée sur les cookies, définissez **CookieBasedAffinity** sur *activé* dans l’élément **BackendHttpSettings** .



Vous pouvez construire votre configuration en créant un objet de configuration ou à l’aide d’un fichier XML de configuration.
Pour construire votre configuration à l’aide d’un fichier XML de configuration, utilisez l’exemple suivant :

**Exemple de configuration XML**

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendIPConfigurations />
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>443</Port>
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
                <Protocol>Https</Protocol>
                <SslCert>GWCert</SslCert>
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

Ensuite, vous définissez la passerelle d’application. Vous pouvez utiliser l’applet de commande **Set-AzureApplicationGatewayConfig** avec un objet configuration ou avec un fichier XML de configuration.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

## <a name="start-the-gateway"></a>Démarrer la passerelle

Une fois la passerelle a été configurée, utilisez l’applet de commande **Start-AzureApplicationGateway** pour démarrer la passerelle. Facturation d’une passerelle d’application commence une fois la passerelle a été démarrée avec succès.


**Remarque :** L’applet de commande **Start-AzureApplicationGateway** peut prendre jusqu'à 15 à 20 minutes pour terminer.

    Start-AzureApplicationGateway AppGwTest

## <a name="verify-the-gateway-status"></a>Vérifier l’état de la passerelle

L’applet de commande **Get-AzureApplicationGateway** permet de vérifier l’état de la passerelle. Si **Début-AzureApplicationGateway** a réussi à l’étape précédente, *état* doit être en cours d’exécution, et *VirtualIPs* et *NomDNS* doivent avoir des entrées valides.

Cet exemple illustre une passerelle d’application, en cours d’exécution, et est prêt à prendre le trafic.

    Get-AzureApplicationGateway AppGwTest

    Name          : AppGwTest2
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {23.96.22.241}
    DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## <a name="next-steps"></a>Étapes suivantes


Si vous souhaitez plus d’informations sur des options d’équilibrage de charge en général, voir :

- [Équilibreur de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)