<properties
   pageTitle="Mise en œuvre d’une architecture de réseau sécurisée hybride dans Azure | Microsoft Azure"
   description="Comment implémenter une architecture de réseau sécurisée hybride dans Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
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

# <a name="implementing-a-dmz-between-azure-and-your-on-premises-datacenter"></a>Mise en œuvre d’un réseau de périmètre entre Azure et votre centre de données sur site

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les méthodes conseillées pour l’implémentation d’un réseau sécurisé hybride qui étend un réseau local vers Azure. Cette architecture de référence implémente un réseau de périmètre entre un réseau local et d’un réseau virtuel Azure utilisant des itinéraires définis par l’utilisateur (UDRs). La zone DMZ inclut les appliances virtuelles réseau hautement disponible (NVAs) qui implémentent la fonctionnalité de sécurité tels que les pare-feux et inspection des paquets. Tout le trafic sortant à partir de la VNet est la force acheminés via un tunnel à Internet via le réseau local afin qu’il peut être audité. 

Cette architecture nécessite une connexion à votre centre de données local implémenté à l’aide d’une [passerelle VPN][ra-vpn], ou une [ExpressRoute] [ ra-expressroute] connexion.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Scénarios d’utilisation classiques de cette architecture sont les suivantes :

- Les applications hybride dans lequel exécuter des charges de travail partiellement sur site et partiellement dans Azure.

- Infrastructure Azure qui achemine le trafic entrant à partir de locaux et internet.

- Applications requises pour auditer le trafic sortant. Cela est souvent une exigence réglementaire de nombreux systèmes commerciaux et peut aider à empêcher la divulgation des informations confidentielles.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le schéma suivant met en évidence les éléments importants de cette architecture :

> Un document Visio qui contient ce diagramme d’architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur la page « DMZ – « privé ».

[! [0]][0]

- **Réseau de locaux.** Il s’agit d’un réseau d’ordinateurs et de périphériques connectés via un privé réseau local mis en œuvre dans une organisation.

- **Réseau virtuel Azure (VNet).** Le VNet héberge l’application et autres ressources en cours d’exécution dans le nuage.

- **Passerelle.** La passerelle offre une connectivité entre les routeurs sur le réseau local et le VNet.

- **Matériel de réseau virtuel (à couloirs).** À couloirs est un terme générique qui décrit une machine virtuelle exécutant des tâches comme autoriser ou refuser l’accès sous la forme d’un pare-feu, optimisation des opérations WAN (y compris la compression de réseau), routage personnalisé ou autres fonctionnalités réseau. 

- **Couche Web, couche d’entreprise et sous-réseaux de couche de données.** Il s’agit des sous-réseaux hébergeant les ordinateurs virtuels et les services qui implémentent un exemple d’application à 3 niveaux en cours d’exécution dans le nuage. Voir [Exécutant des machines virtuelles Windows d’une architecture à N niveaux sur Azure] [ ra-n-tier] pour plus d’informations.

- **Itinéraires définis par l’utilisateur (UDR).** [Itinéraires définis par l’utilisateur] [ udr-overview] définir le flux du trafic IP dans Azure VNets. 

> [AZURE.NOTE] Selon les besoins de votre connexion VPN, vous pouvez configurer des itinéraires de protocole BGP (Border Gateway) en guise d’alternative à l’utilisation de UDRs pour implémenter les règles de transmission qui dirigent le trafic de sauvegarde via le réseau local.

- **Sous-réseau de gestion.** Ce sous-réseau contient les ordinateurs virtuels qui implémentent les fonctions de gestion et surveillance pour les composants exécutés dans le VNet. 

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être configuré de différentes façons. Nous avons fourni un modèle Azure Resource Manager pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir une recommandation ne correspondant pas à un besoin spécifique.

### <a name="rbac-recommendations"></a>Recommandations de RBAC

Créer plusieurs rôles RBAC pour gérer les ressources de votre application. Envisagez de créer un [rôle personnalisé] de DevOps[ rbac-custom-roles] avec des autorisations pour gérer l’infrastructure de l’application. Pensez à créer un administrateur [de rôle personnalisé] centralisé[ rbac-custom-roles] pour gérer les ressources réseau et une [rôle personnalisé] administrateur de sécurité distinct[ rbac-custom-roles] pour gérer les ressources de réseau sécurisé tel que les NVAs. 

