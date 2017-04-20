<properties
   pageTitle="Configurer le mode de distribution d’équilibreur de charge | Microsoft Azure"
   description="Comment faire pour configurer le mode de distribution d’équilibrage de la charge Azure pour prendre en charge les affinités IP de source"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="configure-the-distribution-mode-for-load-balancer"></a>Configurer le mode de distribution pour l’équilibrage de la charge

## <a name="hash-based-distribution-mode"></a>Mode de distribution fondés sur le hachage

L’algorithme de distribution par défaut est un 5-tuple (source IP, port source, adresse IP de destination, le port de destination, type de protocole) hachage pour mapper le trafic vers les serveurs disponibles. Il fournit l’adhésivité qu’au sein d’une session de transport. Dans la même session, les paquets seront dirigés vers la même instance de centre de données IP (DIP) derrière le point de terminaison d’équilibrage de la charge. Lorsque le client démarre une nouvelle session à partir de la même adresse IP source, le port source change et provoque le trafic accéder à un autre point de terminaison DIP.

![équilibrage de la charge en fonction de hachage](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

Figure 1-5-uplet distribution

## <a name="source-ip-affinity-mode"></a>Mode d’affinité IP source

Nous avons un autre mode de distribution affinité Source IP (aussi appelée affinité de session ou d’affinité du client IP). Azure équilibreur de charge peut être configuré pour utiliser un 2-tuple (adresse IP Source, adresse IP de Destination) ou un 3-tuple (protocole IP Source, adresse IP de Destination) pour mapper le trafic vers les serveurs disponibles. À l’aide d’affinité de l’adresse IP Source, connexions établies à partir du même ordinateur client accède au même point de terminaison DIP.

Le diagramme suivant illustre une configuration 2-tuple. Notez comment le tuple de 2 s’exécute par l’intermédiaire de l’équilibreur de charge pour la machine virtuelle 1 (VM1) qui est ensuite sauvegardée par ordinateur virtuel 2 et VM3.

![affinité de session](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Figure 2 : distribution de 2-tuple.

L’affinité de source IP résout un problème d’incompatibilité entre l’équilibreur de charge Azure et passerelle de bureau à distance (RD). Maintenant, vous pouvez créer une batterie de passerelle des services Bureau à distance dans un service cloud unique.

Un autre scénario d’utilisation est le téléchargement de média dans lequel le transfert de données se produit via UDP, mais le plan de contrôle s’effectue via TCP :

- Tout d’abord, un client établit une session TCP avec l’adresse publique d’équilibrage de la charge, obtient dirigé vers un DIP spécifique, ce canal est laissé actif pour surveiller l’état de la connexion
- Une nouvelle session UDP à partir du même ordinateur client commence sur la même charge équilibrée public point de terminaison, le but ici est que cette connexion est également dirigée vers le même point de terminaison DIP comme la connexion TCP précédente afin que le téléchargement de média peut être exécutée à un débit élevé tout en conservant un canal de contrôle via TCP.

>[AZURE.NOTE] Lorsqu’un ensemble équilibré en charge est modifié (suppression ou ajout d’une machine virtuelle), la distribution des demandes du client est recalculée. Impossible de compter sur les nouvelles connexions de clients existants se retrouver sur le même serveur. En outre, à l’aide de la source IP mode de distribution d’affinité peut entraîner une répartition inégale du trafic. Les clients qui exécutent derrière les serveurs proxy peuvent être considérés comme une application de client unique.

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>Configuration des paramètres IP Source affinité pour l’équilibreur de charge

Pour les machines virtuelles, vous pouvez utiliser PowerShell pour modifier les paramètres de délai d’attente :

Ajouter un point de terminaison Azure à une Machine virtuelle et de définir le mode de distribution d’équilibrage de la charge

    Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

LoadBalancerDistribution peut être définie à sourceIP de 2-tuple (adresse IP Source, adresse IP de Destination) d’équilibrage de charge, sourceIPProtocol pour l’équilibrage de la charge (protocole IP Source, adresse IP de Destination,) 3 tuples, ou none si vous souhaitez que le comportement par défaut de l’équilibrage de charge de 5-tuple.

Pour récupérer une configuration en mode de point de terminaison charge équilibrage de la distribution, utilisez ce qui suit :

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
    LoadBalancerDistribution : sourceIP

Si l’élément LoadBalancerDistribution n’est pas présente l’équilibreur de charge d’Azure utilise l’algorithme du 5-uplet par défaut.

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>Définir le mode de Distribution sur un ensemble de point de terminaison d’équilibrage de la charge

Si les points de terminaison font partie d’un ensemble de point de terminaison d’équilibrage de la charge, le mode de distribution doit être défini sur l’ensemble de point de terminaison d’équilibrage de la charge :

    Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>Configuration de Service cloud pour modifier le mode de distribution

Vous pouvez utiliser le SDK Azure pour .NET 2.5 (publié en novembre) pour mettre à jour votre Service Cloud. Paramètres de point de terminaison pour les Services en nuage sont effectuées dans le .csdef. Pour mettre à jour le mode de distribution d’équilibrage de la charge pour un déploiement de Services en nuage, une mise à niveau du déploiement est requis.
Voici un exemple de modifications .csdef pour les paramètres de point de terminaison :

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
      </Endpoints>
    </WorkerRole>
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

## <a name="api-example"></a>Exemple d’API

Vous pouvez configurer la distribution d’équilibrage de la charge à l’aide de l’API de gestion de service. Veillez à ajouter le `x-ms-version` en-tête est défini à la version `2014-09-01` ou version ultérieure.

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>Mise à jour de la configuration de l’ensemble d’équilibrage de charge spécifié dans un déploiement

#### <a name="request-example"></a>Exemple de requête

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet    x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

La valeur de LoadBalancerDistribution peut être sourceIP pour l’affinité de 2 tuples, sourceIPProtocol pour une affinité 3-tuple ou aucun (aucune affinité. par exemple, 5-uplet)

#### <a name="response"></a>Réponse

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble des équilibreur de charge interne](load-balancer-internal-overview.md)

[Démarrer la configuration d’un équilibreur de charge d’ouvert sur Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)
