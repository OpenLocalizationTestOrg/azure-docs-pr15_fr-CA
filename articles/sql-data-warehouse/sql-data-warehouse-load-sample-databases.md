<properties
   pageTitle="Charger les données d’exemple dans SQL Data Warehouse | Microsoft Azure"
   description="Charger des exemples de données dans Data Warehouse de SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="load-sample-data-into-sql-data-warehouse"></a>Charger des exemples de données dans Data Warehouse de SQL

Suivez ces étapes simples pour charger et interroger la base de données Adventure Works. Tout d’abord, ces scripts utilisent sqlcmd pour exécuter SQL qui va créer les tables et les vues. Une fois que les tables ont été créées, les scripts utilisent bcp pour charger des données.  Si vous ne disposez pas de sqlcmd et bcp installé, cliquez sur ces liens pour [installer bcp][] et [installer sqlcmd][].

##<a name="load-sample-data"></a>Charger des exemples de données

1. Téléchargez le fichier zip de [Adventure Works exemples de Scripts pour l’entrepôt de données SQL][] .

2. Extrayez les fichiers zip téléchargés dans un répertoire sur votre ordinateur local.

3. Modifier le fichier extrait de aw_create.bat et de définir les variables suivantes en haut du fichier.  Veillez à ne laisser aucun espace entre le « = » et le paramètre.  Voici des exemples de vos modifications peut se présenter de la façon dont.

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. À partir d’une invite de commande Windows, exécutez le aw_create.bat modifié.  Assurez-vous que vous êtes dans le répertoire où vous avez enregistré votre version modifiée du aw_create.bat.
Ce script sera...
    * Supprimer les tables de la société Adventure Works ou vues qui existent déjà dans votre base de données
    * Créer des vues et les tables d’Adventure Works
    * Charger chaque table Adventure Works à l’aide de bcp
    * Valider le nombre de lignes de chaque table d’Adventure Works
    * Collecter des statistiques sur toutes les colonnes de chaque table d’Adventure Works


##<a name="query-sample-data"></a>Exemple de données de requête

Une fois que vous avez chargé des exemples de données dans le Data Warehouse de SQL, vous pouvez exécuter rapidement quelques requêtes.  Pour exécuter une requête, de se connecter à votre base de données Adventure Works nouvellement créée dans le Data Warehouse de SQL Azure à l’aide de Visual Studio et SSDT, comme décrit dans le document [de requête avec Visual Studio][] .

Exemple d’une instruction select simple à obtenir toutes les informations des employés :

```sql
SELECT * FROM DimEmployee;
```

Exemple d’une requête plus complexe à l’aide de constructions, telles que GROUP BY pour consulter le montant total de toutes les ventes pour chaque jour :

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Exemple d’une instruction SELECT avec une clause WHERE pour filtrer les ordres d’avant une certaine date :

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SQL Data Warehouse prend en charge presque toutes les constructions T-SQL qui prend en charge de SQL Server.  Toutes les différences sont documentées dans notre documentation [migrer du code][] .

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez eu l’occasion d’essayer certaines requêtes comportant des exemples de données, découvrez comment [développer][], [charger][]ou [migrer][] vers SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[effectuer la migration]: sql-data-warehouse-overview-migrate.md
[développer]: sql-data-warehouse-overview-develop.md
[charge]: sql-data-warehouse-overview-load.md
[requête avec Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migration du code]: sql-data-warehouse-migrate-code.md
[installer l’utilitaire bcp]: sql-data-warehouse-load-with-bcp.md
[installer sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Adventure Works des exemples de Scripts pour l’entrepôt de données SQL]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
