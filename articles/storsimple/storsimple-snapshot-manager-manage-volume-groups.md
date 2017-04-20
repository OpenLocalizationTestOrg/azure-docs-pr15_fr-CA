<properties 
   pageTitle="Les groupes de volumes de gestionnaire de snapshots StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC du Gestionnaire de snapshots StorSimple, pour créer et gérer des groupes de volumes."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Gestionnaire de capture instantanée StorSimple permet de créer et de gérer des groupes de volumes

## <a name="overview"></a>Vue d’ensemble

Vous pouvez utiliser le nœud **Groupes de volumes** dans le volet de **portée** à l’affectation de volumes aux groupes de volumes, afficher des informations sur un groupe de volumes, planifier des sauvegardes et modifier des groupes de volumes. 

Groupes de volumes sont des pools de volumes apparentés permet de garantir que vos sauvegardes sont cohérentes avec les applications. Pour plus d’informations, consultez [groupes de volumes et des Volumes](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) et [l’intégration avec le Service de cliché instantané de Volume Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

>[AZURE.IMPORTANT] 
>
> * Tous les volumes dans un groupe de volumes doivent provenir d’un fournisseur de services de cloud unique.
> 
> * Lorsque vous configurez des groupes de volumes, ne mélangez pas les volumes partagés de cluster (CSVs) et non-CSVs dans le même groupe de volumes. StorSimple Gestionnaire de snapshots ne gère pas un mélange de CSVs et non-CSVs dans la même capture instantanée.
 
![Nœud groupes de volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**La figure 1 : Nœud groupes de volumes StorSimple Snapshot Manager** 

Ce didacticiel explique comment vous pouvez utiliser le Gestionnaire de snapshots StorSimple à :

- Afficher des informations sur les groupes de volumes 
- Créer un groupe de volumes
- Sauvegarder un groupe de volumes
- Modifier un groupe de volumes
- Supprimer un groupe de volumes

Toutes ces actions sont également disponibles dans le volet **Actions** .
 
## <a name="view-volume-groups"></a>Afficher les groupes de volumes

Si vous cliquez sur le nœud **Groupes de volumes** , le volet **résultats** affiche les informations suivantes sur chaque groupe de volumes, en fonction de la colonne que vous sélectionnez. (Les colonnes dans le volet de **résultats** sont configurables. Cliquez droit sur le nœud **Volumes** , sélectionnez **affichage**et puis sélectionnez **Ajout/Suppression de colonnes**.)

Colonne de résultats | Description 
:--------------|:------------ 
Nom           | La colonne **nom** indique le nom du groupe de volumes.
Application    | La colonne **Applications** indique le nombre d’enregistreurs VSS actuellement installé et en cours d’exécution sur l’hôte Windows.
Sélectionné       | La colonne **sélectionné** indique le nombre de volumes qui sont contenus dans le groupe de volumes. Un zéro (0) indique qu’aucune application n’est associée avec les volumes dans le groupe de volumes.
Importé       | La colonne **Imported** indique le nombre de volumes importés. Lorsque cette propriété a la valeur **True**, cette colonne indique qu’un groupe de volumes ont été importé à partir du portail classique Azure n'a pas été créé dans le Gestionnaire de snapshots StorSimple.
 
>[AZURE.NOTE] Les groupes de volumes StorSimple Snapshot Manager sont également affichés sous l’onglet **Stratégies de sauvegarde** dans Azure portal classique.
 
## <a name="create-a-volume-group"></a>Créer un groupe de volumes

Utilisez la procédure suivante pour créer un groupe de volumes.

#### <a name="to-create-a-volume-group"></a>Pour créer un groupe de volumes

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet de **portée** , cliquez droit sur des **Groupes de volumes**, puis cliquez sur **Créer un groupe de volumes**. 

    ![Créer le groupe de volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
 
    La boîte de dialogue **créer un groupe de volumes** s’affiche. 

    ![Créer une boîte de dialogue de groupe de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png) 

3.  Entrez les informations suivantes : 

    1. Dans la zone **nom** , tapez un nom unique pour le nouveau groupe de volumes. 

    2. Dans la zone des **Applications** , sélectionnez les applications associées avec les volumes qui seront ajoutés au groupe de volumes. 

        Les listes de zone **Applications** uniquement les applications qui utilisent des volumes de StorSimple et ont les enregistreurs VSS activées pour elles. Un scripteur VSS est activé uniquement si tous les volumes qui connaît le writer sont StorSimple volumes. Si la boîte d’Applications est vide, aucune application qui utilisent des volumes d’Azure StorSimple et prennent en charge les writers VSS n’est installées. (Actuellement, Azure StorSimple prend en charge Microsoft Exchange et SQL Server.) Pour plus d’informations sur les writers VSS, reportez-vous à la section [intégration avec le Service de cliché instantané de Volume Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

        Si vous sélectionnez une application, tous les volumes qui lui sont associés sont automatiquement sélectionnées. À l’inverse, si vous sélectionnez les volumes associés à une application spécifique, l’application est automatiquement sélectionnée dans la zone des **Applications** . 

    3. Dans la zone de **Volumes** , sélectionnez StorSimple les volumes à ajouter au groupe de volumes. 

      - Vous pouvez inclure des volumes avec des partitions simples ou multiples. (Plusieurs volumes de partition peuvent être des disques dynamiques ou des disques de base avec plusieurs partitions.) Un volume qui contient plusieurs partitions est traité comme une seule unité. Par conséquent, si vous ajoutez uniquement une des partitions dans un groupe de volumes, toutes les autres partitions sont ajoutées automatiquement à ce groupe de volumes en même temps. Après avoir ajouté un volume de partition plusieurs à un groupe de volumes, le volume de partition plusieurs continue d’être traité comme une seule unité.

      - Vous pouvez créer des groupes de volumes vides en leur assignant ne pas tous les volumes. 

      - Ne mélangez pas les volumes partagés de cluster (CSVs) et non-CSVs dans le même groupe de volumes. StorSimple Gestionnaire de snapshots ne gère pas un mélange de volumes CSV et les volumes non-CSV dans la même capture instantanée. 

4. Cliquez sur **OK** pour enregistrer le groupe de volumes.

## <a name="back-up-a-volume-group"></a>Sauvegarder un groupe de volumes

La procédure suivante permet de sauvegarder un groupe de volumes.

#### <a name="to-back-up-a-volume-group"></a>Pour sauvegarder un groupe de volumes

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud **Groupes de volumes** , droit sur un nom de groupe de volumes, puis cliquez sur **Effectuer une sauvegarde**. 

    ![Sauvegardez immédiatement les groupe de volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)

3. Dans la boîte de dialogue **Effectuer la sauvegarde** , sélectionnez **Instantané Local** ou un **Nuage instantané**, puis cliquez sur **créer**. 

    ![Prendre la boîte de dialogue sauvegarde](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png) 

4. Pour vérifier que la sauvegarde est en cours d’exécution, développez le nœud de **tâches** , puis cliquez sur **en cours d’exécution**. La sauvegarde doit être répertoriée.

5. Pour afficher la capture instantanée terminée, développez le nœud du **Catalogue de sauvegarde** , développez le nom de groupe de volume, puis cliquez sur **Instantané Local** ou un **Nuage instantané**. La sauvegarde sera répertoriée si elle s’est terminée correctement. 

## <a name="edit-a-volume-group"></a>Modifier un groupe de volumes

Utilisez la procédure suivante pour modifier un groupe de volumes.

#### <a name="to-edit-a-volume-group"></a>Pour modifier un groupe de volumes

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud **Groupes de volumes** , droit sur un nom de groupe de volumes, puis cliquez sur **Modifier**. 

3. La boîte de dialogue **créer un groupe de volumes **s’affiche. Vous pouvez modifier les entrées de **nom**, les **Applications**et les **Volumes** . 

4. Cliquez sur **OK** pour enregistrer vos modifications.

## <a name="delete-a-volume-group"></a>Supprimer un groupe de volumes

Utilisez la procédure suivante pour supprimer un groupe de volumes. 

>[AZURE.WARNING] Cela supprime également toutes les sauvegardes associées au groupe de volumes.

#### <a name="to-delete-a-volume-group"></a>Pour supprimer un groupe de volumes

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet de **l’arborescence** , développez le nœud **Groupes de volumes** , cliquez droit sur le nom d’un groupe de volumes et puis cliquez sur **Supprimer**. 

3. La boîte de dialogue **Supprimer un groupe de volumes** s’affiche. Tapez le **Confirmer** dans la zone de texte, puis cliquez sur **OK**. 

    Le groupe de volume supprimé disparaît de la liste dans le volet de **résultats** et toutes les sauvegardes qui sont associés à ce groupe de volumes sont supprimés du catalogue de sauvegarde.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le Gestionnaire de snapshots StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- Découvrez comment [utiliser le Gestionnaire de snapshots StorSimple pour créer et gérer des stratégies de sauvegarde](storsimple-snapshot-manager-manage-backup-policies.md).
