<properties 
    pageTitle="Connexion en toute sécurité aux ressources de serveur principal, à partir d’un environnement de Service d’application" 
    description="Obtenir des informations sur la façon de connecter en toute sécurité aux ressources de serveur principal dans un environnement de Service d’application." 
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

# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Connexion en toute sécurité aux ressources de serveur principal, à partir d’un environnement de Service d’application #

## <a name="overview"></a>Vue d’ensemble ##
Dans la mesure où un environnement de Service d’application est toujours créé dans **un** un réseau virtuel du Gestionnaire de ressources Azure, **ou** un déploiement classique de modèle de [réseau virtuel][virtualnetwork], des connexions sortantes à partir d’un environnement de Service d’application à d’autres ressources back-end peuvent circuler exclusivement sur le réseau virtuel.  Avec une modification récente de juin 2016, ASEs peuvent également être déployées dans les réseaux virtuels qui utilisent (c'est-à-dire les plages d’adresses publiques ou des espaces d’adressage RFC1918 adresses privées).  

Par exemple, il peut être un SQL Server en cours d’exécution sur un cluster de machines virtuelles avec le port 1433 verrouillé.  Le point de terminaison peut être ACLd pour autoriser uniquement l’accès à partir d’autres ressources sur le même réseau virtuel.  

Comme autre exemple, les points de terminaison sensibles peuvent s’exécuter sur site et être connectés à Azure via deux [Sites] [ SiteToSite] ou [ExpressRoute d’Azure] [ ExpressRoute] les connexions.  Par conséquent, uniquement les ressources dans des réseaux virtuels connectés au Site à Site ou les tunnels ExpressRoute sera en mesure d’accéder aux points de terminaison locaux.

Pour tous ces scénarios, les applications s’exécutant sur un environnement de Service d’application seront en mesure de se connecter de manière sécurisée aux différents serveurs et ressources.  Le trafic sortant à partir des applications en cours d’exécution dans un environnement de Service d’application aux points de terminaison privés sur le même réseau virtuel (ou connecté au même réseau virtuel), seront uniquement des flux sur le réseau virtuel.  Le trafic sortant vers les points de terminaison privés ne passera pas sur l’Internet public.

Un inconvénient s’applique au trafic sortant à partir d’un environnement de Service d’application aux points de terminaison au sein d’un réseau virtuel.  Environnements de Service d’application ne peut pas atteindre des points de terminaison des machines virtuelles que qui se trouve dans le **même** sous-réseau que l’environnement de Service d’application.  Cela normalement ne doit pas être un problème tant que les environnements de Service d’application sont déployés dans un sous-réseau réservé pour une utilisation exclusive par seulement l’environnement de Service d’application.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="outbound-connectivity-and-dns-requirements"></a>Connectivité sortante et la configuration DNS requise ##
Dans un environnement de Service d’application fonctionne correctement, il requiert un accès sortant à différents points de terminaison. Une liste complète des points de terminaison externe utilisée par un équipement de direction auxiliaire est dans la section « Requis de connectivité réseau » de l’article de [Configuration réseau pour ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Les environnements de Service d’application nécessitent une infrastructure DNS valide configurée pour le réseau virtuel.  Si pour une raison quelconque, la configuration DNS est modifiée après la création d’un environnement de Service d’application, les développeurs peuvent forcer un environnement de Service d’application pour prélever la nouvelle configuration de DNS.  Déclencher un redémarrage d’environnement propagée à l’aide de l’icône de « Redémarrage » situé en haut de la blade de gestion d’environnement de Service d’application dans le portail entraînera l’environnement prélever la nouvelle configuration de DNS.

Il est également recommandé que tous les serveurs DNS personnalisées sur le vnet le programme d’installation à l’avance avant de créer un environnement de Service d’application.  Si la configuration de DNS d’un réseau virtuel est modifiée pendant la création d’un environnement de Service d’application, qui entraîne l’échec de processus de création environnement de Service d’application.  De la même manière, si un serveur DNS personnalisé existe sur l’autre extrémité d’une passerelle VPN et le serveur DNS est inaccessible ou indisponible, le processus de création d’environnement de Service d’application échouera également.

## <a name="connecting-to-a-sql-server"></a>Connexion à un SQL Server
Une configuration courante de SQL Server a un point de terminaison à l’écoute sur le port 1433 :

![Point de terminaison de SQL Server][SqlServerEndpoint]

Il existe deux approches pour limiter le trafic sur ce point de terminaison :


- [Listes de contrôle d’accès du réseau] [ NetworkAccessControlLists] (réseau ACL)

- [Groupes de sécurité de réseau][NetworkSecurityGroups]


## <a name="restricting-access-with-a-network-acl"></a>Restriction de l’accès à un réseau ACL

Le port 1433 peut être sécurisé à l’aide d’une liste de contrôle d’accès réseau.  L’exemple ci-dessous d’autorisation client des adresses à partir d’origine à l’intérieur d’un réseau virtuel et bloque l’accès à tous les autres clients.

![Exemple de liste de contrôle de l’accès réseau][NetworkAccessControlListExample]

Toutes les applications en cours d’exécution dans un environnement de Service d’application sur le même réseau virtuel que le SQL Server sera capable de se connecter à l’instance de SQL Server à l’aide de l’adresse IP **interne de VNet** pour la machine virtuelle de SQL Server.  

Chaîne de connexion de l’exemple ci-dessous fait référence à la SQL Server à l’aide de son adresse IP privée.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Bien que l’ordinateur virtuel possède également un point de terminaison public, des tentatives de connexion à l’aide de l’adresse IP publique seront rejetées en raison de l’ACL du réseau. 

## <a name="restricting-access-with-a-network-security-group"></a>Restriction de l’accès à un groupe de sécurité du réseau
Une autre approche de la sécurisation de l’accès est un groupe de sécurité du réseau.  Les groupes de sécurité de réseau peuvent être appliquées aux différentes machines virtuelles, ou pour un sous-réseau contenant des ordinateurs virtuels.

Un groupe de sécurité réseau doit d’abord être créé :

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restriction de l’accès à uniquement du trafic interne de VNet est très simple avec un groupe de sécurité du réseau.  Les règles par défaut dans un groupe de sécurité réseau autorisent l’accès uniquement à partir d’autres clients du réseau sur le même réseau virtuel.

Le verrouillage en conséquence l’accès à SQL Server est aussi simple que l’application d’un groupe de sécurité de réseau avec ses règles par défaut aux deux ordinateurs virtuels exécutant SQL Server ou le sous-réseau contenant des ordinateurs virtuels.

L’exemple ci-dessous s’applique à un groupe de sécurité réseau pour le sous-réseau contenant :

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
Le résultat final est un ensemble de règles de sécurité qui bloquent l’accès externe, tout en autorisant l’accès interne de VNet :

![Règles de sécurité de réseau par défaut][DefaultNetworkSecurityRules]


## <a name="getting-started"></a>Mise en route
Tous les articles et comment-de pour les environnements de Service d’application sont disponibles dans le [fichier Lisezmoi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Pour vous familiariser avec les environnements de Service d’application, consultez [Introduction à l’environnement de Service d’application][IntroToAppServiceEnvironment]

Pour plus d’informations sur le contrôle de trafic entrant pour votre environnement de Service d’application, consultez [contrôle le trafic entrant vers un environnement de Service d’application][ControlInboundASE]

Pour plus d’informations sur la plate-forme de services d’application Azure, consultez le [Service d’application Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ControlInboundASE]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/ 

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
