<properties
   pageTitle="Vue d’ensemble des tables SQL Data Warehouse | Microsoft Azure"
   description="Mise en route avec les Tables de Data Warehouse SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/04/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="overview-of-tables-in-sql-data-warehouse"></a>Vue d’ensemble des tables SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Mise en route de la création de tables SQL Data Warehouse est simple.  La syntaxe [CREATE TABLE][] base suit la syntaxe courante vous serez probablement déjà familiarisés avec d’autres bases de données.  Pour créer une table, il vous suffit d’un nom à votre table, le nom de vos colonnes et définir les types de données pour chaque colonne.  Si vous avez créer des tables dans les autres bases de données, cela devrait vous être très familier.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

L’exemple ci-dessus crée une table nommée Customers avec deux colonnes, FirstName et LastName.  Chaque colonne est définie avec un type de données de VARCHAR(25), ce qui limite les données à 25 caractères.  Ces attributs fondamentaux d’une table, ainsi que d’autres, sont essentiellement le même que d’autres bases de données.  Types de données définis pour chaque colonne et garantissant l’intégrité de vos données.  Les index peuvent être ajoutés pour améliorer les performances en réduisant les e/s.  Partitionnement peut être ajouté pour améliorer les performances lorsque vous avez besoin de modifier les données.

[Renommer] [ RENAME] une table SQL Data Warehouse se présente comme suit :

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Tables distribués

Un nouvel attribut fondamental introduit par des systèmes distribués tels que SQL Data Warehouse est la **colonne distribution**.  La colonne distribution est très bien qu’il semble.  Il s’agit de la colonne qui détermine le mode de distribution, ou de répartition, vos données en arrière-plan.  Lorsque vous créez une table sans spécifier la colonne de la distribution, la table est automatiquement distribuée à l’aide de la **fonction Round robin**.  Tandis que les tables de répétition alternée peuvent être suffisants dans certains scénarios, la définition de colonnes de distribution peut réduire considérablement déplacement de données au cours des requêtes, optimisant ainsi les performances.  Reportez-vous à la section [distribution d’une Table de][répartir] en savoir plus sur la sélection d’une colonne de distribution.

## <a name="indexing-and-partitioning-tables"></a>L’indexation et le partitionnement de tables

Deviennent plus avancés à l’aide de SQL Data Warehouse et à optimiser les performances, vous souhaiterez en savoir plus sur la création de Table.  Pour plus d’informations, consultez les articles sur [Les Types de données de Table de][Types de données], [distribution d’une Table de][distribution], [l’indexation d’un tableau]d'[Index] et [partitionnement d’une Table]de[Partition].

## <a name="table-statistics"></a>Statistiques de la table

Des statistiques sont un extrêmement important pour obtenir les meilleures performances de votre magasin de données SQL.  Étant donné que SQL Data Warehouse ne pas encore crée automatiquement et statistiques de mise à jour pour vous, comme vous pouvez avoir en droit d’attendre dans la base de données SQL Azure, lisez notre article sur les [statistiques][] peuvent être un des principaux articles vous lire pour garantir que vous obtiendrez les meilleures performances de vos requêtes.

## <a name="temporary-tables"></a>Tables temporaires

Les tables temporaires sont des tables qui n’existent pour la durée de l’ouverture de session et ne peuvent pas être vus par d’autres utilisateurs.  Les tables temporaires peuvent être un bon moyen pour empêcher les autres utilisateurs de voir les résultats temporaires et également réduire la nécessité d’un nettoyage.  Dans la mesure où les tables temporaires utilisent également le stockage local, ils peuvent proposer des performances plus rapides pour certaines opérations.  Consultez les articles[temporaires] de la [Table temporaire]pour plus de détails sur les tables temporaires.

## <a name="external-tables"></a>Tables externes

Tables externes, également connu sous le nom des tables Polybase, sont les tables qui peuvent être interrogées à partir de SQL Data Warehouse point à des données externes à partir de l’entrepôt de données SQL.  Par exemple, vous pouvez créer une table externe qui pointe vers les fichiers dans le stockage Blob Azure.  Pour plus d’informations sur la procédure pour créer et interroger une table externe, consultez [chargement de données avec Polybase][].  

## <a name="unsupported-table-features"></a>Fonctions de tableau non pris en charge

Tandis que SQL Data Warehouse contient de nombreuses fonctionnalités même table offertes par d’autres bases de données, il existe certaines fonctionnalités qui ne sont pas pris en charge.  Voici une liste de quelques-unes des fonctionnalités de table qui ne sont pas pris en charge.

| Fonctionnalités non prises en charge |
| --- |
|[Propriété Identity][] (voir la section [Affectation d’une solution de clé de substitution][])|
|Clé primaire, les clés étrangères, Unique et de la vérification [Des contraintes de Table][]|
|[Index uniques][]|
|[Colonnes calculées][]|
|[Colonnes SPARSE][]|
|[Types définis par l’utilisateur][]|
|[Séquence][]|
|[Déclencheurs][]|
|[Vues indexées][]|
|[Synonymes][]|

## <a name="table-size-queries"></a>Requêtes de taille de tableau

Une méthode simple pour identifier l’espace et lignes utilisées par une table dans chacun des 60 distributions, est d’utiliser [DBCC PDW_SHOWSPACEUSED][].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Toutefois, à l’aide des commandes DBCC peut être assez limitée.  Vues de gestion dynamique (DMV) vous permettra de voir beaucoup plus en détail comme vous donnent beaucoup plus grand contrôle sur les résultats de la requête.  Commencez par la création de cette vue, qui sera désignée par de nombreux exemples dans cet article et autres articles.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Espace de table récapitulative

Cette requête renvoie les lignes et l’espace par table.  Il s’agit d’une requête très pour voir quelles tables sont des tables plus grandes et qu’elles soient alternée ou hachage distribué.  Pour les tables de hachage distribuée, il montre également la colonne distribution.  Dans la plupart des cas, vos tables les plus volumineuses doivent être distribué avec un index ordonné en clusters columnstore de hachage.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Espace disque logique par type de distribution

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Espace disque logique par type d’index

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Espace de distribution Résumé

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles sur [Les Types de données de Table de][Types de données], [distribution d’une Table de][distribution], [l’indexation d’un tableau]d'[Index], [partitionnement d’une Table]de[Partition], [Mise à jour des statistiques de Table][statistiques] et[temporaire] [Des Tables temporaires].  Pour plus d’informations sur les méthodes conseillées, consultez méthodes [Conseillées entrepôt de données SQL][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble]: ./sql-data-warehouse-tables-overview.md
[Types de données]: ./sql-data-warehouse-tables-data-types.md
[Distribuer]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[Temporaire]: ./sql-data-warehouse-tables-temporary.md
[Entrepôt de données SQL meilleures pratiques]: ./sql-data-warehouse-best-practices.md
[Charger les données avec Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CRÉER TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Propriété Identity]: https://msdn.microsoft.com/library/ms186775.aspx
[Affectation de solution clé de substitution]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[Contraintes de table]: https://msdn.microsoft.com/library/ms188066.aspx
[Colonnes calculées]: https://msdn.microsoft.com/library/ms186241.aspx
[Colonnes SPARSE]: https://msdn.microsoft.com/library/cc280604.aspx
[Types définis par l’utilisateur]: https://msdn.microsoft.com/library/ms131694.aspx
[Séquence]: https://msdn.microsoft.com/library/ff878091.aspx
[Déclencheurs]: https://msdn.microsoft.com/library/ms189799.aspx
[Vues indexées]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonymes]: https://msdn.microsoft.com/library/ms177544.aspx
[Index uniques]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
