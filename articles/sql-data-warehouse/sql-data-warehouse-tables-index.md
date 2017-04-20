<properties
   pageTitle="L’indexation des tables SQL Data Warehouse | Microsoft Azure"
   description="Mise en route de la table d’indexation dans le magasin de données SQL Azure."
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
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="indexing-tables-in-sql-data-warehouse"></a>L’indexation des tables SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

SQL Data Warehouse propose plusieurs options d’indexation, y compris [les index columnstore ordonné en clusters][], [index ordonnés en clusters et les index non ordonnés en clusters][].  En outre, il propose également une option d’index sans également connu sous le nom de [segment de mémoire][].  Cet article décrit les avantages de chaque type d’index, ainsi que des conseils pour obtenir les meilleures performances de votre index. Comment créer une table dans SQL Data Warehouse, consultez [créer une syntaxe de tableau][] pour plus de détails.

## <a name="clustered-columnstore-indexes"></a>Index ordonnés en clusters columnstore

Par défaut, SQL Data Warehouse crée un index ordonné en clusters columnstore lorsque aucune des options d’index ne sont spécifiées sur une table. Tables de columnstore en clusters offrent le plus haut niveau de compression des données ainsi que les meilleures performances de requête globale.  Tables de columnstore en cluster seront maintiennent généralement des tables d’index ou le segment en cluster et sont généralement le meilleur choix pour les tables volumineuses.  Pour ces raisons, columnstore en cluster est le meilleur endroit pour démarrer lorsque vous ne savez pas comment votre table d’index.  

Pour créer une table en cluster columnstore, spécifier des INDEX COLUMNSTORE ordonné en clusters dans la clause WITH, ou laisser la clause WITH :

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Il existe plusieurs scénarios où les columnstore en cluster peut ne pas être une bonne option :

- Les tables ColumnStore ne supportent pas les index non cluster secondaires.  Envisagez plutôt de segment de mémoire ou des tables d’index ordonné en clusters.
- Les tables ColumnStore ne supportent pas varchar (max), nvarchar (max) et varbinary (max).  Envisagez plutôt de segment de mémoire ou index ordonné en clusters.
- Tables de ColumnStore peuvent être moins efficaces pour les données temporaires.  Envisagez de tas et tables voire temporaires.
- Tables de petite taille avec moins de 100 millions de lignes.  Tenez compte des tables de segment de mémoire.

## <a name="heap-tables"></a>Tables de segments

Lorsque vous sont temporairement débarquement des données dans l’entrepôt de données SQL, vous pouvez constater qu’à l’aide d’une table de tas rendra le processus plus rapide.  C’est pourquoi les charges pour les segments sont plus rapides que pour les index de tables et, dans certains cas, que la lecture suivante peut être effectuée à partir du cache.  Si vous chargez des données uniquement pour elle avant d’exécuter des transformations plus, chargement de la table pour la table de segments de mémoire sera beaucoup plus rapide que le chargement des données à une table en cluster columnstore. En outre, le chargement des données à une [table temporaire][temporaire] chargera également beaucoup plus rapide que le chargement d’une table dans un stockage permanent.  

Petites tables de choix, 100 millions de lignes, souvent les tables de segments de sens.  Tables de columnstore de cluster commencent à atteindre compression optimal, une fois qu’il existe plus de 100 millions de lignes.

Pour créer une table de segment de mémoire, vous devez simplement spécifier tas dans la clause WITH :

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Index ordonnés et non ordonnés en clusters

Index ordonnés en clusters peuvent maintiennent les tables columnstore ordonné en clusters lorsqu’une seule ligne doit être récupérées rapidement.  Pour les requêtes où une seule ou très peu recherche de ligne est requis pour des performances extrêmes vitesse, pensez à un index de cluster ou un index secondaire non ordonné.  L’inconvénient de l’utilisation d’un index ordonné en clusters est que seules les requêtes qui utilisent un filtre hautement sélectif sur la colonne de l’index ordonné en clusters bénéficiera.  Pour améliorer le filtre sur d’autres colonnes, un index non ordonné en clusters peut être ajouté à d’autres colonnes.  Cependant, chaque index qui est ajouté à une table ajoutera d’espace et de temps de traitement à charges.

