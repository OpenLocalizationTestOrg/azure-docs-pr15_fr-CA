<properties
    pageTitle="Visualiser et résoudre les tâches de flux de données Analytique | Microsoft Azure"
    description="Apprenez à visualiser un pipeline de travail Analytique de flux pour le libre-service de dépannage à l’aide de la fonctionnalité de diagramme de diagnostics."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualiser et résoudre les tâches de flux de données Analytique

Dans le flux de données Analytique, à l’instar des autres technologies basées sur le nuage, résolution des problèmes sont parfois nécessaire de pourquoi un travail ne produit pas le résultat attendu (ou toute sortie d’ailleurs). Dans cet esprit, Analytique de flux permet de visualiser un travail en continu. Cela est également utile comme outil de modélisation et présente l’avantage de côté pour ces nécessitant une documentation de leur travail.

Dans le panneau de visualisation, les entrées sont visibles, ainsi que la requête en cours d’exécution et ensuite toutes les sorties configurés. Problèmes de connectivité ou de configuration peuvent devenir plus évidentes et il peut également être utile d’afficher une représentation visuelle de votre configuration.

## <a name="using-the-diagnosis-diagram-tool"></a>À l’aide de l’outil de diagramme de diagnostic

Pour accéder à ce visualiseur, cliquez simplement sur le bouton « Diagramme de diagnostic » de la lame de « Paramètres » de la de la tâche de flux de données Analytique.

![Stream-Analytics-Troubleshoot-Visualization-Diagnosis-Diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Chaque entrée et sortie sont état de couleur pour indiquer le courant de ce composant, comme indiqué ci-dessous.

![Stream-Analytics-Troubleshoot-Visualization-Input-Map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Lorsque l’utilisateur souhaite consulter des étapes de requête intermédiaire à comprendre les modèles de flux de données à l’intérieur d’une tâche, l’outil de visualisation offre une vue de la répartition de la requête dans ses étapes composant et la séquence de flux. Cliquez sur chaque étape de la requête pour afficher la section correspondante dans une requête de modification de volet comme illustré. 

![Stream-Analytics-Troubleshoot-Visualization-Intermediate-Steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
