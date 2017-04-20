<properties 
    pageTitle="Introduction à l’environnement de Service d’application" 
    description="Obtenir des informations sur la fonctionnalité de l’environnement de Service d’application qui fournit des unités d’échelle sécurisée, joints VNet et dédié pour toutes vos applications en cours d’exécution." 
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

# <a name="introduction-to-app-service-environment"></a>Introduction à l’environnement de Service d’application

## <a name="overview"></a>Vue d’ensemble ##
Un environnement de Service d’application est une [prime] [ PremiumTier] option de plan de Service d’application Azure qui fournit un environnement totalement isolé et dédié pour exécuter en toute sécurité des applications de Service d’application Azure à grande échelle, y compris les [Applications Web]de service[WebApps], [Applications Mobile][MobileApps]et les [Applications d’API][APIApps].  

Environnements de Service d’application sont idéales pour des charges de travail nécessitant une :

- Très grande échelle
- Isolement et un accès réseau sécurisé

Les clients peuvent créer plusieurs environnements de Service d’application au sein d’une seule région d’Azure, ainsi que dans plusieurs régions Azure.  Environnements de Service d’application sont donc parfaits pour l’évolution horizontale des couches d’application sans état à l’appui des charges élevées de RPS.

Environnements de Service d’application sont isolés pour exécuter uniquement les applications d’un client unique et sont toujours déployés dans un réseau virtuel.  Les clients ont un contrôle précis sur le trafic réseau entrant et sortant des applications et applications peuvent établir des connexions sécurisées à grande vitesse sur des réseaux virtuels pour les ressources de l’entreprise locale.

Tous les articles et comment-de sur les environnements de Service d’application sont disponibles dans le [fichier Lisezmoi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Pour une vue d’ensemble des environnements de Service d’application activer échelle élevée et sécuriser les accès réseau, reportez-vous à l' [Immersion de AzureCon] [ AzureConDeepDive] sur les environnements de Service d’application !

Pour un approfondi sur la mise à l’échelle horizontalement à l’aide de plusieurs environnements de Service d’application voir l’article sur la façon de configurer un [encombrement de géo-distribué une application][GeodistributedAppFootprint].

Pour afficher la configuration de l’architecture de sécurité indiqué dans l’immersion de AzureCon, consultez l’article sur l’implémentation d’une [architecture de sécurité de couche](app-service-app-service-environment-layered-security.md) avec les environnements de Service d’application.

Les applications en cours d’exécution sur les environnements de Service d’application peuvent avoir leur accès contrôlé par des dispositifs tels que des pare-feu d’applications web (WAF) en amont.  L’article sur la [configuration d’un WAF pour les environnements de Service d’application](app-service-app-service-environment-web-application-firewall.md) couvre ce scénario. 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="dedicated-compute-resources"></a>Ressources de calcul dédiée ##
Toutes les ressources de calcul dans un environnement de Service d’application sont réservés exclusivement à un seul abonnement et un environnement de Service d’application peut être configuré avec les ressources de calcul maximum de cinquante (50) pour une utilisation exclusive par une seule application.

Un environnement de Service d’application se compose d’un pool de ressources de calcul front-end, ainsi que les pools de ressources de calcul travailleur un à trois. 

Le pool frontal contient des ressources de calcul responsable de l’arrêt de SSL comme bien équilibrer automatiquement la charge des demandes d’application dans un environnement de Service d’application. 

Chaque pool de travail contient des ressources de calcul allouées aux [Programmes de Service d’application][AppServicePlan], qui à leur tour contiennent une ou plusieurs applications de Service d’application Azure.  Dans la mesure où il peut y avoir jusqu'à trois pools de travail différents dans un environnement de Service d’application, vous avez la possibilité de choisir les ressources de calcul différent pour chaque pool de travail.  

Cela permet, par exemple créer un pool de travail avec les ressources informatiques moins puissants pour les Plans de Service application conçue pour les applications de développement ou de test.  Un pool de travail de seconde (ou même tiers) peut utiliser les ressources de calcul plus puissants destinés à Plans de Service application exécutant des applications de production.

Pour plus d’informations sur la quantité de ressources de calcul disponibles pour les pools de front-end et de travail, reportez-vous à la section [comment configurer un environnement de Service d’application][HowToConfigureanAppServiceEnvironment].  

Pour plus d’informations sur la disposition calculer les tailles de ressource pris en charge dans un environnement de Service d’application, consultez le [Tarif de Service de l’application] [ AppServicePricing] page et passez en revue les options disponibles pour les environnements de Service d’application de la prime de niveau de prix.

## <a name="virtual-network-support"></a>Prise en charge de réseau virtuel ##
Un environnement de Service d’application peut être créé dans **un** un réseau virtuel du Gestionnaire de ressources Azure, **ou** un déploiement classique de modèle de réseau virtuel ([plus d’informations sur les réseaux virtuels][MoreInfoOnVirtualNetworks]).  Dans la mesure où il existe toujours un environnement de Service d’application dans un réseau virtuel, et plus précisément dans un sous-réseau d’un réseau virtuel, vous pouvez exploiter les fonctionnalités de sécurité des réseaux virtuels pour contrôler les communications réseau entrantes et sortantes.  

Un environnement de Service d’application peut être soit ouvert avec une adresse IP publique, ou interne en vis-à-vis avec uniquement une adresse Azure interne charge Équilibrage (ILB) sur Internet.

Vous pouvez utiliser des [groupes de sécurité réseau] [ NetworkSecurityGroups] de limiter les communications réseau entrantes pour le sous-réseau où se trouve un environnement de Service d’application.  Cela vous permet d’exécuter des applications derrière les périphériques situés en amont et les services de pare-feu d’application web et les fournisseurs de SaaS de réseau.

Applications doivent souvent accéder à des ressources d’entreprise telles que les bases de données internes et des services web.  Une approche courante consiste à rendre ces points de terminaison disponibles uniquement pour le trafic réseau interne s’ECOULANT dans un réseau virtuel Azure.  Une fois qu’un environnement de Service d’application est joint au même réseau virtuel que les services internes, les applications en cours d’exécution dans l’environnement de leur accès, y compris les points de terminaison accessibles via le [Site à] [ SiteToSite] et [Azure ExpressRoute] [ ExpressRoute] les connexions.

Pour plus d’informations sur le fonctionnement des environnements de Service d’application avec les réseaux locaux et les réseaux virtuels, consultez les articles suivants sur [l’Architecture de réseau][NetworkArchitectureOverview], [Contrôler le trafic entrant][ControllingInboundTraffic]et de [Connexion sécurisée pour les serveurs principaux][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Mise en route

Pour vous familiariser avec les environnements de Service d’application, reportez-vous à la section [Comment pour créer une application Service environnement][HowToCreateAnAppServiceEnvironment]

Tous les articles et comment-de pour les environnements de Service d’application sont disponibles dans le [fichier Lisezmoi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Pour plus d’informations sur la plate-forme de services d’application Azure, consultez le [Service d’application Azure][AzureAppService].

Pour une vue d’ensemble de l’architecture de réseau d’environnement de Service d’application, consultez la [Présentation de l’Architecture réseau] [ NetworkArchitectureOverview] l’article.

Pour plus d’informations sur l’utilisation d’un environnement de Service d’application avec ExpressRoute, consultez l’article suivant sur [l’itinéraire de l’Express et les environnements de Service d’application][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->

 
