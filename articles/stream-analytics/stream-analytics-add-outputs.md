<properties 
    pageTitle="Comment configurer les données de sortie pour les tâches de flux de données Analytique | Microsoft Azure" 
    description="Configurer les sorties pour les tâches de flux de données Analytique | apprentissage de segments de chemin d’accès."
    keywords="données de sortie, le déplacement des données"
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

# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Comment configurer les données de sortie pour les tâches de flux de données Analytique

Les travaux de flux Analytique Azure peuvent être connectés à une ou plusieurs sorties de données qui définissent une connexion à un récepteur de données existant. Comme votre tâche de flux de données Analytique traite et transforme les données entrantes, un flux d’événements de sortie de données est écrite dans la sortie de votre tâche.

Données d’Analytique de flux sorties peuvent être utilisés pour les tableaux de bord en temps réel ou des alertes, déclenchent des workflows de déplacement de données, ou simplement archiver les données de traitements par lots ultérieurement. Analytique de flux a integration de première classe avec plusieurs services Azure, qui sont présentés en détail ici.

Pour ajouter une sortie à votre tâche de flux de données Analytique :

1. Dans le portail classique d’Azure, cliquez sur **sorties** et puis cliquez sur **Ajouter une sortie** de votre tâche de flux de données Analytique.

    ![Ajouter les sorties](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  

    Dans le portail Azure, cliquez sur la mosaïque des **sorties** dans votre tâche de flux de données Analytique.

    ![Ajouter les sorties Azure Portal](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Spécifiez le type de la sortie :

    ![Choisissez le type de déplacement de données](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  

    ![Azure portal choisir le type de déplacement de données](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. Fournir un nom convivial pour cette sortie dans la zone **Alias de sortie** . Ce nom peut être utilisé dans les requêtes de votre travail ultérieurement pour faire référence à la sortie.  
    
    Renseignez le reste des propriétés de connexion requises pour se connecter à votre sortie.  Ces champs varient selon le type de sortie et sont définis en détail ici.  

    ![Ajouter des propriétés de sortie des données](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)  

4. Selon le type de sortie, vous devrez spécifier comment les données sont sérialisées ou mis en forme. Les paramètres de sérialisation spécifique pour chaque type de sortie sont décrits ici.

    Renseignez le reste des propriétés de connexion requises pour se connecter à votre source de données. Ces champs varient selon le type d’entrée et source de type et sont définies en détail [ici](stream-analytics-create-a-job.md).  

    ![Ajouter des données de sortie à concentrateur d’événements](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)  

    ![Sortie des données de portail Azure à concentrateur d’événements](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)  

> [Azure.Note] N’importe quel élément output ajouté au projet doit exister avant le démarrage de la tâche et transmission des événements commencent. Par exemple, si vous utilisez le stockage Blob sous la forme d’une sortie, la tâche ne crée pas un compte de stockage automatiquement. Il doit être créé par l’utilisateur avant le démarrage de la tâche d’ASA.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
