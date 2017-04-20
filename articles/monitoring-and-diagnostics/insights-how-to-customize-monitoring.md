<properties
    pageTitle="Vue d’ensemble des mesures dans Microsoft Azure | Microsoft Azure"
    description="Apprenez à personnaliser les graphiques de surveillance dans Azure."
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

# <a name="overview-of-metrics-in-microsoft-azure"></a>Vue d’ensemble des mesures dans Microsoft Azure

Tous les services Azure le suivi des indicateurs clés qui vous permettent de surveiller la santé, de performances, de disponibilité et d’utilisation de vos services. Vous pouvez afficher ces métriques dans Azure portal, et vous pouvez également utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder par programme à l’ensemble des mesures.

Pour certains services, vous devrez activer les diagnostics afin de voir les mesures. Pour d’autres, comme les machines virtuelles, vous obtenez un ensemble de mesures de base, mais doit définir des métriques de haute fréquence pour activer la version complète. Reportez-vous à la section [Activer la surveillance et diagnostic](insights-how-to-use-diagnostics.md) pour en savoir plus.

## <a name="using-monitoring-charts"></a>À l’aide de graphiques de surveillance

Vous pouvez représenter une des mesures les sur une période de temps que vous choisissez.

1. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Parcourir**, puis sur une ressource qui vous intéresse dans l’analyse.

2. La section **analyse** contient la métrique la plus importante pour chaque ressource Azure. Par exemple, une application web a des **erreurs et demandes de**cas comme une machine virtuelle aurait **pourcentage processeur** et **disque de lecture et d’écriture**:  ![lentille de surveillance](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. En cliquant sur n’importe quel graphique affichera la lame de **mesure** . Sur la blade, en plus de la courbe, est un tableau indiquant les agrégations des mesures (par exemple, la moyenne, minimale et maximale, sur la plage horaire que vous avez choisi). Au-dessous se trouvent les règles d’alerte pour la ressource.
    ![Lame de métrique](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Pour personnaliser les lignes qui s’affichent, cliquez sur le bouton **Modifier** dans le graphique, ou la commande **Modifier le graphique** sur la lame de mesure.

5. Sur la lame de modifier la requête, vous pouvez faire trois choses :
    - Modifier la plage de temps
    - Changer l’apparence entre barre et ligne
    - Choisissez différentes metics ![modifier la requête](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Modification de l’intervalle de temps, il vous suffit de sélectionner une plage différente (par exemple, de la **Dernière heure**) et en cliquant sur **Enregistrer** en bas de la lame. Vous pouvez également choisir **personnalisée**, qui vous permet de choisir une période de temps sur les 2 dernières semaines. Par exemple, vous voyez l’ensemble deux semaines ou, simplement 1 heure d’hier. Tapez dans la zone de texte pour entrer une autre heure.
    ![Période personnalisée](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. Au-dessous de la plage de temps, chan vous choisissez n’importe quel nombre de mesures à afficher sur le graphique.

8. Lorsque vous cliquez sur Enregistrer les modifications apportées seront enregistrées pour cette ressource. Par exemple, si vous avez deux ordinateurs virtuels et que vous modifiez un graphique sur une, il n’affecteront pas l’autre.

## <a name="creating-side-by-side-charts"></a>Création de graphiques de côte-à-côte

Avec la personnalisation puissante dans le portail, vous pouvez ajouter autant de graphiques que vous le souhaitez.

1. Dans le menu **...** en haut de la lame, cliquez sur **Ajouter des mosaïques**:  
    ![Ajouter Menu](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Ensuite, vous pouvez sélectionner un graphique à partir de la **Galerie** sur le côté droit de l’écran :  ![la galerie](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Si vous ne voyez pas la mesure que vous souhaitez, vous pouvez toujours ajouter une des mesures prédéfinies et **Modifier** le graphique pour afficher la mesure dont vous avez besoin.

## <a name="monitoring-usage-quotas"></a>Surveillance des quotas d’utilisation

La plupart des mesures de vous montrent des tendances au fil du temps, mais certaines données, telles que les quotas d’utilisation, sont des informations de point-à-temps avec un seuil.

Vous pouvez également voir des quotas d’utilisation sur la lame pour les ressources qui ont des quotas :

![Utilisation de](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Comme avec les mesures, vous pouvez utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder par programme à l’ensemble des quotas d’utilisation.

## <a name="next-steps"></a>Étapes suivantes

* [Recevoir des notifications d’alerte](insights-receive-alert-notifications.md) chaque fois qu’une mesure dépasse un seuil.
* [Activer l’analyse et les tests de diagnostic](insights-how-to-use-diagnostics.md) pour collecter les mesures détaillées de haute fréquence sur votre service.
* [Mettre automatiquement à l’échelle nombre d’instances](insights-how-to-scale.md) pour vous assurer que votre service est disponible et la réactivité.
* [Performances de l’application Moniteur](../application-insights/app-insights-azure-web-apps.md) si vous souhaitez comprendre exactement comment votre code s’exécute dans le nuage.
* Utilisez les [Perspectives d’Application pour les applications de JavaScript et des pages web](../application-insights/app-insights-web-track-usage.md) pour obtenir les analytique du client sur les navigateurs que vous visitez une page web.
* [Analyser la disponibilité et le temps de réponse d’une page web](../application-insights/app-insights-monitor-web-app-availability.md) avec les perspectives d’Application afin que vous pouvez déterminer si votre page ne fonctionne pas.
