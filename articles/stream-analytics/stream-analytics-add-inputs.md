<properties
    pageTitle="Ajouter une entrée de données pour vos travaux d’Analytique de flux | Microsoft Azure"
    description="Découvrez comment connecter une source de données à votre travail Analytique de flux comme flux d’entrée de données à partir des données d’événement concentrateurs ou de référence à partir du stockage du Blog."
    keywords="données d’entrée, en flux continu de données"
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


# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Ajouter un flux d’entrée ou une référence de données à un projet de flux de données Analytique

Découvrez comment connecter une source de données à votre travail Analytique de flux comme flux d’entrée de données à partir de l’événement concentrateurs ou référencer des données depuis le stockage Blob.

Les travaux de flux Analytique Azure peuvent être connectés à l’entrée de données d’un ou plusieurs, chacun d’eux définir une connexion à une source de données existante. Comme les données sont envoyées à la source de données, il est utilisé par la tâche de flux de données Analytique et traité en temps réel comme en flux continu de données. Analytique de flux offre une intégration première classe avec les [Concentrateurs d’événement Azure](https://azure.microsoft.com/services/event-hubs/) et de [stockage des objets Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) au sein et en dehors de l’abonnement du travail.

Cet article est une étape dans [flux Analytique cursus](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Entrée de données : les données de référence et les données de diffusion en continu

Il existe deux types d’entrées dans le flux de données Analytique : flux de données et les données de référence.

- **Flux de données**: les tâches de flux de données Analytique doivent inclure l’entrée de flux de données au moins une pour être utilisés et transformés par la tâche. Stockage Blob Azure et les concentrateurs d’événement Azure sont pris en charge comme sources d’entrée de flux de données. Azure concentrateurs d’événement sont utilisés pour collecter les flux d’événements à partir des applications, services et périphériques connectés. Stockage de Blob Azure peut servir comme une source d’entrée pour l’ingestion des données en bloc sous forme de flux.  
- **Les données de référence**: Analytique de flux prend en charge un deuxième type de données de référence d’appelée d’entrée auxiliaire.  Au lieu des données de mouvement, ces données sont statiques ou le ralentissement de la modification.  Il est généralement utilisé pour effectuer des recherches et des corrélations avec les flux de données pour créer un jeu de données plus riche.  Stockage de Blob Azure est actuellement la seule source d’entrée pris en charge pour les données de référence.  

Pour ajouter une entrée à votre tâche de flux de données Analytique :

1. Dans le portail Azure, cliquez sur les **entrées** et puis cliquez sur **Ajouter une entrée** dans votre tâche de flux de données Analytique.

    ![Azure portal classique - ajouter une entrée.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  

    Dans le portail Azure, cliquez sur la mosaïque **d’entrées** dans votre tâche de flux de données Analytique.  

    ![Azure portal - ajouter l’entrée de données.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  

2. Spécifier le type de l’entrée : **flux de données** ou de **données de référence**.

    ![Ajouter les données correctes d’entrée, diffusé en continu ou référence](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  

    ![Ajouter les données correctes d’entrée, diffusé en continu ou référence](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  

3. Si vous créez une entrée de flux de données, spécifiez le type de source pour l’entrée.  Cette étape peut être omise lors de la création de données de référence en tant que Blob uniquement de stockage est pris en charge pour l’instant.

    ![Ajoutez l’entrée de données de flux de données](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  

    ![Ajoutez l’entrée de données de flux de données](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  

4. Fournir un nom convivial pour cette entrée dans la zone Alias d’entrée.  Ce nom sera utilisé dans votre requête d’ultérieurement pour faire référence à l’entrée.

    Renseignez le reste des propriétés de connexion requises pour se connecter à votre source de données. Ces champs varient selon le type d’entrée et source de type et sont définies en détail [ici](stream-analytics-create-a-job.md).  

    ![Ajouter des entrées de données événement concentrateur](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  

5. Spécifiez les paramètres de sérialisation pour les données d’entrée :
    - Pour vous assurer que vos requêtes fonctionnent comme vous le souhaitez, spécifiez le **Format de sérialisation d’événement** des données entrantes.  JSON, CSV et Avro sont des formats de sérialisation pris en charge.
    - Vérifiez que le **codage** des données.  UTF-8 est le seul format de codage pris en charge pour l’instant.

    ![Paramètres de sérialisation des données pour les données d’entrée](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  

    ![Paramètres de sérialisation des données pour les données d’entrée](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  

6. À l’issue de la création d’entrée, flux Analytique vérifie qu’il peut se connecter à la source d’entrée.  Vous pouvez afficher l’état de l’opération de connexion de Test dans le concentrateur de Notification.

    ![Tester la connexion les flux d’entrée de données](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  

    ![Tester la connexion les flux d’entrée de données](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Obtenir de l’aide avec les entrées de données de transmission en continu
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
