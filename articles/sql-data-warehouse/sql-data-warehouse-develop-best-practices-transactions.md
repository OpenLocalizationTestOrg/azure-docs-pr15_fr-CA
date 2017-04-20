<properties
   pageTitle="Optimisation des transactions pour SQL Data Warehouse | Microsoft Azure"
   description="Méthodes recommandées sur l’écriture de mises à jour de transaction efficace dans l’entrepôt de données SQL Azure"
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess"/>

# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Optimisation des transactions pour SQL Data Warehouse

Cet article explique comment optimiser les performances de votre code transactionnel en limitant les risques pour le restaurations long.

## <a name="transactions-and-logging"></a>Enregistrement de transactions et de

Les transactions sont un composant important d’un moteur de base de données relationnelle. SQL Data Warehouse utilise les transactions lors de la modification des données. Ces opérations peuvent être explicites ou implicites. Seul `INSERT`, `UPDATE` et `DELETE` instructions sont des exemples de transactions implicites. Transactions explicites sont écrits de façon explicite par un développeur à l’aide de `BEGIN TRAN`, `COMMIT TRAN` ou `ROLLBACK TRAN` et sont généralement utilisés lorsque plusieurs instructions de modification doivent être reliées entre elles dans une unité atomique unique. 

Entrepôt de données SQL Azure valide les modifications apportées à la base de données à l’aide de journaux de transactions. Chaque distribution a son propre journal des transactions. Écritures de journal de transactions sont automatiques. Il n’y a aucune configuration requise. Toutefois, si ce processus garantit l’écriture, il introduit une surcharge dans le système. Vous pouvez réduire cet impact en écrivant du code transactionnel efficace. Code transactionnel efficace se situe généralement en deux catégories.

- Tirer parti des constructions de journalisation minimale lorsque cela est possible
- Données de processus à l’aide de lots de portée limitées afin d’éviter les transactions à long terme au singulier
- Adopter une partition de motif pour les modifications de grande taille sur une partition donnée de commutation

## <a name="minimal-vs-full-logging"></a>Minimale et la journalisation complète

À la différence des opérations entièrement consignées, dont le journal des transactions permet d’effectuer le suivi de chaque modification de ligne, les opérations journalisées effectuer le suivi des allocations des extensions et des modifications de métadonnées uniquement. Par conséquent, la journalisation minimale implique journalisation uniquement les informations qui vous est nécessaires de restaurer la transaction dans le cas d’une panne ou à une requête explicite (`ROLLBACK TRAN`). Comme beaucoup moins informations sont suivies dans le journal des transactions, une opération journalisée minimale performant qu’une opération de consignation complète de même taille. En outre, parce que les écritures moins passer le journal des transactions, une plus petite quantité de données de journal est générée et est donc une e/s plus efficace.

Les limites de sécurité de transaction s’appliquent uniquement aux opérations entièrement consignées.

>[AZURE.NOTE] Les opérations journalisées peuvent participer aux transactions explicites. Que toutes les modifications dans les structures d’allocation sont suivies, il est possible d’annuler les opérations journalisées. Il est important de comprendre que la modification est « » journalisée il n’est pas non connecté.

## <a name="minimally-logged-operations"></a>Opérations journalisées

Les opérations suivantes sont capables d’être journalisées :

- CRÉER la TABLE en tant que sélection ([SACT][])
- INSÉRER... SÉLECTIONNEZ
- CRÉATION D’INDEX
- ALTER INDEX REBUILD
- SUPPRIMER L’INDEX
- TRUNCATE TABLE
- SUPPRESSION DE TABLE
- ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

>[AZURE.NOTE] Opérations de déplacement de données internes (tel que `BROADCAST` et `SHUFFLE`) ne sont pas affectés par la limite de sécurité de transaction.

## <a name="minimal-logging-with-bulk-load"></a>Journalisation minimale avec le chargement en bloc

`CTAS`et `INSERT...SELECT` sont tous deux charge des opérations en bloc. Toutefois, les deux sont influencées par la définition de la table cible et dépendent du scénario de charge. Vous trouverez ci-dessous un tableau qui explique si votre opération en bloc est entièrement ou minimale enregistrée :  

