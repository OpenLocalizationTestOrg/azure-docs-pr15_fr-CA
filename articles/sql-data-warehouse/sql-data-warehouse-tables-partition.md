<properties
   pageTitle="Partitionnement de tables SQL Data Warehouse | Microsoft Azure"
   description="Mise en route de partitionnement de table dans le magasin de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="partitioning-tables-in-sql-data-warehouse"></a>Partitionnement de tables SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Le partitionnement est pris en charge sur tous les types de table SQL Data Warehouse ; y compris columnstore ordonné en clusters, index ordonné en clusters et segment de mémoire.  Le partitionnement est également pris en charge sur tous les types de distribution, y compris de hachage ou répétition alternée distribués.  Permet le partitionnement vous permet de diviser vos données en petits groupes de données et dans la plupart des cas, le partitionnement êtes effectuée sur une colonne de dates.

## <a name="benefits-of-partitioning"></a>Avantages du partitionnement

Le partitionnement peut bénéficier des performances de maintenance et de requête de données.  Si il bénéficie à la fois ou une seule est dépendant de la façon dont les données sont chargées et si la même colonne peut être utilisée pour les deux fonctions, dans la mesure où le partitionnement ne peut être effectué que sur une seule colonne.

### <a name="benefits-to-loads"></a>Avantages pour les charges

Le principal avantage du partitionnement dans SQL Data Warehouse est d’améliorer l’efficacité et les performances de chargement de données par l’utilisation de la suppression de la partition, le changement et la fusion.  Dans la plupart des cas, les données sont partitionnées sur une colonne de date qui est étroitement liée à la séquence dans laquelle les données sont chargées dans la base de données.  Un des avantages majeurs de l’utilisation de partitions pour gérer les données il l’évitement de journalisation des transactions.  Tandis que simplement d’insertion, de mise à jour ou de suppression de données peut être l’approche la plus simple, avec un peu de réflexion et d’efforts, en utilisant le partitionnement pendant le processus de chargement peut sensiblement améliorer les performances.

Changement de partition peut servir pour rapidement supprimer ou remplacer une section d’un tableau.  Par exemple, une table de faits de vente peut contenir uniquement les données pour les derniers mois de 36.  À la fin de chaque mois, mois de données de ventes plus ancien est supprimé de la table.  Ces données pourraient être supprimées à l’aide d’une instruction delete pour supprimer les données pour le mois plus ancien.  Toutefois, la suppression d’une grande quantité de données de ligne par ligne avec une instruction delete peut prendre beaucoup de temps, ainsi que créer le risque de transactions volumineuses qui peut prendre du temps pour restaurer si quelque chose se passe mal.  Une approche plus efficace est simplement de supprimer la partition la plus ancienne des données.  Suppression des lignes individuelles où peut prendre des heures, la suppression d’une partition complète peut prendre secondes.

### <a name="benefits-to-queries"></a>Avantages pour les requêtes

Le partitionnement peut également servir pour améliorer les performances de la requête.  Si une requête s’applique un filtre sur une colonne partitionnée, cela peut limiter l’analyse uniquement les partitions de qualification qui peut être un sous-ensemble beaucoup plus petit des données, évitant ainsi une analyse de table complet.  Avec l’introduction de l’index de columnstore en cluster, les avantages de l’élimination de prédicat sont moins avantageux, mais dans certains cas, il peut être un avantage pour les requêtes.  Par exemple, si la table de faits de vente est partitionnée en utilisant le champ de date de vente de 36 mois, interroge ensuite ce filtre sur la date de vente peut doit effectuer des recherches dans les partitions qui ne correspondent pas au filtre.

## <a name="partition-sizing-guidance"></a>Guide de dimensionnement de partition

Si le partitionnement peut être utilisé pour améliorer les performances des scénarios, créer une table comportant **trop de** partitions peut gêner les performances dans certaines circonstances.  Ces problèmes sont particulièrement vrai pour les tables de columnstore ordonné en clusters.  Pour le partitionnement pour être utile, il est important de comprendre quand utiliser le partitionnement et le nombre de partitions à créer.  Aucune règle dur rapide sur combien de partitions est trop nombreuses, il dépend de vos données et le nombre de partitions que vous chargez à simultanément.  Mais, en règle générale, envisager l’ajout de 10 s à 100 s de partitions, pas 1000 s.

