<properties 
    pageTitle="Présentation de l’Architecture réseau des environnements de Service d’application" 
    description="Présentation de l’architecture de la topologie de réseau ofApp les environnements de Service." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="stefsch"/>   

# <a name="network-architecture-overview-of-app-service-environments"></a>Présentation de l’Architecture réseau des environnements de Service d’application

## <a name="introduction"></a>Introduction ##
Environnements de Service d’application sont toujours créées dans un sous-réseau d’un [réseau virtuel] [ virtualnetwork] -applications en cours d’exécution dans un environnement de Service d’application peuvent communiquer avec les points de terminaison privés situés à l’intérieur de la même topologie de réseau virtuel.  Dans la mesure où les clients peuvent verrouiller des parties de leur infrastructure de réseau virtuel, il est important de comprendre les types de flux de communication de réseau qui se produisent avec un environnement de Service d’application.

## <a name="general-network-flow"></a>Flux de réseau général ##
 
Lorsqu’un environnement de Service d’application (ASE) utilise une adresse IP virtuelle (VIP) publique pour les applications, tout le trafic entrant arrive sur ce public VIP.  Cela inclut le trafic HTTP et HTTPS pour les applications, ainsi que les autres types de trafic pour les opérations de gestion Azure, fonctionnalités de débogage à distance et FTP.  Pour une liste complète des ports spécifiques (obligatoires et facultatifs) qui sont disponibles sur l’adresse VIP public, voir l’article sur le [contrôle du trafic entrant] [ controllinginboundtraffic] dans un environnement de Service d’application. 

Environnements de Service d’application prennent également en charge les applications en cours d’exécution qui sont liées uniquement à une adresse interne de réseau virtuel, également appelée adresse ILB (équilibreur de charge interne).  Sur un ILB trafic de ASE, HTTP et HTTPS activé pour les applications, ainsi que les appels de débogage à distance, arrivent sur l’adresse ILB.  Pour les configurations les plus courantes ILB-ASE, le trafic FTP/FTPS arrivera également sur l’adresse ILB.  Toutefois, des opérations de gestion Azure continuent de circuler aux ports 454/455 sur l’adresse IP virtuelle publique d’un ILB activée ASE.

Le diagramme ci-dessous présente une vue d’ensemble des différents flux réseau entrants et sortants pour un environnement de Service d’application où les applications sont liées à une adresse IP publique virtuelle :

![Flux de réseau général][GeneralNetworkFlows]

Un environnement de Service d’application peut communiquer avec plusieurs points de terminaison de client privé.  Par exemple, les applications en cours d’exécution dans l’environnement de Service d’application peuvent se connecter aux serveurs de base de données en cours d’exécution sur les machines virtuelles de IaaS dans la même topologie de réseau virtuel.

>[AZURE.IMPORTANT] « Autres ressources Compute » en examinant le réseau de tâches, sont déployés dans un sous-réseau différent de celui de l’environnement de Service d’application. Déploiement des ressources dans le même sous-réseau avec l’équipement de direction auxiliaire bloque la connectivité à partir de l’équipement de direction auxiliaire à ces ressources (à l’exception de routage intra-ASE spécifique). Déployer sur un sous-réseau différent à la place (dans la même VNET). L’environnement de Service d’application sera alors en mesure de se connecter. Aucune configuration supplémentaire n’est nécessaire.

Environnements de Service d’application aussi communiquent avec DB Sql et le stockage Azure ressources nécessaires pour la gestion et l’exploitation d’un environnement de Service d’application.  Certains d’un environnement de Service d’application communique avec les ressources Sql et de stockage sont situés dans la même région que l’environnement de Service d’application, tandis que d’autres sont situés dans des régions Azure.  Par conséquent, connectivité sortante à Internet est toujours nécessaire pour un environnement de Service d’application fonctionne correctement. 

Dans la mesure où un environnement de Service d’application est déployé dans un sous-réseau, les groupes de sécurité de réseau peuvent servir pour contrôler le trafic entrant vers le sous-réseau.  Pour plus d’informations sur la manière de contrôler le trafic entrant vers un environnement de Service d’application, consultez l' [article]suivant de[controllinginboundtraffic].

Pour plus d’informations sur la façon d’autoriser la connectivité Internet sortante à partir d’un environnement de Service d’application, consultez l’article suivant sur l’utilisation de [l’Itinéraire de l’Express][ExpressRoute].  La même approche décrite dans l’article s’applique lorsque vous travaillez avec une connexion de Site à Site et l’utilisation forcée de tunneling.

## <a name="outbound-network-addresses"></a>Adresses réseau sortant ##
Lorsqu’un environnement de Service d’application effectue des appels sortants, une adresse IP est toujours associée avec les appels sortants.  L’adresse IP spécifique utilisé dépend de si le point de terminaison appelé se trouve dans la topologie de réseau virtuel, ou à l’extérieur de la topologie du réseau virtuel.

