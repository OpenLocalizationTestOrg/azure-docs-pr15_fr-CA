<properties 
   pageTitle="Tableau de bord de la service de gestionnaire de StorSimple - tableau virtuel | Microsoft Azure"
   description="Décrit le tableau de bord Gestionnaire de StorSimple service et explique comment l’utiliser pour surveiller la santé de votre tableau virtuel StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>Utiliser le tableau de bord de service Gestionnaire de StorSimple StorSimple virtuels

## <a name="overview"></a>Vue d’ensemble

La page de tableau de bord de service StorSimple Manager fournit un résumé de la StorSimple tableaux virtuels (également connu sous le nom StorSimple local virtuels périphériques ou périphériques virtuels) qui sont connectés au service du Gestionnaire de StorSimple, mise en évidence celles qui requièrent une attention particulière d’un administrateur système. Ce didacticiel présente la page tableau de bord, explique le contenu du tableau de bord et la fonction et décrit les tâches que vous pouvez effectuer à partir de cette page.

![Tableau de bord de service](./media/storsimple-ova-service-dashboard/dashboard1.png)

Le tableau de bord de service StorSimple Manager affiche les informations suivantes :

- Dans la zone de **graphique** en haut de la page, vous pouvez voir les mesures appropriées pour vos périphériques virtuels. Vous pouvez afficher le stockage principal utilisé sur tous les périphériques virtuels, ainsi que le stockage en nuage consommée par les périphériques virtuels sur une période donnée. Utilisez les contrôles dans le coin supérieur droit du graphique pour spécifier l’utilisation de relative ou absolue et une échelle de temps de 1 semaine, 1 mois, 3 mois ou 1 an. Utilisez le contrôle de l’actualisation de ![-contrôle de l’actualisation](./media/storsimple-ova-service-dashboard/refresh-control.png) pour actualiser le graphique.

- La **Présentation de l’utilisation** indique le stockage principal est mis en service et utilisé par tous les périphériques virtuels par rapport au stockage total disponible sur tous les périphériques virtuels. **Provisioned** fait référence à l’espace de stockage est préparé et alloué à l’utilisation, **utilisé** fait référence à l’utilisation de partages ou volumes comme indiqué par les initiateurs connectés à des périphériques virtuels, et **capacité de Max.** affiche la capacité maximale de tous les périphériques virtuels.

- La zone **alertes** fournit un instantané de toutes les alertes actives sur tous les périphériques virtuels, regroupées par gravité de l’alerte. Cliquez sur le niveau de gravité pour ouvrir la page **alertes** , destinée à afficher les alertes. Dans la page **alertes** , vous pouvez cliquer sur une alerte individuelle pour afficher des détails supplémentaires sur cette alerte, y compris les actions recommandées. Vous pouvez également désactiver l’alerte si le problème a été résolu.

- La zone de **tâches** fournit un instantané des tâches récentes sur tous les périphériques virtuels qui sont connectés à votre service. Il existe des liens que vous pouvez utiliser pour consulter les tâches qui sont actuellement en cours et ceux ayant réussi ou échoué au cours des 24 dernières heures. 

- La zone de **coup de œil** sur la droite de la page fournit des informations utiles telles que de l’état du service, nombre total de périphériques virtuels connectés sur le service, l’emplacement du service et les détails de l’abonnement est associé au service. Il existe également un lien vers le journal des opérations. Cliquez sur le lien pour afficher une liste de toutes les opérations de service de StorSimple le gestionnaire terminé. 

Vous pouvez utiliser la page de tableau de bord de service Gestionnaire de StorSimple pour lancer les tâches suivantes :

- Obtention de la clé de l’enregistrement de service.
- Afficher les journaux de l’opération.

## <a name="get-the-service-registration-key"></a>Obtention de la clé d’inscription de service

La clé d’inscription du service est utilisée pour enregistrer un périphérique virtuel de StorSimple avec le service Gestionnaire de StorSimple, afin que le périphérique apparaît dans le portail Azure classique pour les actions de gestion supplémentaires. La clé est créée sur le premier périphérique virtuel et partagée avec les autres périphériques virtuels. 

**Clé d’enregistrement** (en bas de la page), la boîte de dialogue **Clé d’inscription du Service** dans lequel vous pouvez copier la clé d’inscription de service en cours dans le Presse-papiers ou régénérer la clé d’inscription du service s’ouvre.

![clé d’enregistrement](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

Régénération de la clé n’affecte pas les périphériques virtuels précédemment inscrits : il affecte uniquement les périphériques virtuels qui sont enregistrés auprès du service une fois que la clé est régénérée.

Pour plus d’informations sur l’obtention de la clé d’inscription du service, consultez pour [obtenir la clé de l’enregistrement du service](storsimple-ova-manage-service.md#get-the-service-registration-key).

## <a name="view-the-operations-logs"></a>Afficher les journaux des opérations

Vous pouvez afficher les journaux de l’opération en cliquant sur le lien de journaux d’opération disponible dans le volet **d’Aperçu rapide** du tableau de bord. Vous accédez alors à la page Gestion des services, où vous pouvez filtrer et consultez les journaux spécifiques à votre service de gestionnaire de StorSimple.

![Journal des opérations](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [utiliser l’interface utilisateur pour administrer votre tableau virtuel StorSimple site web local](storsimple-ova-web-ui-admin.md).