<properties
    pageTitle="Instructions d’une Infrastructure de réseau | Microsoft Azure"
    description="Obtenir des informations sur les instructions clés de conception et d’implémentation pour le déploiement d’un réseau virtuel dans les services d’infrastructure Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="networking-infrastructure-guidelines"></a>Instructions d’une infrastructure réseau

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur les étapes de planification requis pour un réseau virtuel dans Azure et la connectivité entre les environnements existants sur prem la compréhension.


## <a name="implementation-guidelines-for-virtual-networks"></a>Instructions de mise en œuvre de réseaux virtuels

Décisions :

- Quel type de réseau virtuel vous devez héberger votre charge de travail informatique ou votre infrastructure (cloud uniquement ou coexistence) ?
- Pour les réseaux virtuels de coexistence, la quantité d’espace adresse vous devez héberger les sous-réseaux et les ordinateurs virtuels, maintenant et à l’avenir une extension raisonnable ?
- Vous allez créer des réseaux virtuels centralisées ou de créer des réseaux virtuels individuels pour chaque groupe de ressources ?

Tâches :

- Définir l’espace d’adressage pour les réseaux virtuels à créer.
- Définir l’ensemble des sous-réseaux et de l’espace d’adressage pour chacun.
- Pour les réseaux virtuels de coexistence, définir l’ensemble des espaces d’adressage de réseau local pour les emplacements sur site que les ordinateurs virtuels dans le réseau virtuel est nécessaire pour atteindre.
- Travail sur site l’équipe réseau afin de garantir le routage approprié est configuré lors de la création de coexistence des réseaux virtuels.
- Créez le réseau virtuel à l’aide de votre convention d’affectation de noms.


## <a name="virtual-networks"></a>Réseaux virtuels