Le rôle de DevOps doit inclure des autorisations pour déployer les composants d’application ainsi qu’à surveiller et redémarrez les ordinateurs virtuels. Le rôle d’administrateur informatique centralisé doit inclure des autorisations pour contrôler les ressources réseau. Aucun de ces rôles doivent avoir accès aux ressources à couloirs comme il doit être limité à la sécurité du rôle d’administrateur informatique.

### <a name="resource-group-recommendations"></a>Recommandations du groupe ressources

Ressources Azure VM, VNets et équilibreurs de charge peuvent être facilement gérés en les regroupant dans les groupes de ressources. Vous pouvez ensuite affecter les rôles ci-dessus pour chaque groupe de ressources pour limiter l’accès.

Nous vous recommandons de la création des éléments suivants :

- Un groupe de ressources contenant les sous-réseaux (à l’exclusion des ordinateurs virtuels), NSGs et les ressources de la passerelle pour la connexion au réseau local. Affecter le rôle d’administrateur informatique centralisé à ce groupe de ressources.

- Un groupe de ressources contenant les ordinateurs virtuels pour l’UDR pour le sous-réseau de passerelle qui force tout le trafic via les NVAs les NVAs (y compris l’équilibreur de charge), la zone passer et autres machines de gestion virtuelles. Affecter la rôle administrateur de la sécurité à ce groupe de ressources.

- Groupes de ressources distincts pour chaque couche d’application qui contiennent l’équilibreur de charge et les ordinateurs virtuels. Notez que ce groupe de ressources ne doit pas inclure les sous-réseaux pour chaque couche. Attribuer le rôle de DevOps à ce groupe de ressources.

### <a name="virtual-network-gateway-recommendations"></a>Recommandations de passerelle réseau virtuel

Trafic de local passe à le VNet via une passerelle de réseau virtuel. Nous vous recommandons une [passerelle VPN d’Azure] [ guidance-vpn-gateway] ou une [passerelle d’Azure ExpressRoute][guidance-expressroute]. 

### <a name="nva-recommendations"></a>Recommandations à couloirs

NVAs fournissent différents services pour la gestion et la surveillance du trafic réseau. Le marché Azure propose plusieurs NVAs de fournisseur tiers, y compris :

- [Barracuda Web Application pare-feu] [ barracuda-waf] et [Barracuda NextGen pare-feu][barracuda-nf]

- [VNS3 réseaux cohésive pare-feu/routeur/VPN][vns3]

- [Fortinet FortiGate-VM][fortinet]

- [Pare-feu pour applications Web de SecureSphere][securesphere]

- [Pare-feu pour applications Web de DenyAll][denyall]

- [VSEC de Point de vérification][checkpoint]

Si aucun de ces NVAs tiers répond à vos besoins, vous pouvez créer un à des couloirs personnalisé à l’aide d’ordinateurs virtuels. Pour obtenir un exemple de création de NVAs personnalisés, consultez la zone DMZ dans cette architecture de référence qui implémente les fonctionnalités suivantes :

- Le trafic est routé à l’aide du [routage IP] [ ip-forwarding] sur les cartes réseau à couloirs.

- Le trafic est autorisé à passer par l’à couloirs uniquement s’il est approprié de le faire. Chaque VM NVA dans l’architecture de référence est un simple routeur Linux avec du trafic arrivant sur le réseau interface *eth0*et les règles de correspondance de trafic sortant définies par des scripts personnalisés distribués via l' interface de réseau *eth1*. 

- Le trafic acheminé vers le sous-réseau de gestion ne passe pas par les NVAs et les NVAs ne peuvent être configurés à partir du sous-réseau de gestion. Si le trafic vers le sous-réseau de gestion doit être routé par l’intermédiaire des NVAs, il n’existe aucun itinéraire vers le sous-réseau de gestion pour corriger le NVAs si elles doivent échouer.  

- Les ordinateurs virtuels pour l’à couloirs sont inclus dans une disponibilité définie derrière un équilibreur de charge. Le UDR dans le sous-réseau de passerelle dirige les requêtes d’à couloirs à l’équilibreur de charge.

Un autre recommandation à prendre en compte se connecte à plusieurs NVAs de série avec chaque à couloirs effectue une tâche de sécurité spécialisés. Ainsi, chaque fonction de sécurité d’être géré sur une base par-à couloirs. Par exemple, un à couloirs mise en œuvre d’un pare-feu peut être placé en série avec un à couloirs exécutant les services d’identité. Le compromis pour une facilité de gestion est l’ajout de sauts de réseau supplémentaires qui peuvent augmenter la latence, assurez-vous que cela n’affecte pas les performances de votre application.

