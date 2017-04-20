<properties 
   pageTitle="Permet d’afficher et de gérer les travaux de StorSimple | Microsoft Azure"
   description="Décrit la page de travaux du service Gestionnaire de StorSimple et comment l’utiliser pour effectuer le suivi des travaux de sauvegarde planifiées, actuelles et récentes."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs-update-2"></a>Le service Gestionnaire de StorSimple permet d’afficher et de gérer les travaux de StorSimple (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Vue d’ensemble

La page **projets** fournit un seul portail central pour l’affichage et la gestion des travaux qui ont été démarrées sur les périphériques connectés à votre service de gestionnaire de StorSimple. Vous pouvez afficher les tâches planifiées, en cours d’exécution, terminées, annulées et échecs pour plusieurs périphériques. Résultats sont présentés dans un format tabulaire. 

![Page tâches](./media/storsimple-manage-jobs-u2/jobs.png)

Vous pouvez trouver rapidement les tâches qui que vous intéressent en filtrant sur les champs, tels que :

- **Statut** – travaux peuvent être en cours d’exécution, terminée, annulée, échec, annulation ou terminée avec des erreurs.
- **À partir de et vers** – travaux peuvent être filtrées en fonction de la plage de date et d’heure.
- **Type** : le type de tâche peut être sauvegarde, sauvegarde manuelle, restauration, clone, le basculement entre périphériques, créer volume localement épinglé, modifier le volume, mettre à jour, prennent en charge le package ou la configuration de périphériques virtuels.

- **Périphériques** – les tâches sont lancées sur un certain périphérique connecté à votre service.
Les tâches filtrées sont ensuite sous forme de tableau sur les attributs suivants :

    - **Type** – sauvegarde, restauration, sauvegarde manuelle, clone, le basculement du périphérique, créer volume localement épinglé, modifier le volume, mettre à jour, prennent en charge le package ou la configuration de périphériques virtuels.

    - **État** : en cours d’exécution, terminé, annulé, échec, annulation ou terminée avec des erreurs.

    - **Entité** – les tâches peuvent être associés à un périphérique, une stratégie de sauvegarde ou un volume. Par exemple, un travail de clone est associé à un volume, qu’une opération de sauvegarde planifiée est associée à une stratégie de sauvegarde. Un travail de périphériques est créé à la suite d’une reprise après sinistre (DR) ou une opération de restauration.

    - Le **périphérique** – le nom du périphérique sur lequel le travail a été démarré.

    - **Démarré sur** – l’heure de début de la tâche.

    - **Cours** – le pourcentage d’achèvement d’une tâche en cours d’exécution. Pour un travail terminé, cela doit toujours être de 100 %.

La liste des tâches est actualisée toutes les 30 secondes.

Vous pouvez effectuer les actions suivantes liées à des projets sur cette page :

- Afficher les détails du projet

- Annulation d’une tâche

## <a name="view-job-details"></a>Afficher les détails du projet

Effectuez les opérations suivantes pour afficher les détails de n’importe quelle tâche.

#### <a name="to-view-job-details"></a>Pour afficher les détails d’une tâche

1. Dans la page **tâches** , afficher l’ou les travaux que vous intéressent en exécutant une requête avec les filtres appropriés. Vous pouvez rechercher pour terminée, en cours d’exécution ou annulation des travaux.

2. Sélectionnez une tâche.

3. En bas de la page, cliquez sur **Détails**.

4. Dans la boîte de dialogue **Détails de la tâche de sauvegarde** , vous pouvez afficher l’état, les détails, les statistiques de temps et les statistiques de données.
 
    ![Page de détails de projet](./media/storsimple-manage-jobs-u2/JobDetails.png)

## <a name="cancel-a-job"></a>Annulation d’une tâche

Procédez comme suit pour annuler une tâche en cours d’exécution.

>[AZURE.NOTE] Certaines tâches, telles que la modification d’un volume pour modifier le type de volume ou extension d’un volume, ne peut pas être annulées.

### <a name="to-cancel-a-job"></a>Pour annuler un travail

1. Dans la page **tâches** , afficher l’ou les tâches en cours d’exécution que vous souhaitez annuler en exécutant une requête avec les filtres appropriés.

1. Sélectionnez la tâche.

1. En bas de la page, cliquez sur **Annuler**.

1. Lorsque vous êtes invité à confirmer une opération, cliquez sur **Oui**.

Ce travail a été annulé.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer vos stratégies de sauvegarde StorSimple](storsimple-manage-backup-policies.md).

- Découvrez comment [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
