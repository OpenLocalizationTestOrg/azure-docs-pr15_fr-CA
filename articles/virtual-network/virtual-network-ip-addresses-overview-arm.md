<properties
   pageTitle="Public et privé l’adressage IP dans le Gestionnaire de ressources Azure | Microsoft Azure"
   description="Obtenir des informations sur le public et privé l’adressage IP dans le Gestionnaire de ressources Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="ip-addresses-in-azure"></a>Adresses IP dans Azure
Vous pouvez attribuer des adresses IP aux ressources Azure pour communiquer avec les autres ressources d’Azure, votre réseau local et Internet. Il existe deux types d’adresses IP que vous pouvez utiliser dans Azure :

- **Les adresses IP publiques**: utilisé pour la communication avec Internet, y compris des services Web public Azure
- **Les adresses IP privées**: utilisé pour la communication au sein d’un réseau virtuel Azure (VNet) et vos locaux réseau lorsque vous utilisez une passerelle VPN ou un circuit de ExpressRoute d’étendre votre réseau sur Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](virtual-network-ip-addresses-overview-classic.md).

Si vous êtes familiarisé avec le modèle classique de déploiement, consultez le [différences entre classique d’adressage IP et le Gestionnaire de ressources](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Adresses IP publiques
Les adresses IP publiques permettent de ressources Azure communiquer avec les services Internet et Azure orienté public comme [Cache de Redis d’Azure](https://azure.microsoft.com/services/cache/), [Azure événement concentrateurs](https://azure.microsoft.com/services/event-hubs/), [bases de données SQL](../sql-database/sql-database-technical-overview.md)et le [stockage Azure](../storage/storage-introduction.md).

Dans le Gestionnaire de ressources d’Azure, une adresse [IP publique](resource-groups-networking.md#public-ip-address) est une ressource qui possède ses propres propriétés. Vous pouvez associer à une ressource d’adresse IP publique avec une des ressources suivantes :

- Machines virtuelles (VM)
- Équilibreurs de charge d’avec accès via Internet
- Passerelles VPN
- Passerelles d’application

### <a name="allocation-method"></a>Méthode de répartition
Il existe deux méthodes dans laquelle une adresse IP est allouée à une ressource IP *publique* - *dynamique* ou *statique*. La méthode d’allocation par défaut est *dynamique*, dans le cas où une adresse IP n’est **pas** allouée au moment de sa création. Au lieu de cela, l’adresse IP publique est allouée lorsque vous démarrez (ou créez) la ressource associée (par exemple, un équilibreur de charge ou de la machine virtuelle). L’adresse IP est libérée lorsque vous arrêtez (ou supprimez) la ressource. Ainsi, l’adresse IP à modifier lors de l’arrêt et le redémarrage d’une ressource.

Pour garantir que l’adresse IP de la ressource reste identique, vous pouvez définir explicitement le mode de ventilation *statique*. Dans ce cas, une adresse IP est immédiatement affectée. Il est publié uniquement lorsque vous supprimez la ressource ou de modifiez sa méthode de ventilation *dynamique*.

>[AZURE.NOTE] Même si vous définissez la méthode de ventilation *statique*, vous ne pouvez pas spécifier l’adresse IP réelle affecté à la *ressource de l’IP*. Au lieu de cela, il est alloué à partir d’un pool d’adresses IP de disponibles à l’emplacement Azure, dans que la ressource est créée.

Statique des adresses IP publiques sont généralement utilisées dans les scénarios suivants :

- Les utilisateurs finaux ont besoin mettre à jour les règles de pare-feu pour communiquer avec vos ressources Azure.
- Résolution de noms DNS, où une modification dans adresse IP requiert la mise à jour des enregistrements de.
- Vos ressources Azure communiquent avec d’autres applications ou les services qui utilisent un modèle de sécurité basé sur l’adresse IP.
- Vous utilisez des certificats SSL liés à une adresse IP.

>[AZURE.NOTE] La liste de plages IP à partir de laquelle les adresses IP publiques (dynamique/statique) sont affectés à des ressources Azure est publiée dans [les plages IP de centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Résolution de nom d’hôte DNS
Vous pouvez spécifier une étiquette de nom de domaine DNS pour une ressource IP publique, ce qui crée un mappage pour *domainnamelabel*. *emplacement*. cloudapp.azure.com à l’adresse IP publique dans les serveurs DNS d’Azure gérés. Par exemple, si vous créez une ressource IP publique avec **contoso** comme un *domainnamelabel* aux **États-Unis Ouest** Azure *emplacement*, le nom de domaine complet **contoso.westus.cloudapp.azure.com** résout l’adresse IP publique de la ressource. Vous pouvez utiliser ce nom de domaine complet pour créer un enregistrement CNAME de domaine personnalisé qui pointe vers l’adresse IP publique dans Azure.

>[AZURE.IMPORTANT] Chaque étiquette de nom de domaine créé doit être unique dans son emplacement d’Azure.  

### <a name="virtual-machines"></a>Ordinateurs virtuels
Vous pouvez associer une adresse IP publique et un [Windows](../virtual-machines/virtual-machines-windows-about.md) [Linux](../virtual-machines/virtual-machines-linux-about.md) VM en l’assignant à son **interface réseau**. Dans le cas d’une interface réseau multiples de machine virtuelle, vous pouvez l’affecter à l’interface réseau *principale* uniquement. Vous pouvez affecter un dynamique, soit une adresse IP publique à une machine virtuelle.

### <a name="internet-facing-load-balancers"></a>Équilibreurs de charge d’avec accès via Internet
Vous pouvez associer une adresse IP publique avec un [Équilibreur de charge Azure](../load-balancer/load-balancer-overview.md), en l’assignant à la configuration de **frontal** d’équilibrage de la charge. Cette adresse IP publique constitue une charge équilibrée adresse IP virtuelle (VIP). Vous pouvez affecter un dynamique, soit une adresse IP publique à un équilibreur de charge frontal. Vous pouvez également affecter plusieurs adresses IP publiques à un équilibreur de charge frontal, qui permet des scénarios de [multi-VIP](../load-balancer/load-balancer-multivip.md) comme un environnement multi-DETENTEURS avec des sites Web SSL.

### <a name="vpn-gateways"></a>Passerelles VPN
[Passerelle de VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) permet de connecter un réseau virtuel Azure (VNet) pour les autres VNets d’Azure, ou à un réseau local. Vous devez affecter une adresse IP publique à sa **configuration IP** pour lui permettre de communiquer avec le réseau distant. Actuellement, vous ne pouvez affecter une adresse IP publique de *dynamique* vers une passerelle VPN.

### <a name="application-gateways"></a>Passerelles d’application
Vous pouvez associer une adresse IP publique avec une [Application passerelle](../application-gateway/application-gateway-introduction.md)d' Azure, en l’assignant à une configuration de **site Web frontal** de la passerelle. Cette adresse IP publique est un VIP d’équilibrage de charge. Actuellement, vous ne pouvez affecter une adresse IP publique de *dynamique* à une configuration de site Web frontal application passerelle.

### <a name="at-a-glance"></a>Un coup de œil
Le tableau ci-dessous affiche la propriété spécifique par l’intermédiaire duquel une adresse IP publique peut être associée à une ressource de niveau supérieur et les méthodes d’allocation possible (dynamiques ou statiques) qui peuvent être utilisés.

|Ressources de niveau supérieur|Association de l’adresse IP|Dynamique|Statique|
|---|---|---|---|
|Machine virtuelle|Interface réseau|Oui|Oui|
|L’équilibreur de charge|Configuration de front-end|Oui|Oui|
|Passerelle VPN|Configuration IP de la passerelle|Oui|N°|
|Passerelle d’application|Configuration de front-end|Oui|N°|

## <a name="private-ip-addresses"></a>Adresses IP privées
Les adresses IP privées permettent des ressources Azure pour communiquer avec d’autres ressources dans un [réseau virtuel](virtual-networks-overview.md) ou d’un réseau local via une passerelle VPN ou un circuit de ExpressRoute, sans utiliser une adresse IP accessible à l’Internet.

Dans le modèle de déploiement d’Azure le Gestionnaire de ressources, une adresse IP privée est associée aux types de ressources Azure suivants :

- Ordinateurs virtuels
- Équilibreurs de charge interne (ILBs)
- Passerelles d’application

### <a name="allocation-method"></a>Méthode de répartition
Une adresse IP privée est allouée à partir de la plage d’adresses du sous-réseau auquel la ressource est associée. La plage d’adresses du sous-réseau lui-même est une partie de la plage d’adresses de la VNet.

Il existe deux méthodes dans laquelle une adresse IP privée est allouée : *statique*ou *dynamique* . La méthode d’allocation par défaut est *dynamique*, où l’adresse IP est allouée automatiquement à partir du sous-réseau de la ressource (à l’aide de DHCP). Cette adresse IP peut changer lors de l’arrêt et le redémarrage de la ressource.

Vous pouvez définir la méthode de ventilation *statique* afin de garantir que l’adresse IP reste le même. Dans ce cas, vous devez également fournir une adresse IP valide qui fait partie d’un sous-réseau de la ressource.

Statique des adresses IP privées sont généralement utilisées pour :

- Machines virtuelles qui agissent en tant que contrôleurs de domaine ou serveurs DNS.
- Ressources qui ont besoin de règles de pare-feu à l’aide d’adresses IP.
- Ressources accédés par d’autres applications/ressources via une adresse IP.

### <a name="virtual-machines"></a>Ordinateurs virtuels
Une adresse IP privée est affectée à l' **interface réseau** d’un [Windows](../virtual-machines/virtual-machines-windows-about.md) [Linux](../virtual-machines/virtual-machines-linux-about.md) VM. Dans le cas d’une interface réseau plusieurs VM, chaque interface Obtient une adresse IP privée assignée. Vous pouvez spécifier la méthode de ventilation dynamique ou statique pour une interface réseau.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>Résolution de nom d’hôte DNS interne (pour les machines virtuelles)
Tous les ordinateurs virtuels de Azure sont configurés avec les [serveurs DNS d’Azure géré](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) par défaut, sauf si vous configurez explicitement les serveurs DNS personnalisés. Ces serveurs DNS fournissent la résolution de noms interne pour les machines virtuelles qui résident dans le même VNet.

Lorsque vous créez une machine virtuelle, un mappage pour le nom d’hôte en son adresse IP privée est ajouté aux serveurs DNS d’Azure gérés. Dans le cas d’une interface réseau plusieurs VM, le nom d’hôte mappé à l’adresse IP privée de l’interface réseau principale.

Ordinateurs virtuels configurés avec les serveurs DNS d’Azure géré sera en mesure de résoudre les noms d’hôte de tous les ordinateurs virtuels au sein de leur VNet à leurs adresses IP privées.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Les équilibreurs de charge interne (ILB) et les passerelles d’Application
Vous pouvez affecter une adresse IP privée à la configuration **frontale** d’un [Équilibreur de charge interne Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou d’une [Passerelle d’Application Azure](../application-gateway/application-gateway-introduction.md). Cette adresse IP privée sert une terminaison interne, accessible uniquement aux ressources au sein de son réseau virtuel (VNet) et les réseaux distants connectés à la VNet. Vous pouvez affecter soit une adresse IP dynamique ou statique privée à la configuration de front-end.

### <a name="at-a-glance"></a>Un coup de œil
Le tableau ci-dessous affiche la propriété spécifique par l’intermédiaire duquel une adresse IP privée peut être associée à une ressource de niveau supérieur et les méthodes d’allocation possible (dynamiques ou statiques) qui peuvent être utilisés.

|Ressources de niveau supérieur|Association d’adresses IP|Dynamique|Statique|
|---|---|---|---|
|Machine virtuelle|Interface réseau|Oui|Oui|
|L’équilibreur de charge|Configuration de front-end|Oui|Oui|
|Passerelle d’application|Configuration de front-end|Oui|Oui|

## <a name="limits"></a>Limites

Les limites imposées sur l’adressage IP sont indiquées dans l’ensemble des [limites pour la mise en réseau](azure-subscription-service-limits.md#networking-limits) dans Azure. Ces limites sont par région et par abonnement. Vous pouvez [contacter le support technique](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) afin d’augmenter les limites par défaut, la limite maximale en fonction des besoins de votre entreprise.

## <a name="pricing"></a>Tarification

Les adresses IP publiques peuvent avoir un coût modique. Pour en savoir plus sur la tarification d’adresse IP dans Azure, passez en revue la page [tarification d’adresse IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

## <a name="next-steps"></a>Étapes suivantes
- [Déployer un ordinateur virtuel avec une adresse IP statique publique via le portail Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [Déployer un ordinateur virtuel avec une adresse IP statique publique à l’aide d’un modèle](virtual-network-deploy-static-pip-arm-template.md)
- [Déployer un ordinateur virtuel avec une adresse IP privée statique en utilisant le portail Azure](virtual-networks-static-private-ip-arm-pportal.md)