### <a name="nsg-recommendations"></a>Recommandations du NSG

La passerelle VPN expose une adresse IP publique pour la connexion au réseau local. Nous vous recommandons de créer un groupe de sécurité réseau (NSG) pour les entrants à couloirs sous-réseau modalités d’application bloquer le trafic ne provenant ne pas de réseau local.

Nous vous recommandons également implémenter NSGs pour chaque sous-réseau afin de fournir un second niveau de protection contre le trafic entrant en ignorant une à des couloirs mal configuré ou désactivé. Par exemple, le sous-réseau de la couche web dans l’architecture de référence implémente un NSG avec une règle pour ignorer toutes les requêtes autres que celles provenant du réseau local (192.168.0.0/16) ou le VNet et une autre qui ignore toutes les requêtes ne faites pas sur le port 80. 

### <a name="internet-access-recommendations"></a>Recommandations d’accès Internet

[Tunnel-force] [ azure-forced-tunneling] tranlation d’adresses (NAT) de tout le trafic internet sortant via votre réseau local en utilisant le tunnel VPN de site à site et un itinéraire vers l’internet à l’aide de réseau. Cela les empêche la fuite accidentelle des informations confidentielles stockées dans votre couche de données et permettent également d’inspection et audit de tout le trafic sortant.

> [AZURE.NOTE] Ne pas bloquer complètement du trafic Internet dans les couches web, business et application. Si ces niveaux utilisent services Azure PaaS qu’ils reposent sur des adresses IP publiques pour l’enregistrement des diagnostics de machine virtuelle, le téléchargement d’extensions de la machine virtuelle et d’autres fonctionnalités. Diagnostics de Windows Azure requiert également que les composants puissent lire et écrire à un compte internet dépendant des stockage Azure.

Nous vous conseillons également de vérifier le trafic internet sortant est en vigueur-tunnel correctement. Si vous utilisez une connexion VPN avec le [service d’accès distant et de routage] [ routing-and-remote-access-service] sur un serveur local, utilisez un outil tel que [WireShark] [ wireshark] ou [l’Analyseur de Message de Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=44226).

### <a name="management-subnet-recommendations"></a>Recommandations de sous-réseau de gestion

Le sous-réseau de gestion contient une zone de saut qui exécute la fonctionnalité de surveillance et de gestion. Mettre en œuvre les recommandations suivantes pour la zone passer :
- Ne créez pas une adresse IP publique de la zone de lien. 
- Créez un itinéraire pour accéder à la zone passer par l’intermédiaire de la passerelle entrante et mettre en œuvre un NSG dans le sous-réseau de gestion pour répondre uniquement aux demandes à partir de la gamme autorisée.
- Restreindre l’exécution de toutes les tâches de gestion sécurisée à la zone de lien.

### <a name="nva-recommendations"></a>Recommandations à couloirs

Inclure une couche 7 à couloirs pour terminer les connexions des applications au niveau à couloirs et conserve les affinités avec les couches principales. Cela garantit la connectivité symétrique dans lequel le trafic de réponse dans les couches principales renvoie via l’à couloirs.

## <a name="scalability-considerations"></a>Évolutivité

L’architecture de référence implémente l’acheminement du trafic de réseau local à un pool de périphériques d’à couloirs un équilibreur de charge. Comme indiqué plus haut, les périphériques à couloirs sont des ordinateurs virtuels de l’exécution des règles de routage de trafic réseau et sont déployées dans un [jeu de disponibilité][availability-set]. Cette conception vous permet de surveiller le débit des NVAs dans le temps et ajouter les périphériques à couloirs en réponse à des augmentations de la charge.

La passerelle SKU VPN standard prend en charge le débit soutenu de jusqu'à 100 Mbits/s. La haute Performance offre jusqu'à 200 Mbits/s. De largeur de bande plus élevée, envisagez la mise à niveau vers une passerelle de ExpressRoute. ExpressRoute fournit la bande passante de 10 Gbits/s avec une latence plus faible qu’une connexion VPN.

> [AZURE.NOTE] Les articles de [mise en œuvre d’une Architecture de réseau hybride avec Azure et sur site VPN] [ guidance-vpn-gateway] et [mise en œuvre d’une architecture de réseau hybride avec Azure ExpressRoute] [ guidance-expressroute] décrivent des problèmes liés à l’évolutivité des passerelles Azure.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

