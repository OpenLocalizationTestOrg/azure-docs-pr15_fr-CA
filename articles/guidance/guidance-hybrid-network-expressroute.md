<properties
   pageTitle="Implémentation d’une Architecture de réseau hybride avec Azure ExpressRoute | Architecture de référence | Microsoft Azure"
   description="Comment faire pour implémenter une architecture de réseau de site à site sécurisé qui s’étend sur un réseau virtuel Azure et un réseau local connecté à l’aide de ExpressRoute d’Azure."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-expressroute"></a>Implémentation d’une architecture de réseau hybride avec Azure ExpressRoute

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les méthodes conseillées pour la connexion d’un réseau local à des réseaux virtuels sur Azure à l’aide de ExpressRoute. ExpressRoute les connexions sont effectuées à l’aide d’une connexion privée dédiée via un fournisseur de connectivité de tiers. La connexion privée étend votre réseau local dans Azure permettant l’accès à votre propre infrastructure IaaS dans Azure, publics les points de terminaison utilisés dans les services PaaS et SaaS d’Office 365 services. Ce document se concentre sur l’utilisation de ExpressRoute pour se connecter à un seul Azure réseau virtuel (VNet) à l’aide de ce que l'on appelle une homologation privé.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Ce modèle utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Scénarios d’utilisation classiques de cette architecture sont les suivantes :

- Applications hybrides où les charges de travail sont répartis entre le réseau de locaux et Azure.

- Applications en cours d’exécution à grande échelle, critiques des charges de travail nécessitant un degré élevé d’évolutivité.

- Installations à grande échelle de sauvegarde et de restauration pour les données qui doivent être enregistrées hors site.

- Gestion des charges de travail de données volumineuses.

- À l’aide d’Azure comme un site de reprise après sinistre.

> [AZURE.NOTE] La [présentation technique de ExpressRoute] [ expressroute-technical-overview] fournit une introduction à ExpressRoute.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le schéma suivant met en évidence les éléments importants de cette architecture :

> Un document Visio qui contient ce diagramme d’architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur la page « Hybride réseau - ER ».

![[0]][0]

- **Réseaux virtuels Azure (VNets).** Chaque VNet se trouve dans une seule région Azure et peut héberger plusieurs niveaux d’applications. Niveaux de l’application peut être placés à l’aide de sous-réseaux dans chaque VNet et/ou réseau des groupes de sécurité (NSGs). 

- **Services publics Azure.** Il s’agit des services Azure qui peuvent être utilisés dans une application hybride. Ces services sont également disponibles sur l’Internet public, mais y accéder via un circuit de ExpressRoute fournit une latence faible et des performances plus prévisibles étant donné que le trafic ne passe pas par Internet. Connexions sont effectuées à l’aide d' **homologation public**, avec les adresses qui sont détenus par votre organisation ou fournis par votre fournisseur de connectivité. 

- **Services d’Office 365.** Ce sont les applications de Office 365 accessibles et les services fournis par Microsoft. Les connexions sont effectuées à l’aide de **l’homologation de Microsoft**, avec des adresses qui sont détenus par votre organisation ou fournis par votre fournisseur de connectivité.

>[AZURE.NOTE] Vous pouvez également vous connecter directement à Microsoft CRM Online via une homologation de Microsoft.

- **Réseau local d’entreprise.** Il s’agit d’un réseau d’ordinateurs et de périphériques connectés via un réseau local privé en cours d’exécution au sein d’une organisation.

- **Routeurs locaux.** Ce sont les routeurs qui se connectent au réseau local pour le circuit géré par le fournisseur. En fonction de la manière dont votre connexion est configurée, vous devez fournir les adresses IP publiques utilisés par les routeurs. 

- **Routeurs de bord de Microsoft.** Il s’agit de deux routeurs dans une configuration à haute disponibilité actif. Ces routeurs permettent à un fournisseur de connectivité connecter leurs circuits directement à leur centre de données. En fonction de la manière dont votre connexion est configurée, vous devez fournir les adresses IP publiques utilisés par les routeurs.

- **Circuit de ExpressRoute.** Il s’agit d’une couche de 2 ou de circuit de couche 3 fournis par le fournisseur de connectivité réseau avec Azure jointures sur site via les routeurs de bordure. Le circuit utilise l’infrastructure matérielle géré par le fournisseur de connectivité.

- **Fournisseurs de connectivité.** Il s’agit de sociétés qui fournissent une connexion à l’aide de couche 2 ou la connectivité de couche 3 entre votre centre de données et un centre de données Azure.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être configuré de différentes façons. Nous avons fourni un modèle Azure Resource Manager pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir une recommandation ne correspondant pas à un besoin spécifique.

