<properties
   pageTitle="Sécurité de réseau et de services de cloud Microsoft | Microsoft Azure"
   description="Apprenez à utiliser certaines des fonctionnalités principales disponibles dans Azure pour aider à créer des environnements réseau sécurisés"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jonor;sivae"/>

# <a name="microsoft-cloud-services-and-network-security"></a>Sécurité de réseau et de services de cloud de Microsoft

Services de cloud de Microsoft offrent hyperscale services et infrastructure, capacités de niveau entreprise et plusieurs choix pour la connectivité des hybrides. Clients la possibilité d’accéder à ces services via Internet ou avec ExpressRoute d’Azure, qui fournit une connectivité de réseau privé. La plate-forme Microsoft Azure permet aux clients d’étendre leur infrastructure dans le nuage en toute transparence et de concevoir des architectures à plusieurs couches. En outre, tiers peuvent activer des fonctionnalités améliorées en proposant des services de sécurité et des appliances virtuelles. Ce livre blanc fournit une vue d’ensemble de la sécurité et les problèmes d’architecture clients doivent prendre en compte lors de l’utilisation de services de cloud de Microsoft accédés via ExpressRoute. Elle couvre également la création de services de sécurité dans des réseaux virtuels Azure.

## <a name="fast-start"></a>Démarrage rapide
Le graphique logique suivant vous oriente vers un exemple spécifique de nombreuses techniques de sécurité disponibles avec la plateforme Azure. Pour référence rapide, recherchez l’exemple parfaitement adaptée à votre cas. Pour obtenir des explications plus complètes, poursuivez la lecture sur le papier.
![Organigramme des options de sécurité][0]

