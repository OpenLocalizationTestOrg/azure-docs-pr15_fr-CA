<properties 
   pageTitle="Aperçu de performances de requête de base de données SQL Azure" 
   description="Analyse des performances de requête d’identifie la plupart des requêtes d’utilisation du processeur pour une base de données de SQL Azure." 
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
   ms.date="08/09/2016"
   ms.author="sstein"/>

# <a name="azure-sql-database-query-performance-insight"></a>Aperçu de performances de requête de base de données SQL Azure


Gérer et optimiser les performances des bases de données relationnelles sont une tâche complexe qui nécessite une réelle expertise et investissement en temps. Analyse des performances de requête vous permet de passer moins de temps à résolution des problèmes de performances de la base de données en fournissant les éléments suivants :

- Plus approfondie de vos bases de données (DTU) la consommation des ressources. 
- Les requêtes principales par nombre de CPU/durée/exécution, qui peut potentiellement être ajusté pour améliorer les performances.
- La possibilité de se plonger dans les détails d’une requête, afficher son texte et l’historique de l’utilisation des ressources. 
- Les annotations qui indiquent les actions exécutées par le [Gestionnaire de base de données SQL Azure](sql-database-advisor.md) le réglage des performances  



## <a name="prerequisites"></a>Conditions préalables