Pour créer une table de l’index ordonné en clusters, vous devez simplement spécifier les INDEX ordonnés en clusters dans la clause WITH :

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Pour ajouter un index non ordonné en clusters sur une table, utilisez la syntaxe suivante :

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

> [AZURE.NOTE] Un index non ordonné en clusters est créé par défaut lorsque vous créez un INDEX est utilisé. En outre, un index non ordonné en clusters n’est autorisé que sur une table de stockage des lignes (segment de mémoire ou INDEX ordonné en clusters). Un index non ordonné en clusters sur un INDEX COLUMNSTORE ordonné en clusters n’est pas autorisée pour l’instant.


## <a name="optimizing-clustered-columnstore-indexes"></a>Optimisation des index de cluster columnstore

Tables de columnstore en cluster sont organisés dans les données en segments.  Qualité du segment élevée est essentielle pour obtenir des performances optimales de requête sur une table columnstore.  Qualité du segment peut être mesurée par le nombre de lignes dans un groupe de lignes compressé.  Qualité de segment est optimale dans le cas où il y a au moins des lignes de 100 Ko par ligne compressé de groupe et de gagner en performance comme le nombre de lignes par approche de groupe de ligne 1 048 576 lignes, qui est un groupe de lignes peut contenir la plupart des lignes.

Le dessous de vue peut être créée et permettent de calculer les lignes moyennes par ligne groupe et identifient n’importe quel index de columnstore de cluster optimale sur votre système.  La dernière colonne sur cette vue génère en tant qu’instruction SQL qui peut être utilisée pour reconstruire votre index.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,   COUNT(DISTINCT rg.[partition_number])                   AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,   CEILING ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Maintenant que vous avez créé l’affichage, exécutez cette requête pour identifier les tables avec des groupes de lignes avec moins de lignes de 100 Ko.  Bien sûr, vous souhaiterez augmenter le seuil de 100 Ko si vous recherchez pour une qualité optimale de segment plus. 

