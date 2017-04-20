<properties
   pageTitle="Azure prise en charge du Gestionnaire de ressources pour l’équilibrage de la charge | Microsoft Azure "
   description="Utilisation de powershell pour l’équilibrage de la charge avec le Gestionnaire de ressources Azure. Utilisation de modèles pour l’équilibrage de la charge"
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


# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>À l’aide de la prise en charge du Gestionnaire de ressources Azure avec équilibreur de charge Azure

Le Gestionnaire de ressources Azure est le framework de gestion par défaut pour les services dans Azure. Azure équilibreur de charge peut être gérée à l’aide des API basées sur le Gestionnaire de ressources Azure et outils.

## <a name="concepts"></a>Concepts

Avec le Gestionnaire de ressources, équilibrage de la charge Azure contient les ressources enfants suivantes :

- Configuration de front-end IP – un équilibreur de charge peut inclure un ou plusieurs front-end des adresses IP, également appelés IPs virtuel (VIPs). Ces adresses IP servent de pénétration du trafic.

- Pool d’adresses principal – il s’agit des adresses IP associées avec la machine virtuelle Interface de carte réseau (NIC) qui charge est distribué.

- Règles d’équilibrage de charge – met en correspondance une propriété règle donné front-end IP et combinaison de port pour un ensemble d’adresses IP de back-end et combinaison de port. Un équilibreur de charge unique peut avoir des règles d’équilibrage de charge multiples. Chaque règle est une combinaison d’un front-end IP et de port et back-end IP et un port associé aux machines virtuelles.

- Sondes – sondes permettent d’effectuer le suivi de l’état des instances de la machine virtuelle. En cas de défaillance d’une sonde de santé, l’instance de la machine virtuelle est prise automatiquement sa rotation.

- Règles de trafic entrant NAT – règles NAT définissant le trafic transitant par l’avant fin IP et distribué à l’adresse IP du serveur principal.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Azure Resource Manager vous permet de configurer vos applications à l’aide d’un modèle déclaratif. Dans un modèle unique, vous pouvez déployer plusieurs services ainsi que leurs dépendances. Vous utilisez le même modèle à plusieurs reprises de déployer votre application au cours de chaque étape du cycle de vie d’application.

Modèles peuvent inclure les définitions pour les ordinateurs virtuels, réseaux virtuels, jeux de disponibilité, Interfaces réseau (cartes réseau), comptes de stockage, équilibreurs de charge, groupes de sécurité réseau et IPs publics. Avec les modèles vous pouvez créer tout ce dont vous avez besoin d’une application complexe. Le fichier de modèle peut être archivé dans les système de gestion de contenu pour le contrôle de version et de collaboration.

[Pour en savoir plus sur les modèles](http://go.microsoft.com/fwlink/?LinkId=544798)

[En savoir plus sur les ressources réseau](../virtual-network/resource-groups-networking.md)

Modèles de démarrage rapide utilisant l’équilibrage de la charge Azure sont accessibles dans un [référentiel de GitHub](https://github.com/Azure/azure-quickstart-templates) qui héberge un ensemble de modèles de généré par la Communauté.

Exemples de modèles :

- [2 VM dans un équilibreur de charge et les règles d’équilibrage de charge](http://go.microsoft.com/fwlink/?LinkId=544799)
- [2 VM dans un VNET avec un équilibreur de charge interne et équilibrage de la charge des règles](http://go.microsoft.com/fwlink/?LinkId=544800)
- [2 VM dans un équilibreur de charge et la configuration des règles NAT sur les livres](http://go.microsoft.com/fwlink/?LinkId=544801)


## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configuration de l’équilibreur de charge Azure avec PowerShell ou l’interface CLI

Commencer à utiliser des applets de commande, les outils de ligne de commande et autres API Azure le Gestionnaire de ressources

- [Applets de commande de mise en réseau de Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) peut servir à créer un équilibreur de charge.
- [Comment créer un équilibreur de charge à l’aide du Gestionnaire de ressources Azure](load-balancer-get-started-ilb-arm-ps.md)
- [À l’aide de l’interface CLI Azure avec la gestion des ressources Azure](../xplat-cli-azure-resource-manager.md)
- [API du reste de l’équilibrage de charge](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également [commencer à créer un équilibreur de charge d’ouvert sur Internet](load-balancer-get-started-internet-arm-ps.md) et de configurer le type de [mode de distribution](load-balancer-distribution-mode.md) d’un comportement de trafic de réseau équilibrage de la charge spécifique.

Apprenez à gérer les [paramètres de délai d’attente TCP inactives pour un équilibreur de charge](load-balancer-tcp-idle-timeout.md). Ceci est important lorsque votre application a besoin pour conserver les connexions actives pour les serveurs situés derrière un équilibreur de charge.
