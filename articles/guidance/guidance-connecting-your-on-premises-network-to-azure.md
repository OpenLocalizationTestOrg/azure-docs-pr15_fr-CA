<properties
   pageTitle="Connexion de votre réseau local vers Azure | Microsoft Azure"
   description="Décrit et compare les différentes méthodes disponibles pour la connexion aux services de cloud Microsoft Azure, Office 365 et Dynamics CRM en ligne."
   services=""
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="jdial"/>
   
# <a name="connecting-your-on-premises-network-to-azure"></a>Connexion de votre réseau local vers Azure

Microsoft fournit plusieurs types de services en nuage. Alors que vous pouvez vous connecter à tous les services sur l’Internet public, vous pouvez également vous connecter à certains des services à l’aide d’un tunnel de réseau privé virtuel (VPN) sur Internet ou sur une connexion privée directe à Microsoft. Cet article vous aide à déterminer quelle option de connectivité répondra le mieux à vos besoins selon les types de services de cloud de Microsoft que vous consommez. La plupart des organisations utilisent plusieurs types de connexions décrits ci-dessous.

## <a name="connecting-over-the-public-internet"></a>Connexion via Internet

Ce type de connexion fournit un accès aux services de cloud Microsoft directement sur Internet, comme indiqué ci-dessous.

