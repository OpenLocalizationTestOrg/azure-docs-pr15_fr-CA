<properties
   pageTitle="Gestion des statistiques sur les tables SQL Data Warehouse | Microsoft Azure"
   description="Mise en route avec les statistiques sur les tables dans le magasin de données SQL Azure."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Gestion des statistiques sur les tables SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Plus SQL Data Warehouse sait sur vos données, plus il peut exécuter des requêtes sur vos données.  La que vous indiquez à SQL Data Warehouse sur vos données, consiste à collecter des statistiques sur vos données.  Statistiques de vos données est une des choses plus importantes que vous pouvez effectuer pour optimiser vos requêtes.  Statistiques aident à créer le plan optimal pour vos requêtes SQL Data Warehouse.  C’est parce que l’optimiseur de requêtes SQL Data Warehouse est un optimiseur de coût en fonction.  Autrement dit, il compare le coût de différents plans de requête et choisit ensuite le plan avec le coût le plus bas, ce qui doit être également le plan qui exécutera le plus rapide.

Des statistiques peuvent être créées sur une seule colonne, plusieurs colonnes ou sur un index d’une table.  Statistiques sont stockées dans un histogramme qui capture la plage et la sélectivité des valeurs.  Il s’agit d’un intérêt particulier lorsque l’optimiseur doit évaluer les jointures, GROUP BY, HAVING et la clause WHERE dans une requête.  Par exemple, si l’optimiseur estime que la date que vous filtrez dans votre requête renvoie 1 ligne, il peut choisir un autre plan que s’il estimations qu’ils date vous ont sélectionné va renvoyer 1 million de lignes.  Lors de la création de statistiques est extrêmement important, il est tout aussi important de que statistiques *précisément* reflètent l’état actuel de la table.  Les statistiques à jour permet de garantir qu’un bon plan est sélectionné par l’optimiseur.  Les plans créés par l’optimiseur ne sont utiles que pour les statistiques sur vos données.

Le processus de création et de mise à jour des statistiques est actuellement un processus manuel, mais il est très simple à réaliser.  Il s’agit à la différence de SQL Server qui crée automatiquement et met à jour les statistiques sur des colonnes uniques et les index.  En utilisant les informations ci-dessous, vous pouvez largement automatiser la gestion des statistiques sur vos données. 

## <a name="getting-started-with-statistics"></a>Mise en route avec statistiques

 Création de statistiques échantillonnées dans chaque colonne est un moyen facile de commencer avec des statistiques.  Puisqu’il est également important de maintenir les statistiques à jour, une méthode plus classique peut être mise à jour de vos statistiques quotidiennes ou après chaque charge. Il existe toujours des compromis entre les performances et le coût de créer et de mettre à jour les statistiques.  Si vous trouvez qu’il prend trop de temps pour mettre à jour tous vos statistiques, vous souhaiterez peut-être essayez d’être plus sélectif sur les colonnes qui ont des statistiques ou les colonnes qui doivent souvent mises à jour.  Par exemple, vous pouvez souhaiter mettre à jour les colonnes date tous les jours, comme les nouvelles valeurs peuvent être ajoutées et non après chaque charge. Encore une fois, vous allez acquérir les plus intéressantes en ayant des statistiques sur des colonnes impliquées dans les jointures, GROUP BY, HAVING et la clause WHERE.  Si vous avez une table avec un grand nombre de colonnes qui ne sont utilisées que dans la clause SELECT, ne permettront pas de statistiques sur ces colonnes et dépenses un peu plus d’efforts pour identifier uniquement les colonnes où les statistiques vous aide, permet de réduire le temps nécessaire pour mettre à jour de vos statistiques.

## <a name="multi-column-statistics"></a>Statistiques multi-colonnes

En plus de créer des statistiques sur des colonnes uniques, vous pouvez constater que vos requêtes bénéficieront de statistiques multi-colonnes.  Statistiques multi-colonnes sont les statistiques créées sur une liste de colonnes.  Ils comprennent les statistiques de colonne sur la première colonne dans la liste, ainsi que certaines informations de corrélation de cross-colonne appelée densité.  Par exemple, si vous avez une table qui se joint à un autre sur deux colonnes, vous constaterez que SQL Data Warehouse peut améliorer le plan s’il comprend la relation entre les deux colonnes.   Statistiques multi-colonnes peuvent améliorer les performances pour certaines opérations telles que les jointures composites et de group by de la requête.

