<properties 
    pageTitle="Environnement de Service d’application | Microsoft Azure" 
    description="Quel est un environnement de Service d’application Azure ? Introduction à l’environnement de Service d’application." 
    keywords="environnement de service d’application Azure, réseau virtuel, la sécurisation des réseaux"
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

# <a name="app-service-environment-documentation"></a>Documentation d’environnement de Service d’application

Un environnement de Service d’application est une [prime] [ PremiumTier] option de plan de Service d’application Azure qui fournit un environnement totalement isolé et dédié pour exécuter en toute sécurité des applications de Service d’application Azure à grande échelle, y compris les [Applications Web]de service[WebApps], [Applications Mobile][MobileApps]et les [Applications d’API][APIApps].  

Environnements de Service d’application sont idéales pour des charges de travail nécessitant une :

- Très grande échelle
- Isolement et un accès réseau sécurisé

Les clients peuvent créer plusieurs environnements de Service d’application au sein d’une seule région d’Azure, ainsi que dans plusieurs régions Azure.  Environnements de Service d’application sont donc parfaits pour l’évolution horizontale des couches d’application sans état à l’appui des charges élevées de RPS.

Environnements de Service d’application sont isolés pour exécuter uniquement les applications d’un client unique et sont toujours déployés dans un réseau virtuel.  Les clients ont un contrôle précis sur le trafic de réseau application entrant et sortant à l’aide de [groupes de sécurité réseau][NetworkSecurityGroups].  Les applications peuvent également établir des connexions sécurisées à grande vitesse sur des réseaux virtuels pour les ressources de l’entreprise locale.

Les applications ont souvent besoin d’accéder à des ressources d’entreprise telles que les bases de données internes et services web.  Les applications en cours d’exécution sur les environnements de Service d’application peuvent accéder les ressources accessibles via le [Site-à] [ SiteToSite] VPN et [Azure ExpressRoute] [ ExpressRoute] les connexions.

* [Quel est un environnement de Service d’application ?](../app-service-web/app-service-app-service-environment-intro.md)
* [Création d’un environnement de Service d’application](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Création d’applications dans un environnement de Service d’application](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Création et utilisation d’un équilibreur de charge interne avec les environnements de Service d’application](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configuration d’un environnement de Service d’application](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Mise à l’échelle des applications dans un environnement de Service d’application](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Architecture et sécurité du réseau](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Comment de

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## <a name="videos"></a>Vidéos
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