L’architecture de référence implémente un équilibreur de charge distribuant les requêtes en local à un pool de périphériques à couloirs dans Azure. Les périphériques à couloirs sont des ordinateurs virtuels de l’exécution des règles de routage de trafic réseau et sont déployées dans un [jeu de disponibilité][availability-set]. Régulièrement, l’équilibreur de charge interroge une sonde de santé mis en œuvre sur chaque à couloirs et supprimera toute NVAs ne répond pas à partir du pool. 

Si vous utilisez des ExpressRoute d’Azure pour fournir la connectivité entre le réseau VNet et sur site, [configurez une passerelle VPN pour assurer le basculement] [ guidance-vpn-failover] si la connexion de ExpressRoute n’est plus disponible.

Pour plus d’informations sur la gestion de disponibilité pour les connexions VPN et ExpressRoute, consultez les articles [mise en œuvre d’une Architecture de réseau hybride avec Azure et sur site VPN] [ guidance-vpn-gateway] et [mise en œuvre d’une architecture de réseau hybride avec Azure ExpressRoute][guidance-expressroute].

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Toutes les applications et l’analyse de ressource doivent être effectuées par la zone de passer dans le sous-réseau de gestion. En fonction des besoins de votre application, vous devrez peut-être ajouter des ressources supplémentaires de surveillance dans le sous-réseau de gestion, mais une nouvelle fois une de ces ressources supplémentaires doivent être accessibles via la zone passer.

Si la connectivité de la passerelle du réseau local vers Azure est en panne, vous pouvez toujours atteindre la zone passer en déployant un PIP, en ajoutant à la zone passer et l’accès distant dans à partir d’internet.

Sous-réseau de chaque couche de l’architecture de référence est protégé par les règles NSG et il peut être nécessaire de créer une règle pour ouvrir le port 3389 pour l’accès RDP sur les ordinateurs virtuels de Windows ou 22 pour l’accès SSH sur des machines virtuelles de Linux. Gestion des outils de surveillance peuvent nécessiter des règles pour ouvrir des ports supplémentaires.

Si vous utilisez ExpressRoute pour fournir la connectivité entre votre centre de données sur site et l’Azure, utiliser [Azure connectivité Shared Computer Toolkit (AzureCT)] [ azurect] pour analyser et dépanner les problèmes de connexion.

> [AZURE.NOTE] Vous trouverez des informations supplémentaires visant spécifiquement les surveiller et de gérer des connexions VPN et ExpressRoute dans les articles de [mise en œuvre d’une Architecture de réseau hybride avec Azure et sur site VPN] [ guidance-vpn-gateway] et [mise en œuvre d’une architecture de réseau hybride avec Azure ExpressRoute][guidance-expressroute].

## <a name="security-considerations"></a>Considérations sur la sécurité

Cette architecture de référence implémente plusieurs niveaux de sécurité : 

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a>Routage de toutes les demandes d’utilisateur local via l’à couloirs

Le UDR dans le sous-réseau de passerelle bloque toutes les demandes des utilisateurs autres que ceux provenant des locaux. Les passes UDR autorisé des demandes aux NVAs dans le sous-réseau privé de la DMZ et ces demandes sont transmis à l’application si elles sont autorisées par les règles à couloirs. Autres itinéraires peuvent être ajoutés à la UDR et veiller à ce qu’ils n’ignore par inadvertance de la NVAs ou bloquer le trafic d’administration conçu pour le sous-réseau de gestion.

L’équilibreur de charge devant les NVAs joue également un dispositif de sécurité en ignorant le trafic sur les ports qui ne sont pas ouverts dans la règles d’équilibrage de la charge. N’écoutent que les équilibreurs de charge dans l’architecture de référence pour les demandes HTTP sur le port 80 et les demandes HTTPS sur le port 443. Des règles supplémentaires pour les équilibreurs de charge doivent être documentés et le trafic doit être contrôlé pour vous assurer qu’aucun problème de sécurité.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>À l’aide de NSGs à bloc/passe le trafic entre les niveaux de l’application

