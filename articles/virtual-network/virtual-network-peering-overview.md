
<properties
   pageTitle="Azure réseau virtuel peering | Microsoft Azure"
   description="Obtenir des informations sur les VNet homologation dans Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="narayan" />

# <a name="vnet-peering"></a>Homologation de VNet

Homologation de VNet est un mécanisme qui connecte deux réseaux virtuels (VNets) dans la même région à travers le réseau principal d’Azure. Une fois que ressources les deux réseaux virtuels apparaissent sous forme d’une à toutes fins de connectivité. Ils sont toujours gérés comme des ressources distinctes, mais les ordinateurs virtuels de ces réseaux virtuels peuvent communiquer entre eux directement à l’aide des adresses IP privées.

Le trafic entre les ordinateurs virtuels dans les ressources réseaux virtuels est acheminé via l’infrastructure Azure de la même manière que le trafic est acheminé entre les ordinateurs virtuels sur le même réseau virtuel. Parmi les avantages de l’utilisation de l’homologation de VNet :

- Une connexion de faible latence, bande passante élevée entre les ressources dans des réseaux virtuels différents.
- La possibilité d’utiliser des ressources telles que des équipements réseau et les passerelles VPN en tant que points de transit dans un VNet de ressources.
- La possibilité de se connecter à un réseau virtuel qui utilise le modèle de gestionnaire de ressources Azure à un réseau virtuel qui utilise le modèle de déploiement classiques et activer une connectivité totale entre les ressources dans ces réseaux virtuels.

Configuration requise et les aspects clés de l’homologation de VNet :

- Deux réseaux virtuels sont ressources doivent être dans la même région d’Azure.
- Les réseaux virtuels sont ressources doivent avoir des espaces d’adressage IP sans chevauchement.
- Homologation de VNet est entre deux réseaux virtuels, et il n’y a aucune relation transitive dérivée. Par exemple, si le réseau virtuel est ressources réseau virtuel B et si B de réseau virtuel est ressources réseau virtuel C, il ne traduit pas virtuel à réseau une ressources en cours avec le réseau virtuel C.
- Homologation peut être établie entre des réseaux virtuels dans les deux abonnements différents comme la durée pendant laquelle un utilisateur privilégié de deux abonnements autorise l’homologation et les abonnements sont associés au même locataire Active Directory. 
- Homologation entre un réseau virtuel dans le modèle de gestionnaire de ressources et le modèle de déploiement classique nécessite que le VNets doit être dans le même abonnement.
- Un réseau virtuel qui utilise le modèle de déploiement du Gestionnaire de ressources peut être peered avec un autre réseau virtuel qui utilise ce modèle, ou avec un réseau virtuel qui utilise le modèle de déploiement classique. Toutefois, les réseaux virtuels qui utilisent le modèle de déploiement classique ne peut pas être peered à l’autre.
- Si la communication entre les ordinateurs virtuels de ressources réseaux virtuels n’a aucune restriction de bande passante supplémentaire, cap de la bande passante en fonction de la taille de mémoire virtuelle s’applique toujours.


