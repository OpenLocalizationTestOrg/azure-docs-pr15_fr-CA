<properties
   pageTitle="Exigences de gamme pour ExpressRoute | Microsoft Azure"
   description="Cette page fournit des spécifications détaillées pour la configuration et la gestion du routage pour circuits de ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="ganesr"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="osamazia"/>


# <a name="expressroute-routing-requirements"></a>Exigences de gamme ExpressRoute  

Pour se connecter aux services de cloud de Microsoft à l’aide de ExpressRoute, vous aurez besoin configurer et gérer le routage. Certains fournisseurs de connectivité offrent la configuration et la gestion de routage sous la forme d’un service managé. Vérifiez auprès de votre fournisseur de connectivité pour savoir s’il offre ce service. Si ce n’est pas le cas, vous devez respecter les conditions suivantes. 

Reportez-vous à l’article de [Circuits et des domaines de routage](expressroute-circuit-peerings.md) pour obtenir une description des sessions de routage qui doivent être définis pour faciliter la connectivité.

>[AZURE.NOTE] Microsoft ne gère pas les protocoles de redondance de routeur (par exemple, HSRP, VRRP) pour les configurations haute disponibilité. Nous comptons sur une paire redondante de sessions BGP par homologation pour une haute disponibilité.

## <a name="ip-addresses-used-for-peerings"></a>Adresses IP utilisées pour peerings

Vous devez réserver quelques blocs d’adresses IP pour configurer le routage entre votre réseau et les routeurs de bord (MSEEs) d’entreprise de Microsoft. Cette section fournit une liste de conditions et décrit les règles concernant la façon dont ces adresses IP doivent être acquis et utilisés.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Adresses IP utilisées pour homologation privé Azure