## <a name="updating-statistics"></a>Mise à jour des statistiques

Mise à jour des statistiques est une partie importante de votre routine de gestion de base de données.  Lorsque la distribution des données dans la base de données est modifiée, les statistiques doivent être mis à jour.  Les statistiques obsolètes entraîne des performances de requête non optimaux.

Une meilleure solution consiste à mettre à jour les statistiques sur les colonnes date chaque jour lors de l’ajout de nouvelles dates.  Chaque ligne de nouvelle heure est chargées dans le data warehouse, nouvelles dates de charge ou de dates de transaction sont ajoutées. Modifier la distribution des données et les rendre les statistiques obsolètes. À l’inverse, statistiques sur une colonne pays dans une table customer deviez jamais mis à jour, comme la distribution des valeurs généralement ne change pas. En supposant que la distribution est constante entre les clients, ajouter de nouvelles lignes à la variation de table ne va pas modifier la distribution des données. Toutefois, si votre entrepôt de données ne contient qu’un seul pays et que vous importez des données à partir d’un nouveau pays, résultant de données provenant de plusieurs pays sont stockés, puis vous sans aucun doute besoin mettre à jour les statistiques sur la colonne pays.

Une des premières questions à poser lors du dépannage d’une requête est « Sont les statistiques à jour ? »

Cette question n’est pas un qui peuvent être satisfaites par l’âge des données. Un objet statistiques à jour peut être très ancien si aucun matériau modifications aux données sous-jacentes n’ont été. Lorsque le nombre de lignes a changé de façon substantielle ou de changement de matériel dans la distribution des valeurs pour une colonne donnée *puis* il est temps de mettre à jour les statistiques.  

Pour référence, **SQL Server** (pas SQL Data Warehouse) met automatiquement à jour les statistiques pour ces situations :

- Si vous avez aucune ligne dans la table, lorsque vous ajoutez des lignes, vous obtiendrez une mise à jour automatique des statistiques
- Lorsque vous ajoutez plus de 500 lignes à une table, en commençant avec moins de 500 lignes (par exemple, au début vous avez 499 et ensuite ajouter des lignes de 500 à 999 lignes au total), vous obtiendrez une mise à jour automatique 
- Une fois que vous êtes plus de 500 lignes, que vous devez ajouter des lignes supplémentaires de 500 20 % de la taille de la table avant que vous verrez une mise à jour automatique sur les statistiques

Dans la mesure où il n’y a aucune vue pour déterminer si les données dans la table a changé depuis les dernières statistiques de temps ont été mis à jour, la connaissance de l’âge de vos statistiques peut vous fournir avec la partie de l’image.  Vous pouvez utiliser la requête suivante pour déterminer la dernière fois que vos statistiques lorsque la mise à jour sur chaque table.  

> [AZURE.NOTE] N’oubliez pas que s’il existe une modification significative de la distribution des valeurs pour une colonne donnée, vous devez mettre à jour les statistiques quelle que soit la dernière fois qu’ils ont été mis à jour.  

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Les colonnes de date dans un entrepôt de données, par exemple, généralement besoin statistiques mises à jour fréquentes. Chaque ligne de nouvelle heure est chargées dans le data warehouse, nouvelles dates de charge ou de dates de transaction sont ajoutées. Modifier la distribution des données et les rendre les statistiques obsolètes.  À l’inverse, statistiques sur une colonne de sexe sur une table de client peuvent jamais besoin d’être mis à jour. En supposant que la distribution est constante entre les clients, ajouter de nouvelles lignes à la variation de table ne va pas modifier la distribution des données. Toutefois, si votre entrepôt de données ne contient qu’un seul sexe et un nouveau résultat des besoins dans plusieurs sexe puis vous sans aucun doute besoin mettre à jour les statistiques sur la colonne de sexe.

Pour plus d’informations, reportez-vous à la section [statistiques][] sur MSDN.