Lorsque vous créez le partitionnement de tables de **columnstore ordonné en clusters** , il est important à prendre en compte le nombre de lignes est alors placé dans chaque partition.  Pour la compression optimal et des performances de cluster columnstore tables, un minimum de 1 million de lignes par partition et de distribution est nécessaire.  Avant la création des partitions, SQL Data Warehouse divise déjà chaque table dans les bases de données distribuées 60.  Tout ajout à une table de partitionnement est en plus les distributions créées en arrière-plan.  À l’aide de cet exemple, si la table de faits de vente contenue 36 partitions mensuelles, et étant donné que SQL Data Warehouse distributions de 60, puis la table de faits de vente doit contenir des lignes de 60 millions par mois ou 2.1 milliards de lignes lorsque tous les mois sont remplis.  Si une table contient beaucoup moins de lignes que le nombre minimal recommandé des lignes par partition, envisagez d’utiliser des partitions de moins pour augmenter le nombre de lignes par partition.  Consultez également l’article[d’Index] [indexation]qui comprend des requêtes qui peuvent être exécutés sur SQL Data Warehouse afin d’évaluer la qualité de l’index columnstore de cluster.

## <a name="syntax-difference-from-sql-server"></a>Différence de syntaxe à partir de SQL Server

SQL Data Warehouse présente une définition simplifiée de partitions qui est légèrement différente de SQL Server.  Les fonctions et les schémas de partitionnement ne sont pas utilisés dans le magasin de données SQL comme ils le sont dans SQL Server.  Au lieu de cela, il vous souhaitez est d’identifier une colonne partitionnée et les points limites.  La syntaxe de partitionnement peut être légèrement différente de SQL Server, les concepts de base sont les mêmes.  Une colonne de partition par table, ce qui peut être une partition sont est prise en charge par SQL Server et SQL Data Warehouse.  Pour en savoir plus sur le partitionnement, consultez [les Tables partitionnées et les index][].

Les partitions ci-dessous un exemple d’instruction SQL Data Warehouse partitionné de [créer la TABLE][] , de la table FactInternetSales de la colonne OrderDateKey :

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migration à partir de SQL Server de partitionnement

Pour migrer des définitions de partition de SQL Server vers SQL Data Warehouse simplement :

- Éliminer le [schéma de partition][]de SQL Server.
- Ajoutez la définition de [fonction de partition][] à la TABLE à créer.

Si vous migrez une table partitionnée à partir d’une instance de SQL Server le dessous SQL peut vous aider à interroger le nombre de lignes dans chaque partition.  Gardez à l’esprit que si la même granularité de partitionnement est utilisée sur SQL Data Warehouse, le nombre de lignes par partition diminue d’un facteur de 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Gestion de la charge de travail

Une dernière pièce dont il faut prendre en compte pour la décision de partition de table est à [la charge de travail][].  Gestion de la charge de travail dans l’entrepôt de données SQL est principalement à la gestion de la mémoire et de l’accès concurrentiel.  La quantité maximale de mémoire allouée à chaque distribution lors de l’exécution de la requête SQL Data Warehouse est les classes de ressource régi.  Dans l’idéal vos partitions seront dimensionnées par d’autres facteurs comme les besoins en mémoire de création d’index de columnstore en cluster.  Cluster columnstore index avantage considérablement lorsqu’ils sont alloués à davantage de mémoire.  Par conséquent, vous souhaiterez vous assurer qu’une reconstruction d’index de partition n’est pas manque de mémoire. Augmentation de la quantité de mémoire disponible pour votre requête est possible en changeant le rôle par défaut, smallrc, à l’un des autres rôles, tels que largerc.

Vous trouverez des informations sur l’allocation de mémoire par distribution en interrogeant les vues de gestion dynamique de gouverneur de ressources. En réalité, votre allocation de mémoire sera moins les chiffres ci-dessous. Toutefois, cela fournit un niveau de conseils que vous pouvez utiliser lors du dimensionnement de vos partitions pour les opérations de gestion de données.  Essayez d’éviter le dimensionnement de vos partitions au-delà de l’allocation de mémoire fournie par la classe de ressources très volumineux. Si vos partitions croître au-delà de cette illustration vous risquez de sollicitation de la mémoire qui à son tour entraîne moins la compression optimale.

