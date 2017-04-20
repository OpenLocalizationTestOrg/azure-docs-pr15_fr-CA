<properties
    pageTitle="Recevoir des notifications d’alerte pour les services Azure | Microsoft Azure"
    description="Être averti lorsque les conditions de règles d’alerte sont remplies."
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
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="receive-alert-notifications"></a>Recevoir des notifications d’alerte

Vous pouvez recevoir une alerte basée sur des mesures de surveillance pour ou d’événements sur vos services Azure.

Pour une règle d’alerte sur une valeur métrique, lorsque la valeur d’une mesure spécifiée dépasse un seuil affecté, la règle d’alerte devient active et peut envoyer une notification. Pour une règle d’alerte sur les événements, une règle peut envoyer une notification sur *chaque* événement ou, uniquement lorsqu’un certain nombre d’événements se produire.

Lorsque vous créez une règle d’alerte, vous pouvez sélectionner des options pour envoyer une notification par e-mail à l’administrateur de service et des administrateurs, ou à un autre administrateur que vous spécifiez. Un e-mail de notification est envoyé lorsque la règle devient active et lorsqu’une condition d’alerte est résolue.

Vous pouvez utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931945.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) à configurer et à obtenir par programmation des informations sur les règles d’alerte.

## <a name="create-an-alert-rule"></a>Créer une règle d’alerte

1. Dans le [portail](https://portal.azure.com/), cliquez sur **Parcourir**, puis sur une ressource qui vous intéresse dans l’analyse.

2. Cliquez sur le carré de **règles d’alerte** dans l’objectif **d’opérations** .

3. Cliquez sur la commande **Ajouter une alerte** .

    ![Ajout d’alerte](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. Vous pouvez nommer votre règle d’alerte et choisissez une description qui s’affichera dans l’e-mail de notification.

5. Lorsque vous sélectionnez **mesures** vous allez choisir une condition et une valeur de seuil pour la mesure. Il s’agit de la période de temps qui Azure utilise pour surveiller et traçage l’activité alerte.

    ![Condition et seuil](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. Vous pouvez également choisir des **événements**et recevoir une notification lorsqu’un certain événement se produit.

    ![Événements](./media/insights-receive-alert-notifications/Insights_Events.png)

7. Enfin, vous pouvez choisir d’envoyer la notification par courrier électronique aux administrateurs responsables.

Après avoir cliqué sur **Enregistrer**, dans quelques minutes vous serez informé dès que la métrique que vous choisissez dépasse le seuil.

## <a name="managing-your-alert-rules"></a>Gestion de vos règles d’alerte

Une fois que vous avez créé une règle d’alerte, vous pouvez afficher un aperçu de votre alerte de seuil par rapport à la métrique du jour précédent.

![Événements](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


Vous pouvez bien entendu modifier cette règle d’alerte et **Activer** ou **désactiver** il si vous voulez temporairement arrêter de recevoir des notifications sur celui-ci.

## <a name="next-steps"></a>Étapes suivantes

* [Webhooks de configuration sur vos alertes](insights-webhooks-alerts.md) pour acheminer des notifications à des canaux différents
* [Métriques de service Moniteur](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et la réactivité.
* [Activer l’analyse et les tests de diagnostic](insights-how-to-use-diagnostics.md) pour collecter les mesures détaillées de haute fréquence sur votre service.
* [Analyser la disponibilité et le temps de réponse d’une page web](../application-insights/app-insights-monitor-web-app-availability.md) avec les perspectives d’Application afin que vous pouvez déterminer si votre page ne fonctionne pas.
* [Performances de l’application Moniteur](../application-insights/app-insights-azure-web-apps.md) si vous souhaitez comprendre exactement comment votre code s’exécute dans le nuage.
* [Afficher les événements et les journaux d’audit](insights-debugging-with-events.md) pour découvrir tout ce qui est arrivé dans votre service.
* [État de service de suivi](insights-service-health.md) pour savoir quand Azure a rencontré des interruptions de service ou une dégradation des performances.
