## <a name="virtual-network-basics"></a>Notions de base de réseau virtuel

### <a name="what-is-an-azure-virtual-network-vnet"></a>Ce qu’est un réseau virtuel Azure (VNet) ?

Vous pouvez utiliser VNets pour provisionner et gérer des réseaux privés virtuels (VPN) dans Azure et, vous pouvez également lier la VNets avec les autres VNets dans Azure, ou vos locaux infrastructure informatique pour créer des solutions hybride ou de coexistence. Chaque VNet que vous créez possède son propre bloc CIDR et peut être lié à d’autres réseaux VNets et sur site, dans la mesure où les blocs CIDR éviter toute collision. Vous avez également les contrôles des paramètres de serveur DNS de VNets et la segmentation de la VNet en sous-réseaux.

Utilisez VNets pour :

- Créer un réseau privé dédié uniquement nuage virtuel

    Parfois vous ne nécessitent pas une configuration de la coexistence de votre solution. Lorsque vous créez un VNet, vos services et les ordinateurs virtuels dans votre VNet peuvent communiquer directement et en toute sécurité entre eux dans le nuage. Cela conserve le trafic en toute sécurité au sein de la VNet, mais toujours vous permet de configurer des connexions de point de terminaison pour les ordinateurs virtuels et les services qui requièrent une communication Internet dans le cadre de votre solution.

- Étendre en toute sécurité votre centre de données

    Avec VNets, vous pouvez générer traditionnels VPN (S2S) de site à site pour mettre à l’échelle la capacité de votre datacenter en toute sécurité. S2S VPN utilisent IPSEC pour fournir une connexion sécurisée entre votre passerelle VPN d’entreprise et les Azure.

- Activer les scénarios de cloud hybride

    VNets vous donnent la possibilité de prendre en charge une variété de scénarios de nuage hybride. Vous pouvez connecter en toute sécurité des applications basées sur le nuage de n’importe quel type de système locaux tels que les mainframes et les systèmes Unix.

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>Comment savoir si j’ai besoin d’un réseau virtuel ?

Consultez la [Vue d’ensemble de réseau virtuel](../articles/virtual-network/virtual-networks-overview.md) pour afficher une table de décision qui vous aidera à décider de la meilleure option de conception de réseau pour vous.

### <a name="how-do-i-get-started"></a>Comment commencer ?

Consultez [la documentation de réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) pour commencer. Cette page comporte des liens vers les communs des étapes de configuration ainsi que des informations qui vous aideront à comprendre les choses que vous devez prendre en considération lors de la conception de votre réseau virtuel.

### <a name="what-services-can-i-use-with-vnets"></a>Quels services puis-je utiliser avec VNets ?

VNets peut être utilisé avec une variété de différents services Azure, tels que les Services en nuage (PaaS), les ordinateurs virtuels et les applications Web. Toutefois, il y a quelques services qui ne sont pas pris en charge sur un VNet. Veuillez vérifier le service spécifique que vous souhaitez utiliser et assurez-vous qu’il est compatible.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Puis-je utiliser VNets sans connectivité de coexistence ?

Oui. Vous pouvez utiliser un VNet sans utiliser de connexion de site à site. Ceci est particulièrement utile si vous souhaitez exécuter les contrôleurs de domaine et les batteries de serveurs SharePoint dans Azure.

## <a name="virtual-network-configuration"></a>Configuration du réseau virtuel

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quels outils utiliser pour créer un VNet ?

Vous pouvez utiliser les outils suivants pour créer ou configurer un réseau virtuel :

- Azure Portal (pour classique et Gestionnaire de ressources VNets).

- Un fichier de configuration de réseau (netcfg - pour VNets classique uniquement). Reportez-vous à la section [configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](../articles/virtual-network/virtual-networks-using-network-configuration-file.md).

- PowerShell (pour classique et Gestionnaire de ressources VNets).

- CLI Azure (pour classique et Gestionnaire de ressources VNets).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Les plages d’adresses puis-je utiliser dans mon VNets ?

