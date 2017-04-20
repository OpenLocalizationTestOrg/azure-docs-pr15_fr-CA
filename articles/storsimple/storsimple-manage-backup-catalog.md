<properties 
   pageTitle="Gérer votre catalogue de sauvegarde StorSimple | Microsoft Azure"
   description="Explique comment utiliser la page de catalogue de sauvegarde du service Gestionnaire de StorSimple à la liste, sélectionner et supprimer des jeux de sauvegarde d’un volume."
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
   ms.date="04/28/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>Le service Gestionnaire de StorSimple permet de gérer votre catalogue de sauvegarde

## <a name="overview"></a>Vue d’ensemble

La page **Catalogue de sauvegarde** du service Gestionnaire de StorSimple affiche tous les jeux de sauvegarde créés lors des sauvegardes manuelles ou programmées. Vous pouvez utiliser cette page pour répertorier toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionner ou supprimer les sauvegardes, ou utiliser une sauvegarde pour la restauration ou le clonage d’un volume.

Ce didacticiel explique comment afficher, sélectionner et supprimer un jeu de sauvegarde. Pour savoir comment restaurer votre périphérique de sauvegarde, accédez à [restaurer votre appareil à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md). Pour plus d’informations sur le clonage d’un volume, accédez à [Clone un volume StorSimple](storsimple-clone-volume.md).

![Catalogue de sauvegarde](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

La page **Catalogue de sauvegarde** fournit une requête afin d’affiner votre sauvegarde définie la sélection. Vous pouvez filtrer les jeux de sauvegarde qui sont récupérés, en fonction des paramètres suivants :

- **Périphérique** – le périphérique sur lequel le jeu de sauvegarde a été créé.

- **Stratégie de sauvegarde ou le Volume** à la stratégie de sauvegarde ou le volume associé à ce jeu de sauvegarde.

- **À partir de et à** , la plage de date et d’heure lors de la création du jeu de sauvegarde.

Les jeux de sauvegarde filtrés sont ensuite sous forme de tableau basé sur les attributs suivants :

- **Nom** : le nom de la stratégie de sauvegarde ou le volume associé avec le jeu de sauvegarde.

- **Taille** -la taille réelle du jeu de sauvegarde.

- **Créé** – la date et l’heure auxquelles les sauvegardes ont été créés. 

- **Type** – les jeux de sauvegarde peuvent être des snapshots locaux ou des snapshots en nuage. Un instantané local est une sauvegarde de toutes vos données de volume stocké localement sur le périphérique, qu’un instantané du nuage fait référence à la sauvegarde des données d’un volume résidant dans le nuage. Snapshots locaux fournissent un accès plus rapide, des instantanés de nuage ont été choisis pour la résilience des données.

- **Initié par** : les sauvegardes peuvent être lancées automatiquement par un programme ou manuellement par un utilisateur. Vous pouvez utiliser une stratégie de sauvegarde pour planifier des sauvegardes. Ou bien, vous pouvez utiliser l’option **prendre la sauvegarde** pour effectuer une sauvegarde manuelle.

## <a name="list-backup-sets-for-a-volume"></a>Jeux de sauvegarde de la liste pour un volume
 
Effectuez les étapes suivantes pour répertorier toutes les sauvegardes d’un volume.

#### <a name="to-list-backup-sets"></a>Pour les jeux de sauvegarde de liste

1. Dans la page du service Gestionnaire de StorSimple, cliquez sur l’onglet **catalogue de sauvegarde** .

2. Les sélections de filtre comme suit :

    1. Sélectionnez le périphérique approprié.

    2. Dans la liste déroulante, choisissez un volume pour afficher les sauvegardes correspondant.

    3. Spécifiez la plage de temps.

    4. Cliquez sur l’icône de vérification ![Icône de contrôle](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Pour exécuter cette requête.
 
    Les sauvegardes associées au volume sélectionné doivent apparaître dans la liste des jeux de sauvegarde.

## <a name="select-a-backup-set"></a>Sélectionnez un jeu de sauvegarde

Effectuez les étapes suivantes pour sélectionner une jeu de sauvegarde pour un volume ou d’une stratégie de sauvegarde.

#### <a name="to-select-a-backup-set"></a>Pour sélectionner un jeu de sauvegarde

1. Dans la page du service Gestionnaire de StorSimple, cliquez sur l’onglet **catalogue de sauvegarde** .

2. Les sélections de filtre comme suit :

    1. Sélectionnez le périphérique approprié.

    2. Dans la liste déroulante, choisissez la stratégie de sauvegarde ou le volume pour la sauvegarde que vous souhaitez sélectionner.

    3. Spécifiez la plage de temps.

    4. Cliquez sur l’icône de vérification ![Icône de contrôle](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Pour exécuter cette requête.

    Les sauvegardes associées le volume sélectionné ou la stratégie de sauvegarde doit apparaître dans la liste des jeux de sauvegarde.

3. Sélectionnez et développez un jeu de sauvegarde. Les options de **restauration** et **Supprimer** s’affichent au bas de la page. Vous pouvez effectuer une ou l’autre de ces actions sur le jeu de sauvegarde que vous avez sélectionné.

## <a name="delete-a-backup-set"></a>Supprimer un jeu de sauvegarde

Supprimer une sauvegarde lorsque vous ne souhaitez plus conserver les données qui lui est associées. Effectuez les étapes suivantes pour supprimer un jeu de sauvegarde.

#### <a name="to-delete-a-backup-set"></a>Pour supprimer un jeu de sauvegarde

1. Dans la page du service Gestionnaire de StorSimple, cliquez sur l' **onglet catalogue de sauvegarde**.

2. Les sélections de filtre comme suit :

    1. Sélectionnez le périphérique approprié.

    2. Dans la liste déroulante, choisissez la stratégie de sauvegarde ou le volume pour la sauvegarde que vous souhaitez sélectionner.

    3. Spécifiez la plage de temps.

    4. Cliquez sur l’icône de vérification ![Icône de contrôle](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Pour exécuter cette requête.

    Les sauvegardes associées le volume sélectionné ou la stratégie de sauvegarde doit apparaître dans la liste des jeux de sauvegarde.

3. Sélectionnez et développez un jeu de sauvegarde. Les options de **restauration** et **Supprimer** s’affichent au bas de la page. Cliquez sur **Supprimer**.

4. Vous serez averti lorsque la suppression est en cours et après avoir terminé avec succès. Une fois la suppression terminée, actualisez la requête sur cette page. Le jeu de sauvegarde supprimé n’apparaît plus dans la liste des jeux de sauvegarde.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le catalogue de sauvegarde pour restaurer votre appareil à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md).

- Découvrez comment [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
