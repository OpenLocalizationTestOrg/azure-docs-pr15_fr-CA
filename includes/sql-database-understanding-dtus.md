L’unité de Transaction de base de données (DTU) est l’unité de mesure de base de données SQL qui représente la puissance relative des bases de données basé sur une mesure réelle : la transaction de base de données. Nous avons pris un ensemble d’opérations qui sont typiques pour une transaction en ligne traitement (OLTP) demande et puis mesuré le nombre de transactions peut être effectué par seconde sous entièrement chargé des conditions (qui est la version courte, vous pouvez lire le rentrer dans les détails dans la [vue d’ensemble du banc d’essai](../articles/sql-database/sql-database-benchmark-overview.md)). 

Par exemple, une base de données Premium P11 1750 DTUs fournit 350 x DTU plus calculer la puissance à une base de données de base avec 5 DTUs. 

![Présentation de la base de données SQL : unique DTUs de base de données par couche et de niveau.](./media/sql-database-understanding-dtus/single_db_dtus.png)

>[AZURE.NOTE] Si vous faites migrer une base de données SQL Server existante, vous pouvez utiliser un outil tiers, [La calculatrice de DTU de base de données SQL Azure](http://dtucalculator.azurewebsites.net/), pour obtenir une estimation du niveau de performance et de service de couche de que votre base de données peut nécessiter de base de données de SQL Azure.

### <a name="dtu-vs-edtu"></a>DTU et eDTU

Le DTU pour les bases de données unique se traduit directement par l’eDTU élastique bases de données. Par exemple, une base de données dans une grappe de base de la base de données élastique offre jusqu'à 5 eDTUs. C’est la même performance qu’une seule base de données de base. La différence est que la base de données élastique ne consomme toute eDTUs à partir du pool jusqu'à ce qu’il ait à. 

![Présentation de la base de données SQL : élastiques pools par niveau.](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Aide d’un exemple simple. Mettre un base élastique avec 1000 DTUs et drop 800 bases de données qu’il contient. Tant que 200 uniquement les bases de données 800 sont utilisées à tout moment dans le temps (DTU 5 X 200 = 1000), la capacité du pool n’a, et ne sont pas dégrader les performances de la base de données. Pour plus de clarté, cet exemple est simplifié. Le calcul réel est un peu plus complexe. Le portail effectue les calculs à votre place et fait une recommandation, fondée sur l’utilisation de la base de données historiques. Reportez-vous à la section [Considérations de prix et de performances pour un pool d’élasticité de la base de données](../articles/sql-database/sql-database-elastic-pool-guidance.md) pour savoir comment fonctionnent les recommandations ou à effectuer des calculs vous-même. 