Vous pouvez utiliser soit des adresses IP privées ou des adresses IP publiques pour configurer les peerings. La plage d’adresses utilisée pour configurer des itinéraires ne doit pas se chevaucher avec les plages d’adresses utilisées pour créer des réseaux virtuels dans Azure. 

 - Vous devez réserver un /29 ou les deux /30 sous-réseaux pour les interfaces de routage.
 - Les sous-réseaux utilisés pour le routage peuvent être des adresses IP privées ou des adresses IP publiques.
 - Les sous-réseaux ne doivent pas entrer en conflit avec la plage réservée par le client pour une utilisation dans le cloud de Microsoft.
 - Si un /29 sous-réseau est utilisée, il est divisé en sous-réseaux de deux /30. 
     - Le premier /30 sous-réseau sera utilisé pour le lien principal et le second/30 sous-réseau sera utilisé pour le lien secondaire.
     - Pour chaque de la /30 des sous-réseaux, vous devez utiliser la première adresse IP de la /30 sous-réseau sur votre routeur. Microsoft utilisera la deuxième adresse IP de la /30 sous-réseau pour configurer une session BGP.
     - Vous devez configurer les deux sessions BGP pour notre [SLA de disponibilité](https://azure.microsoft.com/support/legal/sla/) être valide.  

#### <a name="example-for-private-peering"></a>Exemple d’homologation privé

Si vous choisissez d’utiliser a.b.c.d/29 pour définir l’homologation, il est scindé en deux /30 sous-réseaux. Dans l’exemple ci-dessous, nous allons étudier comment est utilisé le sous-réseau a.b.c.d/29. 

a.b.c.d/29 sera fractionné sur a.b.c.d/30 et a.b.c.d+4/30 et transmises à Microsoft par le biais de l’API de mise en service. Vous utilisez a.b.c.d+1 comme l’IP VRF pour le PE primaire et Microsoft consomme a.b.c.d+2 comme le VRF IP pour le principal MSEE. Vous utilisez a.b.c.d+5 comme l’IP VRF pour le PE secondaire et Microsoft utilise a.b.c.d+6 comme l’IP VRF pour le MSEE secondaire.

Considérez un cas où vous sélectionnez 192.168.100.128/29 pour configurer une homologation privé. 192.168.100.128/29 comprend les adresses allant de 192.168.100.128 à 192.168.100.135, parmi lesquelles :

- 192.168.100.128/30 incombera aux link1, avec le fournisseur à l’aide de Microsoft à l’aide de 192.168.100.130 et 192.168.100.129.
- 192.168.100.132/30 incombera aux link2, avec le fournisseur à l’aide de Microsoft à l’aide de 192.168.100.134 et 192.168.100.133.

### <a name="ip-addresses-used-for-azure-public-and-microsoft-peering"></a>Adresses IP utilisées pour public Azure et homologation de Microsoft

Vous devez utiliser des adresses IP publiques que vous possédez pour configurer les sessions BGP. Microsoft doit être en mesure de vérifier la propriété des adresses IP par le biais de routage des registres Internet et les registres de routage Internet. 

- Vous devez utiliser un unique/29 ou les sous-réseaux de deux /30 pour configurer le protocole BGP homologation pour chaque homologation par ExpressRoute de circuit (si vous avez plus d’un). 
- Si un /29 sous-réseau est utilisée, il est divisé en sous-réseaux de deux /30. 
    - Le premier /30 sous-réseau sera utilisé pour le lien principal et le second/30 sous-réseau sera utilisé pour le lien secondaire.
    - Pour chaque de la /30 des sous-réseaux, vous devez utiliser la première adresse IP de la /30 sous-réseau sur votre routeur. Microsoft utilisera la deuxième adresse IP de la /30 sous-réseau pour configurer une session BGP.
    - Vous devez configurer les deux sessions BGP pour notre [SLA de disponibilité](https://azure.microsoft.com/support/legal/sla/) être valide.

## <a name="public-ip-address-requirement"></a>Demande d’adresse IP publique 

### <a name="private-peering"></a>Homologation privé 

Vous pouvez choisir d’utiliser des adresses IPv4 publiques ou privées pour homologation privé. Nous fournissons d’isolation de bout en bout du trafic se chevauchant des adresses avec d’autres clients n’est pas possible dans le cas d’homologation privé. Ces adresses ne sont pas annoncées à Internet. 

### <a name="public-peering"></a>Homologation public

Le chemin homologation public Azure permet de vous connecter à tous les services hébergés dans Azure sur les adresses IP publiques. Notamment les services répertoriés dans le [Forum aux questions sur ExpessRoute](expressroute-faqs.md) et tous les services hébergés par les éditeurs de logiciels de Microsoft Azure. Connexion aux services Microsoft Azure sur peering public est toujours lancée à partir de votre réseau dans le réseau de Microsoft. Vous devez utiliser des adresses IP publiques pour le trafic destiné au réseau Microsoft.

### <a name="microsoft-peering"></a>Homologation de Microsoft

Le chemin d’accès homologation Microsoft vous permet de vous connecter aux services de cloud de Microsoft qui ne sont pas pris en charge via le chemin d’homologation public Azure. La liste des services comprend des services Office 365, tel qu’Exchange Online, SharePoint Online, Skype pour les entreprises et CRM en ligne. Microsoft prend en charge une connectivité bidirectionnelle sur l’homologation de Microsoft. Le trafic destiné à des services en nuage Microsoft doit utiliser des adresses IPv4 publiques valides avant leur entrée sur le réseau Microsoft.

Assurez-vous que votre adresse IP et en nombre sont enregistrés sous les registres répertoriés ci-dessous.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)

>[AZURE.IMPORTANT] Des adresses IP publiques publiés sur Microsoft sur le ExpressRoute ne doivent pas annoncés à Internet. Ceci peut empêcher la connectivité à d’autres services de Microsoft. Toutefois, peuvent faire l’objet des adresses IP publiques utilisées par les serveurs de votre réseau qui communiquent avec les points de terminaison O365 au sein de Microsoft sur ExpressRoute. 

## <a name="dynamic-route-exchange"></a>Exchange de la gamme dynamique

Routage exchange sera sur eBGP protocole. Sessions EBGP sont établies entre les MSEEs et les routeurs. L’authentification des sessions du protocole BGP n’est pas une obligation. Si nécessaire, un hachage MD5 peut être configuré. Pour plus d’informations sur la configuration des sessions BGP, consultez la [configuration routage](expressroute-howto-routing-classic.md) et [Circuit de mise en service de flux de travail et des États de circuit](expressroute-workflows.md) .

## <a name="autonomous-system-numbers"></a>Numéros de système autonomes

Microsoft utilisera comme 12076 pour Azure publique, privé d’Azure et homologation de Microsoft. Nous vous avons réservé les APE à partir de 65515 à 65520 pour un usage interne. Les 16 et 32 bits sous forme de nombres sont pris en charge.

Il n’y a aucune exigence concernant la symétrie de transfert de données. Les chemins d’accès aller et retour peuvent parcourir les paires de l’autre routeur. Itinéraires identiques doivent être publiés à partir des deux côtés sur plusieurs paires de circuit vous appartenant. Métriques d’itinéraire ne doivent pas être identiques.

## <a name="route-aggregation-and-prefix-limits"></a>Regroupement des itinéraires et des limites de préfixe

Nous prenons en charge jusqu'à 4000 préfixes annoncés pour nous par l’intermédiaire de l’homologation privé Azure. Elle peut être augmentée jusqu'à 10 000 préfixes si le complément de la prime ExpressRoute est activé. Nous allons accepter jusqu'à 200 préfixes par session BGP pour Azure public et homologation de Microsoft. 

La session BGP est supprimée si le nombre de préfixes dépasse la limite. Nous allons accepter les itinéraires par défaut sur le lien d’homologation privé uniquement. Fournisseur doit éliminer d’itinéraire par défaut et les adresses IP privées (RFC 1918) à partir de l’Azure public et homologation chemins d’accès de Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Transit et routage inter-région

ExpressRoute ne peut pas être configuré en tant que routeurs de transit. Vous devrez compter sur votre fournisseur de connectivité pour les services de routage de transit.

## <a name="advertising-default-routes"></a>Itinéraires par défaut de publicité

Itinéraires par défaut sont autorisés uniquement sur Azure sessions d’homologation privées. Dans ce cas, nous achemine tout le trafic dans les réseaux virtuels associés à votre réseau. Publicité des itinéraires par défaut dans l’homologation privé entraînera le chemin d’accès internet à partir d’Azure bloqué. Vous devez compter sur votre bord d’entreprise à acheminer le trafic depuis et vers internet pour les services hébergés dans Azure. 

 Pour activer la connectivité à d’autres services Azure et les services d’infrastructure, vous devez vous assurer qu’un des éléments suivants est en place :

 - Homologation public Azure est activé pour acheminer le trafic vers des points de terminaison publics
 - Vous utilisez le routage de défini par l’utilisateur pour permettre la connectivité internet pour chaque sous-réseau nécessitant une connectivité Internet.
 
>[AZURE.NOTE] Annonce des itinéraires par défaut interrompt Windows et autre activation de licence de machine virtuelle. Suivez les instructions [ici](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) pour contourner ce problème.

## <a name="support-for-bgp-communities-preview"></a>Prise en charge des Communautés BGP (aperçu)


Cette section fournit une vue d’ensemble de l’utilisation des Communautés BGP avec ExpressRoute. Microsoft annonce des itinéraires dans le public et les chemins d’homologation Microsoft avec des itinéraires balisés avec valeurs communautaires appropriées. Le raisonnement de le faire, les informations sur les valeurs de la Communauté sont décrites ci-dessous. Microsoft, toutefois, ne respecteront pas les valeurs de communauté balisés pour les itinéraires annoncés à Microsoft.

Si vous vous connectez à Microsoft par le biais de ExpressRoute à n’importe quel une emplacement homologation dans une région géopolitique, avoir accès à tous les services de nuage de Microsoft dans toutes les régions dans les limites géopolitiques. 

Par exemple, si vous connecté à Microsoft d’Amsterdam par le biais de ExpressRoute, vous aurez accès à tous les services de cloud Microsoft hébergé en Europe du Nord et en Europe de l’ouest. 

Reportez-vous à la page [ExpressRoute partenaires et homologation des emplacements](expressroute-locations.md) pour une liste détaillée des régions géopolitiques, les zones de Azure associés et homologation des emplacements de ExpressRoute correspondant.

Vous pouvez acheter plus d’un circuit de ExpressRoute par région géopolitique. Connexions multiples vous offre des avantages significatifs sur la disponibilité élevée en raison de la redondance géographique. Dans le cas où vous avez plusieurs circuits de ExpressRoute, vous recevrez le même jeu de préfixes annoncés de Microsoft sur l’homologation public et homologation des chemins d’accès. Cela signifie que vous avez plusieurs chemins d’accès de votre réseau dans Microsoft. Ceci risque de provoquer des décisions de routage optimale à apporter au sein de votre réseau. Par conséquent, vous pouvez rencontrer des expériences de connectivité optimale aux différents services. 

Microsoft permet d’identifier les préfixes annoncés via peering public et Microsoft homologue avec les valeurs de communauté BGP appropriées indiquant la région hébergées dans les préfixes. Vous pouvez compter sur les valeurs de la Communauté pour prendre des décisions de routage appropriées pour offrir [un routage optimal à ses clients](expressroute-optimize-routing.md).

| **Région géopolitique** | **Région de Microsoft Azure** | **Valeur de communauté BGP** |
|---|---|---|
| **Amérique du Nord** |    |  |
|    | Les États-Unis | 12076:51004 |
|    | Les États-Unis 2 | 12076:51005 |
|    | États-Unis Ouest | 12076:51006 |
|    | États-Unis Ouest 2 | 12076:51026 |
|    | Ouest des États-Unis centre | 12076:51027 |
|    | États-Unis centre nord | 12076:51007 |
|    | États-Unis centre sud | 12076:51008 |
|    | États-Unis centre | 12076:51009 |
|    | Canada Central | 12076:51020 |
|    | Est du Canada | 12076:51021 |
| **Amérique du Sud** |  |  |
|    | Sud du Brésil | 12076:51014 |
| **Europe** |    |  |
|    | Europe du Nord | 12076:51003 |
|    | Europe de l’ouest | 12076:51002 |
| **Asie-Pacifique** |    |   |
|    | Asie de l’est | 12076:51010 |
|    | Asie du Sud-est | 12076:51011 |
| **Japon** |     |   |
|    | Nord-est du Japon | 12076:51012 |
|    | Ouest du Japon | 12076:51013 |
| **Australie** |    |   | 
|    | Est de l’Australie | 12076:51015 |
|    | Sud-est de l’Australie | 12076:51016 |
| **Inde** |    |   |
|    | Sud de l’Inde | 12076:51019 |
|    | Ouest de l’Inde | 12076:51018 |
|    | Central de l’Inde | 12076:51017 |

Tous les itinéraires sont publiés à partir de Microsoft seront identifiés par la valeur de la COMMUNAUTE. 

>[AZURE.IMPORTANT] Les préfixes globaux seront marqués avec une valeur de communauté approprié et seront publiés uniquement lorsque le module complémentaire de prime ExpressRoute est activé.


En outre, Microsoft va également baliser les préfixes basés sur le service auquel ils appartiennent. Cela s’applique uniquement à l’homologation de Microsoft. Le tableau ci-dessous fournit un mappage de service à la valeur de communauté BGP.

| **Service** | **Valeur de communauté BGP** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype pour entreprise** | 12076:5030 |
| **CRM en ligne** | 12076:5040 |
| **Autres Services Office 365** | 12076:5100 |

>[AZURE.NOTE] Microsoft ne tient pas compte des valeurs communautaires BGP que vous définissez sur les itinéraires annoncés à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- Configurez votre connexion ExpressRoute.

    - [Créer un circuit de ExpressRoute pour le modèle de déploiement classique](expressroute-howto-circuit-classic.md) ou [créer et modifier un circuit de ExpressRoute à l’aide du Gestionnaire de ressources Azure](expressroute-howto-circuit-arm.md)
    - [Configurer le routage pour le modèle de déploiement classique](expressroute-howto-routing-classic.md) ou [configurer le routage pour le modèle de déploiement du Gestionnaire de ressources](expressroute-howto-routing-arm.md)
    - [Lien un VNet classique pour un circuit de ExpressRoute](expressroute-howto-linkvnet-classic.md) ou [Un VNet du Gestionnaire de ressources pour un circuit de ExpressRoute](expressroute-howto-linkvnet-arm.md)


