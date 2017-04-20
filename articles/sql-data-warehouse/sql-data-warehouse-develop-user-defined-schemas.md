<properties
   pageTitle="Les schémas définis par l’utilisateur dans le magasin de données SQL | Microsoft Azure"
   description="Conseils d’utilisation des schémas de Transact-SQL dans le magasin de données SQL Azure pour le développement de solutions."
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

# <a name="user-defined-schemas-in-sql-data-warehouse"></a>Schémas définis par l’utilisateur dans le magasin de données SQL

Entrepôts de données traditionnelles utilisent souvent des bases de données distinctes pour créer des limites d’application basées sur la charge de travail, domaine ou sécurité. Par exemple, un entrepôt de données traditionnel de SQL Server peut-être inclure une base de données intermédiaire, une base de données du magasin de données et des données de bases de données mart. Dans cette topologie, chaque base de données fonctionne comme une charge de travail et la frontière de sécurité dans l’architecture.

En revanche, SQL Data Warehouse s’exécute à la charge de travail du magasin de données dans une base de données. Les jointures ne sont pas autorisés entre la base de données. Par conséquent, SQL Data Warehouse attend toutes les tables utilisées par l’entrepôt pour être stockées dans la base d’un données.

> [AZURE.NOTE] SQL Data Warehouse ne gère pas les requêtes de base de données croisée de n’importe quel type. Par conséquent, les implémentations de data warehouse qui exploitent ce modèle devrez révisée.

## <a name="recommendations"></a>Recommandations

Il s’agit de recommandations pour la consolidation des limites fonctionnelles, la sécurité, le domaine et les charges de travail en utilisant les schémas définis par l’utilisateur

1. Une base de données SQL Data Warehouse permet d’exécuter votre charge de travail du magasin de données
2. Consolider votre environnement d’entrepôt de données existant pour utiliser une base de données SQL Data Warehouse
3. Exploiter les **schémas définis par l’utilisateur** pour fournir la limite précédemment implémentée à l’aide de bases de données.

Si les schémas définis par l’utilisateur n’ont pas été utilisés auparavant vous avez vierge. Simplement utiliser l’ancien nom de la base de données comme base de vos schémas définis par l’utilisateur dans la base de données SQL Data Warehouse.

Si les schémas ont déjà été utilisées puis vous disposez de plusieurs options :

1. Supprimer les noms de schémas hérités et recommencer
2. Conserver les noms de schémas existants en attente avant le nom de schéma hérités au nom de table
3. Conserver les noms de schémas hérités par l’implémentation de vues sur la table dans un schéma supplémentaire pour recréer l’ancienne structure de schéma.

> [AZURE.NOTE] Inspection de première option 3 peut sembler identique à l’option la plus intéressante. Toutefois, le diable est dans le détail. Les vues sont lus uniquement dans le magasin de données SQL. Toute modification apportée aux données ou la table devra être effectuée par rapport à la table de base. L’option 3 introduit également une couche de vues dans votre système. Vous pouvez souhaiter à présent une réflexion supplémentaire si vous utilisez déjà des vues de votre architecture.


### <a name="examples"></a>Exemples :

Implémenter les schémas définis par l’utilisateur basés sur les noms de base de données

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Conserver les noms de schémas hérités par préalable en attente pour le nom de la table. Utilisez des schémas pour la limite de charge de travail.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Conserver les noms de schéma hérités à l’aide de vues

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [AZURE.NOTE] Toute modification de la stratégie de schéma a besoin d’une révision du modèle de sécurité pour la base de données. Dans de nombreux cas, vous pourrez simplifier le modèle de sécurité en attribuant des autorisations au niveau du schéma. Si des autorisations plus précises sont nécessaires, vous pouvez utiliser des rôles de base de données.

## <a name="next-steps"></a>Étapes suivantes
Pour plus de conseils de développement, consultez [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble du développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
