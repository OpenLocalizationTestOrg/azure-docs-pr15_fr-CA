<properties
   pageTitle="Désactiver ou activer un point de terminaison de Traffic Manager | Microsoft Azure"
   description="Cet article vous aidera à désactiver ou activer les points de terminaison de votre profil Traffic Manager."
   services="traffic-manager"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="disable-or-enable-a-traffic-manager-endpoint"></a>Désactiver ou activer un point de terminaison de Traffic Manager

Vous pouvez également désactiver les points de terminaison individuels qui font partie d’un profil de gestionnaire de trafic. Points de terminaison incluent les services en nuage et sites Web. La désactivation d’un point de terminaison le laisse dans le cadre du profil, mais le profil se comporte comme si le point de terminaison n’est pas inclus dans celui-ci. Cette action est très utile pour supprimer temporairement un point de terminaison est en mode maintenance ou en cours de redéploiement. Une fois que le point de terminaison est en cours d’exécution, une nouvelle fois, il peut être activé

>[AZURE.NOTE] **Désactivation d’un point de terminaison n’a rien à voir avec l’état de déploiement dans Azure. Un point de terminaison sain reste haut et est en mesure de recevoir le trafic même si désactivée dans le Gestionnaire de trafic. En outre, la désactivation d’un point de terminaison d’un profil n’affecte pas son état dans un autre profil.**

## <a name="to-disable-an-endpoint"></a>Pour désactiver un point de terminaison

1. Dans le volet Gestionnaire de trafic dans Azure portal classique, recherchez le profil de Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom du profil. Ceci ouvrira la page de paramètres pour le profil.
1. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
1. Cliquez sur le point de terminaison que vous souhaitez désactiver, puis cliquez sur **désactiver** au bas de la page.
1. Le trafic s’arrête le dirige vers le point de terminaison basé sur la DNS Time-to-Live (TTL) configurée pour le nom de domaine du Gestionnaire de trafic. Vous pouvez modifier la durée de vie de la page de Configuration du profil Traffic Manager.

## <a name="to-enable-an-endpoint"></a>Pour activer un point de terminaison


1. Dans le volet Gestionnaire de trafic dans Azure portal classique, recherchez le profil de Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom du profil. Ceci ouvrira la page de paramètres pour le profil.
1. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
1. Cliquez sur le point de terminaison que vous souhaitez activer, puis cliquez sur **Activer** en bas de la page.
1. Démarre le trafic circulant au nouveau comme dicté par le profil de service.

## <a name="next-steps"></a>Étapes suivantes

[Trafic Manager - désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Résolution des problèmes de Traffic Manager dégradées état](traffic-manager-troubleshooting-degraded.md)

[Considérations relatives aux performances de Traffic Manager](traffic-manager-performance-considerations.md)