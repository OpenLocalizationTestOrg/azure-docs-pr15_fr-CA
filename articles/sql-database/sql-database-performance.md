<properties 
   pageTitle="Analyse de performances de base de données SQL Azure | Microsoft Azure" 
   description="La base de données de SQL Azure propose des outils de performance pour vous aider à identifier les zones qui peuvent améliorer les performances de la requête en cours." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="07/19/2016"
   ms.author="sstein"/>

# <a name="sql-database-performance-insight"></a>Analyse de performances de base de données SQL

Une base de données SQL Azure fournit des outils pour vous aider à identifier et à améliorer les performances de vos bases de données en fournissant des recommandations et des actions intelligentes de réglage des performances. 

1. Accédez à votre base de données dans le [Portail Azure](http://portal.azure.com) et cliquez sur **tous les paramètres** > **performances **  >  **vue d’ensemble** pour ouvrir la page des **performances** . 


2. Cliquez sur **recommandations** pour ouvrir le [Gestionnaire de base de données SQL](#sql-database-advisor), cliquez sur **requêtes** pour ouvrir [l’Aperçu des performances de requête](#query-performance-insight).

    ![Afficher les performances](./media/sql-database-performance/entries.png)



## <a name="performance-overview"></a>Vue d’ensemble des performances

**Vue d’ensemble** ou sur la **Performance** de mosaïque vous redirigera vers le tableau de bord de performances pour votre base de données. Cette vue fournit un récapitulatif des performances de votre base de données et vous aide à l’optimisation des performances et résolution des problèmes. 

![Performances](./media/sql-database-performance/performance.png)

- La mosaïque de **recommandations** détaille les recommandations pour votre base de données de réglage (recommandations des 3 premiers sont affichées s’il y a plusieurs). Cliquez sur cette mosaïque pour accéder au **Gestionnaire de base de données SQL**. 
- La mosaïque de **l’activité de réglage** fournit un résumé de la valeur en cours et terminées dans le réglage des actions pour votre base de données, ce qui vous donne un aperçu rapide dans l’historique de l’activité de réglage. Cliquez sur cette mosaïque pour accéder à l’affichage de l’historique de réglage complète pour votre base de données.
- La mosaïque de **réglage automatique de la** montre la configuration de réglage automatique pour votre base de données (les actions de réglage sont configurées pour être automatiquement appliquées à votre base de données). Cliquez sur cette mosaïque pour ouvrir la boîte de dialogue de configuration d’automation.
- La mosaïque de **requêtes de base de données** affiche le résumé des performances de requête pour votre base de données (global DTU utilisation et haut ressource consommant des requêtes). Cliquez sur cette mosaïque pour accéder à **l’Analyse des performances de requête**.



## <a name="sql-database-advisor"></a>Gestionnaire de base de données SQL


[Gestionnaire de base de données SQL](sql-database-advisor.md) fournit des recommandations de réglage intelligentes qui peuvent améliorer les performances de votre base de données. 

- Recommandations qui indexe à créer ou à supprimer (et à appliquer des recommandations d’index automatiquement sans aucune intervention de l’utilisateur et d’annuler automatiquement les recommandations qui ont un impact négatif sur les performances).
- Recommandations lors de problèmes de schéma sont identifiés dans la base de données.
- Recommandations lors de requêtes peuvent tirer parti des requêtes paramétrées.




## <a name="query-performance-insight"></a>Analyse des performances de requête

[Analyse des performances de requête](sql-database-query-performance.md) vous permet de passer moins de temps à résolution des problèmes de performances de la base de données en fournissant :

- Plus approfondie de vos bases de données (DTU) la consommation des ressources. 
- UC supérieur utilisant des requêtes, ce qui peuvent potentiellement être ajustés pour améliorer les performances. 
- La possibilité de se plonger dans les détails d’une requête. 


## <a name="additional-resources"></a>Ressources supplémentaires

- [Conseils pour les performances de la base de données SQL Azure des bases de données unique](sql-database-performance-guidance.md)
- [Quand un pool élastique de la base de données doit être utilisé ?](sql-database-elastic-pool-guidance.md)