- Analyse des performances de requête est disponible uniquement avec V12 de base de données SQL Azure.
- Analyse des performances de requête nécessite que le [Magasin de requête](https://msdn.microsoft.com/library/dn817826.aspx) est active sur votre base de données. Si la banque de la requête ne s’exécute pas, le portail vous invite à activer.

 
## <a name="permissions"></a>Autorisations

Les autorisations de [contrôle d’accès basé sur les rôles](../active-directory/role-based-access-control-configure.md) suivants sont requis pour utiliser l’analyse des performances de requête : 

- Autorisations de **lecteur**, **propriétaire**, **collaborateur**, **Collaborateur de base de données SQL**ou **Collaborateur de SQL Server** sont nécessaires pour afficher les requêtes de beaucoup de ressources principales et les graphiques. 
- Les autorisations **propriétaire**, **collaborateur**, **Collaborateur de base de données SQL**ou **Collaborateur de SQL Server** sont nécessaires pour afficher le texte de la requête.



## <a name="using-query-performance-insight"></a>À l’aide d’aperçu des performances de requête

Analyse des performances de requête est facile à utiliser :

- Ouvrez le [portail Azure](https://portal.azure.com/) et rechercher la base de données que vous souhaitez examiner. 
  - À partir du menu de gauche, sous prise en charge et le dépannage, sélectionnez « Insight performances de requête ».
- Sur le premier onglet, passez en revue la liste des requêtes de consommateurs de ressources principales.
- Sélectionnez une requête individuelle pour afficher ses détails.
- Ouvrez le [Gestionnaire de base de données SQL Azure](sql-database-advisor.md) et vérifiez si les recommandations sont disponibles.
- Utilisez les curseurs ou Zoom sur les icônes pour modifier l’intervalle observé.

    ![tableau de bord performances](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] Quelques heures de données doit être capturé par banque de requête de base de données SQL afin de fournir des analyses des performances de requête. Si la base de données n’a aucune activité ou requête n’a pas été active pendant une période de temps donnée, les graphiques est vides lors de l’affichage de cette période. Vous pouvez activer la banque de la requête à tout moment si elle n’est pas en cours d’exécution.   



## <a name="review-top-cpu-consuming-queries"></a>Examinez les UC supérieur utilisant des requêtes

Dans le [portail](http://portal.azure.com) , effectuez le des opérations suivantes :

1. Accédez à une base de données SQL, cliquez sur **tous les paramètres** > **prise en charge + dépannage** > **insight des performances de requête**. 

    ![Analyse des performances de requête][1]

    La vue supérieure de requêtes s’ouvre et les premières requêtes beaucoup de processeur sont répertoriées.

1. Cliquez sur autour du graphique pour plus d’informations.<br>La première ligne affiche le % DTU global pour la base de données, tandis que les barres affichent % processeur consommé par les requêtes sélectionnées au cours de l’intervalle sélectionné (par exemple, si la **semaine** est sélectionné chaque barre représente un jour).

    ![requêtes principales][2]

    La grille inférieure représente des informations agrégées pour les requêtes visibles.

  - ID de requête – identificateur unique de la requête dans la base de données.
  - UC par requête intervalle observables (dépend de la fonction d’agrégation).
  - Durée par requête (dépend de la fonction d’agrégation).
  - Nombre total d’exécutions d’une requête particulière.

    Activez ou désactivez les requêtes individuelles pour les inclure ou les exclure du graphique à l’aide des cases à cocher.

1. Si vos données deviennent obsolètes, cliquez sur le bouton **Actualiser** .
1. Vous pouvez utiliser les curseurs et les boutons pour modifier l’intervalle d’observation et d’analyser les pics de zoom :  ![paramètres](./media/sql-database-query-performance/zoom.png)
1. Éventuellement, si vous voulez un affichage différent, vous pouvez sélectionner l’onglet **personnalisées** et définir :
  
  - Métrique (CPU, la durée, le nombre d’exécutions)
  - Intervalle de temps (dernières 24 heures, après une semaine, le mois dernier). 
  - Nombre de requêtes.
  - Fonction d’agrégation.

    ![Paramètres](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Affichage des détails d’une requête individuelle

Pour afficher les détails de la requête :

1. Cliquez sur n’importe quelle requête dans la liste des requêtes top.

    ![Détails](./media/sql-database-query-performance/details.png)

1. La vue Détails s’ouvre et le nombre de consommation/durée / l’exécution de requêtes UC est réparti dans le temps.
1. Cliquez sur autour du graphique pour plus d’informations.
  - Graphique supérieur affiche ligne globale % de DTU de base de données, et les barres sont % processeur consommé par la requête sélectionnée.
  - Le deuxième graphique affiche la durée totale par la requête sélectionnée.
  - Graphique du bas indique le nombre total d’exécutions par la requête sélectionnée.
    
    ![Détails de la requête][3]

1. Le cas échéant, utilisez les curseurs, boutons de zoom ou cliquez sur **paramètres** pour personnaliser l’affichage des données de la requête, ou sélectionner une autre période.

## <a name="review-top-queries-per-duration"></a>Passez en revue les requêtes principales par durée

Dans la mise à jour récente de requête performances Insight, nous avons introduit deux nouvelles mesures qui peuvent vous aider à identifier les goulots d’étranglement potentiels : compteur de durée et de l’exécution.<br>

Requêtes d’exécution longue ont le plus de possibilités de verrouillage de ressources plus, bloquant d’autres utilisateurs et limiter l’évolutivité. Ils sont également les mieux adaptées pour l’optimisation.<br>

Pour identifier les requêtes à long terme :

1. Ouvrir l’onglet **personnalisé** dans l’analyse des performances de requête de base de données sélectionnée
1. Modifier les mesures à **durée**
1. Sélectionner le nombre de requêtes et l’intervalle d’observation
1. Sélectionnez la fonction d’agrégation
  - **Somme** additionne tous les temps d’exécution requête au cours de l’intervalle d’observation entier.
  - **Max** recherche des requêtes de temps d’exécution qui a été maximale à intervalle de toute observation.
  - **Avg** trouve la durée moyenne d’exécution de toutes les exécutions de la requête et afficher la partie supérieure de ces moyennes. 

    ![durée de la requête][4]

## <a name="review-top-queries-per-execution-count"></a>Passez en revue les requêtes principales par nombre d’exécutions

Nombre élevé d’exécutions ne peut-être pas affecter de base de données elle-même et l’utilisation de ressources peut être faible, mais globales de l’application peuvent obtenir lente.

Dans certains cas, nombre d’exécutions très élevée peut conduire à augmenter d’aller-retour réseau. Allers-retours affecter considérablement les performances. Ils sont soumis à la latence du réseau et à la latence du serveur en aval. 

Par exemple, de nombreux sites du Web pilotée par des données fortement accéder à la base de données pour chaque demande de l’utilisateur. Alors que la charge sur le serveur de base de données de traitement et de regroupement vous permet, de l’augmentation du trafic de connexion peut nuire aux performances.  Conseils généraux consiste à conserver des allers-retours à un minimum absolu.

Pour identifier fréquemment l’exécution de requêtes de requêtes (« bavardages ») :

1. Ouvrir l’onglet **personnalisé** dans l’analyse des performances de requête de base de données sélectionnée
1. Modifier les mesures à **nombre d’exécutions**
1. Sélectionner le nombre de requêtes et l’intervalle d’observation

    ![nombre de requêtes d’exécution][5]

## <a name="understanding-performance-tuning-annotations"></a>Présentation des annotations de réglage de performances 

Lors de l’exploration de votre charge de travail dans l’analyse des performances de requête, vous pouvez remarquer des icônes avec une ligne verticale sur le graphique.<br>

Ces icônes sont des annotations ; ils représentent des performances affectant les actions exécutées par le [Gestionnaire de base de données SQL Azure](sql-database-advisor.md). Par pointage d’annotation, vous obtenez des informations de base sur l’action :

![annotation de la requête][6]

Si vous souhaitez savoir plus ou d’appliquer la recommandation du Gestionnaire d’accès, cliquez sur l’icône. Détails de l’action s’ouvrira. S’il s’agit d’une recommandation active, que vous pouvez l’appliquer immédiatement à l’aide de la commande.

![Détails de la requête d’annotation][7]

### <a name="multiple-annotations"></a>Plusieurs annotations. ###

Il est possible que, en raison du niveau de zoom, annotations proches les unes des autres seront obtenir réduites en une seule. Il sera représenté par une icône spéciale, cliquant dessus ouvert nouvelle lame où liste de regroupée des annotations s’afficheront.
Corrélation des requêtes et des actions de réglage des performances peut aider à mieux comprendre votre charge de travail. 


##  <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Optimisation de la configuration de la banque de requête pour l’analyse des performances de requête

Lors de votre utilisation de l’analyse des performances de requête, vous pouvez rencontrer les messages de requête banque suivantes :

- « Banque de requête n’est pas correctement configuré sur cette base de données. Cliquez ici pour en savoir plus. »
- « Banque de requête n’est pas correctement configuré sur cette base de données. Cliquez ici pour modifier les paramètres ». 

Ces messages s’affichent habituellement lorsque le magasin de requête n’est pas en mesure de collecter de nouvelles données. 

Premier cas se produit lorsque le magasin de la requête est en lecture seule et les paramètres sont définis de manière optimale. Vous pouvez résoudre ce problème en augmentant la taille du magasin de la requête ou en désactivant des banque de requête.

![bouton de QdS][8]

Deuxième cas se produit lorsque le magasin de requête est désactivé ou que les paramètres ne sont pas définis de façon optimale. <br>Vous pouvez modifier la stratégie de Capture et de rétention et activer le magasin de la requête en exécutant les commandes ci-dessous ou directement à partir de portail :

![bouton de QdS][9]

### <a name="recommended-retention-and-capture-policy"></a>Stratégie de capture et de rétention recommandée

Il existe deux types de stratégies de rétention :

- Taille : si la valeur AUTO il va nettoyer des données automatiquement lorsque près de taille maximale est atteinte.
- Basé sur le temps - par défaut, nous allons définir il à 30 jours, ce qui signifie que, si la requête magasin manque d’espace, il supprimera les informations de requête plues de 30 jours

Capture de stratégie peut être définie sur :

- **Tous les** : capture toutes les requêtes.
- **Automatique** – peu fréquentes et les requêtes dont la durée de compilation et d’exécution non significatif sont ignorés. Seuils pour la durée d’exécution de nombre, de compilation et d’exécution sont déterminés en interne. Il s’agit de l’option par défaut.
- **Aucun** – requête banque arrête la capture des nouvelles requêtes, cependant, statistiques de runtime pour les requêtes déjà capturées sont toujours collectés.
    
Nous vous recommandons de définir toutes les stratégies sur AUTO et une stratégie propre à 30 jours :

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
        
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Augmenter la taille du magasin de la requête. Cela pourrait être exécutée par la connexion à une base de données et l’émission à la suite de requête :

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Appliquer ces paramètres mettra finalement banque de requête collecte les nouvelles requêtes, mais si vous ne voulez pas attendre, vous pouvez désactiver le magasin de requête. 
> [AZURE.NOTE] Exécution de requête suivante va supprimer toutes les informations actuelles dans le magasin de la requête. 


    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Résumé

Analyse des performances de requête vous aide à comprendre l’impact de votre charge de travail de requête et sa relation avec la consommation des ressources de base de données. Grâce à cette fonctionnalité, vous en savoir plus sur l’utilisation de requêtes en haut et d’identifier facilement les corriger avant de devenir un problème.




## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des recommandations supplémentaires sur l’amélioration des performances de votre base de données SQL, cliquez sur les [recommandations](sql-database-advisor.md) sur la blade **d’Analyse des performances de requête** .

![Conseiller de performances](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

