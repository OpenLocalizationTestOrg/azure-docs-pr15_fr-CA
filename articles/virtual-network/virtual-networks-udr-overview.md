<properties 
   pageTitle="Quels sont les itinéraires définis par l’utilisateur et le routage IP ?"
   description="Apprenez à utiliser des itinéraires de défini par l’utilisateur (UDR) et le routage IP pour transférer le trafic de réseau virtuel dans Azure."
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

# <a name="what-are-user-defined-routes-and-ip-forwarding"></a>Quels sont les itinéraires définis par l’utilisateur et le routage IP ?
Lorsque vous ajoutez des machines virtuelles (VM) à un réseau virtuel (VNet) dans Azure, vous remarquerez que les ordinateurs virtuels sont en mesure de communiquer entre eux sur le réseau, automatiquement. Vous n’avez pas besoin spécifier une passerelle, même si les ordinateurs virtuels sont dans des sous-réseaux différents. Cela vaut pour la communication entre les ordinateurs virtuels et l’Internet public, et même à vos locaux réseau lorsqu’une connexion hybride d’Azure dans votre propre centre de données est présent.

Ce flux de communication est possible car Azure utilise une série d’itinéraires du système pour définir le flux de trafic IP. Itinéraires de système contrôlent le flux de communication dans les scénarios suivants :

- À partir de dans le même sous-réseau.
- À partir d’un sous-réseau à un autre dans une VNet.
- À partir d’ordinateurs virtuels à Internet.
- À partir d’un VNet à un autre VNet via une passerelle VPN.
- À partir d’un VNet à votre réseau local via une passerelle VPN.

La figure ci-dessous illustre une configuration simple avec un VNet et deux sous-réseaux quelques VMs, ainsi que les itinéraires de système qui autorisent le trafic IP.

![Itinéraires de système dans Azure](./media/virtual-networks-udr-overview/Figure1.png)

Bien que l’utilisation d’itinéraires du système facilite le trafic automatiquement pour votre déploiement, il existe des cas dans lesquels vous voulez contrôler le routage des paquets via l’appliance virtuelle. Vous pouvez ainsi en créant des itinéraires définis par l’utilisateur qui spécifier le saut suivant pour les paquets circulant à un sous-réseau spécifique pour accéder à votre appliance virtuelle au lieu de cela et activer le transfert IP pour l’ordinateur virtuel en cours d’exécution en tant que l’appliance virtuelle.

La figure ci-dessous montre un exemple de parcours de défini par l’utilisateur et le transfert pour forcer les paquets IP envoyés à un sous-réseau à partir d’une autre à passer par une appliance virtuelle sur un troisième sous-réseau.

![Itinéraires de système dans Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT] Les itinéraires définis par l’utilisateur sont appliquent uniquement au trafic qui quitte un sous-réseau. Vous ne pouvez pas créer des itinéraires pour spécifier la manière dont le trafic est sur un sous-réseau à partir d’Internet, par exemple. En outre, la solution matérielle-logicielle que vous transférez le trafic ne peut pas être dans le même sous-réseau que celui où le trafic provient. Toujours créer un sous-réseau distinct pour vos applications. 

## <a name="route-resource"></a>Ressource de l’itinéraire
Les paquets sont routés via un réseau TCP/IP basé sur une table d’itinéraires définie sur chaque nœud sur le réseau physique. Une table de routage est une collection d’itinéraires individuels utilisé pour décider de l’emplacement transférer les paquets en fonction de l’adresse IP de destination. Une gamme comprend les éléments suivants :

