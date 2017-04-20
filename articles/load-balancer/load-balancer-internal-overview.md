
<properties
   pageTitle="L’équilibreur de charge interne présentation | Microsoft Azure"
   description="Vue d’ensemble de l’équilibreur de charge interne et ses fonctionnalités. Fonctionne d’un équilibreur de charge pour les scénarios d’Azure et possibles configurer des points de terminaison internes"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="internal-load-balancer-overview"></a>Vue d’ensemble des équilibreur de charge interne

Contrairement aux puis Internet en vis-à-vis équilibreur de charge, l’équilibreur de charge interne (ILB) dirige le trafic uniquement aux ressources à l’intérieur du service nuage ou à l’aide de VPN pour accéder à l’infrastructure Azure. L’infrastructure limite l’accès à l’équilibrage de la charge adresses IP virtuelles (VIP) d’un réseau virtuel ou d’un Service en nuage afin qu’ils seront jamais directement exposées à un point de terminaison d’Internet. Cela permet une ligne interne d’applications commerciales (LOB) à exécuter dans Azure et d’y accéder à partir de dans le nuage ou de ressources sur site.

## <a name="why-you-may-need-an-internal-load-balancer"></a>Pourquoi vous devrez peut-être un équilibreur de charge interne

Azure interne charge Équilibrage (ILB) fournit l’équilibrage de charge entre les machines virtuelles qui résident à l’intérieur d’un service en nuage ou d’un réseau virtuel avec une portée régionale. Pour plus d’informations sur l’utilisation et la configuration des réseaux virtuels avec une portée régionale, consultez [Régionaux de réseaux virtuels](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) dans le blog d’Azure. Les réseaux virtuels existants qui ont été configurés pour un groupe d’affinité ne peut pas utiliser ILB.

ILB Active les types suivants d’équilibrage de charge :

- Au sein d’un service en nuage, des machines virtuelles à un ensemble de machines virtuelles qui résident dans le même service de cloud (voir Figure 1).
- Au sein d’un réseau virtuel, à partir d’ordinateurs virtuels dans le réseau virtuel à un ensemble de machines virtuelles qui résident dans le même service de cloud du répertoire réseau (voir Figure 2).
- Pour un réseau virtuel de coexistence, à partir d’ordinateurs locaux à un ensemble de machines virtuelles qui résident dans le même service de cloud du répertoire réseau (voir Figure 3).
- Applications faisant face à Internet, à plusieurs niveaux dans lequel les niveaux principaux ne sont pas exposés à Internet mais nécessitent un équilibrage de la charge pour le trafic de la couche d’accès via Internet.
- Équilibrage de charge pour les applications métier hébergées dans Azure sans nécessiter de logiciel ou matériel d’équilibrage de charge supplémentaire. Y compris les serveurs locaux dans l’ensemble des ordinateurs dont le trafic est à charge équitablement.

## <a name="internet-facing-multi-tier-applications"></a>Côté n-tier applications Internet


La couche web a des points de terminaison qui fait face vers Internet pour les clients Internet et fait partie d’un ensemble équilibré en charge. L’équilibreur de charge répartit le trafic entrant à partir de clients web pour le port TCP 443 (HTTPS) vers les serveurs web.

Les serveurs de base de données sont derrière un point de terminaison ILB qui les serveurs web utilisent pour le stockage. Cette charge de maintenance de base de données équilibrée de point de terminaison, le trafic qui est à charge équilibrée entre les serveurs de base de données dans le jeu de ILB.

L’image suivante montre Internet face à des applications à plusieurs niveaux au sein du même service de cloud.

Figure 1 : côté n-tier applications Internet

![Service de cloud unique d’équilibrage de charge interne](./media/load-balancer-internal-overview/IC736321.png)

Une autre utilisation possible pour une application multicouche est lors du déployée de l’ILB d’un service en nuage autre que la consommation du service pour l’ILB.

Les services en nuage à l’aide du même réseau virtuel auront accès au point de terminaison ILB.

La figure 2 montre web frontaux, les serveurs sont dans un service de nuage différent à partir de la base de données back-end et en utilisant le point de terminaison ILB au sein du même réseau virtuel.

![Interne équilibrage de charge entre les services en nuage](./media/load-balancer-internal-overview/IC744147.png)

Figure 2 : les serveurs frontaux dans un service de nuage différent

## <a name="intranet-line-of-business-applications"></a>Ligne d’intranet d’applications d’entreprise

Le trafic des clients sur le réseau local obtenir l’équilibrage de charge sur l’ensemble des serveurs d’applications commerciales à l’aide de la connexion VPN au réseau d’Azure.

L’ordinateur client auront accès à une adresse IP à partir du service d’Azure VPN à l’aide de point à site VPN. Il permet l’utilisation de l’application cœur de métier hébergée derrière le point de terminaison ILB.

![L’équilibrage de la charge interne au VPN de site à l’aide de point](./media/load-balancer-internal-overview/IC744148.png)

Figure 3 : applications métiers hébergées derrière le point de terminaison LB

Un autre scénario pour le LOB est d’avoir un VPN de site à site au réseau virtuel dans lequel le point de terminaison ILB est configuré. Ainsi, le trafic de réseau local être routé vers le point de terminaison ILB.

![L’équilibrage de la charge interne à l’aide de VPN de site à site](./media/load-balancer-internal-overview/IC744150.png)

Figure 4 : le trafic de réseau local routé vers le point de terminaison ILB


## <a name="next-steps"></a>Étapes suivantes

[Azure prise en charge du Gestionnaire de ressources pour l’équilibrage de la charge Azure](load-balancer-arm.md)

[Démarrer la configuration d’un équilibreur de charge d’ouvert sur Internet](load-balancer-get-started-internet-arm-ps.md)

[Démarrer la configuration d’un équilibreur de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)

