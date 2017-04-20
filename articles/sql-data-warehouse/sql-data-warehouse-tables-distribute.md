<properties
   pageTitle="Distribution de tables SQL Data Warehouse | Microsoft Azure"
   description="Mise en route de la distribution de tables dans l’entrepôt de données SQL Azure."
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
   ms.date="08/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="distributing-tables-in-sql-data-warehouse"></a>Distribution de tables SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

SQL Data Warehouse est qu'un traitement massivement parallèle (MPP) distribuée de système de base de données.  En divisant les données et capacités de traitement sur plusieurs nœuds, SQL Data Warehouse peut offrir évolutivité considérable - au-delà d’un seul système.  Décider comment répartir vos données dans le magasin de données SQL est un des facteurs plus importants pour obtenir des performances optimales.   La clé des performances optimales est de minimiser les déplacements de données et à son tour pour réduire le déplacement de données consiste à sélectionner la stratégie de distribution de droite.

## <a name="understanding-data-movement"></a>Présentation de déplacement de données

Dans un système MPP, les données de chaque table sont divisées entre plusieurs bases de données sous-jacentes.  Les requêtes les plus optimisées sur un système MPP peuvent simplement passer à exécuter sur les bases de données distribuées avec aucune interaction entre les autres bases de données.  Par exemple, supposons que vous ayez une base de données avec les données de vente qui contient deux tables, les ventes et les clients.  Si vous avez une requête qui a besoin de joindre votre table vente à votre table clients et que vous divisez une vos ventes et les tables de clients paramétrer par numéro de client, placer chaque client dans une base de données distincte, toutes les requêtes qui relient les ventes et le client peuvent être résolus dans chaque base de données sans connaissance des autres bases de données.  En revanche, si vos données de vente que vous divisé par numéro de commande et les données de votre client par numéro de client, puis toute donnée de la base de données n’aura les données correspondantes pour chaque client et donc si vous souhaitez joindre vos données de vente pour les données de votre client, vous devez obtenir les données pour chaque client à partir d’autres bases de données.  Dans ce deuxième exemple, le déplacement de données devra se produisent pour déplacer les données des clients vers les données de ventes, afin que les deux tables peuvent être jointes.  

Déplacement de données n’est pas toujours une mauvaise chose, il est parfois nécessaire de résoudre une requête.  Mais, lors de cette étape supplémentaire peut être évitée, naturellement votre requête s’exécute plus rapidement.  Déplacement de données se produit plus fréquemment lorsque les tables sont jointes ou agrégations sont effectuées.  Souvent, vous avez besoin à la fois, même si vous êtes en mesure d’optimiser pour un scénario, comme une jointure, vous devez toujours déplacement des données pour vous aider à résoudre pour le scénario comme une agrégation.  L’astuce est de déterminer qui est le moins de travail.  Dans la plupart des cas, la distribution de tables de faits importantes dans une colonne jointe couramment est la méthode la plus efficace pour réduire la circulation de données la plupart des.  Distribution des données sur les colonnes de jointure est une méthode beaucoup plus courante pour réduire le déplacement des données à la distribution des données sur des colonnes impliquées dans une agrégation.

## <a name="select-distribution-method"></a>Sélectionnez la méthode de distribution

Dans les coulisses, SQL Data Warehouse répartit vos données dans les bases de données de 60.  Chaque base de données spécifique est appelé une **distribution**.  Lorsque les données sont chargées dans chaque table, SQL Data Warehouse doit savoir comment diviser vos données sur ces distributions de 60.  

La méthode de distribution est définie au niveau de la table et actuellement, il existe deux possibilités :

1. **Répétition alternée** , lequel distribuer les données uniformément mais de façon aléatoire.
2. **Hachage distribué** qui distribue les données basées sur les valeurs à partir d’une seule colonne de hachage

Par défaut, lorsque vous ne définissez pas une méthode de distribution de données, la table sera distribuée à l’aide de la méthode de distribution de **répétition alternée** .  Toutefois, lorsque vous serez plus sophistiquées dans votre implémentation, vous devez envisager d’utiliser des tables de **hachage distribué** pour minimiser le déplacement de données, qui à son tour optimise les performances des requêtes.