|Propriété|Description|Contraintes|Considérations relatives à la|
|---|---|---|---|
| Préfixe d’adresse | Le CIDR de destination auquel l’itinéraire s’applique, tel que 10.1.0.0/16.|Doit être une plage valide de CIDR représentant les adresses sur l’Internet public, Azure réseau virtuel ou centre de données sur site.|Assurez-vous que le **préfixe d’adresse** ne contient pas l’adresse pour l' **adresse du tronçon suivant**, sinon vos paquets entrera dans une boucle allant de la source jusqu’au saut suivant sans jamais atteindre la destination. |
| Type du saut suivant | Le type de saut Azure le paquet doit être envoyé à. | Doit être une des valeurs suivantes : <br/> **Réseau virtuel**. Représente le réseau virtuel local. Par exemple, si vous avez deux sous-réseaux, 10.1.0.0/16 et 10.2.0.0/16 dans le même réseau virtuel, l’itinéraire de chaque sous-réseau de la table de routage a une valeur de saut suivante de *Réseau virtuel*. <br/> **Passerelle réseau virtuel**. Représente une passerelle VPN S2S Azure. <br/> **Internet**. Représente la passerelle Internet par défaut fournie par l’Infrastructure d’Azure. <br/> **Appliance virtuelle**. Représente une appliance virtuelle que vous avez ajouté à votre réseau virtuel Azure. <br/> **Aucun**. Représente un trou noir. Paquets transmis à un trou noir ne seront pas du tout transmises.| Envisager d’utiliser un type **Aucun** arrêt des paquets de transiter vers une destination donnée. | 
| Adresse du tronçon suivant | L’adresse de tronçon suivant contient l’adresse IP paquets doivent être transférées. Les valeurs de tronçon suivant ne sont autorisées que dans les gammes où le type du saut suivant est *Appliance virtuelle*.| Doit être une adresse IP qui est accessible dans le réseau virtuel dans lequel l’itinéraire défini par l’utilisateur est appliquée. | Si l’adresse IP représente un ordinateur virtuel, assurez-vous que vous activez [le routage IP](#IP-forwarding) dans Azure pour la machine virtuelle. |

Dans PowerShell d’Azure, certaines valeurs de « NextHopType » ont des noms différents :
- Réseau virtuel est VnetLocal
- Passerelle de réseau virtuel est VirtualNetworkGateway
- Appliance virtuelle est VirtualAppliance
- Internet est Internet
- Aucun est None

### <a name="system-routes"></a>Itinéraires de système
Chaque sous-réseau créé dans un réseau virtuel est automatiquement associée à une table de routage qui contient les règles de routage système suivantes :

- **Règle de Vnet local**: cette règle est automatiquement créée pour chaque sous-réseau dans un réseau virtuel. Il indique qu’il existe un lien direct entre les ordinateurs virtuels dans le VNet et qu’il n’y a aucun saut suivant intermédiaire.
- **Sur site règle**: cette règle s’applique à tout le trafic destiné à la plage d’adresses sur site et qu’il utilise la passerelle VPN comme la destination du saut suivante.
- **Internet règle**: cette règle les handles tout le trafic destiné à l’Internet public (préfixe d’adresse 0.0.0.0/0) et utilise la passerelle internet infrastructure comme tronçon suivant pour tout le trafic destiné à Internet.

### <a name="user-defined-routes"></a>Itinéraires définis par l’utilisateur
Pour la plupart des environnements, vous devez uniquement les itinéraires de système déjà définis par Azure. Toutefois, vous devrez créer une table d’itinéraires et ajoutez un ou plusieurs itinéraires dans des cas spécifiques, par exemple :

- Forcer le tunneling à Internet via votre réseau local.
- Utilisation de boîtiers virtuels dans votre environnement Azure.

Dans les scénarios ci-dessus, vous devez créer une table d’itinéraires et de lui ajouter des itinéraires définis par l’utilisateur. Vous pouvez avoir plusieurs tables de routage et la table de routage même peut être associée à un ou plusieurs sous-réseaux. Et chaque sous-réseau peut uniquement être associé à une table de routage unique. Tous les ordinateurs virtuels et les services en nuage dans une utilisation de sous-réseau la table de routage associée à ce sous-réseau.

Sous-réseaux s’appuient sur les itinéraires du système jusqu'à ce qu’une table de routage est associée au sous-réseau. Dès qu’une association existe, routage est effectué en fonction de la plus longue du préfixe correspondance (LPM) parmi les itinéraires définis par l’utilisateur et de routes de système. S’il existe plusieurs itinéraires avec la même correspondance LPM un itinéraire est sélectionné en fonction de son origine dans l’ordre suivant :

1. Itinéraire défini d’utilisateur
1. Routage BGP (lorsque ExpressRoute est utilisé)
1. Itinéraire de système

Pour apprendre à créer des itinéraires définis par l’utilisateur, reportez-vous à la section [comment créer des itinéraires et activer le routage IP dans Azure](virtual-network-create-udr-arm-template.md).

>[AZURE.IMPORTANT] Les itinéraires définis par l’utilisateur sont appliquent uniquement aux services des ordinateurs virtuels d’Azure et le nuage. Par exemple, si vous souhaitez ajouter une appliance virtuelle de pare-feu entre votre réseau de locaux et la Azure, vous devrez créer un itinéraire défini par l’utilisateur pour les tables de votre itinéraire Azure qui transmet tout le trafic à l’espace d’adressage sur site à l’appliance virtuelle. Vous pouvez également ajouter un itinéraire défini par l’utilisateur (UDR) pour le GatewaySubnet pour transférer le trafic de tous les locaux vers Azure par le biais de l’appliance virtuelle. Il s’agit d’un ajout récent.

### <a name="bgp-routes"></a>Itinéraires BGP
Si vous disposez d’une connexion ExpressRoute entre votre réseau de locaux et la Azure, vous pouvez activer BGP propager les itinéraires à partir de votre réseau local vers Azure. Ces itinéraires BGP sont utilisés de la même manière que les itinéraires de système et les itinéraires définis par l’utilisateur dans chaque sous-réseau Azure. Pour plus d’informations, consultez [Présentation de l’ExpressRoute](../expressroute/expressroute-introduction.md).

>[AZURE.IMPORTANT] Vous pouvez configurer votre environnement Azure pour utiliser force tunneling via votre réseau local, en créant un itinéraire défini d’utilisateur pour 0.0.0.0/0 de sous-réseau qui utilise la passerelle VPN comme tronçon suivant. Toutefois, cela ne fonctionne que si vous utilisez une passerelle VPN, pas ExpressRoute. Pour ExpressRoute, le tunneling forcé est configuré via BGP.

## <a name="ip-forwarding"></a>Transfert IP
Comme décrit ci-dessus, une des principales raisons pour créer un itinéraire défini utilisateur doit transférer le trafic vers l’appliance virtuelle. Une appliance virtuelle n’est rien de plus qu’un ordinateur virtuel qui exécute une application permet de gérer le trafic réseau d’une certaine manière, par exemple un pare-feu ou un périphérique NAT.

Cette appliance virtuelle VM doit être capable de recevoir le trafic entrant qui n’est pas adressé à lui-même. Pour permettre à un ordinateur virtuel recevoir le trafic adressé vers d’autres destinations, vous devez activer le routage IP de la machine virtuelle. Il s’agit d’un paramètre, pas un système d’exploitation invité de Azure.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer des itinéraires dans le modèle de déploiement du Gestionnaire de ressources](virtual-network-create-udr-arm-template.md) et de les associer à des sous-réseaux. 
- Découvrez comment [créer des itinéraires dans le modèle de déploiement classiques](virtual-network-create-udr-classic-ps.md) et de les associer à des sous-réseaux.
