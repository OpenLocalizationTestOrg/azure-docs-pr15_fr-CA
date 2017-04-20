<properties 
    pageTitle="Comment écrire des requêtes dans le flux de données Analytique | Microsoft Azure" 
    description="Écrire des requêtes Analytique de flux de données et les données de la requête | apprentissage de segments de chemin d’accès."
    keywords="comment écrire des requêtes, des données de la requête, écrivez une requête, l’écriture de requêtes"
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

# <a name="how-to-write-queries-in-stream-analytics"></a>Comment écrire des requêtes dans le flux de données Analytique

Écriture de requêtes pour flux de traitement d’une logique dans Azure flux Analytique est implémenté comme une « requête permanent » définie avant une tâche démarre et exécutée sur les données qu’il atteint le travail. La transformation des données est exprimée dans un langage de requête de type SQL, qui est principalement un sous-ensemble de T-SQL avec certaines extensions de langage ajoutées comme [AWE](https://msdn.microsoft.com/library/azure/dn835019.aspx) utilisée pour exprimer la sémantique temporelle.

## <a name="writing-queries"></a>Écriture de requêtes : ##

1. Dans votre travail d’Analytique de flux dans le portail de gestion d’Azure, cliquez sur **requête**.

    ![Sélectionnez la requête](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  

    Dans le portail d’Azure, cliquez sur **requête**.

    ![Sélectionnez l’option Aperçu de la requête](./media/stream-analytics-write-queries/query-preview-portal.png)  

2.  Nouveaux travaux ont un modèle de requête pour vous aider à démarrer. Le modèle de requête exécute une requête « pass-through » projets tous les champs des événements d’entrée dans la sortie.  

    - Si vous avez défini au moins une entrée et la sortie de votre projet, vous pouvez remplacer l’espace réservé « [YourOutputAlias] » et les champs « [YourInputAlias] » avec l’alias de l’entrée et la sortie que vous souhaitez utilisent d’abord. En outre, vous pouvez toujours créer et tester votre requête dans le portail classique Azure sans définir les entrées et les sorties du projet.
    - Si vous souhaitez effectuer un traitement plus qu’un simple point de transit, vous pouvez modifier la définition de requête. Pour vous familiariser avec la création de la requête, examinons quelques modèles sont capturés de requête commune [ici](stream-analytics-stream-analytics-query-patterns.md).  
  
    ![Fenêtre de données de requête](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>Pour valider des données de requête fonctionne : ##

Vous pouvez vérifier que votre requête se comporte comme prévu en l’exécutant dans le navigateur sur un ou plusieurs fichiers JSON locales contenant les données de test. Cela ne sera pas démarrer le travail ou ont des implications de facturation.

> [AZURE.NOTE] Actuellement le test de requête de navigateur n’est pas pris en charge dans le portail Azure.  

1.  Assurez-vous qu’il n’y a pas d’erreurs dans la requête (dans le cas contraire le bouton Test est désactivé), puis cliquez sur le bouton Test.  

    ![Consulter les données de Test](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  

2.  Vous devrez spécifier des fichiers pour chacune des entrées référencées dans la requête. Dans cet exemple, la requête du modèle est laissée tel-étant, la boîte de dialogue demande une entrée appelée « yourinputalias ».  

    ![Tester la requête de données](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  

3.  Accédez à un fichier de test. Plusieurs exemples de fichiers sont disponibles sur [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) et vous pouvez également récupérer des données d’exemple à partir de vos propres entrées de flux de données via la fonction d’exemple de données dans l’onglet entrées.  

    ![Entrée de requête](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  

4.  Après avoir fermé la boîte de dialogue, votre requête sera exécutée sur les données de test, et vous verrez les résultats en bas de la page de requête.  

    ![Résumé de la requête](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