### <a name="round-robin-tables"></a>Tables de répétition alternée

À l’aide de la méthode de répétition alternée de distribution des données est très bien comment il semble.  Lors du chargement de vos données, chaque ligne est envoyée simplement à la prochaine distribution.  Cette méthode de distribution des données toujours au hasard distribuera les données très uniformément sur l’ensemble des distributions.  Autrement dit, il n’est aucun tri effectué au cours du processus de répétition alternée, qui place les données.  Une distribution à répétition alternée est parfois appelée un hachage aléatoire pour cette raison.  Avec une table distribuée alternée, il est inutile de comprendre les données.  Pour cette raison, les tables de répétition alternée s’avérer chargement bonnes cibles.

Par défaut, si aucune méthode de distribution n’est choisi, la méthode de distribution de la répétition alternée est utilisée.  Toutefois, alors que les tables de répétition alternée sont faciles à utiliser, car les données sont réparties au hasard sur le système, que cela signifie que le système ne peut pas garantir la distribution chaque ligne est sur.  Par conséquent, le système doit parfois appeler une opération de déplacement de données pour mieux organiser vos données avant qu’il peut résoudre une requête.  Cette étape supplémentaire peut ralentir vos requêtes.

Envisagez d’utiliser la distribution de répétition alternée pour votre table dans les scénarios suivants :

- Lors de la mise en route sous la forme d’un simple point de départ
- Si aucune clé de jointure évidente
- Si il n’est pas colonne bon candidat pour la distribution de la table de hachage
- Si la table ne partage pas une clé de jointure communes avec d’autres tables
- Si la jointure est moins importante que les autres jointures dans la requête
- Lorsque la table est une table intermédiaire temporaire

Les deux exemples suivants crée une Table Round Robin :

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [AZURE.NOTE] Lors de la répétition alternée est le type de tableau par défaut qui est explicite dans votre code DDL est considéré comme une meilleure pratique afin que les intentions de la disposition de votre tableau sont évidents à d’autres personnes.

### <a name="hash-distributed-tables"></a>Distribué des Tables de hachage

À l’aide d’un algorithme de **hachage distribué** pour distribuer vos tables peut améliorer les performances pour de nombreux scénarios en réduisant le déplacement des données au moment de la requête.  Tables de hachage distribué sont les tables qui sont répartis entre les bases de données distribuées à l’aide d’un algorithme de hachage sur une seule colonne que vous sélectionnez.  La colonne distribution est ce qui détermine la façon dont les données sont réparties sur vos bases de données distribuées.  La fonction de hachage utilise la colonne distribution pour affecter des lignes aux distributions.  L’algorithme de hachage et de la distribution résultante est déterministe.  Qui est la même valeur avec le même type de données sera toujours la même distribution.    

Cet exemple crée une table distribuée sur id :

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>Sélectionnez la colonne de distribution

Lorsque vous choisissez une table de **hachage distribuer** , vous devez sélectionner une colonne de distribution unique.  Lorsque vous sélectionnez une colonne de distribution, il existe trois principaux facteurs à prendre en compte.  

Permet de sélectionner une seule colonne qui sera :

1. N’est ne pas mis à jour
2. Répartir les données uniformément, éviter le décalage des données
3. Réduire le déplacement des données

### <a name="select-distribution-column-which-will-not-be-updated"></a>Sélectionnez la colonne de distribution qui n’est pas mises à jour

Colonnes de distribution ne sont pas mis à jour, par conséquent, sélectionnez une colonne avec des valeurs statiques.  Si une colonne doivent être mis à jour, il n’est généralement pas un candidat de bonne distribution.  S’il existe un cas où vous devez mettre à jour une colonne de distribution, ceci est possible en tout d’abord supprimer la ligne, puis d’insérer une nouvelle ligne.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Sélectionnez la colonne de distribution qui va distribuer les données uniformément