Si le point de terminaison appelé est **en dehors** de la topologie de réseau virtuel, l’adresse sortante (également appelé adresse NAT sortant) qui est utilisé est l’adresse IP virtuelle public de l’environnement de Service d’application.  Cette adresse peut être trouvée dans l’interface utilisateur du portail pour l’environnement de Service d’application de lame de propriétés.
 
![Adresse IP sortante][OutboundIPAddress]

Cette adresse peut également être déterminée pour ASEs qui ont seulement un VIP public en créant une application dans l’environnement de Service d’application, puis en effectuant un *nslookup* sur l’adresse de l’application. L’adresse IP résultante est à la fois l’adresse IP virtuelle public, ainsi que les adresse NAT sortant de l’environnement Service application.

Si le point de terminaison appelé se trouve **à l’intérieur** de la topologie de réseau virtuel, l’adresse sortant de l’application appelante sera l’adresse IP interne de la ressource de calcul individuel exécutant l’application.  Toutefois, il n’existe pas un mappage permanent des adresses IP internes de réseau virtuel pour les applications.  Les applications peuvent déplacer entre les ressources de calcul différentes et le pool de ressources dans un environnement de Service d’application peuvent être modifiées en raison de la mise à l’échelle des opérations de calcul disponible.

Toutefois, dans la mesure où un environnement de Service d’application est toujours situé dans un sous-réseau, vous avez la garantie que l’adresse IP interne d’une ressource de calcul une application en cours d’exécution va se trouvent toujours dans la plage CIDR du sous-réseau.  En conséquence, lorsque des ACL précis ou des groupes de sécurité de réseau sont utilisés pour sécuriser l’accès aux autres points de terminaison dans le réseau virtuel, la plage de sous-réseau contenant de l’environnement de Service d’application doit avoir accès.

Le diagramme suivant illustre ces concepts plus en détail :

![Adresses réseau sortant][OutboundNetworkAddresses]

Dans le diagramme ci-dessus :

- Étant donné que l’adresse IP virtuelle public de l’environnement de Service d’application est 192.23.1.2, qui est l’adresse IP sortant utilisé lors d’appels aux points de terminaison « Internet ».
- La plage CIDR du sous-réseau contenant de l’environnement de Service d’application est 10.0.1.0/26.  Autres points de terminaison dans la même infrastructure de réseau virtuel va apparaître dans les appels à partir d’applications provenant de quelque part dans cette plage d’adresses.

## <a name="calls-between-app-service-environments"></a>Appels entre les environnements de Service d’application ##
Un scénario plus complexe peut se produire si vous déployez plusieurs environnements de Service d’application sur le même réseau virtuel et effectuez des appels sortants à partir d’un environnement de Service d’application dans un autre environnement de Service d’application.  Ces types de traverser un environnement de Service d’application les appels seront également traités comme les appels « Internet ».

Le diagramme suivant montre un exemple d’une architecture en couches avec des applications dans un environnement de Service d’application (par exemple, les applications web « Porte »), l’appel d’applications sur un deuxième environnement de Service d’application (par exemple, interne principal API applications ne pas destinées à être accessible à partir d’Internet). 

![Appels entre les environnements de Service d’application][CallsBetweenAppServiceEnvironments] 

Dans l’exemple ci-dessus, l’environnement de Service d’application « ASE un » a une adresse IP sortante de 192.23.1.2.  Si une application en cours d’exécution sur cet environnement de Service d’application effectue un appel sortant à une application en cours d’exécution sur un deuxième environnement de Service d’application (« ASE deux ») situé dans le même réseau virtuel, l’appel sortant sera traitée comme un appel « Internet ».  Par conséquent le trafic arrivant sur le deuxième environnement de Service d’application s’affiche sous la forme d’origine à partir de 192.23.1.2 (c'est-à-dire pas la sous-réseau plage d’adresses du premier environnement de Service d’application).

Bien que les appels entre différents environnements de Service d’application sont traités comme des appels de « Internet », lorsque les deux environnements de Service d’application se trouvent dans la même région Azure le trafic réseau restera sur le réseau d’Azure régionaux et ne passera pas physiquement sur l’Internet public.  Par conséquent, vous pouvez utiliser un groupe de sécurité réseau sur le sous-réseau du deuxième environnement de Service d’application pour autoriser uniquement les appels entrants à partir de la première application Service environnement (dont l’adresse IP sortante est 192.23.1.2), afin de garantir une communication sécurisée entre les environnements de Service d’application.

## <a name="additional-links-and-information"></a>Informations et liens supplémentaires ##
Tous les articles et comment-de pour les environnements de Service d’application sont disponibles dans le [fichier Lisezmoi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Ports utilisés par les environnements de Service d’application d’entrée de plus d’informations sur et à l’aide de groupes de sécurité de réseau pour contrôler le trafic entrant est disponible [ici][controllinginboundtraffic].

Pour plus d’informations sur l’utilisation d’utilisateur défini itinéraires pour accorder un accès Internet sortant pour les environnements de Service d’application est disponible dans cet [article][ExpressRoute]. 


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

