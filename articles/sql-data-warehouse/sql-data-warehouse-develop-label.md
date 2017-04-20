<properties
   pageTitle="Utiliser des étiquettes pour les requêtes de l’instrument dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’utilisation d’étiquettes aux requêtes d’instrument dans le magasin de données SQL Azure pour le développement de solutions."
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

# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Utiliser des étiquettes pour les requêtes d’instrument dans le magasin de données SQL
SQL Data Warehouse prend en charge un concept appelé des étiquettes de la requête. Avant de passer dans n’importe quelle profondeur, nous allons examiner un exemple :

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Cette dernière ligne balises la chaîne 'Ma requête étiquette' à la requête. Cela est particulièrement utile que l’étiquette est capable de requête via les DMV. Ceci nous fournit un mécanisme pour détecter les requêtes problématiques et également pour aider à identifier la progression via une exécution ETL.

Une bonne convention d’affectation de noms aide vraiment ici. Par exemple, quelque chose comme « projet : procédure : instruction : commentaire ' contribue à identifier de façon unique la requête avec tous les codes de contrôle de code source.

Pour effectuer une recherche par étiquette, vous pouvez utiliser la requête suivante utilise les vues de gestion dynamique :

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [AZURE.NOTE] Il est essentiel que vous renvoyer à la ligne des crochets ou des guillemets doubles autour de l’étiquette de word lors de l’interrogation. Étiquette est un mot réservé et a provoqué une erreur si elle n’a pas été délimité.


## <a name="next-steps"></a>Étapes suivantes
Pour plus de conseils de développement, consultez [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble du développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
