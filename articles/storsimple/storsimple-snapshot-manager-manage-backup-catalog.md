<properties 
   pageTitle="Catalogue de sauvegarde de gestionnaire de snapshots StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC du Gestionnaire de snapshots de StorSimple, d’afficher et de gérer le catalogue de sauvegarde."
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

# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Utilisez StorSimple Snapshot Manager pour gérer le catalogue de sauvegarde

## <a name="overview"></a>Vue d’ensemble

La fonction principale du Gestionnaire de snapshots StorSimple doit vous permettent de créer des copies de sauvegarde cohérentes avec les applications de volumes de StorSimple sous la forme de captures instantanées. Les snapshots sont répertoriés dans un fichier XML appelé *catalogue de sauvegarde*. Le catalogue de sauvegarde organise les snapshots par groupe de volumes, puis par instantané local ou un nuage instantané. 

Ce didacticiel décrit comment vous pouvez utiliser le nœud **Catalogue de sauvegarde** pour effectuer les tâches suivantes :

- Restauration d’un volume 
- Un volume ou un groupe de volumes du clone 
- Supprimer une sauvegarde 
- Récupérer un fichier
- Restaurer la base de données du Gestionnaire de snapshots de Storsimple

Vous pouvez afficher le catalogue de sauvegarde en développant le nœud **Catalogue de sauvegarde** dans le volet de **portée** et puis en développant le groupe de volumes.

- Si vous cliquez sur le nom de groupe de volumes, le volet **résultats** affiche le nombre de snapshots locaux et des instantanés de cloud disponibles pour le groupe de volumes. 

- Si vous cliquez sur **Instantané Local** ou un **Nuage instantané**, le volet **résultats** affiche les informations suivantes sur chaque instantané de sauvegarde (en fonction de vos paramètres **d’affichage** ) : 

    - **Nom** – la durée de la capture instantanée. 

    - **Type** – s’il s’agit d’un instantané local ou un instantané du nuage. 

    - **Propriétaire** : le propriétaire du contenu. 

    - **Disponible** – si l’instantané est actuellement disponible. **True** indique que la capture instantanée est disponible et peut être restaurée ; **La valeur False** indique que la capture instantanée n’est plus disponible. 

    - **Importés** – indique si la sauvegarde a été importée. **La valeur True** indique que la sauvegarde a été importée à partir du service Gestionnaire de StorSimple au moment où que le périphérique a été configuré dans le Gestionnaire de snapshots de StorSimple ; **False** indique qu’il n’a pas été importé, mais il a été créé par le Gestionnaire de snapshots StorSimple. (Vous pouvez facilement identifier un groupe de volumes importés car un suffixe est ajouté qui identifie le périphérique à partir duquel le groupe de volumes a été importé.)

    ![Catalogue de sauvegarde](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)

- Si vous développez **Instantané Local** ou un **Nuage instantané**, puis cliquez sur un nom d’instantané individuels, le volet **résultats** affiche les informations suivantes sur la capture instantanée que vous avez sélectionné :

    - **Nom** – le volume identifié par une lettre de lecteur. 

    - **Nom local** – le nom local du lecteur (si disponible). 

    - Le **périphérique** – le nom du périphérique sur lequel réside le volume. 

    - **Disponible** – si l’instantané est actuellement disponible. **True** indique que la capture instantanée est disponible et peut être restaurée ; **La valeur False** indique que la capture instantanée n’est plus disponible. 


## <a name="restore-a-volume"></a>Restauration d’un volume

Utilisez la procédure suivante pour restaurer un volume de sauvegarde.

#### <a name="prerequisites"></a>Conditions préalables

Si vous ne le n'avez pas déjà fait, créer un volume et un groupe de volumes et supprimez ensuite le volume. Par défaut, Gestionnaire de snapshots StorSimple sauvegarde un volume avant de permettre la suppression. Cette précaution peut éviter la perte de données si le volume est supprimé par accident, ou si les données doivent être récupérées pour une raison quelconque. 

Gestionnaire de snapshots StorSimple affiche le message suivant lors de la création de la sauvegarde de précaution.

