<properties
   pageTitle="Vue d’ensemble des configurations haute disponibilité grâce à des passerelles VPN Azure | Microsoft Azure"
   description="Cet article fournit une vue d’ensemble des options de configuration à haute disponibilité à l’aide de passerelles VPN de Azure."
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
   ms.date="09/24/2016"
   ms.author="yushwang"/>

# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Coexistence hautement disponible et VNet à VNet connectivité

Cet article fournit une vue d’ensemble des options de configuration de haute disponibilité pour votre coexistence et la connectivité de VNet-à-VNet à l’aide de passerelles VPN d’Azure.

## <a name = "activestandby"></a>À propos de la redondance de passerelle VPN de Azure

Chaque passerelle VPN d’Azure se compose de deux instances dans une configuration active-de réserve. Pour une maintenance planifiée ou d’interruptions non planifiées qui se trouve à l’instance active, l’instance en attente serait prendre automatiquement le relais (basculement) et reprendre la S2S VPN ou connexions de VNet-à-VNet. Le commutateur sur entraîne une brève interruption. Pour la maintenance planifiée, la connectivité doit être restaurée dans les 10 à 15 secondes. Pour les problèmes non planifiés, la récupération de la connexion sera plus longue, environ 1 minute à quelques minutes de 1 et une partie dans le pire des cas. Les connexions du client VPN de P2S à la passerelle, les connexions P2S vont être déconnectées et les utilisateurs doivent se reconnecter à partir des ordinateurs client.

