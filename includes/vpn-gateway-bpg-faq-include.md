### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP est pris en charge sur toutes les références de passerelle VPN Azure ?

Non, BGP est pris en charge sur les passerelles VPN **Standard** et **ultraperformante** Azure. **Base** Point de stock n’est pas pris en charge.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Puis-je utiliser BGP avec les passerelles VPN de Azure Policy-Based ?

Non, BGP est pris en charge uniquement les passerelles VPN basée sur un itinéraire.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Puis-je utiliser les APE privées (numéros de système autonome) ?

Oui, vous pouvez utiliser votre propre APE publics ou les APE privés pour votre des réseaux locaux et des réseaux virtuels Azure.

#### <a name="are-there-asns-reserved-by-azure"></a>Quelles sont les APE réservés par Azure ?

Oui, les APE suivants sont réservés par Azure pour peerings internes et externes :

- APE publics : 8075, 8076, 12076
- Les APE de privée : 65515, 65517, 65518, 65519, 65520

Vous ne pouvez pas spécifier ces les APE pour vos locaux sur les périphériques VPN lors de la connexion de passerelles VPN d’Azure.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Puis-je utiliser le même ASN pour les réseaux VPN sur site et Azure VNets ?

Non, vous devez affecter les APE différents entre vos réseaux locaux et de votre VNets Azure si vous les connectez avec BGP. Les passerelles VPN Azure ont par défaut ASN de 65515 affecté, si BGP est activé pour pas pour votre connectivity de coexistence. Vous pouvez substituer cette valeur par défaut en assignant un ASN différent lors de la création de la passerelle VPN, ou modifier l’ASN après création de la passerelle. Vous devez affecter votre APE sur site pour les passerelles réseau Local Azure correspondant.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Les préfixes d’adresses passerelles VPN d’Azure seront annonce pour moi ?

Passerelle VPN Azure s’annonce les itinéraires suivants à vos périphériques BGP sur site :

- Préfixes d’adresse votre VNet
- Les préfixes d’adresse pour chaque passerelles de réseau Local est connecté à la passerelle VPN d’Azure
- Itinéraires appris provenant des autres sessions d’homologation BGP connectées à la passerelle VPN d’Azure, **à l’exception de la gamme par défaut ou des itinéraires qui se chevauchent avec n’importe quel préfixe VNet**.

#### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Puis-je publier des itinéraire par défaut (0.0.0.0/0) pour les passerelles VPN d’Azure ?

Pas pour l’instant.

#### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Puis-je publier les préfixes exactes comme mon préfixes réseau virtuel ?

Non, les mêmes préfixes comme votre réseau virtuel de publicité les préfixes d’adresse seront bloqués ou filtrés par la plateforme Azure. Toutefois, vous pouvez annoncer un préfixe qui est un sur-ensemble des éléments à l’intérieur de votre réseau virtuel. Par exemple, votre réseau virtuel peut utiliser le 10.10.0.0/16 espace adresse et vous pouvez annoncer 10.0.0.0/8.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Puis-je utiliser BGP avec mes connexions VNet-à-VNet ?

Oui, vous pouvez utiliser BGP pour les connexions de coexistence et VNet-à-VNet.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Puis-je combiner BGP avec des connexions non-BGP pour mon passerelles VPN d’Azure ?

Oui, vous pouvez mélanger les BGP BGP non connexions et pour la même passerelle VPN d’Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Fait Azure VPN passerelle support BGP transit routage ?

Oui, l’acheminement de transit BGP est pris en charge, à l’exception que va de passerelles VPN d’Azure **pas** publie les routes par défaut des autres pairs BGP. Pour permettre l’acheminement de transit entre plusieurs passerelles VPN d’Azure, vous devez activer le protocole BGP sur toutes les connexions de VNet à VNet intermédiaires.

### <a name="can-i-have-more-than-one-tunnels-between-azure-vpn-gateway-and-my-on-premises-network"></a>Puis-je avoir plusieurs tunnels entre la passerelle VPN d’Azure et mon réseau local ?

Oui, vous pouvez établir plusieurs tunnels VPN de S2S entre une passerelle VPN d’Azure et votre réseau local. Veuillez noter que toutes ces tunnels seront imputées sur le nombre total de tunnels pour vos passerelles VPN d’Azure. Par exemple, si vous avez deux tunnels redondants entre votre passerelle VPN d’Azure et une de votre réseau local, elles consomment de 2 tunnels sur le quota total pour votre passerelle VPN d’Azure (10 standard) et 30 pour ultraperformante.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Puis-je avoir plusieurs tunnels entre deux VNets Azure avec BGP ?

Non, les tunnels redondants entre deux réseaux virtuels ne sont pas pris en charge.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Puis-je utiliser BGP pour S2S VPN dans une configuration de coexistence de ExpressRoute/S2S VPN ?

Pas pour l’instant.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Quelle adresse de passerelle VPN de Azure utilise-t-il pour BGP homologue IP ?

La passerelle VPN d’Azure alloue une adresse IP unique à partir de la plage de GatewaySubnet définie pour le réseau virtuel. Par défaut, il s’agit de la dernière seconde adresse de la plage. Par exemple, si votre GatewaySubnet est 10.12.255.0/27, allant de 10.12.255.0 à 10.12.255.31, puis l’adresse BGP homologue IP de la passerelle VPN d’Azure sera 10.12.255.30. Vous pouvez trouver ces informations lorsque vous répertoriez les informations de passerelle VPN d’Azure.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quelles sont les conditions requises pour les adresses IP d’homologue BGP sur mon périphérique VPN ?

Votre BGP sur site homologue adresse **Ne doit pas** être le même que l’adresse IP publique de votre périphérique VPN. Pour votre adresse IP homologue de BGP, utilisez une adresse IP différente sur l’appareil VPN. Il peut être une adresse affectée à l’interface de bouclage sur le périphérique. Spécifiez cette adresse de la passerelle réseau locale correspondante représentant l’emplacement.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Que dois-je spécifier en tant que préfixes de mon adresse de la passerelle du réseau Local lorsque vous utilisez le protocole BGP ?

Passerelle de réseau Local Azure spécifie les préfixes d’adresse initiale pour le réseau local. Avec le protocole BGP, vous devez allouer le préfixe de l’hôte (/ 32 préfixe) de votre adresse IP homologue de BGP en l’espace d’adressage pour ce réseau local. Si votre adresse IP homologue de BGP est 10.52.255.254, vous devez spécifier « 10.52.255.254/32 » comme le localNetworkAddressSpace de la passerelle réseau Local représentant ce réseau local. C’est pour vous assurer que la passerelle VPN d’Azure établit la session BGP via le tunnel VPN de S2S.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Que dois-je ajouter à mon appareil VPN sur site pour la session d’homologation BGP ?

Vous devez ajouter un itinéraire d’hôte de l’adresse IP d’homologue Azure BGP sur votre périphérique VPN pointant vers le tunnel IPsec S2S VPN. Par exemple, si l’adresse IP de Azure VPN Peer est « 10.12.255.30 », vous devez ajouter un itinéraire hôte pour « 10.12.255.30 » avec une interface de saut suivant de l’interface de tunnel IPsec correspondante sur l’appareil VPN.
