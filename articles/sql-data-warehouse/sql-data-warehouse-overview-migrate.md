<properties
   pageTitle="Migration de votre solution SQL Data Warehouse | Microsoft Azure"
   description="Guide de migration pour mettre votre solution à la plate-forme de l’entrepôt de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="barbkess;jrj;sonyama"/>

# <a name="migrate-your-solution-to-sql-data-warehouse"></a>Migration de votre solution d’entrepôt de données de SQL

SQL Data Warehouse est un système de base de données distribuée qui ELASTIQUEMENT s’adapte pour répondre à vos besoins. Pour maintenir des performances et évolutivité, pas toutes les fonctionnalités de SQL Server sont implémentées à l’intérieur du magasin de données SQL. Les rubriques suivantes de la migration tactile sur certains des facteurs clés pour la migration de votre solution d’entrepôt de données SQL. Conception d’entrepôts de données de l’échelle présente la conception de différente modèles et les approches traditionnelles ainsi ne sont pas toujours la meilleure. Vous pouvez par conséquent trouver que vous pouvez adapter votre solution existante garantit que profiter pleinement de la plate-forme distribuée fournie par SQL Data Warehouse.

Il est également important de noter que SQL Data Warehouse est une plate-forme dans Microsoft Azure. Par conséquent partie de la migration, citons également transférer vos données dans le nuage. Transfert de données est un objet à part entière et doit être soigneusement ; en particulier sous la forme d’augmentation des volumes. Transfert de données et de chargement de données sont des rubriques distinctes.

## <a name="migration-guidance"></a>Guide de migration

Assurez-vous que vous avez lu ces articles afin de que comprendre certains concepts fondamentaux et différences de produit avant de commencer la migration.

- [Migration de votre schéma.][]
- [Migration de vos données][]
- [Migration du code][]

## <a name="next-steps"></a>Étapes suivantes

Le Chat (équipe de conseil clientèle) a également certains conseils SQL Data Warehouse très utiles qui leur publient par le biais de blogs.  Examinons leur article, [faire migrer des données de l’entrepôt de données SQL Azure dans la pratique][] pour obtenir des conseils supplémentaires sur la migration.

<!--Image references-->

<!--Article references-->
[Migration de votre schéma.]: sql-data-warehouse-migrate-schema.md
[Migration de vos données]: sql-data-warehouse-migrate-data.md
[Migration du code]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migration des données vers l’entrepôt de données SQL Azure dans la pratique]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
