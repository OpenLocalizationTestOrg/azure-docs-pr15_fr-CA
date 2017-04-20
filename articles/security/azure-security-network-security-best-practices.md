<properties
   pageTitle="Meilleures pratiques de sécurité réseau Azure | Microsoft Azure"
   description="Cet article fournit un ensemble de meilleures pratiques pour l’utilisation de sécurité réseau Azure capacités intégrées."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor="TomShinder"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/25/2016"
   ms.author="TomSh"/>

# <a name="azure-network-security-best-practices"></a>Meilleures pratiques de sécurité réseau Azure

Microsoft Azure permet de connecter des machines virtuelles et les équipements à d’autres périphériques réseau en les plaçant sur des réseaux virtuels Azure. Un réseau virtuel Azure est une construction de réseau virtuel qui vous permet de connecter des cartes d’interface réseau virtuelle à un réseau virtuel pour autoriser les communications basées sur IP entre les périphériques réseau activé. Azure ordinateurs virtuels connectés à un réseau virtuel Azure sont en mesure de se connecter à des périphériques sur le même Azure réseau virtuel, différents réseaux virtuels Azure, sur Internet ou même sur vos réseaux locaux.

Dans cet article, nous allons étudier un ensemble de meilleures pratiques de sécurité réseau Azure. Ces recommandations sont dérivées de notre expérience avec un réseau Azure et l’expérience des clients tels que vous-même.

Pour chaque meilleure pratique, nous allons expliquer :

- Quelle est la meilleure pratique
- Raison pour laquelle vous souhaitez activer que les meilleures pratiques
- Quel peut être le résultat si vous ne parvenez pas à activer la meilleure pratique
- Alternatives possibles à la pratique recommandée
- Comment vous pouvez apprendre à activer la meilleure pratique

Cet article de meilleures pratiques de la sécurité de réseau Azure est basé sur un avis de consensus et les fonctionnalités de la plateforme Azure et les ensembles de fonctionnalités, tels qu’ils existent au moment de la que rédaction de cet article. Les technologies et les avis de changer avec le temps, et cet article sera mis à jour régulièrement pour refléter ces modifications.

Azure réseau meilleures pratiques de sécurité abordées dans cet article sont les suivantes :

- Logiquement les sous-réseaux de segment
- Contrôler le comportement de routage
- Activer le Tunneling de force
- Utiliser des équipements de réseau virtuel
- Déployer la DMZ pour le zonage de sécurité
- Éviter l’exposition à Internet avec les liaisons WAN dédiées
- Optimiser les performances et la disponibilité
- Utilisez l’équilibrage de la charge globale
- Désactiver l’accès à des ordinateurs virtuels Azure RDP
- Activer le centre de sécurité Azure
- Étendre votre centre de données dans Azure


## <a name="logically-segment-subnets"></a>Logiquement les sous-réseaux de segment