### <a name="connectivity-providers"></a>Fournisseurs de connectivité

Sélectionnez un fournisseur de connectivité ExpressRoute approprié pour votre emplacement. Pour obtenir une liste de fournisseurs de connectivité disponibles sur votre site, utilisez la commande Azure PowerShell suivante :

```powershell
Get-AzureRmExpressRouteServiceProvider
```

Fournisseurs de connectivité des ExpressRoute, votre centre de données connectent à Microsoft, dans l’une des manières suivantes :

- **Se trouver à un échange de nuage.** Si vous êtes co-existent dans une installation avec un échange de nuage, vous pouvez commander des traverses virtuels vers le nuage Microsoft via exchange d’Ethernet du fournisseur de la colocalisation. Les fournisseurs de colocalisation peuvent offrir des traverses de couche 2 soit géré de couche 3 entre connexions entre votre infrastructure dans les installations de colocalisation et le nuage Microsoft...

- **Connexions Ethernet de point à point.** Vous pouvez vous connecter à vos centres de données de locaux/distants vers le nuage Microsoft via des liaisons Ethernet de point à point. Fournisseurs de Ethernet de point à point peuvent offrir des connexions de couche 2, ou gestion des connexions de couche 3 entre votre site et le nuage Microsoft.

- **Réseaux à tout (IPVPN).** Vous pouvez intégrer votre réseau étendu avec le nuage de Microsoft. Fournisseurs de IPVPN (généralement des VPN MPLS) offrent une connectivité à tout entre vos sites distants et les centres de données. Le cloud Microsoft peut être interconnecté pour votre réseau étendu pour qu’elle ressemble à l’instar des autres succursales. Fournisseurs de réseau étendus offrent généralement gérée connectivité de couche 3.

Pour plus d’informations sur les fournisseurs de connectivité, consultez [les circuits de ExpressRoute et de domaines de routage][connectivity-providers].

### <a name="expressroute-circuit"></a>Circuit de ExpressRoute

Assurez-vous que votre organisation a atteint les [conditions préalables de ExpressRoute] [ expressroute-prereqs] pour la connexion à Azure.

Si vous ne l’avez pas déjà fait, ajoutez un sous-réseau nommé `GatewaySubnet` pour votre VNet d’Azure et créer une passerelle de réseau virtuel ExpressRoute en utilisant le service de passerelle de VPN Azure. Pour plus d’informations sur ce processus, reportez-vous à la section [ExpressRoute de flux de travail pour la mise en service du circuit et de l’état des circuits][ExpressRoute-provisioning].

Créer un circuit de ExpressRoute comme suit :

1. Exécutez la commande PowerShell suivante :

    ```powershell
    New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```

2. Envoyer le `ServiceKey` pour le nouveau circuit au fournisseur de services.

3. Attendez que le fournisseur de mise en service du circuit. Vous pouvez vérifier l’état de mise en service d’un circuit à l’aide de la commande PowerShell suivante :

    ```powershell
    Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    ```

Le `Provisioning state` de la `Service Provider` section de la sortie ne pourra `NotProvisioned` à `Provisioned` lorsque le circuit est prêt.

>[AZURE.NOTE]Si vous utilisez une connexion de couche 3, le fournisseur doit configurer et gérer le routage vous fournissez les informations nécessaires pour activer le fournisseur mettre en œuvre les itinéraires appropriés.

Si vous utilisez une connexion de couche 2, procédez comme suit :

1. Réserver deux/30 sous-réseaux constitué des adresses IP publiques valides pour chaque type d’homologation vous souhaitez implémenter. Ces 30 sous-réseaux va être utilisée pour fournir des adresses IP pour les routeurs utilisés pour le circuit. Si vous implémentez private, public et homologation de Microsoft, vous devez les sous-réseaux de 6 /30 avec des adresses IP publiques valides.     

2. Configurer le routage pour le circuit de ExpressRoute. Vous devez exécuter la commande ci-dessous pour chaque type d’homologation que vous souhaitez configurer (public, privé et Microsoft).

    >[AZURE.NOTE]Consultez [créer et modifier la gamme pour un circuit de ExpressRoute] [ configure-expressroute-routing] pour plus de détails. Pour ajouter un réseau homologation le routage du trafic, utilisez les commandes PowerShell suivantes :

    ```powershell
    Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
    ```

3. Réserver un autre pool des adresses IP publiques valides à utiliser pour NAT pour le public et l’homologation de Microsoft. Il est recommandé d’avoir un pool différent pour chaque homologation. Spécifier le pool à votre fournisseur de connectivité, les ils peuvent configurer les publications de BGP pour les plages.

