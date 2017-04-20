<properties 
   pageTitle="Utilisez la vue de l’exécution de Vertex dans les outils de données LAC pour Visual Studio | Microsoft Azure" 
   description="Apprenez à utiliser la vue de l’exécution de Vertex pour les travaux d’examen Analytique de LAC de données." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/13/2016"
   ms.author="jgao"/>

# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Utilisez la vue de l’exécution de Vertex dans les outils de données LAC pour Visual Studio

Apprenez à utiliser la vue de l’exécution de Vertex pour les travaux d’examen Analytique de LAC de données.

## <a name="prerequisites"></a>Conditions préalables

- Certaines connaissances de base sur l’utilisation des outils de LAC de données pour Visual Studio pour développer des scripts de SQL-U.  Consultez [didacticiel : développement de scripts U-SQL à l’aide des outils de données LAC pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

## <a name="open-the-vertex-execution-view"></a>Ouvrez la vue de l’exécution de sommet

Pour effectuer une tâche précise, vous pouvez cliquer sur le lien « Vue de l’exécution de sommet » dans le coin inférieur gauche. Vous pouvez être invité à charger les profils, et elle peut prendre un certain temps en fonction de la connectivité réseau.

![Vue de l’exécution de sommet des outils de données lac Analytique](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Présentation du mode d’exécution de sommet

Après avoir entré la vue de l’exécution de sommet, il existe trois parties :

![Vue de l’exécution de sommet des outils de données lac Analytique](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

- Sélecteur de sommet : sur la gauche est le sélecteur de sommet.  Vous pouvez sélectionner des sommets par les fonctionnalités (telles que les 10 premières données lire ou choisir par étape).

    Les filtres utilisés plus courants est les sommets sur le chemin critique. Chemin critique est le chemin d’accès la plus longue d’une tâche SQL-U. Il est utile pour l’optimisation de vos travaux en vérifiant le vertex prend le plus de temps.

- Le volet centre haut :

    ![Vue de l’exécution de sommet des outils de données lac Analytique](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

    Cette vue affiche également l’état en cours d’exécution de tous les sommets. Elle convertit l’heure en conséquence à votre ordinateur local et affiche un statut différent dans différentes couleurs.

- Le volet central du bas :

    ![Vue de l’exécution de sommet des outils de données lac Analytique](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

    - Nom du processus : Nom de l’instance de sommet. Il est composé de différentes parties de StageName | VertexName | VertexRunInstance. Par exemple, le sommet .v1 [62] SV7_Split est l’abréviation de la seconde instance en cours d’exécution (.v1, index commençant à 0) du nombre de Vertex 62 dans le stade SV7_Split.
    - Total données en lecture/écriture : Les données ont été lus/écrits par ce sommet.
    - Statut de l’état et de sortie : L’état final lorsque le sommet est terminé.
    - Type de Code ou de l’échec de sortie : Erreur lors de l’échec le sommet.
    - Raison de la création : Pourquoi le sommet a été créé.
    - Temps de latence de file d’attente de ressources latence/processus latence/PN : la durée du sommet d’attente de ressources, de traitement des données et de rester dans la file d’attente.
    - GUID du processus/créateur : Le GUID pour le sommet en cours d’exécution en cours ou de son créateur.
    - Version : la N ième instance du sommet en cours d’exécution (le système peut planifier des nouvelles instances d’un sommet de calculent de nombreuses raisons, par exemple le basculement, redondance, etc..)
    - Version créée.
    - Traiter les créer début/processus d’exécution en attente/processus d’exécution début/processus d’exécution complète temps : lorsque le processus de vertex démarre la création ; Lorsque le processus de vertex démarre en file d’attente ; Lorsque le processus de vertex donné démarre ; Lorsque certains le sommet est terminée.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de données lac Analytique, consultez [vue d’ensemble de l’Analytique de LAC de données Azure](data-lake-analytics-overview.md).
- Pour commencer à développer des applications de U-SQL, consultez [scripts de développer U-SQL à l’aide des outils de LAC de données pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour en savoir plus U-SQL, consultez [mise en route du lac de données Azure Analytique U-SQL language](data-lake-analytics-u-sql-get-started.md).
- Pour les tâches de gestion, voir [gestion de LAC Azure données Analytique à l’aide d’Azure portal](data-lake-analytics-manage-use-portal.md).
- Pour enregistrer des informations de diagnostic, consultez [accès à des journaux de diagnostics pour Azure données lac Analytique](data-lake-analytics-diagnostic-logs.md)
- Pour voir une requête plus complexe, consultez [les journaux de site Web d’analyse à l’aide d’Azure données lac Analytique](data-lake-analytics-analyze-weblogs.md).
- Pour afficher les détails d’une tâche, reportez-vous à la section [utilisation travail navigateur et affichage de tâche pour les tâches d’Azure données lac Analytique](data-lake-analytics-data-lake-tools-view-jobs.md)