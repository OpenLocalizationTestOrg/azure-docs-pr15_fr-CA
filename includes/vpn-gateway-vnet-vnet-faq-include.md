- Les réseaux virtuels peuvent être dans les régions Azure identiques ou différentes (emplacements).

- Un service en nuage ou une point de terminaison d’équilibrage de la charge ne peut pas s’étendre sur des réseaux virtuels, même s’ils sont connectés entre eux.

- Relier plusieurs réseaux virtuels Azure ne nécessite pas les passerelles VPN de local à moins que la connectivité de coexistence n’est requise.

- VNet-à-VNet prend en charge la connexion de réseaux virtuels. Pas en charge les machines virtuelles qui se connecte ou ne pas dans un réseau virtuel, les services en nuage.

- VNet-à-VNet requiert des passerelles VPN d’Azure avec RouteBased (précédemment appelé routage dynamique) types VPN. 

- Connectivité de réseau virtuel peut être utilisée simultanément avec les réseaux privés virtuels sur plusieurs sites, avec un maximum de 10 (passerelles par défaut/Standard) ou 30 (haute Performance passerelles) VPN des tunnels de passerelle réseau virtuel VPN connectant aux deux autres réseaux virtuels ou sites locaux.

- Les espaces d’adressage des sites de réseau local virtuels local et les réseaux ne doivent pas se chevaucher. Chevauchement des espaces d’adressage entraîne la création de connexions de VNet de VNet échec.

- Tunnels redondants entre deux réseaux virtuels ne sont pas pris en charge.

- Tous les tunnels VPN du réseau virtuel partagent la bande passante disponible sur la passerelle VPN d’Azure et la disponibilité de passerelle VPN même SLA dans Azure.

- VNet-à-VNet trafic transite sur Microsoft Network, pas à Internet.

- Le trafic de VNet-à-VNet dans la même région est gratuit pour les deux directions ; le trafic entre la sortie de VNet à VNet région est chargé le taux de transfert de données inter-VNet sortants selon les régions du code source. Reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/vpn-gateway/) pour plus d’informations.