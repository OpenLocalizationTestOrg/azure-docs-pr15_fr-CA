<properties
   pageTitle="Vue d’ensemble du protocole BGP avec les passerelles VPN Azure | Microsoft Azure"
   description="Cet article fournit une vue d’ensemble du protocole BGP avec des passerelles VPN Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/16/2016"
   ms.author="yushwang"/>

# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Vue d’ensemble du protocole BGP avec les passerelles VPN Azure

Cet article fournit une vue d’ensemble de la prise en charge de protocole BGP (Border Gateway Protocol) dans les passerelles VPN Azure.

## <a name="about-bgp"></a>À propos de BGP

BGP est le protocole de routage standard couramment dans Internet pour échanger des informations de routage et de l’accessibilité entre deux ou plusieurs réseaux. Lorsqu’il est utilisé dans le contexte des réseaux virtuels d’Azure, BGP permet des passerelles VPN Azure et vos périphériques VPN sur site, appelés BGP homologues ou Voisinage réseau, d’échanger les « route » qui informe les deux passerelles sur la disponibilité et l’accessibilité de ces préfixes à traverser les passerelles ou routeurs impliqués. BGP permettent également d’acheminement de transit entre plusieurs réseaux par propagation des itinéraires qu'une passerelle BGP apprend à partir d’un homologue BGP pour tous les autres pairs BGP.
 
### <a name="why-use-bgp"></a>Pourquoi utiliser BGP ?

BGP est une fonctionnalité facultative que vous pouvez utiliser avec les passerelles VPN basée sur un itinéraire de Azure. Vous devez également vous que vos périphériques VPN sur site prennent en charge BGP avant d’activer la fonctionnalité. Vous pouvez continuer à utiliser des passerelles VPN d’Azure et vos périphériques VPN local sans protocole BGP. Il est l’équivalent de l’utilisation des itinéraires statiques (sans BGP) *par rapport* à l’aide d’un routage dynamique avec BGP entre vos réseaux et vos Azure.

Il existe plusieurs avantages et des nouvelles fonctionnalités avec BGP :

#### <a name="support-automatic-and-flexible-prefix-updates"></a>Prise en charge des mises à jour du préfixe automatique et flexible

Avec BGP, il vous suffit de déclarer un préfixe minimal à un homologue BGP spécifique via le tunnel IPsec S2S VPN. Il peut être aussi petit que le préfixe d’un hôte (/ 32) de l’adresse d’IP homologue BGP de votre périphérique VPN sur site. Vous pouvez contrôler lequel local préfixes réseau que vous souhaitez publier vers Azure pour permettre à votre réseau virtuel d’Azure pour accéder à.
    
Vous pouvez également publier une plus grande préfixes qui peuvent inclure des préfixes d’adresses de votre VNet, comme un grand privé espace d’adresse IP (par exemple, 10.0.0.0/8). Veuillez noter que les préfixes ne peut pas être identiques à l’un des préfixes de votre VNet. Ces itinéraires identiques à votre VNet les préfixes seront rejetées.

>[AZURE.IMPORTANT] Actuellement, l’itinéraire par défaut (0.0.0.0/0) pour les passerelles VPN d’Azure de publicité est bloqué. Mise à jour supplémentaire est fourni dès que cette fonctionnalité est activée.

#### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>Prise en charge de plusieurs tunnels entre un VNet et un site local avec basculement automatique basé sur BGP

Vous pouvez établir plusieurs connexions entre votre VNet Azure et vos périphériques VPN sur site au même emplacement. Cette fonctionnalité fournit plusieurs tunnels (chemins d’accès) entre les deux réseaux dans une configuration active-active. Si un des tunnels est déconnecté, les gammes correspondantes seront retirées via BGP et le trafic se déplace automatiquement vers les tunnels restants.
    
Le diagramme suivant montre un exemple simple de cette configuration hautement disponible :
    
![Plusieurs chemins actifs](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>Prend en charge l’acheminement de transit entre vos réseaux locaux et plusieurs Azure VNets

BGP permet plusieurs passerelles à apprendre et propager des préfixes de réseaux différents, si elles sont directement ou indirectement connectés. Cela peut permettre d’acheminement de transit avec les passerelles VPN d’Azure entre vos sites sur site ou à travers plusieurs réseaux virtuels Azure.
    
Le diagramme suivant illustre un exemple de topologie de sauts multiples avec plusieurs chemins d’accès qui peuvent transiter le trafic entre les deux réseaux locaux par l’intermédiaire de passerelles VPN d’Azure dans la Microsoft Networks :

![Transit de sauts multiples](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faqs"></a>Forum aux questions du protocole BGP


[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 




## <a name="next-steps"></a>Étapes suivantes

Reportez-vous à la section [mise en route avec BGP sur les passerelles VPN d’Azure](./vpn-gateway-bgp-resource-manager-ps.md) pour obtenir la procédure pour configurer BGP pour des connexions de la VNet-de-VNet et de coexistence.