[Lier votre VNet(s) privé dans le nuage pour le circuit de ExpressRoute][link-vnet-to-expressroute]. Utilisez les commandes PowerShell suivantes :

```
$circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

Notez les points suivants :

- ExpressRoute utilise le protocole BGP (Border Gateway) pour échanger des informations de routage entre votre réseau et d’Azure.

- Vous pouvez connecter plusieurs VNets situés dans des régions différentes pour le même circuit ExpressRoute tant que tous les VNets et le circuit de ExpressRoute se trouvent dans la même région géopolitique.

## <a name="scalability-considerations"></a>Évolutivité

Circuits de ExpressRoute fournissent un chemin d’accès de la bande passante élevée entre les réseaux. En règle générale, plus la bande passante plus le coût. Même si certains fournisseurs vous permettent de modifier votre bande passante, assurez-vous de que sélectionner une bande passante initiale qui dépasse de vos besoins et fournit l’espace pour la croissance. Dans le cas où vous avez besoin augmenter la bande passante à l’avenir, vous vous retrouvez avec deux options.

Augmenter la bande passante. N’oubliez pas que tous les fournisseurs vous permettent de le faire dynamiquement. Et d’éviter d’avoir à effectuer ce autant que possible. Mais, si nécessaire, après vérification auprès de votre fournisseur, exécutez les commandes ci-dessous.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Vous pouvez augmenter la bande passante sans perte de la connectivité. Mise à niveau de la bande passante entraînera une interruption de connectivité. Vous devez supprimer le circuit et la recréer avec la nouvelle configuration.

Si vous utilisez la référence Standard pour ExpressRoute et nécessaire pour mettre à niveau vers Premium ou modifier vous êtes votre prix plan (contrôlé ou illimité), exécutez les commandes ci-dessous. Le `Sku.Tier` propriété peut être `Standard` ou `Premium`; le `Sku.Name` propriété peut être `MeteredData` ou `UnlimitedData`.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

$ckt.Sku.Tier = "Premium"
$ckt.Sku.Family = "MeteredData"
$ckt.Sku.Name = "Premium_MeteredData"

 Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

>[AZURE.IMPORTANT] Assurez-vous que le `Sku.Name` correspond à la propriété du `Sku.Tier` et `Sku.Family`. Si vous modifiez la famille et de couche, mais pas le nom, la connexion sera désactivée.

Vous pouvez mettre à jour la référence SKU sans interruption de service, mais vous ne pouvez pas passer le plan de tarification illimité à mesurer. Lors de la mise à niveau de la référence SKU, votre consommation de bande passante doit rester dans la limite par défaut du point de stock standard.

> [AZURE.NOTE] ExpressRoute propose deux plans de tarification aux clients, en fonction des données de mesure ou illimitées. Consultez [tarification d’ExpressRoute] [ expressroute-pricing] pour plus de détails. Les tarifs varient en fonction de la bande passante du circuit. La bande passante disponible probablement peuvent varier d’un fournisseur. Utilisez le `Get-AzureRmExpressRouteServiceProvider` applet de commande pour afficher les fournisseurs disponibles dans votre région et de la largeur de bande qu’ils proposent.

Un circuit de ExpressRoute unique peut prendre en charge un certain nombre de liens de VNet et de peerings. Voir les [limites de ExpressRoute] [ expressroute-limits] pour plus d’informations.

Pour un coût supplémentaire, ExpressRoute prime complémentaire fournit :

- Itinéraires jusqu'à 10 000 par circuit. Sans ExpressRoute complémentaire, la limite est de 4 000 itinéraires par circuit.

- Connectivité globale, l’activation d’un circuit de ExpressRoute situé n’importe où dans le monde d’accéder aux ressources dans toute région plutôt que dans des régions uniquement dans le même continent.

- Une augmentation du nombre de liens de VNet par circuit de 10 à une limite supérieure, en fonction de la bande passante du circuit.

Circuits de ExpressRoute sont conçus pour permettre des pics de réseau temporaire jusqu'à deux fois la limite de bande passante que vous a achetées pour sans coût supplémentaire. Pour ce faire, à l’aide de liens redondants. Toutefois, tous les fournisseurs de connexion prennent en charge cette fonctionnalité ; Vérifiez que votre fournisseur de connectivité permet cette fonctionnalité avant lui.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Vous pouvez configurer la haute disponibilité pour votre connexion Azure de différentes manières, selon le type de fournisseur que vous utilisez et le nombre de circuits de ExpressRoute et de connexions de passerelle réseau virtuel que vous souhaitez configurer. Les points ci-dessous résument vos options de disponibilité :

- ExpressRoute ne prend pas en charge protocoles de redondance de routeur tels que HSRP et VRRP pour assurer une haute disponibilité. Au lieu de cela, il utilise une paire de sessions BGP par homologation redondants. Pour faciliter les connexions à votre réseau hautement disponible, Microsoft Azure dispositions avec deux ports redondants sur les deux routeurs (partie de l’arête de Microsoft) dans une configuration active-active.

- Si vous utilisez une connexion de couche 2, déployer des routeurs redondants dans votre réseau local dans une configuration active-active. Le circuit primaire de se connecter à un routeur et le circuit secondaire à l’autre. Cela vous donnera une connexion hautement disponible aux deux extrémités de la connexion. Ceci est nécessaire si vous avez besoin du SLA ExpressRoute. Consultez le [contrat SLA pour Azure ExpressRoute] [ sla-for-expressroute] pour plus de détails.

Le diagramme suivant affiche une configuration avec des routeurs redondants sur site connecté à circuits principales et secondaires. Chaque circuit gère le trafic pour une homologation publique et à une homologation privé (chaque homologation est désigné une paire de /30 adresse des espaces, comme décrit dans la section précédente).

![[1]][1]

Si vous utilisez une connexion de couche 3, vérifiez qu’il fournit redondante BGP sessions qui gèrent la disponibilité pour vous.

Les réseaux virtuels peuvent être connectés à plusieurs circuits ExpressRoute et chaque circuits peuvent être fournis par les fournisseurs de services différents. Cette stratégie fournit des capacités de récupération plue haute disponibilité et reprise après sinistre.

Configurer un VPN de Site à Site sous la forme d’un chemin de basculement sur incident pour ExpressRoute. Cela est uniquement applicable aux peering privé. Pour les services Azure et Office 365, Internet est le chemin d’accès de basculement uniquement.

Par défaut, les sessions BGP utilisent une valeur de délai d’inactivité de 60 secondes. Une fois qu’une session est dépassé 3 fois, l’itinéraire est marqué comme non disponible, et tout le trafic est redirigé vers le routeur en restant. Ce délai de 180 secondes peut être trop long pour les applications critiques. Dans ce cas, vous pouvez modifier les paramètres de délai d’attente BGP sur le routeur local à une valeur inférieure.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Vous pouvez utiliser [Azure connectivité Shared Computer Toolkit (AzureCT)] [ azurect] pour contrôler la connectivité entre votre centre de données sur site et l’Azure.

## <a name="security-considerations"></a>Considérations sur la sécurité

Vous pouvez configurer les options de sécurité pour votre connexion Azure de différentes façons, en fonction de vos problèmes de sécurité et les exigences de conformité. Les points ci-dessous résument vos options de sécurité.

ExpressRoute fonctionne de la couche 3. Menaces dans la couche d’Application peuvent être évités à l’aide d’une Appliance de sécurisation de réseau qui limite le trafic aux ressources légitimes. En outre, ExpressRoute les connexions à l’aide d’homologation public ne peuvent être lancées que sur site. Cela empêche un service non autorisé d’accéder à et de compromettre des données sur site à partir de l’Internet public.

Pour une sécurité optimale, ajouter des appliances de sécurisation de réseau entre le réseau local et les routeurs de bordure de fournisseur. Cela vous permet de limiter l’entrée de trafic non autorisé à partir de la VNet :

![[2]][2]

À des fins d’audit ou de respect de la réglementation, il peut être nécessaire interdire l’accès direct à partir de composants en cours d’exécution dans le VNet pour Internet et implémenter [forcé tunneling][forced-tuneling]. Dans cette situation, le trafic Internet doit être redirigé via un proxy en cours d’exécution sur les locaux où il peut être audité. Le proxy peut être configuré pour bloquer le trafic non autorisé qui circulent à et filtrer le trafic potentiellement malveillant.

![[3]][3]

Par défaut, Azure VM exposer des points de terminaison utilisés pour fournir un accès de connexion à des fins de gestion - RDP et Powershell à distance pour Windows VMs et SSH pour les machines virtuelles basés sur Linux déployés via le portail Azure. Pour optimiser la sécurité, ne pas activer une adresse IP publique pour vos ordinateurs virtuels et utiliser NSGs pour vous assurer que ces ordinateurs virtuels ne sont pas accessibles au public. Machines virtuelles ne doivent être disponibles à l’aide de l’adresse IP interne. Ces adresses peuvent être accessibles via le réseau ExpressRoute, le personnel sur site DevOps effectuer la configuration nécessaire ni maintenance.

Si vous devez exposer des points de terminaison de gestion pour les machines virtuelles à un réseau externe, utilisez NSGs et/ou le contrôle d’accès pour restreindre la visibilité de ces ports à une liste d’autorisation des adresses IP ou des réseaux.

## <a name="troubleshooting-considerations"></a>Considérations relatives à la résolution des problèmes

Si un circuit ExpressRoute précédemment fonctionne maintenant ne parvient pas à se connecter, en l’absence de toute configuration modifications sur site ou dans votre VNet privé, vous devrez peut-être contacter le fournisseur de connectivité et de travailler avec eux pour corriger le problème. Vous pouvez également utiliser les commandes de PowerShell d’Azure suivantes pour effectuer une vérification limitée et vous aider à déterminer où potentielle des problèmes :

- Vérifiez que le circuit a été mis en service :

```powershell
Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

