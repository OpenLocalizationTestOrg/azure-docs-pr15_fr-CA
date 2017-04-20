<properties
   pageTitle="Plan de réseau virtuel Azure (VNet) et le Guide de conception | Microsoft Azure"
   description="Découvrez comment planifier et concevoir des réseaux virtuels dans Azure en fonction de vos besoins en matière d’isolation, de connectivité et d’emplacement."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/08/2016"
   ms.author="jdial" />

# <a name="plan-and-design-azure-virtual-networks"></a>Planification et conception de réseaux virtuels d’Azure

Création d’un VNet pour expérimenter est assez facile, mais sans doute, vous allez déployer plusieurs VNets au fil du temps pour prendre en charge les besoins de la production de votre organisation. Avec une planification et de conception, vous serez en mesure de déployer les VNets et connecter les ressources dont vous avez besoin plus efficacement. Si vous n’êtes pas familiarisé avec les VNets, il est recommandé que vous [en savoir plus sur les VNets](virtual-networks-overview.md) et [comment déployer](virtual-networks-create-vnet-arm-pportal.md) une avant de continuer. 

## <a name="plan"></a>Plan de

Une connaissance approfondie des abonnements Azure, les régions et les ressources réseau est essentielle pour la réussite. Vous pouvez utiliser la liste de considérations ci-dessous comme point de départ. Une fois que vous comprenez ces considérations, vous pouvez définir les conditions requises pour la conception de votre réseau.

### <a name="considerations"></a>Considérations relatives à la

Avant de répondre à la planification des questions ci-dessous, considérez les points suivants :