## <a name="implementing-statistics-management"></a>Implémentation de la gestion des statistiques

Il est souvent judicieux d’étendre votre processus pour vous assurer que les statistiques sont mises à jour à la fin de la charge de chargement des données. Le chargement de données est lorsque les tables changer plus fréquemment leur taille et/ou leur distribution de valeurs. Par conséquent, il s’agit d’un emplacement logique pour implémenter des processus de gestion.

Pour mettre à jour vos statistiques pendant le processus de chargement, certains principes directeurs sont fournis ci-dessous :

- Assurez-vous que chaque table chargé dispose d’au moins un objet statistiques mis à jour. Cela met à jour les informations de taille (nombre de lignes et le nombre de pages) de tables dans le cadre de la mise à jour des statistiques.
- Se concentrer sur des colonnes impliquées dans la jointure, clauses GROUP BY, ORDER BY et DISTINCT
- Pensez « croissant clé » des colonnes comme transaction dates plus fréquemment que ces valeurs ne figureront pas dans l’histogramme des statistiques.
- Envisagez la mise à jour des colonnes de distribution statique moins fréquemment.
- Rappelez-vous que chaque objet de la statistique est mis à jour en série. Mise en œuvre de simplement `UPDATE STATISTICS <TABLE_NAME>` n’est peut-être pas idéal - notamment pour les tables larges avec un grand nombre d’objets statistiques.

> [AZURE.NOTE] Pour plus d’informations sur [croissant clé], veuillez consulter le livre blanc de SQL Server 2014 cardinalité estimation modèle.

Pour plus d’informations, voir [Estimation de cardinalité][] sur MSDN.

## <a name="examples-create-statistics"></a>Exemples : Créer des statistiques

Ces exemples montrent comment utiliser les différentes options pour la création de statistiques. Les options que vous utilisez pour chaque colonne dépendent des caractéristiques de vos données et la façon dont la colonne sera utilisée dans les requêtes.

### <a name="a-create-single-column-statistics-with-default-options"></a>A. Créer des statistiques de colonne unique avec les options par défaut

Pour créer des statistiques sur une colonne, vous contenter de fournir un nom pour l’objet de statistiques et le nom de la colonne.

Cette syntaxe utilise toutes les options par défaut. Par défaut, SQL Data Warehouse exemples de 20 pour cent de la table lorsqu’il crée des statistiques.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Par exemple :

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. Créer des statistiques de colonne unique en examinant chaque ligne

Le taux d’échantillonnage par défaut de 20 % est suffisant pour la plupart des situations. Toutefois, vous pouvez ajuster le taux d’échantillonnage.

Pour échantillonner la table complète, utilisez la syntaxe suivante :

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Par exemple :

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. Créer des statistiques de colonne unique en spécifiant la taille de l’échantillon

Vous pouvez également spécifier la taille de l’échantillon en pourcentage :

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. Créer des statistiques de colonne unique sur certaines lignes uniquement

Une autre option, vous pouvez créer des statistiques sur une partie des lignes de votre table. Il s’agit d’une statistique filtrée.

Par exemple, vous pouvez utiliser les statistiques filtrées lorsque vous prévoyez d’interroger une partition spécifique d’une grande table partitionnée. En créant des statistiques sur les valeurs de partition, l’exactitude des statistiques améliorer et donc améliorer les performances des requêtes.

Cet exemple crée des statistiques sur une plage de valeurs. Les valeurs peuvent facilement être définies pour correspondre à la plage de valeurs d’une partition.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE] L’optimiseur de requête d’envisager l’utilisation de statistiques filtrées lorsqu’il choisit le plan de requête distribuée, la requête doit tenir à l’intérieur de la définition de l’objet de statistiques. À l’aide de l’exemple précédent, la requête où la clause doit spécifier les valeurs col1 entre 2000101 et 20001231.

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. Créer des statistiques de colonne unique avec toutes les options

Vous pouvez, bien sûr, combiner les options. L’exemple ci-dessous crée un objet statistiques filtrée avec une taille d’échantillon personnalisé :

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Pour la référence complète, voir [CREATE STATISTICS][] sur MSDN.

