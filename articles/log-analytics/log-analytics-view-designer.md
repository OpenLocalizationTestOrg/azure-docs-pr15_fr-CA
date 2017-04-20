<properties
    pageTitle="Journal du Concepteur de vue Analytique | Microsoft Azure"
    description="Concepteur de vues dans le journal Analytique vous permet de créer des affichages personnalisés dans la console de l’OMS qui contiennent différentes visualisations de données dans le référentiel de l’OMS. Cet article contient une vue d’ensemble du Concepteur de vues et les procédures de création et de modification des affichages personnalisés."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer"></a>Concepteur de vue Analytique de journal
Le Concepteur de vue dans le journal Analytique vous permet de créer des affichages personnalisés dans la console de l’OMS qui contiennent différentes visualisations de données dans le référentiel de l’OMS. Cet article contient une vue d’ensemble du Concepteur de vues et les procédures de création et de modification des affichages personnalisés.

Autres articles disponibles pour le Concepteur de vue sont :

- [Référence de mosaïque](log-analytics-view-designer-tiles.md) - référence des paramètres pour chacun des mosaïques disponibles à utiliser dans vos vues personnalisées. 
- [Référence de partie visualisation](log-analytics-view-designer-parts.md) - référence des paramètres pour chacun des mosaïques disponibles à utiliser dans vos vues personnalisées. 


## <a name="concepts"></a>Concepts
Les vues créées avec le Concepteur de vue contiennent les éléments dans le tableau suivant.

| Partie | Description |
|:--|:--|
| Mosaïque | Affiché sur le tableau de bord de présentation Analytique de journal principal.  Inclut un résumé visuel des informations contenues dans la vue personnalisée.  Les différents types de mosaïque fournissent différentes visualisations d’enregistrements dans le référentiel de l’OMS.  Cliquez sur la mosaïque pour ouvrir la vue personnalisée. |
| Affichage personnalisé | S’affiche lorsque l’utilisateur clique sur la mosaïque.  Contient une ou plusieurs parties de visualisation. |
| Parties de visualisation | Visualisation de données dans le référentiel de l’OMS basé sur une ou plusieurs [recherches de journaux](log-analytics-log-searches.md).  La plupart des composants inclut un en-tête qui fournit une visualisation de haut niveau et une liste des premiers résultats.  Types d’articles différents fournissent différentes visualisations d’enregistrements dans le référentiel de l’OMS.  Cliquez sur les éléments de la partie pour effectuer une recherche de journal fournit des rapports détaillés. |

![Vue d’ensemble du Concepteur de vue](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>Ajouter un concepteur de vues dans votre espace de travail
Alors que le Concepteur de vue est en mode Aperçu, vous devez l’ajouter à votre espace de travail en activant des **Fonctionnalités d’aperçu** dans la section **paramètres** du portail OMS.

![Activer l’aperçu](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>Création et modification des vues

### <a name="create-a-new-view"></a>Créer une nouvelle vue
Ouvrir une nouvelle vue dans le **Concepteur de vue** en cliquant sur la mosaïque du Concepteur de vues dans le tableau de bord OMS principal.

![Mosaïque de Concepteur de vue](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>Modifier un affichage existant
Pour modifier une vue existante dans le Concepteur de vue, ouvrez la vue en cliquant sur sa mosaïque dans le tableau de bord OMS principal.  Cliquez ensuite sur le bouton **Modifier** pour ouvrir la vue dans le Concepteur de vue.

![Modifier un affichage](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>Cloner une vue existante
Lorsque vous clonez un affichage, il crée une nouvelle vue et l’ouvre dans le Concepteur de vue.  La nouvelle vue aura le même nom que l’original avec « copier » ajouté à la fin de celui-ci.  Pour cloner un affichage, ouvrez la vue existante en cliquant sur sa mosaïque dans le tableau de bord OMS principal.  Cliquez ensuite sur le bouton **Dupliquer** pour ouvrir la vue dans le Concepteur de vue.

![Cloner une vue](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>Supprimer une vue
Pour supprimer une vue existante, ouvrez la vue en cliquant sur sa mosaïque dans le tableau de bord OMS principal.  Cliquez sur le bouton **Modifier** pour ouvrir la vue dans le Concepteur de vue, puis cliquez sur **Supprimer la vue**.

![Supprimer une vue](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>Exporter un affichage existant
Vous pouvez exporter une vue dans un fichier JSON que vous pouvez importer dans un autre espace de travail ou utiliser dans un [modèle de gestionnaire de ressources Azure](../resource-group-authoring-templates.md).  Pour exporter une vue existante, ouvrez la vue en cliquant sur sa mosaïque dans le tableau de bord OMS principal.  Cliquez ensuite sur le bouton **Exporter** pour créer un fichier dans le dossier de téléchargement du navigateur.  Le nom du fichier sera le nom de la vue avec l' extension *omsview*.

![Exporter un affichage](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>Importer un affichage existant
Vous pouvez importer un fichier *omsview* que vous avez exporté à partir d’un autre groupe d’administration.  Pour importer un affichage existant, d’abord créer une nouvelle vue.  Cliquez sur le bouton **Importer** , puis sélectionnez le fichier *omsview* .  La configuration dans le fichier est copiée dans la vue existante.

![Exporter un affichage](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>Travail avec le Concepteur de vue
Le Concepteur de vue comporte trois volets.  Le volet de **conception** représente la vue personnalisée.  Lorsque vous ajoutez des mosaïques et parties à partir du **contrôle** de volet dans le volet de **conception** , ils sont ajoutés à la vue.  Le volet **Propriétés** affiche les propriétés de mosaïque ou de la partie sélectionnée.

![Concepteur de vues](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Configurer la mosaïque de l’affichage
Un affichage personnalisé peut avoir un seul carreau.  Sélectionnez l’onglet **Afficher en mosaïque** dans le volet de **contrôle** à afficher la mosaïque en cours ou en sélectionner une autre.  Le volet **Propriétés** affiche les propriétés de la mosaïque en cours.  Configurer les propriétés de la mosaïque en fonction des informations détaillées dans la [Mosaïque de référence](log-analytics-view-designer-tiles.md) et cliquez sur **Appliquer** pour enregistrer les modifications.

### <a name="configure-visualization-parts"></a>Configurer les parties de visualisation
Une vue peut inclure un nombre quelconque d’éléments de visualisation.  Sélectionnez l’onglet **affichage** , puis sur Ajouter à l’affichage d’une partie de visualisation.  Le volet **Propriétés** affiche les propriétés de l’article sélectionné.  Configurer les propriétés d’affichage en fonction des informations détaillées dans la [référence de partie de visualisation](log-analytics-view-designer-parts.md) , puis cliquez sur **Appliquer** pour enregistrer les modifications.

### <a name="delete-a-visualization-part"></a>Supprimer une partie de la visualisation
Vous pouvez supprimer une partie de la visualisation de la vue en cliquant sur le bouton **X** dans le coin supérieur droit de la partie.

### <a name="rearrange-visualization-parts"></a>Réorganiser des parties de visualisation
Vues ont uniquement une ligne d’éléments de visualisation.  Réorganiser les articles existants dans un affichage en cliquant sur et en les faisant glisser vers un nouvel emplacement.


## <a name="next-steps"></a>Étapes suivantes

- Ajouter des [carreaux](log-analytics-view-designer-tiles.md) à votre affichage personnalisé.
- Ajouter [Des éléments de visualisation](log-analytics-view-designer-parts.md) à votre affichage personnalisé.