![Homologation de base VNet](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Connectivité
Une fois que deux réseaux virtuels sont ressources, un ordinateur virtuel (rôle web/travail) dans le réseau virtuel peuvent se connecter directement avec les autres ordinateurs virtuels dans le réseau virtuel de ressources. Ces deux réseaux ont complète de niveau IP connectivité.

La latence du réseau pour un aller-retour entre deux ordinateurs virtuels dans les ressources réseaux virtuels est le même que pour un aller-retour au sein d’un réseau virtuel local. Le débit du réseau est basé sur la bande passante est autorisée pour la machine virtuelle proportionnellement à sa taille. Il n’existe pas une restriction supplémentaire sur la bande passante.

Le trafic entre les ordinateurs virtuels de ressources réseaux virtuels est routé directement par le biais de l’infrastructure back-end Azure et non par l’intermédiaire d’une passerelle.

Machines virtuelles dans un réseau virtuel peut accéder les internes avec équilibrage de charge (ILB) points de terminaison dans le réseau virtuel de ressources. Les groupes de sécurité réseau (NSGs) peuvent être appliquées dans un réseau virtuel pour bloquer l’accès à d’autres réseaux virtuels ou les sous-réseaux si vous le souhaitez.

Lorsque les utilisateurs configurent l’homologation, ils peuvent ouvrir ou fermer les règles NSG entre les réseaux virtuels. Si l’utilisateur choisit d’ouvrir une connectivité totale entre ressources réseaux virtuels (qui est l’option par défaut), ils peuvent ensuite utiliser NSGs sur des sous-réseaux ou des machines virtuelles à bloquer ou à refuser l’accès spécifique.

Azure fourni interne résolution de noms DNS pour les ordinateurs virtuels ne fonctionne pas sur les ressources réseaux virtuels. Ordinateurs virtuels ont des noms DNS internes qui peuvent être résolus uniquement dans le réseau virtuel local. Toutefois, les utilisateurs peuvent configurer les ordinateurs virtuels qui s’exécutent dans les ressources réseaux virtuels en tant que serveurs DNS pour un réseau virtuel.

## <a name="service-chaining"></a>Chaînage de service
Les utilisateurs peuvent configurer des tables de routage de défini par l’utilisateur qui pointent vers des machines virtuelles dans des réseaux virtuels ressources comme « saut suivant » adresse IP, comme illustré dans le diagramme plus loin dans cet article. Les utilisateurs peuvent ainsi atteindre le chaînage de service, par le biais duquel ils peuvent diriger le trafic d’un réseau virtuel à une appliance virtuelle qui s’exécute dans un réseau virtuel de ressources par le biais de tables de routage de défini par l’utilisateur.

Les utilisateurs peuvent aussi efficacement créer des environnements de hub-and-spoke où le concentrateur peut héberger les composants de l’infrastructure comme un matériel de réseau virtuel. Tous les réseaux virtuels spoke peuvent ensuite homologue avec elle, ainsi qu’un sous-ensemble de trafic pour les applications qui s’exécutent dans le réseau virtuel du concentrateur. En bref, l’homologation de VNet permet à l’adresse IP de tronçon suivant sur le « utilisateur défini par table de routage » à l’adresse IP d’un ordinateur virtuel dans le réseau virtuel de ressources.

## <a name="gateways-and-on-premises-connectivity"></a>Connectivité des passerelles et des locaux
Chaque réseau virtuel, si elle est ressources avec un autre réseau virtuel, peut toujours avoir sa propre passerelle et l’utiliser pour se connecter à des locaux. Les utilisateurs peuvent configurer également les [connexions de VNet-à-VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) à l’aide de passerelles, même si les réseaux virtuels sont ressources.

Lorsque les deux options pour l’interconnectivité des réseaux virtuels sont configurées, le trafic entre les réseaux virtuels circule dans la configuration d’homologation (autrement dit, par le biais de la colonne vertébrale Azure).

Lorsque les réseaux virtuels sont ressources, les utilisateurs peuvent également configurer la passerelle dans le réseau virtuel de ressources sous la forme d’un point de transit et sur site. Dans ce cas, le réseau virtuel qui est à l’aide d’une passerelle distante ne peut pas avoir sa propre passerelle. Un réseau virtuel peut avoir qu’une seule passerelle. Il peut être une passerelle locale ou une passerelle distante (en ressources réseau virtuel), comme illustré dans l’image suivante.

Transit de passerelle n’est pas pris en charge dans la relation entre les réseaux virtuels à l’aide du modèle de gestionnaire de ressources et ceux qui utilisent le modèle de déploiement classique d’homologation. Les deux réseaux virtuels dans la relation d’homologation doivent utiliser le modèle de déploiement Gestionnaire de ressources pour un transit de passerelle.

Lorsque les réseaux virtuels qui partagent une seule connexion Azure ExpressRoute sont ressources le trafic entre eux passe par la relation d’homologation (c'est-à-dire, via le réseau principal Azure). Les utilisateurs peuvent toujours utiliser passerelles locales dans chaque réseau virtuel pour se connecter au circuit local. Également, ils peuvent utiliser une passerelle partagée et configurer transit pour une connectivité sur site.

![Transit d’homologation VNet](./media/virtual-networks-peering-overview/figure02.png)

## <a name="provisioning"></a>Mise en service
Homologation de VNet est une opération privilégiée. Il s’agit d’une fonction séparée sous l’espace de noms VirtualNetworks. Un utilisateur peut recevoir des droits spécifiques pour autoriser l’homologation. Un utilisateur qui dispose d’un accès en lecture / écriture au réseau virtuel hérite automatiquement de ces droits.

Un utilisateur qui est un administrateur ou un utilisateur privilégié de la possibilité d’homologation peut débuter une homologation sur un autre VNet. S’il existe une demande correspondante pour l’homologation de l’autre côté, et si les autres conditions sont respectées, l’homologation est établie.

Consultez les articles correspondants dans la section « Étapes suivantes » pour en savoir plus sur comment établir VNet peering entre deux réseaux virtuels.

## <a name="limits"></a>Limites
Il existe des limites sur le nombre de peerings qui sont autorisés pour un seul réseau virtuel. Pour plus d’informations, voir la rubrique [limites de réseau Azure](../azure-subscription-service-limits.md#networking-limits) .

## <a name="pricing"></a>Tarification
Homologation de VNet sera gratuitement durant la période de révision. Après leur publication, il y aura un coût modique sur le trafic entrant et sortant qui utilise l’homologation. Pour plus d’informations, reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/virtual-network).


## <a name="next-steps"></a>Étapes suivantes
- [Paramétrer l’homologation entre des réseaux virtuels](virtual-networks-create-vnetpeering-arm-portal.md).
- Obtenir des informations sur les [NSGs](virtual-networks-nsg.md).
- Obtenir des informations sur [les itinéraires définis par l’utilisateur et le transfert IP](virtual-networks-udr-overview.md).
