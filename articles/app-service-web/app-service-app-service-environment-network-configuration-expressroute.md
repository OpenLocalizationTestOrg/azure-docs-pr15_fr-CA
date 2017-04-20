<properties 
    pageTitle="Détails de Configuration de réseau pour l’utilisation de l’itinéraire de Express" 
    description="Détails de la configuration réseau pour les environnements de Service d’application en cours d’exécution dans un réseaux virtuels connectés à un Circuit de ExpressRoute." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="stefsch"/>   

# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Détails de la Configuration réseau pour les environnements de Service d’application avec ExpressRoute 

## <a name="overview"></a>Vue d’ensemble ##
Les clients peuvent connecter un [Azure ExpressRoute] [ ExpressRoute] le circuit à leur infrastructure de réseau virtuel, étendant ainsi leur réseau local vers Azure.  Un environnement de Service d’application peuvent être créé dans un sous-réseau de ce [réseau virtuel] [ virtualnetwork] infrastructure.  Les applications en cours d’exécution sur l’environnement de Service d’application peuvent puis établir des connexions sécurisées aux ressources back-end accessibles uniquement via la connexion ExpressRoute.  

Un environnement de Service d’application peut être créé dans **un** un réseau virtuel du Gestionnaire de ressources Azure, **ou** un déploiement classique de modèle de réseau virtuel.  Avec une modification récente de juin 2016, ASEs peuvent désormais également être déployés dans des réseaux virtuels qui utilisent (c'est-à-dire les plages d’adresses publiques ou des espaces d’adressage RFC1918 adresses privées). 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="required-network-connectivity"></a>Connectivité du réseau. ##
Il existe des exigences en matière de connectivité réseau pour les environnements de Service d’application qui ne peuvent pas être satisfaites au départ dans un réseau virtuel est connecté à un ExpressRoute.  Environnements de Service d’application nécessitent toutes les caractéristiques suivantes pour fonctionner correctement :


-  Connectivité de réseau sortant vers le stockage Azure points de terminaison dans le monde entier sur les ports 80 et 443.  Cela inclut des points de terminaison situés dans la même région, comme l’environnement de Service d’application, ainsi que les points de terminaison de stockage situés dans les **autres** régions Azure.  Résoudre les points de terminaison de stockage Azure sous les domaines DNS suivants : *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* et *file.core.windows.net*.  
-  Connectivité de réseau sortante pour le service de fichiers d’Azure sur le port 445.
-  Connectivité réseau sortant aux points de terminaison de base de données Sql que qui se trouve dans la même région que l’environnement de Service d’application.  Résoudre les points de terminaison de base de données SQL sous le domaine suivant : *database.windows.net*.  Cela implique l’ouverture de l’accès aux ports 1433, 11999-11000 et 14000-14999.  Pour plus d’informations, consultez [cet article sur l’utilisation du port V12 de base de données Sql](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
-  Connectivité de réseau sortante pour les points de terminaison de plan gestion Azure (points de terminaison à la fois ASM et ARM).  Cela inclut la connectivité sortante à la fois *management.core.windows.net* et *management.azure.com*. 
-  Connectivité de réseau sortante pour *ocsp.msocsp.com*, *mscrl.microsoft.com* et *crl.microsoft.com*.  Cela est nécessaire pour prendre en charge les fonctionnalités SSL.
-  La configuration de DNS pour le réseau virtuel doit être capable de résoudre tous les points de terminaison et les domaines mentionnés dans les points précédents.  Si ces points de terminaison ne peut pas être résolus, tentatives de création d’environnement de Service d’application échouera et environnements de Service d’application existant sera marqués comme défectueux.
-  L’accès sortant sur le port 53 est requis pour la communication avec les serveurs DNS.
-  Si un serveur DNS personnalisé existe sur l’autre extrémité d’une passerelle de VPN, le serveur DNS doit être accessible à partir du sous-réseau contenant de l’environnement de Service d’application. 
-  Le chemin d’accès réseau sortant ne peut pas transiter via des proxys d’entreprise internes, ni qu’il peut être acheminés via le tunnel local de force.  Passe alors l’adresse NAT efficace du trafic réseau sortant à partir de l’environnement de Service d’application.  Modification de l’adresse NAT du trafic réseau sortant d’un environnement de Service application entraînera des problèmes de connectivité à la plupart des points de terminaison répertoriés ci-dessus.  Cela entraîne des échecs de création environnement de Service d’application, ainsi que des environnements de Service application sain précédemment marqué comme défectueux.  
-  Accès réseau entrant sur les ports requis pour les environnements de Service d’application doivent être autorisés comme décrit dans cet [article][requiredports].

Le DNS peut être requise en garantissant une infrastructure DNS valide est configurée et gérée pour l’ordinateur virtuel.  Si pour une raison quelconque, la configuration DNS est modifiée après la création d’un environnement de Service d’application, les développeurs peuvent forcer un environnement de Service d’application pour prélever la nouvelle configuration de DNS.  Déclencher un redémarrage d’environnement propagée à l’aide de l’icône « Redémarrer » situé en haut de la blade de gestion d’environnement de Service d’application dans [Azure portal] [ NewPortal] entraîne l’environnement prélever la nouvelle configuration de DNS.

Les exigences d’accès réseau entrant peuvent être respectées en configurant un [groupe de sécurité de réseau] [ NetworkSecurityGroups] sur le sous-réseau de l’environnement Service application pour autoriser l’accès requis, comme décrit dans cet [article][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>L’activation de la connectivité réseau sortante pour un environnement de Service d’application##
Par défaut, un circuit de ExpressRoute nouvellement créé publie un itinéraire par défaut qui permet la connectivité Internet sortante.  Avec cette configuration, un environnement de Service d’application seront en mesure de se connecter aux autres points de terminaison Azure.

Cependant, une configuration de client commun est de définir leur propre itinéraire par défaut (0.0.0.0/0) qui force le trafic Internet sortant à la place de flux local.  Ce flux de trafic sauts invariablement environnements de Service d’application, car le trafic sortant est bloqué sur site ou NAT serait sur un nombre d’adresses qui ne fonctionnent plus avec différents points de terminaison Azure non reconnaissable.

La solution consiste à définir un (ou plusieurs) des itinéraires définis par l’utilisateur (UDRs) sur le sous-réseau qui contient l’environnement de Service d’application.  Un UDR définit les itinéraires de sous-réseau spécifique qui seront respectées au lieu de l’itinéraire par défaut.

Si possible, il est recommandé d’utiliser la configuration suivante :

- La configuration de ExpressRoute publie 0.0.0.0/0 et par défaut force tunnels tout le trafic sortant sur site.
- Le UDR appliquée au sous-réseau contenant de l’environnement de Service d’application définit 0.0.0.0/0 avec un type de saut suivant de l’Internet (un exemple de cela est plu bas dans cet article).

L’effet combiné de ces étapes est que le niveau de sous-réseau UDR ont priorité sur le ExpressRoute forcé tunneling, garantissant ainsi un accès sortant à Internet à partir de l’environnement de Service d’application.

> [AZURE.IMPORTANT] Les itinéraires définis dans une UDR **doit** être suffisamment ont la priorité sur les itinéraires annoncés par la configuration de ExpressRoute.  L’exemple ci-dessous utilise la plage d’adresses 0.0.0.0/0 large et en tant que tel peut potentiellement être accidentellement substituée par les annonces de routage à l’aide de plages d’adresses plus spécifiques.
>
>Environnements de Service d’application ne sont pas pris en charge avec les configurations ExpressRoute **cross-publier des itinéraires à partir du chemin d’homologation public pour le chemin d’accès d’homologation privé**.  Configurations de ExpressRoute ayant un public peering configuré, recevra les annonces de routage de Microsoft sur un vaste ensemble de plages d’adresses IP de Microsoft Azure.  Si ces plages d’adresses sont annoncés entre sur le chemin d’accès d’homologation privé, le résultat final est que tous les paquets réseau sortante du sous-réseau de l’environnement Service application sera en tunnel de force à l’infrastructure de réseau local d’un client.  Ce flux de réseau n’est actuellement pas pris en charge avec les environnements de Service d’application.  Une solution à ce problème consiste à arrêter des itinéraires de cross-publicité à partir du chemin d’homologation public pour le chemin d’accès d’homologation privé.

Informations générales sur les itinéraires définis par l’utilisateur sont disponibles dans cette [vue d’ensemble]de l'[UDROverview].  

Pour plus d’informations sur la création et la configuration des itinéraires définis par l’utilisateur est disponible dans ce [Mode Guide][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Exemple de Configuration UDR pour un environnement de Service d’application ##

**Conditions préalables**

1. Installer Azure Powershell à partir de la [page de téléchargements d’Azure] [ AzureDownloads] (datée de juin 2015 ou version ultérieure).  Sous « Outils de ligne de commande » est une liaison de « Installation » sous « Windows Powershell » qui va installer les applets de commande Powershell plus récente.

2. Il est recommandé qu’un sous-réseau unique est créé pour une utilisation exclusive par un environnement de Service d’application.  Cela garantit que les UDRs appliqués au sous-réseau ouvre uniquement le trafic sortant pour l’environnement de Service d’application.
3. **Important**: ne pas déployer l’environnement de Service d’application qu' **après avoir** suivi les étapes de configuration suivantes.  Cela garantit que la connectivité réseau sortant est disponible avant d’essayer de déployer un environnement de Service d’application.

**Étape 1 : Créer une table d’itinéraires nommés**

L’extrait de code suivant crée une table de routage appelée « DirectInternetRouteTable » dans la région Ouest nous Azure :

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Étape 2 : Créer un ou plusieurs itinéraires dans la table de routage**

Vous devez ajouter un ou plusieurs itinéraires à la table de routage afin de permettre d’accéder à Internet.  

L’approche recommandée pour la configuration de l’accès sortant à Internet est de définir une gamme de 0.0.0.0/0 comme indiqué ci-dessous.
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

N’oubliez pas que 0.0.0.0/0 est une plage d’adresses d’étendue, en tant que tel sera remplacée par les plages d’adresses plus spécifiques annoncées par le ExpressRoute.  Pour ré-itérer au sein de cette recommandation antérieure, un UDR avec un itinéraire 0.0.0.0/0 doit être utilisée conjointement avec une configuration ExressRoute qui n’en annonçant ainsi 0.0.0.0/0. 

Comme alternative, vous pouvez télécharger une liste complète et mise à jour des plages CIDR en cours d’utilisation par Azure.  Le fichier Xml contenant toutes les plages d’adresses IP d’Azure est disponible à partir du [Centre de téléchargement Microsoft][DownloadCenterAddressRanges].  

Notez cependant que ces plages changent avec le temps, d'où la nécessité des mises à jour manuelles périodiques pour les itinéraires définis par l’utilisateur de synchroniser.  Également, dans la mesure où il existe une limite par défaut de 100 itinéraires dans un seul UDR, vous devrez « synthèse » les plages d’adresses IP d’Azure pour tenir dans les limites de la 100 gamme, en gardant à l’esprit que UDR défini les itinéraires doivent être plus spécifique que les itinéraires annoncés par votre ExpressRoute.  


**Étape 3 : Associer la table de routage pour le sous-réseau contenant de l’environnement de Service d’application**

La dernière étape de la configuration est à associer la table de routage pour le sous-réseau où l’environnement de Service d’application sera déployée.  La commande suivante associe le « DirectInternetRouteTable » pour le « ASESubnet » qui contiendra un environnement de Service d’application.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Étape 4 : Dernières étapes**

Une fois la table de routage est liée au sous-réseau, il est recommandé de commencer par tester et confirmer l’effet souhaité.  Par exemple, déployer un ordinateur virtuel sur le sous-réseau et confirmez que :


- Le trafic sortant vers les points de terminaison Azure et non-Azure mentionné plus haut dans cet article est circulent **pas** vers le bas le circuit de ExpressRoute.  Il est très important de vérifier ce comportement, car si le trafic sortant à partir du sous-réseau est toujours mise en tunnel local, environnement de Service d’application de création qui échouera toujours. 
- Les recherches DNS pour les points de terminaison mentionné plus haut sont tous résout correctement. 

Une fois les étapes ci-dessus sont confirmées, vous devez supprimer l’ordinateur virtuel car le sous-réseau doit être « vide » au moment de que la création de l’environnement de Service d’application.
 
Passez ensuite à la création d’un environnement de Service d’application !

## <a name="getting-started"></a>Mise en route
Tous les articles et comment-de pour les environnements de Service d’application sont disponibles dans le [fichier Lisezmoi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Pour vous familiariser avec les environnements de Service d’application, consultez [Introduction à l’environnement de Service d’application][IntroToAppServiceEnvironment]

Pour plus d’informations sur la plate-forme de services d’application Azure, consultez le [Service d’application Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]:  https://portal.azure.com
 

<!-- IMAGES -->
