<properties 
    pageTitle="Surveiller et de gérer les pipelines Azure Data Factory" 
    description="Découvrez comment utiliser de suivi et d’application de gestion pour surveiller et gérer les pipelines et les fabriques de données Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="spelluru"/>

# <a name="monitor-and-manage-azure-data-factory-pipelines-using-new-monitoring-and-management-app"></a>Surveiller et gérer les pipelines Azure Data Factory à l’aide de la nouvelle analyse et application de gestion
> [AZURE.SELECTOR]
- [À l’aide d’Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
- [À l’aide de surveillance et de gestion App](data-factory-monitor-manage-app.md)

Cet article décrit comment faire pour contrôler, gérer et déboguer vos pipelines et créer des alertes pour être averti de défaillances à l’aide de l' **analyse et gestion App**. Vous pouvez également visionner la vidéo suivante pour en savoir plus sur l’utilisation de l’analyse et l’application de gestion.
   

> [AZURE.VIDEO azure-data-factory-monitoring-and-managing-big-data-piplines]
      
## <a name="launching-the-monitoring-and-management-app-a"></a>Lancement de la surveillance des applications de gestion et un
Pour lancer le moniteur et l’application de gestion, cliquez sur mosaïque de **surveillance et gestion** sur la lame **FACTORY de données** pour votre fabrique de données.

![Surveillance de mosaïque sur le Factory de données page d’accueil](./media/data-factory-monitor-manage-app/MonitoringAppTile.png) 

Vous devez voir que le suivi et l’application de gestion lancement dans une onglet/une fenêtre séparée.  

![Application de gestion et de surveillance](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [AZURE.NOTE] Si vous voyez que le navigateur web est bloqué au niveau de la « Autorisation... », désactiver/désactiver le paramètre de **données de site et de bloquer les cookies tiers** (ou) conserver activé et créer une exception pour **login.microsoftonline.com** et puis réessayez de démarrer l’application à nouveau.


Si vous ne voyez pas windows d’activité dans la liste du bas, cliquez sur le bouton **Actualiser** dans la barre d’outils pour actualiser la liste. En outre, définir les valeurs pour les filtres de **l’heure de début** et de **fin** .  


## <a name="understanding-the-monitoring-and-management-app"></a>Présentation de la surveillance et de gestion App
Il existe trois onglets (**Explorateur de ressources**, **Vues d’analyse**et les **alertes**) sur la gauche et le premier onglet (Explorateur de ressources) est sélectionné par défaut. 

### <a name="resource-explorer"></a>Explorateur de ressources
Vous consultez les rubriques suivantes : 

- Ressources d’Explorateur **arborescence** dans le volet gauche.
- **Affichage de tâches** dans la partie supérieure.
- Liste **Activité Windows** en bas dans le volet central.
- **Propriétés**/**Activité fenêtre Explorer** les onglets dans le volet droit. 

Dans l’Explorateur de ressources, vous voyez toutes les ressources (pipelines, des groupes de données, les services liés) à l’usine de données dans une arborescence. Lorsque vous sélectionnez un objet dans l’Explorateur de ressources, vous remarquez les éléments suivants : 

- entité de données fabrique associé est mis en surbrillance dans la vue schéma.
- associé à windows d’activité (cliquez [ici](data-factory-scheduling-and-execution.md) pour en savoir plus sur windows d’activité) sont mis en surbrillance dans la liste activité Windows en bas.  
- propriétés de l’objet sélectionné dans la fenêtre Propriétés dans le volet droit. 
- Définition de JSON de l’objet sélectionné, le cas échéant. Par exemple : un service lié ou d’un groupe de données ou d’un pipeline. 

![Explorateur de ressources](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Voir [planification et l’exécution de](data-factory-scheduling-and-execution.md) l’article pour plus d’informations sur la fenêtre de l’activité. 

### <a name="diagram-view"></a>Affichage des diagrammes
L’affichage du schéma d’une fabrique de données fournit un seul volet de verre pour surveiller et gérer le factory de données et de ses actifs. Lorsque vous sélectionnez une entité de données usine (groupe de données/pipeline) dans l’affichage de tâches, vous remarquez les éléments suivants :
 
- l’entité de fabrique de données est sélectionnée dans l’arborescence
- activité associée windows sont mis en surbrillance dans la liste activité Windows.
- propriétés de l’objet sélectionné dans la fenêtre Propriétés

Lorsque le pipeline est activé (et non dans l’état suspendu), qu’il est indiqué par une ligne verte. 

![Pipeline en cours d’exécution](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Vous remarquez qu’il y a trois boutons de commande pour le pipeline de la vue schéma. Vous pouvez utiliser le second bouton pour mettre en pause le pipeline. La suspension ne pas terminer les activités en cours d’exécution et les laisser se poursuivre jusqu'à la fin. Troisième bouton interrompt le pipeline et termine son exécution d’activités existant. Premier bouton reprend le pipeline. Lorsque votre pipeline est suspendu, vous notez la modification de la couleur pour le pipeline de mosaïque comme suit.

![Suspendre/reprendre sur mosaïque](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

Vous pouvez sélectionner plusieurs pipelines de deux ou plus (à l’aide de CTRL) et utilisez les boutons de barre de commande pour suspendre/reprendre plusieurs pipelines à la fois.

![Pause/reprise de barre de commandes](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

Vous pouvez voir toutes les activités dans le pipeline, avec le bouton droit de la mosaïque du pipeline, cliquez sur **ouverte**.

![Ouvrir le menu de Pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Dans la vue du tuyau ouvert, vous voyez toutes les activités dans le pipeline. Dans cet exemple, il y n'a qu’une seule activité : activité de copie. Pour revenir à l’affichage précédent, cliquez sur le nom d’usine de données dans le menu de navigation du haut.

![Opportunités ouvertes](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Dans le pipeline, lorsque vous cliquez sur un groupe de données de sortie ou lorsque vous déplacez votre souris sur le groupe de données de sortie, vous consultez la fenêtre pop-up Windows de l’activité pour ce groupe de données.

![Menu contextuel de Windows activité](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Vous pouvez cliquer sur une fenêtre de l’activité pour afficher les détails pour lui dans la fenêtre **Propriétés** dans le volet droit. 

![Propriétés de la fenêtre activité](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

Dans le volet droit, basculez vers l’onglet **Activité fenêtre Explorateur** pour afficher plus de détails.

![Explorateur d’activité](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png) 

Vous consultez également **résolu variables** pour chaque exécution de l’activité essayer dans la section de **tentatives** . 

![Variables résolues](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Basculez vers l’onglet **Script** pour voir la définition de script JSON pour l’objet sélectionné.   

![Onglet script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Vous pouvez voir windows activité à trois endroits différents :

- Fenêtre pop-up Windows d’activité dans l’affichage de diagramme (volet central).
- Explorateur de fenêtre activité dans le volet droit.
- Liste des activités Windows dans le volet inférieur.

Dans le Windows d’activité pop-up et l’activité fenêtre Explorateur, vous pouvez faire défiler pour la semaine précédente et la semaine suivante à l’aide des flèches gauche et droite.

![Flèches gauche et droite d’activité fenêtre Explorateur](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

En bas de l’affichage de tâches, vous consultez boutons pour effectuer un zoom avant dans, Zoom arrière, Zoom pour ajuster, effectuer un Zoom de 100 %, verrouiller la mise en page. Le bouton de mise en forme de verrou vous empêche de déplacer accidentellement des tables et des pipelines dans la vue schéma et est activée par défaut. Vous pouvez le désactiver et déplacer des entités dans le schéma. Lorsque vous le désactivez, vous pouvez utiliser le bouton dernier pour positionner automatiquement des tables et des pipelines. Vous pouvez également un zoom avant / Zoom arrière à l’aide de la roulette de la souris.

![Commandes de Zoom de la vue de diagramme](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)


### <a name="activity-windows-list"></a>Liste des activités Windows
La liste des activités windows dans la partie inférieure du volet du milieu affiche toutes les fenêtres d’activité pour le groupe de données que vous avez sélectionné dans l’Explorateur de ressources ou diagramme. Par défaut, la liste est dans l’ordre décroissant, ce qui signifie que vous voyez la dernière fenêtre d’activité en haut. 

![Liste des activités Windows](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Cette liste ne pas actualiser automatiquement, par conséquent, utilisez le bouton Actualiser dans la barre d’outils pour actualiser manuellement.  


Les fenêtres de l’activité peuvent être dans un des statuts suivants :

<table>
<tr>
    <th align="left">État</th><th align="left">Sous-état</th><th align="left">Description</th>
</tr>
<tr>
    <td rowspan="8">En attente</td><td>ScheduleTime</td><td>L’heure n’a pas encore de la fenêtre d’activité à exécuter.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Les dépendances en amont ne sont pas prêts.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Les ressources de calcul ne sont pas disponibles.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Toutes les instances d’activité sont occupés par l’exécution d’autres fenêtres de l’activité.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Activité est suspendue et ne peut pas exécuter les fenêtres activité jusqu'à sa reprise.</td>
</tr>
<tr>
<td>Nouvelle tentative</td><td>L’exécution des activités est relancée.</td>
</tr>
<tr>
<td>Validation</td><td>La validation n’a pas encore commencé.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>En attente de validation afin d’être retraité.</td>
</tr>
<tr>
<tr
<td rowspan="2">InProgress</td><td>Validation</td><td>Validation en cours.</td>
</tr>
<td></td>
<td>La fenêtre activité est en cours de traitement.</td>
</tr>
<tr>
<td rowspan="4">A échoué</td><td>TimedOut</td><td>L’exécution a duré plus longtemps que cela est autorisé par l’activité.</td>
</tr>
<tr>
<td>Annulé</td><td>Annulé par l’utilisateur.</td>
</tr>
<tr>
<td>Validation</td><td>La validation a échoué.</td>
</tr>
<tr>
<td></td><td>Impossible de générer et/ou valider la fenêtre activité.</td>
</tr>
<td>Prêt</td><td></td><td>La fenêtre activité est prête à la consommation.</td>
</tr>
<tr>
<td>Ignoré</td><td></td><td>La fenêtre d’activité n’est pas traitée.</td>
</tr>
<tr>
<td>Aucun</td><td></td><td>Une fenêtre d’activité utilisée pour existe avec un autre statut, mais a été réinitialisée.</td>
</tr>
</table>


Lorsque vous cliquez sur une fenêtre de l’activité dans la liste, vous voyez plus d’informations à ce sujet dans la fenêtre **Propriétés** ou de **L’Explorateur Windows activité** sur la droite.

![Explorateur d’activité](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Actualisation de windows de l’activité  
Les détails ne sont pas automatiquement actualisés, afin que vous utiliser **l’actualisation** du bouton (deuxième) dans la barre de commande pour actualiser manuellement la liste activité.  
 

### <a name="properties-window"></a>Fenêtre Propriétés
La fenêtre Propriétés est dans le volet le plus à droite de l’application de la surveillance et la gestion. 

![Fenêtre Propriétés](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Il affiche les propriétés de l’élément sélectionné dans l’Explorateur de ressources (arborescence) (ou) diagramme affichage (ou) liste des activités windows. 

### <a name="activity-window-explorer"></a>Explorateur d’activité

La fenêtre **Explorateur de fenêtre activité** se trouve dans le volet le plus à droite de suivi et d’application de gestion. Il affiche des détails sur la fenêtre d’activité que vous avez sélectionné dans la liste de Windows de l’activité ou le pop-up à activité Windows. 

![Explorateur d’activité](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Vous pouvez basculer vers une autre fenêtre d’activité en cliquant dessus dans la vue du calendrier en haut. Vous pouvez également utiliser la **flèche vers la gauche**/des boutons de**flèche vers la droite** en haut pour voir windows d’activité du précédent/semaine prochaine.

Vous pouvez utiliser les boutons de barre d’outils dans le volet inférieur pour **exécuter de nouveau** la fenêtre d’activité ou **d’Actualiser** les détails dans le volet. 

### <a name="script"></a>Script 
Vous pouvez utiliser l’onglet **Script** pour afficher la définition de JSON de l’entité de données usine sélectionnée (services liés, dataset et pipeline). 

![Onglet script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="using-system-views"></a>À l’aide de vues système
Le suivi et l’application de gestion comprend des vues système prédéfinies (**windows d’activité récente**, **windows d’activité a échoué**, **Windows de l’activité en cours**) qui vous permet d’afficher les windows les activités récentes/échec/en cours de votre usine de données. 

Basculer vers l’onglet de **Contrôle des vues** sur la gauche en cliquant dessus. 

![Onglet vues de surveillance](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Il existe actuellement trois vues système pris en charge. Sélectionnez une option pour voir récente activité windows (ou) windows d’activité a échoué (ou) windows les activités en cours dans la liste activité Windows (en bas du volet central). 

Lorsque vous sélectionnez option **récente activité windows** , vous voyez toutes les fenêtres activité récente dans l’ordre décroissant de la **dernière tentative de temps**. 

Vous pouvez utiliser la vue **fenêtres d’activité a échoué** pour voir toutes les fenêtres d’activité a échoué dans la liste. Sélectionnez une fenêtre activité a échoué dans la liste pour voir les détails à ce sujet dans les **Propriétés de** fenêtre (ou) **Activité fenêtre Explorateur**. Vous pouvez également télécharger tous les journaux pour une fenêtre d’activité a échoué. 


## <a name="sorting-and-filtering-activity-windows"></a>Tri et filtrage de windows de l’activité
Modifier les paramètres **d’heure de début** et de **fin** dans la barre de commandes filtre activité Windows. Après avoir modifié l’heure de début et de fin, cliquez sur le bouton en regard de l’heure de fin pour actualiser la liste activité Windows.

![Heures de début et fin](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [AZURE.NOTE] Actuellement, toutes les heures sont au format UTC dans le suivi et l’application de gestion. 

Dans la **liste activité Windows**, cliquez sur le nom d’une colonne (par exemple : état). 

![Menu de colonne Windows liste activités](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Vous pouvez effectuer les opérations suivantes :

- Tri dans l’ordre croissant.
- Tri dans l’ordre décroissant.
- Filtrer par une ou plusieurs valeurs (prêt, en attente, etc.)

Lorsque vous spécifiez un filtre sur une colonne, vous voyez le bouton Filtre activé pour cette colonne indiquer que les valeurs de la colonne sont les valeurs filtrées. 

![Filtrer dans une colonne de la liste d’activités Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Vous pouvez utiliser la même fenêtre pop-up pour effacer les filtres. Pour effacer tous les filtres de la liste activité, cliquez sur le bouton Effacer le filtre sur la barre de commandes. 

![Effacer tous les filtres dans une liste d’activités Windows](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)


## <a name="performing-batch-actions"></a>Exécution d’actions de traitement par lots

### <a name="rerun-selected-activity-windows"></a>Relancez windows de l’activité sélectionnée
Sélectionnez une fenêtre de l’activité, cliquez sur la flèche vers le bas pour le premier bouton de barre de commande puis **relancer** / **relancer avec en amont dans le pipeline**. Lorsque vous sélectionnez option de **réexécuter avec en amont dans le pipeline** , elle exécute à nouveau toutes les activités en amont sous windows ainsi. 
    ![Exécutez à nouveau une fenêtre de l’activité](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Vous pouvez également sélectionner plusieurs fenêtres d’activité dans la liste et les réexécuter en même temps. Vous pouvez souhaiter filtrer windows d’activité en fonction de l’état (par exemple : **Échec**), puis réexécutez les fenêtres activité a échoué après avoir corrigé le problème qui entraîne l’échec de windows activité. Consultez la section suivante pour plus d’informations sur le filtrage windows d’activité dans la liste.  

### <a name="pauseresume-multiple-pipelines"></a>Suspendre/reprendre plusieurs pipelines
Vous pouvez sélectionner plusieurs pipelines de deux ou plus (à l’aide de CTRL) et utilisez les boutons de barre de commandes (mis en surbrillance dans un rectangle rouge dans l’image suivante) pour suspendre/reprendre les à la fois.

![Suspension/reprise de barre de commandes](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## <a name="creating-alerts"></a>Création d’alertes 
La page des alertes vous permet de créer une alerte, afficher/modifier/supprimer des alertes existantes. Vous pouvez également désactiver/activer une alerte. Pour afficher la page alertes, cliquez sur l’onglet Alertes.

![Onglet Alertes](./media/data-factory-monitor-manage-app/AlertsTab.png)

### <a name="to-create-an-alert"></a>Pour créer une alerte

1. Cliquez sur **Ajouter une alerte** pour ajouter une alerte. Vous consultez la page Détails. 

    ![Créer des alertes - page de détails](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
1. Spécifiez le **nom** et la **description** de l’alerte, puis cliquez sur **suivant**. Vous devez voir la page **filtres** .

    ![Créer des alertes - page de filtres](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)

2. Sélectionnez **l’événement**, un **état**et un **sous-état** (facultatif) sur lequel vous souhaitez que le service Data Factory pour vous avertir, puis cliquez sur **suivant**. Vous devez voir la page de **destinataires** .

    ![Créer des alertes - page de destinataires](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png) 
3. L’option **E-mail abonnement administrateurs** et/ou entrez la **messagerie de l’administrateur supplémentaires**et cliquez sur **Terminer**. Vous devriez voir l’alerte dans la liste. 
    
    ![Liste d’alertes](./media/data-factory-monitor-manage-app/AlertsList.png)

Dans la liste des alertes, utilisez les boutons associés à l’alerte pour modifier/supprimer/activer/désactiver une alerte. 

### <a name="eventstatussubstatus"></a>Statut/événement/sous-état
Le tableau suivant fournit la liste des événements disponibles et statuts (sous-états).

Nom de l’événement | État | Statut de secondaire
-------------- | ------ | ----------
Activité a commencé | Démarrage | Démarrage
Activité exécutée terminée | A réussi | A réussi 
Activité exécutée terminée | A échoué| Allocation de ressources ayant échoué<br/><br/>Échec de l’exécution<br/><br/>Dépassement de délai<br/><br/>Échec de la Validation<br/><br/>Abandonnée
À la demande HDI Cluster créer démarré | Démarrage | &nbsp; |
Cluster à la demande HDI créé avec succès | A réussi | &nbsp; |
Cluster à la demande HDI supprimé | A réussi | &nbsp; |
### <a name="to-editdeletedisable-an-alert"></a>Pour modifier/supprimer/désactiver une alerte


![Boutons d’alertes](./media/data-factory-monitor-manage-app/AlertButtons.png)



    
 


