<properties 
    pageTitle="Comment démarrer la diffusion en continu des travaux dans le flux de données Analytique | Microsoft Azure" 
    description="Comment exécuter une tâche de transmission en continu dans Azure flux Analytique | apprentissage de segments de chemin d’accès."
    keywords="flux de travaux"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Comment faire pour exécuter un travail en continu dans Azure flux Analytique

Lorsqu’une tâche d’entrée, la requête et la sortie ont tous été spécifiés que vous pouvez démarrer la tâche de flux de données Analytique.

Pour démarrer votre projet :

1.  Dans le portail Azure classique, à partir du tableau de bord projet, cliquez sur **Démarrer** au bas de la page.

    ![Démarrer les travaux, bouton](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  

    Dans le portail Azure, cliquez sur **Démarrer** en haut de votre page de tâche.

    ![Tâche de début bouton portail Azure](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  

2.  Spécifiez une valeur de **Sortie de démarrer** pour déterminer quand cette tâche commencera à générer une sortie. Le paramètre par défaut pour les tâches qui n’ont pas déjà été lancées est l' **Heure de début du projet**, ce qui signifie que la tâche démarre immédiatement les données de traitement. Vous pouvez également spécifier **une heure** dans le passé (pour consommer des données historiques) ou à l’avenir (pour retarder le traitement jusqu'à une date ultérieure). Pour les cas lorsqu’une tâche a été précédemment démarrée et arrêtée, l’option **Dernière arrêté** est disponible afin de reprendre la tâche à partir de la dernière heure de sortie et d’éviter toute perte de données.  

    ![Démarrer la diffusion en continu de temps de travail](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  

    ![Azure portail en continu tâche de début heure](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  

3.  Confirmez votre sélection. Le statut de la tâche passera à *démarrage* et se déplace sous peu à *l’exécution* une fois que la tâche a commencé. Vous pouvez surveiller la progression de l’opération de **démarrage** dans le **Concentrateur de Notification**:

    ![diffusion en continu de la progression du projet](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  

    ![Azure portal en flux continu de la progression du projet](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