### <a name="f-create-multi-column-statistics"></a>F. Créer des statistiques multi-colonnes

Pour créer des statistiques multi-colonnes, utilisez les exemples précédents, mais spécifier plus de colonnes.

> [AZURE.NOTE] L’histogramme, laquelle est utilisée pour estimer le nombre de lignes dans le résultat de la requête, est uniquement disponible pour la première colonne figurant dans la définition de l’objet statistiques.

Dans cet exemple, l’histogramme est de *produit\_catégorie*. Statistiques de colonne croisée sont calculées sur *produit\_catégorie* et *produit\_sub_c\ategory*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Dans la mesure où il existe une corrélation entre les *produit\_catégorie* et *produit\_sub\_catégorie*, un stat multi-colonnes peut être utile si vous accédez à ces colonnes en même temps.

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. Créer des statistiques sur toutes les colonnes d’une table

Une façon de créer des statistiques est aux problèmes les commandes CREATE STATISTICS après la création de la table.

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H. Utiliser une procédure stockée pour créer des statistiques sur toutes les colonnes dans une base de données

SQL Data Warehouse n’a pas d’une procédure système stockée équivalente en [] de [sp_create_stats] dans SQL Server. Cette procédure stockée crée un objet de statistiques de colonne unique sur toutes les colonnes de la base de données qui ne possède pas de statistiques.

Cela vous permettra de démarrer avec la conception de votre base de données. N’hésitez pas à les adapter à vos besoins.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN   sys.[external_tables] e ON  e.[object_id]       = t.[object_id]
WHERE       l.[object_id] IS NULL
AND         e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Pour créer des statistiques sur toutes les colonnes de la table avec cette procédure, appelez simplement la procédure.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Exemples : mise à jour de statistiques

Pour mettre à jour des statistiques, vous pouvez :

1. Mettre à jour un objet statistiques. Spécifiez le nom de l’objet de statistiques que vous souhaitez mettre à jour.
2. Mettre à jour tous les objets statistiques sur une table. Spécifiez le nom de la table au lieu d’un objet statistiques spécifique.


### <a name="a-update-one-specific-statistics-object"></a>A. Mise à jour d’un objet statistiques spécifique ###
Mettre à jour un objet statistiques spécifique, utilisez la syntaxe suivante :

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Par exemple :

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

En mettant à jour les objets statistiques spécifiques, vous pouvez réduire le temps et les ressources nécessaires pour gérer des statistiques. Cette opération nécessite réflexion, cependant, choisir les objets statistiques meilleures mise à jour.


### <a name="b-update-all-statistics-on-a-table"></a>B. Mettre à jour toutes les statistiques sur une table ###
Voici une méthode simple de mise à jour de tous les objets statistiques sur une table.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Par exemple :

```sql
UPDATE STATISTICS dbo.table1;
```

Cette instruction est facile à utiliser. N’oubliez pas que cela met à jour toutes les statistiques sur la table et par conséquent peut travailler plus que nécessaire. Si la performance n’est pas un problème, il s’agit sans aucun doute la façon plus simple et la plus complète pour garantir les statistiques sont à jour.

> [AZURE.NOTE] Lors de la mise à jour toutes les statistiques d’une table, SQL Data Warehouse effectue une analyse à la table pour chaque statistiques d’échantillon. Si la table est volumineuse, a le nombre de colonnes et de nombreuses statistiques, il peut être plus efficace de mettre à jour des statistiques en fonction des besoins.

Pour l’implémentation d’un `UPDATE STATISTICS` procédure, consultez l’article[temporaire] [Des Tables temporaires]. La méthode d’implémentation est légèrement différente de la `CREATE STATISTICS` procédure décrite ci-dessus, mais le résultat final est le même.

Pour la syntaxe complète, consultez [Les statistiques de mise à jour][] sur MSDN.

## <a name="statistics-metadata"></a>Métadonnées de statistiques
Il existe plusieurs vue système et les fonctions que vous pouvez utiliser pour rechercher des informations sur les statistiques. Par exemple, vous pouvez voir si un objet statistiques peut être périmé pour voir quand les statistiques dernière création ou mise à jour à l’aide de la fonction statistiques-date.