Depuis un système distribué s’exécute aussi vite que sa distribution plus lente, il est important répartir l’uniformément sur les distributions pour atteindre l’exécution équilibrée sur le système.  La façon dont le travail est réparti sur un système distribué est basé sur où les données de chaque distribution résident.  Cela rend très important sélectionner la colonne de droite de distribution pour la distribution des données afin que chaque distribution a le même travail et aura le même temps sa partie du travail.  Lorsque le travail est bien répartie sur le système, les données sont équilibrées entre les distributions.  Lorsque les données ne sont pas équilibrées, nous appelons cette **inclinaison des données**.  

Pour répartir les données uniformément et éviter les données en inclinaison, considérez les éléments suivants lors de la sélection de votre colonne de distribution :

1. Sélectionnez une colonne qui contient un nombre important de valeurs distinctes.
2. Éviter la distribution des données sur les colonnes avec un peu de valeurs distinctes. 
3. Éviter la distribution des données sur des colonnes ayant une fréquence élevée des valeurs NULL.
4. Éviter la distribution des données sur les colonnes de date.

Dans la mesure où chaque valeur est haché à 1 sur 60 distributions, vous souhaitez sélectionner une colonne qui est hautement unique et contient des valeurs uniques plus de 60 pour obtenir la répartition égale.  Pour illustrer, examinons un cas où une colonne contient uniquement les 40 valeurs uniques.  Si cette colonne a été sélectionnée en tant que la clé de répartition, les données de la table seraient atterrir sur 40 distributions au plus, laissant 20 distributions avec aucune donnée et aucun traitement.  À l’inverse, les autres 40 distributions aurait plus de travail pour le faire si les données a été réparties de plus de 60 distributions.  Ce scénario est un exemple de données inclinaison.

Dans un système MPP, chaque étape de requête attend pour toutes les distributions terminer leur part du travail.  Si une distribution effectue plus de travail que les autres, puis la ressource des autres distributions sont essentiellement perdu attendait simplement la distribution à l’activité.  Lorsque le travail n’est pas équitablement réparti entre toutes les distributions, nous appelons cette **transformation d’inclinaison**.  Inclinaison de traitement entraîne des requêtes à exécuter plus lentement que si la charge de travail peut être répartie sur les distributions.  Inclinaison de données conduira à inclinaison de traitement.

Éviter de distribuer sur hautement n’acceptant que les valeurs null est alors placé sur la même distribution. Distribution sur une colonne de date peut également provoquer inclinaison de traitement, car toutes les données pour une date donnée est alors placé sur la même distribution. Si plusieurs utilisateurs exécutent des requêtes tout le filtrage à la même date, puis seulement 1 des 60 distributions allez effectuer tout le travail depuis la date indiquée sera uniquement sur une distribution. Dans ce scénario, les requêtes seront exécutera probablement de 60 fois plus lents que si les données ont été équitablement réparties sur toutes les distributions. 

Lorsqu’il n’existe aucune colonne susceptible, envisagez la répétition alternée comme méthode de distribution.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Sélectionnez la colonne de distribution qui permet de minimiser le déplacement de données

Réduction des déplacements de données en sélectionnant la colonne de droite de distribution est une des plus importantes stratégies pour optimiser les performances de votre magasin de données SQL.  Déplacement de données se produit plus fréquemment lorsque les tables sont jointes ou agrégations sont effectuées.  Les colonnes utilisées dans les `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` et `HAVING` clauses tous faire de **bonnes** candidates de distribution de hachage. 

D’autre part, les colonnes dans le `WHERE` do de clause **pas** pour les candidats de colonne hachage adéquate car ils limitent les distributions de participent à la requête, à l’origine du traitement incliner.  Un bon exemple d’une colonne qui peut être tentant de distribuer sur, mais peut souvent entraîner ce traitement inclinaison est une colonne de dates.

En règle générale, si vous avez deux grandes tables de faits souvent impliquées dans une jointure, vous allez acquérir les meilleures performances en répartissant les deux tables sur une des colonnes de jointure.  Si vous avez une table qui n’est jamais joint à une autre table de faits volumineuse, puis rechercher les colonnes qui sont fréquemment dans les `GROUP BY` clause.

Il existe quelques critères clés qui doivent être remplies pour éviter le déplacement des données au cours d’une jointure :

