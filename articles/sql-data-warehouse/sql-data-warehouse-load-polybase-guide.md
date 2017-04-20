<properties
   pageTitle="Guide d’utilisation de PolyBase dans SQL Data Warehouse | Microsoft Azure"
   description="Instructions et recommandations sur l’utilisation de PolyBase dans les scénarios de Data Warehouse de SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guide d’utilisation de PolyBase dans l’entrepôt de données SQL

Ce guide donne des informations pratiques sur l’utilisation de PolyBase dans l’entrepôt de données SQL.

Pour commencer, consultez le didacticiel de [charger les données avec PolyBase][] .


## <a name="rotating-storage-keys"></a>Rotation des clés de stockage

De temps en temps, vous souhaiterez modifier la touche d’accès rapide à votre stockage blob pour des raisons de sécurité.

Pour effectuer cette tâche, la plus élégante consiste à suivre un processus appelé « faire pivoter les clés ». Vous avez peut-être remarqué que vous disposez de deux clés de stockage de votre compte de stockage blob. C’est ainsi que vous pouvez effectuer la transition

Rotation de vos clés de compte de stockage Azure est un processus simple en trois étapes

1. Créer la deuxième étendue de la base de données d’informations d’identification basée sur la clé d’accès de stockage secondaire
2. Créer la deuxième source de données externe en fonction de cette nouvelle qualification
3. Supprimez et recréez les tables externes pointant vers la nouvelle source de données externe

Lorsque vous avez migré tous vos externes des tables à la nouvelle source de données externe, puis vous pouvez effectuer les tâches de nettoyage :

1. Déplacer la première source de données externe
2. Base de données de la première liste étendue d’informations d’identification basée sur la clé d’accès de stockage principal
3. Ouvrez une session dans Azure et régénérer la clé d’accès principal prête pour le prochain

## <a name="query-azure-blob-storage-data"></a>Interroger les données du stockage blob Azure
Requêtes sur des tables externes utilisent simplement le nom de table comme s’il s’agissait d’une table relationnelle.

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE] Une requête sur une table externe peut échouer avec l’erreur *« Requête abandonnée--le maximum rejeter le seuil a été atteint lors de la lecture à partir d’une source externe »*. Cela signifie que vos données externes contient des enregistrements *modifiés* . Un enregistrement de données est considéré comme « modifié » si le types de données réelles/nombre de colonnes ne correspondent pas les définitions de colonne de la table externe ou si les données n’est pas conforme au format de fichier externe spécifié. Pour résoudre ce problème, assurez-vous que votre table externe et les définitions de format de fichier externe sont correctes et que vos données externes est conforme à ces définitions. Dans le cas où un sous-ensemble d’enregistrements de données externes sont modifiées, vous pouvez choisir de rejeter ces enregistrements pour vos requêtes en utilisant les options de rejet de créer les DDL de TABLE externe.


## <a name="load-data-from-azure-blob-storage"></a>Charger des données depuis le stockage blob Azure
Cet exemple charge les données depuis le stockage blob Azure à une base de données de l’entrepôt de données SQL.

Stockage des données directement supprime le temps de transfert de données pour les requêtes. Stockage de données avec un index columnstore améliore les performances de requête pour les requêtes d’analyse par jusqu'à 10 fois.

Cet exemple utilise l’instruction CREATE TABLE AS SELECT pour charger les données. La nouvelle table hérite les colonnes dans la requête. Il hérite les types de données de ces colonnes dans la définition de la table externe.

CREATE TABLE AS SELECT est un très performantes à instruction Transact-SQL qui charge les données en parallèle sur tous les nœuds de calcul de votre magasin de données SQL.  Il a été développé pour le moteur de traitement massivement parallèle (MPP) dans le système de la plate-forme Analytique et se trouve maintenant dans le magasin de données SQL.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Reportez-vous à la section [CREATE TABLE AS sélectionnez (Transact-SQL)][].

## <a name="create-statistics-on-newly-loaded-data"></a>Créer des statistiques sur les données qui vient d’être chargées.

Entrepôt de données SQL Azure est pas encore prise en charge automatique créer ou automatique des statistiques de mise à jour.  Pour obtenir les meilleures performances de vos requêtes, il est important que créer des statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou apporter aucune modification substantielle se produire dans les données.  Pour une explication détaillée des statistiques, consultez la rubrique de [statistiques][] dans le groupe de développement de rubriques.  Voici un exemple rapide de la façon de créer des statistiques sur le tableau chargé dans cet exemple.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>Exporter des données vers le stockage blob Azure
Cette section montre comment exporter les données de magasin de données SQL pour le stockage des objets blob Azure. Cet exemple utilise créer externe TABLE AS SELECT qui est un très performantes à instruction Transact-SQL pour exporter les données en parallèle à partir de tous les nœuds de calcul.

L’exemple suivant crée une table externe Weblogs2014 à l’aide des définitions de colonne et les données de dbo. Table des journaux Web. La définition de la table externe est stockée dans le magasin de données SQL et les résultats de l’instruction SELECT sont exportés vers le répertoire « / archive/log2014 / » sous le conteneur de l’objet blob spécifié par la source de données. Les données sont exportées dans le format de fichier texte spécifié.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


## <a name="working-around-the-polybase-utf-8-requirement"></a>Travail autour de la spécification PolyBase UTF-8
À présent PolyBase prend en charge le chargement des fichiers de données qui ont été codés en UTF-8. Comme de l’UTF-8 utilise le même codage de caractères comme ASCII PolyBase sera également prise en charge du chargement de données qui est encodée en ASCII. Toutefois, PolyBase ne prend pas en charge autre codage de caractère, par exemple UTF-16 / Unicode ou des caractères ASCII étendus. Code ASCII étendu comprend des caractères accentués tels que le tréma qui est courant en allemand.

Pour contourner cette exigence, la meilleure réponse est d’écrire de nouveau au format UTF-8.

Il existe plusieurs façons de le faire. Voici deux méthodes à l’aide de Powershell :

### <a name="simple-example-for-small-files"></a>Exemple simple pour les petits fichiers

Voici un simple d’une ligne qui crée le fichier de script Powershell.

```PowerShell
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Toutefois, s’il s’agit d’un moyen simple de ré-encoder les données il n’est en aucun cas la plus efficace. Cet exemple en flux continu d’e/s est beaucoup, beaucoup plus rapide et donne le même résultat.

### <a name="io-streaming-example-for-larger-files"></a>Exemple de flux d’e/s de fichiers plus volumineux

L’exemple de code ci-dessous est plus complexe, mais il affiche les lignes de données à partir de la source avec la cible qu’il est beaucoup plus efficace. Utilisez cette approche pour les fichiers plus volumineux.

```PowerShell
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path\","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path\","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le déplacement des données pour l’entrepôt de données SQL, consultez la [vue d’ensemble de migration de données][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Charger les données avec PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[vue d’ensemble de migration de données]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[FORMAT de fichier externe créer (Transact-SQL)] : https://msdn.microsoft.com/library/dn935026) .aspx [créer une TABLE externe (Transact-SQL)] : https://msdn.microsoft.com/library/dn935021.aspxx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CRÉER TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
