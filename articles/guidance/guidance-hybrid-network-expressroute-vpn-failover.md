<properties
   pageTitle="Implémentation d’une architecture de réseau hautement disponible hybride | Microsoft Azure"
   description="Comment faire pour implémenter une architecture de réseau de site à site sécurisé qui s’étend sur un réseau virtuel Azure et un réseau local connecté à l’aide de ExpressRoute avec basculement sur incident de la passerelle VPN."
   services="guidance,virtual-network,vpn-gateway,expressroute"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-highly-available-hybrid-network-architecture"></a>Implémentation d’une architecture de réseau hautement disponible hybride

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les méthodes conseillées pour la connexion d’un réseau local à des réseaux virtuels sur Azure à l’aide de ExpressRoute, avec un site-à réseau privé virtuel (VPN) sous la forme d’une connexion de secours. Le flux de trafic entre le réseau local et d’un réseau virtuel Azure (VNet) via une connexion ExpressRoute.  S’il existe une perte de connectivité dans le circuit de ExpressRoute, le trafic est routé via un tunnel VPN IPSec.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Ce modèle utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Scénarios d’utilisation classiques de cette architecture sont les suivantes :

- Applications hybrides où les charges de travail sont répartis entre le réseau de locaux et Azure.

- Applications en cours d’exécution à grande échelle, critiques des charges de travail nécessitant un degré élevé d’évolutivité.

- Installations à grande échelle de sauvegarde et de restauration pour les données qui doivent être enregistrées hors site.

- Gestion des charges de travail de données volumineuses.

- À l’aide d’Azure comme un site de reprise après sinistre.

Notez que si le circuit de ExpressRoute n’est pas disponible, l’itinéraire VPN gère uniquement les connexions privées homologation. Homologation publique et les Microsoft peering connexions passe sur Internet.

## <a name="architecture-diagram"></a>Diagramme d’architecture

>[AZURE.NOTE] Le [service de passerelle de VPN Azure] [ azure-vpn-gateway] implémente deux types de passerelles de réseau virtuel ; Passerelles de réseau virtuel VPN et réseau virtuel ExpressRoute. Tout au long de ce document, le terme *Passerelle VPN* fait référence au service Azure, tout en les phrases *passerelle de réseau virtuel VPN* et *passerelle réseau virtuel de ExpressRoute* sont utilisés pour faire référence aux implémentations de la passerelle VPN et ExpressRoute respectivement.

Le schéma suivant met en évidence les éléments importants de cette architecture :

> Un document Visio qui contient ce diagramme d’architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur « réseau hybride - ER-VPN » page.

![[0]][0]

- **Réseaux virtuels Azure (VNets).** Chaque VNet se trouve dans une seule région Azure et peut héberger plusieurs niveaux d’applications. Niveaux de l’application peut être placés à l’aide de sous-réseaux dans chaque VNet et/ou réseau des groupes de sécurité (NSGs).

- **Réseau local d’entreprise.** Il s’agit d’un réseau d’ordinateurs et de périphériques connectés via un réseau local privé en cours d’exécution au sein d’une organisation.

- **Appareil VPN.** Il s’agit d’un périphérique ou un service qui fournit la connectivité externe au réseau local. L’appareil VPN peut être un périphérique matériel, ou elle peut être une solution de logiciels tels que le routage et le Service d’accès distant (RRAS) de Windows Server 2012.

> [AZURE.NOTE] Pour une liste des équipements pris en charge de VPN et les informations sur la configuration des appareils VPN sélectionnés pour la connexion à une passerelle de VPN Azure, reportez-vous aux instructions pour le périphérique approprié dans la [liste des périphériques VPN pris en charge par Azure][vpn-appliance].

- **Passerelle de réseau virtuel VPN.** La passerelle de réseau virtuel VPN permet le VNet pour se connecter à l’appareil VPN sur le réseau local. La passerelle de réseau virtuel VPN est configurée pour accepter des demandes émanant du réseau local uniquement par l’intermédiaire de l’appareil VPN. Pour plus d’informations, consultez [connecter un réseau local à un réseau virtuel Microsoft Azure][connect-to-an-Azure-vnet].

- **Passerelle de réseau virtuel ExpressRoute.** La passerelle de réseau virtuel ExpressRoute permet le VNet pour se connecter au circuit de ExpressRoute utilisé pour la connexion à votre réseau local.

- **Sous-réseau de passerelle.** Les passerelles de réseau virtuel sont trouvent dans le même sous-réseau.

- **Connexion VPN.** La connexion a des propriétés qui spécifient le type de connexion (IPSec) et la clé partagée avec la solution de VPN local pour crypter le trafic.

- **Circuit de ExpressRoute.** Il s’agit d’une couche de 2 ou de circuit de couche 3 fournis par le fournisseur de connectivité réseau avec Azure jointures sur site via les routeurs de bordure. Le circuit utilise l’infrastructure matérielle géré par le fournisseur de connectivité.