1. Les tables impliquées dans la jointure doivent être distribué sur **une** des colonnes participant à la jointure de hachage.
2. Les types de données des colonnes de jointure doivent correspondre entre les deux tables.
3. Les colonnes doivent être joint avec un opérateur d’égalité.
4. Le type de jointure peut ne pas être un `CROSS JOIN`.


## <a name="troubleshooting-data-skew"></a>Résolution des problèmes de distorsion d’image de données

Lorsque les données de la table sont distribuées à l’aide de la méthode de distribution de hachage, il est possible que certaines distributions seront erronées pour les données sans respect des proportions plus que d’autres. Inclinaison de trop de données peut influer sur les performances des requêtes car le résultat final de la requête distribuée doit attendre la distribution la plus longue en cours d’exécution à la fin. Selon le degré de l’inclinaison de données, vous devrez peut-être à corriger le problème.

### <a name="identifying-skew"></a>Identification d’inclinaison

Un moyen simple d’identifier une table comme incliné consiste à utiliser `DBCC PDW_SHOWSPACEUSED`.  Il s’agit d’un moyen très simple et rapide pour voir le nombre de lignes de la table sont stockées dans chacune des 60 distributions de votre base de données.  N’oubliez pas que pour des performances plus équilibrée, les lignes de votre table distribuée doivent être réparties uniformément sur toutes les distributions.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Toutefois, si vous interrogez les vues de gestion dynamique (DMV) Azure SQL Data Warehouse, vous pouvez effectuer une analyse plus détaillée.  Pour commencer, créez la vue de [dbo.vTableSizes][] afficher en utilisant le SQL à partir de la [Vue d’ensemble de la Table],[vue d’ensemble de] l’article.  Une fois que la vue est créée, vous pouvez exécuter cette requête pour identifier les tables qui ont plus de 10 % de données inclinaison.

```sql
select *
from dbo.vTableSizes 
where two_part_name in 
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>Résolution des données inclinaison

Pas tout inclinaison est suffisamment pour justifier un correctif.  Dans certains cas, les performances d’une table dans certaines requêtes peuvent l’emporter sur les dommages de données inclinaison.  Pour décider si vous devez résoudre les données dans une table, vous devez comprendre autant que possible sur les volumes de données et les requêtes dans votre charge de travail.   Une pour examiner l’impact de l’inclinaison consiste à utiliser les étapes dans l’article de [l’Analyse de la requête][] de suivre l’impact d’inclinaison sur les performances de la requête et en particulier l’impact sur les requêtes de la durée nécessaire à la réalisation sur les distributions individuelles.

Distribution des données est une question de trouver le juste équilibre entre la réduction d’inclinaison des données et réduire le déplacement des données. Ceux-ci peuvent être opposées objectifs, et parfois vous souhaitez conserver les données inclinaison afin de réduire le déplacement des données. Par exemple, lorsque la colonne distribution est fréquemment la colonne partagée dans les jointures et les agrégations, vous sera réduction déplacement de données. L’avantage de disposer de la circulation de données minimale peut compenser l’impact d’avoir les données incliner. 

Le standard résoudre l’inclinaison des données consiste à recréer la table avec une colonne de distribution différent. Dans la mesure où il n’y a aucun moyen de modifier la colonne de distribution sur une table existante, la façon de modifier la distribution d’une table à la recréer avec un [de] [SACT].  Voici deux exemples de la façon de résoudre des données inclinaison :

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>Exemple 1 : Créer de nouveau la table avec une nouvelle colonne de distribution

Cet exemple utilise [] de [SACT] pour recréer une table avec une colonne de distribution de hachage différent. 

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>Exemple 2 : Créez de nouveau le tableau à l’aide de la distribution de répétition alternée

Cet exemple utilise [] de [SACT] pour recréer une table avec la répétition alternée au lieu d’une distribution de hachage. Cette modification entraîne la même la distribution des données au détriment de la circulation des données accrue. 

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la création de table, reportez-vous à [répartir][], [Index][], [Partition][], [Types de données][], [statistiques][] et articles[temporaires] des [Tables temporaires].

Pour une vue d’ensemble des meilleures pratiques, consultez méthodes [Conseillées entrepôt de données SQL][].


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
[Analyse de la requête]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#querying-table-sizes

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