| Index primaire               | Scénario de charge                                            | Mode de journalisation |
| --------------------------- | -------------------------------------------------------- | ------------ |
| Segment de mémoire                        | Tout                                                      | **Un minimum**  |
| Index ordonné en clusters             | Table cible vide                                       | **Un minimum**  |
| Index ordonné en clusters             | Lignes chargées ne se chevauchent pas avec les pages existantes de la cible | **Un minimum**  |
| Index ordonné en clusters             | Chevauchement de lignes chargées avec les pages existantes de la cible        | Complet         |
| Index Columnstore ordonné en clusters | Taille de lot > = 102,400 par la distribution de la partition alignée | **Un minimum**  |
| Index Columnstore ordonné en clusters | Traitement par lots de taille < 102,400 par la distribution de la partition alignée  | Complet         |

Il est important de noter que toutes les écritures pour mettre à jour les index secondaires ou non mis en cluster sera toujours les opérations entièrement consignées.

> [AZURE.IMPORTANT] SQL Data Warehouse a 60 distributions. Par conséquent, en supposant que toutes les lignes sont réparties uniformément sur une seule partition de lancement, votre lot doit contenir des 6,144,000 lignes ou supérieure à consignées dans le journal lorsque vous écrivez dans un Columnstore Index ordonné en clusters. Si la table est partitionnée et les lignes insérées s’étendent sur les limites de la partition, vous devrez 6,144,000 lignes par limite de partition en supposant même la distribution des données. Chaque partition de chaque distribution doit dépasser indépendamment du seuil de ligne 102 400 d’insertion être journalisées dans la distribution.

Chargement de données dans une table non vide avec un index ordonné en clusters peut souvent contiennent un mélange de lignes entièrement consignées et journalisées. Un index ordonné en clusters est une arborescence équilibrée (b-tree) de pages. Si la page en cours d’écriture à contient déjà des lignes à partir d’une autre transaction, puis ces écritures seront entièrement connectés. Toutefois, si la page est vide puis l’écriture sur cette page est consignées dans le journal.

## <a name="optimizing-deletes"></a>Optimisation des suppressions

`DELETE`est une opération entièrement connectée.  Si vous avez besoin de supprimer une grande quantité de données dans une table ou d’une partition, il est souvent plus judicieux de `SELECT` les données que vous souhaitez conserver, et qui peuvent être exécutées comme une opération journalisée minimale.  Pour ce faire, créez une nouvelle table avec [SACT][].  Une fois créé, cliquez sur [Renommer][] pour permuter votre ancienne table avec la table nouvellement créée.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Optimisation des mises à jour

`UPDATE`est une opération entièrement connectée.  Si vous devez mettre à jour un grand nombre de lignes dans une table ou d’une partition, qu'il peut souvent être beaucoup plus efficace d’utiliser une opération consignée comme [SACT][] pour le faire.

Dans l’exemple ci-dessous, un tableau complet des mise à jour a été converti en un `CTAS` pour que la journalisation minimale est possible.

Dans ce cas nous ajoutons a posteriori un montant d’escompte de la vente de la table :

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(   CLUSTERED INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [AZURE.NOTE] Recréer des tables de grande taille peut bénéficier de l’utilisation des fonctionnalités de gestion de la charge de travail SQL Data Warehouse. Pour plus d’informations reportez-vous à la section Gestion de la charge de travail dans l’article de la [concurrence d’accès][] .

## <a name="optimizing-with-partition-switching"></a>Optimisation avec le changement de partition

Confrontés avec les modifications à grande échelle à l’intérieur d’une [partition de table][], une changement de modèle de partition rend très utile. Si la modification des données est importante et s’étend sur multiples partitions, puis simplement itérer sur les partitions permet d’obtenir le même résultat.

Les étapes pour effectuer une commutation de partition sont les suivantes :
1. Créer la partition vide
2. Effectuer la « mise à jour » sous la forme d’un SACT
3. Éliminer les données existantes à la table de sortie
4. Passer les nouvelles données
5. Nettoyer les données

Toutefois, pour aider à identifier les partitions pour basculer nous devez d’abord créer une procédure d’assistance semblable à celui présenté ci-dessous. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,   @table_name            NVARCHAR(128)
,   @boundary_value        INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
WITH CTE
AS
(
SELECT  s.name                          AS [schema_name]
,       t.name                          AS [table_name]
,       p.partition_number              AS [ptn_nmbr]
,       p.[rows]                        AS [ptn_rows]
,       CAST(r.[value] AS INT)          AS [boundary_value]
FROM        sys.schemas                 AS s
JOIN        sys.tables                  AS t    ON  s.[schema_id]       = t.[schema_id]
JOIN        sys.indexes                 AS i    ON  t.[object_id]       = i.[object_id]
JOIN        sys.partitions              AS p    ON  i.[object_id]       = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes       AS h    ON  i.[data_space_id]   = h.[data_space_id]
JOIN        sys.partition_functions     AS f    ON  h.[function_id]     = f.[function_id]
LEFT JOIN   sys.partition_range_values  AS r    ON  f.[function_id]     = r.[function_id] 
                                                AND r.[boundary_id]     = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT  *
FROM    CTE
WHERE   [schema_name]       = @schema_name
AND     [table_name]        = @table_name
AND     [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Cette procédure optimise la réutilisation de code et conserve la partition commutation exemple plus compact.

Le code ci-dessous montre les cinq étapes mentionnées ci-dessus pour atteindre une partition complète de commutation de routine.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE   OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]   SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))   +' TO [dbo].[FactInternetSales_out] PARTITION ' +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))   +' TO [dbo].[FactInternetSales] PARTITION '     +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Réduire l’enregistrement de lots de petite taille

