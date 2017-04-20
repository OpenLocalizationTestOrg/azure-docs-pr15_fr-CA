<properties
   pageTitle="Public et privé IP adressage (classic) dans Azure | Microsoft Azure"
   description="Obtenir des informations sur le public et privé l’adressage IP dans Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="jdial" />

# <a name="ip-addresses-classic-in-azure"></a>Adresses IP (classic) dans Azure
Vous pouvez attribuer des adresses IP aux ressources Azure pour communiquer avec les autres ressources d’Azure, votre réseau local et Internet. Il existe deux types d’adresses IP que vous pouvez utiliser dans Azure : publics et privés.

Les adresses IP publiques sont utilisées pour la communication avec Internet, y compris des services Web public Azure.

Les adresses IP privées sont utilisées pour la communication au sein d’un réseau virtuel Azure (VNet), un service en nuage et votre réseau local lorsque vous utilisez une passerelle VPN ou un circuit de ExpressRoute d’étendre votre réseau sur Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de déploiement du Gestionnaire de ressources](virtual-network-ip-addresses-overview-arm.md).

## <a name="public-ip-addresses"></a>Adresses IP publiques
Les adresses IP publiques permettent de ressources Azure communiquer avec les services Internet et Azure orienté public comme [Cache de Redis d’Azure](https://azure.microsoft.com/services/cache/), [Azure événement concentrateurs](https://azure.microsoft.com/services/event-hubs/), [bases de données SQL](../sql-database/sql-database-technical-overview.md)et le [stockage Azure](../storage/storage-introduction.md).

Une adresse IP publique est associée à des types de ressources suivants :

- Services en nuage
- Machines virtuelles de IaaS (VMs)
- Instances de rôles PaaS
- Passerelles VPN
- Passerelles d’application

### <a name="allocation-method"></a>Méthode de répartition
Lorsqu’une adresse IP publique doit être affectée à une ressource d’Azure, il est *dynamiquement* alloués à partir d’un pool d’une adresse IP publique disponible dans l’emplacement de que la ressource est créée. Cette adresse IP est libérée lorsque la ressource est arrêtée. En cas d’un service en nuage, cela se produit lorsque toutes les instances de rôle sont arrêtés, qui peut être évité en utilisant une adresse IP *statique* (réservé) (voir les [Services en nuage](#Cloud-services)).

>[AZURE.NOTE] La liste de plages IP à partir de laquelle les adresses IP publiques sont affectés à des ressources Azure est publiée dans [les plages IP de centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Résolution de nom d’hôte DNS
Lorsque vous créez un service en nuage ou une VM IaaS, vous devez fournir un nom DNS de service de cloud qui est unique pour toutes les ressources dans Azure. Cette opération crée un mappage dans les serveurs gérés par Azure pour *NomDNS*. cloudapp.net avec l’adresse IP publique de la ressource. Par exemple, lorsque vous créez un service cloud avec un nom DNS de service cloud de **contoso**, le nom de domaine complet **contoso.cloudapp.net** résout une adresse IP publique (VIP) du service nuage. Vous pouvez utiliser ce nom de domaine complet pour créer un enregistrement CNAME de domaine personnalisé qui pointe vers l’adresse IP publique dans Azure.

### <a name="cloud-services"></a>Services en nuage
Un service en nuage a toujours une adresse IP publique à une adresse IP virtuelle (VIP). Vous pouvez créer des points de terminaison dans un service en nuage à associer des ports différents de l’adresse IP virtuelle pour les ports internes sur les machines virtuelles et d’instances de rôle au sein du service de cloud. 

Un service en nuage peut contenir plusieurs IaaS VMs ou instances de rôle PaaS, tous les exposés via la même adresse IP virtuelle du service nuage. Vous pouvez également affecter [plusieurs VIP à un service de cloud](../load-balancer/load-balancer-multivip.md)qui permet des scénarios de multi-VIP comme environnement multi-DETENTEURS avec des sites Web SSL.

Vous pouvez vérifier que l’adresse IP publique d’un service cloud reste la même, même si toutes les instances de rôle sont arrêtés, à l’aide d’une adresse IP publique *statique* , appelée [IP réservée](virtual-networks-reserved-public-ip.md). Vous pouvez créer une ressource IP statique (réservée) dans un emplacement spécifique et l’affecter à n’importe quel service de nuage à cet emplacement. Vous ne pouvez pas spécifier l’adresse IP réelle pour l’adresse IP réservée, elle est allouée à partir de la liste des adresses IP disponibles à l’emplacement, qu'il est créé. Cette adresse IP n’est pas libérée jusqu'à ce que vous la supprimiez explicitement.

Statique des adresses IP publiques (réservées) sont couramment utilisés dans les scénarios où un service en nuage :

- nécessite des règles de pare-feu à configurer par les utilisateurs finaux.
- dépend de la résolution de noms DNS externe, et une adresse IP dynamique nécessite la mise à jour des enregistrements A.
- consomme des services web externes qui utilisent le modèle de sécurité basé sur IP.
- utilise des certificats SSL liés à une adresse IP.

>[AZURE.NOTE] Lorsque vous créez une machine virtuelle classique, un *service en nuage* du conteneur est créé par Azure, qui a une adresse IP virtuelle (VIP). Lors de la création s’effectue par le biais de portail, de SSH, *point de terminaison* par défaut RDP est configuré par le portail pour vous connecter à la machine virtuelle par le biais de l’adresse IP virtuelle du service nuage. Cette adresse IP virtuelle du service nuage peut être réservé, qui fournit une adresse IP réservée pour se connecter à la machine virtuelle. Vous pouvez ouvrir des ports supplémentaires en configurant plusieurs points de terminaison.

### <a name="iaas-vms-and-paas-role-instances"></a>Instances de rôle VM de IaaS et PaaS
Vous pouvez affecter une adresse IP publique directement à un [ordinateur virtuel](../virtual-machines/virtual-machines-linux-about.md) IaaS regroupe ou une instance de rôle de PaaS au sein d’un service en nuage. Cela correspond à une adresse IP publique au niveau de l’instance ([ILPIP](virtual-networks-instance-level-public-ip.md)). Cette adresse IP publique peut uniquement être dynamique.

>[AZURE.NOTE] Cela est différent de l’adresse IP virtuelle du service cloud, qui est un conteneur pour IaaS VMs ou PaaS instances de rôle, dans la mesure où un service en nuage peut contenir plusieurs machines virtuelles de IaaS, ou des instances de rôle de PaaS, toutes exposées par le biais de la même adresse IP virtuelle de service cloud.

### <a name="vpn-gateways"></a>Passerelles VPN
Une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) permet de connecter un VNet Azure à autres Azure VNets ou à des réseaux locaux. Une passerelle VPN est affectée à un public IP adresse *dynamique*, qui permet la communication avec le réseau distant.

### <a name="application-gateways"></a>Passerelles d’application
Une [passerelle d’Application](../application-gateway/application-gateway-introduction.md) d' Azure peut être utilisé pour Layer7 équilibrage de la charge à rediriger le trafic réseau basé sur HTTP. Passerelle d’application est affectée à un public IP adresse *dynamique*, qui sert à l’adresse IP virtuelle de l’équilibrage de charge.

### <a name="at-a-glance"></a>En un coup de œil
Le tableau ci-dessous montre chaque type de ressource avec les méthodes d’allocation possible (dynamique/statique) et permet d’attribuer plusieurs adresses IP publiques.

|Ressources|Dynamique|Statique|Plusieurs adresses IP|
|---|---|---|---|
|Service de cloud|Oui|Oui|Oui|
|Instance de rôle IaaS VM ou PaaS|Oui|N°|N°|
|Passerelle VPN|Oui|N°|N°|
|Passerelle d’application|Oui|N°|N°|

## <a name="private-ip-addresses"></a>Adresses IP privées
Les adresses IP privées permettent des ressources Azure pour communiquer avec d’autres ressources dans un service en nuage ou sur un [réseau virtuel](virtual-networks-overview.md)(VNet) ou à un réseau local (via une passerelle VPN ou un circuit de ExpressRoute), sans utiliser une adresse IP accessible à l’Internet.

Dans le modèle de déploiement classique d’Azure, une adresse IP privée peut être affectée les ressources Azure suivantes :

- Instances de rôle VM de IaaS et PaaS
- Équilibreur de charge interne
- Passerelle d’application

### <a name="iaas-vms-and-paas-role-instances"></a>Instances de rôle VM de IaaS et PaaS
Une machine virtuelle (VM) créée avec le modèle classique de déploiement sont toujours placée dans un service en nuage, similaire aux instances de rôle PaaS. Le comportement des adresses IP privées sont donc identiques pour ces ressources.

Il est important de noter qu’un service en nuage peut être déployé de deux manières :

- Sous la forme d’un service en nuage *autonome* , où il n’est pas dans un réseau virtuel.
- Dans le cadre d’un réseau virtuel.

#### <a name="allocation-method"></a>Méthode de répartition
Dans le cas d’un service en nuage *autonome* , ressources obtenir un privé IP adresse allouée *dynamiquement* à partir de la plage d’adresses IP privée datacenter Azure. Il peut être utilisé uniquement pour la communication avec les autres ordinateurs virtuels au sein du même service de cloud. Cette adresse IP peut changer lorsque la ressource est arrêtée et démarrée.

Dans le cas d’un service en nuage déployé au sein d’un réseau virtuel, ressources obtient des adresses IP privées alloué à partir de la plage d’adresses de l’associé à sous-réseau (comme spécifié dans la configuration du réseau). Cette adresse IP privée peut être utilisé pour la communication entre tous les ordinateurs virtuels dans le VNet.

En outre, en cas de services en nuage au sein d’un VNet, une adresse IP privée est allouée *dynamiquement* (à l’aide de DHCP) par défaut. Il peut changer lorsque la ressource est arrêtée et démarrée. Pour que l’adresse IP reste la même, vous devez définir la méthode de ventilation *statique*et fournir une adresse IP dans la plage d’adresses correspondant.

Statique des adresses IP privées sont généralement utilisées pour :

 - Machines virtuelles qui agissent en tant que contrôleurs de domaine ou serveurs DNS.
 - Ordinateurs virtuels qui ont besoin de règles de pare-feu à l’aide d’adresses IP.
 - Ordinateurs virtuels exécutant des services accédés par d’autres applications via une adresse IP.

#### <a name="internal-dns-hostname-resolution"></a>Résolution de nom d’hôte DNS interne
Tous les Azure VM et PaaS les instances de rôles sont configurés avec les [serveurs DNS d’Azure géré](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) par défaut, sauf si vous configurez explicitement les serveurs DNS personnalisés. Ces serveurs DNS fournissent une résolution de noms internes pour les ordinateurs virtuels et les instances de rôles qui résident dans le même service de VNet ou de nuage.

Lorsque vous créez une machine virtuelle, un mappage pour le nom d’hôte en son adresse IP privée est ajouté aux serveurs DNS d’Azure gérés. Dans le cas d’une machine virtuelle multi-carte d’interface réseau, le nom d’hôte mappé à l’adresse IP privée de la carte principale Toutefois, ces informations de mappage sont limitées aux ressources au sein du même service nuage ou VNet.

Dans le cas d’un service en nuage *autonome* , vous serez en mesure de résoudre les noms d’hôtes de toutes les instances d’ordinateurs virtuels/rôle au sein du même service nuage. Dans le cas d’un service en nuage au sein d’un VNet, vous serez en mesure de résoudre les noms d’hôte de toutes les instances de machines virtuelles/rôle dans le VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Les équilibreurs de charge interne (ILB) et les passerelles d’Application
Vous pouvez affecter une adresse IP privée à la configuration **frontale** d’un [Équilibreur de charge interne Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou d’une [Passerelle d’Application Azure](../application-gateway/application-gateway-introduction.md). Cette adresse IP privée sert une terminaison interne, accessible uniquement aux ressources au sein de son réseau virtuel (VNet) et les réseaux distants connectés à la VNet. Vous pouvez affecter soit une adresse IP dynamique ou statique privée à la configuration de front-end. Vous pouvez également affecter plusieurs adresses IP privées pour activer les scénarios de multi-vip.

### <a name="at-a-glance"></a>En un coup de œil
Le tableau ci-dessous montre chaque type de ressource avec les méthodes d’allocation possible (dynamique/statique) et permet d’attribuer plusieurs adresses IP privées.

|Ressources|Dynamique|Statique|Plusieurs adresses IP|
|---|---|---|---|
|Machine virtuelle (dans un service en nuage *autonome* )|Oui|Oui|Oui|
|Instance de rôle PaaS (dans un service en nuage *autonome* )|Oui|N°|Oui|
|Instance de rôle VM ou PaaS (dans un VNet)|Oui|Oui|Oui|
|Frontal équilibreur de charge interne|Oui|Oui|Oui|
|Front-end de application gateway|Oui|Oui|Oui|

## <a name="limits"></a>Limites

Le tableau ci-dessous montre les limites imposées IP adressage dans Azure par abonnement. Vous pouvez [contacter le support technique](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) afin d’augmenter les limites par défaut, la limite maximale en fonction des besoins de votre entreprise.

||Limite par défaut|Limite maximale|
|---|---|---|
|Adresses IP publiques (dynamique)|5|Contactez le support technique|
|Adresses IP publiques réservées|20|Contactez le support technique|
|VIP public par déploiement (service en nuage)|5|Contactez le support technique|
|Privé VIP (ILB) par déploiement (service en nuage)|1|1|

Assurez-vous de que lire l’ensemble des [limites pour la mise en réseau](azure-subscription-service-limits.md#networking-limits) dans Azure.

## <a name="pricing"></a>Tarification

Dans la plupart des cas, les adresses IP publiques sont libres. Il existe un coût modique à utiliser des adresses IP publiques supplémentaires et/ou statiques. Assurez-vous que vous comprenez la [tarification pour des adresses IP publique](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Différences entre le Gestionnaire de ressources et de déploiement classique
Voici une comparaison des fonctionnalités d’adressage IP dans le Gestionnaire de ressources et le modèle de déploiement classique.

||Ressources|Classique|Gestionnaire de ressources|
|---|---|---|---|
|**Adresse IP publique**|MACHINE VIRTUELLE|Appelé par un ILPIP (dynamique uniquement)|Appelé par une adresse IP publique (dynamique ou statique)|
|||Affecté à une VM IaaS ou une instance de rôle PaaS|Associé à la carte d’interface réseau de la machine virtuelle|
||Équilibrage de la charge qui fait face à Internet|Appelé VIP (dynamique) ou IP réservée (statique)|Appelé par une adresse IP publique (dynamique ou statique)|
|||Affecté à un service en nuage|Associé à la configuration de front-end de l’équilibreur de charge|
||||
|**Adresse IP privée**|MACHINE VIRTUELLE|Appelé par un plongeur|Appelé par une adresse IP privée|
|||Affecté à une VM IaaS ou une instance de rôle PaaS|Affecté à la carte d’interface réseau de la machine virtuelle|
||Équilibreur de charge interne (ILB)|Affecté à la ILB (dynamique ou statique)|Affecté à la configuration de front-end de la ILB (dynamique ou statique)|

## <a name="next-steps"></a>Étapes suivantes
- [Déployer un ordinateur virtuel avec une adresse IP privée](virtual-networks-static-private-ip-classic-pportal.md) lors de l’utilisation du portail classique.
