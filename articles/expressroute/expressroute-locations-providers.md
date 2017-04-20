<properties
   pageTitle="Les emplacements de ExpressRoute | Microsoft Azure"
   description="Cet article fournit une vue d’ensemble détaillée des emplacements où les services sont proposés et comment se connecter à des régions Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="cherylmc" />

# <a name="expressroute-partners-and-peering-locations"></a>Partenaires de ExpressRoute et d’homologation des emplacements

Les tableaux de cet article fournissent des informations sur les fournisseurs de connectivité ExpressRoute, ExpressRoute de couverture géographique, les services de cloud Microsoft pris en charge sur ExpressRoute et ExpressRoute (intégrateurs système).

## <a name="partners"></a>Fournisseurs de connexion ExpressRoute

ExpressRoute est pris en charge dans toutes les régions Azure et emplacements. La liste suivante fournit une liste de régions Azure et d’emplacements de ExpressRoute. ExpressRoute emplacements se réfèrent à ceux où Microsoft homologues avec plusieurs fournisseurs de services.

![Carte d’emplacements][0]

Vous aurez accès à des services Azure dans toutes les régions au sein d’une région géopolitique si vous connecté au moins un emplacement de ExpressRoute dans la zone géopolitique. Le tableau suivant fournit un mappage des régions Azure à des emplacements de ExpressRoute dans une région géopolitique.

|**Région géopolitique**|**Régions Azure**|**ExpressRoute emplacements**|
|---|---|---|
|**Amérique du Nord**|Les États-Unis, États-Unis Ouest, américains Extrême-Orient 2, États-Unis centre, États-Unis centre sud, États-Unis centre nord, Canada Central, Canada est|Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, + de Montréal, Québec +, Toronto|
|**Amérique du Sud**|Sud du Brésil|Sao Paulo|
|**Europe**|Europe du Nord, Europe de l’ouest, Royaume-Uni ouest, sud du Royaume-Uni|Amsterdam, Dublin, Londres, Newport(Wales) +, Paris|
|**Asie**|Asie de l’est, Asie du Sud-est|Hong Kong, Singapour|
|**Japon**|Japon ouest, au Japon est|Osaka, Tokyo|
|**Australie**|Sud-est de l’Australie, est de l’Australie|Melbourne, Sydney|
|**Inde**|Ouest de l’Inde, du Sud de l’Inde centrale, Inde|Chennai, Mumbai|



Le tableau ci-dessous fournit des informations sur des limites géopolitiques et les régions de nuages nationales.

|**Région géopolitique**|**Régions Azure**|**ExpressRoute emplacements**|
|---|---|---|---|
|**Nuage du gouvernement américain**|Virginie de Gov US Gov Iowa, Etats-Unis|Chicago, Dallas, New York, Washington DC|
|**Chine**|Chine du Nord, est de la Chine|Pékin, Shanghai|
|**Allemagne**|Allemagne Central, Allemagne orientale|Berlin, Francfort|


Connectivité entre régions géopolitiques n’est pas pris en charge sur le SKU ExpressRoute standard. Vous devez activer le module complémentaire à la prime ExpressRoute prendre en charge la connectivité globale. Connectivité pour les environnements de cloud national n’est pas pris en charge. Si un tel besoin se fait sentir, vous pouvez travailler avec votre fournisseur de connectivité.


## <a name="connectivity-provider-locations"></a>Emplacements fournisseur de connectivité