Vous pouvez utiliser des plages d’adresses IP publiques et une plage d’adresses IP défini dans [RFC 1918](http://tools.ietf.org/html/rfc1918).

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Puis-je avoir des adresses IP publiques dans mon VNets ?

Oui. Pour plus d’informations sur les plages d’adresses IP publiques, consultez [l’espace d’adressage IP publique dans un réseau virtuel (VNet)](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md). Gardez à l’esprit que votre solution IPs publics ne sera pas directement accessible à partir d’Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>Y a-t-il une limite au nombre de sous-réseaux dans mon réseau virtuel ?

Il n’y a aucune limite sur le nombre de sous-réseaux que vous utilisez dans un VNet. Tous les sous-réseaux doivent être entièrement contenus dans l’espace d’adressage de réseau virtuel et ne doivent pas se chevaucher entre eux.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?

Azure réserve à certaines adresses IP dans chaque sous-réseau. Les adresses IP et prénom des sous-réseaux sont réservés pour la conformité du protocole, ainsi que des adresses plus 3 utilisée pour les services Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Comment la petite et la grande peuvent VNets et sous-réseaux être ?

Le plus petit sous-réseau que nous prenons en charge est un /29 et le plus grand est un /8 (à l’aide de définitions de sous-réseau CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Puis-je emporter mon VLAN sur Azure à l’aide de VNets ?

N° VNets sont les incrustations de couche 3. Azure ne supporte pas la sémantique quelconque Layer-2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Puis-je spécifier des stratégies de routage personnalisés sur mon VNets et les sous-réseaux ?

Oui. Vous pouvez utiliser le routage défini par l’utilisateur (UDR). Pour plus d’informations sur UDR, visitez [les itinéraires définis par l’utilisateur et le routage IP](../articles/virtual-network/virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNets prennent en charge de multidiffusion ou de diffusion ?

N° Nous ne prennent pas en charge multidiffusion ou de diffusion.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quels protocoles puis-je utiliser dans VNets ?

Vous pouvez utiliser des protocoles basés sur IP standard dans VNets. Cependant, les paquets encapsulés IP-dans-IP, diffusion, multidiffusion et l’Encapsulation GRE (Generic Routing) sont bloqués dans VNets. Les protocoles standard de travail incluent :

- TCP
- UDP
- ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Est-il possible de ping mon routeurs par défaut dans un VNet ?

N°

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Puis-je utiliser tracert pour diagnostiquer la connectivité ?

N°

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Puis-je ajouter des sous-réseaux après avoir créé le VNet ?

Oui. Sous-réseaux peuvent être ajoutés à la VNets à tout moment, tant que l’adresse de sous-réseau n’est pas partie d’un autre sous-réseau dans le VNet.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Puis-je modifier la taille de mon sous-réseau après que j’ai créé ?

Vous pouvez ajouter, supprimer, développer ou réduire un sous-réseau si aucun ordinateurs virtuels ou les services déployés au sein de celui-ci à l’aide des applets de commande PowerShell ou le fichier NETCFG. Vous pouvez également ajouter, supprimer, développer ou réduire tous les préfixes dans la mesure où les sous-réseaux qui contiennent des ordinateurs virtuels ou les services ne sont pas affectés par la modification.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Puis-je modifier des sous-réseaux après la création de leur ?

Oui. Vous pouvez ajouter, supprimer et modifier les blocs CIDR utilisés par une VNet.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>Puis-je me connecter à internet si j’exécute mes services dans un VNet ?

Oui. Tous les services déployés au sein d’un VNet peuvent se connecter à internet. Chaque service en nuage déployé dans Azure a un VIP publiquement adressable qui lui est assigné. Vous devrez définir des points de terminaison d’entrée pour les rôles de PaaS et points de terminaison pour les machines virtuelles activer ces services d’accepter les connexions provenant d’internet.

### <a name="do-vnets-support-ipv6"></a>VNets prennent en charge IPv6 ?

N° Vous ne pouvez pas utiliser IPv6 avec VNets pour l’instant.

### <a name="can-a-vnet-span-regions"></a>Un VNet peut s’étendre sur les régions ?

N° Un VNet est limité à une seule région.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Puis-je connecter un VNet à un autre VNet dans Azure ?

Oui. Vous pouvez créer des VNet pour VNet la communication à l’aide des API de repos ou Windows PowerShell. Vous pouvez également vous connecter à VNets via VNet homologation. Voir plus de détails sur l’homologation [ici.](../articles/virtual-network/virtual-network-peering-overview.md)

## <a name="name-resolution-dns"></a>Résolution de nom (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quelles sont mes options de DNS pour VNets ?

Consultez le tableau dans la page de [Résolution de noms pour les ordinateurs virtuels et les Instances de rôles](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) pour vous guider tout au long de toutes les options de DNS disponibles.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Puis-je spécifier des serveurs DNS pour un VNet ?

Oui. Vous pouvez spécifier des adresses IP de serveur DNS dans les paramètres VNet. Cela sera appliqué en tant que l’ou les serveurs DNS par défaut pour tous les ordinateurs virtuels dans le VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Le nombre de serveurs DNS puis-je spécifier ?

Vous pouvez spécifier jusqu'à 12 serveurs DNS.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Puis-je modifier mes serveurs DNS une fois que j’ai créé le réseau ?

Oui. Vous pouvez modifier la liste des serveurs DNS de votre VNet à tout moment. Si vous modifiez votre liste de serveurs DNS, vous devez redémarrer chacun des ordinateurs virtuels dans votre VNet de façon à sélectionner le nouveau serveur DNS.


### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Nouveautés de DNS fournies par Azure et fonctionne-t-il avec VNets ?

DNS Azure fourni est mutualisée DNS proposé par Microsoft. Azure enregistre tous vos ordinateurs virtuels et les instances de rôles dans ce service. Ce service fournit la résolution de noms par le nom d’hôte pour les ordinateurs virtuels et les instances de rôles contenus dans le même service de cloud et par nom de domaine complet pour les machines virtuelles et d’instances de rôle dans le même VNet.

> [AZURE.NOTE] Il existe une limitation pour l’instant aux services 100 cloud première dans le réseau virtuel cross-locataire résolution de noms à l’aide de DNS Azure fourni. Si vous n’utilisez pas votre propre serveur DNS, cette limitation ne s’applique pas.

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>Puis-je remplacer mes paramètres DNS sur une VM par / base de service ?

Oui. Vous pouvez configurer des serveurs DNS sur une base de service par-nuage pour remplacer les paramètres de réseau par défaut. Toutefois, nous vous recommandons d’utiliser autant que possible à l’échelle du réseau DNS.

### <a name="can-i-bring-my-own-dns-suffix"></a>Est-il possible de mettre mon propre suffixe DNS ?

N° Vous ne pouvez pas spécifier un suffixe DNS personnalisé pour votre VNets.

## <a name="vnets-and-vms"></a>VNets et les machines virtuelles

### <a name="can-i-deploy-vms-to-a-vnet"></a>Puis-je déployer des machines virtuelles à un VNet ?

Oui.

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>Puis-je déployer des machines virtuelles de Linux à un VNet ?

Oui. Vous pouvez déployer des distro de Linux prises en charge par Azure.

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>Quelle est la différence entre une adresse IP virtuelle publique et une adresse IP interne ?

- Une adresse IP interne est une adresse IP qui est attribuée à chaque ordinateur virtuel dans un VNet par DHCP. Il n’est pas avec accès public. Si vous avez créé un VNet, l’adresse IP interne est affecté à partir de la plage que vous avez spécifié dans les paramètres de sous-réseau de votre VNet. Si vous ne disposez pas d’une VNet, une adresse IP interne sera tout de même être attribuée. L’adresse IP interne restera avec la machine virtuelle pendant sa durée de vie, sauf que la machine virtuelle est désallouée.

- Une adresse IP virtuelle publique est l’adresse IP publique affectée à votre équilibreur de charge ou de service cloud. Il n’est pas affectée directement à votre NIC. VM L’adresse IP virtuelle reste avec le service en nuage qu'il est affecté jusqu'à ce que tous les ordinateurs virtuels en service en nuage sont libérés ou supprimés. À ce stade, il est libéré.

### <a name="what-ip-address-will-my-vm-receive"></a>Quelle adresse IP reçoit mon ordinateur virtuel ?

- **Adresse IP interne :** Si vous déployez un ordinateur virtuel à un VNet, l’ordinateur virtuel reçoit une adresse IP interne à partir d’un pool d’adresses IP internes que vous spécifiez. Ordinateurs virtuels communiquent au sein de la VNets à l’aide des adresses IP internes. Bien que Azure affecte une adresse IP interne de dynamique, vous pouvez demander une adresse statique pour votre machine virtuelle. Pour en savoir plus sur les adresses IP internes statiques, consultez [comment définir un IP interne statique](../articles/virtual-network/virtual-networks-reserved-private-ip.md).

- **VIP-** Votre machine virtuelle est également associé à une adresse IP virtuelle, même si une adresse IP virtuelle n’est jamais assigné directement à la machine virtuelle. Une adresse VIP est une adresse IP publique qui peut être affectée à votre service cloud. Vous pouvez, le cas échéant, réserver un VIP pour votre service cloud.

- **ILPIP-** Vous pouvez également configurer une adresse IP publique au niveau de l’instance (ILPIP). ILPIPs sont directement associées à la machine virtuelle, plutôt que le service en nuage. Pour en savoir plus sur ILPIPs, consultez [Vue d’ensemble de IP publique au niveau de l’Instance](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Puis-je réserver une adresse IP interne d’un ordinateur virtuel qui va créer ultérieurement ?

N° Vous ne pouvez pas réserver une adresse IP interne. Si une adresse IP interne est disponible lui est affecté par le serveur DHCP à une instance de rôle ou de la machine virtuelle. Cet ordinateur virtuel peut être ou non celui que vous souhaitez que l’adresse IP interne à affecter à. Vous pouvez, cependant, modifier l’adresse IP interne d’un ordinateur virtuel déjà créé pour toutes les adresses IP internes disponibles.

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>Modification d’adresses IP interne pour les machines virtuelles dans un VNet de faire ?

Oui. Les adresses IP internes restent avec la machine virtuelle pour sa durée de vie, sauf si la machine virtuelle est désallouée. Lorsqu’une machine virtuelle est libérée, l’adresse IP interne est publié, sauf si vous avez défini une adresse IP interne statique pour votre machine virtuelle. Si la machine virtuelle est simplement arrêtée (et pas mettre dans l’état **arrêté (Deallocated)**) l’adresse IP reste assignée à la machine virtuelle.

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>Puis-je attribuer manuellement des adresses IP aux cartes réseau de machines virtuelles ?

N° Vous ne devez pas modifier les propriétés de l’interface de machines virtuelles. Toute modification peut entraîner la perte potentielle de connectivité de la machine virtuelle.

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>Que se passe-t-il à mes adresses IP arrêter une machine virtuelle ?

Rien. Les adresses IP (adresse IP interne et publique VIP) reste avec votre service cloud ou votre machine virtuelle.

> [AZURE.NOTE] Si vous souhaitez simplement arrêter l’ordinateur virtuel, n’utilisez pas le portail de gestion pour le faire. Actuellement, le bouton d’arrêt va libérer l’ordinateur virtuel.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>Déplacement de machines virtuelles à partir d’un sous-réseau à un autre sous-réseau dans un VNet sans redéployer ?

Oui. Vous trouverez plus d’informations [ici](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Puis-je configurer une adresse MAC pour mon ordinateur virtuel ?

N° Une adresse MAC ne peut pas être configurée de manière statique.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>L’adresse MAC reste la même pour mon ordinateur virtuel une fois qu’il a été créé ?

Oui, l’adresse MAC est identique pour un ordinateur virtuel même si l’ordinateur virtuel a été arrêté (libérés) et relancé.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Puis-je me connecter à internet à partir d’un ordinateur virtuel dans un VNet ?

Oui. Tous les services déployés au sein d’un VNet peuvent se connecter à Internet. En outre, chaque service en nuage déployé dans Azure a un VIP publiquement adressable qui lui est assigné. Vous devez définir des points de terminaison d’entrée pour les rôles de PaaS et points de terminaison pour les machines virtuelles activer ces services d’accepter les connexions provenant d’Internet.

## <a name="vnets-and-services"></a>VNets et Services

### <a name="what-services-can-i-use-with-vnets"></a>Quels services puis-je utiliser avec VNets ?

Vous ne pouvez utiliser que les services compute dans VNets. Calcul sont limités aux ordinateurs virtuels et des Services en nuage (rôles web et worker).

### <a name="can-i-use-web-apps-with-virtual-network"></a>Puis-je utiliser des applications Web avec le réseau virtuel ?

Oui. Vous pouvez déployer des applications Web à l’intérieur d’un VNet avec ASE (environnement de Service d’application). Ajouter à ceci, les applications Web peuvent en toute sécurité se connecter et accéder à des ressources dans votre VNet Azure si vous avez configuré pour votre VNet point-à-site. Pour plus d’informations, consultez les rubriques suivantes :


- [Création d’applications Web dans un environnement de Service d’application](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)

- [Intégration de réseau virtuel d’applications Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [À l’aide de VNet intégration et hybride connexions avec les applications Web](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [Intégrer une application web avec un réseau virtuel Azure](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Puis-je déployer des services de nuage avec les rôles web et worker (PaaS) dans un VNet ?

Oui. Vous pouvez déployer des services PaaS dans VNets.

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>Comment déployer des rôles de PaaS à un VNet ?

Vous pouvez y parvenir en spécifiant le nom de VNet et les mappages de /subnet du rôle dans la section de configuration de réseau de la configuration de votre service. Vous n’avez pas besoin de mettre à jour tous vos fichiers binaires.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Puis-je déplacer mes services et VNets ?

N° Vous ne pouvez pas déplacer des services et VNets. Vous devez supprimer et redéployer le service pour le déplacer vers un autre VNet.

## <a name="vnets-and-security"></a>VNets et sécurité

### <a name="what-is-the-security-model-for-vnets"></a>Quel est le modèle de sécurité pour VNets ?

VNets sont complètement isolées une de l’autre et d’autres services hébergés dans l’infrastructure Azure. Un VNet est une limite de confiance.

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>Puis-je définir des ACL ou NSGs sur mon VNets ?

N° Vous ne pouvez pas associer des ACL ou NSGs à VNets. Toutefois, les ACL peut être définies sur des points de terminaison d’entrée pour les machines virtuelles qui ont été déployés sur un VNets, et NSGs peuvent être associés à des sous-réseaux ou des cartes réseau.

### <a name="is-there-a-vnet-security-whitepaper"></a>Y a-t-il un livre blanc sécurité de VNet ?

Oui. Vous pouvez le télécharger [ici](http://go.microsoft.com/fwlink/?LinkId=386611).

## <a name="apis-schemas-and-tools"></a>API, des schémas et des outils

### <a name="can-i-manage-vnets-from-code"></a>Puis-je gérer VNets à partir du code ?

Oui. Vous pouvez utiliser les autres API pour gérer la connectivité des VNets et de coexistence. Vous trouverez plus d’informations [ici](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>Existe-t-il une prise en charge de l’outillage de VNets ?

Oui. Vous pouvez utiliser les outils de PowerShell et de ligne de commande pour une variété de plates-formes. Vous trouverez plus d’informations [ici](http://go.microsoft.com/fwlink/?LinkId=317721).
