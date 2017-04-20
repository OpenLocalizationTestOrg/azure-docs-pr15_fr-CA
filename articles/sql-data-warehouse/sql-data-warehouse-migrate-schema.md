<properties
   pageTitle="Migrer votre schéma SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour la migration de votre schéma d’entrepôt de données SQL Azure pour le développement de solutions."
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
   ms.date="08/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Migrer votre schéma d’entrepôt de données SQL#

Les résumés suivantes permettent de comprendre les différences entre SQL Server et SQL Data Warehouse pour vous aider à faire migrer votre base de données.

## <a name="table-migration"></a>Migration de table

Lors de la migration de vos tables, vous voudrez vous familiariser avec les fonctionnalités de tableau de tables de l’entrepôt de données SQL.  [Vue d’ensemble de la table][] est un bon point de départ.  Cet article présente les considérations les plus importantes lors de la création d’une table de statistiques de la table, distribution, le partitionnement et l’indexation.  Il traite également des [fonctionnalités de tableau non pris en charge][] et des solutions de contournement.

SQL Data Warehouse prend en charge les types de données d’entreprise courants.  Consultez l’article de [types de données][] pour une liste des prises en charge et les [types de données non pris en charge][].  Les [types de données][] contient également une requête pour identifier les [types de données non pris en charge][].  Lors de la conversion de vos types de données, veillez à examiner les [meilleures pratiques de type de données][].

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez correctement migré votre schéma de base de données SQL Data Warehouse, passez à l’un des articles suivants :

- [Migration de vos données][]
- [Migration du code][]

Pour plus d’informations sur les méthodes recommandées de SQL Data Warehouse, consultez l’article de [meilleures pratiques][] .

<!--Image references-->

<!--Article references-->
[Migration du code]: ./sql-data-warehouse-migrate-code.md
[Migration de vos données]: ./sql-data-warehouse-migrate-data.md
[meilleures pratiques]: ./sql-data-warehouse-best-practices.md
[vue d’ensemble de la table]: ./sql-data-warehouse-tables-overview.md
[fonctions de tableau non pris en charge]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[types de données]: ./sql-data-warehouse-tables-data-types.md
[types de données non pris en charge]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[meilleures pratiques de type de données]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->
