<properties
   pageTitle="Déploiement d’appliances virtuelles de haute disponibilité | Microsoft Azure"
   description="Comment déployer des équipements de réseau virtuel de haute disponibilité."
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
   ms.date="09/21/2016"
   ms.author="telmos"/>

# <a name="deploying-virtual-appliances-in-high-availability"></a>Déploiement d’appliances virtuelles de haute disponibilité

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article présente un ensemble de pratiques pour le déploiement de solutions de réseau virtuel (NVAs) de manière hautement disponible. Avant de continuer, assurez-vous que vous comprenez comment [les itinéraires définis par l’utilisateur (UDR)] [ udr-overview] et de [l’équilibrage de la charge] [ lb-overview] fonctionnent dans Azure.

Vous pouvez utiliser différents NVAs disponibles sur le marché Azure pour étendre les fonctionnalités d’Azure de la même manière que vous utilisez des appliances dans votre centre de données sur site. La figure suivante illustre un exemple de déploiement d’un [seul à couloirs] [ nva-scenario] sous la forme d’une appliance de pare-feu. 

![[0]][0]

Bien que le déploiement précédent fonctionne, elle présente un point de défaillance unique. En cas de défaillance de l’appliance virtuelle, aucun trafic n’est transmis. Pour résoudre ce problème, vous devez utiliser plusieurs NVAs. Toutefois, qui requiert également autres paramètres et les ressources à utiliser selon vos besoins.

Vous pouvez utiliser une des solutions suivantes pour déployer un environnement à couloirs hautement disponible.

|Solution|Avantages|Considérations relatives à la|
|---|---|---|
|Les paquets entrants avec des boîtiers virtuels de couche 7|Tous les nœuds sont actifs|Nécessite d’une à couloirs qui peuvent mettre fin aux connexions et utiliser SNAT<br/>Nécessite un ensemble distinct de NVAs pour le trafic en provenance d’Internet et d’Azure<br/>Peut uniquement être utilisé pour le trafic provenant de l’extérieur d’Azure|
|ENTREE-sortie avec des boîtiers virtuels de couche 7|Tous les nœuds sont actifs<br/>Capable de gérer le trafic dans Azure |Nécessite d’une à couloirs qui peuvent mettre fin aux connexions et utiliser SNAT<br/>Nécessite un ensemble distinct de NVAs pour le trafic en provenance d’Internet et d’Azure|
|Commutateur de PIP-UDR|Ensemble unique de NVAs pour tout le trafic<br/>Peut gérer tout le trafic (aucune limite sur les règles de port)|Actif-passif<br/>Nécessite un processus de basculement sur incident|

## <a name="ingress-with-layer-7-virtual-appliances"></a>Les paquets entrants avec des boîtiers virtuels de couche 7
Vous pouvez utiliser un ensemble de NVAs derrière un équilibreur de charge Azure pour fournir une connectivité aux charges de travail Azure derrière un petit ensemble de ports côté serveur (par exemple, HTTP et HTTPS). La figure suivante met en évidence comment fournir une haute disponibilité dans ce scénario au niveau à couloirs.

![[1]][1]

Dans ce scénario, l’appliance virtuelle réseau utilisé doit mettre fin à toutes les connexions et les transmettre au sous-réseau de la charge de travail. Les machines virtuelles de la charge de travail (VMs) répondent à l’à couloirs qu'ils a reçu une demande à partir d’et les flux de trafic sans problèmes. 

## <a name="ingress-egress-with-layer-7-virtual-appliances"></a>ENTREE-sortie avec des boîtiers virtuels de couche 7
Vous pouvez étendre l’architecture précédente et ajouter un autre jeu de NVAs pour traiter le trafic en provenance d’Azure pour être gérée par NVAs, comme illustré dans la figure suivante :

![[2]][2]

Dans ce scénario, tout le trafic provenant de Azure est routé à un équilibreur de charge interne qui répartit la charge entre un ensemble différent de NVAs. Ces NVAs directement le trafic vers Internet à l’aide d’adresses IP publiques individuels. 

## <a name="pip-udr-switch"></a>Commutateur de PIP-UDR
Vous pouvez éviter de créer plusieurs piles à couloirs à l’aide de deux NVAs en mode actif-passif. Dans ce scénario, vous pouvez passer l’adresse IP publique (PIP) et les itinéraires définis par l’utilisateur (UDRs) lorsque le nœud actif s’arrête.  

![[3]][3]

Ce scénario est similaire au scénario à couloirs unique. La seule différence est que le PIP et UDRs doivent être changés pour passer le trafic entre les NVAs. Ces modifications peuvent être apportées manuellement, ou vous pouvez les automatiser également. Pour automatiser, vous pouvez déployer une application à deux NVAs qui vérifie l’état de santé du nœud actif. Une fois le nœud actif vers le bas, votre application peut modifier le PIP et UDRs pour créer un lien vers le nœud passif.

Une implémentation possible de cette solution est d’utiliser un [soigneur] [ zookeeper] démon sur les NVAs pour gérer l’élection du chef (pour décider quel nœud est le nœud actif). Une fois qu’une ligne de repère est choisi, il appelle l’API REST Azure pour supprimer le PIP du nœud défaillant et l’attacher à la ligne de repère. Il doit également modifier UDRs pour pointer vers l’adresse IP privée de la nouvelle ligne de repère.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [implémenter un réseau de périmètre entre Azure et votre centre de données local] [ dmz-on-prem] à l’aide de NVAs de couche 7.
- Découvrez comment [implémenter un réseau de périmètre entre Internet et les Azure] [ dmz-internet] à l’aide de NVAs de couche 7.

<!-- links -->
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[lb-overview]: ../load-balancer/load-balancer-overview.md
[zookeeper]: https://zookeeper.apache.org/
[nva-scenario]: ../virtual-network/virtual-network-scenario-udr-gw-nva.md
[dmz-on-prem]: guidance-iaas-ra-secure-vnet-hybrid.md
[dmz-internet]: guidance-iaas-ra-secure-vnet-dmz.md

<!-- images -->
[0]: ./media/guidance-nva-ha/single-nva.png "Seule architecture à couloirs"
[1]: ./media/guidance-nva-ha/l7-ingress.png "Pénétration de couche 7"
[2]: ./media/guidance-nva-ha/l7-ingress-egress.png "Couche 7 entrant et sortant"
[3]: ./media/guidance-nva-ha/active-passive.png "Cluster actif-passif"