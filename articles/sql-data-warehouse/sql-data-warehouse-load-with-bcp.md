<properties
   pageTitle="Utiliser bcp pour charger des données dans l’entrepôt de données SQL | Microsoft Azure"
   description="Découvrez ce que bcp et comment l’utiliser pour les scénarios de Data warehousing."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/10/2016"
   ms.author="mausher;barbkess;sonyama"/>


# <a name="load-data-with-bcp"></a>Charger les données avec bcp

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Fabrique de données](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)


**[bcp][]** est un utilitaire de chargement en bloc de ligne de commande qui vous permet de copier des données entre SQL Server et les fichiers de données SQL Data Warehouse. Utiliser bcp pour importer un grand nombre de lignes dans les tables SQL Data Warehouse ou pour exporter des données dans les fichiers de données à partir des tables de SQL Server. Sauf lorsqu’utilisé avec l’option queryout, bcp ne nécessite aucune connaissance de Transact-SQL.

bcp est un moyen rapide et facile pour déplacer des plus petits ensembles de données vers et à partir d’une base de données SQL Data Warehouse. La quantité exacte de données qui sont recommandées pour chargement/extraction via bcp dépendra de votre connexion au centre de données Azure au réseau.  En règle générale, les tables de dimension peuvent être chargées et extraire facilement avec bcp, toutefois, bcp n’est pas recommandé pour le chargement ou l’extraction de grands volumes de données.  Polybase est l’outil recommandé pour le chargement et l’extraction de grands volumes de données comme le fait un meilleur travail en exploitant l’architecture de traitement massivement parallèle de l’entrepôt de données SQL.

Avec bcp, vous pouvez :

- Utilisez un utilitaire de ligne de commande simple pour charger des données dans l’entrepôt de données SQL.
- Utilisez un utilitaire de ligne de commande simple pour extraire des données à partir de l’entrepôt de données SQL.

Ce didacticiel vous montrera comment à :

- Importer des données dans une table à l’aide de l’utilitaire bcp dans la commande
- Exporter des données à partir d’une table, en utilisant l’utilitaire bcp commande

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## <a name="prerequisites"></a>Conditions préalables

Pour exécuter ce didacticiel, vous devez :

- Une base de données SQL Data Warehouse
- L’utilitaire de ligne de commande bcp installé
- L’utilitaire de ligne de commande SQLCMD installé

>[AZURE.NOTE] Vous pouvez télécharger les utilitaires bcp et sqlcmd à partir du [Centre de téléchargement Microsoft][].

## <a name="import-data-into-sql-data-warehouse"></a>Importer des données dans l’entrepôt de données SQL

Dans ce didacticiel, vous créez une table dans le magasin de données SQL Azure et importer des données dans la table.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Étape 1 : Créer une table dans le magasin de données SQL Azure

À partir d’une invite de commandes, utiliser sqlcmd pour exécuter la requête suivante pour créer un tableau sur l’instance :

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

>[AZURE.NOTE] Pour plus d’informations sur la création d’une table SQL Data Warehouse et les options disponibles dans la clause WITH, consultez [Vue d’ensemble de la Table][] ou de la [syntaxe CREATE TABLE][] .

### <a name="step-2-create-a-source-data-file"></a>Étape 2 : Créer un fichier de données source

Ouvrez le bloc-notes et copiez les lignes de données suivantes dans un nouveau fichier texte et puis enregistrez ce fichier dans votre répertoire temporaire local, C:\Temp\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [AZURE.NOTE] Il est important de se rappeler que bcp.exe ne gère pas le codage du fichier UTF-8. Utilisez les fichiers ASCII ou codés en UTF-16 lors de l’utilisation de bcp.exe.

### <a name="step-3-connect-and-import-the-data"></a>Étape 3 : Connecter et importer les données
À l’aide de bcp, vous pouvez vous connecter et importer les données à l’aide de la commande suivante, remplacer les valeurs selon le cas :

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Vous pouvez vérifier que les données ont été chargées en exécutant la requête suivante à l’aide de sqlcmd :

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Il doit retourner les résultats suivants :

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Étape 4 : Créer des statistiques sur vos données chargées

Entrepôt de données SQL Azure est pas encore prise en charge automatique créer ou automatique des statistiques de mise à jour. Pour obtenir les meilleures performances de vos requêtes, il est important que créer des statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou apporter aucune modification substantielle se produire dans les données. Pour une explication détaillée des statistiques, consultez la rubrique de [statistiques][] dans le groupe de développement de rubriques. Voici un exemple rapide de la façon de créer des statistiques sur le tableau chargé dans cet exemple

À partir d’une invite de commandes sqlcmd, exécutez les instructions CREATE STATISTICS suivantes :

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exporter les données de l’entrepôt de données SQL
Dans ce didacticiel, vous allez créer un fichier de données à partir d’une table SQL Data Warehouse. Nous exportera les données que nous avons créés ci-dessus dans un nouveau fichier de données appelé DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Étape 1 : Exporter les données

À l’aide de l’utilitaire bcp, vous pouvez vous connecter et exporter des données à l’aide de la commande suivante, remplacer les valeurs selon le cas :

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Vous pouvez vérifier que les données ont été correctement exportées, ouvrez le nouveau fichier. Les données du fichier doivent correspondre le texte ci-dessous :

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

>[AZURE.NOTE] En raison de la nature des systèmes distribués, l’ordre des données ne peut pas être la même sur les bases de données SQL Data Warehouse. Une autre option consiste à utiliser la fonction **queryout** de l’utilitaire bcp pour écrire un extrait de la requête plutôt que d’exporter l’ensemble du tableau.

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d’ensemble du chargement, consultez [chargement de données dans Data Warehouse de SQL][].
Pour plus de conseils de développement, consultez [vue d’ensemble du développement de SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->

[Charger des données dans l’entrepôt de données SQL]: ./sql-data-warehouse-overview-load.md
[Vue d’ensemble du développement SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Vue d’ensemble de la table]: ./sql-data-warehouse-tables-overview.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[Syntaxe de CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centre de téléchargement Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433
