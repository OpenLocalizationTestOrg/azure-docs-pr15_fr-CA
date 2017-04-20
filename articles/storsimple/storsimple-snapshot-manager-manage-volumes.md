<properties 
   pageTitle="StorSimple Gestionnaire de snapshots et volumes | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC du Gestionnaire de snapshots de StorSimple, d’afficher et de gérer des volumes et pour configurer des sauvegardes."
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

# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Utilisez le Gestionnaire de snapshots StorSimple pour afficher et gérer des volumes

## <a name="overview"></a>Vue d’ensemble

Vous pouvez utiliser le nœud Gestionnaire de snapshots StorSimple **des Volumes** (dans le volet de **portée** ) pour sélectionner les volumes et d’afficher des informations sur les. Les volumes sont présentés comme des lecteurs qui correspondent aux volumes montés par l’hôte. Le nœud de **Volumes** affiche les volumes locaux et les types de volume sont pris en charge par les StorSimple, y compris les volumes sont détectés à l’aide d’iSCSI et un périphérique. 

Pour plus d’informations sur les volumes pris en charge, passez à la [prise en charge de plusieurs types de volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Liste des volumes dans le volet de résultats](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Le nœud de **Volumes** vous permet également de relancer l’analyse ou de supprimer des volumes après que StorSimple Snapshot Manager découvre les. 

Ce didacticiel explique comment vous pouvez monter, initialiser et formater des volumes et ensuite utiliser le Gestionnaire de snapshots StorSimple à :

- Afficher des informations sur les volumes 
- Supprimer des volumes
- Analyser les volumes 
- Configurer un volume de base et le sauvegarder
- Configuration d’un volume en miroir dynamique et le sauvegarder

>[AZURE.NOTE] Toutes les actions de nœud de **Volume** sont également disponibles dans le volet **Actions** .
 
## <a name="mount-volumes"></a>Montage de volumes

La procédure suivante permet de monter, initialiser et formater des volumes de StorSimple. Cette procédure utilise le composant Gestion des disques, un utilitaire système de gestion des disques durs et les volumes ou partitions correspondantes. Pour plus d’informations sur la gestion des disques, accédez à [Gestion des disques](https://technet.microsoft.com/library/cc770943.aspx) sur le site Web TechNet de Microsoft.

#### <a name="to-mount-volumes"></a>Pour monter les volumes

1. Sur votre ordinateur hôte, démarrez Microsoft iSCSI initiator.

2. Fournir une des adresses IP de l’interface que le portail cible ou adresse IP de découverte et de se connecter au périphérique. Une fois que le périphérique est connecté, les volumes seront accessibles à votre système Windows. Pour plus d’informations sur l’utilisation de l’initiateur Microsoft iSCSI, consultez la section « Connexion à un périphérique cible iSCSI », dans [l’installation et la configuration de Microsoft iSCSI Initiator][1].

3. Pour démarrer le composant Gestion des disques, utilisez une des options suivantes :

    - Dans la zone **exécuter** , tapez Diskmgmt.msc.

    - Démarrer le Gestionnaire de serveur, développez le nœud de **stockage** et cliquez sur **Gestion des disques**.

    - Démarrer les **Outils d’administration**, développez le nœud de **Gestion de l’ordinateur** , puis sélectionnez **Gestion des disques**. 

    >[AZURE.NOTE] Vous devez utiliser des privilèges d’administrateur pour exécuter l’outil Gestion des disques.
 
4. Prendre l’ou les volumes en ligne :

   1. Dans Gestion des disques, cliquez sur n’importe quel volume marquée **hors connexion**.

   2. Cliquez sur **réactiver le disque**. Une fois le disque réactivé, il doit être marqué **en ligne** .

5. Initialiser l’ou les volumes :

   1. Cliquez droit sur les volumes découverts.

   2. Dans le menu, sélectionnez **Initialiser le disque**.

   3. Dans la boîte de dialogue **Initialiser le disque** , sélectionnez les disques que vous souhaitez initialiser, puis cliquez sur **OK**.

6. Formater des volumes simples :

   1. Cliquez droit sur un volume que vous souhaitez mettre en forme.

   2. Dans le menu, sélectionnez **Nouveau Volume Simple**.

   3. Pour formater le volume, utilisez l’Assistant Nouveau Volume Simple :

      - Spécifier la taille du volume.
      - Fournir une lettre de lecteur.
      - Sélectionnez le système de fichiers NTFS.
      - Spécifier une taille d’unité d’allocation 64 Ko.
      - Effectuer un formatage rapide.

7. Formater des volumes de partition multiples. Pour obtenir des instructions, consultez la section, « Partitions et Volumes », dans la [Mise en œuvre de la gestion de disque](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Afficher des informations sur vos volumes

La procédure suivante permet de consulter les informations locales et Azure StorSimple volumes.

#### <a name="to-view-volume-information"></a>Pour afficher les informations de volume

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet de **l’arborescence** , cliquez sur le nœud de **Volumes** . Une liste des volumes locaux et chargés, y compris tous les volumes d’Azure StorSimple, apparaît dans le volet de **résultats** . Les colonnes dans le volet de **résultats** sont configurables. (Cliquez sur le nœud de **Volumes** , sélectionnez **affichage**et puis sélectionnez **Ajouter/supprimer des colonnes**.)

    ![Configurer les colonnes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    Colonne de résultats | Description 
    :--------------|:-------------
    Nom           | La colonne **Name** contient la lettre de lecteur affectée à chaque volume en découverte.
    DISPOSITIF         | La colonne **périphérique** contient l’adresse IP du périphérique connecté à l’ordinateur hôte.
    Nom de Volume de périphérique | La colonne de **Nom de Volume de l’unité** contient le nom du volume de périphérique à laquelle appartient le volume sélectionné. C’est le nom du volume défini dans Azure portal classique pour ce volume spécifique.
    Chemins d’accès   | La colonne de **Chemins d’accès** affiche le chemin d’accès au volume. C’est le point de montage ou de lettre de lecteur à laquelle le volume est accessible sur l’ordinateur hôte.
 
## <a name="delete-a-volume"></a>Supprimer un volume

Utilisez la procédure suivante pour supprimer un volume à partir du Gestionnaire de capture instantanée StorSimple.

>[AZURE.NOTE] Vous ne pouvez pas supprimer un volume s’il fait partie d’un groupe de volumes. (L’option de suppression n’est pas disponible pour les volumes qui sont membres d’un groupe de volumes). Vous devez supprimer le groupe de volume entier pour supprimer le volume.


#### <a name="to-delete-a-volume"></a>Pour supprimer un volume

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , cliquez sur le nœud de **Volumes** . 

3. Dans le volet de **résultats** , cliquez droit sur le volume que vous voulez supprimer.

4. Dans le menu, cliquez sur **Supprimer**. 

    ![Supprimer un volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 

5. La boîte de dialogue **Supprimer le Volume** s’affiche. Tapez le **Confirmer** dans la zone de texte, puis cliquez sur **OK**.

6. Par défaut, Gestionnaire de snapshots StorSimple sauvegarde un volume avant de le supprimer. Cette précaution peut vous protéger contre la perte de données si la suppression a été involontaire. Gestionnaire de snapshots StorSimple affiche un message de progression **Instantané automatique** pendant qu’il sauvegarde le volume. 

    ![Message de l’instantané automatique](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Analyser les volumes

La procédure suivante permet d’analyser les volumes connectés au Gestionnaire de capture instantanée StorSimple.

#### <a name="to-rescan-the-volumes"></a>Pour analyser les volumes

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , cliquez droit sur des **Volumes**, puis cliquez sur **analyser les volumes**.

    ![Analyser les volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    Cette procédure synchronise la liste des volumes avec le Gestionnaire de snapshots StorSimple. Des modifications, telles que de nouveaux volumes ou les volumes supprimés, apparaîtront dans les résultats.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurer et sauvegarder un volume de base

Utilisez la procédure suivante pour configurer une sauvegarde d’un volume de base, puis démarrer une sauvegarde immédiatement ou créer une stratégie pour les sauvegardes planifiées.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer :

- Assurez-vous que l’ordinateur hôte et le périphérique de StorSimple sont configurés correctement. Pour plus d’informations, accédez à [déployer votre périphérique de StorSimple local](storsimple-deployment-walkthrough-u2.md).

- Installez et configurez le Gestionnaire de snapshots de StorSimple. Pour plus d’informations, accédez à [Gestionnaire de snapshots StorSimple de déployer](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Pour configurer la sauvegarde d’un volume de base

1. Créer un volume de base sur le périphérique StorSimple.

2. Monter, initialiser et formater le volume comme décrit dans le [montage de volumes](#mount-volumes). 

3. Cliquez sur l’icône Gestionnaire de snapshots StorSimple sur votre bureau. La fenêtre Gestionnaire de snapshots de StorSimple s’affiche. 

4. Dans le volet de **portée** , cliquez sur le nœud de **Volumes** et sélectionnez **analyser les volumes**. Lorsque l’analyse est terminée, une liste des volumes doit apparaître dans le volet de **résultats** . 

5. Dans le volet de **résultats** , cliquez sur le volume et puis sélectionnez **Créer un groupe de volumes**. 

    ![Créer le groupe de volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 

6. Dans la boîte de dialogue **Créer un groupe de volumes** , tapez un nom pour le groupe de volumes, lui affecter des volumes, puis cliquez sur **OK**.

7. Dans le volet de **l’arborescence** , développez le nœud **Groupes de volumes** . Le nouveau groupe de volumes doit apparaître sous le nœud **Groupes de volumes** . 

8. Cliquez droit sur le nom de groupe de volumes.

    - Pour démarrer une opération de sauvegarde (sur demande) interactive, cliquez sur **Effectuer une sauvegarde**. 

    - Pour planifier une sauvegarde automatique, cliquez sur **Créer une stratégie de sauvegarde**. Dans la page **Général** , sélectionnez un groupe de volumes à partir de la liste. Dans la page **planification** , entrez les détails de la planification. Lorsque vous avez terminé, cliquez sur **OK**. 

9. Pour confirmer que l’opération de sauvegarde a démarré, développez le nœud de **tâches** dans le volet de **l’arborescence** , puis cliquez sur le nœud **en cours d’exécution** . La liste des tâches en cours d’exécution s’affiche dans le volet de **résultats** . 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configurer et sauvegarder un volume en miroir dynamique

Effectuez les étapes suivantes pour configurer la sauvegarde d’un volume en miroir dynamique :

- Étape 1 : Utiliser la gestion des disques pour créer un volume en miroir dynamique. 

- Étape 2 : Utilisez le Gestionnaire d’instantané StorSimple pour configurer la sauvegarde.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer :

- Assurez-vous que l’ordinateur hôte et le périphérique de StorSimple sont configurés correctement. Pour plus d’informations, accédez à [déployer votre périphérique de StorSimple local](storsimple-deployment-walkthrough-u2.md).

- Installez et configurez le Gestionnaire de snapshots de StorSimple. Pour plus d’informations, accédez à [Gestionnaire de snapshots StorSimple de déployer](storsimple-snapshot-manager-deployment.md).

- Configurer deux volumes sur le périphérique StorSimple. (Dans les exemples, les volumes disponibles sont **disque 1** et **disque 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Étape 1 : Utiliser la gestion des disques pour créer un volume en miroir dynamique

Gestion des disques est un utilitaire système de gestion des disques durs et les volumes ou les partitions qu’ils contiennent. Pour plus d’informations sur la gestion des disques, accédez à [Gestion des disques](https://technet.microsoft.com/library/cc770943.aspx) sur le site Web TechNet de Microsoft.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Pour créer un volume en miroir dynamique

1. Pour démarrer le composant Gestion des disques, utilisez une des options suivantes : 

   - Ouvrez la zone **exécuter** , tapez **Diskmgmt.msc**et appuyez sur ENTRÉE.

   - Démarrer le Gestionnaire de serveur, développez le nœud de **stockage** et cliquez sur **Gestion des disques**. 

   - Démarrer les **Outils d’administration**, développez le nœud de **Gestion de l’ordinateur** , puis sélectionnez **Gestion des disques**. 

2. Assurez-vous que vous disposez de deux volumes disponibles sur le périphérique StorSimple. (Dans l’exemple, les volumes disponibles sont **disque 1** et **disque 2**.) 

3. Dans la fenêtre Gestion des disques, dans la colonne de droite du volet inférieur, cliquez droit sur le **disque 1** , puis sélectionnez **Nouveau Volume en miroir**. 

    ![Nouveau Volume en miroir](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 

4. Sur la page de l’Assistant **Nouveau Volume en miroir** , cliquez sur **suivant**.

5. Dans la page **Sélectionner les disques** , sélectionnez le **disque 2** dans le volet **sélectionné** , cliquez sur **Ajouter**, puis cliquez sur **suivant**. 

6. Dans la page **attribuer une lettre de lecteur ou de chemin d’accès** , acceptez les valeurs par défaut, puis cliquez sur **suivant**. 

7. Dans la page **Formatage de Volume** , dans la zone de la **Taille d’unité d’Allocation** , sélectionnez **64 Ko**. Activez la case à cocher **effectuer un formatage rapide** , puis cliquez sur **suivant**. 

8. Sur la page **fin le nouveau Volume en miroir** , passez en revue vos paramètres, puis cliquez sur **Terminer**. 

9. Un message s’affiche pour indiquer que le disque de base va être converti en disque dynamique. Cliquez sur **Oui**.

    ![Message de conversion de disque dynamique](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 

10. Dans Gestion des disques, vérifiez que le disque 1 et disque 2 sont affichés comme des volumes en miroir dynamiques. (**Dynamique** doit apparaître dans la colonne État, et la couleur de barre de capacité doit modifier en rouge, indiquant un volume en miroir) 

    ![Disques dynamiques de gestion mis en miroir de disque](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 
 
### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Étape 2 : Utiliser le Gestionnaire de Snapshot StorSimple de configuration de sauvegarde

Utilisez la procédure suivante pour configurer un volume en miroir dynamique, puis de démarrer immédiatement la sauvegarde ou créer une stratégie pour les sauvegardes planifiées.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Pour configurer la sauvegarde d’un volume en miroir dynamique

1. Cliquez sur l’icône Gestionnaire de snapshots StorSimple sur votre bureau. La fenêtre Gestionnaire de snapshots de StorSimple s’affiche. 

2. Dans le volet de **portée** , cliquez sur le nœud de **Volumes** et sélectionnez **analyser les volumes**. Lorsque l’analyse est terminée, une liste des volumes doit apparaître dans le volet de **résultats** . Le volume en miroir dynamique est répertorié comme un volume unique. 

3. Dans le volet de **résultats** , avec le bouton droit de la dynamique des volumes en miroir, puis cliquez sur **Créer un groupe de volumes**. 

4. Dans la boîte de dialogue **Créer un groupe de volumes** , tapez un nom pour le groupe de volumes, affecter le volume en miroir dynamique à ce groupe, puis cliquez sur **OK**. 

5. Dans le volet de **l’arborescence** , développez le nœud **Groupes de volumes** . Le nouveau groupe de volumes doit apparaître sous le nœud **Groupes de volumes** . 

6. Cliquez droit sur le nom de groupe de volumes. 

    - Pour démarrer une opération de sauvegarde (sur demande) interactive, cliquez sur **Effectuer une sauvegarde**. 

    - Pour planifier une sauvegarde automatique, cliquez sur **Créer une stratégie de sauvegarde**. Dans la page **Général** , sélectionnez le groupe de volumes à partir de la liste. Dans la page **planification** , entrez les détails de la planification. Lorsque vous avez terminé, cliquez sur **OK**. 

7. Vous pouvez surveiller le travail de sauvegarde en cours d’exécution. Dans le volet de **l’arborescence** , développez le nœud de **tâches** , puis cliquez sur **en cours d’exécution**, les détails de la tâche s’affichent dans le volet de **résultats** . Lorsque la sauvegarde est terminée, les informations sont transférées vers la liste des **dernières 24** heures. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le Gestionnaire de snapshots StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- Découvrez comment [utiliser le Gestionnaire de snapshots StorSimple pour créer et gérer des groupes de volumes](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