```sql
SELECT  rp.[name]                               AS [pool_name]
,       rp.[max_memory_kb]                      AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                 AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576              AS [mex_memory_gb]
,       rp.[max_memory_percent]                 AS [max_memory_percent]
,       wg.[name]                               AS [group_name]
,       wg.[importance]                         AS [group_importance]
,       wg.[request_max_memory_grant_percent]   AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups  wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools   rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Changement de partition

SQL Data Warehouse prend en charge le fractionnement, la fusion et le changement de partition. Chacune de ces fonctions est incapable, à l’aide de l’instruction [ALTER TABLE][] .

Pour basculer les partitions entre deux tables, vous devez vous assurer que les partitions aligneront sur leurs limites respectives et que les définitions de table identique. Comme les contraintes check ne sont pas disponibles pour appliquer la plage de valeurs dans une table de la table source doit contenir les mêmes limites de partition que la table cible. Si ce n’est pas le cas, le commutateur de partition échouera car les métadonnées de la partition ne seront pas synchronisées.

### <a name="how-to-split-a-partition-that-contains-data"></a>Comment fractionner une partition qui contient les données

La méthode la plus efficace pour diviser une partition qui contient déjà des données consiste à utiliser un `CTAS` instruction. Si la table partitionnée est un columnstore de cluster puis la partition de la table doit être vide avant qu’il peut être fractionné.

Voici un exemple de table partitionnée columnstore contenant une ligne de chaque partition :

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE] En créant l’objet de la statistique, nous assurer que les métadonnées de table sont plus précises. Si nous omettez la création de statistiques, SQL Data Warehouse utilisera les valeurs par défaut. Pour plus d’informations sur les statistiques, veuillez consulter [statistiques][].

Nous pouvons ensuite rechercher le nombre de ligne à l’aide de la `sys.partitions` affichage du catalogue :

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Si vous essayez de fractionner le tableau, nous allons une erreur :

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, niveau 15, état 1, ligne 44 fractionnée de clause de l’instruction de ALTER PARTITION a échoué parce que la partition n’est pas vide.  Seules les partitions vides peuvent être scindées en lorsqu’un index columnstore existe sur la table. Envisagez de désactiver l’index columnstore avant l’émission de l’instruction ALTER PARTITION, la reconstruction de l’index columnstore après que ALTER PARTITION est terminée.

Toutefois, nous pouvons utiliser `CTAS` pour créer une nouvelle table pour stocker des données.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Comme les limites de partition sont alignés, un commutateur est autorisé. Ceci laisse la table source avec une partition vide qui nous pouvons fractionner par la suite.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tout ce qui reste à faire est d’aligner nos données pour les nouvelles limites de partition à l’aide de `CTAS` et commutateur de nos données de nouveau dans la table principale

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Une fois que vous avez terminé le déplacement des données, il est judicieux d’actualiser les statistiques sur la table cible pour s’assurer qu’ils reflètent précisément la nouvelle répartition des données dans leurs partitions respectives :

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Contrôle de code source de partitionnement de table

Pour éviter de votre définition de la table à partir de **soufflures** dans votre système de contrôle de code source vous souhaiterez l’approche suivante :

1. Créer la table comme une table partitionnée, mais sans valeurs de partition

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. `SPLIT`le tableau en tant que partie du processus de déploiement :

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Avec cette approche, le code de contrôle de code source reste statique et les valeurs de limite de partitionnement peuvent être dynamiques ; évolution avec l’entrepôt.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles sur [Vue d’ensemble de la Table][vue d’ensemble],[Des Types de données]de la [Table des Types de données], [distribution d’une Table de][distribution], [l’indexation d’un tableau]d'[Index], [Mise à jour des statistiques de Table][statistiques] et[temporaire] [Des Tables temporaires].  Pour plus d’informations sur les méthodes conseillées, consultez méthodes [Conseillées entrepôt de données SQL][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble]: ./sql-data-warehouse-tables-overview.md
[Types de données]: ./sql-data-warehouse-tables-data-types.md
[Distribuer]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[Temporaire]: ./sql-data-warehouse-tables-temporary.md
[gestion de la charge de travail]: ./sql-data-warehouse-develop-concurrency.md
[Entrepôt de données SQL meilleures pratiques]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Tables et index partitionnés]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CRÉER TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[fonction de partition]: https://msdn.microsoft.com/library/ms187802.aspx
[schéma de partition]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
