<properties
    pageTitle="Réel-temps-statistiques dans Azure CDN | Microsoft Azure"
    description="Statistiques en temps réel fournit des données en temps réel sur les performances de Azure CDN pour la livraison de contenu à vos clients."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Statistiques en temps réel dans Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Vue d’ensemble

Ce document explique les statistiques en temps réel dans Microsoft Azure CDN.  Cette fonctionnalité fournit des données en temps réel, telles que la bande passante et des connexions simultanées à votre profil CDN statuts de cache lors de la diffusion de contenu sur vos clients. Cela permet une surveillance continue de la santé de votre service à tout moment, y compris les événements de mise en service.

Les graphiques suivants sont disponibles :

* [Bande passante](#bandwidth)
* [Codes d’état](#status-codes)
* [Statuts de cache](#cache-statuses)
* [Connexions](#connections)


## <a name="accessing-real-time-stats"></a>L’accès à des statistiques en temps réel

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre profil CDN.

    ![Lame de profil CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)

2. À partir de la blade de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton Gérer les lames de profil CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

3. Pointez sur l’onglet **Analytique** , puis survolez le Lanceur **Les statistiques en temps réel** .  Cliquez sur **l’objet HTTP**.

    ![Portail de gestion CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)

    Les graphiques de statistiques en temps réel sont affichés.
    
Chacun des graphiques affiche des statistiques en temps réel pour la période sélectionnée, lorsque la page est chargée.  Les graphiques automatiquement à jour toutes les quelques secondes.  Le bouton **Actualiser un graphique** , le cas échéant, efface le graphique, après laquelle il affiche uniquement les données sélectionnées.

## <a name="bandwidth"></a>Bande passante

![Graphique de la bande passante](./media/cdn-real-time-stats/cdn-bandwidth.png)

Le graphique de **la bande passante** affiche la quantité de bande passante utilisée pour la plateforme en cours sur l’intervalle de temps sélectionné. La partie ombrée du graphique indique la bande passante. Le montant exact de la bande passante en cours d’utilisation s’affiche directement en dessous de la courbe.

## <a name="status-codes"></a>Codes d’état

![Graphique de code d’état](./media/cdn-real-time-stats/cdn-status-codes.png)

Le graphique de **Codes d’état** indique la fréquence à laquelle certains codes de réponse HTTP sont produisent sur l’intervalle de temps sélectionné.

> [AZURE.TIP]  Pour obtenir une description de chaque option de code d’état HTTP, reportez-vous à la section [Codes d’état HTTP Azure CDN](https://msdn.microsoft.com/library/mt759238.aspx).

Une liste des codes d’état HTTP s’affiche directement au-dessus du graphique. Cette liste indique chaque code d’état qui peut être incluse dans le graphique de la ligne et le nombre d’occurrences par seconde pour ce code d’état. Par défaut, une ligne s’affiche pour chacun de ces codes d’état dans le graphique. Toutefois, vous pouvez choisir de surveiller uniquement les codes d’état qui ont une signification spéciale pour votre configuration CDN. Pour ce faire, les codes d’état de votre choix et décochez toutes les autres options, puis cliquez sur **Actualiser le graphique**. 

Vous pouvez masquer temporairement les données enregistrées pour un code de statut particulier.  Dans la légende juste en dessous du graphique, cliquez sur le code d’état que vous souhaitez masquer. Le code d’état est masqué immédiatement à partir du graphique. Cliquez de nouveau sur ce code d’état provoque cette option s’affiche à nouveau.

## <a name="cache-statuses"></a>Statuts de cache

![Graphique de statuts de cache](./media/cdn-real-time-stats/cdn-cache-status.png)

Le graphique de **Statuts de Cache** indique la fréquence à laquelle certains types de statuts de cache sont produisent sur l’intervalle de temps sélectionné. 

> [AZURE.TIP]  Pour obtenir une description de chaque option de code d’état du cache, consultez [Codes d’état du Cache Azure CDN](https://msdn.microsoft.com/library/mt759237.aspx).

Une liste des codes d’état de cache s’affiche directement au-dessus du graphique. Cette liste indique chaque code d’état qui peut être incluse dans le graphique de la ligne et le nombre d’occurrences par seconde pour ce code d’état. Par défaut, une ligne s’affiche pour chacun de ces codes d’état dans le graphique. Toutefois, vous pouvez choisir de surveiller uniquement les codes d’état qui ont une signification spéciale pour votre configuration CDN. Pour ce faire, les codes d’état de votre choix et décochez toutes les autres options, puis cliquez sur **Actualiser le graphique**. 

Vous pouvez masquer temporairement les données enregistrées pour un code de statut particulier.  Dans la légende juste en dessous du graphique, cliquez sur le code d’état que vous souhaitez masquer. Le code d’état est masqué immédiatement à partir du graphique. Cliquez de nouveau sur ce code d’état provoque cette option s’affiche à nouveau.

## <a name="connections"></a>Connexions

![Graphique des connexions](./media/cdn-real-time-stats/cdn-connections.png)

Ce graphique indique le nombre de connexions ont été établi à vos serveurs edge. Chaque demande pour une immobilisation qui passe par le biais de nos résultats CDN dans une connexion.

## <a name="next-steps"></a>Étapes suivantes

- Obtenez des [alertes en temps réel dans Azure CDN](cdn-real-time-alerts.md)
- Aller plus loin avec [les états HTTP avancés](cdn-advanced-http-reports.md)
- Analyser les [modèles d’utilisation](cdn-analyze-usage-patterns.md)

