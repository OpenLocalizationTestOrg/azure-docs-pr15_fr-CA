<properties 
    pageTitle="Contrôle des travaux Analytique flux présentation | Microsoft Azure" 
    description="Présentation de contrôle des flux de données Analytique travaux" 
    keywords="Analyseur de requête"
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

# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Comprendre comment surveiller les requêtes et surveillance de travail Analytique de flux

## <a name="introduction-the-monitor-page"></a>Présentation : La page surveillance

Le portail de gestion d’Azure et Azure Portal de surface des mesures de performances clés qui peuvent être utilisés pour surveiller et résoudre les problèmes de votre requête et travail. 

Dans le portail de gestion d’Azure, cliquez sur l’onglet **Moniteur** d’un travail Analytique de flux de données en cours d’exécution pour voir ces mesures. Il existe un délai d’au maximum 1 minute dans les métriques de performances s’affiche dans la page de l’écran.  

  ![Travail d’analyse du tableau de bord](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

Dans le portail d’Azure, accédez à la tâche flux Analytique vous intéressez à la métrique pour voir et consultez la section **analyse** .  

  ![Travail Azure Portal analyse du tableau de bord](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)  

La première fois qu’une tâche de flux de données Analytique est créée dans une région, vous devrez configurer les Diagnostics pour la région. Pour ce faire, cliquez n’importe où dans la section de **surveillance** et de la lame de **Diagnostics** s’affiche. Ici vous pouvez activer les tests de diagnostic et de spécifier un compte de stockage pour les données d’analyse.  

  ![Requête de configuration du portail Azure Diagnostics](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Mesures disponibles pour les flux de données Analytique


| Métrique | Définition |
|--------|-------------|
| Utilisation de % SU | L’utilisation de l’unité de transmission en continu est assigné à un travail à partir de l’onglet échelle de la tâche. Cet indicateur doit parvenir à 80 %, ou ci-dessus, il est fort probable que le traitement de l’événement peut être retardée ou arrêté sa progression. |
| Événements d’entrée | Volume de données reçues par la tâche de flux Analytique, nombre d’événements. Cela permet de valider que les événements sont envoyés à la source d’entrée. |
| Événements de sortie | Quantité de données envoyées par la tâche de flux de données Analytique à la cible de sortie dans le nombre d’événements. |
| Ordre des événements | Nombre d’événements reçus dans l’ordre qui ont été supprimées ou donné un horodatage ajusté, basé sur la stratégie de classement des événements. Cela peut être affecté par la configuration du paramètre de tolérance fenêtre. |
| Erreurs de Conversion de données | Nombre d’erreurs de conversion de données supportés par une tâche de flux de données Analytique. |
| Erreurs d’exécution | Nombre d’erreurs qui se produisent pendant l’exécution d’une tâche de flux de données Analytique. |
| Événements d’entrée tardives | Nombre d’événements arrivant au plus tard à partir de la source qui a été supprimée ou leur horodatage a été ajusté, selon la configuration de la stratégie de classement des événements du paramètre fenêtre de tolérance d’arrivée tardive. |

## <a name="customizing-monitoring-in-the-azure-management-portal"></a>Surveillance de personnalisation du portail de gestion d’Azure ##

Jusqu'à 6 mesures peuvent être affichés sur un graphique.

Pour basculer entre l’affichage des valeurs relatives (valeur finale uniquement pour chaque mesure) et les valeurs absolues (axe des Y affiché), sélectionnez Relative ou absolue du haut du graphique.

  ![Analyseur de requête relatif absolu](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)  

Les mesures peuvent être affichés dans le graphique de l’analyseur dans les agrégations de 1 heure, 12 heures, 24 heures ou 7 jours.

Pour modifier la plage de temps la métrique du graphique s’affiche, sélectionnez 1 heure, 24 heures, soit 7 jours en haut du graphique.

  ![Analyseur de requête échelle de temps](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)  

Vous pouvez définir des règles qui peuvent vous informer par courrier électronique si le travail dépasse un seuil défini. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personnalisation de l’analyse dans le portail Azure ##

Vous pouvez ajuster le type de graphique, les mesures affichées et la période dans les paramètres modifier le graphique. Pour plus d’informations, consultez [comment personnaliser le contrôle](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Échelle de temps du moniteur Azure requête portail](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  

## <a name="job-status"></a>Statut de la tâche

Le statut des tâches de flux de données Analytique peut être affiché dans le portail classique Azure où vous voyez une liste de tâches. Vous pouvez voir la liste des tâches en cliquant sur l’icône de flux Analytique dans le portail classique d’Azure.

| État | Définition |
|--------|------------|
| Créé | Une tâche a été créé, n’a toutefois pas été démarré. |
| Démarrage | Un utilisateur a cliqué, sur début, le travail et le démarrage de la tâche |
| En cours d’exécution | Le travail est alloué, le traitement des entrées, ou en attente de traiter l’entrée. Si la tâche indique un état d’exécution sans générer une sortie, il est probable que la fenêtre de temps de traitement de données est grande ou si la logique de requête est complexe. Une autre raison peut être qu’actuellement il n’est pas toutes les données envoyées à la tâche. |
| Arrêt | Un utilisateur clique sur à la fin de la tâche, et l’arrêt de la tâche. |
| Arrêté | Le travail a été arrêté. |
| Dégradé | Cet état indique qu’une tâche de flux de données Analytique est rencontrer des erreurs transitoires (pour ex. Les erreurs d’entrée/sorties, erreurs de traitement, des erreurs de conversion etc..). Le travail est toujours en cours d’exécution, cependant, il existe un grand nombre d’erreurs en cours de génération. Cette tâche nécessite l’attention du client, et le client peut voir les journaux d’opérations pour les erreurs. |
| A échoué | Cela indique que l’opération a échoué en raison d’erreurs, et le traitement s’est arrêté. Le client doit rechercher dans les journaux des opérations pour déboguer les erreurs. |
| Suppression de | Cela indique que le travail est en cours de suppression. |

## <a name="diagnosis"></a>Diagnostic

Dans le portail de gestion d’Azure, le tableau de bord projet fournit des informations sur où vous devez rechercher pour le diagnostic, c'est-à-dire les entrées, sorties ou les opérations. Vous pouvez cliquer sur le lien à l’emplacement approprié afin d’examiner le diagnostic.

  ![Analyseur de requête erreur](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)  

Fournit des informations de diagnostic détaillées en cliquant sur la ressource d’entrée ou de sortie. Il est actualisé avec les dernières informations de diagnostic pendant l’exécution de la tâche.

  ![Diagnostics de requête](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
