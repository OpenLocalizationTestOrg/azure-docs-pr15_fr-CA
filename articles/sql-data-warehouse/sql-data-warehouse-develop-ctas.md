<properties
   pageTitle="Créer la table que vous sélectionnez (SACT) dans le magasin de données SQL | Microsoft Azure"
   description="Conseils de codage avec la créer une table en tant qu’instruction select de (SACT) dans le magasin de données SQL Azure pour le développement de solutions."
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>Créer la Table comme Select (SACT) dans le magasin de données SQL
Créer tableau Select ou `CTAS` l’une des plus importantes fonctionnalités de T-SQL est disponible. Il s’agit d’une opération entièrement parallélisée qui crée une table en fonction du résultat d’une instruction SELECT. `CTAS`est la façon la plus simple et la plus rapide pour créer une copie d’une table. Vous pouvez considérer qu’il s’agit d’une version de suralimentation de `SELECT..INTO` si vous le souhaitez. Ce document fournit des exemples et meilleures pratiques pour `CTAS`.

## <a name="using-ctas-to-copy-a-table"></a>Pour copier une table à l’aide de SACT

Peut-être le plus souvent utilisé pour `CTAS` consiste à créer une copie d’une table de sorte que vous pouvez modifier le code DDL. Si par exemple vous avez créé votre table en tant que `ROUND_ROBIN` et maintenant que vous souhaitez modifier une table distribué sur une colonne, `CTAS` est la façon de modifier la colonne de distribution. `CTAS`peut également servir à modifier les types de partitionnement, l’indexation ou de colonne.

Supposons que vous avez créé cette table en utilisant le type par défaut de `ROUND_ROBIN` distribuées car aucune colonne de distribution a été spécifié dans le `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Vous voulez maintenant créer une nouvelle copie de cette table avec un Columnstore Index ordonné en clusters afin que vous pouvez tirer parti de la performance des tables de Columnstore de cluster. Aussi, vous souhaitez distribuer le tableau de ProductKey dans la mesure où vous comptez des jointures sur cette colonne et pour éviter le déplacement des données au cours des jointures sur ProductKey. Enfin vous voulez également ajouter le partitionnement sur OrderDateKey de sorte que vous pouvez rapidement supprimer les anciennes données en supprimant les anciennes partitions. Voici l’instruction SACT, qui copie votre ancienne table dans une nouvelle table.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Enfin, vous pouvez renommer vos tables pour permuter dans votre nouvelle table et puis déposez votre ancienne table.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [AZURE.NOTE] Entrepôt de données SQL Azure est pas encore prise en charge automatique créer ou automatique des statistiques de mise à jour.  Pour obtenir les meilleures performances de vos requêtes, il est important que créer des statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou apporter aucune modification substantielle se produire dans les données.  Pour une explication détaillée des statistiques, consultez la rubrique de [statistiques][] dans le groupe de développement de rubriques.

## <a name="using-ctas-to-work-around-unsupported-features"></a>À l’aide de SACT de contourner les fonctionnalités non prises en charge

`CTAS`peut également servir à contourner un certain nombre de fonctionnalités non prises en charge répertoriées ci-dessous. Ceci peut souvent s’avérer une situation gagnant/gagnant comme non seulement votre code soit conforme, mais il s’exécute souvent plus rapidement dans l’entrepôt de données SQL. C’est en raison de sa conception entièrement parallélisée. Les scénarios qui peuvent être contournées par SACT sont les suivantes :

- SÉLECTIONNEZ... DANS
- JOINTURES en ANSI sur les mises à jour
- Jointures en ANSI sur les suppressions
- FUSION d’instruction

> [AZURE.NOTE] Essayez de vous demander « SACT premier ». Si vous pensez que vous pouvez résoudre un problème par le biais de le `CTAS` puis qui est généralement la meilleure approche, même si vous écrivez davantage de données en conséquence.
>

## <a name="selectinto"></a>SÉLECTIONNEZ... DANS
Vous pouvez trouver `SELECT..INTO` s’affiche dans plusieurs endroits dans votre solution.

Vous trouverez ci-dessous un exemple d’une `SELECT..INTO` instruction :

```sql
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

Pour convertir les produits ci-dessus `CTAS` est assez directe :

```sql
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [AZURE.NOTE] SACT requiert actuellement qu'une colonne distribution être spécifié.  Si vous n’essayez pas de modifier la colonne distribution, volontairement votre `CTAS` effectuera le plus rapidement si vous sélectionnez une colonne de distribution qui est identique à la table sous-jacente, car cette stratégie évite le déplacement des données.  Si vous créez une petite table où les performances ne sont pas un facteur, puis vous pouvez spécifier `ROUND_ROBIN` pour éviter d’avoir à décider sur une colonne de distribution.

## <a name="ansi-join-replacement-for-update-statements"></a>Remplacement de jointure ANSI pour les instructions de mise à jour

Vous trouverez peut-être qu'une mise à jour complexe qui joint plusieurs tables en utilisant ANSI syntaxe de jointure pour effectuer la mise à jour ou suppression.

Imaginez que vous deviez mettre à jour cette table :

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(   [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,   [CalendarYear]                  SMALLINT        NOT NULL
,   [TotalSalesAmount]              MONEY           NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

La requête d’origine peut avoir regardé quelque chose comme ceci :

```sql
UPDATE  acs
SET     [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT  [EnglishProductCategoryName]
        ,       [CalendarYear]
        ,       SUM([SalesAmount])              AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]       AS s
        JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
        JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
        WHERE   [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,       [CalendarYear]
        ) AS fis
