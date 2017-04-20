<properties 
   pageTitle="Les tâches de sauvegarde de gestionnaire de snapshots StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC du Gestionnaire de snapshots de StorSimple, d’afficher et de gérer les travaux de sauvegarde planifiées, en cours d’exécution et terminées."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/26/2016"
   ms.author="v-sharos" />


# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>StorSimple Gestionnaire de snapshots permet d’afficher et de gérer les tâches de sauvegarde

## <a name="overview"></a>Vue d’ensemble

Noeud **tâches** , dans le volet de **l’arborescence** affiche les **planifiée**, **dernières 24 heures**et **exécute** les tâches de sauvegarde que vous avez lancé interactivement ou par une stratégie configurée. 

Ce didacticiel explique comment vous pouvez utiliser le nœud **travaux** pour afficher des informations sur les tâches de sauvegarde planifiées, récentes et en cours d’exécution. (La liste des tâches et les informations correspondantes s’affiche dans le volet de **résultats** ). En outre, vous pouvez avec le bouton droit à un travail répertorié et voir un menu contextuel qui répertorie les actions disponibles.

## <a name="view-scheduled-jobs"></a>Afficher les tâches planifiées

La procédure suivante permet d’afficher les tâches de sauvegarde planifiées.

#### <a name="to-view-scheduled-jobs"></a>Pour afficher les tâches planifiées

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet de **l’arborescence** , développez le nœud de **tâches** , puis cliquez sur **planifiée**. Les informations suivantes apparaissent dans le volet de **résultats** :

    - **Nom** : le nom de la capture instantanée planifiée

    - **Prochaine exécution** – la date et l’heure de la prochaine capture programmée

    - **Dernière exécution** – la date et l’heure de la dernière capture instantanée planifiée

    >[AZURE.NOTE] Pour les captures instantanées uniquement uniques, la **Prochaine exécution** et **Dernière exécution** sera le même. 
 
    ![Tâches de sauvegarde planifiées](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
 
3. Pour effectuer des actions supplémentaires sur une tâche spécifique, cliquez sur le nom du travail dans le volet **résultats** et sélectionnez parmi les options de menu.

## <a name="view-recent-jobs"></a>Afficher les travaux récents

La procédure suivante permet d’afficher de sauvegarde et de restauration des tâches effectuées au cours des 24 dernières heures.

#### <a name="to-view-recent-jobs"></a>Pour afficher les projets récents

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud de **tâches** , puis cliquez sur **dernières 24 heures**. Le volet **résultats** affiche les tâches de sauvegarde pour les dernières 24 heures (pour un maximum de 64 travaux). Les informations suivantes apparaissent dans le volet de **résultats** , selon les options **d’affichage** que vous spécifiez :

    - **Nom** : le nom de la capture instantanée planifiée.
 
    - **Commencé** – la date et l’heure de début lors de la capture instantanée.

    - **Arrêté** – la date et l’heure de la capture instantanée terminée ou a été arrêtée.

    - Le délai **écoulé** , le temps écoulé entre la **démarré** et **arrêté** .

    - **État** -l’état de la tâche a été récemment effectuée. **Réussite** indique que la sauvegarde a été créée avec succès. **Échec** indique que la tâche ne s’est pas exécuté correctement.

    - **Informations** – la raison de l’échec.

    - **Octets traités (Mo)** , la quantité de données dans le groupe de volumes qui a été traité (en Mo). 

    ![Travaux exécutés au cours des 24 dernières heures](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 

3. Pour effectuer des actions supplémentaires sur une tâche spécifique, cliquez sur le nom du travail dans le volet **résultats** et sélectionnez parmi les options de menu.

    ![Supprimer une tâche](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png) 
     
## <a name="view-currently-running-jobs"></a>Permet d’afficher les tâches en cours d’exécution

Utilisez la procédure suivante pour afficher les tâches en cours d’exécution.

#### <a name="to-view-currently-running-jobs"></a>Pour afficher les tâches en cours d’exécution

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud de **tâches** , puis cliquez sur **en cours d’exécution**. Selon les options **d’affichage** spécifiées, les informations suivantes apparaissent dans le volet de **résultats** : 

    - **Nom** : le nom de la capture instantanée planifiée.

    - **Commencé** – la date et l’heure de début lors de la capture instantanée.

    - **Point de contrôle** : l’action en cours de la sauvegarde.

    - **Statut** – le pourcentage d’achèvement.
    
    - **Écoulé** – le laps de temps écoulé depuis le début de la sauvegarde. 

    - **Débit moyen (Mo)** – rapport du nombre total d’octets de données traitées à celle de la durée totale de traitement (Mo).

    - **Octets traités (Mo)** : nombre total d’octets de données traitées (en Mo).

    - **Octets écrits (Mo)** : nombre total d’octets de données écrites (en Mo). Il comprend les données ainsi que les métadonnées et est donc généralement plus importantes que les octets traités.

    ![Travaux en cours d’exécution](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. Pour effectuer des actions supplémentaires sur une tâche spécifique, cliquez sur le nom du travail dans le volet **résultats** et sélectionnez parmi les options de menu.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le Gestionnaire de snapshots StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- Découvrez comment [utiliser le Gestionnaire de snapshots StorSimple pour gérer le catalogue de sauvegarde](storsimple-snapshot-manager-manage-backup-catalog.md).















            


 

