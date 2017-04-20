<properties 
   pageTitle="Restauration d’un volume de StorSimple à partir de la sauvegarde | Microsoft Azure"
   description="Explique comment utiliser la page de catalogue de sauvegarde du service Gestionnaire de StorSimple pour restaurer un volume de StorSimple à partir d’un jeu de sauvegarde."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restauration d’un volume de StorSimple à partir d’un jeu de sauvegarde

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Vue d’ensemble

La page **Catalogue de sauvegarde** affiche tous les jeux de sauvegarde créés lors des sauvegardes manuelles ou automatisées. Vous pouvez utiliser cette page pour répertorier toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionner ou supprimer les sauvegardes, ou utiliser une sauvegarde pour la restauration ou le clonage d’un volume.

 ![Page de catalogue de sauvegarde](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Ce didacticiel explique comment utiliser la page de **Catalogue de sauvegarde** pour restaurer un volume sur le périphérique à partir d’un jeu de sauvegarde.

## <a name="how-to-use-the-backup-catalog"></a>Comment faire pour utiliser le catalogue de sauvegarde 

La page **Catalogue de sauvegarde** fournit une requête qui vous aide à affiner votre sauvegarde définie la sélection. Vous pouvez filtrer les jeux de sauvegarde qui sont récupérés en fonction des paramètres suivants :

- **Périphérique** – le périphérique sur lequel le jeu de sauvegarde a été créé.
- **Stratégie de sauvegarde** ou un **volume** – la stratégie de sauvegarde ou le volume associé à ce jeu de sauvegarde.
- **À partir** d’et **à** , la plage de date et d’heure lors de la création du jeu de sauvegarde.

Les jeux de sauvegarde filtrés sont ensuite sous forme de tableau basé sur les attributs suivants :

- **Nom** : le nom de la stratégie de sauvegarde ou le volume associé avec le jeu de sauvegarde.
- **Taille** -la taille réelle du jeu de sauvegarde.
- **Créé le** – date et heure auxquelles les sauvegardes ont été créés. 
- **Type** – les jeux de sauvegarde peuvent être des snapshots locaux ou des snapshots en nuage. Un instantané local est une sauvegarde de toutes vos données de volume stocké localement sur le périphérique, qu’un instantané du nuage fait référence à la sauvegarde des données d’un volume résidant dans le nuage. Snapshots locaux fournissent un accès plus rapide, des instantanés de nuage ont été choisis pour la résilience des données.
- **Initié par** : les sauvegardes peuvent être lancées automatiquement selon une planification ou manuellement par un utilisateur. (Vous pouvez utiliser une stratégie de sauvegarde pour planifier des sauvegardes. Sinon, vous pouvez utiliser l’option **prendre la sauvegarde** pour effectuer une sauvegarde interactive.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Comment faire pour restaurer le volume de votre StorSimple à partir d’une sauvegarde

Vous pouvez utiliser la page **Catalogue de sauvegarde** pour restaurer le volume de votre StorSimple à partir d’une sauvegarde spécifique. 

> [AZURE.WARNING] Restauration à partir d’une sauvegarde remplacera les volumes existants à partir de la sauvegarde. Cela peut entraîner la perte de toutes les données ont été écrites après que la sauvegarde a été effectuée.

Avant de lancer une restauration sur un volume, assurez-vous que le volume est hors connexion. Vous devrez prendre le volume hors connexion sur l’hôte du premier et puis sur le périphérique. Suivez les étapes de [prendre un volume hors connexion](storsimple-manage-volumes.md#take-a-volume-offline). Procédez comme suit pour restaurer un volume à partir d’un jeu de sauvegarde.

### <a name="to-restore-from-a-backup-set"></a>Restaurer à partir d’un jeu de sauvegarde

1. Dans la page du service Gestionnaire de StorSimple, cliquez sur l’onglet **catalogue de sauvegarde** .

    ![Catalogue de sauvegarde](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. Sélectionnez une sauvegarde comme suite :
  1. Sélectionnez le périphérique approprié.
  2. Dans la liste déroulante, choisissez la stratégie de sauvegarde ou le volume pour la sauvegarde que vous souhaitez sélectionner.
  3. Spécifiez la plage de temps.
  4. Cliquez sur l’icône de vérification ![Vérifiez l’icône](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) Pour exécuter cette requête.
 
    Les sauvegardes associées le volume sélectionné ou la stratégie de sauvegarde doit apparaître dans la liste des jeux de sauvegarde.

3. Développez la jeu de sauvegarde pour afficher les volumes associés. Ces volumes doivent être prises en mode hors connexion sur l’hôte et le périphérique avant de pouvoir les restaurer. Suivez les étapes de [prendre un volume hors connexion](storsimple-manage-volumes.md#take-a-volume-offline).

    >  [AZURE.IMPORTANT] Assurez-vous que vous avez pris les volumes en mode hors connexion sur l’hôte en premier lieu, avant de prendre les volumes hors ligne sur le périphérique. Si vous ne prenez pas les volumes en mode hors connexion sur l’hôte, il peut potentiellement entraîner une corruption des données.

4. Sélectionnez un jeu de sauvegarde. Cliquez sur **restaurer** en bas de la page.

6. Vous serez invité à confirmer. 

    ![Page de confirmation](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. Passez en revue les informations de restauration, puis cliquez sur l’icône de vérification ![Vérifiez l’icône de](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Ceci va démarrer un travail de restauration que vous pouvez afficher en accédant à la page **tâches** . 

8. Une fois la restauration terminée, vous pouvez vérifier que le contenu de vos volumes est remplacé par les volumes à partir de la sauvegarde.

![Vidéo disponible](./media/storsimple-restore-from-backup-set/Video_icon.png) **vidéo disponible**

Pour regarder une vidéo qui montre comment vous pouvez utiliser le clone et restaurer les fonctions de StorSimple pour récupérer les fichiers supprimés, cliquez [ici](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Gérer les StorSimple volumes](storsimple-manage-volumes.md).

- Découvrez comment [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