- **Application de nuage de N-couche.** Il s’agit de l’application hébergée dans Azure. Il peut inclure plusieurs niveaux, avec plusieurs sous-réseaux connectés via les équilibreurs de charge Azure. Le trafic de chaque sous-réseau peut être soumis à des règles définies à l’aide de [Groupes de sécurité de réseau Azure][azure-network-security-group](NSGs). Pour plus d’informations, consultez [mise en route avec Microsoft Azure sécurité][getting-started-with-azure-security].

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être configuré de différentes façons. Nous avons fourni un modèle Azure Resource Manager pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir une recommandation ne correspondant pas à un besoin spécifique.

### <a name="vnet-and-gatewaysubnet"></a>VNet et GatewaySubnet

Créez la passerelle réseau virtuel de ExpressRoute et de la passerelle de réseau virtuel VPN dans le même VNet. Cela signifie qu’ils doivent partager le même sous-réseau nommé **GatewaySubnet**

Si le VNet comprend déjà un sous-réseau nommé **GatewaySubnet**, vérifiez qu’il a un /27 ou le plus grand espace d’adressage. Si le sous-réseau existant est trop petit, supprimez-la comme suit, puis créer un autre, comme indiqué dans l’alinéa suivant :

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
```

Si le VNet ne contient-elle pas un sous-réseau nommé **GatewaySubnet**, puis créer un nouveau comme suit :

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.224/27"
$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="vpn-and-expressroute-gateways"></a>Passerelles VPN et ExpressRoute

Vérifiez que votre organisation remplit les [conditions préalables de ExpressRoute] [ expressroute-prereq] pour la connexion à Azure.

Si vous disposez déjà d’une passerelle de réseau virtuel VPN dans votre VNet d’Azure, supprimer, comme indiqué ci-dessous.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
```

Suivez les instructions de [mise en œuvre d’une architecture de réseau hybride avec Azure ExpressRoute] [ implementing-expressroute] pour établir votre connexion ExpressRoute.

Suivez les instructions de [mise en œuvre d’une architecture de réseau hybride avec Azure et sur site VPN] [ implementing-vpn] pour établir votre connexion VPN de passerelle de réseau virtuel.

Après avoir établi les connexions de la passerelle réseau virtuel, tester l’environnement comme suit :

1. Vérifiez que vous pouvez vous connecter à votre réseau local à votre VNet d’Azure.

2. Contactez votre fournisseur pour arrêter la connectivité de ExpressRoute pour le test.

3. Vérifiez que vous pouvez toujours vous connecter à partir de votre réseau local à votre VNet Azure à l’aide de la connexion de passerelle de réseau virtuel VPN.

4. Contactez votre fournisseur de connectivité des ExpressRoute de reestabilish.

## <a name="considerations"></a>Considérations relatives à la

Pour des raisons de ExpressRoute, consultez l' [implémentation d’une Architecture de réseau hybride avec Azure ExpressRoute] [ guidance-expressroute] conseils.

Pour des raisons de VPN de Site à Site, consultez l' [implémentation d’une Architecture de réseau hybride avec Azure et sur site VPN] [ guidance-vpn] conseils.

Général Azure en matière de sécurité, reportez-vous à la section [services de nuage de Microsoft et de la sécurité du réseau][best-practices-security].

## <a name="solution-deployment"></a>Déploiement de la solution

Si vous disposez d’une infrastructure sur site déjà configurée avec un matériel de réseau appropriée, vous pouvez déployer l’architecture de référence en suivant ces étapes :

1. Cliquez avec le bouton droit de la souris sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déployer vers Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy.json)

2. Attendez que le lien pour ouvrir le portail Azure, puis procédez comme suit : 
  - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : cliquez sur **Créer** et saisissez `ra-hybrid-vpn-er-rg` dans la zone de texte.
  - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
  - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
  - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement à effectuer.

4. Cliquez avec le bouton droit de la souris sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déployer vers Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy-expressRouteCircuit.json)

5. Attendez que le lien pour ouvrir le portail Azure, puis entrez, puis procédez comme suit : 
  - Sélectionnez **utilisation existant** dans la section **groupe de ressources** , puis entrez `ra-hybrid-vpn-er-rg` dans la zone de texte.
  - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
  - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
  - Cliquez sur le bouton **achat** .

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[azure-network-security-group]: ../virtual-network/virtual-networks-nsg.md
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[expressroute-prereq]: ../expressroute/expressroute-prerequisites.md
[implementing-expressroute]: ./guidance-hybrid-network-expressroute.md#implementing-this-architecture
[implementing-vpn]: ./guidance-hybrid-network-vpn.md#implementing-this-architecture
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn]: ./guidance-hybrid-network-vpn.md
[best-practices-security]: ../best-practices-network-security.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-vpn-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-vpn.parameters.json
[virtualnetworkgateway-expressroute-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-expressRoute.parameters.json
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[naming conventions]: ./guidance-naming-conventions.md
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/hybrid-network-expressroute-vpn-failover.png "Architecture d’une architecture de réseau hybride hautement disponible à l’aide de la passerelle VPN et ExpressRoute"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
