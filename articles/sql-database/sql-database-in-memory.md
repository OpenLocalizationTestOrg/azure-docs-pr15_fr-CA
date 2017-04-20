<properties
    pageTitle="SQL en mémoire, mise en route | Microsoft Azure"
    description="Technologies de mémoire dans SQL améliorent considérablement les performances de transactionnelle et les charges de travail analytique. Apprenez à tirer parti de ces technologies."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jodebrui"/>


# <a name="get-started-with-in-memory-preview-in-sql-database"></a>Mise en route dans la mémoire (aperçu) de la base de données SQL

Les fonctionnalités en mémoire améliorent considérablement les performances de transactionnelle et les charges de travail analytique dans les situations de droite.

Cette rubrique met l’accent sur deux démonstrations, une pour les OLTP en mémoire et l’autre pour en mémoire Analytique. Chaque démonstration est livré avec les étapes et le code, que vous devez exécuter la démonstration. Vous pouvez soit :

- Le code permet de tester des variantes pour voir les différences dans les résultats de performances ; ou
- Lisez le code pour comprendre le scénario et pour savoir comment créer et utiliser des objets en mémoire.

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- [1 de démarrage rapide : Technologies de OLTP en mémoire pour accélérer les performances de T-SQL](http://msdn.microsoft.com/library/mt694156.aspx) -est un autre article pour vous aider à démarrer.

#### <a name="in-memory-oltp"></a>En mémoire OLTP

Les fonctionnalités d’en mémoire [OLTP](#install_oltp_manuallink) (traitement des transactions en ligne) sont les suivantes :

- Tables de mémoire optimisée.
- En mode natif compilé des procédures stockées.


Une table de mémoire optimisée a une seule représentation de lui-même dans la mémoire active, en plus de la représentation standard sur un disque dur. Transactions commerciales par rapport à la table de s’exécuter plus rapidement car elles interagissent directement avec uniquement la représentation dans la mémoire active.

Avec les OLTP en mémoire, vous pouvez obtenir à prendre 30 fois le débit des transactions, selon les caractéristiques de la charge de travail.


Procédures stockées compilées en mode natif nécessitent des instructions machine moins au moment de l’exécution qu’interprétées traditionnelle des procédures stockées. Nous avons vu les résultats de compilation native dans les durées qui sont de 1/100 de la durée à interpréter.


#### <a name="in-memory-analytics"></a>En mémoire Analytique 

La fonctionnalité de mémoire [Analytique](#install_analytics_manuallink) est :

Index de ColumnStore améliorent les performances d’analytique et les requêtes de rapport. 


#### <a name="real-time-analytics"></a>Analytique en temps réel

Pour [En temps réel Analytique](http://msdn.microsoft.com/library/dn817827.aspx) , vous combinez en mémoire OLTP et Analytique pour obtenir :

- Insight d’entreprise en temps réel basée sur des données opérationnelles.


#### <a name="availability"></a>Disponibilité


GA, disponibilité générale :

- [Index de Columnstore](http://msdn.microsoft.com/library/dn817827.aspx) qui se trouvent *sur le disque*.


Aperçu :

- En mémoire OLTP
- Analytique d’exploitation en temps réel


Considérations alors que les fonctionnalités en mémoire sont en aperçu sont décrites [plus loin dans cette rubrique](#preview_considerations_for_in_memory).


> [AZURE.NOTE] Ces fonctionnalités sont disponibles uniquement pour les bases de données de [*prime*](sql-database-service-tiers.md) pas dans des pools d’élastiques et non disponible pour les bases de données de base ou Standard.  Prise en charge des bases de données de prime de pools élastiques sera bientôt disponible. 


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="a-install-the-in-memory-oltp-sample"></a>A. Installez l’exemple OLTP de In-Memory

Vous pouvez créer la base de données AdventureWorksLT [V12] en quelques clics dans [Azure portal](https://portal.azure.com/). Puis les étapes de cette section expliquent comment vous pouvez enrichir votre base de données AdventureWorksLT avec :

- Tables en mémoire.
- Une procédure stockée compilée de manière native.


#### <a name="installation-steps"></a>Étapes d’installation

1. Dans le [portail Azure](https://portal.azure.com/), créer une base de données Premium sur un serveur de V12. Affectez à la **Source de** la base de données AdventureWorksLT [V12].
 - Pour obtenir des instructions détaillées, vous pouvez voir [créer votre première base de données SQL d’Azure](sql-database-get-started.md).

2. Se connecter à la base de données avec SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copiez le [script de Transact-SQL de OLTP en mémoire](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) dans votre Presse-papiers.
 - Le script T-SQL crée les objets en mémoire nécessaire dans la base de données AdventureWorksLT créé à l’étape 1.

4. Collez le script T-SQL dans SSMS et puis exécuter le script.
 - Crucial est la `MEMORY_OPTIMIZED = ON` les instructions CREATE TABLE clause, comme dans :


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Erreur 40536


Si vous obtenez une erreur 40536 lorsque vous exécutez le script T-SQL, exécutez le script T-SQL suivant pour vérifier si la base de données prend en charge en mémoire :


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Un résultat de **0** signifie en mémoire n’est pas pris en charge, et la valeur 1 signifie qu’il est pris en charge. Pour diagnostiquer le problème :

- Assurez-vous que la base de données a été créé après que les fonctions In-Memory OLTP est devenue actives pour l’aperçu.
- Assurez-vous que la base de données est au niveau de service Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Sur les éléments de mémoire optimisée créés

**Tables**: l’échantillon contienne les tables de mémoire optimisée suivantes :

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Vous pouvez inspecter la mémoire optimisée des tables par le biais de l' **Explorateur d’objets** dans SSMS par :

- Droit de **Tables** > **filtre** > **Les paramètres de filtre** > **Optimisé de mémoire est** égal à 1.


Ou bien, vous pouvez interroger les vues de catalogue telles que :


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procédure stockée compilée en mode natif**: SalesLT.usp_InsertSalesOrder_inmem peut être inspectée via une requête de vue de catalogue :


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

## <a name="run-the-sample-oltp-workload"></a>Exécuter la charge de travail OLTP exemple

La seule différence entre les deux suivantes *des procédures stockées* est que la première procédure utilise des versions optimisées de mémoire des tables, tandis que la deuxième procédure utilise des tableaux sur le disque :

- SalesLT**.** usp_InsertSalesOrder**_inmem**
- SalesLT**.** usp_InsertSalesOrder**_ondisk**


Dans cette section, vous allez apprendre à utiliser l’utilitaire pratique **ostress.exe** pour exécuter les procédures stockées de deux niveaux stressantes. Vous pouvez comparer la durée les exécutions de deux contraintes pour terminer.


Lorsque vous exécutez ostress.exe, nous recommandons que vous passez des valeurs de paramètre conçus à la fois :

- Exécuter un grand nombre de connexions simultanées, à l’aide de-n100.
- A chaque boucle de connexion des centaines de fois, en utilisant - r500.


Toutefois, vous souhaiterez peut-être commencer par beaucoup plus petites valeurs comme - n10 et - r50 afin de garantir le tout fonctionne.


### <a name="script-for-ostressexe"></a>Script de ostress.exe


Cette section affiche le script T-SQL qui est incorporé dans notre ligne de commande ostress.exe. Le script utilise les éléments qui ont été créés par le script T-SQL que vous avez installé précédemment.


Le script suivant insère un exemple de commande vente avec cinq éléments de ligne dans la mémoire optimisée *tables*suivantes :

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;
    
INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);
    
WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Pour rendre la version _ondisk de la T-SQL précédente pour ostress.exe, remplacez simplement les deux occurrences de la sous-chaîne de *_inmem* par *_ondisk*. Ces remplace affecte les noms des tables et des procédures stockées.


### <a name="install-rml-utilities-and-ostress"></a>Installation des utilitaires par Intel et ostress


Dans l’idéal souhaitez que vous souhaitez exécuter le ostress.exe sur un ordinateur virtuel d’Azure. Vous devez créer un [ordinateur virtuel de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) dans la même région géographique Azure l’emplacement de votre base de données AdventureWorksLT. Mais vous pouvez exécuter ostress.exe sur votre ordinateur portable à la place.


Sur la machine virtuelle, ou quel que soit l’hôte vous choisir, installer les utilitaires de langue recommandés (relecture balisage par), qui incluent ostress.exe.

- Consultez la description de ostress.exe dans la [Base de données exemple en mémoire OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
 - Ou, consultez la [Base de données exemple en mémoire OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
 - Ou consultez [le Blog d’installation ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Exécutez d’abord la charge de travail du stress _inmem


Vous pouvez utiliser une fenêtre *d’Invite de commande par Intel* pour exécuter notre ligne de commande ostress.exe. Les paramètres de ligne de commande directement ostress à :

- Exécution simultanée de 100 connexions (-n100).
- A chaque connexion, exécutée le script T-SQL 50 fois (-r 50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Pour exécuter la ligne de commande ostress.exe précédente :


1. Réinitialiser le contenu des données de base de données en exécutant la commande suivante dans SSMS, supprimer toutes les données qui ont été insérées par les exécutions précédentes :
```
EXECUTE Demo.usp_DemoReset;
```

2. Copier le texte de la ligne de commande ostress.exe précédente dans le Presse-papiers.

3. Remplacer le `<placeholders>` pour les paramètres -S - U -P -d avec les valeurs correctes de réels.

4. Exécutez votre ligne de commande modifiée dans une fenêtre recommandés par Cmd.


#### <a name="result-is-a-duration"></a>Il en résulte une durée


Lorsque ostress.exe est terminée, il écrit la durée d’exécution en tant que sa dernière ligne de sortie dans la fenêtre recommandés par Cmd. Par exemple, une série de tests plus courte a duré environ 1,5 minutes :

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Réinitialiser, modifier pour _ondisk, puis exécutez à nouveau


Une fois que le résultat de l’exécution de _inmem, effectuez les opérations suivantes pour l’exécution de _ondisk :


1. Réinitialisation de la base de données en exécutant la commande suivante dans SSMS, supprimer toutes les données qui a été insérées par l’exécution précédente :
```
EXECUTE Demo.usp_DemoReset;
```

2. Modifiez la ligne de commande ostress.exe pour remplacer tous les *_inmem* *_ondisk*.

3. Réexécuter la ostress.exe pour la seconde fois et capturer le résultat de la durée.

4. À nouveau, réinitialiser la base de données, suppression de responsable de ce que peut être une grande quantité de données de test.


#### <a name="expected-comparison-results"></a>Résultats de la comparaison attendu

Nos tests en mémoire ont montré une amélioration des performances **9 fois** pour cette charge de travail simple, avec ostress en cours d’exécution sur un ordinateur virtuel d’Azure dans la même région Azure comme la base de données.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## <a name="b-install-the-in-memory-analytics-sample"></a>B. Installez l’exemple en mémoire Analytique


Dans cette section, vous comparez les résultats statistiques et d’e/s lors de l’utilisation d’un index columnstore par rapport à un index b-tree traditionnel.


Pour analytique en temps réel sur une charge de travail OLTP, il est souvent préférable d’utiliser un index non ordonné en clusters columnstore. Pour plus d’informations, consultez [Index de Columnstore décrites](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Préparer le test analytique columnstore


1. Le portail Azure permet de créer une nouvelle base de données AdventureWorksLT à partir de l’exemple.
 - Utilisez ce nom exact.
 - Choisissez n’importe quel niveau de service Premium.

2. Copier le [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) dans le Presse-papiers.
 - Le script T-SQL crée les objets en mémoire nécessaire dans la base de données AdventureWorksLT créé à l’étape 1.
 - Le script crée la table de Dimension et deux tables de faits. Les tables de faits sont équipés de 3,5 millions de lignes de chaque.
 - Le script peut prendre 15 minutes.

3. Collez le script T-SQL dans SSMS et puis exécuter le script.
 - Essentiel est le mot clé **COLUMNSTORE** dans une instruction **CREATE INDEX** , comme dans :<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Niveau de compatibilité de 130 la valeur AdventureWorksLT :<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - 130 du niveau n’est pas directement liée à des fonctions en mémoire. Mais le niveau de 130 offre généralement les performances des requêtes que 120.


#### <a name="crucial-tables-and-columnstore-indexes"></a>Essentiels tables et index de columnstore


- dbo. FactResellerSalesXL_CCI est une table qui possède un index ordonné en clusters **columnstore** , qui a avancé de compression au niveau des *données* .

- dbo. FactResellerSalesXL_PageCompressed est une table qui a un équivalent index ordonné en clusters standard, qui est compressé uniquement au niveau de la *page* .


#### <a name="crucial-queries-to-compare-the-columnstore-index"></a>Requêtes cruciales pour comparer l’index columnstore


[Ici](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) sont de plusieurs types de requête T-SQL que vous pouvez exécuter pour voir les améliorations de performances. À l’étape 2 dans le script T-SQL, il existe une paire de requêtes qui présentent un intérêt direct. Les deux requêtes diffèrent uniquement sur une seule ligne :


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Un index ordonné en clusters columnstore est sur la table**_CCI** de FactResellerSalesXL.

L’extrait de script T-SQL suivant imprime les statistiques d’e/s et de temps de la requête de chaque table.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## <a name="preview-considerations-for-in-memory-oltp"></a>Considérations relatives à la prévisualisation pour en mémoire OLTP


Les fonctions In-Memory OLTP dans la base de données de SQL Azure est devenue [active pour l’aperçu sur le 28 octobre 2015](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


Dans l’aperçu en cours, en mémoire OLTP est pris en charge uniquement pour :

- Bases de données à un niveau de service *Premium* .

- Bases de données qui ont été créées après les fonctions In-Memory OLTP est devenue actives.
 - Une nouvelle base de données ne peut pas prendre en charge en mémoire OLTP si elle est restaurée à partir d’une base de données qui a été créé avant que les fonctions In-Memory OLTP est devenue actives.


En cas de doute, vous pouvez toujours exécuter à sélectionner les T-SQL suivante pour vérifier si votre base de données prend en charge en mémoire OLTP. Un résultat de **1** signifie que la base de données ne prend pas en charge les OLTP en mémoire :

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Si la requête renvoie la valeur **1**, en mémoire OLTP est pris en charge dans cette base de données, et toute copie de base de données et une restauration de base de données créée en fonction de cette base de données.


#### <a name="objects-allowed-only-at-premium"></a>Objets n'autorisées qu’à la prime


Si une base de données contient l’un des types suivants d’objets de mémoire OLTP ou de types, la mise à niveau de la couche de service de la base de données à partir de la prime soit Standard ou de base n’est pas pris en charge. Pour mettre à niveau la base de données, d’abord supprimer ces objets :

- Tables de mémoire optimisée
- Types de tables de mémoire optimisée
- Modules compilés en mode natif


#### <a name="other-relationships"></a>Autres relations


- Fonctionnalités OLTP d’en mémoire à l’aide des bases de données dans des pools d’élasticité n’est pas pris en charge lors de l’aperçu.
 - Pour déplacer une base de données qui a ou a eu des objets de mémoire OLTP vers un pool élastique, procédez comme suit :
  - 1. Supprimer tout mémoire optimisée des tables, des types de tableau et modules de T-SQL compilés de manière native dans la base de données
  - 2. Modifier le niveau de service de la base de données standard
  - 3. Déplacer la base de données dans le pool élastique

- À l’aide de OLTP en mémoire avec SQL Data Warehouse n’est pas pris en charge.
 - La fonctionnalité d’index columnstore de mémoire Analytique est pris en charge dans le magasin de données SQL.

- La banque de requête ne capture pas les requêtes intérieur compilé en mode natif les modules.

- Certaines fonctions Transact-SQL ne sont pas pris en charge avec les OLTP en mémoire. Cela s’applique à Microsoft SQL Server et de base de données de SQL Azure. Pour plus d’informations, voir :
 - [Prise en charge de Transact-SQL pour en mémoire OLTP](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Constructions Transact-SQL non pris en charge par les OLTP en mémoire](http://msdn.microsoft.com/library/dn246937.aspx)


## <a name="next-steps"></a>Étapes suivantes


- Essayez [OLTP d’en mémoire d’utilisation dans une Application de SQL Azure existante.](sql-database-in-memory-oltp-migration.md)


## <a name="additional-resources"></a>Ressources supplémentaires

#### <a name="deeper-information"></a>Informations plus profondes

- [En savoir plus sur OLTP en mémoire, qui s’applique à Microsoft SQL Server et de base de données de SQL Azure](http://msdn.microsoft.com/library/dn133186.aspx)

- [En savoir plus sur l’Analytique de fonctionnement en temps réel sur MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- Livre blanc sur [les modèles de charge de travail et les considérations sur la Migration](http://msdn.microsoft.com/library/dn673538.aspx), qui décrit les modèles de charge de travail où en mémoire OLTP fournissent généralement des gains de performances significatifs.

#### <a name="application-design"></a>Conception de l’application

- [OLTP (optimisation de la mémoire) en mémoire](http://msdn.microsoft.com/library/dn133186.aspx)

- [Utilisez les OLTP en mémoire dans une Application de SQL Azure existante.](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Outils

- [Aperçu d’outils de données (SSDT) de SQL Server](http://msdn.microsoft.com/library/mt204009.aspx), de la dernière version mensuelle.

- [Description des utilitaires relecture Markup Language (recommandés par) pour SQL Server](http://support.microsoft.com/en-us/kb/944837)

- [Stockage en mémoire de moniteur](sql-database-in-memory-oltp-monitoring.md) en mémoire OLTP.

