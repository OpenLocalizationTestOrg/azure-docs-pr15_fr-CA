<properties
   pageTitle="Tables temporaires de SQL Data Warehouse | Microsoft Azure"
   description="Mise en route avec des tables temporaires dans l’entrepôt de données SQL Azure."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="temporary-tables-in-sql-data-warehouse"></a>Tables temporaires de SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Les tables temporaires sont très utiles lors du traitement des données - en particulier pendant la transformation, dans laquelle les résultats intermédiaires sont transitoires. SQL Data Warehouse, les tables temporaires existent au niveau de la session.  Ils sont uniquement visibles par la session dans laquelle ils ont été créés et sont automatiquement supprimées lors de cette session se déconnecte.  Les tables temporaires offrent un gain de performances car leurs résultats sont écrits dans le local au lieu du stockage étendu.  Les tables temporaires sont légèrement différentes dans l’entrepôt de données SQL Azure de base de données de SQL Azure comme ils sont accessibles à partir de n’importe où à l’intérieur de la session, y compris à l’intérieur et en dehors d’une procédure stockée.

Cet article contient des conseils essentiels pour l’utilisation de tables temporaires et met en évidence les principes de tables temporaires au niveau de session. En utilisant les informations dans cet article peut vous aider à organiser par modules de votre code, améliorer la réutilisabilité et facilité de maintenance de votre code.

## <a name="create-a-temporary-table"></a>Créer une table temporaire

Les tables temporaires sont créées en ajoutant simplement de votre nom de la table avec un `#`.  Par exemple :

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]       NVARCHAR(128) NOT NULL
,   [table_name]            NVARCHAR(128) NOT NULL
,   [stats_name]            NVARCHAR(128) NOT NULL
,   [stats_is_filtered]     BIT           NOT NULL
,   [seq_nmbr]              BIGINT        NOT NULL
,   [two_part_name]         NVARCHAR(260) NOT NULL
,   [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
```

Les tables temporaires peuvent également être créés avec un `CTAS` à l’aide de la même approche :

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS`est une commande très puissante, et présente l’avantage d’être très efficace dans son utilisation de l’espace du journal des transactions. 


## <a name="dropping-temporary-tables"></a>Suppression des tables temporaires

Lorsqu’une nouvelle session est créée, aucune table temporaire ne doit exister.  Toutefois, si vous appelez la procédure stockée même, ce qui crée un fichier temporaire avec le même nom, pour vous assurer que votre `CREATE TABLE` instructions réussissent une vérification de l’existence préalable simple avec un `DROP` peut être utilisée comme dans le Voici un exemple :

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Pour le codage de la cohérence, il est recommandé d’utiliser ce modèle pour les tables et les tables temporaires.  Il est également judicieux d’utiliser `DROP TABLE` pour supprimer les tables temporaires lorsque vous avez fini de les utiliser dans votre code.  Développement, il est assez courant de voir les liste de commandes regroupées à la fin d’une procédure pour s’assurer de ces objets sont nettoyés dans la procédure stockée.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizing de code

Dans la mesure où les tables temporaires peuvent être visibles dans une session utilisateur, peut être exploitée pour vous aider à modulariser le code de votre application.  Par exemple, la procédure stockée ci-dessous regroupe les méthodes recommandées ci-dessus pour générer un fichier DDL qui mettra à jour toutes les statistiques dans la base de données par nom de statistique.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

À ce stade, la seule action qui s’est produite est la création d’une procédure stockée qui sera simplement généré une table temporaire, stats_ddl #, avec des instructions DDL.  Cette procédure stockée supprime #stats_ddl s’il existe déjà pour vous assurer qu’il n’échoue pas si vous exécutez plusieurs fois au cours d’une session.  Toutefois, étant donné qu’aucun `DROP TABLE` à la fin de la procédure stockée, lorsque la procédure stockée se termine, il laisse le tableau afin qu’il puisse être lu en dehors de la procédure stockée.  Dans le magasin de données SQL, contrairement à d’autres bases de données SQL Server, il est possible d’utiliser la table temporaire, en dehors de la procédure qui l’a créé.  Tables temporaires de SQL Data Warehouse peuvent être utilisées **n’importe où** à l’intérieur de la session. Cela peut entraîner davantage de code modulaire et facile à gérer que dans le Voici un exemple :

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Restrictions de table temporaire

SQL Data Warehouse impose deux limitations lors de l’implémentation des tables temporaires.  Actuellement, seules session étendue des tables temporaires sont pris en charge.  Les Tables temporaires globales ne sont pas pris en charge.  En outre, vues ne peut pas être créés sur les tables temporaires.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles sur la [Vue d’ensemble de la Table][vue d’ensemble],[Des Types de données]de la [Table des Types de données], [distribution d’une Table de][distribution], [l’indexation d’un tableau]d'[Index], [partitionnement d’une Table]de[Partition] et de [Mise à jour des statistiques de Table][statistiques].  Pour plus d’informations sur les méthodes conseillées, consultez méthodes [Conseillées entrepôt de données SQL][].

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

<!--Other Web references-->
