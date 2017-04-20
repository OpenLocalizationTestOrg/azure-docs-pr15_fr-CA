<properties
   pageTitle="Créer une sonde personnalisée pour la passerelle d’Application à l’aide de PowerShell dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à créer une sonde personnalisée pour la passerelle d’Application à l’aide de PowerShell dans le modèle de déploiement classique"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Créer une sonde personnalisée pour la passerelle d’Application Azure (classique) à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Azure portal](application-gateway-create-probe-portal.md)
- [Azure PowerShell de gestionnaire de ressources](application-gateway-create-probe-ps.md)
- [Azure PowerShell classique](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](application-gateway-create-probe-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

Pour créer une passerelle d’application :

1. Créer une ressource d’application passerelle.
2. Créer un fichier XML de configuration ou d’un objet de configuration.
3. Valider la configuration de la ressource de passerelle d’application nouvellement créé.

### <a name="create-an-application-gateway-resource"></a>Créer une ressource d’application passerelle

Pour créer la passerelle, utilisez l’applet de commande **New-AzureApplicationGateway** , en remplaçant les valeurs avec vos propres. Facturation de la passerelle ne démarre pas à ce stade. Facturation commence dans une étape ultérieure, lorsque la passerelle est démarrée avec succès.

L’exemple suivant crée une passerelle d’application à l’aide d’un réseau virtuel appelé « testvnet1 » et un sous-réseau appelé « sous-réseau-1 ».

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Pour valider que la passerelle a été créée, vous pouvez utiliser l’applet de commande **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest

>[AZURE.NOTE]  La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut de *GatewaySize* est moyen. Vous pouvez choisir entre les petites, moyennes et grandes.

 *VirtualIPs* et *NomDNS* sont affichés comme vide parce que la passerelle n’a pas encore démarré. Ceux-ci sont créés une fois que la passerelle est en cours d’exécution.

## <a name="configure-an-application-gateway"></a>Configuration d’une passerelle d’application

Vous pouvez configurer la passerelle d’application à l’aide de XML ou un objet de configuration.

## <a name="configure-an-application-gateway-by-using-xml"></a>Configurer une passerelle d’application à l’aide de XML

Dans l’exemple suivant, vous utilisez un fichier XML pour configurer tous les paramètres de passerelle d’application et de les valider à la ressource de passerelle d’application.  

### <a name="step-1"></a>Étape 1

Copiez le texte suivant dans le bloc-notes.

    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
    <FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
      </Probes>
     <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
                <IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
        <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


Modifier les valeurs entre parenthèses pour les éléments de configuration. Enregistrez le fichier avec l’extension .xml.

L’exemple suivant montre comment utiliser un fichier de configuration pour configurer la passerelle d’application, pour équilibrer le trafic HTTP sur le port 80 public et envoyer le trafic réseau vers le back-end port 80 entre deux adresses IP à l’aide d’une sonde personnalisée.

>[AZURE.IMPORTANT] L’élément de protocole Http ou Https est sensible à la casse.

Un nouvel élément de configuration \<sonde\> est ajouté à la configuration des sondes personnalisées.

Les paramètres de configuration sont les suivantes :

- **Nom** - nom de référence pour sonde personnalisé.
- **Protocol** - protocole utilisé (les valeurs possibles sont HTTP ou HTTPS).
- **Hôte** et le **chemin d’accès** : chemin d’accès URL complète qui est appelée par la passerelle d’application afin de déterminer l’état de l’instance. Par exemple, si vous avez un site Web http://contoso.com/, la sonde personnalisée peut alors être configurée pour « http://contoso.com/path/custompath.htm » pour les vérifications de sondages d’avoir une réponse HTTP réussie.
- **Intervalle** - configure les vérifications d’intervalle de sonde en secondes.
- **Délai d’attente** - définit le délai d’attente de sonde pour une vérification de la réponse HTTP.
- **UnhealthyThreshold** - le nombre d’échecs de réponse HTTP nécessaire pour marquer l’instance principaux comme *défectueux*.

Le nom de la sonde est référencé dans le <BackendHttpSettings> configuration à affecter le pool principal utilise des paramètres de sonde personnalisé.

## <a name="add-a-custom-probe-configuration-to-an-existing-application-gateway"></a>Ajouter une configuration de sonde personnalisé à une passerelle d’application existante

Modification de la configuration actuelle d’une passerelle d’application requiert trois étapes : obtenir le fichier de configuration XML actuel, modifier pour une sonde personnalisé et configurer la passerelle d’application avec les nouveaux paramètres de XML.

### <a name="step-1"></a>Étape 1

Obtenir le fichier XML à l’aide de get-AzureApplicationGatewayConfig. Cette commande exporte la configuration XML à modifier pour ajouter un paramètre de la sonde.

    Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"


### <a name="step-2"></a>Étape 2

Ouvrez le fichier XML dans un éditeur de texte. Ajouter un `<probe>` section après `<frontendport>`.

    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    </Probes>

Dans la section backendHttpSettings du fichier XML, ajoutez le nom de la sonde, comme illustré dans l’exemple suivant :

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Enregistrez le fichier XML.

### <a name="step-3"></a>Étape 3

Mettre à jour la configuration de passerelle d’application avec le nouveau fichier XML à l’aide de **Set-AzureApplicationGatewayConfig**. La passerelle d’application mis à jour avec la nouvelle configuration.

    Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"


## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez configurer le déchargement de la couche de Sockets sécurisée (SSL), consultez [configurer une passerelle d’application pour SSL de déchargement](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle d’application à utiliser avec un équilibreur de charge interne, voir [Création d’une passerelle d’application avec un équilibreur de charge interne (ILB)](application-gateway-ilb.md).
