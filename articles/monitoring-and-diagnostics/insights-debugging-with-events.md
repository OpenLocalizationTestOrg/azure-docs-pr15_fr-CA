<properties
    pageTitle="Afficher les événements et les journaux d’audit"
    description="Découvrez comment voir tous les événements qui se produisent dans votre abonnement Azure."
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
    ms.date="04/28/2015"
    ms.author="robb"/>

# <a name="view-events-and-audit-logs"></a>Afficher les événements et les journaux d’audit

Toutes les opérations effectuées sur les ressources Azure a entièrement sont auditées par le Gestionnaire de ressources Azure, depuis la création et la suppression de l’autorisation ou révocation de l’accès. Vous pouvez parcourir ces journaux dans Azure portal, et vous pouvez également utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder par programme à l’ensemble des événements.

## <a name="browse-the-events-impacting-your-azure-subscription"></a>Recherchez les événements sur votre abonnement Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir** , puis sélectionnez des **journaux d’Audit**.  
    ![Parcourir le concentrateur](./media/insights-debugging-with-events/Insights_Browse.png)
3. Vous ouvrirez ainsi une lame pour afficher tous les événements ont des conséquences sur un de vos abonnements au cours des 7 derniers jours. En haut est un graphique qui affiche les données par niveau et ci-dessous la liste complète des journaux :  ![tous les événements](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE] Vous ne pouvez afficher que les événements les plus récents 500 pour un abonnement donné dans le portail Azure.

4. Vous pouvez cliquer sur une entrée du journal pour voir les événements qui la composent. Par exemple, lorsque vous déployez un élément à un groupe de ressources, différentes ressources peuvent être créés ou modifiés. Pour chaque entrée, vous pouvez voir :
    * Le **niveau** de l’événement : par exemple, il peut être quelque chose à suivre (**Information**), ou lorsqu’un problème que vous devez savoir sur (**erreur**).
    * L' **état** - l’état final sera généralement **Succeeded** ou **Failed**, mais il peut également être **accepté** pour les opérations longues.
    * *Lorsque* l’événement s’est produit.
    * *Qui a* effectué l’opération, si tout le monde. Toutes les opérations sont effectuées par les utilisateurs, certaines sont exécutées par les services back-end afin qu’ils n’auraient pas un **appelant**.
    * L' **ID de corrélation** de l’événement : c’est l’identificateur unique de cette série d’opérations.

5. À partir de là, vous pouvez atteindre la lame de détails pour afficher les détails de l’événement.

    ![Groupes de ressources](./media/insights-debugging-with-events/Insights_EventDetails.png)

    Pour les événements **d’Échec** , cette page inclut généralement un **sous-état** et une section de **Propriétés** qui incluent des détails utiles pour le débogage.

## <a name="filter-to-specific-logs"></a>Filtrer les journaux spécifiques

Pour voir les événements qui s’appliquent à une entité spécifique, ou d’un type spécifique, vous pouvez filtrer la lame de journaux d’Audit en cliquant sur la commande de **filtre** . La lame de filtre permet également de modifier l' **intervalle de temps** de la lame de journaux d’Audit.

Une fois que vous cliquez sur cette commande, une nouvelle lame s’ouvre :

![Filtre](./media/insights-debugging-with-events/Insights_EventFilter.png)

Il existe quatre types de filtres :

1. Par abonnement
2. Par un **groupe de ressources**
3. Par un **type de ressource**
4. Par une **ressource** particulière - pour cela, vous devez au-delà de *l’ID de ressource* complet de la ressource qui que vous intéresse

En outre, vous pouvez également filtrer des événements qui a effectué l’événement ou, par le niveau de l’événement.

Une fois vous avez fini de choisir ce que vous souhaitez afficher, cliquez sur le bouton de **mise à jour** en bas de la lame.

## <a name="monitor-events-impacting-specific-resources"></a>Surveiller les événements ayant un impact sur des ressources spécifiques

1. Cliquez sur **Parcourir** pour rechercher la ressource qui que vous intéressez. Vous pouvez également voir tous les journaux de tout un **groupe de ressources**.
2. Sur la lame de la ressource, faites défiler jusqu'à ce que vous trouviez la mosaïque **d’événements** .  
    ![Mosaïque d’événements](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. Cliquez sur cette mosaïque pour afficher les événements filtrés pour uniquement sur la ressource que vous avez sélectionné. Vous pouvez utiliser la commande de **filtre** pour modifier la plage de temps ou d’appliquer des filtres plus spécifiques.

## <a name="next-steps"></a>Étapes suivantes

* [Recevoir des notifications d’alerte](insights-receive-alert-notifications.md) chaque fois qu’un événement se produit.
* [Métriques de service Moniteur](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et la réactivité.
* [État de service de suivi](insights-service-health.md) pour savoir quand Azure a rencontré des interruptions de service ou une dégradation des performances.  
