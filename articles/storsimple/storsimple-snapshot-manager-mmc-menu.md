<properties 
   pageTitle="Actions du menu console MMC Gestionnaire de snapshots StorSimple | Microsoft Azure"
   description="Décrit comment utiliser les actions de menu standard Microsoft Management Console (MMC) dans le Gestionnaire de snapshots StorSimple."
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
   ms.date="04/25/2016"
   ms.author="v-sharos" />

# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Utilisez les actions du menu MMC dans le Gestionnaire de snapshots StorSimple

## <a name="overview"></a>Vue d’ensemble

Dans le Gestionnaire de snapshots de StorSimple, vous verrez les actions suivantes sur tous les menus action et toutes les variantes du volet **Actions** . 

- Affichage
- Nouvelle fenêtre à partir d’ici 
- Actualiser 
- Exporter la liste 
- Aide 

Ces actions font partie de Microsoft Management Console (MMC) et ne sont pas spécifiques au Gestionnaire de capture instantanée StorSimple. Ce didacticiel décrit ces actions et explique comment utiliser chacun d’eux dans le Gestionnaire de snapshots StorSimple.

## <a name="view"></a>Affichage

Vous pouvez utiliser l’option **d’affichage** pour modifier l’affichage du volet **résultats** et pour modifier l’affichage de la fenêtre console. 

#### <a name="to-change-the-results-pane-view"></a>Pour modifier l’affichage du volet résultats

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **portée** , avec le bouton droit à n’importe quel nœud ou développez le nœud et clic droit sur un élément dans le volet de **résultats** , puis cliquez sur l’option **d’affichage** . 

3. Pour ajouter ou supprimer des colonnes qui s’affichent dans le volet de **résultats** , cliquez sur **Ajouter/supprimer des colonnes**. La boîte de dialogue **Ajouter/supprimer des colonnes** s’affiche.

    ![Ajouter ou supprimer des colonnes dans le volet résultats](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 

4. Complétez le formulaire comme suit :

    - Sélectionner des éléments dans la liste colonnes **disponibles** et cliquez sur **Ajouter** pour les ajouter à la liste **colonnes affichées** . 

    - Cliquez sur les éléments de la liste **colonnes affichées** , puis cliquez sur **Supprimer** pour les supprimer de la liste. 

    - Sélectionnez un élément dans la liste des colonnes **affichées** , puis cliquez sur **Déplacer vers le haut** ou **Vers le bas** pour déplacer l’élément vers le haut ou vers le bas dans la liste. 

    - Cliquez sur **Restaurer les valeurs par défaut** pour revenir à la configuration par défaut du volet de **résultats** . 

5. Lorsque vous avez terminé vos sélections, cliquez sur **OK**. 

#### <a name="to-change-the-console-window-view"></a>Pour modifier l’affichage de la fenêtre console

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **portée** , avec le bouton droit à n’importe quel nœud, cliquez sur **affichage**, puis cliquez sur **Personnaliser**. La boîte de dialogue **Personnaliser** s’affiche.

    ![Personnaliser la fenêtre de console](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 

3. Activez ou désactivez les cases à cocher pour afficher ou masquer des éléments dans la fenêtre de console. Lorsque vous avez terminé vos sélections, cliquez sur **OK**.

## <a name="new-window-from-here"></a>Nouvelle fenêtre à partir d’ici

Vous pouvez utiliser l’option **Nouvelle fenêtre à partir d’ici** ouvrir une nouvelle fenêtre de console.

#### <a name="to-open-a-new-console-window"></a>Pour ouvrir une nouvelle fenêtre de console

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **portée** , avec le bouton droit à n’importe quel nœud, puis cliquez sur **Nouvelle fenêtre à partir d’ici**. 

    Une nouvelle fenêtre s’affiche, n'indiquant que la portée que vous avez sélectionné. Par exemple, si vous cliquez sur le nœud **Stratégies de sauvegarde** , la nouvelle fenêtre affichera uniquement le nœud **Stratégies de sauvegarde** dans le volet de **portée** et une liste de stratégies de sauvegarde définies dans le volet de **résultats** . Voir l’exemple suivant.

    ![Nouvelle fenêtre à partir d’ici](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 
 
## <a name="refresh"></a>Actualiser

Vous pouvez utiliser l’action **Actualiser** pour mettre à jour de la fenêtre de console.

#### <a name="to-update-the-console-window"></a>Mise à jour de la fenêtre de console

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **portée** , avec le bouton droit à n’importe quel nœud ou développez le nœud et clic droit sur un élément dans le volet de **résultats** , puis cliquez sur **Actualiser**. 

## <a name="export-list"></a>Exporter la liste

Vous pouvez utiliser l’action **Exporter la liste** pour enregistrer une liste dans un fichier de valeurs séparées par des virgules (CSV). Par exemple, vous pouvez exporter la liste des règles de sauvegarde ou le catalogue de sauvegarde. Vous pouvez ensuite importer le fichier CSV dans une application de feuille de calcul pour l’analyse.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Pour enregistrer une liste dans un fichier de valeurs séparées par des virgules (CSV)

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet de **portée** , avec le bouton droit à n’importe quel nœud ou développez le nœud et clic droit sur un élément dans le volet de **résultats** , puis cliquez sur **Exporter la liste**. 

3. La boîte de dialogue **Exporter la liste** s’affiche. Complétez le formulaire comme suit : 

    1. Dans la zone **nom de fichier** , tapez un nom pour le fichier CSV, ou cliquez sur la flèche pour sélectionner dans la liste déroulante.

    2. Dans la zone **type** de fichier, cliquez sur la flèche et sélectionnez un type de fichier dans la liste déroulante.

    3. Pour enregistrer uniquement les éléments sélectionnés, sélectionnez les lignes et cliquez sur la case à cocher **Enregistrer uniquement les lignes sélectionnées** . Pour enregistrer des listes exportées tout, désactivez la case à cocher **Enregistrer seulement les lignes sélectionnées** .

    4. Cliquez sur **Enregistrer**.

    ![Exporter la liste dans un fichier de valeurs séparées par des virgules](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 
 
## <a name="help"></a>Aide

Vous pouvez utiliser le menu **aide** pour afficher l’aide en ligne disponible pour StorSimple Gestionnaire de snapshots et de la console MMC.

#### <a name="to-view-available-online-help"></a>Pour afficher l’aide en ligne disponible

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **portée** , avec le bouton droit à n’importe quel nœud ou développez le nœud et clic droit sur un élément dans le volet de **résultats** , puis cliquez sur **aide**. 

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l' [interface utilisateur du Gestionnaire de capture instantanée StorSimple](storsimple-use-snapshot-manager.md).
- Pour en savoir plus sur [l’utilisation du Gestionnaire de snapshots StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