[Exemple 1 : Créer un réseau de périmètre (également appelé DMZ, zone démilitarisée ou DMZ et sous-réseau filtré) pour protéger les applications avec des groupes de sécurité réseau (NSGs).](#example-1-build-a-simple-dmz-with-nsgs)</br>
[Exemple 2 : Créer un réseau de périmètre pour protéger les applications avec un pare-feu et NSGs.](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br>
[Exemple 3 : Créer un réseau de périmètre pour protéger les réseaux avec un pare-feu, gamme de défini par l’utilisateur (UDR) et NSG.](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br>
[Exemple 4 : Ajouter une connexion hybride avec un réseau privé virtuel de l’appliance virtuel de site à site (VPN).](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Exemple 5 : Ajouter une connexion hybride avec la passerelle VPN site à site, Azure.](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br>
[Exemple 6 : Ajouter une connexion hybride avec ExpressRoute.](#example-6-adding-a-hybrid-connection-with-expressroute)</br>
Exemples pour l’ajout de connexions entre réseaux virtuels, haute disponibilité et le chaînage de service seront ajoutés à ce document dans les prochains mois.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Protection de la conformité et de l’infrastructure de Microsoft
Microsoft a pris une position de leader prenant en charge les initiatives de conformité requis par les clients de l’entreprise. Voici quelques-unes des certifications de la conformité pour Azure : ![badges de conformité Azure][1]

Pour plus d’informations, consultez les informations de mise en conformité sur le [Centre de confidentialité de Microsoft](https://azure.microsoft.com/support/trust-center/compliance/).

Microsoft a une approche complète pour protéger l’infrastructure cloud nécessaire à l’exécution de hyperscale globales services. Infrastructure de nuage Microsoft inclut le matériel, les logiciels, les réseaux et d’administration et le personnel chargé des opérations, en plus de centres de données physiques.

![Fonctionnalités de sécurité Azure][2]

Cette approche fournit une base plus sûre pour les clients à déployer leurs services dans le nuage de Microsoft. L’étape suivante consiste pour les clients à concevoir et créer une architecture de sécurité pour protéger ces services.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Architectures de sécurité traditionnelles et les réseaux de périmètre
Bien que Microsoft investit lourdement dans la protection de l’infrastructure de nuage, les clients doivent également protéger leurs services en nuage et les groupes de ressources. Une approche multicouche de la sécurité fournit la meilleure défense. Une zone de sécurité de réseau de périmètre protège les ressources de réseau interne à partir d’un réseau non approuvé. Un réseau de périmètre désigne les arêtes ou les parties du réseau qui se trouvent entre Internet et l’infrastructure informatique d’entreprise protégé.

Dans les réseaux d’entreprise typique, l’infrastructure de base est fortement enrichi dans le périmètre, avec plusieurs couches de dispositifs de sécurité. La limite de chaque couche se compose de points d’application de stratégie et de périphériques. Les périphériques peuvent inclure les éléments suivants : pare-feu, prévention du déni de Service distribué (DDoS), détection des intrusions ou des systèmes de Protection (IDS/IPS) et les périphériques VPN. Application de la stratégie peut prendre la forme de stratégies de pare-feu, de listes de contrôle d’accès (ACL) ou de routage spécifique. La première ligne de défense du réseau, directement accepter le trafic entrant à partir d’Internet, est une combinaison de ces mécanismes pour bloquer les attaques et du trafic dangereux tout en autorisant davantage les demandes légitimes dans le réseau. Ce trafic s’achemine directement aux ressources du réseau de périmètre. Cette ressource peut ensuite « parler » entre ressources plus profonds dans le réseau de transit à travers la limite suivante de validation premier. La couche la plus éloignée est appelée le réseau de périmètre, car cette partie du réseau est exposée à Internet, généralement avec une certaine forme de protection des deux côtés. La figure suivante montre un exemple d’un réseau de périmètre de sous-réseau unique dans un réseau d’entreprise, avec deux limites de sécurité.

![Un réseau de périmètre dans un réseau d’entreprise][3]

Il existe de nombreuses architectures utilisées pour implémenter un réseau de périmètre, un équilibreur de charge simple en face d’une batterie de serveurs web, pour un réseau de périmètre de plusieurs sous-réseau avec des mécanismes différents à chaque frontière de bloquer le trafic et de protéger les couches les plus profondes du réseau d’entreprise. Comment le réseau de périmètre est créé dépend de besoins spécifiques de l’entreprise et de sa tolérance de risque global.

Clients déplacer leurs charges de travail vers les clouds publics, il est essentiel de prendre en charge des fonctionnalités similaires pour l’architecture de réseau de périmètre dans Azure pour répondre aux exigences de sécurité et de conformité. Ce document fournit des instructions sur la façon dont les utilisateurs peuvent générer un environnement de réseau sécurisé dans Azure. Il se concentre sur le réseau de périmètre, mais inclut également des informations complètes sur de nombreux aspects de la sécurité du réseau. Les questions suivantes informent cette discussion :

- Comment un réseau de périmètre dans Azure peut être développée ?
- Quelles sont certaines des fonctionnalités disponibles pour construire le réseau de périmètre Azure ?
- Comment les charges de travail principal peuvent être protégés ?
- Comment les communications Internet sont contrôlées pour les charges de travail dans Azure ?
- Comment les réseaux locaux peuvent être protégés à partir des déploiements dans Azure ?
- Lorsque les fonctionnalités de sécurité Azure native doivent être utilisées par rapport à des dispositifs tiers ou des services ?

Le diagramme suivant montre les différentes couches de sécurité Qu'azure fournit aux clients. Ces couches sont natif sur la plateforme Azure lui-même et fonctions définies par le client :

![Architecture de la sécurité Azure][4]

Trafic entrant à partir d’Internet, DDoS Azure protège votre PC contre les attaques à grande échelle par rapport à Azure. La couche suivante est définie par le client publics points de terminaison, qui sont utilisés pour déterminer le trafic peut traverser le service en nuage au réseau virtuel. Azure natif virtual isolation du réseau garantit une isolation complète de tous les autres réseaux, et que le trafic circule uniquement par le biais de méthodes et de chemins d’accès de l’utilisateur configuré. Ces chemins d’accès et les méthodes sont la couche suivante, où NSGs UDR et équipements de réseau virtuel peuvent servir à créer des limites de sécurité pour protéger les déploiements d’application dans le réseau protégé.

La section suivante fournit une vue d’ensemble des Azure réseaux virtuels. Ces réseaux virtuels est créés par les clients et est leurs charges de travail déployés qui sont connectés à. Les réseaux virtuels sont la base de toutes les fonctionnalités de sécurité réseau requis pour établir un réseau de périmètre pour protéger les déploiements clients dans Azure.

## <a name="overview-of-azure-virtual-networks"></a>Vue d’ensemble des Azure réseaux virtuels
Avant d’obtenir le trafic Internet vers les réseaux virtuels Azure, il existe deux niveaux de sécurité inhérente à la plateforme Azure :

1.  **DDoS protection**: protection de DDoS est une couche réseau physique Azure qui protège la plateforme Azure lui-même contre les attaques Internet à grande échelle. Ces attaques permet plusieurs nœuds de « robots » dans une tentative de submerger un service Internet. Azure a une protection DDoS robuste de maillage sur l’ensemble de la connectivité Internet entrant. Cette couche de protection DDoS a pas utilisateur d’attributs configurables et n’est pas accessible au client. Cela protège Azure en tant que plate-forme d’attaques à grande échelle, mais ne protège pas directement d’application de chaque client. Niveaux de résilience supplémentaires peut être configurés par le client contre une attaque localisée. Par exemple, si le client A a été attaqué par une attaque DDoS à grande échelle sur un point de terminaison public, Azure bloque les connexions à ce service. Client A pu basculer sur un autre réseau virtuel ou service de point de terminaison non impliqué dans l’attaque pour rétablir le service. Il convient de noter que bien que le client A peut être affectée sur ce point de terminaison, aucun autre service en dehors de ce point de terminaison ne serait affectés. En outre, d’autres clients et les services ne verraient aucun impact de cette attaque.
2.  **Points de terminaison de service**: points de terminaison autorisent cloud services ou ressources groupes ont des adresses IP Internet public et ports exposés. Le point de terminaison utilise la traduction d’adresses réseau (NAT) pour acheminer le trafic vers l’adresse interne et le port sur le réseau virtuel Azure. C’est le chemin d’accès principal pour le trafic externe à passer dans le réseau virtuel. Les points de terminaison de service sont configurable par l’utilisateur pour déterminer quel trafic est passé et de comment et où elle est convertie en du réseau virtuel.

Une fois que le trafic atteigne le réseau virtuel, il existe de nombreuses fonctionnalités qui entrent en jeu. Les réseaux virtuels Azure constituent la base pour les clients de joindre leurs charges de travail et où s’applique la sécurité de base au niveau du réseau. Il est un réseau privé (une superposition de réseau virtuel) dans Azure pour les clients possédant les caractéristiques et fonctionnalités suivantes :
 
- **Isolation du trafic**: un réseau virtuel est la limite d’isolation du trafic sur la plateforme Azure. Machines virtuelles (VM) dans un réseau virtuel ne peut pas communiquer directement aux ordinateurs virtuels un réseau virtuel différent, même si les deux réseaux virtuels est créés par le même client. Il s’agit d’une propriété critique qui garantit que les ordinateurs virtuels du client et communication reste privée au sein d’un réseau virtuel.
- **Topologie de multiniveau**: les réseaux virtuels permettent aux clients de définir la topologie de multicouche en allouant des sous-réseaux et de désigner les espaces d’adressage distincts pour les différents éléments ou « niveaux » de leurs charges de travail. Ces regroupements logiques et les topologies aider les clients à définir une stratégie d’accès différents selon les types de charge de travail et également de contrôler les flux de trafic entre les différents niveaux.
- **Connectivité de coexistence**: les clients peuvent établir une connectivité de coexistence entre un réseau virtuel et de plusieurs sites sur site ou d’autres réseaux virtuels dans Azure. Pour ce faire, les clients peuvent utiliser les passerelles VPN Azure ou appliances virtuelles de réseau tiers. Azure prend en charge les réseaux privés virtuels (S2S) de site à site à l’aide des protocoles IPsec/IKE standard et connectivité privée de ExpressRoute.
- **NSG** permet aux clients de créer des règles (ACL) au niveau de granularité souhaité : réseau des interfaces, des ordinateurs virtuels individuels ou sous-réseaux virtuels. Les clients peuvent contrôler l’accès en autorisant ou en refusant des communications entre les charges de travail au sein d’un réseau virtuel, à partir des systèmes sur les réseaux du client via la connectivité entre sites, ou diriger la communication Internet.
- **UDR** et le **Routage IP** permettent aux clients de définir les chemins d’accès de communication entre les différents niveaux au sein d’un réseau virtuel. Les clients peuvent déployer un pare-feu, IDS/IPS et autres appliances virtuelles et router le trafic réseau par le biais de ces équipements de sécurité pour l’application de stratégies de limite de sécurité, d’audit et inspection.
- **Équipements de réseau virtuels** sur le marché d’Azure : appliances de sécurité tels que les pare-feu, IDS/IPS et les équilibreurs de charge sont disponibles sur le marché de Azure et de la galerie d’Image de machine virtuelle. Pour terminer un environnement de réseau sécurisé de plusieurs niveaux, les clients peuvent déployer ces appareils dans leurs réseaux virtuels et en particulier, à leurs limites de sécurité (y compris les sous-réseaux du réseau de périmètre).

Avec ces caractéristiques et de fonctionnalités, un exemple de comment une architecture de réseau de périmètre peut être construite dans Azure est le suivant :

![Un réseau de périmètre d’un réseau virtuel Azure][5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Configuration requise et les caractéristiques de réseau de périmètre
Le réseau de périmètre est conçu pour être la partie frontale du réseau, interface directement les communications à partir d’Internet. Les paquets entrants doivent circuler via les appliances de sécurité, tels que les pare-feu, IDS et IPS, avant d’atteindre les serveurs principaux. Internet lié aux paquets de charges de travail peuvent également circulent à travers les appliances de sécurité dans le réseau de périmètre pour l’application des stratégies, d’inspection et à des fins d’audit, avant de quitter le réseau. En outre, le réseau de périmètre peut héberger des passerelles VPN de coexistence entre les réseaux des clients virtuels et les réseaux locaux.

### <a name="perimeter-network-characteristics"></a>Caractéristiques de réseau de périmètre
Référencement de la figure précédente, certaines des caractéristiques d’un réseau de périmètre bonne sont les suivantes :

- Avec accès via Internet :
    - Le sous-réseau du réseau de périmètre lui-même est orienté Internet, communiquer directement avec Internet.
    - IPs publics, VIP et/ou de points de terminaison de service passent le trafic Internet vers le réseau frontal et les périphériques.
    - Le trafic entrant provenant d’Internet passe par le biais de périphériques de sécurité avant d’autres ressources sur le réseau frontal.
    - Si la sécurité sortante est activée, le trafic passe par l’intermédiaire de dispositifs de sécurité, comme l’étape finale, avant de passer à l’Internet.
- Réseau protégé :
    - Il n’y a aucun chemin d’accès direct à partir d’Internet à l’infrastructure de base.
    - Canaux de l’infrastructure principale doivent traverser des périphériques de sécurité tels que les NSGs, les pare-feu ou périphériques VPN.
    - Autres périphériques ne doivent pas pont Internet et l’infrastructure principale.
    - Dispositifs de sécurité sur la côté Internet et sur le réseau protégé face à des limites du réseau de périmètre (par exemple, les deux pare-feu icônes illustrés dans la figure précédente) peuvent être une seule appliance virtuelle avec différenciée des règles ou des interfaces pour chaque frontière. (Autrement dit, un seul périphérique, séparé de façon logique, qui gère la charge pour les deux frontières du réseau de périmètre.)
- Autres pratiques et contraintes courantes :
    - Charges de travail ne doivent pas stocker des informations stratégiques.
    - Accès et mises à jour des configurations de réseau de périmètre et les déploiements sont limitées aux seuls les administrateurs autorisés.

### <a name="perimeter-network-requirements"></a>Exigences de réseau de périmètre
Pour activer ces caractéristiques, suivez les instructions sur les exigences en matière de réseau virtuel pour implémenter un réseau de périmètre réussie :

- **Architecture de sous-réseau :** Spécifiez le réseau virtuel, telle qu’un sous-réseau complet dédié comme le réseau de périmètre, séparé des autres sous-réseaux dans le même réseau virtuel. Ainsi, le trafic entre le réseau de périmètre et les autres flux de niveaux de sous-réseau interne ou privé via un pare-feu ou une solution IDS/IPS d’appliance virtuelle sur les limites de sous-réseau avec des itinéraires définis par l’utilisateur.
- **NSG :** Le sous-réseau du réseau de périmètre proprement dit doit être ouvert pour permettre la communication avec Internet, mais qui ne signifie pas que les clients doivent être en contournant NSGs. Suivez les pratiques de sécurité classiques pour réduire les surfaces réseau exposées à Internet. Verrouiller les plages d’adresse distante peuvent accéder les déploiements ou les protocoles d’application spécifique et les ports qui sont ouverts. Il peut exister des circonstances dans lesquelles il n’est pas toujours possible. Par exemple, si les clients ont un site Web externe dans Azure, le réseau de périmètre doit autoriser les demandes web entrantes à partir des adresses IP publiques, mais doit s’ouvrir uniquement les ports d’application web : TCP : 80 et TCP:443.
- **Table de routage :** Le sous-réseau du réseau de périmètre lui-même doit être en mesure de communiquer directement à Internet, mais ne doit pas permettre une communication directe vers et depuis les réseaux de fin ou sur site arrière sans passer par une solution de pare-feu ou de sécurité.
- **Configuration de la solution matérielle-logicielle sécurité :** Pour router et inspecter les paquets entre le réseau de périmètre et les réseaux protégés, les appliances de sécurité tels que les pare-feu, IDS et IPS les autres périphériques peuvent être à hébergement multiple. Ils peuvent avoir séparé des cartes réseau pour le réseau de périmètre et les sous-réseaux back-end. Les cartes réseau dans le réseau de périmètre communiquent directement vers et depuis Internet, avec les NSGs correspondantes et la table de routage de réseau de périmètre. Les cartes réseau se connectant aux principaux sous-réseaux ont plus restreint NSGs et des tables de routage des sous-réseaux back-end correspondants.
- **Fonctionnalités sécurité :** Les appliances de sécurité déployés dans le réseau de périmètre en général effectuent les fonctions suivantes :
    - Pare-feu : Application des règles de pare-feu ou de stratégies de contrôle d’accès pour les demandes entrantes.
    - Détection et prévention des menaces : détection et l’atténuation des attaques à partir d’Internet.
    - Audit et journalisation : mise à jour des journaux détaillés d’audit et d’analyse.
    - Proxy inverse : Rediriger les demandes entrantes vers les serveurs dorsaux correspondants. Cela implique le mappage et traduire les adresses de destination sur les périphériques frontaux, pare-feu en règle générale, pour les adresses de serveur principal.
    - Proxy direct : fourniture de NAT et de l’audit pour la communication établie à partir du réseau virtuel à Internet.
    - Routeur : Transfert entre sous-réseaux du trafic entrant et à l’intérieur du réseau virtuel.
    - Périphérique VPN : agissant en tant que les passerelles VPN de coexistence pour une connexion VPN de coexistence entre les réseaux locaux et des réseaux virtuels Azure.
    - Serveur VPN : accepter les clients VPN se connectant à des réseaux virtuels Azure.

>[AZURE.TIP] Séparer les deux groupes suivants : les individus autorisés d’accéder à l’engrenage de sécurité de réseau de périmètre et les personnes autorisées en tant qu’administrateurs opérations, de développement ou de déploiement d’application. Conserver ces groupes distincts permet une répartition des tâches et empêche une seule personne de contourner la sécurité des applications et contrôles de sécurité réseau.

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Questions pour qu’un message lors de la création des limites du réseau
Dans cette section, sauf s’il est spécifiquement mentionné, le terme « réseaux » fait référence à Azure des réseaux privés virtuels créés par un administrateur d’abonnement. Le terme ne fait pas référence à des réseaux physiques sous-jacents dans Azure.

En outre, les réseaux virtuels Azure sont souvent utilisés pour étendre les réseaux locaux traditionnels. Il est possible d’incorporer des ExpressRoute des solutions réseau hybride avec des architectures de réseau de périmètre ou site à site. Il s’agit d’un facteur important dans la création de limites de sécurité réseau.

Aux trois questions suivantes sont essentielles pour répondre lorsque vous générez un réseau avec un réseau de périmètre et plusieurs limites de sécurité.

#### <a name="1-how-many-boundaries-are-needed"></a>1) le nombre de limites sont nécessaires ?
Il est le premier point de décision pour déterminer le nombre de limites de sécurité sont nécessaires dans un scénario donné :

- Une seule frontière : un sur le réseau de périmètre frontal, entre le réseau virtuel et Internet.
- Deux frontières : une du côté Internet de réseau de périmètre et l’autre entre le sous-réseau de réseau de périmètre et les sous-réseaux back-end dans les réseaux virtuels Azure.
- Trois limites : une du côté Internet du réseau de périmètre entre le réseau de périmètre et les sous-réseaux de back-end et entre les sous-réseaux principaux et le réseau local.
- Limites de N : un nombre variable. Selon les exigences de sécurité, il est vraiment à n’importe quel nombre de limites de sécurité pouvant être appliqués dans un réseau donné.

Le nombre et le type de limites nécessaires varient en fonction de la tolérance au risque de l’entreprise et la mise en oeuvre de scénario spécifique. Il s’agit souvent d’une décision conjointe faite par plusieurs groupes au sein d’une organisation, souvent avec un risque et équipe de conformité, un réseau et équipe de la plate-forme et une équipe de développement d’application. Personnes ayant connaissance de la sécurité, les données impliquées et les technologies utilisées doivent posséder un dites dans la présente décision afin de garantir l’attitude de sécurité appropriés pour chaque implémentation.

>[AZURE.TIP] Utiliser le plus petit nombre de frontières qui satisfont aux exigences de sécurité dans une situation donnée. Avec les limites plus les plus difficiles les opérations et la résolution des problèmes peuvent être, ainsi que les frais de gestion liés à la gestion de plusieurs stratégies de limite dans le temps. Toutefois, des limites insuffisantes augmentent les risques. Il est essentiel de trouver le juste équilibre.

![Réseau hybride avec trois limites de sécurité][6]

La figure précédente montre une vue d’ensemble d’un réseau de frontière de trois sécurité. Les limites sont entre le réseau de périmètre et Internet, les Azure front-end et back-end sous-réseaux privés et le sous-réseau de back-end Azure et réseau local d’entreprise.

#### <a name="2-where-are-the-boundaries-located"></a>2) où se trouvent les limites ?
Une fois que le nombre de limites est décidé, où les implémenter constitue le prochain point de décision. Il existe généralement trois choix :
- À l’aide d’un service intermédiaire basé sur Internet (par exemple, un nuage pare-feu pour applications Web, qui n’est pas abordé dans ce document)
- À l’aide des fonctionnalités natives et/ou d’équipements de réseau virtuel dans Azure
- À l’aide de périphériques physiques sur le réseau local

Sur les réseaux purement Azure, les options sont des fonctionnalités natives de Azure (par exemple, les équilibreurs de charge Azure) ou de réseau virtuel appliances depuis la riche écosystème de partenaires d’Azure (par exemple, les pare-feu Check Point).

Si une limite est nécessaire entre Azure et un réseau local, les dispositifs de sécurité peuvent résider sur un côté de la connexion (ou les deux côtés). Ainsi, une décision doit être prise sur l’emplacement pour placer les équipements de sécurité.

Dans la figure précédente, le réseau de périmètre-à-Internet les limites avant-back-end sont entièrement contenus dans Azure et doivent avoir des fonctionnalités natives de Azure ou réseau appliances virtuelles. Dispositifs de sécurité sur la limite entre Azure (sous-réseau back-end) et le réseau d’entreprise peuvent être soit sur le côté Azure le côté local ou encore une combinaison de périphériques sur les deux côtés. Il peut y être importants avantages et des inconvénients soit l’option qui doit être sérieusement considéré.

Par exemple, à l’aide d’engins de sécurité physique existant sur le côté du réseau local possède l’avantage qu’aucun nouvel équipement n’est nécessaire. Il suffit de reconfiguration. L’inconvénient, cependant, est que tout le trafic doit revenir dans Azure au réseau local pour être vus par les équipements de sécurité. Par conséquent le trafic d’Azure-sur-Azure pourrait être d’une latence significative, et affectent les performances des applications et l’utilisateur expérience, si elle a été forcée sur le réseau local pour l’application des stratégies de sécurité.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) sont les limites de fonctionnement ?
Chaque frontière de sécurité aura probablement des exigences différentes (par exemple, les ID et les règles de pare-feu du côté Internet du réseau de périmètre, mais uniquement les ACL entre le réseau de périmètre et le sous-réseau de back-end). Déterminer les périphériques à utiliser dépend des exigences de scénario et de la sécurité. Dans la section suivante, les exemples 1, 2 et 3 traitent certaines options qui pourraient être utilisées. Examiner les fonctionnalités Azure réseau natif et les périphériques disponibles dans Azure à partir de l’écosystème de partenaires montre le large éventail d’options disponibles pour résoudre pratiquement tous les scénarios.

Un autre point de décision d’implémentation de clé consiste à se connecter au réseau local avec Azure. Utiliser la passerelle virtuelle Azure ou un matériel de réseau virtuel ? Ces options sont traitées en détail dans la section suivante (les exemples 4, 5 et 6).

En outre, le trafic entre des réseaux virtuels dans Azure peut-être être nécessaires. Ces scénarios seront ajoutées à une date ultérieure.

Une fois que vous connaissez les réponses aux questions précédentes, la section [Fast Start](#fast-start) peut aider à identifier les exemples les plus appropriés pour un scénario donné.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Exemples : Création de limites de sécurité des réseaux virtuels Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Exemple 1 : Créer un réseau de périmètre pour protéger les applications avec NSGs
[Au démarrage rapide](#fast-start) | [instructions pour que cet exemple de génération de détails][Example1]

![Réseau de périmètre entrant avec NSG][7]

#### <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il existe un abonnement qui contient les éléments suivants :

- Deux services en nuage : « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel, « CorpNetwork », avec deux sous-réseaux : « Frontal » et « Back-end »
- Un groupe de sécurité réseau qui est appliquée pour les deux sous-réseaux.
- Un serveur Windows qui représente un serveur web d’application (« IIS01 »)
- Deux serveurs Windows qui représentent l’application sur les serveurs principaux (« AppVM01 », « AppVM02 »)
- Un serveur Windows qui représente un serveur DNS (« DNS01 »)

Pour les scripts et un modèle de gestionnaire de ressources Azure, consultez les [instructions de génération détaillées][Example1].

#### <a name="nsg-description"></a>Description du NSG
Dans cet exemple, un groupe NSG est généré et ensuite chargé avec six règles.

>[AZURE.TIP] En règle générale, vous devez créer les règles « Autoriser » spécifiques en premier lieu, suivie par les règles de « Refuser » plus génériques. La priorité donnée détermine les règles sont évaluées en premier. Une fois que le trafic est trouvé à appliquer à une règle spécifique, aucuns plus de règles ne sont évaluées. Règles NSG s’appliquent dans la direction entrante ou sortante (du point de vue du sous-réseau).

De façon déclarative, les règles suivantes sont en cours de génération pour le trafic entrant :

1.  Trafic interne de DNS (port 53) est autorisé.
2.  Le trafic RDP (le port 3389) à partir d’Internet à n’importe quelle Machine virtuelle est autorisé.
3.  Le trafic HTTP (port 80) à partir d’Internet vers le serveur web (IIS01) est autorisé.
4.  Tout le trafic (tous les ports) à partir de IIS01 à AppVM1 est autorisé.
5.  Tout le trafic (tous les ports) à partir d’Internet à l’ensemble du réseau virtuel (les deux sous-réseaux) est refusé.
6.  Tout le trafic (tous les ports) à partir du sous-réseau frontal au sous-réseau back-end est refusé.

Avec ces règles lié à chaque sous-réseau, si une requête HTTP a été entrante provenant d’Internet vers le serveur web, les deux règles 3 (autoriser) et 5 (refuser) s’appliquent. Mais dans la mesure où les règles 3 a une priorité plus élevée, seulement il serait applicable et règle 5 ne serait pas entrent en jeu. Ainsi, la requête HTTP serait autorisée au serveur web. Si vous essayez le même trafic d’atteindre le serveur DNS01, la règle 5 (refuser) est le premier à appliquer, et le trafic ne serait pas autorisé à transmettre au serveur. Règle 6 (refuser) bloque le sous-réseau frontal de communiquer au sous-réseau back-end (à l’exception du trafic autorisé dans les règles 1 et 4). Ceci protège le réseau principal au cas où un intrus compromet l’application web sur le serveur frontal. L’attaquant aurait ont d’un accès limité au réseau principal « protected » (uniquement pour les ressources exposées sur le serveur de AppVM01).

Il existe une règle sortante par défaut qui autorise le trafic vers Internet. Pour cet exemple, nous allons autorisant le trafic sortant et ne modifie ne pas les règles de trafic sortant. Pour verrouiller le trafic dans les deux directions, défini par l’utilisateur un routage est requis (voir exemple 3).

#### <a name="conclusion"></a>Conclusion
Il s’agit d’un moyen relativement simple et rapide d’isoler le sous-réseau back-end du trafic entrant. Pour plus d’informations, consultez les [instructions de génération détaillées][Example1]. Ces instructions sont les suivantes :

- La création de ce réseau de périmètre avec scripts PowerShell.
- La création de ce réseau de périmètre avec un modèle de gestionnaire de ressources Azure.
- Descriptions détaillées de chaque commande NSG.
- Scénarios de trafic détaillées, indiquant comment le trafic est autorisé ou refusé dans chaque couche.


 ### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Exemple 2 : Créer un réseau de périmètre pour protéger les applications avec un pare-feu et NSGs
[Au démarrage rapide](#fast-start) | [instructions pour que cet exemple de génération de détails][Example2]

![Réseau de périmètre entrant à couloirs et NSG][8]

#### <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il existe un abonnement qui contient les éléments suivants :

- Deux services en nuage : « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel, « CorpNetwork », avec deux sous-réseaux : « Frontal » et « Back-end »
- Un groupe de sécurité réseau qui est appliquée pour les deux sous-réseaux.
- Un matériel de réseau virtuel, un pare-feu, dans ce cas est connecté au sous-réseau frontal
- Un serveur Windows qui représente un serveur web d’application (« IIS01 »)
- Deux serveurs Windows qui représentent l’application sur les serveurs principaux (« AppVM01 », « AppVM02 »)
- Un serveur Windows qui représente un serveur DNS (« DNS01 »)

Pour les scripts et un modèle de gestionnaire de ressources Azure, consultez les [instructions de génération détaillées][Example2].

#### <a name="nsg-description"></a>Description du NSG
Dans cet exemple, un groupe NSG est généré et ensuite chargé avec six règles.

>[AZURE.TIP] En règle générale, vous devez créer les règles « Autoriser » spécifiques en premier lieu, suivie par les règles de « Refuser » plus génériques. La priorité donnée détermine les règles sont évaluées en premier. Une fois que le trafic est trouvé à appliquer à une règle spécifique, aucuns plus de règles ne sont évaluées. Règles NSG s’appliquent dans la direction entrante ou sortante (du point de vue du sous-réseau).

De façon déclarative, les règles suivantes sont en cours de génération pour le trafic entrant :

1.  Trafic interne de DNS (port 53) est autorisé.
2.  Le trafic RDP (le port 3389) à partir d’Internet à n’importe quelle Machine virtuelle est autorisé.
3.  Tout le trafic Internet (tous les ports) à l’appliance virtuelle du réseau (pare-feu) est autorisé.
4.  Tout le trafic (tous les ports) à partir de IIS01 à AppVM1 est autorisé.
5.  Tout le trafic (tous les ports) à partir d’Internet à l’ensemble du réseau virtuel (les deux sous-réseaux) est refusé.
6.  Tout le trafic (tous les ports) à partir du sous-réseau frontal au sous-réseau back-end est refusé.

Avec ces règles lié à chaque sous-réseau, si une requête HTTP a été entrante à partir d’Internet et le pare-feu, les deux règles 3 (autoriser) et 5 (refuser) s’appliquent. Mais dans la mesure où les règles 3 a une priorité plus élevée, seulement il serait applicable et règle 5 ne serait pas entrent en jeu. Ainsi, la requête HTTP serait autorisée au pare-feu. Si le même trafic tentait d’atteindre le serveur IIS01, même s’il est sur le sous-réseau frontal, la règle 5 (refuser) s’appliquent, et le trafic ne serait pas être autorisé à transmettre au serveur. Règle 6 (refuser) bloque le sous-réseau frontal de communiquer au sous-réseau back-end (à l’exception du trafic autorisé dans les règles 1 et 4). Ceci protège le réseau principal au cas où un intrus compromet l’application web sur le serveur frontal. L’attaquant aurait ont d’un accès limité au réseau principal « protected » (uniquement pour les ressources exposées sur le serveur de AppVM01).

Il existe une règle sortante par défaut qui autorise le trafic vers Internet. Pour cet exemple, nous allons autorisant le trafic sortant et ne modifie ne pas les règles de trafic sortant. Pour verrouiller le trafic dans les deux directions, défini par l’utilisateur un routage est requis (voir exemple 3).

#### <a name="firewall-rule-description"></a>Description de règle de pare-feu
Sur le pare-feu, règles de transfert doivent être créé. Dans la mesure où cet exemple achemine uniquement le trafic Internet entrant au pare-feu et ensuite au serveur web, un seul transfert traduction d’adresses réseau (NAT) règle est nécessaire.

La règle de transfert accepte n’importe quelle adresse source entrante qui atteint le pare-feu, essayez d’atteindre HTTP (port 80 ou 443 pour HTTPS). Il a envoyé de l’interface du pare-feu local et redirigés vers le serveur web avec l’adresse IP de 10.0.1.5.

#### <a name="conclusion"></a>Conclusion
Il s’agit d’un moyen relativement simple de protéger votre application à l’aide d’un pare-feu et d’isoler le sous-réseau back-end du trafic entrant. Pour plus d’informations, consultez les [instructions de génération détaillées][Example2]. Ces instructions sont les suivantes :

- La création de ce réseau de périmètre avec scripts PowerShell.
- Comment générer cet exemple avec un modèle de gestionnaire de ressources Azure.
- Descriptions détaillées de chaque règle de pare-feu et de commande NSG.
- Scénarios de trafic détaillées, indiquant comment le trafic est autorisé ou refusé dans chaque couche.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemple 3 : Créer un réseau de périmètre pour protéger les réseaux avec un pare-feu, UDR et NSG
[Au démarrage rapide](#fast-start) | [instructions pour que cet exemple de génération de détails][Example3]

![Réseau de périmètre de bidirectionnelle avec les UDR, NSG et à couloirs][9]

#### <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il existe un abonnement qui contient les éléments suivants :

- Trois services en nuage : « SecSvc001 », « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel, « CorpNetwork », avec les trois sous-réseaux : « SecNet », « Frontal » et « Back-end »
- Un matériel de réseau virtuel, un pare-feu, dans ce cas est connecté au sous-réseau SecNet
- Un serveur Windows qui représente un serveur web d’application (« IIS01 »)
- Deux serveurs Windows qui représentent l’application sur les serveurs principaux (« AppVM01 », « AppVM02 »)
- Un serveur Windows qui représente un serveur DNS (« DNS01 »)

Pour les scripts et un modèle de gestionnaire de ressources Azure, consultez les [instructions de génération détaillées][Example3].

#### <a name="udr-description"></a>Description de UDR
Par défaut, les itinéraires de système suivants sont définis comme :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

Le VNETLocal est toujours le prefix(es) défini d’adresse de réseau virtuel pour ce réseau spécifique (autrement dit, il passe de réseau virtuel à un réseau virtuel, selon la façon dont chaque réseau virtuel spécifique est défini). Les itinéraires de système restant sont statiques et par défaut comme indiqué dans le tableau.

Dans cet exemple, deux tables de routage sont créées, une pour les sous-réseaux front-end et back-end. Chaque table est chargée avec des itinéraires statiques appropriés pour le sous-réseau donné. Pour cet exemple, chaque table possède trois itinéraires directement tout le trafic (0.0.0.0/0) via le pare-feu (saut suivant = adresse IP virtuelle de solution matérielle-logicielle) :

1. Le trafic du sous-réseau local avec aucune zone de tronçon suivant définie pour autoriser le trafic du sous-réseau local à contourner le pare-feu.
2. Trafic de réseau virtuel avec un tronçon suivant définie en tant que pare-feu ; Ce paramètre remplace la règle par défaut autorisant local virtuel le trafic réseau acheminer directement.
3. Tous les autres le trafic (0/0) avec un tronçon suivant définie comme le pare-feu.

>[AZURE.TIP] N’ayant ne pas de l’entrée de sous-réseau local dans le UDR interrompt les communications du sous-réseau local. 
> - Dans notre exemple, 10.0.1.0/24 pointant vers VNETLocal est critique que dans le cas contraire, paquet en laissant le serveur Web (10.0.1.4) destiné à un autre serveur local (par exemple) 10.0.1.25 échouera car ils seront envoyées par à l’à couloirs, qui lui enverra au sous-réseau, et le sous-réseau sera ré-envoyer à l’à couloirs et ainsi de suite.
> - Risques d’une boucle de routage sont généralement très élevés sur les appliances de plusieurs cartes qui sont directement connectés à chaque sous-réseau, ils communiquent ce qui est souvent de traditionnel, sur site, les appliances. 

Une fois les tables de routage sont créés, ils sont liés à leurs sous-réseaux. La table de routage sous-réseau frontal, une fois créé et lié au sous-réseau, ressemblerait à ceci :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE] UDR peut maintenant être appliquée au sous-réseau passerelle sur lesquels le circuit ExpressRoute est connecté.
>
> Exemples d’activation de votre réseau de périmètre avec ExpressRoute ou de la mise en réseau de site à site sont illustrés dans les exemples 3 et 4.


#### <a name="ip-forwarding-description"></a>Description de transfert IP
Le routage IP est une fonction de guide à UDR. Il s’agit d’un paramètre sur une appliance virtuelle qui lui permet de recevoir du trafic non spécifiquement adressé à la solution matérielle-logicielle, puis transférer ce trafic vers sa destination finale.

Par exemple, si le trafic à partir de AppVM01 effectue une demande au serveur DNS01, UDR Router ce pare-feu. Avec le routage IP activé, le trafic à la destination de DNS01 (10.0.2.4) est accepté par la solution matérielle-logicielle (10.0.0.4), puis transféré vers sa destination finale (10.0.2.4). Sans le routage IP activé sur le pare-feu, le trafic ne serait pas accepté par la solution matérielle-logicielle même si la table de routage possède le pare-feu comme tronçon suivant. Pour utiliser l’appliance virtuelle, il est essentiel de n’oubliez pas d’activer le routage IP en conjonction avec UDR.

#### <a name="nsg-description"></a>Description du NSG
Dans cet exemple, un groupe NSG est généré et ensuite chargé avec une seule règle. Ce groupe est ensuite lié uniquement aux sous-réseaux front-end et back-end (pas le SecNet). Déclarative la règle suivante est en cours de génération :

- Tout le trafic (tous les ports) à partir d’Internet à l’ensemble du réseau virtuel (tous les sous-réseaux) est refusé.

Bien que NSGs sont utilisés dans cet exemple, sa fonction principale est comme une couche secondaire de défense contre une mauvaise configuration manuelle. L’objectif est de bloquer tout le trafic entrant à partir d’Internet pour les sous-réseaux front-end ou back-end. Le trafic doit passer uniquement via le sous-réseau SecNet au pare-feu (et, le cas échéant, sur les sous-réseaux front-end ou back-end). De plus, avec les règles UDR en place, tout ayant effectué dans les sous-réseaux front-end ou back-end serait trafic vers le pare-feu (grâce à UDR). Le pare-feu voit ce sous la forme d’un flux asymétrique et tomberaient du trafic sortant. Par conséquent, il existe trois couches de sécurité protégeant les sous-réseaux :
- Aucun point de terminaison ouvert sur les FrontEnd001 et les BackEnd001 services en nuage.
- NSGs refuser le trafic provenant d’Internet.
- Le trafic asymétrique de la suppression de pare-feu.

Un point intéressant concernant la NSG dans cet exemple est qu’il contient une seule règle, qui consiste à refuser le trafic Internet vers le réseau virtuel complet, y compris le sous-réseau de sécurité. Toutefois, étant donné que le NSG est lié uniquement vers les sous-réseaux du front-end et back-end, la règle n’est pas traitée sur le trafic entrant pour le sous-réseau de sécurité. Par conséquent, le trafic passera au sous-réseau de sécurité.

#### <a name="firewall-rules"></a>Règles de pare-feu
Sur le pare-feu, règles de transfert doivent être créé. Étant donné que le pare-feu est le blocage ou transfert de tous les entrant, sortant et le trafic de réseau intra-virtuel, de nombreuses règles de pare-feu sont nécessaires. En outre, tout le trafic entrant sera atteint l’adresse IP publique de Service de sécurité (sur des ports différents), pour être traitées par le pare-feu. Il est recommandé de créer un diagramme de flux logiques avant de configurer les sous-réseaux et les règles de pare-feu, pour éviter de reprendre ultérieurement. La figure suivante est une vue logique des règles de pare-feu pour que cet exemple :
 
![Vue logique de règles de pare-feu][10]

>[AZURE.NOTE] En fonction de l’Appliance virtuelle réseau utilisé, les ports de gestion peuvent varier. Dans cet exemple, un pare-feu de NextGen Barracuda est référencé, qui utilise les ports 22, 801 et 807. Consultez la documentation du fournisseur de matériel pour rechercher les ports exacts utilisés pour la gestion de l’appareil utilisé.

#### <a name="firewall-rules-description"></a>Description des règles de pare-feu
Dans le diagramme logique précédent, le sous-réseau de sécurité n’est pas affiché. C’est parce que le pare-feu est la seule ressource sur ce sous-réseau, et ce diagramme affiche les règles de pare-feu et comment ils logiquement autorisent ou refuser des flux de trafic, pas le chemin routé. En outre, les ports externes sélectionnés pour le trafic RDP sont plus élevés plage de ports (8014 – 8026) et ont été sélectionnés pour aligner un peu avec les deux derniers octets de l’adresse IP locale pour être plus lisibles (par exemple, l’adresse du serveur local 10.0.1.4 est associé à un port externe 8014). Les ports non conflictuelles supérieurs, cependant, peuvent être utilisées.

Pour cet exemple, nous avons besoin de sept types de règles :

- Règles externes (pour le trafic entrant) :
  1.    Règle de gestion de pare-feu : règle de redirection de cette application autorise le trafic sur les ports de gestion du matériel réseau virtuel.
  2.    Règles RDP (pour chaque serveur Windows) : ces quatre règles (une pour chaque serveur) permettent la gestion des serveurs individuels via RDP. Cela pourrait également regroupé dans une seule règle, en fonction des capacités du matériel réseau virtuel en cours d’utilisation.
  3.    Règles de trafic d’applications : il y a deux de ces règles, la première pour le trafic web frontal et la seconde pour le trafic de back-end (par exemple, serveur web à la couche de données). La configuration de ces règles dépend de l’architecture du réseau (où sont placés vos serveurs) et les flux de trafic (direction dans laquelle les flux de trafic et quels ports sont utilisés).
      - La première règle autorise le trafic de l’application réelle atteindre le serveur d’application. Tandis que les autres règles de sécurité et de gestion, les règles de trafic d’application sont qu’Autoriser les utilisateurs externes ou des services accéder aux applications. Pour cet exemple, il existe un serveur web sur le port 80. Ainsi, une règle d’application unique pare-feu redirige le trafic entrant vers l’adresse IP externe, à l’adresse IP interne des serveurs web. La session de trafic redirigé serait traduite via NAT vers le serveur interne.
      - La deuxième est la règle de back-end pour permettre au serveur web communiquer avec le serveur de AppVM01 (mais pas AppVM02) via n’importe quel port.
- Règles internes (pour le trafic intra-virtuel de réseau)
  4.    Règle Internet sortant : cette règle autorise le trafic à partir de n’importe quel réseau pour transmettre vers les réseaux sélectionnés. Cette règle est généralement une règle par défaut déjà sur le pare-feu, mais dans un état désactivé. Cette règle doit être activée pour cet exemple.
  5.    Règle DNS : cette règle autorise uniquement le trafic DNS (port 53) pour le serveur DNS. Dans cet environnement, la plupart du trafic à partir de la partie frontale pour le back-end est bloqué. Cette règle permet en particulier DNS à partir de n’importe quel sous-réseau local.
  6.    Sous-réseau de règle de sous-réseau : cette règle est de permettre à n’importe quel serveur sur le sous-réseau du back-end pour se connecter à n’importe quel serveur sur le sous-réseau frontal (mais pas l’inverse).
- Règle de prévention de défaillance (pour le trafic qui ne respecte pas une des précédentes) :
  7.    Refuser toutes les règles de trafic : il doit toujours être la dernière règle (en termes de priorité), et en tant que tel si un flux de trafic ne correspond pas à une des précédentes de règles il est supprimé par cette règle. Ceci est une règle par défaut et généralement activé. Aucune modification n’est généralement nécessaire.

>[AZURE.TIP] Sur la deuxième règle de trafic de l’application, pour simplifier cet exemple, n’importe quel port est autorisé. Dans un scénario réel, le port plus spécifique et les plages d’adresses doivent être utilisés pour réduire la surface d’attaque de cette règle.

Une fois que toutes les règles précédentes sont créées, il est important de réviser la priorité de chaque règle pour assurer le trafic est autorisé ou refusé comme vous le souhaitez. Pour cet exemple, les règles sont dans l’ordre de priorité.

#### <a name="conclusion"></a>Conclusion
Il s’agit d’une façon plus complexe mais plus complète de protection et d’isoler le réseau que les exemples précédents. (Exemple 2 protège uniquement l’application et exemple 1 isole simplement des sous-réseaux). Cette conception permet de surveiller le trafic dans les deux directions et protège non seulement le serveur entrant application mais met en œuvre la stratégie de sécurité du réseau pour tous les serveurs sur ce réseau. En outre, selon le matériel utilisé, le trafic total d’audit et de sensibilisation est possible. Pour plus d’informations, consultez les [instructions de génération détaillées][Example3]. Ces instructions sont les suivantes :

- Comment construire cet exemple de réseau de périmètre avec scripts PowerShell.
- Comment générer cet exemple avec un modèle de gestionnaire de ressources Azure.
- La description détaillée du chaque UDR, NSG commande et règle de pare-feu.
- Scénarios de trafic détaillées, indiquant comment le trafic est autorisé ou refusé dans chaque couche.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-virtual-private-network-vpn"></a>Exemple 4 : Ajouter une connexion hybride avec un réseau privé virtuel de l’appliance virtuel de site à site (VPN)
[À Fast start](#fast-start) | Détaillée des instructions de génération disponibles bientôt

![Réseau de périmètre avec à couloirs connecté réseau hybride][11]

#### <a name="environment-description"></a>Description de l’environnement
Réseau hybride à l’aide d’une appliance virtuelle du réseau (à couloirs) peut être ajouté à tous les types de réseau de périmètre décrits dans les exemples 1, 2 ou 3.

Comme illustré dans la figure précédente, une connexion VPN via Internet (site à site) est utilisée pour connecter un réseau local à un réseau virtuel Azure via un à couloirs.

>[AZURE.NOTE] Si vous utilisez ExpressRoute avec l’option Azure Public Peering est activée, un itinéraire statique doit être créé. Cela doit acheminer vers l’adresse IP du VPN à couloirs votre entreprise Internet et non via le WAN ExpressRoute. Le NAT requis sur l’option ExpressRoute le Peering Public Azure peut interrompre la session VPN.

Une fois la connexion VPN est en place, l’à couloirs devient le concentrateur central pour tous les réseaux et sous-réseaux. Les règles de transmission du pare-feu déterminent les flux de trafic est autorisés, sont traduits par NAT, sont redirigées ou sont supprimées (même pour les flux de trafic entre le réseau de locaux et Azure).

Flux de trafic doivent être avec attention, comme ils peuvent être optimisées ou dégradée par ce modèle de conception, selon cas d’usage.

À l’aide de l’environnement créé dans l’exemple 3, puis en ajoutant une connexion de réseau de site à site VPN hybride, produit la conception suivante :

![Réseau de périmètre avec à couloirs connecté à l’aide d’un VPN de site à site][12]

Le routeur local ou tout autre périphérique réseau qui est compatible avec votre à couloirs pour VPN, serait le client VPN. Ce périphérique physique est responsable pour l’initialisation et le maintien de la connexion VPN avec votre à couloirs.

Logiquement pour aux couloirs, le réseau ressemble à quatre distinct « zones de sécurité » avec les règles sur l’à couloirs étant le directeur principal du trafic entre ces zones :

![Réseau logique du point de vue à couloirs][13]

#### <a name="conclusion"></a>Conclusion
L’ajout d’une connexion réseau de site à site VPN hybride à un réseau virtuel Azure peut étendre le réseau local dans Azure de manière sécurisée. En utilisant une connexion VPN, le trafic est crypté et achemine via Internet. L’à couloirs dans cet exemple fournit un emplacement central pour mettre en œuvre et de gérer la stratégie de sécurité. Pour plus d’informations, consultez les instructions de génération détaillées (à paraître). Ces instructions sont les suivantes :

- Comment construire cet exemple de réseau de périmètre avec scripts PowerShell.
- Comment générer cet exemple avec un modèle de gestionnaire de ressources Azure.
- Scénarios de trafic détaillées, indiquant comment le trafic passe par le biais de cette conception.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn"></a>Exemple 5 : Ajouter une connexion hybride avec la passerelle VPN site à site, Azure
[À Fast start](#fast-start) | Détaillée des instructions de génération disponibles bientôt

![Réseau de périmètre avec la passerelle réseau connecté hybride][14]

#### <a name="environment-description"></a>Description de l’environnement
Réseau hybride à l’aide d’une passerelle VPN d’Azure peut être ajouté à des deux types de réseau de périmètre décrit dans les exemples 1 ou 2.

Comme illustré dans la figure précédente, une connexion VPN via Internet (site à site) est utilisée pour connecter un réseau local à un réseau virtuel Azure via une passerelle VPN d’Azure.

>[AZURE.NOTE] Si vous utilisez ExpressRoute avec l’option Azure Public Peering est activée, un itinéraire statique doit être créé. Cela doit acheminer vers l’adresse IP du VPN à couloirs votre entreprise Internet et non via le WAN ExpressRoute. Le NAT requis sur l’option ExpressRoute le Peering Public Azure peut interrompre la session VPN.

La figure suivante montre les deux bords de réseau dans cette option. Sur le premier point, l’à couloirs et NSGs de contrôle des flux de trafic pour les réseaux intra-Azure et entre Azure et Internet. La deuxième arête est la passerelle VPN d’Azure, qui est une arête de réseau complètement séparé et isolées entre Azure et locaux.

Flux de trafic doivent être avec attention, comme ils peuvent être optimisées ou dégradée par ce modèle de conception, selon cas d’usage.

À l’aide de l’environnement créé dans l’exemple 1, puis en ajoutant une connexion de réseau de site à site VPN hybride, produit la conception suivante :

![Réseau de périmètre avec la passerelle connecté à l’aide d’une connexion ExpressRoute][15]

#### <a name="conclusion"></a>Conclusion
L’ajout d’une connexion réseau de site à site VPN hybride à un réseau virtuel Azure peut étendre le réseau local dans Azure de manière sécurisée. À l’aide de la passerelle VPN d’Azure native, le trafic est crypté IPSec et achemine via Internet. À l’aide de la passerelle VPN d’Azure peut également, fournir une option (aucune licence supplémentaire de coût comme avec NVAs de tiers). Cette solution est plus économique dans l’exemple 1, où aucun à couloirs n’est utilisé. Pour plus d’informations, consultez les instructions de génération détaillées (à paraître). Ces instructions sont les suivantes :

- Comment construire cet exemple de réseau de périmètre avec scripts PowerShell.
- Comment générer cet exemple avec un modèle de gestionnaire de ressources Azure.
- Scénarios de trafic détaillées, indiquant comment le trafic passe par le biais de cette conception.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Exemple 6 : Ajouter une connexion hybride avec ExpressRoute
[À Fast start](#fast-start) | Détaillée des instructions de génération disponibles bientôt

![Réseau de périmètre avec la passerelle réseau connecté hybride][16]

#### <a name="environment-description"></a>Description de l’environnement
Réseau hybride à l’aide d’une connexion d’homologation privée ExpressRoute peut être ajouté à des deux types de réseau de périmètre décrit dans les exemples 1 ou 2.

Comme illustré dans la figure précédente, ExpressRoute privé peering fournit une connexion directe entre votre réseau local et le réseau virtuel Azure. Que le réseau du fournisseur de service et le réseau Microsoft Azure, jamais toucher Internet transitent sur le trafic.

>[AZURE.NOTE] Il existe certaines restrictions lors de l’utilisation de UDR avec ExpressRoute, en raison de la complexité du routage dynamique est utilisée dans la passerelle virtuelle Azure. Celles-ci sont les suivantes :
>
>- UDR ne doit pas être appliqué au sous-réseau passerelle sur lequel la passerelle liée ExpressRoute virtuelle Azure est connectée.
>- La passerelle liée ExpressRoute virtuelle Azure ne peut pas être que le périphérique de saut suivant pour les autre UDR lié des sous-réseaux.
>
>
<br />

>[AZURE.TIP] À l’aide de ExpressRoute, permet de conserver le trafic de réseau d’entreprise sur Internet pour une meilleure sécurité et accroît considérablement les performances. Il permet également de contrats de niveau de service à partir de votre fournisseur de ExpressRoute. La passerelle Azure peut passer jusqu'à 2 Go/s avec ExpressRoute, considérant qu’avec les connexions VPN de site à site, le débit maximal de passerelle d’Azure est de 200 Mo/s.

Comme illustré dans le diagramme suivant, cette option l’environnement a maintenant deux bords du réseau. L’à couloirs et NSG de contrôle des flux de trafic pour les réseaux intra-Azure et entre Azure et d’Internet, alors que la passerelle est une arête de réseau complètement séparé et isolées entre Azure et locaux.

Flux de trafic doivent être avec attention, comme ils peuvent être optimisées ou dégradée par ce modèle de conception, selon cas d’usage.

À l’aide de l’environnement créé dans l’exemple 1, puis en ajoutant une connexion de réseau hybride ExpressRoute, produit la conception suivante :

![Réseau de périmètre avec la passerelle connecté à l’aide d’une connexion ExpressRoute][17]

#### <a name="conclusion"></a>Conclusion
L’ajout d’une connexion de réseau privé de ExpressRoute Peering peut étendre le réseau local dans Azure dans une latence sécurisée, de réduire, exécution supérieure de manière. En outre, à l’aide de la passerelle Azure natif, comme dans cet exemple, fournit une option (ne plue de licences comme avec NVAs de tiers). Pour plus d’informations, consultez les instructions de génération détaillées (à paraître). Ces instructions sont les suivantes :

- Comment construire cet exemple de réseau de périmètre avec scripts PowerShell.
- Comment générer cet exemple avec un modèle de gestionnaire de ressources Azure.
- Scénarios de trafic détaillées, indiquant comment le trafic passe par le biais de cette conception.

## <a name="references"></a>Références
### <a name="helpful-websites-and-documentation"></a>Documentation et des sites Web utiles
- Accès Azure avec le Gestionnaire de ressources Azure :
- Accéder à Azure avec PowerShell : [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Documentation de mise en réseau virtuelle : [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Documentation de groupe de sécurité du réseau : [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Documentation de routage défini par l’utilisateur : [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Les passerelles virtuels Azure : [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- Connexions VPN de site à Site : [https://azure.microsoft.com/documentation/articles/vpn-gateway-create-site-to-site-rm-powershell](./vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- Documentation de ExpressRoute (veillez à consulter les sections « Mise en route » et « How To ») : [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Organigramme des Options de sécurité"
[1]: ./media/best-practices-network-security/compliancebadges.png "Badges de conformité Azure"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Fonctionnalités de sécurité Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Un réseau de périmètre dans un réseau d’entreprise"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Architecture de la sécurité Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Un réseau de périmètre d’un réseau virtuel Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Réseau hybride avec trois limites de sécurité"
[7]: ./media/best-practices-network-security/example1design.png "DMZ entrant avec NSG"
[8]: ./media/best-practices-network-security/example2design.png "DMZ entrant à couloirs et NSG"
[9]: ./media/best-practices-network-security/example3design.png "DMZ bidirectionnelle avec UDR, NSG et à couloirs"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Vue logique de règles de pare-feu"
[11]: ./media/best-practices-network-security/example4designoptions.png "DMZ avec à couloirs connecté réseau hybride"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ avec à couloirs connecté à l’aide d’un VPN de Site à Site"
[13]: ./media/best-practices-network-security/example4networklogical.png "Réseau logique du point de vue à couloirs"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ avec Azure passerelle connecté réseau hybride de Site à Site"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ avec la passerelle Azure à l’aide de VPN de Site à Site"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ avec Azure passerelle connecté réseau hybride de ExpressRoute"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ avec la passerelle Azure à l’aide d’une connexion ExpressRoute"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
