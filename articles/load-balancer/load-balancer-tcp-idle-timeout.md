<properties
   pageTitle="Configurer le délai d’inactivité TCP équilibreur de charge | Microsoft Azure"
   description="Configurer le délai d’inactivité TCP d’équilibrage de la charge"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Configurer les paramètres de délai d’inactivité TCP pour l’équilibrage de la charge Azure

Dans sa configuration par défaut, équilibrage de la charge Azure a un paramètre de délai d’inactivité de 4 minutes. Si une période d’inactivité est supérieure à la valeur de délai d’attente, il n’existe aucune garantie que la session TCP ou HTTP est maintenue entre le client et votre service cloud.

Lorsque la connexion est fermée, l’application cliente peut recevoir le message d’erreur suivant : « la connexion sous-jacente a été fermée : une connexion qui devait être maintenue active a été fermée par le serveur. »

Une pratique courante consiste à utiliser un TCP keep-alive. Cette application pratique maintient la connexion active pendant une période plus longue. Pour plus d’informations, consultez ces [exemples de .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Activé paramètre keep-alive, les paquets sont envoyés au cours des périodes d’inactivité de la connexion. Ces paquets persistants assurent que la valeur du délai d’inactivité n’est jamais atteint et que la connexion est maintenue pendant une longue période.

Ce paramètre fonctionne pour les connexions entrantes. Pour éviter la perte de la connexion, vous devez configurer le protocole TCP keep-alive avec un intervalle inférieur au paramètre de délai d’inactivité ou augmentez la valeur du délai d’inactivité. Pour prendre en charge ces scénarios, nous avons ajouté la prise en charge d’un délai configurable. Vous pouvez maintenant définir pour une durée de 4 à 30 minutes.

TCP keep-alive fonctionne bien pour les scénarios où la vie de la batterie n’est pas une contrainte. Il n’est pas recommandé pour les applications mobiles. Un TCP keep-alive dans une application mobile à l’aide de la se décharge plus rapidement la batterie de l’appareil.

![Délai d’expiration TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Les sections suivantes décrivent comment modifier les paramètres de délai d’inactivité dans des machines virtuelles et services en nuage.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configurer le délai d’attente TCP pour votre adresse IP publique au niveau de l’instance à 15 minutes

    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

`IdleTimeoutInMinutes`est facultatif. S’il n’est pas défini, le délai par défaut est de 4 minutes. La plage acceptable de délai d’attente est de 4 à 30 minutes.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Définir le délai d’inactivité lors de la création d’un point de terminaison Azure sur une machine virtuelle

Pour modifier le paramètre de délai d’attente pour un point de terminaison, vous devez utiliser la syntaxe suivante :

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

Pour récupérer votre configuration du délai d’inactivité, utilisez la commande suivante :

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Définir le délai d’attente TCP sur un ensemble de point de terminaison avec équilibrage de charge

Si les points de terminaison font partie d’un ensemble de point de terminaison avec équilibrage de charge, le délai d’attente TCP doit être définie sur l’ensemble de l’équilibrage de charge le point de terminaison. Par exemple :

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## <a name="change-timeout-settings-for-cloud-services"></a>Modifier les paramètres de délai d’attente pour les services en nuage

Vous pouvez utiliser le SDK Azure pour mettre à jour votre service cloud. Vous vérifiez les paramètres de point de terminaison pour les services en nuage dans le fichier .csdef. Le délai d’attente TCP pour le déploiement d’un service en nuage de mise à jour nécessite une mise à niveau du déploiement. Une exception est si le délai d’attente TCP est spécifiée uniquement pour une adresse IP publique. Les paramètres IP publiques sont dans le fichier .cscfg, et vous pouvez les mettre à jour par le biais de mise à niveau et mise à jour du déploiement.

Les modifications de .csdef pour les paramètres de point de terminaison sont les suivantes :

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>

Les modifications de .cscfg pour le paramètre de délai d’adresses IP publiques sont les suivants :

    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
        <InstanceAddress roleName="VMRolePersisted">
        <PublicIPs>
          <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
        </PublicIPs>
        </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>

## <a name="rest-api-example"></a>Exemple de l’API REST

Vous pouvez configurer le délai d’inactivité TCP à l’aide de l’API de gestion de service. Assurez-vous que le `x-ms-version` en-tête est défini à la version `2014-06-01` ou une version ultérieure. Mettre à jour la configuration de spécifié avec équilibrage de charge d’entrée terminaison sur tous les ordinateurs virtuels dans un déploiement.

### <a name="request"></a>Demande

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Réponse

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
        <LocalPort>local-port-number</LocalPort>
        <Port>external-port-number</Port>
        <LoadBalancerProbe>
          <Path>path-of-probe</Path>
          <Port>port-assigned-to-probe</Port>
          <Protocol>probe-protocol</Protocol>
          <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
          <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
        </LoadBalancerProbe>
        <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
        <Protocol>endpoint-protocol</Protocol>
        <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
        <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
        <EndpointACL>
          <Rules>
            <Rule>
              <Order>priority-of-the-rule</Order>
              <Action>permit-rule</Action>
              <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
              <Description>description-of-the-rule</Description>
            </Rule>
          </Rules>
        </EndpointACL>
      </InputEndpoint>
    </LoadBalancedEndpointList>

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble des équilibreur de charge interne](load-balancer-internal-overview.md)

[Démarrer la configuration d’un équilibreur de charge pour Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)