> [AZURE.SELECTOR]
[Emplacements par fournisseur de](expressroute-locations.md#connectivity-provider-locations)
[les fournisseurs par zone géographique](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>Production Azure
| **Emplacement**  | **Fournisseurs de services** |
|---------------|-----------------------|
| **Amsterdam** | Aryaka réseaux, AT & T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Solutions Internet - connexion de nuage, Interxion, de niveau 3 Communications, Orange, Tata Communications, TeleCity Group, Telenor, Verizon |
| **Atlanta** | Equinix |
| **Chennai** | Communications de TATA |
| **Chicago** | AT & T NetBond, Comcast, Equinix, niveau 3, Zayo groupe de Communications |
| **Dallas** | AT & T NetBond, Cologix, Equinix, niveau 3 Communications, Megaport |
| **Dublin** | COLT, groupe de Telecity |
| **RAS de Hong Kong** | Télécommunications de British Telecom, Chine Global, Equinix, Megaport, Orange, PCCW Global limitée, les Communications de Tata, Verizon |
| **London** | AT & T NetBond, British Telecom, Colt, Equinix, InterCloud, Solutions de Internet - connexion de nuage, Interxion, Jisc, Level 3 Communications, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telenor, Verizon, Vodafone |
| **Las Vegas** | Niveau 3 Communications +, Megaport
| **Los Angeles** | CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **Melbourne** | AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **New York** | Equinix, Megaport, Zayo Group |
| **Newport(Wales) +** | Prochaine génération + données |
| **Montréal** | Cologix + |
| **Mumbai** | Communications de TATA |
| **Osaka** | Equinix, Internet Initiative Japon Inc. - IIJ, NTT Communications, Softbank |
| **Paris** | Interxion, Equinix + |
| **Sao Paulo** | Equinix, Telefonica |
| **Seattle** | Megaport de Equinix, Level 3 Communications, |
| **Silicon Valley** | Réseaux de Aryaka, AT & T NetBond, British Telecom, CenturyLink +, Comcast, Equinix, Level 3 Communications, Orange, Tata, Verizon, Zayo groupe de Communications |
| **Singapour** | Réseaux de Aryaka, AT & T NetBond, British Telecom, Equinix, InterCloud, Megaport, Orange, SingTel, Tata Communications, Verizon |
| **Sydney** | AARNet, AT & T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Orange, Telstra Corporation, Verizon |
| **Tokyo** | Aryaka réseaux, British Telecom, Colt, Equinix, Internet Initiative Japon Inc. - IIJ, NTT Communications, Softbank, Verizon |
| **Toronto** | Cologix, Equinix, Zayo Group |
| **Washington DC** | Réseaux de Aryaka, AT & T NetBond, British Telecom, Comcast, Equinix, InterCloud, niveau 3, Megaport, Orange, Tata Communications, Verizon, Zayo groupe de Communications |

 **+**Indique à venir

### <a name="national-cloud-environments"></a>Environnements de cloud nationales

#### <a name="us-government-cloud"></a>Nuage du gouvernement américain

| **Emplacement**  |**Fournisseurs de services** |
|---------------|--------------------|
| **Chicago** | AT & T, NetBond, Equinix, niveau 3 Communications, Verizon |
| **Dallas** |  Equinix, Verizon + |
| **New York** | Equinix, Level 3 Communications +, Verizon |
| **Washington DC** | AT & T, NetBond, Equinix, niveau 3 Communications, Verizon |

#### <a name="china"></a>Chine

| **Emplacement**  | **Fournisseurs de services** |
|---------------|-----------------------|
| **Pékin** | Télécommunications de Chine |
| **Shanghai** |  Télécommunications de Chine |
Pour plus d’informations, voir [ExpressRoute en Chine](http://www.windowsazure.cn/home/features/expressroute/)

#### <a name="germany"></a>Allemagne

| **Emplacement**  | **Fournisseurs de services** |
|---------------|-----------------------|
| **Berlin** | COLT +, e-abri |
| **Francfort** | COLT, Equinix, Interxion |

## <a name="nonpartners"></a>Connectivité via des fournisseurs de services non répertoriés

Si votre fournisseur de connectivité n’est pas répertorié dans les sections précédentes, vous pouvez toujours créer une connexion.

- Vérifiez auprès de votre fournisseur de connectivité pour voir s’ils sont connectés à un des échanges dans le tableau ci-dessus. Vous pouvez vérifier les liens suivants pour rassembler plus d’informations sur les services offerts par les fournisseurs d’exchange. Plusieurs fournisseurs de connectivité sont déjà connectés à des échanges d’Ethernet.

    - [Nuage de Equinix Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Avoir votre fournisseur de connectivité d’étendre votre réseau à l’emplacement d’homologation de choix.
    - Assurez-vous que votre fournisseur de connectivité s’étend votre connectivity de manière hautement disponible afin qu’il n’y a aucun point de défaillance unique.
- Commander un circuit de ExpressRoute avec l’échange en tant que votre fournisseur de connectivité pour se connecter à Microsoft.
    - Suivez les étapes dans [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) pour configurer la connectivité.

|**Emplacement**|**Exchange**|**Fournisseurs de connectivité**|
|-------------|------------|-------------------------|
| **New York** | Equinix | Lightower |
| **Seattle** | Equinix | Communications de l’Alaska |
| **Silicon Valley** | Equinix | Communications de XO |
| **Singapour** | Equinix | 1CLOUDSTAR |
| **Washington DC** | Equinix | Lightower |

## <a name="expressroute-system-integrators"></a>Intégrateurs de système de ExpressRoute

Activation de la connectivité privée répondre à vos besoins peut se révéler complexe, en fonction de l’échelle de votre réseau. Vous pouvez travailler avec tous les intégrateurs système répertoriés dans le tableau suivant pour vous aider à l’intégration à ExpressRoute.

|**Continent**|**Intégrateurs de systèmes**|
|-------------|---------------------|
| **Asie** | Avanade Inc., OneAs1a|
| **Europe** | Avanade Inc., Dotnet Solutions|
| **NOUS** | Avanade Inc., les Services professionnels de Equinix, Perficient, direction de projet|

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur ExpressRoute, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md).
- Assurez-vous que toutes les conditions préalables sont remplies. Reportez-vous à la section [conditions préalables de ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Carte d’emplacements"