![Internet] (./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Cette connexion est généralement le premier type utilisé pour la connexion aux services de cloud de Microsoft. Le tableau ci-dessous répertorie les avantages et les inconvénients de ce type de connexion.



| **Avantages**| **Considérations relatives à la**|
|---------|---------|
|Ne nécessite aucune modification à votre réseau local tant que tous les périphériques de clients ont un accès illimité à toutes les adresses IP et ports sur Internet.|Bien que le trafic est souvent crypté à l’aide de HTTPS, il peut être intercepté en transit dans la mesure où il parcourt le réseau Internet public.|
|Peut se connecter à tous les services de cloud Microsoft exposées à l’Internet public.|Latence imprévisible, car la connexion circulent sur Internet.|
|Utilise votre connexion Internet existante.||
|Ne nécessite pas la gestion des périphériques de connectivité.||

Cette connexion n’a aucune connectivité ou les coûts de bande passante dans la mesure où vous utilisez votre connexion Internet existante. 

## <a name="connecting-with-a-point-to-site-connection"></a>Connexion avec une connexion point-à-site

Ce type de connexion permet d’accéder à certains services de cloud de Microsoft via un tunnel Secure Socket Tunneling Protocol (SSTP) sur Internet, comme indiqué ci-dessous.

![P2S] Connexion de (./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "point-à-site")

La connexion a lieu via votre connexion Internet existante, mais nécessite l’utilisation d’une passerelle de VPN Azure. Le tableau ci-dessous répertorie les avantages et les inconvénients de ce type de connexion.

| **Avantages**| **Considérations relatives à la**|
|---------|---------|
|Ne nécessite aucune modification à votre réseau local tant que tous les périphériques de clients ont un accès illimité à toutes les adresses IP et ports sur Internet.|Bien que le trafic est crypté à l’aide d’IPSec, il peut être intercepté en transit dans la mesure où il parcourt le réseau Internet public.|
|Utilise votre connexion Internet existante.|Latence imprévisible, car la connexion circulent sur Internet.|
|Débit de 200 Mo/s par passerelle.|Requiert la création et la gestion des connexions distinctes entre chaque périphérique de votre réseau local et de chaque passerelle à que chaque périphérique doit se connecter.|
|Peut servir à se connecter à des services Azure qui peuvent être connectés à un Azure des réseaux virtuels (VNet), tels que les ordinateurs virtuels Azure et d’Azure Cloud Services.|Nécessite très peu d’administration continue d’une passerelle de VPN Azure.|
||Ne peut pas être utilisé pour se connecter à Microsoft Office 365 ou Dynamics CRM en ligne.
||Ne peut pas être utilisé pour se connecter à des services Azure qui ne peut pas être connectés à un VNet.|

Pour en savoir plus sur le service de [Passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) , son [prix](https://azure.microsoft.com/pricing/details/vpn-gateway)et des transferts de données sortantes [de tarification](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Connexion avec une connexion de site à site

Ce type de connexion permet d’accéder à certains services de cloud de Microsoft via un tunnel IPSec sur Internet, comme indiqué ci-dessous.

![S2S] (./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "Connexion de site à site")

La connexion a lieu via votre connexion Internet existante, mais nécessite l’utilisation d’une passerelle de VPN Azure avec sa tarification associée et la tarification des transferts de données sortantes. Le tableau ci-dessous répertorie les avantages et les inconvénients de ce type de connexion.

| **Avantages**| **Considérations relatives à la**|
|---------|---------|
|Tous les périphériques de votre réseau local peuvent communiquer avec des services Azure connectés à un VNet, il est inutile de configurer des connexions individuelles pour chaque périphérique.|Bien que le trafic est crypté à l’aide d’IPSec, il peut être intercepté en transit dans la mesure où il parcourt le réseau Internet public.|
|Utilise votre connexion Internet existante.|Latence imprévisible, car la connexion circulent sur Internet.|
|Peut être utilisé pour se connecter à des services Azure qui peuvent être connectés à une VNet tels que des Machines virtuelles et Services dans le nuage.|Doit configurer et gérer un périphérique VPN validé * sur site.|
|Débit de 200 Mo/s par passerelle.|Nécessite très peu d’administration continue d’une passerelle de VPN Azure.|
|Peut forcer le trafic sortant initié à partir d’ordinateurs virtuels de nuage via le réseau local pour l’inspection et la journalisation à l’aide des itinéraires définis par l’utilisateur ou le protocole BGP (Border Gateway) **.|Ne peut pas être utilisé pour se connecter à Microsoft Office 365 ou Dynamics CRM en ligne.|
||Ne peut pas être utilisé pour se connecter à des services Azure qui ne peut pas être connectés à un VNet.|
||Si vous utilisez les services qui initier des connexions vers les périphériques locaux et vos stratégies de sécurité exigent, vous devrez peut-être un pare-feu entre le réseau de locaux et Azure.|

- * Afficher la liste des [périphériques VPN de validation](../vpn-gateway/vpn-gateway-about-vpn-devices.md#validated-vpn-devices).
- ** En savoir plus sur l’utilisation [des itinéraires définis par l’utilisateur](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) ou [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) pour forcer le routage d’Azure VNets sur un périphérique local.

## <a name="connecting-with-a-dedicated-private-connection"></a>Connexion avec une connexion privée dédiée

Ce type de connexion permet d’accéder à tous les services de cloud de Microsoft via une connexion privée dédiée à Microsoft qui ne passe pas par Internet, comme indiqué ci-dessous.

![ER] (./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute connexion")

La connexion nécessite l’utilisation du service ExpressRoute et une connexion à un fournisseur de connectivité. Le tableau ci-dessous répertorie les avantages et les inconvénients de ce type de connexion.

| **Avantages**| **Considérations relatives à la**|
|---------|---------|
|Le trafic ne peut être intercepté lors du transit sur l’Internet public depuis une connexion dédiée via un fournisseur de service est utilisée.|Requiert la gestion du routeur local.|
|Bande passante jusqu'à 10 Go/s par circuit de ExpressRoute et de débit de 2 Gbit/s pour chaque passerelle.|Requiert une connexion dédiée à un fournisseur de connectivité.|
|Latence prévisible car il s’agit d’une connexion dédiée à Microsoft qui ne passe pas par Internet.|Vous devrez très peu d’administration continue d’un ou de plusieurs passerelles VPN de Azure (si la connexion du circuit à VNets).|
|Ne nécessite pas une communication cryptée, même si vous pouvez crypter le trafic, si vous le souhaitez.| Si vous utilisez des services en nuage qui initier des connexions vers les périphériques locaux, vous devrez peut-être un pare-feu entre le réseau de locaux et Azure.|
|Peut se connecter directement à tous les services de nuage de Microsoft, avec quelques exceptions *.|Requiert la traduction d’adresses réseau (NAT) d’adresses d’IP local entrant les centres de données de Microsoft pour les services qui ne peut pas être connectés à un VNet.* *|
|Peut forcer le trafic sortant initié à partir d’ordinateurs virtuels de nuage via le réseau local pour l’inspection et la journalisation à l’aide du protocole BGP.|

- * Afficher la [liste des services](../expressroute/expressroute-faqs.md#supported-services) qui ne peut pas être utilisé avec ExpressRoute. Votre abonnement Azure doit être approuvée pour se connecter à Office 365.  Consultez l’article [ExpressRoute Azure pour Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) pour plus de détails.
- ** En savoir plus sur ExpressRoute [NAT](../expressroute/expressroute-nat.md) exigences.

Pour en savoir plus sur [ExpressRoute](../expressroute/expressroute-introduction.md), son associé [de tarification](https://azure.microsoft.com/pricing/details/expressroute)et les [fournisseurs de connectivité](../expressroute/expressroute-locations.md#connectivity-provider-locations).

## <a name="additional-considerations"></a>Considérations supplémentaires

- Plusieurs options ci-dessus ont différentes limites maximales qu’ils peuvent prendre en charge pour les connexions, les connexions de passerelle et d’autres critères VNet. Il est recommandé de consulter l’Azure [limite de mise en réseau](../azure-subscription-service-limits.md#networking-limits) pour comprendre si un d’eux un impact sur les types de connectivité que vous choisissez d’utiliser. 
- Si vous envisagez de connecter une passerelle à partir d’une connexion VPN de site à site vers le même VNet comme une passerelle de ExpressRoute, vous devez vous familiariser avec les contraintes importantes tout d’abord. Voir l’article [configurer les ExpressRoute et les connexions de Site à Site coexistence](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) pour plus de détails.

## <a name="next-steps"></a>Étapes suivantes

Les ressources ci-dessous vous indiquent comment implémenter les types de connexions traitées dans cet article.

-   [Mettre en œuvre une connexion point-à-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
-   [Implémenter une connexion de site à site](guidance-hybrid-network-vpn.md)
-   [Implémenter une connexion privée dédiée](guidance-hybrid-network-expressroute.md)
-   [Implémenter une connexion privée dédiée avec une connexion de site à site pour une haute disponibilité](guidance-hybrid-network-expressroute-vpn-failover.md)
