<properties 
   pageTitle="Forum aux questions de la passerelle VPN de réseau virtuel | Microsoft Azure"
   description="La Forum aux questions sur la passerelle VPN. Forum aux questions sur les connexions de réseau virtuel de Microsoft Azure coexistence, les connexions de configuration hybride et passerelles VPN"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# <a name="vpn-gateway-faq"></a>Passerelle VPN Forum aux questions

## <a name="connecting-to-virtual-networks"></a>Connexion à des réseaux virtuels

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Est-il possible de connecter des réseaux virtuels dans différentes régions Azure ?
Oui. En fait, il n’existe aucune contrainte de région. Un réseau virtuel peut se connecter à un autre réseau virtuel dans la même région ou dans une autre région d’Azure.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Est-il possible de connecter des réseaux virtuels dans les différentes souscriptions ?
Oui.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Puis-je connecter à plusieurs sites à partir d’un seul réseau virtuel ?

Vous pouvez vous connecter à plusieurs sites à l’aide de Windows PowerShell et les API de reste Azure. Reportez-vous à la section [multisites et connectivité de VNet à VNet](#multi-site-and-vnet-to-vnet-connectivity) Forum aux questions.
## <a name="what-are-my-cross-premises-connection-options"></a>Quelles sont mes options de connexion de coexistence ?

Les connexions de coexistence suivantes sont prises en charge :

- [Site-à-Site](vpn-gateway-site-to-site-create.md) – connexion VPN sur IPsec (IKE v1 et v2 de IKE). Ce type de connexion nécessite un périphérique VPN ou le service RRAS.

- [Point-à-Site](vpn-gateway-point-to-site-create.md) – connexion VPN sur SSTP (Secure Socket Tunneling Protocol). Cette connexion ne nécessite pas un périphérique VPN.

- [VNet-à-VNet](virtual-networks-configure-vnet-to-vnet-connection.md) – ce type de connexion est identique à une configuration de Site à Site. VNet à VNet est une connexion VPN sur IPsec (IKE v1 et v2 de IKE). Il ne nécessite pas un périphérique VPN.

- [Plusieurs sites](vpn-gateway-multi-site.md) – il s’agit d’une variante d’une configuration de Site à Site qui vous permet de connecter plusieurs sites en local à un réseau virtuel.

- [ExpressRoute](../expressroute/expressroute-introduction.md) – ExpressRoute est une connexion directe vers Azure à partir de votre réseau étendu, pas sur l’Internet public. Consultez la [Présentation technique de ExpressRoute](../expressroute/expressroute-introduction.md) et le [ExpressRoute FAQ](../expressroute/expressroute-faqs.md) pour plus d’informations.

Pour plus d’informations sur les connexions, reportez-vous à la section [Sur la passerelle de VPN](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Quelle est la différence entre une connexion de Site à Site et le Site-à-Point ?

Les connexions de **Site à Site** vous permettent de connecter entre les ordinateurs situés dans vos locaux à n’importe quel ordinateur virtuel ou d’une instance de rôle au sein de votre réseau virtuel, selon le mode choisi configurer le routage. Il constitue une excellente solution pour une connexion de coexistence toujours disponibles et est parfaitement adaptée aux configurations des hybrides. Ce type de connexion s’appuie sur une solution VPN IPsec (matériel ou application logicielle), qui doit être déployée à la périphérie de votre réseau. Pour créer ce type de connexion, vous devez disposer le matériel nécessaire est VPN et une adresse IPv4 tourné vers l’extérieur.

**Point-à-Site** connexions vous permettent de vous connecter à partir d’un seul ordinateur depuis n’importe où pour tout ce qui se trouve dans votre réseau virtuel. Il utilise le client VPN de zone dans Windows. Dans le cadre de la configuration de Point-à-Site, vous installez un certificat et un package de configuration de client VPN, qui contient les paramètres qui permettent à votre ordinateur pour vous connecter à n’importe quel ordinateur virtuel ou d’une instance de rôle au sein du réseau virtuel. Il est idéal lorsque vous souhaitez vous connecter à un réseau virtuel, mais ne trouve sur site. Il est également un bon choix lorsque vous n’avez pas accès au matériel VPN ou une adresse IPv4 tourné vers l’extérieur, qui sont tous deux requis pour une connexion de Site à Site. 

Vous pouvez configurer votre réseau virtuel pour utiliser le Site à Site et Point-à-Site simultanément, sous réserve que vous créez votre connexion de Site à Site à l’aide d’un type VPN basée sur un itinéraire pour la passerelle. Basée sur une gamme de types VPN sont appelés passerelles dynamiques dans le modèle de déploiement classique.

### <a name="what-is-expressroute"></a>Quel est ExpressRoute ?

ExpressRoute vous permet de créer des connexions entre les centres de données Microsoft et de l’infrastructure dans vos locaux ou dans un environnement de colocalisation. Avec ExpressRoute, vous pouvez établir des connexions aux services de cloud de Microsoft tels que Microsoft Azure et Office 365 dans un centre de colocalisation partenaire ExpressRoute ou vous connecter directement à partir de votre réseau WAN existante (par exemple, un VPN MPLS fournis par un fournisseur de services réseau).

ExpressRoute connexions offrent une meilleure sécurité, plus de fiabilité, bande passante supérieure et latence inférieure que les connexions classiques sur Internet. Dans certains cas, à l’aide de connexions de ExpressRoute pour transférer des données entre votre réseau de locaux et la Azure peut également produire des économies significatives. Si vous avez déjà créé une connexion de coexistence à partir de votre réseau local vers Azure, vous pouvez migrer vers une connexion ExpressRoute, tout en conservant votre réseau virtuel.

Consultez le [Forum aux questions sur ExpressRoute](../expressroute/expressroute-faqs.md) pour plus de détails.

## <a name="site-to-site-connections-and-vpn-devices"></a>Les périphériques VPN et les connexions de site à Site

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Que dois-je prendre en compte lors de la sélection d’un périphérique VPN ?

Nous avons validé un ensemble de périphériques standard de VPN de Site à Site en partenariat avec les fabricants de périphériques. Une liste de connus périphériques VPN compatibles, leurs instructions de configuration correspondantes ou des exemples et caractéristiques du dispositif se trouve [ici](vpn-gateway-about-vpn-devices.md). Tous les périphériques dans les familles de périphérique listés comme compatible connu doivent fonctionner avec le réseau virtuel. Pour vous aider à configurer votre périphérique VPN, reportez-vous à l’exemple de configuration de périphérique ou un lien qui correspond à la famille de périphérique approprié.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>Que faire si j’ai un périphérique VPN qui ne figure pas dans la liste des périphériques compatibles connus ?

Si vous ne voyez pas votre périphérique est répertorié comme un périphérique compatible VPN et que vous souhaitez utiliser pour votre connexion VPN, vous devez vérifier qu’il répond à la prise en charge IPsec/IKE configuration options et les paramètres répertoriés [ici](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Exigences minimales de périphériques fonctionne parfaitement avec les passerelles VPN. Pour des instructions supplémentaires sur la configuration et la prise en charge, contactez le fabricant de votre périphérique.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Pourquoi mon tunnel VPN basée sur la stratégie a-t-elle vers le bas lorsque le trafic est inactif ?

Ce comportement est attendu pour le routage par stratégie (également connu sous le nom statique) passerelles VPN. Lorsque le trafic via le tunnel est inactif pendant plus de 5 minutes, le tunnel sera détruit. Démarrage de trafic circulant dans les deux sens, le tunnel sera rétabli immédiatement.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Puis-je utiliser le logiciel VPN pour se connecter à Azure ?

Nous prenons en charge le routage Windows Server 2012 et les serveurs d’accès distant (RRAS) pour la configuration de coexistence de Site à Site.

Autres solutions VPN de logiciels doivent collaborer avec notre passerelle tant qu’ils sont conformes aux implémentations IPsec standard de secteur. Contactez le fournisseur du logiciel pour obtenir des instructions de configuration et prise en charge.

## <a name="point-to-site-connections"></a>Connexions point-à-Site

### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>Quels systèmes d’exploitation puis-je utiliser avec Point-à-Site ?

Les systèmes d’exploitation suivants sont pris en charge :

- Windows 7 (32 bits et 64 bits)

- Windows Server 2008 R2 (64 bits uniquement)

- Windows 8 (32 bits et 64 bits)

- 8.1 Windows (32 bits et 64 bits)

- Windows Server 2012 (64 bits uniquement)

- Windows Server 2012 R2 (64 bits uniquement)

- Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>Puis-je utiliser n’importe quel logiciel client VPN pour Point-à-Site qui prend en charge de SSTP ?

N° Prise en charge est limitée uniquement pour les versions de système d’exploitation Windows répertoriées ci-dessus.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Des points de terminaison VPN clients combien puis-je disposer dans ma configuration de Point-à-Site ?

Nous prenons en charge jusqu'à 128 clients VPN pour se connecter à un réseau virtuel en même temps.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Puis-je utiliser ma propre autorité de certification de racine PKI interne pour la connectivité de Point-à-Site ?

Oui. Auparavant, seuls les certificats racines auto-signés peuvent être utilisés. Vous pouvez télécharger les certificats racine 20.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Est-il possible de parcourir des serveurs proxy et pare-feu à l’aide de la fonction de Point-à-Site ?

Oui. Nous utilisons SSTP (Secure Socket Tunneling Protocol) au tunnel à travers des pare-feu. Ce tunnel s’affiche sous la forme d’une connexion HTTPs.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Si je redémarre un ordinateur client configuré pour le Point-à-Site, la connexion VPN se reconnectera automatiquement ?

Par défaut, l’ordinateur client va rétablir pas automatiquement la connexion VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Est prise en charge de Point-à-Site la reconnexion automatique et DDNS sur les clients VPN ?

Reconnexion automatique et DDNS ne sont actuellement pas pris en charge dans les VPN Site-à-Point.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Est-il de Site à Site et les configurations de Point-à-Site coexistent au même réseau virtuel ?

Oui. Ces deux solutions fonctionnent si vous avez un type RouteBased VPN pour votre passerelle. Pour le modèle de déploiement classique, vous avez besoin d’une passerelle dynamique. Nous ne faisons pas prise en charge de Point-à-Site de passerelles VPN de routage statiques ou les passerelles à l’aide de VpnType - PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Est-il possible de configurer un client de Point-à-Site pour se connecter à plusieurs réseaux virtuels en même temps ?

Oui, c’est possible. Mais les réseaux virtuels ne peuvent pas avoir de chevauchement IP préfixes et les espaces d’adressage Point-à-Site ne doivent pas se chevaucher entre les réseaux virtuels.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quel débit puis-je attendre via des connexions de Site à Site ou Point-à-Site ?

Il est difficile de maintenir le débit exact des tunnels VPN. IPsec et SSTP sont des protocoles VPN crypto-lourds. Le débit est également limité par la latence et la bande passante entre vos locaux et Internet.

## <a name="gateways"></a>Passerelles

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Qu’est une stratégie (routage statique) passerelle ?

Les passerelles basées sur une stratégie implémentent des réseaux privés virtuels basés sur la stratégie. Réseaux privés virtuels basés sur la stratégie de chiffrement et dirigent des paquets par le biais de tunnels IPsec basées sur les combinaisons de préfixes d’adresses entre votre réseau local et de le VNet d’Azure. La stratégie (ou le sélecteur de trafic) est généralement défini comme une liste d’accès dans la configuration VPN.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Quelle est la passerelle (routage dynamique) basée sur un itinéraire ?

Les passerelles basées sur l’itinéraire implémentent les VPN basée sur un itinéraire. Réseaux privés virtuels basés sur un itinéraire utilisent « parcours » dans l’adresse IP de transfert ou de table de routage pour dirigent les paquets dans les interfaces de tunnel leur correspondant. Les interfaces de tunnel puis crypter ou déchiffrement les paquets et les tunnels. Le sélecteur de trafic ou de stratégie pour les réseaux privés virtuels de l’itinéraire en fonction sont configurés comme pour tout (ou des caractères génériques).

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Puis-je obtenir mon adresse IP de la passerelle VPN avant de créer il ?

N° Vous devez créer votre passerelle pour obtenir l’adresse IP. L’adresse IP change si vous supprimez et recréez votre passerelle VPN.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Comment mon tunnel VPN s’authentifier ?

VPN Azure utilise l’authentification PSK (clé prépartagée). Nous générer une clé prépartagée (PSK) lorsque nous créons le tunnel VPN. Vous pouvez modifier cette clé générée automatiquement pour votre propre avec l’applet de commande PowerShell de clé communiquée à l’avance de la valeur ou l’API REST.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Puis-je utiliser l’API défini de clé communiquée à l’avance pour configurer mon-basée sur des règles (routage statique) passerelle VPN ?

Oui, l’applet de commande PowerShell et de définir des API de clé pré-partagée peut servir à configurer Azure (statiques) VPN basée sur la stratégie et VPN de routage (dynamiques) basée sur un itinéraire.

### <a name="can-i-use-other-authentication-options"></a>Puis-je utiliser les autres options d’authentification ?

Nous sont limités à l’utilisation de clés prépartagées (PSK) pour l’authentification.

### <a name="what-is-the-gateway-subnet-and-why-is-it-needed"></a>Quel est le sous-réseau « passerelle » et pourquoi est-elle nécessaire ?

Nous avons un service passerelle exécutées pour activer la connectivité de coexistence. 

Vous devez créer un sous-réseau de passerelle pour votre VNet configurer une passerelle VPN. Tous les sous-réseaux de passerelle doivent être nommés GatewaySubnet pour fonctionner correctement. Ne nommez votre sous-réseau passerelle quelque chose d’autre. Et ne déploient pas les ordinateurs virtuels ou tout autre élément dans le sous-réseau de passerelle.

La taille minimale du sous-réseau passerelle dépend entièrement de la configuration que vous souhaitez créer. Bien qu’il soit possible de créer un sous-réseau passerelle aussi petit que /29 pour certaines configurations, nous vous recommandons de créer un sous-réseau de passerelle de /28 ou plus (/ 28, /27, /26 etc..). 

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Puis-je déployer des ordinateurs virtuels ou les instances de rôles pour mon sous-réseau passerelle ?

N°

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Comment spécifier que le trafic passe par l’intermédiaire de la passerelle VPN ?

Si vous utilisez le portail classique d’Azure, ajoutez chaque plage que vous souhaitez envoyé via la passerelle pour votre réseau virtuel de la page réseaux sous réseaux locaux.

### <a name="can-i-configure-forced-tunneling"></a>Puis-je configurer forcé de Tunneling ?

Oui. Consultez [configurer forcé de tunneling](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Puis-je installer mon propre serveur VPN dans Azure et l’utiliser pour se connecter à mon réseau local ?

Oui, vous pouvez déployer vos propres serveurs dans Azure à partir de l’Azure Marketplace ou créer votre propre routeurs VPN ou passerelles VPN. Vous devez configurer des itinéraires définis par l’utilisateur dans votre réseau virtuel pour assurer le trafic est acheminé correctement entre vos réseaux locaux et les sous-réseaux de votre réseau virtuel.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Pourquoi certains ports ouverts sur ma passerelle VPN ?

Ils sont nécessaires pour la communication d’infrastructure Azure. Ils sont protégés (verrouillé) par certificats Azure. Sans certificats appropriés, des entités externes, y compris les clients de ces passerelles, ne sera pas en mesure de provoquer un effet sur ces points de terminaison.

Une passerelle VPN est fondamentalement un périphérique multirésident avec un taraudage de carte réseau dans le réseau privé du client et une carte réseau vers le réseau public. Entités d’infrastructure Azure ne peut pas exploiter réseaux privés de client pour des raisons de conformité, d’utiliser des points de terminaison publics pour les communications de l’infrastructure. Les points de terminaison publics sont analysés régulièrement par l’audit de sécurité Azure.


### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Plus d’informations sur les types de passerelle, les exigences et débit

Pour plus d’informations, consultez [Paramètres passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>Plusieurs sites et VNet à VNet connectivité

### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>Quel type de passerelles peut prendre en charge plusieurs sites et VNet à VNet connectivité ?

Uniquement basée sur un itinéraire (routage dynamique) de réseaux privés virtuels.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Puis-je connecter un VNet avec un Type de VPN de RouteBased à un autre VNet avec un type de PolicyBased VPN ?

Non, les deux réseaux virtuels doivent utiliser basée sur un itinéraire (routage dynamique) VPN.

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>Est-ce que le trafic de VNet-à-VNet est sécurisée ?

Oui, il est protégé par le chiffrement de IPsec/IKE.

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>Le trafic de VNet à VNet circulent via le réseau principal Azure ?

Oui.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Un réseau virtuel peut connecter à combien les sites locaux et des réseaux virtuels ?

Max. 10 combiné pour les passerelles Basic et routage dynamique Standard ; 30 pour les passerelles VPN de performances élevées.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Faire utiliser Point-à-Site les réseaux privés virtuels avec mon réseau virtuel avec plusieurs tunnels VPN ?

Oui, les réseaux privés virtuels de Point-à-Site (P2S) peuvent être utilisés avec les passerelles VPN se connecter à plusieurs sites de locaux et d’autres réseaux virtuels.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Puis-je configurer plusieurs tunnels entre mon réseau virtuel et mon site local à l’aide de VPN de plusieurs sites ?

Non, les tunnels redondants entre un réseau virtuel Azure et un site local ne sont pas pris en charge.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>Peut il être superposées les espaces d’adressage entre les réseaux virtuels connectés et les sites locaux sur site ?

N° Chevauchement des espaces d’adressage entraîne le téléchargement du fichier de configuration réseau ou « Création de réseau virtuel « échec.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Je reçois plus de bande passante avec VPN de Site à Site plus que pour un seul réseau virtuel ?

Non, tous les tunnels VPN, y compris les VPN Site-à-Point, partagent la même passerelle VPN d’Azure et la bande passante disponible.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Puis-je utiliser passerelle VPN d’Azure au trafic de transit entre mes sites sur site ou à un autre réseau virtuel ?

**Modèle de déploiement classique**<br>
Trafic de transit via une passerelle de VPN d’Azure est possible en utilisant le modèle de déploiement classique, mais s’appuie sur les espaces d’adressage définis de manière statique dans le fichier de configuration réseau. BGP n’est pas encore pris en charge avec les réseaux virtuels Azure et les passerelles VPN en utilisant le modèle de déploiement classique. Sans protocole BGP, définition manuelle des espaces d’adressage de transit est très source d’erreurs et non recommandé.<br>
**Modèle de gestionnaire de ressources du déploiement**<br>
Si vous utilisez le modèle de déploiement du Gestionnaire de ressources, reportez-vous à la section [BGP](#bgp) pour plus d’informations.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure génère la même clé pré-partagée IPsec/IKE pour toutes les connexions de mon VPN pour le même réseau virtuel ?

Non, Azure par défaut génère des clés pré-partagées différentes pour des connexions VPN différentes. Toutefois, vous pouvez utiliser l’applet de commande PowerShell ou de définir des API REST VPN passerelle clé pour définir la valeur de clé que vous préférez. La clé doit être une chaîne alphanumérique d’une longueur de 1 à 128 caractères.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>Azure facture pour le trafic entre les réseaux virtuels ?

Pour le trafic entre les différents réseaux virtuels Azure, Azure frais uniquement le trafic traverser d’une région Azure à un autre. Le taux de frais est répertorié dans la page [Tarification de passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) de Azure.


### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Puis-je connecter un réseau virtuel avec IPsec VPN pour circuit de mon ExpressRoute ?

Oui, cela est pris en charge. Pour plus d’informations, voir [configurer les ExpressRoute et les connexions VPN de Site à Site qui coexistent](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="bgp"></a>BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 



## <a name="cross-premises-connectivity-and-vms"></a>Connectivité de coexistence et de machines virtuelles

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Si mon ordinateur virtuel est dans un réseau virtuel, et j’ai une connexion de coexistence, comment dois-je me connecter à la machine virtuelle ?

Vous disposez de plusieurs options. Si vous avez RDP activé et que vous avez créé un point de terminaison, vous pouvez vous connecter à votre machine virtuelle à l’aide de l’adresse IP virtuelle. Dans ce cas, vous devez spécifier l’adresse IP virtuelle et le port que vous souhaitez vous connecter. Vous devez configurer le port sur votre machine virtuelle pour le trafic. En règle générale, vous accédez au portail Azure classique et enregistrer les paramètres de la connexion RDP sur votre ordinateur. Les paramètres contiennent les informations de connexion nécessaires.

Si vous avez un réseau virtuel avec une connectivité entre sites configurée, vous pouvez vous connecter à votre machine virtuelle à l’aide de la DIP interne ou l’adresse IP privée. Vous pouvez également vous connecter à votre machine virtuelle en DIP interne à partir d’un autre ordinateur virtuel qui se trouve sur le même réseau virtuel. Vous ne pouvez pas RDP à votre machine virtuelle à l’aide de la DIP si vous vous connectez à partir d’un emplacement à l’extérieur de votre réseau virtuel. Par exemple, si vous avez un réseau virtuel Point-à-Site configuré et vous ne pas établir une connexion à partir de votre ordinateur, vous ne peut pas se connecter à la machine virtuelle en DIP.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Si mon ordinateur virtuel est dans un réseau virtuel avec une connectivité de coexistence, tout le trafic à partir de mon ordinateur virtuel passe par le biais de cette connexion ?

N° Que le trafic a une destination IP se trouvant dans les plages d’adresses IP du réseau Local virtuel de réseau que vous avez spécifié passe par l’intermédiaire de la passerelle réseau virtuel. Le trafic a une destination IP située sur le réseau virtuel reste dans le réseau virtuel. Tout autre trafic est envoyé par l’intermédiaire de l’équilibreur de charge pour les réseaux publics, ou si le tunneling forcé est utilisé, envoyé par l’intermédiaire de la passerelle VPN d’Azure. Si vous effectuez un dépannage, il est important de vous assurer que vous disposez de toutes les plages listées dans votre réseau Local que vous souhaitez envoyer par l’intermédiaire de la passerelle. Vérifiez que les plages d’adresses de réseau Local ne se chevauchent pas avec les plages d’adresses du réseau virtuel. Vous souhaitez également, vérifiez que le serveur DNS que vous utilisez est résout le nom de l’adresse IP appropriée.


## <a name="virtual-network-faq"></a>FAQ des réseaux virtuel

Vous permet d’afficher les informations de réseau virtuel supplémentaire dans le [Forum aux questions de réseau virtuel](../virtual-network/virtual-networks-faq.md).
 