La sortie de cette commande montre plusieurs propriétés pour votre circuit, y compris les `ProvisioningState`, `CircuitProvisioningState`, et `ServiceProviderProvisioningState` comme indiqué ci-dessous.

```
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
```

Si le `ProvisioningState` n’est pas définie `Succeeded` une fois que vous avez essayé de créer un nouveau circuit, supprimer le circuit en utilisant la commande ci-dessous et essayez à nouveau de le créer.

```powershell
Remove-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

Si votre fournisseur a mis déjà en service le circuit et le `ProvisioningState` a `Failed`, ou `CircuitProvisioningState` n’est pas `Enabled`, contactez votre fournisseur pour obtenir de l’aide.

## <a name="solution-deployment"></a>Déploiement de la solution

Si vous disposez d’une infrastructure sur site déjà configurée avec un matériel de réseau appropriée, vous pouvez déployer l’architecture de référence en suivant ces étapes :

Si vous disposez d’une infrastructure sur site déjà configurée avec une solution de VPN, vous pouvez déployer l’architecture de référence en suivant ces étapes :

1. Cliquez avec le bouton droit de la souris sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déployer vers Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy.json)

2. Attendez que le lien pour ouvrir le portail Azure, puis procédez comme suit : 
  - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : cliquez sur **Créer** et saisissez `ra-hybrid-er-rg` dans la zone de texte.
  - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
  - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
  - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement à effectuer.

4. Cliquez avec le bouton droit de la souris sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déployer vers Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy-expressRouteCircuit.json)

5. Attendez que le lien pour ouvrir le portail Azure, puis procédez comme suit : 
  - Sélectionnez **utilisation existant** dans la section **groupe de ressources** , puis entrez `ra-hybrid-er-rg` dans la zone de texte.
  - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
  - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
  - Cliquez sur le bouton **achat** .

6. Attendez que le déploiement à effectuer.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [implémentation d’une architecture de réseau hautement disponible hybride] [ highly-available-network-architecture] pour plus d’informations sur l’augmentation de la disponibilité d’un réseau hybride selon ExpressRoute en basculant sur une connexion VPN.

<!-- links -->
[expressroute-technical-overview]: ../expressroute/expressroute-introduction.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[expressroute-prereqs]: ../expressroute/expressroute-prerequisites.md
[configure-expressroute-routing]: ../expressroute/expressroute-howto-routing-arm.md
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute/v1_0/
[link-vnet-to-expressroute]: ../expressroute/expressroute-howto-linkvnet-arm.md
[ExpressRoute-provisioning]: ../expressroute/expressroute-workflows.md
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: ../azure-subscription-service-limits.md#networking-limits
[sample-script]: #sample-solution-script
[connectivity-providers]: ../expressroute/expressroute-introduction.md#how-can-i-connect-my-network-to-microsoft-using-expressroute
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[forced-tuneling]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[highly-available-network-architecture]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[arm-templates]: ../resource-group-authoring-templates.md
[naming-conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetworkGateway.parameters.json
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/guidance-hybrid-network-expressroute/figure1.png "Architecture de réseau hybride à l’aide de ExpressRoute d’Azure"
[1]: ./media/guidance-hybrid-network-expressroute/figure2.png "À l’aide de routeurs redondants avec circuits de ExpressRoute primaires et secondaires"
[2]: ./media/guidance-hybrid-network-expressroute/figure3.png "Ajout de périphériques de sécurité au réseau local"
[3]: ./media/guidance-hybrid-network-expressroute/figure4.png "À l’aide de forcé de tunneling pour auditer le trafic de liaison Internet"
[4]: ./media/guidance-hybrid-network-expressroute/figure5.png "Localisation de la clé de service d’un circuit de ExpressRoute"
