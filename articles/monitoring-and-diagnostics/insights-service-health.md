<properties
    pageTitle="Suivre l’intégrité du service Azure à l’aide des journaux d’activité de moniteur Azure | Microsoft Azure"
    description="Découvrez lorsque Azure a rencontré des interruptions de service ou une dégradation des performances. "
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Suivre l’intégrité du service Azure à l’aide des journaux d’activité de moniteur Azure

Azure publicizes chaque fois qu’il y a une dégradation de performances ou d’interruption de service. Vous pouvez parcourir ces événements dans le portail Azure, et vous pouvez également utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder par programme à l’ensemble des événements.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Parcourir les journaux de santé service pour votre abonnement

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Dans **la maison** , vous devriez voir une mosaïque appelée le **fonctionnement du Service**. Cliquez dessus.

    ![Accueil](./media/insights-service-health/Insights_Home.png)

3. Vous consultez une liste de toutes les zones dans Azure. Cliquez sur n’importe quelle région afin d’afficher la requête du journal d’activité qui affiche les incidents de service ont des conséquences sur un de vos abonnements au cours des 24 dernières heures.

    ![Fonctionnement du Service abonnement activité journal](./media/insights-service-health/AzureActivityLogServiceHealth3.png)

4. Vous pouvez voir les détails d’un incident individuel en cliquant sur cet événement dans la table.

5. Modifier le **Timespan** pour afficher une période plus longue.

## <a name="next-steps"></a>Étapes suivantes

* [Analyser la disponibilité et le temps de réponse d’une page web](../application-insights/app-insights-monitor-web-app-availability.md) avec les perspectives d’Application afin que vous pouvez déterminer si votre page ne fonctionne pas.