![Active-de réserve](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Connectivité de coexistence hautement disponible

Pour fournir une plus grande disponibilité pour vos connexions entre locaux, deux options sont disponibles :

- Plusieurs périphériques VPN sur site
- Passerelle de VPN d’Azure actif-actif
- Combinaison des deux

### <a name = "activeactiveonprem"></a>Plusieurs périphériques VPN sur site

Vous pouvez utiliser plusieurs périphériques VPN de votre réseau local pour se connecter à la passerelle VPN d’Azure, comme illustré dans le diagramme suivant :

![Plusieurs locaux VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

Cette configuration offre plusieurs tunnels actifs à partir de la même passerelle VPN d’Azure à vos périphériques locaux dans le même emplacement. Il existe certaines exigences et les contraintes :

1. Vous devez créer plusieurs connexions VPN de S2S à partir de vos périphériques VPN vers Azure. Lorsque vous vous connectez plusieurs périphériques VPN au réseau local sur Azure, vous devez créer une passerelle de réseau local pour chaque périphérique VPN et une connexion à partir de la passerelle VPN d’Azure de la passerelle du réseau local.

2. Les passerelles de réseau local correspondant à vos périphériques VPN doivent avoir des adresses IP publiques uniques dans la propriété « GatewayIpAddress ».

3. BGP est requis pour cette configuration. Chaque passerelle réseau local représentant un périphérique VPN doit avoir une BGP homologue adresse IP spécifiée dans la propriété « BgpPeerIpAddress ».

4. Le champ de la propriété AddressPrefix dans chaque passerelle du réseau local ne doit pas se chevaucher. Vous devez spécifier le « BgpPeerIpAddress » dans les /32 format CIDR dans le champ AddressPrefix, par exemple, 10.200.200.254/32.

5. Vous devez utiliser BGP pour annoncer les mêmes préfixes du même local les préfixes de réseau à votre passerelle VPN d’Azure, et le trafic ne sera transmis via ces tunnels, simultanément.

6. Chaque connexion est imputée sur le nombre maximal de tunnels pour votre passerelle VPN d’Azure, 10 pour Basic et des points de stock Standard et 30 pour HighPerformance SKU. 

Dans cette configuration, la passerelle VPN d’Azure est toujours en mode de veille active, le même comportement de basculement et d’une brève interruption se produira toujours comme décrit [ci-dessus](#activestandby). Mais ce programme d’installation vous protège contre les défaillances ou interruptions sur votre réseau de locaux et de périphériques VPN.
 
### <a name="active-active-azure-vpn-gateway"></a>Passerelle de VPN d’Azure actif-actif

Vous pouvez maintenant créer une passerelle VPN d’Azure dans une configuration actif-actif, où les deux instances des passerelle VM va établir des tunnels VPN de S2S sur votre périphérique VPN sur site, comme le montre le diagramme suivant :

![Actif-actif](./media/vpn-gateway-highlyavailable/active-active.png)

Dans cette configuration, chaque instance Azure passerelle aura une adresse IP publique, et chacun va établir un tunnel IPsec/IKE S2S VPN à votre périphérique VPN local spécifié dans votre passerelle du réseau local et votre connexion. Notez que les deux tunnels VPN sont en réalité pas partie de la même connexion. Vous devez toujours configurer votre périphérique VPN local pour accepter ou établir des deux tunnels VPN de S2S à ces deux Azure VPN passerelle des adresses IP publiques.

Étant donné que les instances de passerelle Azure sont en configuration actif-actif, le trafic de votre réseau virtuel Azure à votre réseau local sera routé via les deux tunnels simultanément, même si votre périphérique VPN local peut favoriser un tunnel sur l’autre. Notez que le même flux TCP ou UDP parcourt toujours le même tunnel ou chemin d’accès, à moins qu’un événement d’entretien se produit sur une des instances.

Une maintenance planifiée ou un événement imprévu se produit pour une instance d’une passerelle, le tunnel IPsec à partir de cette instance au périphérique VPN local est déconnecté. Les gammes correspondantes sur les périphériques VPN doivent être supprimées ou retirées automatiquement afin que le trafic bascule sur d’autre tunnel IPsec actif. Sur le côté d’Azure, le passage se produira automatiquement à partir de l’instance affectée à l’instance active.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Redondance double : les passerelles VPN actif pour les réseaux à la fois sur site et Azure

L’option la plus fiable consiste à combiner des passerelles actif sur votre réseau et Azure, comme illustré dans le schéma ci-dessous.

![Redondance double](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

Vous créez le programme d’installation de la passerelle VPN d’Azure dans une configuration active-active et créez deux passerelles de réseau local et deux connexions pour vos locaux périphériques VPN comme décrit ci-dessus. Le résultat est une maille pleine de connectivité de 4 tunnels IPsec entre votre réseau virtuel Azure et votre réseau local.

Toutes les passerelles et les tunnels sont actives du côté Azure, afin que le trafic est répartie entre tous les 4 tunnels simultanément, bien que chaque flux TCP ou UDP à nouveau suivent le même tunnel ou le chemin d’accès du côté Azure. Même si en répartissant le trafic, vous pouvez voir légèrement meilleur débit sur les tunnels IPsec, l’objectif principal de cette configuration est d’une haute disponibilité. Et en raison de la nature statistique de la propagation, il est difficile d’en fournir la mesure sur les différentes conditions de trafic d’application aura une incidence sur le débit global.

Cette topologie nécessite deux passerelles de réseau local et deux connexions à la prise en charge de la paire de périphériques VPN de locaux et BGP est requise pour permettre les deux connexions au même réseau local. Ces exigences sont les mêmes que l' [ci-dessus](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Connectivité à forte disponibilité VNet-à-VNet par l’intermédiaire de passerelles VPN Azure

La même configuration actif-actif peut s’appliquent également aux connexions d’Azure de VNet à VNet. Vous pouvez créer des passerelles VPN d’actif pour les deux réseaux virtuels et les connecter ensemble au formulaire de la même connectivité maille pleine de 4 tunnels entre les deux VNets, comme illustré dans le diagramme ci-dessous :

![VNet-à-VNet](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

Cela garantit qu’il n’y a toujours une paire de tunnels entre les deux réseaux virtuels pour tout événement d’entretien planifié, offrant une meilleure disponibilité. Bien que la même topologie de coexistence connectivité nécessite deux connexions, la topologie de VNet à VNet ci-dessus devra qu’une seule connexion pour chaque passerelle. En outre, BGP est facultative à moins que l’acheminement de transit via la connexion VNet-à-VNet est obligatoire.


## <a name="next-steps"></a>Étapes suivantes

Reportez-vous à la section [Configuration des passerelles VPN Active-Active pour les connexions de VNet-de-VNet et de la coexistence](vpn-gateway-activeactive-rm-powershell.md) pour les étapes pour configurer la coexistence actif-actif et les connexions de VNet-à-VNet.