Pour les opérations de modification de données de grande taille, il peut être judicieux diviser l’opération en segments ou les lots à portée l’unité de travail.

Un exemple est fourni ci-dessous. La taille de lot a été définie à un nombre insignifiant pour mettre en surbrillance de la technique. En réalité, la taille de lot serait beaucoup plus importante. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,       SalesOrderNumber
,       SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE   [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE @seq_start      INT = 1
,       @batch_iterator INT = 1
,       @batch_size     INT = 50
,       @max_seq_nmbr   INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,       @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE   @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT  1
            FROM    #t t
            WHERE   seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND     FactInternetSales.SalesOrderNumber      = t.SalesOrderNumber
            AND     FactInternetSales.SalesOrderLineNumber  = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Pause et conseils de mise à l’échelle

Entrepôt de données SQL Azure vous permet de mettre en pause, reprendre et mettre à l’échelle votre entrepôt de données à la demande. Lorsque vous suspendez ou mettre à l’échelle votre entrepôt de données SQL, il est important de comprendre que toutes les transactions en cours sont interrompues immédiatement ; à l’origine de toutes les transactions ouvertes à restaurer. Si votre charge de travail avait délivré une modification des données de longue durée et incomplète avant l’opération de la pause ou l’échelle, puis ce travail devez être annulée. Cela peut avoir un impact sur le temps que nécessaire pour mettre en pause ou mettre à l’échelle votre base de données de l’entrepôt de données SQL Azure. 

> [AZURE.IMPORTANT] Les deux `UPDATE` et `DELETE` sont des opérations entièrement consignées et donc annuler/rétablir ces opérations peuvent prendre beaucoup plus de temps que les opérations journalisées sur équivalent. 

Le meilleur scénario consiste à laisser dans les transactions de modification des données vol complets avant la suspension ou de mise à l’échelle de SQL Data Warehouse. Toutefois, ceci ne peut pas toujours être pratique. Pour atténuer le risque d’une restauration longue, envisagez une des options suivantes :

- Réécriture des opérations de longue durée à l’aide de [SACT][]
- L’opération de les décomposer en segments ; fonctionne sur un sous-ensemble de lignes

## <a name="next-steps"></a>Étapes suivantes

Consultez [Transactions SQL Data Warehouse][] pour en savoir plus sur les niveaux d’isolement et de limites transactionnelles.  Pour une vue d’ensemble des autres pratiques recommandées, consultez méthodes [Conseillées entrepôt de données SQL][].

<!--Image references-->

<!--Article references-->
[Transactions dans le magasin de données SQL]: ./sql-data-warehouse-develop-transactions.md
[partition de table]: ./sql-data-warehouse-tables-partition.md
[Concurrence d’accès]: ./sql-data-warehouse-develop-concurrency.md
[SACT]: ./sql-data-warehouse-develop-ctas.md
[Entrepôt de données SQL meilleures pratiques]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[alter index]:https://msdn.microsoft.com/library/ms188388.aspx
[CHANGEMENT DE NOM]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->