![Message de l’instantané automatique](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

>[AZURE.IMPORTANT]Vous ne pouvez pas supprimer un volume qui fait partie d’un groupe de volumes. L’option de suppression n’est pas disponible. <br>

#### <a name="to-restore-a-volume"></a>Pour restaurer un volume

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet de **l’arborescence** , développez le nœud du **Catalogue de sauvegarde** , développez un groupe de volumes, puis cliquez sur **Snapshots locaux** ou **Des Snapshots de nuage**. Une liste de clichés instantanés de sauvegarde s’affiche dans le volet de **résultats** . 

3. Trouver la sauvegarde que vous souhaitez restaurer, cliquez du bouton droit, puis cliquez sur **restaurer**. 

    ![Restaurer le catalogue de sauvegarde](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 

4. Sur la page de confirmation, passez en revue les détails, tapez **Confirmer**et puis cliquez sur **OK**. Gestionnaire de snapshots StorSimple utilise la sauvegarde pour restaurer le volume. 

    ![Restauration du message de confirmation](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 

5. Vous pouvez contrôler l’action de restauration en cours d’exécution. Dans le volet de **l’arborescence** , développez le nœud de **tâches** , puis cliquez sur **en cours d’exécution**. Les détails de la tâche apparaissent dans le volet de **résultats** . Lorsque le travail de restauration est terminé, les détails de la tâche sont transférées vers la liste des **dernières 24 heures** .

## <a name="clone-a-volume-or-volume-group"></a>Un volume ou un groupe de volumes du clone

Utilisez la procédure suivante pour créer un doublon (clones) d’un volume ou d’un groupe de volumes.

#### <a name="to-clone-a-volume-or-volume-group"></a>Pour cloner un volume ou un groupe de volumes

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud du **Catalogue de sauvegarde** , développez un groupe de volumes, puis cliquez sur **Snapshots de nuage**. Une liste de sauvegardes s’affiche dans le volet de **résultats** .

3. Recherchez le volume ou le groupe de volumes que vous souhaitez cloner, cliquez droit sur le volume ou le nom du groupe et cliquez sur **cloner**. La boîte de dialogue **Clone d’instantané Cloud** s’affiche.

    ![Cloner un instantané du nuage](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Complétez la boîte de dialogue **Clone d’instantané nuage** comme suit : 

    1. Dans la zone de texte **nom** , tapez un nom pour le volume de clonage. Ce nom apparaîtra dans le nœud de **Volumes** . 

    2. (Facultatif) sélectionnez le **lecteur**, puis sélectionnez une lettre de lecteur dans la liste déroulante. 

    3. (Facultatif) sélectionnez le **Dossier (NTFS)**, tapez un chemin d’accès du dossier ou cliquez sur Parcourir et sélectionnez un emplacement pour le dossier. 

    4. Cliquez sur **créer**.

5. Lorsque le processus de clonage est terminé, vous devez initialiser le volume de clonage. Démarrez le Gestionnaire de serveur et puis démarrez Gestion des disques. Pour obtenir des instructions détaillées, reportez-vous à la section [montage de volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Après son initialisation, le volume sera répertorié sous le nœud de **Volumes** dans le volet de **portée** . Si vous ne voyez pas le volume indiqué, actualiser la liste des volumes (cliquez sur le nœud de **Volumes** , puis cliquez sur **Actualiser**).

## <a name="delete-a-backup"></a>Supprimer une sauvegarde

Utilisez la procédure suivante pour supprimer une capture instantanée à partir du catalogue de sauvegarde. 

>[AZURE.NOTE]Supprimez une capture instantanée des données sauvegardées associées à la capture instantanée. Toutefois, le processus de nettoyage de données à partir du cloud peut prendre un certain temps.<br>
 
#### <a name="to-delete-a-backup"></a>Pour supprimer une sauvegarde

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud du **Catalogue de sauvegarde** , développez un groupe de volumes, puis cliquez sur **Snapshots locaux** ou **Des Snapshots de nuage**. Une liste des snapshots s’affiche dans le volet de **résultats** . 

3. Avec le bouton droit de la capture instantanée que vous souhaitez supprimer, puis cliquez sur **Supprimer**.

4. Lorsque le message de confirmation s’affiche, cliquez sur **OK**. 

## <a name="recover-a-file"></a>Récupérer un fichier

Si un fichier est accidentellement supprimé à partir d’un volume, vous pouvez récupérer le fichier en récupérant un instantané antérieur à la suppression, à l’aide de la capture instantanée pour créer un clone du volume et puis en copiant le fichier du volume cloné vers le volume d’origine.

#### <a name="prerequisites"></a>Conditions préalables

Avant de commencer, assurez-vous que vous disposez d’une sauvegarde en cours du groupe de volumes. Ensuite, supprimez un fichier stocké sur un des volumes dans ce groupe de volumes. Enfin, procédez comme suit pour restaurer le fichier supprimé à partir de votre sauvegarde. 

#### <a name="to-recover-a-deleted-file"></a>Pour récupérer un fichier supprimé

1. Cliquez sur l’icône Gestionnaire de snapshots StorSimple sur votre bureau. La fenêtre de la console Gestionnaire de snapshots de StorSimple s’affiche. 

2. Dans le volet de **l’arborescence** , développez le nœud de **Catalogue de sauvegarde** et accédez à une capture instantanée qui contient le fichier supprimé. En règle générale, vous devez sélectionner un instantané qui a été créé avant la suppression. 

3. Recherchez le volume que vous souhaitez cloner, cliquez du bouton droit et cliquez sur **cloner**. La boîte de dialogue **Clone d’instantané Cloud** s’affiche.

    ![Cloner un instantané du nuage](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Complétez la boîte de dialogue **Clone d’instantané nuage** comme suit : 

   1. Dans la zone de texte **nom** , tapez un nom pour le volume de clonage. Ce nom apparaîtra dans le nœud de **Volumes** . 

   2. (Facultatif) Sélectionnez le **lecteur**et puis sélectionnez une lettre de lecteur dans la liste déroulante. 

   3. (Facultatif) Sélectionner le **Dossier (NTFS)**, tapez un chemin d’accès du dossier ou cliquez sur **Parcourir** et sélectionnez un emplacement pour le dossier. 

   4. Cliquez sur **créer**. 

5. Lorsque le processus de clonage est terminé, vous devez initialiser le volume de clonage. Démarrez le Gestionnaire de serveur et puis démarrez Gestion des disques. Pour obtenir des instructions détaillées, reportez-vous à la section [montage de volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Après son initialisation, le volume sera répertorié sous le nœud de **Volumes** dans le volet de **portée** . 

    Si vous ne voyez pas le volume indiqué, actualiser la liste des volumes (cliquez sur le nœud de **Volumes** , puis cliquez sur **Actualiser**).

6. Ouvrez le dossier NTFS qui contient le volume de clonage, développez le nœud de **Volumes** et ouvrez le volume cloné. Recherchez le fichier que vous souhaitez récupérer et le copier dans le volume principal.

7. Après avoir restauré le fichier, vous pouvez supprimer le dossier NTFS contenant le volume cloné.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Restaurer la base de données du Gestionnaire de snapshots de StorSimple

Vous devez sauvegarder régulièrement la base de données du Gestionnaire de snapshots StorSimple sur l’ordinateur hôte. Si un sinistre se produit ou si l’ordinateur hôte échoue pour une raison quelconque, vous pouvez ensuite le restaurer à partir de la sauvegarde. Création de la sauvegarde de la base de données est un processus manuel.

#### <a name="to-back-up-and-restore-the-database"></a>Pour sauvegarder et restaurer la base de données

1. Arrêtez le Service de gestion de Microsoft StorSimple :

    1. Démarrez le Gestionnaire de serveur.

    2. Sur le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.

    3. Dans la fenêtre **Services** , sélectionnez le **Service de gestion de StorSimple de Microsoft**.

    4. Dans le volet droit, sous le **Service de gestion de StorSimple de Microsoft**, cliquez sur **Arrêter le service**.

2. Sur l’ordinateur hôte, accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData est un dossier masqué.
 
3. Rechercher le fichier de catalogue XML, copiez le fichier et stocker la copie dans un endroit sûr, ou dans le nuage. Si l’hôte échoue, vous pouvez utiliser ce fichier de sauvegarde pour récupérer les règles de sauvegarde que vous avez créé dans le Gestionnaire de snapshots StorSimple.

    ![Fichier de catalogue de sauvegarde StorSimple Azure](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)

4. Redémarrez le Service de gestion de Microsoft StorSimple : 

    1. Sur le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.
    
    2. Dans la fenêtre **Services** , sélectionnez le **Service de gestion de StorSimple de Microsoft**.

    3. Dans le volet droit, sous le **Service de gestion de StorSimple de Microsoft**, cliquez sur **redémarrer le service**.

5. Sur l’ordinateur hôte, accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

6. Supprimer le fichier de catalogue XML et remplacez-le par la version de sauvegarde que vous avez créé. 

7. Cliquez sur l’icône Gestionnaire de snapshots StorSimple du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur [l’utilisation du Gestionnaire de snapshots StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- Plus d’informations sur les [workflows et les tâches du Gestionnaire de capture instantanée StorSimple](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).
