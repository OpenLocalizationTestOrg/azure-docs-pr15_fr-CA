<properties
   pageTitle="Procédures stockées SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’implémentation de procédures stockées dans l’entrepôt de données SQL Azure pour le développement de solutions."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="stored-procedures-in-sql-data-warehouse"></a>Procédures stockées dans l’entrepôt de données SQL

SQL Data Warehouse prend en charge de nombreuses fonctionnalités Transact-SQL dans SQL Server. Il n’y a surtout mise à l’échelle les fonctionnalités spécifiques que nous souhaitons tirer parti pour optimiser les performances de votre solution.

Toutefois, pour mettre à jour les évolutivité et des performances de SQL Data Warehouse il sont également certaines fonctions et fonctionnalités ayant des différences de comportement et d’autres qui ne sont pas pris en charge.

Cet article explique comment implémenter des procédures stockées dans l’entrepôt de données SQL.

## <a name="introducing-stored-procedures"></a>Présentation des procédures stockées
Les procédures stockées sont un excellent moyen d’encapsuler votre code SQL. stockage proche de vos données dans le data warehouse. En encapsulant le code en unités gérables procédures stockées aident les développeurs à modulariser leurs solutions ; de faciliter une plus grande réutilisation du code. Chaque procédure stockée peut également accepter des paramètres pour les rendre encore plus flexible.

SQL Data Warehouse fournit une implémentation simplifiée et rationalisée de procédure stockée. La plus grande différence par rapport à SQL Server est que la procédure stockée n’est pas code précompilé. Dans les entrepôts de données nous intéressent généralement moins le moment de la compilation. Il est plus important que le code de la procédure stockée est optimisé pour correctement lors d’un fonctionnement par rapport à gros volumes de données. L’objectif est d’enregistrer les heures, les minutes et les secondes, pas aux millisecondes. Il est par conséquent plus comparables à des procédures stockées en tant que conteneurs pour la logique SQL.     

Lorsque l’entrepôt de données SQL exécute la procédure stockée les instructions SQL sont analysées, traduit et optimisées au moment de l’exécution. Au cours de ce processus, chaque instruction est convertie dans les requêtes distribuées. Le code SQL qui est réellement exécuté sur les données est différent de la requête soumise.

## <a name="nesting-stored-procedures"></a>Procédures stockée de l’imbrication
Lorsque les procédures stockées appellent d’autres procédures stockées ou exécuter sql dynamique, l’appel de code ou de la procédure stockée interne est dit être imbriqués.

SQL Data Warehouse prend en charge un maximum de 8 niveaux d’imbrication. Il est légèrement différent pour SQL Server. Le niveau d’imbrication dans SQL Server est de 32.

L’appel de procédure stockée de niveau supérieur est égale pour imbriquer de niveau 1

```sql
EXEC prc_nesting
```
Si la procédure stockée effectue également un autre EXEC appel puis Ceci augmentera le niveau d’imbrication à 2
```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Si la seconde procédure puis exécute des instructions sql dynamiques puis Ceci augmentera le niveau d’imbrication à 3
```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Remarque SQL Data Warehouse ne prend pas en charge @@NESTLEVEL. Vous devez conserver une trace de votre niveau d’imbrication vous-même. Il est peu probable que vous atteignez la limite de niveau de 8 imbrication, mais si vous ne vous devrez travailler de nouveau votre code et « aplatir » afin qu’il tienne dans cette limite.

## <a name="insertexecute"></a>INSÉRER... EXÉCUTER
SQL Data Warehouse ne vous permet pas d’utiliser le jeu de résultats d’une procédure stockée avec une instruction INSERT. Toutefois, il est d’une autre approche, que vous pouvez utiliser.

Veuillez vous reporter à l’article suivant sur les [tables temporaires] pour obtenir un exemple sur la façon de procéder.

## <a name="limitations"></a>Limitations

Il existe certains aspects des procédures stockée de Transact-SQL qui ne sont pas implémentées dans SQL Data Warehouse.

Ils sont les suivantes :

- procédures stockées temporaires
- procédures stockées numérotées
- procédures stockées étendues
- Procédures stockées CLR
- option de chiffrement
- option de réplication
- Paramètres table
- paramètres en lecture seule
- paramètres par défaut
- contextes d’exécution
- return (instruction)

## <a name="next-steps"></a>Étapes suivantes
Pour plus de conseils de développement, consultez [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[tables temporaires]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[vue d’ensemble du développement]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