Les réseaux virtuels sont nécessaires pour prendre en charge les communications entre les machines virtuelles (VM). Vous pouvez définir des sous-réseaux, des adresses IP personnalisée, les paramètres DNS, le filtrage de sécurité et l’équilibrage de charge, à l’instar des réseaux physiques. À l’aide d’une [Passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou un [circuit d’itinéraire de Express](../expressroute/expressroute-introduction.md), vous pouvez vous connecter à des réseaux virtuels Azure à vos réseaux locaux. Vous pouvez en savoir plus sur les [réseaux virtuels et leurs composants](../virtual-network/virtual-networks-overview.md).

À l’aide de groupes de ressources, vous avez la flexibilité dans la façon dont vous concevez vos composants de réseau virtuels. Ordinateurs virtuels peuvent se connecter à des réseaux virtuels en dehors de leur propre groupe de ressources. Une approche de conception courante serait de créer des groupes de ressources centralisées qui contiennent votre infrastructure de réseau de base qui peut être géré par une équipe commune. Ordinateurs virtuels et leurs applications déployées vers les groupes de ressources distincts. Cette approche permet à access de propriétaires d’application pour le groupe de ressources qui contient les ordinateurs virtuels sans ouvrir l’accès à la configuration, les ressources réseau virtuel le plus large.

## <a name="site-connectivity"></a>Connectivité du site

### <a name="cloud-only-virtual-networks"></a>Réseaux virtuels nuage uniquement
Si les ordinateurs et les utilisateurs locaux ne requièrent pas une connectivité permanente aux ordinateurs virtuels dans un réseau virtuel Azure, votre conception de réseau virtuel est simple :

![Diagramme de réseaux simples nuage uniquement virtuel](./media/virtual-machines-common-infrastructure-service-guidelines/vnet01.png)

Cette approche est généralement pour des charges de travail pour Internet, tel qu’un serveur web basé sur Internet. Vous pouvez gérer ces ordinateurs virtuels à l’aide de SSH ou les connexions VPN site-à-point.

Ils ne connectent pas à votre réseau local, Azure uniquement les réseaux virtuels peuvent utiliser n’importe quelle partie de l’espace d’adressage IP privé. L’espace d’adressage peut être le même espace privé qui est en usage sur site.


### <a name="cross-premises-virtual-networks"></a>Réseaux virtuels de coexistence
Si les utilisateurs locaux et des ordinateurs nécessitent une connectivité continue aux ordinateurs virtuels dans un réseau virtuel Azure, créez un réseau virtuel de coexistence. Le réseau virtuel de se connecter à votre réseau local avec une ExpressRoute ou d’une connexion VPN de site à site.

![Diagramme de réseau virtuel de coexistence](./media/virtual-machines-common-infrastructure-service-guidelines/vnet02.png)

Dans cette configuration, le réseau virtuel Azure est essentiellement une extension de nuage de votre réseau local.

Dans la mesure où ils se connectent à votre réseau local, coexistence des réseaux virtuels doivent utiliser une partie de l’espace d’adressage utilisé par votre entreprise est unique. De la même manière à différents emplacements d’entreprise sont affectés à un sous-réseau IP spécifique, Azure devient un autre emplacement que vous étendez votre réseau.

Pour autoriser les paquets de circuler à partir de votre réseau virtuel coexistence à votre réseau local, vous devez configurer l’ensemble des préfixes d’adresse locale appropriée dans le cadre de la définition de réseau local pour l’ordinateur virtuel. En fonction de l’espace d’adressage du réseau virtuel et l’ensemble des emplacements des locaux concernés, plusieurs préfixes d’adresse peut être sur le réseau local.

Vous pouvez convertir un réseau virtuel nuage uniquement à un réseau virtuel de coexistence, mais il est probable vous devez re-IP votre espace d’adressage virtuel de réseau et les ressources Azure. Par conséquent, avec soin si un réseau virtuel doit être connecté à votre réseau local lorsque vous affectez un sous-réseau IP.

## <a name="subnets"></a>Sous-réseaux
Sous-réseaux permettent d’organiser les ressources apparentées, soit logiquement (par exemple, un sous-réseau pour les machines virtuelles associées à la même application), ou physique (par exemple, il s’agit d’un sous-réseau par groupe de ressources). Vous pouvez également employer des techniques d’isolation de sous-réseau pour une sécurité accrue.

Pour les réseaux virtuels de coexistence, vous devez concevoir les sous-réseaux avec les mêmes conventions que vous utilisez pour les ressources locales. **Azure toujours utilise les trois premières adresses IP de l’espace d’adressage pour chaque sous-réseau**. Pour déterminer le nombre d’adresses nécessaires pour le sous-réseau, démarrez à partir du nombre d’ordinateurs virtuels dont vous avez besoin maintenant. Estimation de la croissance future et puis utilisez le tableau suivant pour déterminer la taille du sous-réseau.

Nombre d’ordinateurs virtuels nécessaires | Nombre de bits d’hôte si nécessaire | Taille du sous-réseau
--- | --- | ---
1 à 3 | 3 | / 29
4 – 11     | 4 | / 28
12 – 27 | 5 | / 27
28 – 59 | 6 | / 26
60-123 | 7 | 25

> [AZURE.NOTE] Pour les sous-réseaux locaux normal, le nombre maximal d’adresses d’hôtes d’un sous-réseau avec bits d’hôte n est 2<sup>n</sup> – 2. Pour un sous-réseau Azure, le nombre maximal d’adresses d’hôtes d’un sous-réseau avec bits d’hôte n est 2<sup>n</sup> – 5 (2 plus 3 pour les adresses Azure utilise sur chaque sous-réseau).

Si vous choisissez une taille de sous-réseau qui est trop petite, vous devez re-IP et redéployez les ordinateurs virtuels dans le sous-réseau.


## <a name="network-security-groups"></a>Groupes de sécurité de réseau
Vous pouvez appliquer des règles de filtrage pour le trafic qui transite par le biais de vos réseaux virtuels à l’aide de groupes de sécurité réseau. Vous pouvez créer des règles de filtrage granulaires pour sécuriser votre environnement de réseau virtuel, contrôle entrants et sortants source et destination du trafic IP plages, autorisé des ports, etc.. Les groupes de sécurité de réseau peut être appliqués à des sous-réseaux au sein d’un réseau virtuel ou directement à une interface réseau virtuel donné. Il est recommandé d’avoir un niveau de groupe de sécurité réseau filtrent le trafic sur vos réseaux virtuels. Vous pouvez en savoir plus sur les [Groupes de sécurité de réseau](../virtual-network/virtual-networks-nsg.md).


## <a name="additional-network-components"></a>Composants réseau supplémentaires
Comme avec une infrastructure de réseau local physique, un réseau virtuel Azure peut contenir plus que des sous-réseaux et adresses IP. Lorsque vous concevez votre infrastructure d’application, vous souhaitez incorporer certains de ces composants supplémentaires :

- [Passerelles VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) - connectez Azure réseaux virtuels à d’autres réseaux virtuels Azure, ou à des réseaux locaux via une connexion VPN de Site à Site. Mettre en œuvre des connexions Express itinéraire dédié et sécurisé. Vous pouvez également fournir aux utilisateurs un accès direct avec connexions Point-à-Site VPN.
- [L’équilibreur de charge](../load-balancer/load-balancer-overview.md) - fournit l’équilibrage de la charge de trafic pour le trafic à la fois interne et externe comme vous le souhaitez.
- [Passerelle d’application](../application-gateway/application-gateway-introduction.md) – HTTP équilibrage de la charge au niveau de la couche application, offrant des avantages supplémentaires pour les applications web plutôt que de déployer l’Azure l’équilibreur de charge.
- [Traffic Manager](../traffic-manager/traffic-manager-overview.md) - distribution de trafic DNS pour diriger les utilisateurs finaux pour le point de terminaison le plus proche disponible, ce qui vous permet d’héberger votre application de centres de données Azure dans différentes régions.


## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 