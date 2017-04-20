<properties
   pageTitle="Assigner des variables SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’affectation des variables Transact-SQL dans le magasin de données SQL Azure pour le développement de solutions."
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

# <a name="assign-variables-in-sql-data-warehouse"></a>Assigner des variables SQL Data Warehouse
Dans le magasin de données SQL, les variables sont définies à l’aide de la `DECLARE` instruction ou de la `SET` instruction.

Tous les éléments suivants sont des méthodes parfaitement valides pour définir la valeur d’une variable :

## <a name="setting-variables-with-declare"></a>Définition de variables avec DECLARE

L’initialisation de variables avec DECLARE est un des moyens plus flexibles pour définir la valeur d’une variable SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Vous pouvez également utiliser DECLARE pour définir plusieurs variables à la fois. Vous ne pouvez pas utiliser `SELECT` ou `UPDATE` pour cela :

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Vous ne peut pas initialiser et d’utiliser une variable dans la même instruction DECLARE. Pour illustrer le point de l’exemple ci-dessous n’est **pas** autorisé en tant que @p1 est initialisé et utilisé dans la même instruction DECLARE. Cela provoquera une erreur.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Valeurs de paramètre dont la valeur
Ensemble est une méthode très courante pour définir une variable unique.

Tous les exemples ci-dessous sont des méthodes valides pour la définition d’une variable dont la valeur :

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Vous ne pouvez définir qu’une seule variable à la fois avec le jeu. Toutefois, comme indiqué ci-dessus opérateurs composés sont permise.

## <a name="limitations"></a>Limitations
Vous ne pouvez pas utiliser SELECT ou UPDATE pour une affectation de variable.


## <a name="next-steps"></a>Étapes suivantes
Pour plus de conseils de développement, consultez [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble du développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
