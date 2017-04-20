<properties
   pageTitle="Vue d’ensemble du réseau virtuel Azure (VNet)"
   description="Obtenir des informations sur les réseaux virtuels (VNets) dans Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="virtual-network-overview"></a>Vue d’ensemble du réseau virtuel

Un réseau virtuel Azure (VNet) est une représentation de votre propre réseau dans le nuage.  Il s’agit d’une isolation logique du nuage Azure dédié à votre abonnement. Vous pouvez contrôler totalement les paramètres blocs d’adresses IP, DNS, les stratégies de sécurité et des tables de routage au sein de ce réseau. Vous pouvez également segmenter votre VNet en sous-réseaux et lancer des machines virtuelles de Azure IaaS (VMs) et/ou des [services en nuage (instances de rôle PaaS)](../cloud-services/cloud-services-choose-me.md). En outre, vous pouvez vous connecter au réseau virtuel pour votre réseau local à l’aide d’une des [options de connectivité](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponibles dans Azure. Concrètement, vous pouvez développer votre réseau vers Azure, avec un contrôle complet sur les blocs d’adresses IP avec l’avantage de l’échelle de l’entreprise Qu'azure fournit.

Pour mieux comprendre VNets, examinons la figure ci-dessous, qui illustre un réseau simplifiée sur site.

![Réseau des locaux](./media/virtual-networks-overview/figure01.png)

La figure ci-dessus illustre un réseau local connecté à l’Internet public via un routeur. Vous pouvez également voir un pare-feu entre le routeur et d’un réseau de périmètre qui héberge un serveur DNS et une batterie de serveurs web. La batterie de serveurs web est à charge équilibrée à l’aide d’un équilibreur de charge matériel qui est exposé à Internet et consomme des ressources du sous-réseau interne. Le sous-réseau interne est séparé de la zone DMZ par un autre pare-feu et héberge des serveurs de contrôleur de domaine Active Directory, les serveurs de base de données et serveurs d’applications.

Le même réseau peut être hébergé dans Azure, comme illustré dans la figure ci-dessous.

![Azure réseau virtuel](./media/virtual-networks-overview/figure02.png)

Notez comment l’infrastructure Azure joue le rôle de routeur, autoriser l’accès à l’Internet public sans avoir besoin de toutes les configurations de votre VNet. Pare-feu peuvent être remplacés par des groupes de sécurité réseau (NSGs) appliqué à chaque sous-réseau individuel. Et équilibreurs de charge physiques sont remplacées par des équilibreurs de charge internet en vis-à-vis et interne dans Azure.

>[AZURE.NOTE] Il existe deux modes de déploiement dans Azure : standard (également appelé Service Management) et Azure Resource Manager (ARM). VNets classique pourrait ajouté à un groupe d’affinité ou créé sous la forme d’un VNet régional. Si vous avez un VNet dans un groupe d’affinité, il est recommandé du [faire migrer vers un VNet régional](virtual-networks-migrate-to-regional-vnet.md).

## <a name="virtual-network-benefits"></a>Avantages des réseaux virtuels

- **L’isolation**. VNets sont complètement isolés à partir d’une de l’autre. Qui vous permet de créer des réseaux disjoints de développement, de test et de production qui utilisent le même bloc d’adresse CIDR.

- **Accès à l’Internet public**. Tous les ordinateurs virtuels de IaaS et PaaS instances de rôle dans une VNet peuvent accéder à l’Internet public par défaut. Vous pouvez contrôler l’accès à l’aide de groupes de sécurité réseau (NSGs).

- **Accès aux ordinateurs virtuels dans le VNet**. Les instances de rôles PaaS et IaaS VM peuvent être lancé dans le même réseau virtuel, et ils peuvent se connecter à l’aide des adresses IP privées, même si elles sont dans des sous-réseaux différents sans avoir besoin de configurer une passerelle ou d’utiliser des adresses IP publiques.

- **Résolution de noms**. Azure fournit la résolution de noms interne pour IaaS VMs et instances de rôle de PaaS déployés dans votre VNet. Vous pouvez également déployer vos propres serveurs DNS et configurer le VNet pour les utiliser.

- **Sécurité**. Le trafic entrant et sortant des ordinateurs virtuels et des instances de rôle PaaS dans un VNet peut être contrôlé à l’aide de groupes de sécurité du réseau.

- **Connectivité**. VNets peut être connecté à l’aide des passerelles de réseau ou l’homologation de VNet. VNets peut être connecté à des centres de données sur site par le biais de réseaux VPN de site à site ou ExpressRoute d’Azure. Pour en savoir plus sur la connexion VPN de site à site, visitez [passerelles sur VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site). Pour en savoir plus sur ExpressRoute, consultez [vue d’ensemble technique de ExpressRoute](../expressroute/expressroute-introduction.md). Pour en savoir plus sur l’homologation de VNet, visitez le site [VNet homologation](virtual-network-peering-overview.md).

    >[AZURE.NOTE] Assurez-vous que vous créez un VNet avant de déployer toute IaaS VMs ou des instances de rôle PaaS à votre environnement Azure. ARM sur ordinateurs virtuels nécessitent une VNet, et si vous ne spécifiez pas un VNet existant, Azure crée un VNet qui peut avoir un conflit de bloc d’adresse CIDR avec votre réseau local par défaut. Qu’il soit impossible de vous connecter votre VNet à votre réseau local.

## <a name="subnets"></a>Sous-réseaux

Sous-réseau est une plage d’adresses IP dans le VNet, vous pouvez diviser un VNet en plusieurs sous-réseaux de l’organisation et de sécurité. Les ordinateurs virtuels et des instances de rôle PaaS déployés sur les sous-réseaux (identiques ou différents) au sein d’un VNet peuvent communiquer entre eux sans aucune configuration supplémentaire. Vous pouvez également configurer des tables de routage et NSGs à un sous-réseau.

## <a name="ip-addresses"></a>Adresses IP


Il existe deux types d’adresses IP assignées aux ressources dans Azure : *publics* et *privés*. Les adresses IP publiques permettent de ressources Azure communiquer avec Internet et autres services Web public Azure comme [Cache de Redis d’Azure](https://azure.microsoft.com/services/cache/), [Concentrateurs d’événement Azure](https://azure.microsoft.com/documentation/services/event-hubs/). Adresses IP privées permet la communication entre les ressources dans un réseau virtuel, ainsi que ceux connectés via un réseau privé virtuel, sans utiliser une adresse IP routable Internet les adresses.

Pour plus d’informations sur les adresses IP dans Azure, visitez [les adresses IP de réseau virtuel](virtual-network-ip-addresses-overview-arm.md)

## <a name="azure-load-balancers"></a>Équilibreurs de charge Azure

Machines virtuelles et services de cloud dans un réseau virtuel peuvent être exposées à Internet à l’aide des équilibreurs de charge d’Azure. Ligne d’applications d’entreprise qui sont interfaçage interne uniquement peut être charge équilibrée à l’aide d’équilibreur de charge interne.

- **Équilibreur de charge externe**. Vous pouvez utiliser un équilibreur de charge externe pour fournir une haute disponibilité pour les instances de rôle VM de IaaS et PaaS accédés à partir de l’Internet public.

- **L’équilibreur de charge interne**. Vous pouvez utiliser un équilibreur de charge interne pour offrir une haute disponibilité pour IaaS VMs et instances de rôle de PaaS accédés à partir d’autres services dans votre VNet.

Pour en savoir plus sur l’équilibrage de charge en Azure, consultez [Présentation de l’équilibrage de la charge](../load-balancer/load-balancer-overview.md).

## <a name="network-security-group-nsg"></a>Groupe de sécurité réseau (NSG)

NSGs pour contrôler l’accès entrant et sortant pour les interfaces réseau (cartes réseau), vous pouvez créer des ordinateurs virtuels et les sous-réseaux. Chaque NSG contient une ou plusieurs règles spécifiant si le trafic est acceptée ou refusé en fonction de l’adresse IP source, port source, adresse IP de destination et le port de destination. Pour en savoir plus sur NSGs, visitez [ce qu’est un groupe de sécurité du réseau](virtual-networks-nsg.md).

## <a name="virtual-appliances"></a>Boîtiers virtuels

Une appliance virtuelle est juste un autre VM dans votre VNet qui exécute une fonction de solution matérielle-logicielle en fonction des logiciels, des pare-feu, optimisation WAN, détection des intrusions. Vous pouvez créer un itinéraire dans Azure pour acheminer le trafic VNet grâce à une appliance virtuelle pour utiliser ses fonctionnalités.

Par exemple, NSGs peut servir à assurer la sécurité sur votre VNet. Cependant, NSGs fournit la couche 4 liste de contrôle d’accès (ACL) pour les paquets entrants et sortants. Si vous souhaitez utiliser un modèle de sécurité 7 couches, vous devez utiliser un pare-feu.

Boîtiers virtuels dépendent [d’itinéraires et transmission IP défini par l’utilisateur](virtual-networks-udr-overview.md).

## <a name="limits"></a>Limites
Il existe des limites sur le nombre de réseaux virtuels autorisés dans un abonnement, veuillez vous reporter aux [limites d’Azure réseau](../azure-subscription-service-limits.md#networking-limits) pour plus d’informations.

## <a name="pricing"></a>Tarification
Il est sans frais supplémentaires pour l’utilisation de réseaux virtuels dans Azure. Les instances de calcul lancés au sein de la Vnet seront débités les taux standard comme décrit dans [Azure VM de tarification](https://azure.microsoft.com/pricing/details/virtual-machines/). Les [Passerelles VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) et [des adresses IP publiques] (https://azure.microsoft.com/pricing/details/ip-addresses/) utilisé dans la VNet sera également chargée des taux standard.

## <a name="next-steps"></a>Étapes suivantes

- [Créer un VNet](virtual-networks-create-vnet-arm-pportal.md) et des sous-réseaux.
- [Créer un ordinateur virtuel dans un VNet](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Obtenir des informations sur les [NSGs](virtual-networks-nsg.md).
- Obtenir des informations sur [les itinéraires et transmission IP défini par l’utilisateur](virtual-networks-udr-overview.md).
