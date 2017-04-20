<properties
   pageTitle="SQL dynamique SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’utilisation de SQL dynamique dans l’entrepôt de données SQL Azure pour le développement de solutions."
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

# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dynamique dans l’entrepôt de données SQL
Lors du développement de code d’application pour SQL Data Warehouse, vous devrez utiliser sql dynamique permettant de fournir des solutions flexibles, génériques et modulaires. SQL Data Warehouse ne gère pas les types de données blob en ce moment. Cela peut limiter la taille de vos chaînes comme types blob incluent les types varchar (max) et nvarchar (max) à la fois. Si vous avez utilisé ces types dans votre code d’application lors de la création de très grandes chaînes, vous devez rompre le code en segments et utilisez plutôt l’instruction EXEC.

Un exemple simple :

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Si la chaîne est courte, vous pouvez utiliser [sp_executesql][] comme à l’accoutumée.

> [AZURE.NOTE] Instructions exécutées comme SQL dynamique seront toujours soumis à toutes les règles de validation de TSQL.

## <a name="next-steps"></a>Étapes suivantes
Pour plus de conseils de développement, consultez [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble du développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
