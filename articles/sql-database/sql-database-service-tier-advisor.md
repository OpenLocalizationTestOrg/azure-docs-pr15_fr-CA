<properties 
   pageTitle="Tarification des recommandations de niveau de base de données de SQL Azure" 
   description="Lors de la modification de tarification niveaux dans le portail Azure, recommandations de niveau de tarification est fournis recommander le niveau qui correspond le mieux adapté à la charge de travail un existant Azure SQL de base de données en cours d’exécution. Niveaux de tarification décrire le niveau de performances et le niveau de service d’une base de données SQL." 
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
   ms.date="08/08/2016"
   ms.author="sstein"/>

# <a name="sql-database-pricing-tier-recommendations"></a>Recommandations de niveau de prix de base de données SQL

 Recommandations de niveau prix proposer le niveau de service niveau et les performances qui est plus adapté à la charge de travail un existant Azure SQL de base de données en cours d’exécution.

> [AZURE.NOTE] Recommandations de couche tarification sont uniquement disponibles pour les bases de données Web et de l’entreprise et les pools d’élastique de la base de données--et disponible uniquement dans le [portail Azure](https://portal.azure.com/).


Obtenez le tarif des recommandations de couche au cours des tâches suivantes :

- [Modifier le niveau et les performances niveau de service (couche de tarification) d’une base de données SQL](sql-database-scale-up.md)
- [Mise à niveau serveur Azure SQL V12](sql-database-upgrade-server-portal.md)
- Accédez à votre serveur V12. Voir [les recommandations de niveau de tarification de la base de données SQL](sql-database-service-tier-advisor.md).
- [Créer un pool d’élasticité de la base de données](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## <a name="overview"></a>Vue d’ensemble

Le service de base de données SQL analyse les performances actuelles et les exigences de la fonction en évaluant l’utilisation des ressources historiques pour une base de données SQL. En outre, le niveau de service acceptable minimum est déterminé en fonction de la taille de la base de données et les fonctionnalités de activé [la continuité d’activité](sql-database-business-continuity.md) . 

Cette information est analysée et le niveau de performances et de la couche service qui correspond le mieux adapté à la charge de travail typique de la base de données en cours d’exécution et leur gestion qu'est en cours de jeu de fonctionnalités est recommandé.

- Ce service examine les précédente de 15 à 30 jours de données historiques (utilisation des ressources, la taille de la base de données et activité de la base de données) et effectue une comparaison entre la quantité de ressources consommées et les limitations réelles des niveaux de performances et les niveaux de service actuellement disponible.
- Données sont analysées dans les 15 secondes et jeu de résultats de chaque intervalle est classé dans la couche de service existant et niveau de performance qui correspond le mieux adapté pour gérer la charge de travail de ce jeu de résultats.
- Ces 15 secondes échantillons sont puis agrégées dans l’analyse de 15 à 30 jours plus grand et le niveau de service niveau et les performances qui peut optimiser la gestion des 95 % de la charge de travail historique est recommandé.

### <a name="recommendations"></a>Recommandations

Basé sur l’utilisation de votre base de données, il existe actuellement 2 catégories de recommandations que vous peuvent rencontrer :


| Recommandation | Description |
| :--- | :--- |
| Mise à niveau | Mise à niveau vers une nouvelle couche. |
| Non disponible | Une base de données requiert une charge de travail minimale ou environ 35 jours d’activité. Il n’est pas suffisamment de données pour fournir une recommandation valide. |

## <a name="getting-pricing-tier-recommendations"></a>Obtention de recommandations de niveau de prix

Renseignez-vous sur le prix des recommandations de couche en sélectionnant une base de données Web ou d’entreprise existant, cliquez sur **tous les paramètres**, puis cliquez sur **niveau de tarification (échelle de DTUs)**. (Tarification recommandations de couche sont également disponibles lorsque vous [mettez à niveau le SQL Azure le serveur à V12](sql-database-upgrade-server-portal.md).)

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir** > **les bases de données SQL**.
4. De la lame de **bases de données SQL** , cliquez sur la base de données que vous souhaitez voir une recommandation pour :

    ![Sélectionnez la base de données][1]

5. Sur la lame de la base de données, sélectionnez **tous les paramètres** , puis sélectionnez **le niveau de tarification (échelle de DTUs)**.


7. Le **recommandé de tarification niveaux** ouvrir où vous pouvez cliquez sur la couche suggérée et puis cliquez sur **le bouton pour passer à ce niveau** .

    ![Inscrivez-vous pour l’aperçu][4]

8. Éventuellement, cliquez sur **Afficher les détails de l’utilisation** pour ouvrir la lame de **Détails de recommandation de niveau de tarification** qui permet d’afficher le niveau recommandé pour la base de données, une comparaison des fonctionnalités entre les niveaux actuels et recommandés et un graphique de l’analyse de l’utilisation de ressources historiques.

    ![Inscrivez-vous pour l’aperçu][5]



## <a name="summary"></a>Résumé

Tarification recommandations de couche fournissent une expérience automatisée pour la collecte des données de télémétrie pour chaque base de données SQL et de recommander la meilleure service/performances de niveau niveau combinaison en fonction des besoins de performances réelles d’une base de données et les exigences de la fonction. Sur la lame de paramètres cliquez sur **niveau de tarification (échelle de DTUs)** pour afficher les prix des recommandations de couche pour les bases de données Web et de l’entreprise.



## <a name="next-steps"></a>Étapes suivantes

Selon les détails de votre base de données spécifique, effectuer une mise à niveau ou le déclassement de généralement ne se produit pas immédiatement. Le portail de fournir des notifications comme les transitions de la base de données vers sa nouvelle couche, ou vous pouvez surveiller l’état de mise à niveau en interrogeant l’affichage [sys.dm_operation_status (de base de données SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx) les base de données master du serveur de base de données SQL.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 
