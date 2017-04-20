<properties
   pageTitle="Désactiver, activer ou supprimer un profil de Traffic Manager | Microsoft Azure"
   description="Cet article vous aidera à travailler avec vos profils de Traffic Manager."
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

# <a name="disable-enable-or-delete-a-profile"></a>Désactiver, activer ou supprimer un profil


Vous pouvez désactiver un profil Traffic Manager existant afin qu’il se réfère pas les demandes de l’utilisateur pour ses points de terminaison configurés. Lorsque vous désactivez un profil Traffic Manager, le profil lui-même et les informations contenues dans le profil reste intacte et peut être modifié dans l’interface du Gestionnaire de trafic. Lorsque vous souhaitez activer de nouveau le profil, vous pouvez facilement faire dans l’Azure portal et références va reprendre. Lorsque vous créez un profil de gestionnaire de trafic dans le portail d’Azure, il est automatiquement activé.

## <a name="to-disable-a-profile"></a>Pour désactiver un profil

1. Modifier l’enregistrement de ressource DNS sur votre serveur DNS Internet à utiliser le type d’enregistrement approprié et un pointeur sur un autre nom ou l’adresse IP d’un emplacement spécifique sur Internet. En d’autres termes, modifier l’enregistrement de ressource DNS sur votre serveur DNS Internet afin qu’il n’utilise plus un enregistrement de ressource CNAME qui pointe vers le nom de domaine de votre profil de gestionnaire de trafic.
1. Le trafic s’arrête est dirigée vers les points de terminaison via les paramètres de profil du Gestionnaire de trafic.
1. Sélectionnez le profil que vous souhaitez désactiver. Sélectionnez le profil, dans la page Gestionnaire de trafic, sélectionnez le profil en cliquant sur la colonne en regard du nom de profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom, comme cela vous amènera à la page de paramètres pour le profil.
1. Après avoir sélectionné le profil, cliquez sur Désactiver au bas de la page.

## <a name="to-enable-a-profile"></a>Pour activer un profil

1. Sélectionnez le profil que vous souhaitez activer. Sélectionnez le profil, dans la page Gestionnaire de trafic, sélectionnez le profil en cliquant sur la colonne en regard du nom de profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom, comme cela vous amènera à la page de paramètres pour le profil.
1. Après avoir sélectionné le profil, cliquez sur Activer en bas de la page.
1. Modifier l’enregistrement de ressource DNS sur votre serveur DNS Internet à utiliser le type d’enregistrement CNAME, qui mappe le nom de domaine de votre société au nom de domaine de votre profil de gestionnaire de trafic. Pour plus d’informations, voir [Point un domaine Internet de l’entreprise à un domaine de Traffic Manager](traffic-manager-point-internet-domain.md).
1. Démarre le trafic redirigé vers les points de terminaison à nouveau.

## <a name="delete-a-profile"></a>Supprimer un profil


1. Assurez-vous que l’enregistrement de ressource DNS sur votre serveur DNS Internet n’utilise plus un enregistrement de ressource CNAME qui pointe vers le nom de domaine de votre profil de gestionnaire de trafic.
1. Sélectionnez le profil que vous souhaitez supprimer. Sélectionnez le profil, dans la page Gestionnaire de trafic, sélectionnez le profil par
1. en cliquant sur la colonne en regard du profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom, comme cela vous amènera à la page de paramètres pour le profil.
1. Après avoir sélectionné le profil, cliquez sur Supprimer au bas de la page.

## <a name="next-steps"></a>Étapes suivantes

[Traffic Manager - désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

[Configurer la méthode d’acheminement de basculement](traffic-manager-configure-failover-routing-method.md)

[Configurer la méthode de routage alternée](traffic-manager-configure-round-robin-routing-method.md)

[Configurer la méthode de routage de performances](traffic-manager-configure-performance-routing-method.md)

[Résolution des problèmes de Traffic Manager dégradées état](traffic-manager-troubleshooting-degraded.md)

