<properties
   pageTitle="Types de données pour les tables SQL Data Warehouse | Microsoft Azure"
   description="Mise en route avec les types de données pour les tables de l’entrepôt de données SQL Azure."
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

# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Types de données pour les tables SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Prise en charge de l’entrepôt de données SQL les plus courantes des types de données.  Vous trouverez ci-dessous la liste des types de données pris en charge par le magasin de données SQL.  Pour plus de détails sur le type de données prennent en charge, voir [Création de table][].

|**Types de données pris en charge**|||
|---|---|---|
[bigint][]|[décimal][]|[smallint][]|
[fichier binaire][]|[float][]|[smallmoney][]|
[bit][]|[int][]|[sysname][]|
[Char][]|[Microsoft Money][]|[heure][]|
[date][]|[NCHAR][]|[tinyint][]|
[DateTime][]|[nvarchar][]|[uniqueidentifier][]|
[datetime2][]|[réel][]|[varbinary][]|
[DateTimeOffset][]|[smalldatetime][]|[varchar][]|


## <a name="data-type-best-practices"></a>Meilleures pratiques de type de données

 Lorsque vous définissez vos types de colonnes, en utilisant le plus petit type de données qui prend en charge vos données améliore les performances de la requête. Ceci est particulièrement important pour les colonnes CHAR et VARCHAR. Si la valeur la plus longue dans une colonne est de 25 caractères, puis définissez la colonne sous la forme VARCHAR(25). Évitez de définir toutes les colonnes de type caractère à une longueur par défaut de grande taille. En outre, de définir des colonnes en tant que VARCHAR lorsque c’est tout ce qui est nécessaire, plutôt que d’utiliser [NVARCHAR][].  Utilisez nvarchar (4000) ou VARCHAR(8000) lorsque cela est possible au lieu de nvarchar (max) ou varchar (max).

## <a name="polybase-limitation"></a>Limitation des Polybase

Si vous utilisez Polybase pour charger vos tables, définissez vos tables, la taille maximale de ligne possibles, y compris la longueur totale des colonnes de longueur variable, ne doit pas dépasser 32 767 octets.  Vous pouvez définir une ligne avec les données de longueur variable qui peuvent dépasser cette largeur et charger les lignes à l’aide de BCP, vous ne serez pas en mesure d’utiliser Polybase pour charger ces données.  Prise en charge de Polybase de lignes étendues est bientôt ajouté.

## <a name="unsupported-data-types"></a>Types de données non pris en charge

Si vous migrez votre base de données à partir d’une autre plate-forme SQL comme base de données de SQL Azure, migrer, vous pouvez rencontrer certains types de données qui ne sont pas pris en charge sur SQL Data Warehouse.  Voici les types de données non pris en charge ainsi que d’autres solutions que vous pouvez utiliser à la place des types de données non pris en charge.

|Type de données|Solution de contournement|
|---|---|
|[géométrie][]|[varbinary][]|
|[emplacement géographique][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][] (4000)|
|[image][ntext,text,image]|[varbinary][]|
|[texte][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql_variant][]|Colonne de fractionnement en plusieurs colonnes fortement typées.|
|[table][]|Convertir les tables temporaires.|
|[horodatage][]|Retravailler le code pour utiliser [datetime2][] et `CURRENT_TIMESTAMP` fonction.  Seules des constantes sont pris en charge en tant que valeurs par défaut, par conséquent current_timestamp ne peut être définie comme une contrainte default. Si vous avez besoin migrer les valeurs de version de ligne d’une colonne de type timestamp puis utilisez [BINARY][](8) ou [VARBINARY][BINARY](8) pour les valeurs de version de ligne pas la valeur NULL ou la valeur NULL.|
|[XML][]|[varchar][]|
|[types définis par l’utilisateur][]|convertir à leurs types natifs, lorsque cela est possible|
|valeurs par défaut|valeurs par défaut prend en charge les littéraux et des constantes uniquement.  Expressions non déterministes ou des fonctions, telles que `GETDATE()` ou `CURRENT_TIMESTAMP`, ne sont pas pris en charge.|

Le sous SQL peut être exécutée sur votre base de données SQL pour identifier les colonnes qui sont ne pas être pris en charge par le magasin de données SQL Azure :

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles sur [Vue d’ensemble de la Table][vue d’ensemble], [distribuer une Table de][distribution], [l’indexation d’un tableau]d'[Index], [partitionnement d’une Table]de[Partition], [Mise à jour des statistiques de Table][statistiques] et[temporaire] [Des Tables temporaires].  Pour plus d’informations sur les méthodes conseillées, consultez méthodes [Conseillées entrepôt de données SQL][].

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
[Créer table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[fichier binaire]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[Char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[DateTime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[DateTimeOffset]: https://msdn.microsoft.com/library/bb630289.aspx
[décimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[géométrie]: https://msdn.microsoft.com/library/cc280487.aspx
[emplacement géographique]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[Microsoft Money]: https://msdn.microsoft.com/library/ms179882.aspx
[NCHAR]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[réel]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[heure]: https://msdn.microsoft.com/library/bb677243.aspx
[horodatage]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[XML]: https://msdn.microsoft.com/library/ms187339.aspx
[types définis par l’utilisateur]: https://msdn.microsoft.com/library/ms131694.aspx
