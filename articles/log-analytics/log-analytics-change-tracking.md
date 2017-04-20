<properties
    pageTitle="Solution Analytique du journal de suivi des modifications | Microsoft Azure"
    description="Vous pouvez utiliser la solution de suivi des modifications de Configuration dans le journal Analytique pour vous aider à identifier facilement les logiciels et les Services Windows les modifications qui se produisent dans votre environnement, l’identification de ces modifications de configuration peut vous aider à identifier des problèmes de fonctionnement."
    services="operations-management-suite"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operations-management-suite"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="change-tracking-solution-in-log-analytics"></a>Modifier la solution de suivi dans le journal Analytique


Vous pouvez utiliser la solution de suivi des modifications de Configuration dans le journal Analytique pour vous aider à identifier facilement les logiciels et les modifications de Services Windows et les modifications de démon Linux qui se produisent dans votre environnement, l’identification de ces modifications de configuration peut vous aider à identifier des problèmes de fonctionnement.

Vous installez la solution pour le type d’agent que vous avez installée de mettre à jour. Modifications de logiciel installé et services Windows sur les serveurs analysés sont lus et puis les données sont envoyées au service journal Analytique dans le nuage pour le traitement. La logique est appliquée pour les données reçues et le service en nuage enregistre les données. Lorsque les modifications sont trouvées, les serveurs avec les modifications figurent dans le tableau de bord de suivi des modifications. En utilisant les informations du tableau de bord de suivi des modifications, vous pouvez facilement voir les modifications qui ont été apportées dans votre infrastructure de serveur.

## <a name="installing-and-configuring-the-solution"></a>L’installation et la configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- Operations Manager est requis pour la solution de suivi des modifications.
- Vous devez disposer d’un agent Operations Manager ou de Windows sur chaque ordinateur où vous voulez surveiller les modifications.
- Ajouter la solution de suivi des modifications dans votre espace de travail OMS en utilisant la procédure décrite dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).  Il n’y a aucune configuration supplémentaire requise.


## <a name="change-tracking-data-collection-details"></a>Modifier les détails de collecte de données de suivi

Suivi des modifications collecte d’inventaire logiciel et les métadonnées de Service Windows à l’aide d’agents que vous avez activés.

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur la manière dont les données sont collectées pour le suivi des modifications.

| plate-forme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|
|Windows|![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png)|![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png)|![N°](./media/log-analytics-change-tracking/oms-bullet-red.png)|            ![N°](./media/log-analytics-change-tracking/oms-bullet-red.png)|![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png)| toutes les heures|

## <a name="use-change-tracking"></a>Utilisez le suivi des modifications

Une fois la solution installée, vous pouvez afficher le résumé des modifications pour les serveurs surveillés à l’aide de la mosaïque de **Suivi des modifications** sur la page de **vue d’ensemble** de l’OMS.

![image de mosaïque de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

Vous pouvez afficher les modifications apportées à votre infrastructure, puis dans l’extraction des détails pour les catégories suivantes :

- Modifications apportées par le type de configuration pour les logiciels et les services Windows
- Modifications de logiciels d’applications et de mises à jour pour serveurs individuels
- Nombre total de modifications de logiciel pour chaque application
- Modifications de service Windows pour les serveurs individuels

![image du tableau de bord de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking01.png)

![image du tableau de bord de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>Pour afficher des modifications type

1. Dans la page **vue d’ensemble** , cliquez sur la mosaïque de **Suivi des modifications** .
2. Sur le tableau de bord **Modifier le suivi** , vérifiez les informations résumées de l’une des lames du type de modification et puis cliquez sur une option pour afficher des informations détaillées le concernant dans la page de **recherche des journaux** .
3. Sur toutes les pages de recherche de journal, vous pouvez afficher les résultats en temps, des résultats détaillés et votre historique de recherche de journal. Vous pouvez également filtrer par facettes pour limiter les résultats.

## <a name="next-steps"></a>Étapes suivantes

- [Recherche de journal de journal Analytique](log-analytics-log-searches.md) permet d’afficher les données de suivi des modifications détaillées.
