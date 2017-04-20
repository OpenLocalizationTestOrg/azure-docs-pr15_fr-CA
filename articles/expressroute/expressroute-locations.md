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

| **Fournisseur de services**  |**Microsoft Azure** | **Office 365 et CRM en ligne** | **Emplacements** |
|-----------------------|--------------------|----------------|---------------|
| **AARNet** | Prise en charge | Prise en charge | Melbourne, Sydney |
| **[Réseaux de Aryaka]( http://www.aryaka.com/)** | Prise en charge | Prise en charge | Amsterdam, Silicon Valley, Singapour, Tokyo, Washington DC |
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Prise en charge | Prise en charge | Amsterdam, Chicago, Dallas, London, Silicon Valley, Singapour, Sydney, Washington DC |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Prise en charge | Prise en charge | Amsterdam, Hong Kong, Londres, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
|**CenturyLink** | À venir | À venir| Silicon Valley |
|**Télécommunications de Chine Global** | Prise en charge | Non pris en charge | RAS de Hong Kong |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | Prise en charge | À venir | Dallas, Montréal +, Toronto |
| **[COLT]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  Prise en charge | Prise en charge | Amsterdam, Dublin, Londres, Tokyo |
| **Comcast** | Prise en charge | Prise en charge | Chicago, Silicon Valley, Washington DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Prise en charge | Prise en charge | Los Angeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Prise en charge | Prise en charge | Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Angeles, Melbourne, New York, Osaka, Paris +, Sao Paulo, Seattle, Silicon Valley, Singapour, Sydney, Tokyo, Toronto, Washington DC |
| **euNetworks** |  Prise en charge | Prise en charge | Amsterdam |
| **GÉANT** | Prise en charge | Prise en charge | Amsterdam |
| **[Internet Initiative Japon Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  Prise en charge | Prise en charge | Osaka, Tokyo |
| **[InterCloud]( https://www.intercloud.com/)** | Prise en charge | Prise en charge | Amsterdam, Londres, Singapour, Washington DC |
| **Connectent des Solutions Internet - nuage** | Prise en charge | Prise en charge | Amsterdam, Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)**  | Prise en charge | Prise en charge | Amsterdam, Londres, Paris |
| **Jisc** | Prise en charge | Prise en charge | London | 
| **[Communications de niveau 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Prise en charge | Prise en charge | Amsterdam, Chicago, Dallas, Las Vegas +, Londres, Seattle, Silicon Valley, Washington DC |
| **Megaport** | Prise en charge | Prise en charge | Dallas, Hong Kong, Las Vegas, Los Angeles, Melbourne, New York, Seattle, Singapour, Sydney, Washington DC |
| **MTN** | Prise en charge | Prise en charge | London |
| **Données de la génération suivante** | À venir | À venir | Newport(Wales) + |
| **NEXTDC** | Prise en charge | Prise en charge | Melbourne, Sydney |
| **NTT Communications** | Prise en charge | Prise en charge | Osaka de Los Angeles, Londres, Tokyo |
| **[Orange]( http://www.orange-business.com/en/products/business-vpn-galerie)** | Prise en charge | Prise en charge | Amsterdam, Hong Kong, London, Silicon Valley, Singapour, Sydney, Washington DC |
| **PCCW Global limité** | Prise en charge | Prise en charge | RAS de Hong Kong |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  Prise en charge | Prise en charge | Singapour |
| **Softbank** | Prise en charge | Prise en charge | Osaka, Tokyo | 
| **[Communications de TATA](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Prise en charge | Prise en charge | Amsterdam, Chennai, Hong Kong, Londres, Mumbai, la société Silicon Valley, Singapour, Washington DC |
| **[Groupe de TeleCity]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Prise en charge | Prise en charge | Amsterdam, Dublin, Londres |
| **Telefonica** | Prise en charge | Prise en charge | Sao Paulo |
| **Telenor** | Prise en charge | Prise en charge | Amsterdam, Londres |
| **[Telstra Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Prise en charge | Prise en charge | Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Prise en charge | Prise en charge | Amsterdam, Hong Kong, Londres, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
| **Vodafone** | Prise en charge | Non pris en charge | London | 
| **[Groupe de Zayo]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Prise en charge | Prise en charge | Chicago, Los Angeles, New York, Silicon Valley, Toronto, Washington DC |

 **+**Indique à venir

### <a name="national-cloud-environments"></a>Environnements de cloud nationales

#### <a name="us-government-cloud"></a>Nuage du gouvernement américain

| **Fournisseur de services**  |**Microsoft Azure** | **Office 365** | **Emplacements** |
|-----------------------|--------------------|----------------|---------------|
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Prise en charge | Prise en charge | Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Prise en charge | Prise en charge | Chicago, Dallas, New York, Washington DC |
| **[Communications de niveau 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Prise en charge | Prise en charge | Chicago, + de New York, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Prise en charge | Prise en charge | Chicago, Dallas +, New York, Washington DC |

#### <a name="china"></a>Chine

| **Fournisseur de services**  |**Microsoft Azure** | **Office 365** | **Emplacements** |
|-----------------------|--------------------|----------------|---------------|
| **Télécommunications de Chine** | Prise en charge | Non pris en charge | Pékin, Shanghai|
Pour plus d’informations, consultez [ExpressRoute en Chine](http://www.windowsazure.cn/home/features/expressroute/).

#### <a name="germany"></a>Allemagne

| **Fournisseur de services**  |**Microsoft Azure** | **Office 365** | **Emplacements** |
|-----------------------|--------------------|----------------|---------------|
| **[COLT]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Prise en charge | Non pris en charge | Berlin +, Francfort|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Prise en charge | Non pris en charge | Francfort|
| **e-abri** | Prise en charge | Non pris en charge | Berlin|
| **Interxion** | Prise en charge | Non pris en charge | Francfort|

## <a name="nonpartners"></a>Connectivité via des fournisseurs de services non répertoriés

Si votre fournisseur de connectivité n’est pas répertorié dans les sections précédentes, vous pouvez toujours créer une connexion.

- Vérifiez auprès de votre fournisseur de connectivité pour voir s’ils sont connectés à un des échanges dans le tableau ci-dessus. Vous pouvez vérifier les liens suivants pour rassembler plus d’informations sur les services offerts par les fournisseurs d’exchange. Plusieurs fournisseurs de connectivité sont déjà connectés à des échanges d’Ethernet.

    - [Nuage de Equinix Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Avoir votre fournisseur de connectivité d’étendre votre réseau à l’emplacement d’homologation de choix.
    - Assurez-vous que votre fournisseur de connectivité s’étend votre connectivity de manière hautement disponible afin qu’il n’y a aucun point de défaillance unique.
- Commander un circuit de ExpressRoute avec l’échange en tant que votre fournisseur de connectivité pour se connecter à Microsoft.
    - Suivez les étapes dans [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) pour configurer la connectivité.

|**Fournisseur de connectivité**|**Exchange**|**Emplacements**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|Singapour|
|**Communications de l’Alaska**|Equinix|Seattle|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure )**|Equinix|New York, Washington DC|
|**[Communications de XO](http://www.xo.com/)**|Equinix|Silicon Valley|


## <a name="expressroute-system-integrators"></a>Intégrateurs de système de ExpressRoute

Activation de la connectivité privée répondre à vos besoins peut se révéler complexe, en fonction de l’échelle de votre réseau. Vous pouvez travailler avec tous les intégrateurs système répertoriés dans le tableau suivant pour vous aider à l’intégration à ExpressRoute.

|**Intégrateur de système**|**Continent**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| Asie, Europe, États-Unis |
|**[Solutions dotnet](http://www.dotnetsolutions.co.uk/)**| Europe |
|**[Services professionnels de Equinix](http://www.equinix.com/services/consulting/)**|NOUS|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Asie |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | NOUS |
|**[Direction du projet](http://www.projectleadership.net/azure)** | NOUS |

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur ExpressRoute, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md).
- Assurez-vous que toutes les conditions préalables sont remplies. Reportez-vous à la section [conditions préalables de ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Carte d’emplacements"