```sql
SELECT  *
FROM    [dbo].[vColumnstoreDensity]
WHERE   COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Une fois que vous avez exécuté la requête, que vous pouvez commencer à examiner les données et analyser les résultats. Ce tableau explique ce que vous recherchez dans l’analyse de groupe de ligne.


| Colonne                             | L’utilisation de ces données                                                                                                                                                                      |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table_partition_count]            | Si la table est partitionnée, vous devriez voir le compte du groupe d’ouvrir ligne supérieure. Chaque partition de la distribution peut en théorie avoir un groupe d’ouvrir ligne lui est associé. Tenir compte de cela dans votre analyse. Une petite table a été partitionnée peut être optimisée en supprimant le partitionnement complètement car cela améliore la compression.                                                                        |
| [row_count_total]                  | Nombre total de lignes de la table. Par exemple, vous pouvez utiliser cette valeur pour calculer le pourcentage de lignes de l’état compressé.                                                                      |
| [row_count_per_distribution_MAX]   | Si toutes les lignes sont répartis de cette valeur est le nombre de cibles de lignes par distribution. Comparer cette valeur avec le compressed_rowgroup_count.                                 |
| [COMPRESSED_rowgroup_rows]         | Nombre total de lignes dans le format columnstore pour la table.                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_AVG]     | Si le nombre moyen de lignes est sensiblement inférieur au nombre maximal de lignes pour un groupe de lignes, puis utilisez SACT ou ALTER INDEX REBUILD pour recompresser les données                     |
| [COMPRESSED_rowgroup_count]        | Nombre de groupes de lignes dans un format de columnstore. Si ce nombre est très élevé par rapport à la table, il est un indicateur que la densité de columnstore est faible.                                  |
| [COMPRESSED_rowgroup_rows_DELETED] | Les lignes sont supprimées logiquement sous forme de columnstore. Si le nombre est élevé par rapport à la taille de la table, envisagez de recréer la partition ou la reconstruction d’un index comme cela les supprime physiquement. |
| [COMPRESSED_rowgroup_rows_MIN]     | Utilisez-le conjointement avec les colonnes AVG et MAX pour comprendre la plage de valeurs pour les groupes de lignes dans votre columnstore. Un nombre faible dépasse le seuil de chargement (102,400 par la distribution de la partition alignée) suggère que les optimisations sont disponibles dans le chargement de données                                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_MAX]     | Comme ci-dessus                                                                                                                                                                                  |
| [OPEN_rowgroup_count]              | Groupes de lignes ouverts sont normaux. On peut raisonnablement s’attendre un groupe d’ouvrir ligne par distribution de tables (60). Un nombre excessif suggérer le chargement sur des partitions de données. Vérifiez la stratégie de partitionnement pour vous assurer qu’elle est saine                                                                                                                                                                                                |
| [OPEN_rowgroup_rows]               | Chaque groupe de lignes peut avoir 1 048 576 lignes qu’il contient au maximum. Utilisez cette valeur pour voir comment complète les groupes de lignes ouvertes                                                                 |
| [OPEN_rowgroup_rows_MIN]           | Groupes ouverts indiquent que les données sont soit Percoler chargées dans la table ou que la charge précédente sont répandues sur lignes restantes dans ce groupe de lignes. Vous pouvez utiliser le MIN, MAX, colonnes AVG pour voir la quantité de données est Assi dans ouvrir des groupes de lignes. Pour les petites tables, il pourrait être 100 % de toutes les données ! Auquel cas ALTER INDEX REBUILD pour forcer les données à columnstore.                                                                       |
| [OPEN_rowgroup_rows_MAX]           | Comme ci-dessus                                                                                                                                                                                  |
| [OPEN_rowgroup_rows_AVG]           | Comme ci-dessus                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows]             | Examinez les lignes de groupe de ligne fermée sous la forme d’un contrôle de validité.                                                                                                                                       |
| [CLOSED_rowgroup_count]            | Le nombre de groupes de lignes fermées doit être faible si les sont visibles du tout. Groupes de lignes fermé peuvent être convertis en roups rowg compressé à l’aide de l’instruction ALTER INDEX... RÉORGANISER les commandes. Toutefois, cela n’est pas normalement nécessaire. Groupes fermés sont automatiquement convertis en columnstore les groupes de lignes par le processus de « tuple mover » en arrière-plan.                                                                                               |
| [CLOSED_rowgroup_rows_MIN]         | Groupes de lignes fermées doivent avoir un taux de remplissage très élevé. Si le taux de remplissage pour un groupe de lignes fermé est faible, puis une analyse plus approfondie de la columnstore est requise.                                   |
| [CLOSED_rowgroup_rows_MAX]         | Comme ci-dessus                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows_AVG]         | Comme ci-dessus                                                                                                                                                                                  |
| [Rebuild_Index_SQL]         | SQL pour reconstruire des index columnstore d’une table                                                                                                                                                     |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causes de la qualité des index columnstore faible

Si vous avez identifié des tables avec une qualité médiocre de segment, vous devez identifier la cause.  Voici certaines autres causes courantes du segment médiocre quaility :

1. Pression de mémoire lorsque l’index a été créé.
2. Volume élevé des opérations DML
3. Petit ou segmenter les opérations de chargement
4. Trop de partitions

Ces facteurs peuvent provoquer un index columnstore avoir considérablement inférieure à la 1 million de lignes optimale par groupe de lignes.  Ils peuvent également entraîner des lignes atteindre le groupe de lignes delta au lieu d’un groupe de lignes compressé. 

### <a name="memory-pressure-when-index-was-built"></a>Pression de mémoire lorsque l’index a été créé.

Le nombre de lignes par groupe de lignes compressés est directement lié à la largeur de la ligne et la quantité de mémoire disponible pour traiter le groupe de lignes.  Lorsque les lignes sont écrites dans les tables de columnstore sous la pression de mémoire, qualité du segment columnstore risquent d’être affectées.  Par conséquent, la meilleure pratique consiste à donner à la session qui est écrit à l’accès de tables d’index columnstore à autant de mémoire que possible.  Dans la mesure où il existe un compromis entre la mémoire et d’accès concurrentiel, les directives sur l’allocation de mémoire de droite dépend des données dans chaque ligne de la table, la quantité de DWU vous avez alloué à votre système et la quantité des emplacements d’accès concurrentiel que vous pouvez donner à la session qui écrit des données dans votre table.  Pour obtenir les meilleurs résultats, nous vous recommandons de xlargerc si vous utilisez DW300 ou moins, largerc si vous utilisez DW400, DW600 et mediumrc si vous utilisez DW1000 et au-dessus.

### <a name="high-volume-of-dml-operations"></a>Volume élevé des opérations DML

Un volume important de mettre à jour et supprimer des lignes d’opérations DML peut introduire des inefficacité dans le columnstore. Cela est particulièrement vrai lorsque la majorité des lignes dans un groupe de lignes sont modifiés.

- Suppression d’une ligne à partir d’un groupe de lignes compressé uniquement logiquement de marque la ligne comme étant supprimé. La ligne reste dans le groupe ligne compressé jusqu'à ce que la partition ou la table est reconstruit.
- Insertion d’une ligne d’ajoute la ligne à une table rowstore interne appelée un groupe de lignes de delta. La ligne insérée n’est pas convertie à columnstore jusqu'à ce que le groupe de lignes delta est plein et est marqué comme clôturée. Groupes de lignes sont fermés une fois qu’ils atteignent la capacité maximale de 1 048 576 lignes. 
- Mise à jour d’une ligne dans le format de columnstore est traitée comme une suppression logique, puis une instruction insert. La ligne insérée peut-être être stockée dans le magasin de delta.

Mise à jour groupée et insérer les opérations qui dépassent le seuil en bloc de 102 400 lignes par partition alignée de distribution sera écrit directement dans le format columnstore. Toutefois, en supposant une répartition uniforme, vous devez modifier plusieurs de 6.144 millions de lignes en une seule opération pour ce faire. Si le nombre de lignes d’une partition donnée alignés distribution est inférieure à 102,400 les lignes iront à la banque delta et il y restera jusqu'à ce que suffisamment de lignes ont été insérées ou modifiées pour fermer le groupe de lignes ou de l’index a été reconstruit.

### <a name="small-or-trickle-load-operations"></a>Petit ou segmenter les opérations de chargement

Petite charge que flux dans SQL Data Warehouse sont aussi parfois appelés Percoler les charges. Ils représentent généralement un flux constant proche de données d’être ingérés par le système. Toutefois, comme ce flux est presque continue le volume de lignes n’est pas très grand. La plupart du temps, les données sont nettement en deçà du seuil requis pour une charge directe au format de columnstore.

Dans ces situations, il est souvent préférable de débarquer tout d’abord les données dans le stockage blob Azure et laisser s’accumuler avant le chargement. Cette technique est souvent appelée *micro-le traitement par lots*.

### <a name="too-many-partitions"></a>Trop de partitions

Une autre chose à prendre en compte est l’impact du partitionnement sur vos tables columnstore ordonné en clusters.  Avant de partitionner, SQL Data Warehouse divise déjà vos données dans les bases de données de 60.  Partitionnement divise encore vos données.  Si vous partitionnez vos données, vous devez considérer que **chaque** partition doit avoir au moins 1 million de lignes de bénéficier d’un index ordonné en clusters columnstore.  Si vous partitionnez votre table en 100 partitions, puis votre table doit disposer au moins de 6 milliards de lignes de bénéficier d’un index ordonné en clusters columnstore (lignes *100 partitions* 1 million de 60 distributions). Si votre table de 100 partition n’a pas de lignes de 6 milliards, réduisez le nombre de partitions ou envisagez d’utiliser une table de segment de mémoire.

Une fois que les tables ont été chargés avec des données, suivez la suit d’identifier et de recréer les tables avec des index columnstore de cluster optimale.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Reconstruction des index pour améliorer la qualité de segment

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Étape 1 : Identifier ou créer l’utilisateur qui utilise la classe de la ressource de droite

Un moyen rapide pour améliorer immédiatement la qualité de segment est de reconstruire l’index.  Le SQL renvoyée par la vue ci-dessus renvoie une instruction ALTER INDEX REBUILD, qui peut être utilisée pour reconstruire votre index.  Lors de la reconstruction de votre index, n’oubliez pas que vous allouez suffisamment de mémoire pour la session qui reconstruit votre index.  Pour ce faire, augmentez la classe de ressource d’un utilisateur qui dispose d’autorisations de reconstruire l’index sur cette table à la valeur minimale recommandée.  La classe de ressource de l’utilisateur propriétaire de base de données ne peut pas être modifiée, si vous n’avez pas créé un utilisateur sur le système, vous devez faire en premier.  Le minimum recommandé est xlargerc si vous utilisez DW300 ou moins, largerc si vous utilisez DW400, DW600 et mediumrc si vous utilisez DW1000 et au-dessus.

Voici un exemple de comment allouer plus de mémoire à un utilisateur en augmentant leur classe de ressource.  Pour plus d’informations sur la ressource de classes et la création d’un nouvel utilisateur sont accessibles dans la [Gestion d’accès concurrentiel et la charge de travail] [ Concurrency] l’article.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Étape 2 : Reconstruire les index columnstore ordonné en clusters avec supérieur utilisateur de classe de ressource
Ouverture de session en tant que l’utilisateur de l’étape 1 (par exemple, LoadUser), qui est maintenant à l’aide d’une classe de ressource plus élevée et exécuter les instructions ALTER INDEX.  N’oubliez pas que cet utilisateur a l’autorisation ALTER dans les tables où l’index est en cours de reconstruction.  Ces exemples montrent comment reconstruire l’index columnstore ensemble ou reconstruire une partition unique. Sur les tables de grande taille, il est plus pratique de ne pas régénérer indexe une seule partition à la fois.

Également, au lieu de la reconstruction de l’index, vous pouvez copier la table dans une nouvelle table à l’aide de [SACT][].  De quelle façon consiste le mieux ? Pour des volumes importants de données, [SACT][] est généralement plus rapide que [l’Instruction ALTER INDEX][]. De petits volumes de données, [ALTER INDEX][] est plus facile à utiliser et ne vous obligent à remplacer la table.  Voir **index de reconstruction avec le SACT et le changement de partition** ci-dessous pour plus de détails sur la procédure pour reconstruire des index avec SACT.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Reconstruction d’un index dans SQL Data Warehouse est une opération hors connexion.  Pour plus d’informations sur la reconstruction d’index, reportez-vous à la section ALTER INDEX REBUILD dans la rubrique syntaxe [ALTER INDEX][]et de [Défragmentation d’index Columnstore][] .
 
### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Étape 3 : Vérifier la qualité du segment columnstore ordonné en clusters a amélioré
Réexécuter la requête quelle table identifiée avec une mauvaise qualité de segment et vérifier la qualité du segment a été améliorée.  Si la qualité du segment n’ont pas amélioré, peut-être que les lignes de votre table sont très larges.  Envisagez d’utiliser une classe de ressource supérieure ou un DWU lors de la reconstruction de votre index.

 
## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Reconstruction des index avec SACT et le changement de partition

Cet exemple utilise [SACT][] et commutation pour recréer une partition de table de partition. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Create a SWITCH out table
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
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Pour plus d’informations sur la recréation des partitions à l’aide de `CTAS`, consultez l’article de la [Partition][] .

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles sur la [Vue d’ensemble de la Table][vue d’ensemble],[Des Types de données]de la [Table des Types de données], [distribution d’une Table de][répartir], [partitionnement d’une Table]de[Partition], [Mise à jour des statistiques de Table][statistiques] et[temporaire] [Des Tables temporaires].  Pour en savoir plus sur les méthodes conseillées, consultez méthodes [Conseillées entrepôt de données SQL][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble]: ./sql-data-warehouse-tables-overview.md
[Types de données]: ./sql-data-warehouse-tables-data-types.md
[Distribuer]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[Temporaire]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[SACT]: ./sql-data-warehouse-develop-ctas.md
[Entrepôt de données SQL meilleures pratiques]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx
[segment de mémoire]: https://msdn.microsoft.com/library/hh213609.aspx
[index ordonnés en clusters et les index non ordonnés en clusters]: https://msdn.microsoft.com/library/ms190457.aspx
[créer la table, syntaxe]: https://msdn.microsoft.com/library/mt203953.aspx
[Défragmentation des index de ColumnStore]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[index ordonnés en clusters columnstore]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