### <a name="catalog-views-for-statistics"></a>Vues de catalogue pour les statistiques
Ces vues système fournissent des informations sur les statistiques :

| Vue de catalogue | Description |
| :----------- | :---------- |
| [Sys.Columns][]  | Une ligne pour chaque colonne. |
| [Sys.Objects][]  | Une ligne pour chaque objet dans la base de données. |  |
| [Sys.Schemas][]  | Une ligne pour chaque schéma de la base de données. |  |
| [Sys.stats][] | Une ligne pour chaque objet statistiques. |
| [Sys.stats_columns][] | Une ligne pour chaque colonne dans l’objet de statistiques. Liens vers sys.columns. |
| [Sys.tables][] | Une ligne pour chaque table (y compris les tables externes). |
| [Sys.table_types][] | Une ligne pour chaque type de données. |


### <a name="system-functions-for-statistics"></a>Fonctions de statistiques système
Ces fonctions système sont utiles pour travailler avec des statistiques :

| Fonction système | Description |
| :-------------- | :---------- |
| [FONCTION STATS_DATE][]    | Date de que dernière mise à jour de l’objet de statistiques. |
| [DBCC SHOW_STATISTICS][] | Fournit des informations de synthèse et détaillées sur la distribution des valeurs comme compris par l’objet de statistiques. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combiner des colonnes de statistiques et de fonctions dans une vue

Cette vue affiche les colonnes liées aux statistiques et résulte de la fonction de [] [STATS_DATE()] ensemble.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Exemples de DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() indique les données stockées dans un objet statistiques. Ces données proviennent en trois parties.

1. En-tête
2. Vecteur de densité
3. Histogramme

Les métadonnées d’en-tête sur les statistiques. L’histogramme affiche la distribution des valeurs dans la première colonne de clé de l’objet de statistiques. Le vecteur de densité mesure les corrélations entre colonnes. SQLDW calcule les estimations de cardinalité avec des données dans l’objet de statistiques.

### <a name="show-header-density-and-histogram"></a>Afficher l’en-tête, densité et l’histogramme,

Cet exemple simple montre tous les trois parties d’un objet statistiques.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Par exemple :

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Afficher une ou plusieurs parties de SHOW_STATISTICS() de la DBCC ;

Si vous êtes uniquement intéressé par des parties spécifiques, utilisez le `WITH` clause et spécifient les éléments que vous voulez voir :

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Par exemple :

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Différences de DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() est plus strictement implémenté dans SQL Data Warehouse par rapport à SQL Server.

1. Fonctions non documentées ne sont pas pris en charge.
- Impossible d’utiliser Stats_stream
- Impossible de joindre les résultats pour les sous-ensembles spécifiques de données statistiques, par exemple (DENSITY_VECTOR de jointure STAT_HEADER)
2. NO_INFOMSGS ne peut pas être définie pour la suppression du message
3. Crochets autour des noms de statistiques ne peut pas être utilisés.
4. Impossible d’utiliser des noms de colonnes pour identifier les objets statistiques
5. Message d’erreur personnalisé 2767 n’est pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir [DBCC SHOW_STATISTICS][] sur MSDN.  Pour plus d’informations, consultez les articles sur[vue d’ensemble]de la [Vue d’ensemble de la Table], [Les Types de données de Table de][Types de données], [distribution d’une Table de][distribution], [l’indexation d’un tableau]d'[Index], [partitionnement d’une Table]de[Partition] et[temporaire] [Des Tables temporaires].  Pour plus d’informations sur les méthodes conseillées, consultez méthodes [Conseillées entrepôt de données SQL][].  

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

<!--MSDN references-->  
[Estimation de la cardinalité]: https://msdn.microsoft.com/library/dn600374.aspx
[CRÉER DES STATISTIQUES]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistiques]: https://msdn.microsoft.com/library/ms190397.aspx
[FONCTION STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[Sys.Columns]: https://msdn.microsoft.com/library/ms176106.aspx
[Sys.Objects]: https://msdn.microsoft.com/library/ms190324.aspx
[Sys.Schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[Sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[Sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[Sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[Sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[METTRE À JOUR LES STATISTIQUES]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
