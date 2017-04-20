<properties 
    pageTitle="Comment créer une tâche de traitement de données analytique de flux Analytique | Microsoft Azure" 
    description="Créer une tâche de traitement de données analytique de flux Analytique | apprentissage de segments de chemin d’accès."
    keywords="traitement analytique des données"
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

# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Comment faire pour créer une tâche de traitement de données analytique pour l’Analytique de flux

Les ressources de niveau supérieur dans Azure flux Analytique est un flux de travail d’Analytique.  Il se compose d’une ou plusieurs cibles de sortie des résultats sont écrits dans une ou plusieurs sources de données d’entrée et une requête exprimant la transformation des données. Ensemble ces permettent à l’utilisateur d’effectuer l’analytique des données de traitement pour les scénarios de données de transmission en continu.

Pour démarrer à l’aide de flux de données Analytique, commencez par créer une nouvelle tâche de flux de données Analytique.  Notez que cette action n’a aucune incidence facturation jusqu'à ce que le démarrage de la tâche.

1.  Connectez-vous sur le site online [Azure portal classique](http://manage.windowsazure.com) ou le [portail Azure](https://portal.azure.com/).
2.  Dans le portail : **Cliquez sur Nouveau**, puis cliquez sur **Services de données** ou des **Données Analytique** en fonction de votre portail, puis sur **Azure flux Analytique** ou **Analytique de flux** et puis **Création rapide**.

    ![Assistant de tâche de traitement analytique des données](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  

    ![Analytique des données travail en cours de création](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  

3.  Spécifier la configuration souhaitée pour la tâche de flux de données Analytique.
    - Dans la zone **Nom de la tâche** , entrez un nom pour identifier la tâche de flux de données Analytique. Lorsque le **Nom du travail** est validé, une coche verte s’affiche dans la zone Nom de la tâche. Le **Nom de la tâche** peut contenir uniquement des caractères alphanumériques et '-' caractères et doit être comprise entre 3 et 63 caractères.
    - Utilisez **la région** dans le portail Azure ou **l’emplacement** dans le portail Azure pour spécifier l’emplacement géographique où vous souhaitez exécuter la tâche.
    - Si vous utilisez le portail d’Azure, sélectionnez ou créez un compte de stockage à utiliser comme le **Compte de stockage de surveillance régionale**. Ce compte de stockage est utilisé pour stocker les données de tous les travaux d’Analytique de flux de données en cours d’exécution dans cette région.
    - Si vous utilisez le portail d’Azure, spécifiez un **Groupe de ressources** de nouvelle ou existante pour contenir les ressources associées à votre application.

4.  Une fois les nouvelles options de travail Analytique de flux de données sont configurées, cliquez sur **Créer un flux de travail Analytique**. Il peut prendre quelques minutes pour le travail d’Analytique de flux de données à créer. Pour vérifier l’état, vous pouvez surveiller la progression dans le concentrateur de Notifications.

    ![Concentrateur de notifications de données analytique traitement de travail](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  

    ![Analytique de données portail Azure traitement de tâche Créer tâche](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  

5.  Le nouveau projet apparaît avec le statut **créé**. Notez que le bouton **Démarrer** est désactivé. Avant de commencer le travail, vous devez configurer l’entrée de tâche, la requête et la sortie.

    ![Projet de traitement analytique des données état](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  

    ![Analytique de données portail Azure traitement de statut de la tâche projet](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