- Tout ce que vous créez dans Azure se compose d’une ou plusieurs ressources. Une machine virtuelle (VM) est une ressource, l’interface de carte réseau (NIC) utilisée par un ordinateur virtuel est une ressource, l’adresse IP publique utilisée par une carte réseau est une ressource, le VNet de la carte réseau est connectée à est une ressource.
- Vous créez des ressources au sein d’une [région d’Azure](https://azure.microsoft.com/regions/#services) et d’abonnement. Et ressources peuvent uniquement être connectés à un VNet qui existe dans la même région et abonnement, dans qu'ils sont. 
- VNets peut se connecter à l’autre à l’aide d’une [Passerelle VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)d' Azure. Vous pouvez également connecter des VNets entre les régions et les abonnements de cette manière.
- VNets pouvez vous connecter à votre réseau local à l’aide d’une des [options de connectivité](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponibles dans Azure. 
- Des ressources différentes peuvent être regroupés dans des [groupes de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups), rendant ainsi plus facile de gérer la ressource en tant qu’unité. Un groupe de ressources peut contenir des ressources provenant de plusieurs régions, dans la mesure où les ressources appartiennent à la même abonnement.

### <a name="define-requirements"></a>Définition des impératifs

Utilisez les questions ci-dessous comme point de départ pour la conception de votre réseau d’Azure.  

1. Quels emplacements Azure utilisera à l’hôte VNets ?
2. Vous avez besoin assurer la communication entre ces emplacements Azure ?
3. Vous avez besoin assurer la communication entre votre VNet(s) Azure et vos centres locaux ?
4. Le nombre sous la forme d’un Service (IaaS) VM, cloud services d’Infrastructure rôles et effectuez des applications web que vous avez besoin pour votre solution ?
5. Vous avez besoin isoler le trafic en fonction des groupes d’ordinateurs virtuels (c'est-à-dire avant serveurs web et serveurs de base de données back-end) ?
6. Vous avez besoin contrôler le flux de trafic à l’aide de boîtiers virtuels ?
7. Les utilisateurs ont-ils besoin de différents jeux d’autorisations à différentes ressources Azure ?

### <a name="understand-vnet-and-subnet-properties"></a>Comprendre les propriétés VNet et sous-réseau

Ressources de VNet et de sous-réseaux aident à définir une limite de sécurité pour les charges de travail en cours d’exécution dans Azure. Un VNet est caractérisée par une collection d’espaces d’adressage, défini en tant que blocs CIDR. 

>[AZURE.NOTE] Les administrateurs réseau sont familiarisés avec la notation CIDR. Si vous n’êtes pas familiarisé avec CIDR, [en savoir plus sur elle](http://whatismyipaddress.com/cidr).

VNets contient les propriétés suivantes.

|Propriété|Description|Contraintes|
|---|---|---|
|**nom**|Nom de VNet|Chaîne de 80 caractères. Peut contenir des lettres, nombres, trait de soulignement, de points ou des traits d’union. Doit commencer par une lettre ou un chiffre. Doit se terminer par une lettre, un nombre ou un trait de soulignement. Pouvez contient la lettre majuscule ou minuscule.|  
|**emplacement**|Emplacement Azure (également appelé zone).|Doit être un des emplacements Azure valides.|
|**à EMC**|Collection de préfixes d’adresses qui composent le VNet dans la notation CIDR.|Doit être un tableau de blocs d’adresses CIDR valides, y compris les plages d’adresses IP publiques.|
|**sous-réseaux**|Collection des sous-réseaux qui composent le VNet|consultez le tableau de propriétés du sous-réseau ci-dessous.||
|**dhcpOptions**|Objet qui contient une seule propriété obligatoire nommée **dnsServers**.||
|**dnsServers**|Tableau des serveurs DNS utilisés par le VNet. Si aucun serveur n’est spécifié, la résolution des noms internes Azure est utilisée.|Doit être un tableau de jusqu'à 10 serveurs DNS par adresse IP.| 

Un sous-réseau est une ressource enfant d’une VNet, et permettant de définir les segments d’espaces d’adressage dans un bloc CIDR, à l’aide de préfixes d’adresses IP. Cartes réseau peut être ajoutées à des sous-réseaux et connectés aux ordinateurs virtuels, la fourniture de connectivité sur des charges de travail différentes.

Sous-réseaux contient les propriétés suivantes. 

|Propriété|Description|Contraintes|
|---|---|---|
|**nom**|Nom du sous-réseau|Chaîne de 80 caractères. Peut contenir des lettres, nombres, trait de soulignement, de points ou des traits d’union. Doit commencer par une lettre ou un chiffre. Doit se terminer par une lettre, un nombre ou un trait de soulignement. Pouvez contient la lettre majuscule ou minuscule.|
|**emplacement**|Emplacement Azure (également appelé zone).|Doit être un des emplacements Azure valides.|
|**addressPrefix**|Préfixe d’adresse unique qui constituent le sous-réseau en notation CIDR|Doit être un seul bloc CIDR qui fait partie de l’un des espaces d’adressage de la VNet.|
|**networkSecurityGroup**|NSG appliquée au sous-réseau|voir [NSGs](resource-groups-networking.md#Network-Security-Group)|
|**routeTable**|Table de routage appliquée au sous-réseau|reportez-vous à la section [UDR](resource-groups-networking.md#Route-table)|
|**ipConfigurations**|Collection d’objets de configuration IP utilisée par la carte réseau connectée au sous-réseau|reportez-vous à la section [configuration IP](../resource-groups-networking.md#IP-configurations)|

### <a name="name-resolution"></a>Résolution de noms

Par défaut, utilise votre VNet [la résolution de noms fournis par Azure.](virtual-networks-name-resolution-for-vms-and-role-instances.md#Azure-provided-name-resolution) Pour résoudre les noms à l’intérieur de la VNet et sur l’Internet public. Toutefois, si vous vous connectez vos VNets à vos centres de données local, vous devez fournir [votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#Name-resolution-using-your-own-DNS-server) pour résoudre les noms entre vos réseaux.  

### <a name="limits"></a>Limites

Passez en revue les limites de mise en réseau dans l’article [Azure limite](../azure-subscription-service-limits.md#networking-limits) pour vous assurer que votre conception n’entrent en conflit avec les limites. Certaines limites peuvent être augmentées en ouvrant un ticket de support.

### <a name="role-based-access-control-rbac"></a>Contrôle d’accès basé sur les rôles (RBAC)

Vous pouvez utiliser [Azure RBAC](../active-directory/role-based-access-built-in-roles.md) pour contrôler le niveau d’accès des différents utilisateurs peuvent auprès de ressources différentes dans Azure. De cette façon, que vous pouvez répartir le travail effectué par votre équipe, en fonction de leurs besoins. 

Dans le cadre des réseaux virtuels sont concernés, les utilisateurs dans le rôle **Collaborateur du réseau** ont un contrôle total sur les ressources du réseau virtuel Azure le Gestionnaire de ressources. De même, les utilisateurs dans le rôle **Collaborateur du réseau classique** ont un contrôle total sur les ressources classique de réseau virtuel.

>[AZURE.NOTE] Vous pouvez également [créer vos propres rôles](../active-directory/role-based-access-control-configure.md) pour séparer vos besoins d’administration.

## <a name="design"></a>Conception

Une fois que vous connaissez les réponses aux questions de la section [Plan](#Plan) , passez en revue les éléments suivants avant de définir votre VNets.

### <a name="number-of-subscriptions-and-vnets"></a>Nombre d’abonnements et de VNets

Vous devez envisager de créer plusieurs VNets dans les scénarios suivants :

- **Ordinateurs virtuels qui doivent être placés à différents endroits d’Azure**. VNets dans Azure sont régionaux. Ils ne peuvent pas englober des emplacements. Par conséquent, vous devez au moins un VNet pour chaque emplacement Azure aux ordinateurs virtuels hôtes.
- **Charges de travail qui doivent être complètement isolées des unes des autres**. Vous pouvez créer des VNets distincts, même en utilisant les mêmes espaces d’adressage IP, d’isoler les différentes charges de travail à partir d’un autre. 

Gardez à l’esprit que les limites de que vous voir ci-dessus sont par région, par abonnement. Cela signifie que vous pouvez utiliser plusieurs abonnements pour augmenter la limite des ressources, que vous pouvez mettre à jour dans Azure. Vous pouvez utiliser un réseau VPN de site à site, ou un circuit ExpressRoute, se VNets dans les différentes souscriptions.

### <a name="subscription-and-vnet-design-patterns"></a>Abonnement et les modèles de conception VNet

Le tableau ci-dessous présente quelques modèles de conception courants pour l’utilisation d’abonnements et VNets.

|Scénario|Diagramme|Professionnels de l’informatique|Inconvénients|
|---|---|---|---|
|Seul abonnement, deux VNets par application|![Seul abonnement](./media/virtual-network-vnet-plan-design-arm/figure1.png)|Qu’un seul abonnement à gérer.|Nombre maximal de VNets par région Azure. Vous avez besoin de plusieurs abonnements par la suite. Consultez l’article [Azure limite](../azure-subscription-service-limits.md#networking-limits) pour plus de détails.|
|Un seul abonnement par application, deux VNets par application|![Seul abonnement](./media/virtual-network-vnet-plan-design-arm/figure2.png)|Utilise uniquement deux VNets par abonnement.|Plus difficile à gérer lorsqu’il y a beaucoup trop d’applications.|
|Un seul abonnement par centre de profit, deux VNets par l’application.|![Seul abonnement](./media/virtual-network-vnet-plan-design-arm/figure3.png)|Équilibre entre le nombre d’abonnements et de VNets.|Nombre maximal de VNets par division (abonnement). Consultez l’article [Azure limite](../azure-subscription-service-limits.md#networking-limits) pour plus de détails.|
|Abonnement à un par centre de profit, deux VNets par groupe d’applications.|![Seul abonnement](./media/virtual-network-vnet-plan-design-arm/figure4.png)|Équilibre entre le nombre d’abonnements et de VNets.|Les applications doivent être isolées à l’aide de sous-réseaux et NSGs.|


### <a name="number-of-subnets"></a>Nombre de sous-réseaux

Vous devez envisager plusieurs sous-réseaux dans un VNet dans les scénarios suivants :

- **Pas suffisamment d’adresses IP privées pour toutes les cartes réseau dans un sous-réseau**. Si votre espace d’adressage de sous-réseau ne contient-elle pas suffisamment d’adresses IP pour le nombre de cartes réseau dans le sous-réseau, vous devez créer plusieurs sous-réseaux. N’oubliez pas que Azure réserve 5 adresses IP privées à partir de chaque sous-réseau ne peut pas être utilisée : les première et la dernières adresses de l’espace d’adressage (pour l’adresse de sous-réseau et la multidiffusion) et 3 à utiliser en interne (pour des raisons de DHCP et DNS). 
- **Sécurité**. Vous pouvez utiliser des sous-réseaux pour séparer des groupes d’ordinateurs virtuels à partir d’un autre pour les charges de travail ayant une structure multicouche et s’appliquent à différents [groupes de sécurité réseau (NSGs)](virtual-networks-nsg.md#subnets) pour ces sous-réseaux.
- **Connectivité des hybrides**. Vous pouvez utiliser des passerelles VPN et circuits de ExpressRoute pour [se connecter](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) de votre VNets à un autre et à votre center(s) de données sur site. Les passerelles VPN et circuits de ExpressRoute nécessitent un sous-réseau de leurs propres à créer.
- **Les appliances virtuelles**. Vous pouvez utiliser un matériel virtuel, tel qu’un pare-feu, un accélérateur WAN ou une passerelle VPN dans un VNet d’Azure. Lorsque vous procédez ainsi, vous avez besoin pour [acheminer le trafic](virtual-networks-udr-overview.md) vers les appareils et les Isolez dans leur propre sous-réseau.

### <a name="subnet-and-nsg-design-patterns"></a>Les modèles de conception NSG et de sous-réseau

Le tableau ci-dessous présente quelques modèles de conception courants pour l’utilisation de sous-réseaux.

|Scénario|Diagramme|Professionnels de l’informatique|Inconvénients|
|---|---|---|---|
|Sous-réseau unique, NSGs par la couche d’application, par l’application|![Sous-réseau unique](./media/virtual-network-vnet-plan-design-arm/figure5.png)|Qu’un seul sous-réseau à gérer.|Plusieurs NSGs sont nécessaires pour isoler chaque application.|
|Un sous-réseau par application, NSGs par la couche d’application|![Sous-réseau par application](./media/virtual-network-vnet-plan-design-arm/figure6.png)|NSGs de moins à gérer.|Plusieurs sous-réseaux à gérer.|
|Un sous-réseau par la couche d’application, NSGs par l’application.|![Sous-réseau par couche](./media/virtual-network-vnet-plan-design-arm/figure7.png)|Équilibre entre le nombre de sous-réseaux et NSGs.|Nombre maximal de NSGs par abonnement. Consultez l’article [Azure limite](../azure-subscription-service-limits.md#networking-limits) pour plus de détails.|
|Un sous-réseau par la couche d’application, par l’application, NSGs par sous-réseau|![Sous-réseau par couche par application](./media/virtual-network-vnet-plan-design-arm/figure8.png)|Éventuellement plus petit nombre de NSGs.|Plusieurs sous-réseaux à gérer.|

## <a name="sample-design"></a>Exemple de conception

Pour illustrer l’application des informations de cet article, envisagez le scénario suivant.

Vous travaillez pour une société qui possède 2 centres de données en Amérique du Nord et deux centres de données en Europe. Vous avez identifié 6 applications vis-à-vis des clients différents maintenues par 2 différentes centres de profit que vous souhaitez migrer vers Azure sous la forme d’un projet pilote. L’architecture de base pour les applications sont les suivantes :

- App4, App3, App1 et App2 sont des applications web hébergées sur des serveurs Linux Ubuntu en cours d’exécution. Chaque application connecte à un serveur d’application distincte qui héberge les services RESTful sur des serveurs Linux. Les services RESTful se connectent à une base de données back-end.
- App5 et App6 sont des applications web hébergées sur les serveurs Windows qui exécutent Windows Server 2012 R2. Chaque application se connecte à une base de données de SQL Server back-end.
- Toutes les applications sont actuellement hébergées dans un des centres de données de la société en Amérique du Nord.
- Les centres de données locaux utilisent l’espace d’adresses 10.0.0.0/8.

Vous devez concevoir une solution de réseau virtuel qui répond aux exigences suivantes :

- Chaque centre de profit ne doit pas affecté par la consommation de ressources d’autres divisions.
- Vous devez réduire le montant de VNets et de sous-réseaux pour faciliter la gestion.
- Chaque unité doit avoir un seul test/développement VNet utilisée pour toutes les applications.
- Chaque application est hébergée dans 2 centres de données Azure différentes par continent (Amérique du Nord et Europe).
- Chaque application est totalement isolée des autres.
- Chaque application est accessible par les clients sur Internet à l’aide de HTTP.
- Chaque application est accessible par des utilisateurs connectés pour les centres de données en local à l’aide d’un tunnel chiffré.
- Connexion aux centres de données sur site doit utiliser les périphériques VPN existantes.
- Groupe de mise en réseau de la société doit avoir un contrôle total sur la configuration de VNet.
- Les développeurs de chaque centre de profit doivent uniquement être en mesure de déployer des machines virtuelles à un sous-réseau existant.
- Toutes les applications seront migrées comme ils sont vers Azure (ascenseur et MAJ).
- Les bases de données dans chaque emplacement doivent répliquer vers d’autres emplacements Azure une fois par jour.
- Chaque application doit utiliser les serveurs web front-end 5, 2 serveurs d’application (si nécessaire) et 2 serveurs de base de données.

### <a name="plan"></a>Plan de

Vous devez commencer la conception de votre planification en répondant à la question dans la section [Configuration requise de définir](#Define-requirements) comme indiqué ci-dessous.

1. Quels emplacements Azure utilisera à l’hôte VNets ?

    2 emplacements en Amérique du Nord et 2 emplacements en Europe. Vous devez choisir celles qui sont basées sur l’emplacement physique de vos centres de données local existant. De cette façon, votre connexion de vos emplacements physiques vers Azure aura une latence plue.

2. Vous avez besoin assurer la communication entre ces emplacements Azure ?

    Oui. Dans la mesure où les bases de données doivent être répliquées sur tous les emplacements.

3. Vous devez center(s) de données pour la communication entre votre VNet(s) Azure et vos locaux ?

    Oui. Dans la mesure où les utilisateurs connectés aux centres de données sur site doivent être en mesure d’accéder aux applications via un tunnel chiffré.
 
4. Machines virtuelles de IaaS Combien avez-vous besoin pour votre solution ?

    200 IaaS VMs. App1 et App2 de App3 requièrent chaque 5 serveurs web, chaque 2 serveurs d’applications et les serveurs de base de données 2. Qui est un total de 9 IaaS VMs, par application ou 36 IaaS VMs. App5 et App6 requièrent 5 serveurs web et les serveurs de base de données 2. Qui est un total de 7 IaaS VMs, par application ou 14 IaaS VMs. Vous devez donc 50 IaaS ordinateurs virtuels pour toutes les applications dans chaque région Azure. Étant donné que nous devons utiliser 4 régions, il y aura 200 IaaS VMs.

    Vous devrez également fournir les serveurs DNS dans chaque VNet ou dans vos centres de données sur site pour résoudre le nom entre vos ordinateurs virtuels IaaS de Azure et votre réseau local. 

5. Vous avez besoin isoler le trafic en fonction des groupes d’ordinateurs virtuels (c'est-à-dire avant serveurs web et serveurs de base de données back-end) ?

    Oui. Chaque application doit être complètement isolée des unes des autres, et chaque couche d’application doit également être isolé. 

6. Vous avez besoin contrôler le flux de trafic à l’aide de boîtiers virtuels ?

    N° Appliances virtuelles permet de mieux contrôler le flux du trafic, y compris l’enregistrement de plan de données plus détaillée. 

7. Les utilisateurs ont-ils besoin de différents jeux d’autorisations à différentes ressources Azure ?

    Oui. L’équipe réseau a besoin d’un contrôle total sur les paramètres de mise en réseau virtuels, alors que les développeurs doivent être uniquement en mesure de déployer leurs machines virtuelles à des sous-réseaux préexistants. 

### <a name="design"></a>Conception

Vous devez suivre la conception spécifiant des NSGs, des VNets, des sous-réseaux et des abonnements. Nous aborderons ici NSGs, mais vous devez en savoir plus sur les [NSGs](virtual-networks-nsg.md) avant de terminer votre conception.

**Nombre d’abonnements et de VNets**

Les exigences suivantes sont liés aux abonnements et VNets :

- Chaque centre de profit ne doit pas affecté par la consommation de ressources d’autres divisions.
- Vous devez réduire le montant de VNets et de sous-réseaux.
- Chaque unité doit avoir un seul test/développement VNet utilisée pour toutes les applications.
- Chaque application est hébergée dans 2 centres de données Azure différentes par continent (Amérique du Nord et Europe).

En fonction de ces exigences, vous ont besoin d’un abonnement pour chaque centre de profit. De cette façon, la consommation de ressources à partir d’un centre de profit pas comptera dans limites pour d’autres unités de l’entreprise. Et, dans la mesure où vous souhaitez réduire le nombre de VNets, vous devez envisager d’utiliser le modèle **d’un abonnement par centre de profit, deux VNets par groupe d’applications** comme indiqué ci-dessous.

![Seul abonnement](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Vous devez également spécifier l’espace d’adressage pour chaque VNet. Étant donné que vous devez centres de connectivité entre les données locales et les régions d’Azure, l’espace d’adressage utilisé pour Azure VNets ne peut pas entrer en conflit avec le réseau local et l’espace d’adressage utilisé par chaque VNet ne doit pas entrer en conflit avec autres VNets existant. Vous pouvez utiliser les espaces d’adressage dans le tableau ci-dessous pour satisfaire ces exigences.  

|**Abonnement**|**VNet**|**Région Azure**|**Espace d’adressage**|
|---|---|---|---|
|BU1|ProdBU1US1|États-Unis Ouest|172.16.0.0/16|
|BU1|ProdBU1US2|Les États-Unis|172.17.0.0/16|
|BU1|ProdBU1EU1|Europe du Nord|172.18.0.0/16|
|BU1|ProdBU1EU2|Europe de l’ouest|172.19.0.0/16|
|BU1|TestDevBU1|États-Unis Ouest|172.20.0.0/16|
|BU2|TestDevBU2|États-Unis Ouest|172.21.0.0/16|
|BU2|ProdBU2US1|États-Unis Ouest|172.22.0.0/16|
|BU2|ProdBU2US2|Les États-Unis|172.23.0.0/16|
|BU2|ProdBU2EU1|Europe du Nord|172.24.0.0/16|
|BU2|ProdBU2EU2|Europe de l’ouest|172.25.0.0/16|

**Nombre de sous-réseaux et de NSGs**

Les exigences suivantes sont liées à des sous-réseaux et NSGs :

- Vous devez réduire le montant de VNets et de sous-réseaux.
- Chaque application est totalement isolée des autres.
- Chaque application est accessible par les clients sur Internet à l’aide de HTTP.
- Chaque application est accessible par des utilisateurs connectés pour les centres de données en local à l’aide d’un tunnel chiffré.
- Connexion aux centres de données sur site doit utiliser les périphériques VPN existantes.
- Les bases de données dans chaque emplacement doivent répliquer vers d’autres emplacements Azure une fois par jour.

En fonction de ces exigences, vous pouvez utiliser un sous-réseau par la couche d’application et utiliser NSGs pour filtrer le trafic par application. De cette façon, vous ne devez 3 sous-réseaux dans chaque VNet (front-end, couche application et couche de données) et un NSG par application et par sous-réseau. Dans ce cas, vous devez envisager d’utiliser le modèle de conception **d’un sous-réseau par la couche d’application, NSGs par l’application** . La figure ci-dessous illustre l’utilisation du modèle de conception représentant le VNet **ProdBU1US1** .

![Un sous-réseau par couche, un NSG par application et par couche](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Toutefois, vous devez également créer un sous-réseau supplémentaire pour la connectivité VPN entre le VNets et vos centres de données sur site. Et vous devez spécifier l’espace d’adressage pour chaque sous-réseau. La figure ci-dessous illustre un exemple de solution de VNet de **ProdBU1US1** . Vous devez répliquer ce scénario pour chaque VNet. Chaque couleur représente une autre application.

![Exemple VNet](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Contrôle d’accès**

Les exigences suivantes sont liées au contrôle d’accès :

- Groupe de mise en réseau de la société doit avoir un contrôle total sur la configuration de VNet.
- Les développeurs de chaque centre de profit doivent uniquement être en mesure de déployer des machines virtuelles à un sous-réseau existant.

En fonction de ces exigences, vous pouvez ajouter les utilisateurs de l’équipe de mise en réseau pour le rôle de **Collaborateur de réseau** intégré dans chaque abonnement ; et créer un rôle personnalisé pour les développeurs d’application dans chaque abonnement en leur donnant des droits pour ajouter des ordinateurs virtuels dans des sous-réseaux existants.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un réseau virtuel](virtual-networks-create-vnet-arm-template-click.md) basé sur un scénario.
- Comprendre comment [équilibrer la charge](../load-balancer/load-balancer-overview.md) des ordinateurs virtuels IaaS et [gérer le routage sur plusieurs régions Azure](../traffic-manager/traffic-manager-overview.md).
- En savoir plus sur les [NSGs et comment planifier et concevoir](virtual-networks-nsg.md) une solution NSG.
- Pour en savoir plus sur vos [options de connectivité de VNet et de coexistence](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site).
