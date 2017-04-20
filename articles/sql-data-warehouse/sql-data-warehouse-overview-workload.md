<properties
   pageTitle="Charge d’entrepôt de données"
   description="L’élasticité du SQL Data Warehouse vous permet de développer, réduire ou suspendre la puissance de calcul à l’aide d’une échelle mobile d’unités d’entrepôt de données (DWUs). Cet article explique les métriques d’entrepôt de données et leur relation avec les DWUs. "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/25/2016"
   ms.author="barbkess;mausher;jrj;sonyama"/>


# <a name="data-warehouse-workload"></a>Charge d’entrepôt de données
Une charge de travail de magasin de données fait référence à toutes les opérations qui s’écouler par rapport à un magasin de données. La charge de travail de magasin de données englobe l’intégralité du processus de chargement de données dans l’entrepôt, l’analyse et reporting sur le data warehouse, gestion des données dans le data warehouse et exportation de données à partir de l’entrepôt de données. L’étendue et ces composants sont souvent proportionnelles avec le niveau de maturité de l’entrepôt de données.


## <a name="new-to-data-warehousing"></a>Nouvelle d’entreposage de données ?
Un data warehouse est une collection de données qui sont chargé à partir d’une ou plusieurs sources de données et sont utilisées pour effectuer des tâches telles que l’analyse de données et de rapports intelligence d’entreprise.

Entrepôts de données se caractérisent par des requêtes d’analyse le plus grand nombre de lignes, de grandes plages de données et peuvent retourner des résultats relativement importants aux fins d’analyse et de reporting. Les data warehouses sont également caractérisés par des charges de données relativement importante par rapport aux petites insertions/modifications/suppressions au niveau des transactions.

- Un entrepôt de données offre les meilleures performances lorsque les données sont stockées d’une manière qui optimise les requêtes nécessitant un grand nombre de lignes ou de grandes plages de données d’analyse. Ce type d’analyse fonctionne mieux lorsque les données sont stockées et une fouille par colonnes, au lieu de lignes.

>[AZURE.NOTE] L’index en mémoire columnstore, qui utilise le stockage de la colonne, fournit jusqu'à 10 fois les gains de compression et 100 x requête des gains de performances sur les arborescences binaires traditionnelles pour les requêtes de création de rapports et analytique. Nous considérons columnstore index comme la norme pour le stockage et l’analyse des données de grande taille dans un data warehouse.

- Un data warehouse a des exigences différentes à un système qui permet d’optimiser pour les transactions en ligne (OLTP) de traitement. Le système OLTP contient de nombreux insérer, mettre à jour et supprimer des opérations. Ces opérations de recherche sur des lignes spécifiques de la table. Table de recherche fonctionnent mieux si les données sont stockées sous forme de ligne par ligne. Les données peuvent être triées et rapidement une division de recherche pour mieux régner approche appelée une recherche binaire de l’arborescence ou btree.


## <a name="data-loading"></a>Chargement de données
Chargement de données est une partie importante de la charge de travail de magasin de données. Les entreprises ont généralement un système OLTP occupé qui suit les modifications tout au long de la journée lorsque les clients génèrent des transactions commerciales. Périodiquement, souvent la nuit pendant une période de maintenance, les transactions sont déplacées ou copiées vers le data warehouse. Une fois les données dans le data warehouse, les analystes peuvent effectuer une analyse et prendre des décisions sur les données.

- En règle générale, le processus de chargement est appelé ETL pour extraction, transformation et chargement. Généralement, les données doivent être transformées afin qu’il soit cohérent avec d’autres données dans le data warehouse. Auparavant, les entreprises utilisé des serveurs dédiés ETL pour effectuer les transformations. Maintenant, avec ce type de traitement rapide massivement parallèle, vous pouvez charger des données dans l’entrepôt de données SQL tout d’abord et ensuite effectuer les transformations. Ce processus est appelé extrait, la charge et transformation (ELT) et devient une nouvelle norme pour la charge de travail de magasin de données.

