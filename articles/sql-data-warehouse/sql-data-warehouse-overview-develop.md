<properties
   pageTitle="Concevoir des décisions et des techniques de codage pour le développement de SQL Data Warehouse | Microsoft Azure"
   description="Concepts de développement, des décisions de conception, des recommandations et des techniques de codage pour l’entrepôt de données SQL."
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
   ms.date="08/16/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Décisions de conception et les techniques de codage pour l’entrepôt de données SQL

Jetez un œil à ces articles sur le développement pour mieux comprendre les principales décisions de conception, des recommandations et des techniques de codage pour l’entrepôt de données SQL.

## <a name="key-design-decisions"></a>Principales décisions de conception
Les articles suivants de la mettre en surbrillance certains des concepts clés et des décisions de conception que vous devez comprendre pour le développement de votre data warehouse distribué à l’aide de SQL Data Warehouse :

- [connexions][]
- [concurrence d’accès][]
- [transactions][]
- [schémas définis par l’utilisateur][]
- [distribution de tables][]
- [index de table][]
- [partitions de table][]
- [SACT][]
- [statistiques][]

## <a name="development-recommendations-and-coding-techniques"></a>Les recommandations de développement et de techniques de codage
Ces articles présentent des techniques de codage spécifiques, des conseils et des recommandations pour le développement de votre magasin de données SQL :

- [procédures stockées][]
- [étiquettes][]
- [Affichage][]
- [tables temporaires][]
- [SQL dynamique][]
- [en boucle][]
- [Regrouper par options][]
- [affectation de variable][]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez été les articles de développement un œil via la page de [référence de Transact-SQL][] pour plus d’informations sur la syntaxe prise en charge pour l’entrepôt de données SQL.

<!--Image references-->

<!--Article references-->
[concurrence d’accès]: ./sql-data-warehouse-develop-concurrency.md
[connexions]: ./sql-data-warehouse-connect-overview.md
[SACT]: ./sql-data-warehouse-develop-ctas.md
[SQL dynamique]: ./sql-data-warehouse-develop-dynamic-sql.md
[Regrouper par options]: ./sql-data-warehouse-develop-group-by-options.md
[étiquettes]: ./sql-data-warehouse-develop-label.md
[en boucle]: ./sql-data-warehouse-develop-loops.md
[statistiques]: ./sql-data-warehouse-tables-statistics.md
[procédures stockées]: ./sql-data-warehouse-develop-stored-procedures.md
[distribution de tables]: ./sql-data-warehouse-tables-distribute.md
[index de table]: ./sql-data-warehouse-tables-index.md
[partitions de table]: ./sql-data-warehouse-tables-partition.md
[tables temporaires]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[schémas définis par l’utilisateur]: ./sql-data-warehouse-develop-user-defined-schemas.md
[affectation de variable]: ./sql-data-warehouse-develop-variable-assignment.md
[Affichage]: ./sql-data-warehouse-develop-views.md
[Référence de Transact-SQL]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
