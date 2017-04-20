<properties 
    pageTitle="Déboguer à l’aide d’opération et service consigne les flux Analytique | Microsoft Azure" 
    description="Journaux d’opération de flux Analytique sur les procédures d’utilisation" 
    keywords="journaux de service"
    services="stream-analytics" 
    documentationCenter="" 
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

# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Travaux Analytique de flux de données à l’aide du service et l’exploitation des journaux de débogage

Tous les services Azure fournissent des messages de journalisation opérationnel pour les utilisateurs à enregistrer les détails liés aux opérations de gestion. Analytique de flux d’Azure, ces informations peuvent être utilisées pour le débogage, tels que l’affichage de l’état du travail, progression de la tâche, et messages d’échec pour effectuer le suivi de l’avancement d’une tâche dans le temps, à partir de démarrer le traitement en sortie.

## <a name="find-operation-logs-in-the-azure-management-portal"></a>Rechercher l’opération enregistre dans le portail de gestion d’Azure

Journaux d’opération est accessible de deux manières :  

- Tableau de bord de la tâche de flux de données Analytique  
- Services de gestion dans le portail Azure classique  

## <a name="dashboard-of-the-stream-analytics-job"></a>Tableau de bord de la tâche de flux de données Analytique

Un lien vers les journaux correspondants d’une tâche de flux de données Analytique est affiché sur un onglet de tableau de bord du travail. Si vous cliquez sur ce lien, il définit les filtres de sorte qu’il affiche des journaux plus récents pour cette tâche spécifique.

  ![Sélectionnez les journaux de gestion des Services](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Services de gestion

Pour accéder manuellement aux journaux d’opération pour l’Analytique de flux et d’autres services dans le portail Azure classique :

1.  Cliquez sur **Gestion des Services** dans le [portail Azure classique](https://manage.windowsazure.com).
2.  Nom du **Service**, sélectionnez **Analytique de flux** pour le **Type** et le nom de la tâche.  

  ![Sélectionnez le flux de données Analytique](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Rechercher des journaux d’audit dans le portail Azure ##

Pour rechercher les journaux des opérations de votre tâche de flux Analytique dans Azure portal, cliquez sur **Parcourir** et sélectionnez les **journaux d’Audit**.

  ![Azure portal Analytique de flux sélectionnez](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Ceci ouvrira une blade d’affichage des événements survenus au cours des 7 derniers jours pour toutes les ressources de votre abonnement.  Vous pouvez filtrer pour afficher les événements d’un type de spécifier ou d’intervalle de temps en cliquant sur la commande de **filtre** .

  ![Azure portal Analytique de flux sélectionnez](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Obtenir les détails du journal

Vous pouvez filtrer par plage de temps et d’état pour afficher les journaux pour votre travail.

Dans le portail de gestion d’Azure, cliquez sur le bouton **Détails** en bas de la fenêtre pour afficher plus de détails sur un événement sélectionné. 

  ![Sélectionnez les détails](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

Dans le portail Azure, cliquez sur une entrée de journal pour voir les détail des événements qu’elle contient.

  ![Sélectionnez les détails de portail Azure](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

À partir de là, vous pouvez ouvrir la lame de **Détail** en cliquant sur l’événement.

  ![Sélectionnez les détails de portail Azure](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Déboguer l’échec d’un travail

Dans le portail de gestion d’Azure, cliquez sur l’icône de recherche et le type « échoué ». Cela donne un résultat de tous les journaux avec des erreurs. 

  ![Échec d’un travail de débogage](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

Dans le portail Azure, vous pouvez filtrer par niveau de message pour afficher les événements **critiques** .

  ![Débogage de portail Azure](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

Vous pouvez sélectionner n’importe laquelle des échecs d’et cliquez sur **Détails** pour plus d’informations sur l’erreur.  Certains messages d’erreur fournissent des informations sur la façon d’atténuer le problème. 

  ![Détails de l’opération](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

Dans le cas où vous devez contacter le [support technique](https://azure.microsoft.com/support/options/) ou de fournir des informations à l’équipe via le [forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), notez les détails de l’opération, en particulier l' **ID de corrélation**. 

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