> [AZURE.NOTE] Avec 2016 de SQL Server, vous pouvez désormais effectuer analytique en temps réel sur une table OLTP. Il ne remplace pas la nécessité d’un entrepôt de données stocker et analyser les données, mais il ne fournit pas un moyen d’effectuer l’analyse en temps réel.

### <a name="reporting-and-analysis-queries"></a>Requêtes de Reporting et d’analyse
Requêtes de Reporting et d’analyse sont souvent classés en petite, moyenne et grande selon un certain nombre de critères, mais généralement, il est basé sur le temps. Dans la plupart des magasins de données, il existe une charge de travail mixte d’exécution rapide et les requêtes d’exécution longue. Dans chaque cas, il est important de déterminer ce mélange et déterminer sa fréquence (horaire, quotidienne, fin de mois, fin de trimestre, etc.). Il est important de comprendre que la charge de travail mixte requête associée à l’accès concurrentiel, vous amener à bonne planification de capacité pour un entrepôt de données.

- Planification de la capacité peut être une tâche complexe pour une charge de travail mixte de requête, en particulier lorsque vous avez besoin d’un long délai pour ajouter de la capacité pour l’entrepôt de données. SQL Data Warehouse supprime l’urgence de la planification de la capacité dans la mesure où vous pouvez développer et réduire la capacité de traitement à tout moment et depuis le stockage et de capacité de traitement sont dimensionnés de façon indépendante.

### <a name="data-management"></a>Gestion des données
Gestion des données sont importante, en particulier lorsque vous savez que vous allez manquer d’espace disque dans un avenir proche. Généralement, les entrepôts de données divisent les données dans des plages explicites, qui sont stockés comme des partitions d’une table. Tous les produits à base de SQL Server vous permettent de déplacer des partitions dans la table. Cette partition de commutation permet de transférer les données anciennes vers un stockage moins coûteux et conserver les données les plus récentes disponibles sur le stockage en ligne.

- ColumnStore index prend en charge les tables partitionnées. Pour columnstore les index, les tables partitionnées sont utilisés pour la gestion des données et l’archivage. Pour les tables stockées ligne par ligne, les partitions ont un rôle plus important dans les performances des requêtes.  

- PolyBase joue un rôle important dans la gestion des données. À l’aide de PolyBase, vous avez l’option d’archivage des données anciennes vers un stockage blob Hadoop ou Azure.  Cela offre beaucoup d’options dans la mesure où les données sont toujours en ligne.  Il peut prendre plus de temps à récupérer les données d’Hadoop, mais le compromis entre temps de récupération peut compenser le coût de stockage.

### <a name="exporting-data"></a>Exportation de données
Une façon de rendre les données disponibles pour les rapports et d’analyse est pour envoyer des données à partir de l’entrepôt de données aux serveurs dédiés pour l’exécution de rapports et analyse. Ces serveurs sont appelés des data marts. Vous pouvez, par exemple, avant de traiter les données de rapport et puis l’exporter à partir de l’entrepôt de données pour un grand nombre de serveurs dans le monde entier, pour le rendre disponible pour les clients et les analystes.

- Pour générer des rapports, chaque nuit vous pourriez remplir des serveurs de rapports en lecture seule avec un instantané des données quotidiennes. Cela donne une bande passante de clients tout en réduisant les besoins en ressources calcul sur le data warehouse. À partir d’un aspect de la sécurité, des data marts permettent de réduire le nombre d’utilisateurs qui ont accès à l’entrepôt de données.
- Pour analytique, vous pouvez créer un cube d’analyse sur le data warehouse et exécuter l’analyse par rapport à l’entrepôt de données, ou des données de processus et exporter vers le serveur d’analyse pour plus analytique.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez un peu sur SQL Data Warehouse, découvrez comment rapidement [créer un entrepôt de données SQL][] et de [charger des exemples de données][].

<!--Image references-->

<!--Article references-->
[charger des exemples de données]: ./sql-data-warehouse-load-sample-databases.md
[Créez un entrepôt de données SQL]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