Chacune des couches web, business et données de restreindre le trafic entre eux à l’aide de NSGs. La couche d’entreprise utilise un NSG pour bloquer tout le trafic qui ne provient pas de la couche web et la couche de données utilise un NSG à bloquer tout le trafic qui ne provient pas du niveau de l’entreprise. Si vous avez besoin pour développer les règles NSG pour permettre un accès plus large à ces niveaux, peser ces exigences contre les risques de sécurité. Chaque nouvelle voie d’accès entrant représente une opportunité de dommages de fuites ou à l’application de données accidentelle ou intentionnelle.

### <a name="devops-access"></a>DevOps accès

Restreindre les opérations DevOps peut effectuer sur chaque couche de l’utilisation de [RBAC] [ rbac] pour gérer les autorisations. Lorsque vous accordez des autorisations, utilisez le [principe du moindre privilège][security-principle-of-least-privilege]. Journal de toutes les opérations d’administration et effectuez des audits réguliers pour vérifier les que modifications de configuration ont été planifiées.

> [AZURE.NOTE] Pour la plus complète des informations, des exemples et des scénarios à propos de la gestion de la sécurité réseau avec Azure, reportez-vous à la section [sécurité réseau et services de cloud de Microsoft][cloud-services-network-security]. Pour plus d’informations sur la protection des ressources dans le nuage, consultez [mise en route avec Microsoft Azure sécurité][getting-started-with-azure-security]. Pour plus d’informations sur les questions de sécurité via une connexion de passerelle Azure, consultez [implémentation d’une Architecture de réseau hybride avec Azure et sur site VPN] [ guidance-vpn-gateway] et [mise en œuvre d’une architecture de réseau hybride avec Azure ExpressRoute][guidance-expressroute].

## <a name="solution-deployment"></a>Déploiement de la solution

Un déploiement d’une architecture de référence qui implémente ces recommandations est disponible sur Github. Cette architecture de référence comprend un réseau virtuel (VNet), le groupe de sécurité réseau (NSG), équilibrage de la charge et deux machines virtuelles (VM).

L’architecture de référence peut être déployée avec Windows ou Linux VM en suivant les instructions ci-dessous : 

1. Cliquez avec le bouton droit de la souris sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déployer vers Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet%2Fazuredeploy.json)

2. Une fois le lien ouvert dans Azure portal, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : cliquez sur **Créer** et saisissez `ra-private-dmz-rg` dans la zone de texte.
    - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
    - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
    - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement à effectuer.

4. Les fichiers de paramètres incluent les nom d’utilisateur codé en dur l’administrateur et le mot de passe pour tous les ordinateurs virtuels, et il est fortement recommandé que vous modifiez immédiatement à la fois. Pour chaque ordinateur virtuel dans le déploiement, sélectionnez-le dans le portail Azure, puis sur **Réinitialiser le mot de passe** dans la lame **de prise en charge + de résolution des problèmes** . Sélectionnez **Réinitialiser le mot de passe** dans la zone de liste déroulante **en Mode** , puis sélectionnez un nouveau **nom d’utilisateur** et un **mot de passe**. Cliquez sur le bouton de **mise à jour** pour rendre persistantes.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à implémenter une [DMZ entre Azure et Internet](./guidance-iaas-ra-secure-vnet-dmz.md).
- Apprenez à implémenter une [architecture de réseau hybride hautement disponible](./guidance-hybrid-network-expressroute-vpn-failover.md).

<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-create-availability-set.md
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[barracuda-nf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/barracuda-ng-firewall/
[barracuda-waf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/
[checkpoint]: https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/
[cloud-services-network-security]: https://azure.microsoft.com/documentation/articles/best-practices-network-security/
[denyall]: https://azure.microsoft.com/marketplace/partners/denyall/denyall-web-application-firewall/
[fortinet]: https://azure.microsoft.com/marketplace/partners/fortinet/fortinet-fortigate-singlevmfortigate-singlevm/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[ip-forwarding]: ../virtual-network/virtual-networks-udr-overview.md#IP-forwarding
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[ra-n-tier]: ./guidance-compute-n-tier-vm.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[rbac-custom-roles]: ../active-directory/role-based-access-control-custom-roles.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[securesphere]: https://azure.microsoft.com/marketplace/partners/imperva/securesphere-waf-for-azr/
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vns3]: https://azure.microsoft.com/marketplace/partners/cohesive/cohesiveft-vns3-for-azure/
[wireshark]: https://www.wireshark.org/
[0]: ./media/blueprints/hybrid-network-secure-vnet.png "Architecture de réseau hybride sécurisée"
