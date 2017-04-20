<properties
   pageTitle="Distribué les données et les options de table pour les systèmes massivement parallèle de traitement (MPP) de SQL Data Warehouse et Parallel Data Warehouse | Microsoft Azure"
   description="Découvrez comment les données sont distribuées massivement parallèle de traitement (MPP) et les options pour la distribution de tables dans l’entrepôt de données SQL Azure et Parallel Data Warehouse."
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
   ms.date="10/10/2016"
   ms.author="barbkess"/>


# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Données distribuées et distribuées tables pour massivement parallèle de traitement (MPP)

Découvrez comment les données de l’utilisateur sont distribuées dans le magasin de données SQL Azure et Parallel Data Warehouse, qui sont des systèmes de massivement parallèle de traitement (MPP) de Microsoft. Conception du data warehouse pour utiliser efficacement des données distribuées vous aide à réaliser les avantages de l’architecture MPP de traitement de requête. Certains choix de conception de base de données peut avoir un impact significatif sur l’amélioration des performances des requêtes.  

>[AZURE.NOTE] Azure SQL Data Warehouse et Parallel Data Warehouse utilisent la même conception massivement parallèle de traitement (MPP), mais ils ont quelques différences en raison de la plate-forme sous-jacente. SQL Data Warehouse est une plate-forme en tant que Service (PaaS) qui s’exécute sur Azure. Parallel Data Warehouse s’exécute sur Analytique plate-forme système (points d’accès) qui est une solution matérielle-logicielle en local qui s’exécute sur Windows Server.

## <a name="what-is-distributed-data"></a>Quelles sont les données distribuées ?

Dans le magasin de données SQL et Parallel Data Warehouse, données distribuées fait référence aux données utilisateur sont stockées dans plusieurs emplacements sur le système MPP. Chacun de ces emplacements fonctionne en tant que stockage indépendant et unité de traitement qui exécute des requêtes sur sa partie des données. Données distribuées sont indispensable à l’exécution de requêtes en parallèle pour obtenir des performances élevées pour les requêtes.

Pour distribuer des données, le data warehouse attribue chaque ligne d’une table utilisateur à un seul endroit.  Vous pouvez distribuer des tables avec une méthode de distribution de hachage ou une méthode de répétition alternée. La méthode de distribution est spécifiée dans l’instruction CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Tables de hachage distribué
  
Le diagramme suivant illustre comment complet (table non distribuées) est stocké sous la forme d’une table de hachage distribuée. Une fonction déterministe affecte à chaque ligne appartiennent à une distribution. Dans la définition de table, une des colonnes est désignée comme colonne de distribution. La fonction de hachage utilise la valeur dans la colonne distribution à affecter chaque ligne d’une distribution.

Il existe des considérations de performances pour la sélection d’une colonne de la distribution, tels que la distinction, d’inclinaison de données et les types de requêtes s’exécutent sur le système.
  
![Table de distribués] (media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Table de distribués")  
  
-   Chaque ligne appartient à une distribution.  
  
-   Un algorithme de hachage déterministe attribue à chaque ligne d’une distribution.  
  
-   Le nombre de lignes de tableau par distribution varie comme indiqué par les différentes tailles de tables.

## <a name="round-robin-distributed-tables"></a>Tables distribuées alternée

Une table distribuée round-robin répartit les lignes en assignant chaque ligne d’une distribution de manière séquentielle. Il est rapide charger des données dans une table de répétition alternée, mais les performances de la requête peuvent être plus lent.  Jointure d’une table de répétition alternée généralement nécessite le remaniement de lignes pour permettre à la requête produire un résultat précis, ce qui prend du temps.

## <a name="distributed-storage-locations-are-called-distributions"></a>Emplacements de stockage distribués sont appelés des distributions

Chaque emplacement distribué est appelé une distribution. Lorsqu’une requête s’exécute en parallèle, chaque distribution exécute une requête SQL sur sa partie des données. SQL Data Warehouse utilise de la base de données SQL pour exécuter la requête. Parallel Data Warehouse utilise SQL Server pour exécuter la requête. Cette conception de l’architecture de type shared-nothing est essentielle pour la réalisation de l’informatique parallèle horizontale.

### <a name="can-i-view-the-distributions"></a>Afficher les distributions ?

Chaque distribution possède un ID de distribution et est visible dans les vues système qui se rapportent à l’entrepôt de données SQL et Parallel Data Warehouse. Vous pouvez utiliser l’ID de distribution pour résoudre les problèmes de performances des requêtes et autres problèmes. Pour obtenir la liste des vues du système, consultez la [vue système MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Différence entre une distribution et un nœud de calcul

Une distribution est l’unité de base pour le stockage de données distribuées et de traitement des requêtes en parallèle. Distributions sont regroupées dans les nœuds de calcul. Chaque nœud de calcul suit une ou plusieurs distributions.  

-   Système de plate-forme Analytique utilise les nœuds de calcul comme un composant central des fonctionnalités matérielles architecture et horizontale. Il utilise toujours huit distributions par nœud de calcul, comme illustré dans le schéma pour les tables de hachage distribué. Le nombre de nœuds de calcul et par conséquent le nombre de distributions, sont déterminés par le nombre de nœuds de calcul que vous achetez pour la solution matérielle-logicielle. Par exemple, si vous achetez huit nœuds de calcul, vous obtenez des 64 distributions (8 nœuds x 8 distributions/nœud de calcul). 

-   Entrepôt de données SQL a un nombre fixe de 60 distributions et un nombre flexible de nœuds de calcul. Les nœuds de calcul sont implémentés avec les ressources de calcul et de stockage Azure. Le nombre de nœuds de calcul peut varier en fonction de la charge de travail de service back-end et de la capacité informatique (DWUs) que vous spécifiez pour l’entrepôt de données. Lorsque le nombre de nœuds de calcul est modifiée, le nombre de distributions par nœud de calcul change également. 

### <a name="can-i-view-the-compute-nodes"></a>Puis-je afficher des nœuds de calcul ?

Chaque nœud de calcul possède un ID de nœud et est visible dans les vues système qui se rapportent à l’entrepôt de données SQL et Parallel Data Warehouse.  Vous pouvez voir le nœud de calcul en recherchant la colonne node_id : dans les vues système dont les noms commencent par sys.pdw_nodes. Pour obtenir la liste des vues du système, consultez la [vue système MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="Replicated"></a>Réplication de Tables pour l’entrepôt de données en parallèle 
  
S’applique à : entrepôt de données en parallèle

En plus d’utiliser les tables distribuées, Parallel Data Warehouse propose une option de réplication des tables. Une *table répliquée* est une table qui est stockée dans son intégralité sur chaque nœud de calcul. Réplication d’une table supprime la nécessité de transférer ses lignes de table entre les nœuds de calcul avant d’utiliser la table dans une jointure ou une agrégation. Les tables répliquées ne sont réalisables avec les tables de petite taille en raison de l’espace de stockage supplémentaire nécessaire pour stocker la table complète sur chaque nœud de calcul.  
  
Le diagramme suivant montre une table répliquée qui est stockée sur chaque nœud de calcul. La table répliquée est stockée sur tous les disques affectés pour le nœud de calcul. Cette stratégie de disque est implémentée à l’aide de groupes de fichiers SQL Server.  
  
![Table de répliqué] (media/sql-data-warehouse-distributed-data/replicated-table.png "Table de répliqué") 
  
## <a name="next-steps"></a>Étapes suivantes
  
Pour utiliser efficacement les tables distribués, consultez [tables de distribution dans l’entrepôt de données SQL](sql-data-warehouse-tables-distribute.md)  
  



  