ON  [acs].[EnglishProductCategoryName]  = [fis].[EnglishProductCategoryName]
AND [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

ANSI SQL Data Warehouse ne gérant pas les jointures dans le `FROM` clause d’une `UPDATE` instruction, vous ne pouvez pas copier ce code sur sans le modifier légèrement.

Vous pouvez utiliser une combinaison d’un `CTAS` et une jointure implicite pour remplacer ce code :

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT  ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,       ISNULL(CAST([CalendarYear] AS SMALLINT),0)                      AS [CalendarYear]
,       ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                     AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]       AS s
JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
WHERE   [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,       [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Remplacement de jointure ANSI pour supprimer des instructions
Parfois, la meilleure approche pour la suppression des données est d’utiliser `CTAS`. Au lieu de supprimer simplement les données, sélectionnez les données que vous souhaitez conserver. Cette particulièrement vrai pour les `DELETE` les instructions qui utilisent ansi syntaxe de jointure dans la mesure où SQL Data Warehouse ne prend pas en charge ANSI d’une jointure dans le `FROM` clause d’un `DELETE` instruction.

Un exemple d’une instruction DELETE converti est le suivant :

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Remplacez les instructions de fusion
Instructions de fusion peuvent être remplacées, au moins en partie, à l’aide de `CTAS`. Vous pouvez consolider les `INSERT` et `UPDATE` dans une instruction unique. Tous les enregistrements supprimés doit être clôturé dans une deuxième instruction.

Un exemple d’une `UPSERT` est le suivant :

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Recommandation de SACT : déclarer explicitement le type de données et la possibilité de valeur null de la sortie

Lors de la migration du code, vous pouvez trouver que vous rencontrer ce type de modèle de codage :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instinctivement, vous pensez vous devez migrer ce code à un SACT et vous ne serait pas correct. Toutefois, il existe un masqué problème ici.

Le code suivant ne produit pas le même résultat :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Notez que la colonne « résultat » reprend les valeurs de type et de la possibilité de valeur NULL des données de l’expression. Cela peut entraîner des variations subtiles dans les valeurs si vous ne faites pas attention.

Essayez la méthode suivante comme exemple :

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

La valeur stockée pour résultat est différente. L’erreur devient encore plus significative de la valeur persistante dans la colonne de résultat est utilisée dans d’autres expressions.

![][1]

Ceci est particulièrement important pour les migrations de données. Même si la seconde requête est sans doute plus précise, il existe un problème. Les données doivent être différentes par rapport au système source et qui permet d’accéder aux questions d’intégrité dans la migration. Il s’agit d’un de ces cas rares où la réponse « mauvaise » est en réalité la bonne !

La raison pour laquelle que nous voir cette disparité entre les deux résultats est à la conversion de type implicite. Dans le premier exemple, la table définit la définition de colonne. Lorsque la ligne est insérée, une conversion de type implicite se produit. Dans le deuxième exemple n’a aucune conversion de type implicite l’expression définit le type de données de la colonne. Notez également que la colonne dans le deuxième exemple a été définie comme une colonne pouvant être null que dans le premier exemple n’a pas. Lorsque la table a été créée dans les valeurs de colonne NULL premier exemple a été explicitement définie. Dans le second exemple qu'il était juste gauche à l’expression et par défaut cela aurait pour résultat dans une définition de valeur NULL.  

Pour résoudre ces problèmes vous devez définir explicitement la conversion de type et de la possibilité de valeur NULL dans le `SELECT` partie de le `CTAS` instruction. Vous ne pouvez pas définir ces propriétés dans la partie tableau de créer.

L’exemple ci-dessous montre comment corriger le code :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Notez les points suivants :
- CAST ou CONVERT aurait pu être utilisé
- ISNULL est utilisée pour forcer la fusion pas de valeurs null
- ISNULL est la fonction la plus externe
- La deuxième partie de la ISNULL est une constante c'est-à-dire 0

> [AZURE.NOTE] La possibilité de valeur null à définir correctement il est indispensable d’utiliser `ISNULL` et non `COALESCE`. `COALESCE`n’est pas une fonction déterministe et donc le résultat de l’expression sera toujours NULLable. `ISNULL`est différent. Il n’est pas déterministe. Par conséquent, lorsque la deuxième partie de la `ISNULL` fonction est une constante ou un littéral, puis la valeur résultante sera non NULL.

Cette info-bulle n’est pas seulement utile pour assurer l’intégrité de vos calculs. Il est également important pour le changement de partition de table. Imaginez que vous avez défini comme le fait le tableau :

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Toutefois, le champ de valeur est une expression calculée ne fait pas partie de la source de données.

Pour créer votre jeu partitionné, que vous souhaiterez peut-être effectuer cette opération :

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

La requête s’exécute parfaitement. Un problème se pose lorsque vous essayez d’exécuter le commutateur de partition. Les définitions de table ne correspondent pas. Pour rendre les définitions de table correspondent à la SACT doit être modifié.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Par conséquent, vous pouvez voir que la cohérence de type et de la mise à jour des propriétés de possibilité de valeur NULL sur un SACT est une bonne pratique de meilleure ingénierie des procédés. Il vous aide à maintenir l’intégrité de vos calculs et garantit également que le changement de partition est possible.

Pour plus d’informations sur l’utilisation de [SACT][], reportez-vous à MSDN. Il s’agit d’une des plus importantes instructions dans le magasin de données SQL Azure. Assurez-vous de que bien comprendre.

## <a name="next-steps"></a>Étapes suivantes
Pour plus de conseils de développement, consultez [vue d’ensemble du développement][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[vue d’ensemble du développement]: sql-data-warehouse-overview-develop.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[SACT]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->
