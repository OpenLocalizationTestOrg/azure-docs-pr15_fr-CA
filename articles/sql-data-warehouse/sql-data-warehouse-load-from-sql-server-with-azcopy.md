<properties
   pageTitle="Charger des données à partir de SQL Server dans l’entrepôt de données SQL Azure (PolyBase) | Microsoft Azure"
   description="Utilise l’utilitaire bcp pour exporter des données à partir de SQL Server vers des fichiers plats, AZCopy pour importer des données dans le stockage blob Azure et PolyBase pour recevoir les données dans l’entrepôt de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>Charger des données à partir de SQL Server dans l’entrepôt de données SQL Azure (AZCopy)

Utilisez bcp et les utilitaires de ligne de commande AZCopy pour charger des données à partir de SQL Server pour le stockage des objets blob Azure. Puis, utilisez PolyBase ou Azure Data Factory pour charger les données dans l’entrepôt de données SQL Azure. 


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
