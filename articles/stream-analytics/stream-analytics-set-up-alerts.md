<properties
    pageTitle="Configurer des alertes pour les requêtes dans le flux de données Analytique | Microsoft Azure"
    description="Présentation Analytique de flux de données d’alerte"
    keywords="configurer des alertes"
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


# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Définir des alertes pour les traitements d’Analytique de flux de données Azure

## <a name="introduction-monitor-page"></a>Introduction : La page Moniteur

Vous pouvez définir des alertes pour déclencher une alerte lorsqu’un indicateur atteint une condition que vous spécifiez.

Par exemple, « si les événements de sortie pour les 15 dernières minutes est < 100, envoyer la notification par courrier électronique à l’id de courrier électronique : xyz@company.com”.

Règles permet de configurer des métriques via le portail, ou peuvent être configuré [par programmation](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sur les données de journaux.

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Configurer des alertes par le biais du portail classique Azure

Il existe deux façons de configurer des alertes dans le portail Azure classique :  

1.  L’onglet **Analyseur** de votre tâche de flux de données Analytique  
2.  Le journal des opérations dans les services de gestion  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>Définition d’alerte par le biais de l’onglet Moniteur de la tâche dans le portail

1.  Sélectionnez la mesure dans l’onglet Moniteur, cliquez sur le bouton **Ajouter une règle** au bas du tableau de bord et le programme d’installation de vos règles.  

    ![Tableau de bord](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  

2.  Définissez le nom et la description de l’alerte  

    ![Créer la règle](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  

3.  Entrez les seuils, fenêtre d’alerte d’évaluation et les actions de l’alerte  

    ![Définir des Conditions](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>Configurer des alertes dans les journaux des opérations

1.  Accédez à l’onglet **alertes** dans les Services de gestion dans le [Portail classique d’Azure](https://manage.windowsazure.com).  
2.  Cliquez sur **Ajouter une règle**  

    ![Critères](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  

3.  Définir le nom et la description de l’alerte. Sélectionnez « Flux Analytique » comme Type de Service et le nom de projet comme nom de Service.  

    ![Définir l’alerte](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurer des alertes dans le portail Azure ##

Dans le portail d’Azure, à la tâche flux Analytique vous intéressez le déclenchement d’alertes et cliquez sur la section **analyse** .  De la lame de **mesure** qui s’ouvre, cliquez sur la commande **Ajouter une alerte** .

  ![Programme d’installation portail Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

Vous pouvez nommer votre règle d’alerte et choisissez une description qui s’affichera dans l’e-mail de notification.

Lorsque vous sélectionnez mesures vous allez choisir une condition et une valeur de seuil pour la mesure.

  ![Métrique de sélectionnez portail Azure](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Pour plus d’informations sur la configuration des alertes dans le portail Azure, consultez [réception notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
