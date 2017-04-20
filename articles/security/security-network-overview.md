<properties
   pageTitle="Vue d’ensemble de la sécurité réseau Azure | Microsoft Azure"
   description=" Cet article rend facile à comprendre ce que Microsoft Azure peut offrir dans la zone de sécurité du réseau. Nous fournir des explications de base sur les concepts de sécurité réseau et des exigences et des informations sur ce que Azure peut offrir dans chacun de ces domaines. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-network-security-overview"></a>Vue d’ensemble de la sécurité réseau Azure

Microsoft Azure inclut une infrastructure réseau fiable pour prendre en charge votre application et les besoins en connectivité de service. La connectivité réseau est possible entre les ressources situées dans Azure, entre les locaux et Azure hébergé des ressources et vers et à partir d’Internet et Azure.

L’objectif de cet article est pour le rendre plus facile à comprendre ce que Microsoft Azure peut offrir dans la zone de sécurité du réseau. Ici nous fournir des explications de base pour les concepts de sécurité réseau et des exigences. Nous propose également des informations sur ce que Azure peut offrir dans chacun de ces domaines. Il existe de nombreux liens vers d’autres contenus qui vous permettra d’obtenir une bonne compréhension dans les domaines qui vous intéressent.

Cet article de présentation de la sécurité réseau Azure se concentrera sur les éléments suivants :

- Mise en réseau Azure
- Contrôle d’accès réseau
- Assurer la connectivité de coexistence et de l’accès à distance
- Disponibilité
- Enregistrement dans le journal
- Résolution de noms
- Architecture de la zone DMZ
- Le centre de sécurité Azure

## <a name="azure-networking"></a>Mise en réseau Azure

Machines virtuelles ont besoin de connectivité réseau. Pour prendre en charge cette exigence, Azure nécessite des ordinateurs virtuels pour être connecté à un réseau virtuel d’Azure. Un réseau virtuel Azure est une construction logique basée sur le fabric physique réseau Azure. Chaque réseau virtuel Azure logique est isolé de tous les autres réseaux virtuels Azure. Cela permet de s’assurer que le trafic réseau dans vos déploiements n’est pas accessible aux autres clients Microsoft Azure.

Pour en savoir plus :

