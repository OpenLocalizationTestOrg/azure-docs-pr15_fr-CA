
<properties
   pageTitle="Présentation d’équilibrage de la charge de côté Internet | Microsoft Azure "
   description="Vue d’ensemble de Internet face à l’équilibreur de charge et de ses fonctionnalités. Fonctionne d’un équilibreur de charge pour Azure à l’aide de machines virtuelles et services dans le nuage."
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


# <a name="internet-facing-load-balancer-overview"></a>Vue d’ensemble du équilibreur de charge qui fait face à Internet

Équilibreur de charge Azure mappe le public IP adresse et numéro de port du trafic entrant privé adresse et le port numéro IP de l’ordinateur virtuel et vice versa pour le trafic de réponse à partir de l’ordinateur virtuel. Règles d’équilibrage de la charge permettent de distribuer des types spécifiques de trafic entre plusieurs machines virtuelles ou des services. Par exemple, vous pouvez répartir la charge du trafic de demande web sur plusieurs serveurs web ou des rôles web.

Pour un service en nuage qui contient les instances de rôles de web ou les rôles de travail, vous pouvez définir un point de terminaison public dans le fichier de définition (.csdef) du service.

Le fichier _servicedefinition.csdef_ contient la configuration de point de terminaison et lorsque vous avez plusieurs instances de rôles pour un déploiement de rôle web ou travailleur, l’équilibreur de charge sera le programme d’installation pour celle-ci. La manière d’ajouter des instances de votre déploiement de cloud consiste à modifier le nombre d’instances dans le fichier de configuration de service (.csfg).

La figure suivante illustre un point de terminaison avec équilibrage de charge pour le trafic web chiffré qui est partagé entre les trois ordinateurs virtuels pour le public et privé le port TCP 443. Ces trois ordinateurs virtuels sont dans un ensemble équilibré en charge.

![exemple d’équilibrage de la charge publique](./media/load-balancer-internet-overview/IC727496.png))

Figure 1 : point de terminaison avec équilibrage de charge pour le trafic web chiffré

Lorsque les clients Internet envoient des requêtes de page web à l’adresse IP publique du service cloud sur le port TCP 443, l’équilibreur de charge Azure répartit les demandes entre les trois ordinateurs virtuels dans l’ensemble de l’équilibrage de charge. Pour plus d’informations sur les algorithmes d’équilibrage de la charge, voir la [page de présentation du programme d’équilibrage de charge](load-balancer-overview.md#load-balancer-features).

Par défaut, Azure équilibreur de charge répartit le trafic de réseau équitablement entre plusieurs instances de l’ordinateur virtuel. Vous pouvez également configurer l’affinité de session, pour plus d’informations, voir [mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Étapes suivantes

Obtenir des informations sur [l’équilibrage de charge interne](load-balancer-internal-overview.md) afin de mieux comprendre l’équilibrage de la charge est plus adaptée à votre déploiement de cloud.

Vous pouvez également [commencer à créer un équilibreur de charge d’ouvert sur Internet](load-balancer-get-started-internet-arm-ps.md) et de configurer le type de [mode de distribution](load-balancer-distribution-mode.md) d’un comportement de trafic de réseau équilibrage de la charge spécifique.

Si votre application a besoin conserver les connexions actives pour les serveurs situés derrière un équilibreur de charge, vous pouvez savoir plus sur les [paramètres de délai d’attente TCP inactives pour un équilibreur de charge](load-balancer-tcp-idle-timeout.md). Il vous aide à en savoir plus sur le comportement des connexions inactives lorsque vous utilisez l’équilibrage de la charge Azure.