[Les réseaux virtuels Azure](https://azure.microsoft.com/documentation/services/virtual-network/) sont similaires à un réseau local de votre réseau local. L’idée derrière un réseau virtuel Azure est de créer un seul réseau IP privé adresse basée sur un espace sur lequel vous pouvez placer tous vos [ordinateurs virtuels Azure](https://azure.microsoft.com/services/virtual-machines/). Les espaces d’adressage IP privés disponibles sont dans la classe A (10.0.0.0/8), la classe B (de 172.16.0.0/12) et la classe C (192.168.0.0/16) plages.

Similaire à ce que vous effectuez sur site, vous devez segmenter le plus grand espace d’adressage en sous-réseaux. Principes de gestion des sous-réseaux [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) en fonction vous permet de créer vos sous-réseaux.

Le routage entre les sous-réseaux se produira automatiquement et vous n’avez pas besoin de configurer manuellement les tables de routage. Toutefois, le paramètre par défaut est qu’il n’y a aucun contrôle d’accès réseau entre les sous-réseaux que vous créez sur le réseau virtuel Azure. Pour créer des contrôles d’accès réseau entre les sous-réseaux, vous devez faire quelque chose entre les sous-réseaux.

L’une des choses que vous pouvez utiliser pour accomplir cette tâche est un [Groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) (NSG). NSGs sont les périphériques d’inspection des paquets avec état simple qui utilisent le 5-uplet (l’adresse IP source, port source, adresse IP de destination, port de destination et protocole de la couche 4) approche pour créer autoriser/refuser les règles pour le trafic réseau. Vous pouvez autoriser ou refuser le trafic vers et à partir de l’adresse IP unique, vers et à partir de plusieurs adresses IP ou même et sous-réseaux entiers.

Pour le contrôle d’accès réseau entre les sous-réseaux à l’aide de NSGs vous permet de placer les ressources qui appartiennent à la même zone de sécurité ou d’un rôle dans leurs propres sous-réseaux. Par exemple, considérez une application à 3 couches simple qui a un niveau web, une couche de logique d’application et un niveau de base de données. Vous placez des machines virtuelles qui appartiennent à chacune de ces couches dans leurs propres sous-réseaux. Ensuite, vous utilisez NSGs pour contrôler le trafic entre les sous-réseaux :

- Hiérarchiser les machines virtuelles Web peut uniquement établir des connexions avec les ordinateurs de logique d’application et qu’il accepte uniquement les connexions à partir d’Internet
- Ordinateurs virtuels d’application logique permet de lancer des connexions avec la couche de base de données et ne peut accepter que les connexions à partir de la couche web
- Machines virtuelles de niveau base de données ne peut pas établir de connexion quoi que ce soit à l’extérieur de leur propre sous-réseau et accepte uniquement les connexions à partir de la couche de logique d’application

Pour plus d’informations sur les groupes de sécurité réseau et comment vous pouvez les utiliser pour segmenter de façon logique vos réseaux virtuels Azure, lisez l’article [ce qu’est un groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Contrôler le comportement de routage

Lorsque vous placez une machine virtuelle sur un réseau virtuel d’Azure, vous pouvez constater que la machine virtuelle peut se connecter à d’autres machines virtuelles sur le même réseau virtuel Azure, même si les autres ordinateurs virtuels sont sur des sous-réseaux différents. Pourquoi cette opération est possible parce qu’il existe une collection d’itinéraires de système sont activées par défaut qui permettent ce type de communication. Ces itinéraires par défaut permettent de machines virtuelles sur un même réseau virtuel Azure à établir des connexions entre eux et avec Internet (pour les communications sortantes vers Internet uniquement).

Tandis que les itinéraires de système par défaut sont utiles pour de nombreux scénarios de déploiement, il est parfois lorsque vous souhaitez personnaliser la configuration de routage de vos déploiements. Ces personnalisations vous permettra de configurer l’adresse de tronçon suivant pour atteindre des destinations spécifiques.

Nous vous recommandons de configurer des itinéraires définis par l’utilisateur lorsque vous déployez une appliance de sécurisation de réseau virtuel, nous allons parler dans une version ultérieure est recommandé.

> [AZURE.NOTE] les itinéraires définis par l’utilisateur ne sont pas nécessaires et les itinéraires du système par défaut fonctionne dans la plupart des cas.

Pour plus d’informations sur les itinéraires définis par l’utilisateur et comment les configurer en lisant l’article [ce que sont les itinéraires définis par l’utilisateur et le routage IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Activer le Tunneling de force

Pour mieux comprendre le tunneling forcé, il est utile de comprendre est que « la division tunneling ».
L’exemple le plus courant de tunneling fractionné est vu avec les connexions VPN. Imaginez que vous établissez une connexion VPN à partir de votre chambre d’hôtel à votre réseau d’entreprise. Cette connexion vous permet de vous connecter à des ressources sur votre réseau d’entreprise et les ressources de votre réseau d’entreprise de toutes les communications passent par le tunnel VPN.

Que se passe-t-il lorsque vous souhaitez vous connecter à des ressources sur Internet ? Lorsque le tunneling fractionné est activé, les connexions accéder directement à Internet et non via le tunnel VPN. Des experts en sécurité considèrent que cela à un risque potentiel et donc est recommandé que le tunneling fractionné est désactivée et toutes les connexions, ceux destinés à Internet et ceux destinés à des ressources d’entreprise, passent par le tunnel VPN. L’avantage de cette opération est que les connexions à Internet sont ensuite forcées par les dispositifs de sécurité réseau de l’entreprise, qui ne serait pas le cas si le client VPN connecté à Internet en dehors du tunnel VPN.

Maintenant nous allons ramener ce aux machines virtuelles sur un réseau virtuel d’Azure. Les itinéraires par défaut pour un réseau virtuel Azure permettent à des ordinateurs virtuels à initier un trafic vers Internet. Ce trop peut représenter un risque pour la sécurité, que ces connexions sortantes peuvent augmenter la surface d’attaque d’un ordinateur virtuel et être exploitées par les pirates.
Pour cette raison, nous vous recommandons d’activer le tunneling forcé sur vos ordinateurs virtuels lorsque vous disposez de connectivité de la coexistence entre votre réseau virtuel Azure et votre réseau local. Nous parlerons croisée connectivité locaux plus loin dans ce document de pratiques meilleur réseau Azure.

Si vous ne disposez pas d’une connexion entre des locaux, assurez-vous que vous tirer parti des groupes de sécurité réseau (décrit précédemment) ou Azure virtuel des appliances de sécurisation (présentée suivant) pour empêcher les connexions sortantes à Internet à partir de vos Machines virtuelles de Azure du réseau.

Pour en savoir plus sur forcé tunneling et la procédure d’activation, veuillez lire l’article [configuration forcée Tunneling à l’aide de PowerShell et le Gestionnaire de ressources Azure](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Utiliser des applications de réseau virtuel

Alors que les groupes de sécurité réseau et le routage défini par l’utilisateur peuvent fournir une certaine sécurité réseau au niveau des couches réseau et transport du [modèle OSI](https://en.wikipedia.org/wiki/OSI_model), il doit y avoir des situations où vous voulez ou devez activer la sécurité au niveau de hauts niveaux de la pile. Dans ce cas, nous vous recommandons de déployer des appliances de sécurisation de réseau virtuel fournis par des partenaires Azure.

Appliances de sécurisation de réseau Azure peuvent fournir des niveaux de sécurité améliorés de manière significative sur ce qui est fourni par les contrôles de niveau réseau. Parmi les fonctionnalités de sécurité réseau fournies par les appliances de sécurisation de réseau virtuel :

- Installation de pare-feu
- Intrusion/détection des intrusions Prevention
- Gestion des vulnérabilités
- Contrôle de l’application
- Détection d’anomalie de réseau
- Filtrage de contenu Web
- Antivirus
- Protection du réseau de bots

Si vous avez besoin d’un niveau de sécurité réseau que vous pouvez obtenir avec les contrôles d’accès au réseau, nous vous recommandons que vous examinez et déployez des appliances de sécurité Azure réseau virtuel.

Pour obtenir des informations sur les appliances de sécurité Azure réseau virtuel sont disponibles et leurs fonctions, veuillez consulter [l’Azure Marketplace](https://azure.microsoft.com/marketplace/) et recherchez « sécurité » et « sécurité du réseau ».

##<a name="deploy-dmzs-for-security-zoning"></a>Déployer la DMZ pour le zonage de sécurité
Une DMZ ou « réseau de périmètre » est un segment de réseau physique ou logique qui est conçu pour fournir une couche supplémentaire de sécurité entre vos actifs et d’Internet. L’objectif de la zone DMZ consiste à placer des dispositifs de contrôle d’accès réseau spécialisée sur le bord du réseau DMZ afin qu’uniquement le trafic souhaité est autorisé après le périphérique de sécurité réseau et à votre réseau virtuel Azure.

DMZ est utiles car vous pouvez vous concentrer la gestion de contrôle de l’accès réseau, surveillance, de journalisation et de reporting sur les périphériques à la périphérie de votre réseau virtuel Azure. Ici vous permettrait généralement DDoS prévention, les systèmes de prévention d’Intrusion/détection des intrusions (IDS/IPS), les règles de pare-feu et stratégies, filtrage de contenu web, réseau contre les logiciels malveillants et plus. Les périphériques de sécurité réseau placé entre Internet et votre réseau virtuel Azure et disposent d’une interface sur les deux réseaux.

Bien que ce soit la conception de base d’un réseau de périmètre, il sont nombreuses conceptions DMZ, tels que DOS à DOS, triple hébergement, multi-hébergés et d’autres.

Il est recommandé pour tous les déploiements de haute sécurité que vous envisagez de déployer une zone DMZ pour améliorer le niveau de sécurité de réseau pour vos ressources Azure.

Pour en savoir plus sur la DMZ et comment les déployer dans Azure, veuillez lire l’article [Microsoft Services en nuage et la sécurité du réseau](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Éviter l’exposition à Internet avec les liaisons WAN dédiées
De nombreuses organisations ont choisi la gamme informatique hybride. Hybrides d’informatique, des ressources d’informations de la société sont dans Azure, tandis que d’autres utilisateurs restent sur site. Dans de nombreux cas certains composants d’un service exécutera dans Azure pendant que les autres composants sont locaux.

Dans le scénario de l’informatique d’hybrides, il existe généralement un certain type de connectivité de coexistence. Cette coexistence connectivité permet à l’entreprise à leurs réseaux locaux de se connecter à des réseaux virtuels Azure. Il existe deux solutions de connectivité entre sites :

- VPN de site à site
- ExpressRoute

[VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md) représente une connexion privée virtuelle entre votre réseau local et d’un réseau virtuel d’Azure. Cette connexion s’effectue via Internet et permet de « tunnel » des informations à l’intérieur d’une liaison cryptée entre votre réseau et d’Azure. VPN de site à site est une technologie mature sécurisée qui a été déployée par les entreprises de toutes tailles, depuis des décennies. Cryptage de tunnel est effectué à l’aide du [mode de tunnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

VPN de site à site est une technologie de confiance et fiable établie, le trafic véhiculé par le tunnel ne passe pas par Internet. En outre, la bande passante est relativement limitée à un maximum de sur 200Mbps.

Si vous avez besoin d’un niveau exceptionnel de performances ou la sécurité pour vos connexions de coexistence, nous vous conseillons Azure ExpressRoute pour votre connectivity de coexistence. ExpressRoute est un réseau étendu dédié lien entre l’emplacement local ou d’un fournisseur d’hébergement de Exchange. Car il s’agit d’une connexion de télécommunication, vos données ne voyagent pas sur Internet et par conséquent ne sont pas exposées à des risques potentiels inhérents dans les communications Internet.

Pour en savoir plus sur le fonctionnement de ExpressRoute d’Azure et comment déployer, lisez l’article [Vue d’ensemble technique de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Optimiser les performances et la disponibilité
Confidentialité, intégrité et disponibilité (CID) comprennent les triples plus d’influent du aujourd'hui modèle de sécurité. La confidentialité est sur le cryptage et confidentialité, intégrité consiste à s’assurer que les données ne sont pas modifiées par des personnes non autorisées, et disponibilité consiste à s’assurer que les personnes autorisées sont en mesure d’accéder aux informations qu’ils sont autorisés à accéder. Échec dans l’une de ces zones représente une faille potentielle dans la sécurité.

Disponibilité peut être considérée comme étant sur la disponibilité et de performances. Si un service est arrêté, des informations ne sont pas accessibles. Si la performance est tellement médiocre à rendre les données inutilisables, nous pouvons considérer que les données soient inaccessibles. Par conséquent, du point de vue de la sécurité, nous devons faire tout ce que nous pouvons pour vous assurer que nos services ont des performances et une disponibilité optimale.
Une méthode efficace populaire et les plus utilisée pour améliorer la disponibilité et les performances est d’utiliser l’équilibrage de charge. L’équilibrage de charge est une méthode de distribution du trafic réseau sur les serveurs qui font partie d’un service. Par exemple, si vous avez des serveurs web frontaux dans le cadre de votre service, vous pouvez utiliser l’équilibrage de charge pour distribuer le trafic sur vos serveurs web frontaux.

Cette distribution de trafic augmente la disponibilité, car si un des serveurs web devient indisponible, l’équilibreur de charge va arrêter l’envoi de trafic à ce serveur et rediriger le trafic vers les serveurs qui sont encore en ligne. Équilibrage de la charge permet également de performances, car le processeur, le réseau et la mémoire surcharge pour servir les requêtes est répartie sur la charge de tous les serveurs d’équilibrée.

Nous vous recommandons d’utiliser l’équilibrage de charge autant que possible et en fonction de vos services. Nous traitons de pertinence dans les sections suivantes.
Au niveau du réseau virtuel d’Azure, Azure fournit que des trois principales options d’équilibrage de charge :

- L’équilibrage de charge basé sur HTTP
- L’équilibrage de charge externe
- L’équilibrage de charge interne

## <a name="http-based-load-balancing"></a>L’équilibrage de charge basé sur HTTP
L’équilibrage de charge basé sur HTTP bases de décisions sur le serveur d’envoyer des connexions à l’aide des caractéristiques du protocole HTTP. Azure a un équilibreur de charge HTTP qui passe par le nom de la passerelle d’Application.

Il est recommandé que vous nous Azure Application passerelle lorsque :

- Applications qui requièrent des demandes à partir de la même session utilisateur/client pour atteindre la même machine virtuelle back-end. Des exemples d’achat panier les applications et les serveurs de messagerie web.
- Applications que vous voulez libérer des batteries de serveurs web à partir de surcharge de terminaison SSL en tirant parti de la fonctionnalité [de déchargement SSL](https://f5.com/glossary/ssl-offloading) de la passerelle d’Application.
- Applications, comme un réseau de distribution de contenu, qui ont besoin de plusieurs demandes HTTP sur la même connexion TCP de durée d’exécution longue d’être routé ou de charger équilibrée sur les différents serveurs back-end.

Pour plus d’informations sur le fonctionnement de la passerelle d’Application Azure et comment vous pouvez l’utiliser dans votre déploiement, lisez l’article [Vue d’ensemble de la passerelle Application](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>L’équilibrage de charge externe
L’équilibrage de charge externe intervient lorsque des connexions entrantes à partir d’Internet sont équilibrées entre vos serveurs situés dans un réseau virtuel d’Azure. L’équilibreur de charge externe d’Azure peut vous fournir cette fonctionnalité et nous vous recommandons d’utiliser lorsque vous ne nécessitent pas les sessions persistantes ou de déchargement SSL.

Contrairement à l’équilibrage de charge basé sur HTTP, l’équilibreur de charge externe utilise les informations au niveau des couches réseau et transport du modèle réseau OSI à prendre des décisions sur le serveur pour charger des connexion de solde à.

Nous vous conseillons d’utiliser l’équilibrage de charge externe chaque fois que vous avez [les applications sans état](http://whatis.techtarget.com/definition/stateless-app) , accepter les demandes entrantes provenant d’Internet.

Pour en savoir plus sur le fonctionnement de l’équilibreur de charge externe Azure et comment vous pouvez déployer, veuillez lire l’article [commencer la création d’un équilibrage de la charge face à Internet dans le Gestionnaire de ressources à l’aide de PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>L’équilibrage de charge interne
Équilibrage de la charge interne est similaire à l’équilibrage de charge externe et utilise le même mécanisme de chargement d’équilibrer les connexions vers les serveurs placés derrière eux. La seule différence est que l’équilibreur de charge est dans ce cas accepte les connexions à partir d’ordinateurs virtuels qui ne sont pas sur Internet. Dans la plupart des cas, les connexions qui sont acceptées pour l’équilibrage de charge sont initiées par les périphériques sur un réseau virtuel d’Azure.

Nous vous conseillons d’utiliser interne équilibrage de charge pour les scénarios qui bénéficieront de cette fonctionnalité, par exemple lorsque vous devez charger le solde des connexions à des serveurs de SQL ou de serveurs web internes.

Pour plus d’informations sur le fonctionnement de l’équilibrage de charge interne Azure et comment vous pouvez le déployer, lisez l’article [commencer la création d’un équilibreur de charge interne à l’aide de PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Utilisez l’équilibrage de la charge globale
Rend informatique de nuage public est-il possible de déployer globalement des applications distribuées qui ont des composants situés dans des centres de données partout dans le monde. Cela est possible sur Microsoft Azure en raison de la présence de centre de données global d’Azure. Par opposition à l’équilibrage de technologies mentionnées plus haut, l’équilibrage de charge globale permet à disposition de services même lorsque les centres de données entières peuvent devenir indisponibles.

Vous pouvez obtenir ce type global d’équilibrage de charge dans Azure en tirant parti du [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/). Traffic Manager effectue est possible de charger des connexions de solde sur vos services en fonction de l’emplacement de l’utilisateur.

Par exemple, si l’utilisateur effectue une demande pour votre service de l’Union européenne, la connexion est dirigée à vos services situés dans un centre de données de l’Union européenne. Cette partie de Traffic Manager global charge équilibrage permet d’améliorer les performances, car la connexion au centre de données le plus proche est plus rapide qu’une connexion aux centres de données qui sont loin.

Sur le côté de la disponibilité, l’équilibrage de charge global permet de s’assurer que votre service est disponible même si un centre de données entier doit devenir disponible.

Par exemple, si un centre de données Azure devient indisponible en raison des raisons environnementales ou dues à des pannes (telles que les pannes de réseau régional), les connexions à votre service est reroutées vers le plus proche du centre de données en ligne. Cet équilibrage de la charge globale s’effectue en tirant parti des stratégies DNS que vous pouvez créer dans le Gestionnaire de trafic.

Nous vous recommandons d’utiliser le Gestionnaire de trafic pour n’importe quelle solution de cloud que vous développez qui a une portée largement distribuée dans plusieurs régions et qui requiert le plus haut niveau de disponibilité possible.

Pour plus d’informations sur Azure Traffic Manager et son déploiement, lisez l’article [qu’est le Gestionnaire de trafic](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Désactiver l’accès à des ordinateurs virtuels Azure RDP/SSH
Il est possible d’atteindre les ordinateurs virtuels de Azure à l’aide de [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) et des protocoles [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Ces protocoles permettent de gérer des ordinateurs virtuels à partir d’emplacements distants et sont standard dans le centre de données informatique.

Le problème de sécurité potentiel sur Internet à l’aide de ces protocoles est que les pirates peuvent utiliser des diverses techniques [d’attaque en force](https://en.wikipedia.org/wiki/Brute-force_attack) pour accéder à Azure Virtual Machines. Une fois que les pirates ont accès, vous pouvez utiliser votre ordinateur virtuel comme un point de lancement pour compromettre d’autres ordinateurs sur votre réseau virtuel Azure ou même attaquer des périphériques connectés au réseau en dehors d’Azure.

Pour cette raison, nous vous recommandons de désactiver un accès direct RDP et SSH à vos Machines virtuelles de Azure à partir d’Internet. Une fois l’accès SSH et RDP direct à partir d’Internet est désactivé, vous disposez d’autres options que vous pouvez utiliser pour accéder à ces ordinateurs virtuels pour la gestion à distance :

- VPN site-à-point
- VPN de site à site
- ExpressRoute

[Point-à-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) est un autre terme pour une connexion client/serveur VPN d’accès distant. Un réseau privé virtuel site-à-point permet à un utilisateur de se connecter à un réseau virtuel Azure via Internet. Une fois établie la connexion point-à-site, l’utilisateur sera en mesure d’utiliser le protocole RDP ou SSH pour se connecter à des ordinateurs virtuels sur le réseau virtuel Azure auquel l’utilisateur est connecté au point-à-site VPN. Cela suppose que l’utilisateur est autorisé à accéder à ces ordinateurs virtuels.

Point-à-site VPN est plus sûr que des connexions RDP ou SSH directes car l’utilisateur doit s’authentifier deux fois avant de vous connecter à un ordinateur virtuel. Tout d’abord, l’utilisateur doit s’authentifier (et autorisés) pour établir la connexion VPN point-à-site ; Ensuite, l’utilisateur doit s’authentifier (et autorisés) pour établir la session RDP ou SSH.

Un [VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md) connecte un réseau entier à un autre réseau via Internet. Vous pouvez utiliser un réseau VPN de site à site pour connecter votre réseau local à un réseau virtuel d’Azure. Si vous déployez un VPN de site à site, les utilisateurs sur votre réseau local peut-être se connecter aux machines virtuelles sur votre réseau virtuel Azure en utilisant le protocole RDP ou SSH sur la connexion VPN de site à site et ne nécessite pas autoriser l’accès RDP ou SSH directement sur Internet.

Vous pouvez également utiliser une liaison WAN dédiée pour fournir des fonctionnalités similaires à la connexion VPN de site à site. Les principales différences sont 1. la liaison réseau étendu dédiée ne parcourir Internet et 2. liaisons WAN dédiées sont généralement plus stable et performante. Azure fournit une solution de liaison WAN dédiée sous la forme de [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Activer le centre de sécurité Azure
Azure le centre de sécurité vous aide à empêcher, détecter et répondre aux menaces et fournit le qu'augmentation de visibilité et de contrôler, de la sécurité de vos ressources d’Azure. Il fournit la gestion de stratégie et de surveillance de la sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec un vaste écosystème de solutions de sécurité.

Le centre de sécurité Azure vous aide à optimiser et analyser la sécurité du réseau :

- Fournit des recommandations de sécurité réseau
- Surveillance de l’état de votre configuration de la sécurité réseau
- Vous alerte à réseau en fonction des menaces à la fois au niveau du point de terminaison et de réseau

Il est vivement recommandé d’activer le centre de sécurité Azure pour tous vos déploiements Azure.

Pour plus d’informations sur le centre de sécurité Azure et comment l’activer pour votre déploiement, lisez l’article [Introduction au centre de sécurité Azure](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Étendre en toute sécurité votre centre de données dans Azure
De nombreuses entreprises informatiques aux entreprises cherchent à développer dans le nuage au lieu de la croissance de leurs centres de données sur site. Cette extension représente une extension de l’infrastructure informatique dans le cloud public. En tirant parti de la coexistence des options de connectivité, il est possible de traiter vos réseaux virtuels Azure juste un autre sous-réseau sur votre infrastructure de réseau local.

Toutefois, il existe des beaucoup de problèmes de planification et de conception qui doivent être traités en premier. Ceci est particulièrement important dans le domaine de la sécurité du réseau. Une des meilleures manières de comprendre comment vous approcher de ce type de conception consiste à voir un exemple.

Microsoft a créé le [Schéma d’Architecture de référence Extension de centre de données](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) et la prise en charge de la documentation pour vous aider à comprendre à quoi ressemblerait un telle que ce prolongement de centre de données. Cela fournit un exemple d’implémentation de référence que vous pouvez utiliser pour planifier et concevoir une extension de datacenter d’entreprise sécurisé vers le nuage. Nous vous conseillons d’étudier ce document pour obtenir une idée des composants clés d’une solution sécurisée.

Pour en savoir plus sur la façon d’étendre en toute sécurité votre centre de données dans Azure, veuillez consulter la vidéo [Extension de votre Datacenter de Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
