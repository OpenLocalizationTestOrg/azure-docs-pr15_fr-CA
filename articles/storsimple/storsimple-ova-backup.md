<properties 
   pageTitle="Didacticiel de sauvegarde tableau virtuel de StorSimple | Microsoft Azure"
   description="Décrit comment sauvegarder des volumes et des partages de tableau virtuel de StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="back-up-your-storsimple-virtual-array"></a>Sauvegarder votre tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble 

Ce didacticiel s’applique à la version de Microsoft Azure StorSimple tableau virtuel (également connu sous le nom le périphérique virtuel local de StorSimple ou un périphérique virtuel de StorSimple) en cours d’exécution mars 2016 disponibilité générale (GA) ou une version ultérieure.

Le tableau virtuel StorSimple est un hybride nuage local virtuel périphérique de stockage qui peut être configuré comme un serveur de fichiers ou un serveur iSCSI. Il peut créer des sauvegardes, la restauration à partir de sauvegardes et effectuer le basculement de périphériques si la reprise après sinistre est nécessaire. Lorsqu’il est configuré comme un serveur de fichiers, il autorise également la restauration au niveau élément. Ce didacticiel explique comment utiliser le portail classique Azure ou l’interface utilisateur de StorSimple web pour créer des sauvegardes planifiées et manuelles de votre tableau virtuel StorSimple.


## <a name="back-up-shares-and-volumes"></a>Sauvegarder les partages et volumes

Les sauvegardes protègent le point-à-temps, améliorent la capacité de restauration et minimiser les temps de restauration pour les partages et volumes. Vous pouvez sauvegarder un volume ou un partage sur votre appareil de StorSimple de deux manières : **planifiée** ou **manuelle**. Chaque méthode est décrite dans les sections suivantes.

> [AZURE.NOTE] Dans cette version, les sauvegardes planifiées sont créées par une stratégie par défaut qui s’exécute tous les jours à un moment donné et la sauvegarde de tous les partages ou volumes sur le périphérique. Il n’est pas possible de créer des stratégies personnalisées pour les sauvegardes planifiées à ce moment.

## <a name="change-the-backup-schedule"></a>Modifier la planification de sauvegarde

Votre périphérique virtuel StorSimple possède une stratégie de sauvegarde par défaut qui démarre à une heure spécifique de la journée (22:30) et sauvegarde tous les partages ou volumes sur le périphérique une fois par jour. Vous pouvez modifier l’heure à laquelle le démarrage de la sauvegarde, mais la fréquence et la rétention (qui spécifie le nombre de sauvegardes à conserver) ne peut pas être modifiés. Au cours de ces sauvegardes, le périphérique virtuel complet est sauvegardé ; Par conséquent, nous vous recommandons de planifier ces sauvegardes pendant les heures creuses.

Effectuez les opérations suivantes dans [Azure portal classique](https://manage.windowsazure.com/) pour modifier l’heure de début de la sauvegarde par défaut.

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Pour modifier l’heure de début pour la stratégie de sauvegarde par défaut

1. Accédez à l’onglet de **Configuration** de périphérique.

2. Sous la section de **sauvegarde** , spécifiez l’heure de début de la sauvegarde quotidienne.

3. Cliquez sur **Enregistrer**.

### <a name="take-a-manual-backup"></a>Effectuer une sauvegarde manuelle

Outre les sauvegardes planifiées, vous pouvez prendre un manuel (sur demande) sauvegarde à tout moment.

#### <a name="to-create-a-manual-on-demand-backup"></a>Pour créer une sauvegarde manuelle de (à la demande)

1. Accédez à l’onglet **partages** ou **Volumes** .

2. En bas de la page, cliquez sur **tous les de sauvegarde**. Vous devrez vérifier que vous souhaitez effectuer la sauvegarde maintenant. Cliquez sur l’icône de vérification ![Vérifiez l’icône de](./media/storsimple-ova-backup/image3.png) de procéder à la sauvegarde.

    ![confirmation de sauvegarde](./media/storsimple-ova-backup/image4.png)

    Vous serez averti qu’une opération de sauvegarde démarre.

    ![démarrage de sauvegarde](./media/storsimple-ova-backup/image5.png)

    Vous serez averti que le travail a été créé avec succès.

    ![travail de sauvegarde créé](./media/storsimple-ova-backup/image7.png)

3. Pour suivre la progression de la tâche, cliquez sur **Afficher la tâche**.

4. Une fois la sauvegarde terminée, accédez à l’onglet **catalogue de sauvegarde** . Vous devez voir votre sauvegarde terminée.

    ![Sauvegarde terminée](./media/storsimple-ova-backup/image8.png)

5. Définir les sélections de filtre pour le périphérique approprié, la stratégie de sauvegarde et la plage horaire, puis cliquez sur l’icône de vérification ![Vérifiez l’icône](./media/storsimple-ova-backup/image3.png).

    La sauvegarde doit apparaître dans la liste qui s’affiche dans le catalogue des jeux de sauvegarde.

## <a name="view-existing-backups"></a>Afficher les sauvegardes existantes

Effectuez les opérations suivantes dans le portail classique Azure pour afficher les sauvegardes existantes.

#### <a name="to-view-existing-backups"></a>Pour afficher les sauvegardes existantes

1. Dans la page du service Gestionnaire de StorSimple, cliquez sur l’onglet **catalogue de sauvegarde** .

2. Sélectionnez une sauvegarde comme suite :

    1. Sélectionnez le périphérique.

    2. Dans la liste déroulante, sélectionnez le partage ou le volume pour la sauvegarde que vous souhaitez sélectionner.

    3. Spécifiez la plage de temps.

    4. Cliquez sur l’icône de vérification ![](./media/storsimple-ova-backup/image3.png) pour exécuter cette requête.

    Les sauvegardes associées le partage sélectionné ou volume doit apparaître dans la liste des jeux de sauvegarde.

![video_icon](./media/storsimple-ova-backup/video_icon.png) **vidéo disponible**

Regardez la vidéo pour voir comment vous pouvez créer des partages, sauvegarder des partages et restaurer des données sur une baie virtuelle StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur [l’administration de votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).