- [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Contrôle d’accès réseau
Contrôle d’accès réseau est le fait de limiter la connectivité vers et à partir de périphériques spécifiques ou de sous-réseaux au sein d’un réseau virtuel d’Azure. L’objectif de contrôle d’accès réseau est pour vous assurer que vos ordinateurs virtuels et les services sont accessibles aux utilisateurs et aux périphériques sur lequel vous souhaitez que les accessible uniquement. Contrôles d’accès basés sur Autoriser ou refuser des décisions pour les connexions vers et à partir de votre ordinateur virtuel ou d’un service.

Azure prend en charge plusieurs types de contrôle d’accès réseau. Celles-ci comprennent :

- Contrôle de la couche réseau
- Acheminer le contrôle et forcer le tunneling
- Appliances de sécurisation de réseau virtuel

### <a name="network-layer-control"></a>Contrôle de la couche réseau
Tout déploiement sécurisé nécessite une mesure de contrôle d’accès réseau. L’objectif de contrôle d’accès réseau est pour vous assurer que vos ordinateurs virtuels et les services réseau qui s’exécutent sur ces ordinateurs virtuels peuvent communiquer uniquement avec d’autres périphériques en réseau qu’ils doivent communiquer avec et toutes les autres tentatives de connexion sont bloquées.

Si vous avez besoin de contrôle d’accès au réseau de base (basé sur l’adresse IP et les protocoles TCP ou UDP), vous pouvez utiliser des groupes de sécurité réseau. Un groupe de sécurité réseau (NSG) est un pare-feu de filtrage de paquets avec état de base et vous permet de contrôler l’accès basé sur un [5-tuple](https://www.techopedia.com/definition/28190/5-tuple). NSGs ne fournissent pas d’inspection de la couche application ou authentifié de contrôles d’accès.

Pour en savoir plus :

- [Groupes de sécurité de réseau](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Contrôle de l’itinéraire et le Tunneling forcée
La capacité à contrôler le comportement de routage sur vos réseaux virtuels Azure est une fonction de contrôle de sécurité et d’accès réseau critiques. Si le routage est configuré correctement, les applications et les services hébergés sur votre machine virtuelle peuvent se connecter aux périphériques que vous ne souhaitez pas à se connecter, y compris les périphériques détenus et exploités par des pirates potentiels.

Mise en réseau Azure prend en charge la possibilité de personnaliser le comportement de routage du trafic réseau sur vos réseaux virtuels Azure. Cela vous permet de modifier les entrées de table de routage par défaut dans votre réseau virtuel Azure. Contrôle du comportement de routage vous permet de vous assurer que tout le trafic provenant d’un périphérique ou un groupe de périphériques entre ou quitte votre réseau virtuel Azure grâce à un emplacement spécifique.

Par exemple, peut avoir une appliance de sécurisation de réseau virtuel sur votre réseau virtuel Azure. Vous souhaitez vous assurer que tout le trafic vers et depuis votre réseau virtuel Azure passe par le biais de ce dispositif de sécurité virtuel. Pour cela, vous pouvez configurer [Des itinéraires définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md) dans Azure.

[Forcé de tunneling](https://www.petri.com/azure-forced-tunneling) est un mécanisme qui que vous permet de vous assurer que vos services ne sont pas autorisées à établir une connexion aux périphériques sur Internet. Notez que cela est différent d’accepter les connexions entrantes et la façon d’y répondre. Serveurs web frontaux doivent répondre à la demande à partir d’hôtes Internet, et donc source Internet le trafic est autorisé entrante sur ces serveurs web et les serveurs web sont autorisés à répondre.

Ce que vous ne voulez autoriser est un serveur web frontal pour lancer une requête sortante. Ces requêtes peuvent représenter un risque de sécurité car ces connexions permet de télécharger des logiciels malveillants. Même si vous ne voulez pas que ces serveurs frontaux pour lancer des requêtes sortantes vers Internet, vous pouvez souhaiter les forcer à passer par les proxies de votre web sur site afin que vous pouvez tirer parti de l’URL, le filtrage et la journalisation.

Au lieu de cela, vous pouvez utiliser le tunneling forcée pour éviter ce problème. Lorsque vous activez le tunneling forcée, toutes les connexions à Internet sont contraints par le biais de la passerelle locale. Vous pouvez configurer le tunneling forcé en tirant parti des itinéraires de défini par l’utilisateur.

Pour en savoir plus :

- [Quels sont les itinéraires définis par l’utilisateur et le routage IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Appliances de sécurisation de réseau virtuel
Tandis que les groupes de sécurité réseau, des itinéraires définis par l’utilisateur et tunneling forcée vous fournissent un niveau de sécurité au niveau des couches réseau et transport du [modèle OSI](https://en.wikipedia.org/wiki/OSI_model), il peut arriver lorsque vous souhaitez activer la sécurité au niveau supérieur dans le réseau.

Par exemple, vos besoins en matière de sécurité peuvent inclure :

- L’authentification et l’autorisation avant d’autoriser l’accès à votre application
- Détection d’intrusion et réponse d’intrusion
- Inspection de la couche application pour des protocoles de haut niveau
- Filtrage d’URL
- Contre les logiciels malveillants et antivirus au niveau du réseau
- Protection d’un robot
- Contrôle des accès applicatifs
- Protection supplémentaire DDoS (au-dessus de la protection fournie le tissu Azure lui-même DDoS)

Vous pouvez accéder à ces fonctionnalités de sécurité réseau améliorée en utilisant une solution partenaire Azure. Vous pouvez trouver des solutions de sécurité réseau le partenaire Azure plus récent en visitant l' [Azure Marketplace](https://azure.microsoft.com/marketplace/) et recherchez « sécurité » et « sécurité du réseau ».

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Accès à distance sécurisé et locaux croisée connectivité
Le programme d’installation, de configuration et de gestion de vos besoins de ressources Azure être effectuées à distance. En outre, vous souhaitez déployer des solutions [informatiques hybrides](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) ayant des composants locaux et dans le nuage public Azure. Ces scénarios requièrent un accès à distance sécurisé.

Mise en réseau Azure prend en charge les scénarios d’accès à distance sécurisé suivants :

- Stations de travail individuelles de se connecter à un réseau virtuel Azure
- Connecter votre réseau local à un réseau virtuel d’Azure avec un réseau privé virtuel
- Connecter votre réseau local à un réseau virtuel d’Azure avec une liaison WAN dédiée
- Connecter les réseaux virtuels Azure entre eux

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Stations de travail individuelles de se connecter à un réseau virtuel Azure
Il peut arriver lorsque vous souhaitez activer le personnel les développeurs ou les opérations individuel gérer les machines virtuelles et services dans Azure. Par exemple, vous devez accéder à un ordinateur virtuel sur un réseau virtuel Azure et votre stratégie de sécurité n’autorise pas un accès distant RDP ou SSH vers des machines virtuelles individuelles. Dans ce cas, vous pouvez utiliser une connexion VPN site-à-point.

La connexion VPN point-à-site utilise le protocole [VPN SSTP](https://technet.microsoft.com/library/cc731352.aspx) pour pouvoir configurer une connexion privée et sécurisée entre l’utilisateur et le réseau virtuel Azure. Une fois la connexion VPN est établie, l’utilisateur pourra à RDP ou SSH sur la liaison VPN dans n’importe quelle machine virtuelle sur le réseau virtuel Azure (en supposant que l’utilisateur peut s’authentifier et est autorisé).

Pour en savoir plus :

- [Configurer une connexion Point-à-Site, à un réseau virtuel à l’aide de PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Connecter votre réseau local à un réseau virtuel Azure avec un réseau privé virtuel
Vous souhaiterez peut-être se connecter votre réseau d’entreprise, ou certaines parties de celui-ci, à un réseau virtuel d’Azure. Cela est courant dans informatiques hybrides des scénarios où les entreprises [étendent leur centre de données local dans Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Dans de nombreux cas sociétés hébergera des parties d’un service dans Azure et pièces sur site, par exemple lorsqu’une solution contient des serveurs web frontaux dans Azure et les bases de données back-end sur site. Ces types de connexions de « coexistence » facilitent également la gestion des Azure situé ressources est plus sécurisé et activer des scénarios allant de l’extension des contrôleurs de domaine Active Directory dans Azure.

Une façon d’y parvenir est d’utiliser un [VPN de site à site](https://www.techopedia.com/definition/30747/site-to-site-vpn). La différence entre un réseau privé virtuel de site à site et un réseau privé virtuel site-à-point est qu’un réseau privé virtuel site-à-point connecte un seul périphérique à un réseau virtuel d’Azure, lors d’une connexion VPN de site à site connecte un réseau entier (par exemple, votre réseau local) à un réseau virtuel Azure. Connexions VPN de site à site à un réseau virtuel Azure utilisent le mode de tunnel IPsec protocole VPN hautement sécurisé.

Pour en savoir plus :

- [Créer un gestionnaire de ressources de VNet avec une connexion VPN de site à l’aide du portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Planification et conception de passerelle VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Connecter votre réseau local à un réseau virtuel Azure avec une liaison WAN dédiée
Connexions point-à-site et site à site VPN sont efficaces pour l’activation de la connectivité de coexistence. Toutefois, certaines organisations estiment qu’elles présentent les inconvénients suivants :

- Connexions VPN déplacement des données sur Internet, cela expose ces connexions aux éventuels problèmes de sécurité liés aux déplacements de données sur un réseau public. En outre, la fiabilité et disponibilité pour les connexions Internet ne peut pas être garanties.
- Connexions VPN aux réseaux virtuels d’Azure peuvent être considérées comme limitées par la bande passante pour certaines applications et certains besoins, comme ils max out à autour de 200Mbps.

Organisations nécessitant le niveau le plus élevé de sécurité et de disponibilité pour les connexions de coexistence en général utilisent des liaisons WAN dédiées pour vous connecter à des sites distants. Azure vous offre la possibilité d’utiliser une liaison WAN dédiée que vous pouvez utiliser pour connecter votre réseau local à un réseau virtuel d’Azure. Cette option est activée par le biais de ExpressRoute d’Azure.

Pour en savoir plus :

- [Vue d’ensemble technique de ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Connecter les réseaux virtuels Azure entre eux
Il est possible d’utiliser de nombreux réseaux virtuels Azure pour vos déploiements. Il existe plusieurs raisons peuvent expliquer pourquoi vous pouvez le faire. Une des raisons peut être pour simplifier la gestion ; un autre peut l’être pour des raisons de sécurité. Quelle que soit la motivation ou de justification pour utiliser des ressources sur différents réseaux virtuels Azure, il peut arriver lorsque vous souhaitez que les ressources sur chacun des réseaux pour vous connecter à un autre.

Une option serait de services sur un réseau virtuel Azure pour se connecter à des services sur un autre réseau virtuel Azure par « boucle arrière » via Internet. La connexion serait démarrer sur un réseau virtuel d’Azure, passer par Internet et puis revenir à la destination réseau virtuel d’Azure. Cette option expose la connexion aux problèmes de sécurité inhérents à toutes les communications basées sur Internet.

Est peut-être une meilleure option pour créer un Virtual Azure Azure-de-réseau virtuel réseau VPN de site à site. Ce virtuel Azure Azure-de-réseau virtuel réseau site à site VPN utilise le même protocole de [mode de tunnel IPsec](https://technet.microsoft.com/library/cc786385.aspx) comme la connexion VPN de site à site entre sites mentionnée ci-dessus.

L’avantage d’utiliser un Virtual Azure Azure-de-réseau virtuel réseau VPN de site à site est que la connexion VPN est établie sur le tissu Azure réseau ; Il ne se connecte pas via Internet. Cela fournit une couche supplémentaire de sécurité par rapport aux connexions VPN de site à site qui se connectent via Internet.

Pour en savoir plus :

- [Configurer une connexion VNet à VNet en utilisant le Gestionnaire de ressources Azure et PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Disponibilité
La disponibilité est un composant clé de tout programme de sécurité. Si vos utilisateurs et vos systèmes ne peuvent pas accéder à ce qu’ils doivent accéder sur le réseau, le service peut être considéré comme compromis. Azure a des technologies réseau qui prennent en charge des haute disponibilité mécanismes suivants :

- L’équilibrage de charge basé sur HTTP
- L’équilibrage de la charge au niveau du réseau
- L’équilibrage de la charge globale

L’équilibrage de charge est un mécanisme destiné à répartir équitablement les connexions entre plusieurs dispositifs. Les objectifs de l’équilibrage de charge sont les suivants :

- Augmenter la disponibilité – lorsque vous chargez des connexions de solde sur plusieurs périphériques, un ou plusieurs des périphériques peuvent devenir indisponible et les services en cours d’exécution sur les autres périphériques en ligne peuvent continuer de servir du contenu à partir du service
- Améliorer les performances : lorsque vous chargez des connexions de solde sur plusieurs périphériques, un seul périphérique ne doit pas prendre le positionnement du processeur. Au lieu de cela, les demandes de traitement et de mémoire pour traiter le contenu sont réparties sur plusieurs périphériques.

### <a name="http-based-load-balancing"></a>L’équilibrage de charge basé sur HTTP
Organisations qui exécutent les services basés sur le web souvent souhaitent avoir un équilibreur de charge basé sur HTTP devant ces services web à garantir un niveau adéquat de haute disponibilité et performances. Contrairement aux équilibreurs de charge de réseau traditionnel, l’équilibrage des décisions prises par basé sur HTTP équilibreurs de charge sont basés sur les caractéristiques du protocole HTTP, pas sur les protocoles de couche réseau et transport.

Pour vous fournir basé sur HTTP équilibrage de charge pour vos services basés sur le web, Azure fournit la passerelle d’Application Azure. La passerelle d’Application Azure prend en charge :

- Basé sur HTTP équilibrage de charge – décisions d’équilibrage de la charge sont effectuées sur base caractéristique spéciales du protocole HTTP
- Affinité de cookie de session – cette fonction permet de s’assure que les connexions établies à l’un des serveurs derrière ce équilibreur de charge reste intact entre le client et le serveur. Cela permet de garantir la stabilité des transactions.
- Déchargement SSL – lorsque le client est d’établir une connexion avec l’équilibrage de la charge, que la session entre le client et l’équilibreur de charge est cryptée en utilisant le protocole HTTPS (SSL /) protocole. Cependant, pour augmenter les performances, vous avez l’option pour que la connexion entre l’équilibreur de charge et le serveur web derrière l’utilisation d’équilibrage de charge du protocole HTTP (non crypté). Cela est appelé « Décharger SSL » dans la mesure où les serveurs web derrière l’équilibreur de charge ne rencontrez la surcharge du processeur impliquée avec le cryptage et doivent donc être en mesure de traiter les demandes plus rapidement.
- URL de routage basé sur contenu – cette fonction permet à l’équilibreur de charge prendre des décisions sur où transférer les connexions basées sur l’URL cible. Cela fournit une plus grande flexibilité que les solutions qui chargent l’équilibrage des décisions en fonction des adresses IP.

Pour en savoir plus :

- [Vue d’ensemble de la passerelle application](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>L’équilibrage de la charge au niveau du réseau
Contrairement à l’équilibrage de charge basé sur HTTP, équilibrage de charge au niveau prend des décisions d’équilibrage de charge basées sur l’adresse et le port (TCP ou UDP) numéros IP.
Vous pouvez bénéficier des avantages du niveau équilibrage de la charge dans Azure à l’aide de l’équilibreur de charge Azure. Quelques caractéristiques clés de l’équilibreur de charge Azure sont les suivantes :

- L’équilibrage de la charge au niveau réseau basé sur les numéros de port et adresse IP
- Prise en charge de tout protocole de la couche application
- Répartit la charge de Azure des machines virtuelles et d’instances de rôle des services de cloud
- Peut être utilisé pour (l’équilibrage de charge externe) faisant face à Internet et non Internet (équilibrage de la charge interne) les applications et les machines virtuelles
- Point de terminaison de contrôle, qui est utilisée pour déterminer si un service situé derrière l’équilibreur de charge soient plus disponible

Pour en savoir plus :

- [Équilibreur de charge vis-à-vis d’Internet entre plusieurs Machines virtuelles ou des services](../load-balancer/load-balancer-internet-overview.md)
- [Vue d’ensemble des équilibreur de charge interne](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>L’équilibrage de la charge globale
Certaines organisations souhaiteront le niveau de disponibilité le plus élevé possible. Une pour atteindre cet objectif consiste à héberger des applications dans des centres de données réparti à échelle mondiale. Lorsqu’une application est hébergée dans des centres de données situés dans le monde entier, il est possible qu’une région entière géopolitique deviennent indisponibles et dispose toujours de l’application et en cours d’exécution.

En plus des avantages de disponibilité que vous obtenez en hébergeant des applications dans des centres de données distribués globalement, vous pouvez également obtenir des avantages de performance. Ces avantages de performances peuvent être obtenus à l’aide d’un mécanisme qui dirige les requêtes pour le service du centre de données qui est plus proche de l’appareil qui effectue la demande.

Équilibrage de la charge globale peut vous fournir de ces avantages. Dans Azure, vous pouvez bénéficier des avantages global d’équilibrage de charge à l’aide du Gestionnaire de trafic Azure.

Pour en savoir plus :

- [Quel est le Traffic Manager ?](../traffic-manager/traffic-manager-overview.md)

## <a name="logging"></a>Enregistrement dans le journal
Journalisation au niveau d’un réseau est une fonction clée pour les scénarios de sécurité réseau. Dans Azure, vous pouvez enregistrer les informations obtenues pour les groupes de sécurité de réseau obtenir l’enregistrement des informations de niveau de réseau. Avec l’enregistrement du NSG, vous obtenez des informations à partir de :

- Audit des journaux sont utilisés pour afficher toutes les opérations soumises à vos abonnements Azure. Ces journaux est activés par défaut et peut être utilisé au sein du portail Azure.
- Journaux des événements – ces journaux fournit des informations sur les règles NSG ont été appliquées.
- Les journaux de compteur, ces journaux vous permettre de savoir combien de fois chaque règle NSG a été appliqué pour refuser ou autoriser le trafic.

Vous pouvez également utiliser la [Puissance de Microsoft BI](https://powerbi.microsoft.com/what-is-power-bi/), un outil de visualisation de données puissantes, pour afficher et analyser ces fichiers journaux.

Pour en savoir plus :

- [Analytique de journal pour les groupes de sécurité réseau (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)

## <a name="name-resolution"></a>Résolution de noms
Résolution de noms est une fonction essentielle pour tous les services que vous hébergez dans Azure. Du point de vue de la sécurité, les compromis de la fonction de résolution de nom peut permettre à un attaquant de redirection des demandes à partir de vos sites à un site. Résolution de noms sécurisé est une exigence pour tous vos services de nuage hébergé.

Il existe deux types de résolution de noms que vous devez :

- Résolution de noms interne – interne est utilisée par les services de vos réseaux virtuels Azure, vos réseaux locaux ou les deux. Les noms utilisés pour la résolution de nom interne ne sont pas accessibles sur Internet. Pour une sécurité optimale, il est important que votre schéma de résolution de noms interne n’est pas accessible aux utilisateurs externes.
- Résolution de noms externes – externe est utilisée par les personnes et les périphériques en dehors de vos locaux et les réseaux virtuels d’Azure. Ce sont les noms visibles sur Internet et sont utilisés pour la connexion directe à vos services en nuage.

Pour la résolution de noms internes, vous avez deux options :

- Un serveur DNS de réseau virtuel Azure – lorsque vous créez un nouveau réseau virtuel Azure, un serveur DNS est créé pour vous. Ce serveur DNS peut résoudre les noms des ordinateurs situés sur ce réseau virtuel Azure. Ce serveur DNS n’est pas configurable et est géré par le Gestionnaire d’Azure fabric, ce qui rend une solution de résolution de nom sécurisé.
- Réduisez votre propre serveur DNS, vous avez la possibilité de placer un serveur DNS de votre choix sur votre réseau virtuel Azure. Ce serveur DNS peut être intégré à un Active Directory serveur DNS ou une solution de serveur DNS dédiée fourni par le partenaire d’Azure, ce que vous pouvez obtenir à partir du marché Azure.

Pour en savoir plus :

- [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)
- [Gérer les serveurs DNS utilisés par un réseau virtuel (VNet)](../virtual-network/virtual-networks-manage-dns-in-vnet.md)

Pour la résolution DNS externe, vous avez deux options :

- Héberger votre propre externes DNS server sur site
- Héberger votre propre serveur DNS externe auprès d’un fournisseur de service

De nombreuses grandes organisations hébergent leurs propres DNS serveurs locaux. Pour cela, ils peuvent parce qu’ils ont l’expertise de mise en réseau et de la présence mondiale à le faire.

Dans la plupart des cas, il est préférable de faire héberger vos services de résolution de nom DNS auprès d’un fournisseur de service. Ces fournisseurs de services ont la connaissance des réseaux et présence mondiale afin de garantir une disponibilité très élevée de vos services de résolution de nom. Disponibilité est essentielle pour les services DNS, car si vos services de résolution de noms échouent, ne sera en mesure d’atteindre votre Internet vis-à-vis des services.

Azure fournit une haute disponibilité et performante une solution DNS externe sous la forme d’Azure DNS. Cette solution de résolution de nom externe tire parti de l’infrastructure DNS d’Azure dans le monde entier. Il vous permet d’héberger votre domaine en utilisant les mêmes outils d’informations d’identification, API, Azure et de facturation en tant que vos autres services Azure. Dans le cadre d’Azure, il hérite également des contrôles de sûreté fort intégrées à la plate-forme.

Pour en savoir plus :

- [Vue d’ensemble DNS Azure](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>Architecture de la zone DMZ
De nombreuses organisations d’entreprise permet de leurs réseaux pour créer une zone de mémoire tampon entre Internet et les services de segment DMZ. La partie de la DMZ du réseau est considérée comme une zone de sécurité minimale et sans ressources de valeur élevée sont placés dans ce segment du réseau. Vous verrez généralement des périphériques de sécurité réseau qui ont une interface réseau sur le segment DMZ et une autre interface réseau connectée à un réseau qui possède des machines virtuelles et services qui acceptent les connexions entrantes à partir d’Internet.

Il existe un certain nombre de variantes de conception de la DMZ et la décision de déployer un réseau de périmètre, puis quel type de zone DMZ à utiliser si vous décidez d’en utiliser un, est basée sur les besoins de sécurité de votre réseau.

Pour en savoir plus :

- [Les Services en nuage de Microsoft et la sécurité du réseau](../best-practices-network-security.md)

## <a name="azure-security-center"></a>Le centre de sécurité Azure
Centre de sécurité vous aide à empêcher, détecter et répondre aux menaces et fournit le qu'augmentation de visibilité et de contrôler, de la sécurité de vos ressources d’Azure. Il fournit la gestion de stratégie et de surveillance de la sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec un vaste écosystème de solutions de sécurité.

Le centre de sécurité Azure vous aide à optimiser et analyser la sécurité du réseau :

- Fournit des recommandations de sécurité réseau
- Surveillance de l’état de votre configuration de la sécurité réseau
- Vous alerte à réseau en fonction des menaces à la fois au niveau du point de terminaison et de réseau

Pour en savoir plus :

- [Introduction au centre de sécurité Azure](../security-center/security-center-intro.md)
