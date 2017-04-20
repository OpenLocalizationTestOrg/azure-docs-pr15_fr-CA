<properties
   pageTitle="Migration du code SQL pour SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour la migration de votre code SQL pour l’entrepôt de données SQL Azure pour le développement de solutions."
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
   ms.date="08/02/2016"
   ms.author="lodipalm;barbkess;sonyama;jrj"/>

# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migration du code SQL pour SQL Data Warehouse

Lors de la migration de votre code à partir d’une base de données SQL Data Warehouse, vous aurez probablement besoin d’apporter des modifications à votre base de code. Certaines fonctionnalités de SQL Data Warehouse peuvent améliorer considérablement les performances car ils sont conçus pour fonctionner en mode distribué. Toutefois, pour maintenir les performances et évolutivité, certaines fonctionnalités sont également pas disponibles.

## <a name="common-t-sql-limitations"></a>Limitations de T-SQL courantes

La liste suivante récapitule la fonctionnalité plus courants qui ne sont pas pris en charge dans le magasin de données SQL Azure. Les liens vous dirigent vers des solutions de contournement pour la fonctionnalité non prise en charge :

- [Jointures en ANSI sur les mises à jour][]
- [Jointures en ANSI sur les suppressions][]
- [instruction MERGE][]
- jointures croisées-base de données
- [curseurs][]
- [SÉLECTIONNEZ... DANS][]
- [INSÉRER... EXEC][]
- clause de sortie
- fonctions définies par l’utilisateur inline
- fonctions de plusieurs instructions
- [expressions de table communes](#Common-table-expressions)
- [expressions de table communes (CTE)] (#Recursive-common-table-expressions-(CTE)
- Procédures et fonctions CLR
- fonction de $partition
- variables de table
- paramètres de valeur de table
- transactions distribuées
- COMMIT / rollback de travail
- enregistrer des transactions
- contextes d’exécution (EXECUTE AS)
- [groupe par clause avec correctif cumulatif / cube / définit les options de regroupement][]
- [niveaux d’imbrication au-delà de 8][]
- [mise à jour par l’intermédiaire de vues][]
- [utilisation de l’instruction select pour une affectation de variable][]
- [aucune donnée de MAX ne type pour les chaînes SQL dynamiques][]

Heureusement la plupart de ces limitations peut être contournée. Explications sont fournies dans les articles de développement pertinentes que référencés ci-dessus.

## <a name="supported-cte-features"></a>Fonctionnalités prises en charge de CTE

Les expressions de table communes (CTE) sont partiellement pris en charge dans le magasin de données SQL.  Les fonctionnalités CTE suivantes sont actuellement prises en charge :

- Une CTE peut être spécifiée dans une instruction SELECT.
- Une CTE peut être spécifiée dans une instruction CREATE VIEW.
- Une CTE peut être spécifiée dans une instruction de création TABLE comme sélectionner (SACT).
- Une CTE peut être spécifiée dans une instruction de créer à distance TABLE comme sélectionner (CRTAS).
- Une CTE peut être spécifiée dans une instruction de créer externe TABLE comme sélectionner (CETAS).
- Une table distante peut être référencée à partir d’une CTE.
- Une table externe peut être référencée à partir d’une CTE.
- Plusieurs définitions de requête CTE peuvent être définies dans une CTE.

## <a name="cte-limitations"></a>Limitations de la CTE

Expressions de table communes ont des limitations dans l’entrepôt de données SQL, y compris :

- Une CTE doit être suivie d’une instruction SELECT unique. INSERT, UPDATE, DELETE, et les instructions de fusion ne sont pas pris en charge.
- Une expression de table commune qui inclut des références à elle-même (expression de table commune récursive) n’est pas pris en charge (voir ci-dessous la section).
- Spécifier plusieurs avec clause dans une CTE n’est pas autorisée. Par exemple, si un CTE_query_definition contient une sous-requête, cette sous-requête ne peut pas contenir un élément imbriqué avec une clause qui définit une autre CTE.
- Une clause ORDER BY ne peut pas servir dans la CTE_query_definition, sauf lorsqu’une clause TOP est spécifiée.
- Lorsqu’une CTE est utilisée dans une instruction qui fait partie d’un lot, l’instruction avant qu’il doit être suivie par un point-virgule.
- Lorsqu’il est utilisé dans les instructions préparées par sp_prepare, CTE seront comporte de la même manière que les autres instructions SELECT dans l’Assistant Empaquetage et déploiement. Toutefois, si la CTE sont utilisés dans le cadre de CETAS préparée par sp_prepare, le comportement peut différer à partir de SQL Server et les autres instructions de l’Assistant Empaquetage et déploiement en raison de la manière dont la liaison est implémentée pour sp_prepare. Si l’option références que CTE est à l’aide d’une mauvaise colonne qui n’existe pas dans la CTE, le sp_prepare transmettra sans détecter l’erreur que l’erreur est levée pendant sp_execute à la place.

## <a name="recursive-ctes"></a>CTE récursive

Les CTE récursive ne sont pas pris en charge dans le magasin de données SQL.  La migraion de CTE récursive peut être quelque peu complète et la meilleure procédure est pour décomposer l’en plusieurs étapes. Vous pouvez généralement utiliser une boucle et remplir une table temporaire que vous parcourez les requêtes intermédiaires récursives. Une fois la table temporaire est remplie, vous pouvez puis renvoyer les données sous la forme d’un seul jeu de résultats. Une approche similaire a été utilisée pour résoudre les `GROUP BY WITH CUBE` dans l’article [groupe par clause avec correctif cumulatif / cube / définit les options de regroupement][] .

## <a name="unsupported-system-functions"></a>Fonctions système non pris en charge

Il existe également certaines fonctions du système qui ne sont pas pris en charge. Les principales que vous trouverez généralement utilisés dans les entrepôts de données sont les suivantes :

- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

Certains de ces problèmes peuvent être contourné.

## <a name="rowcount-workaround"></a>@@ROWCOUNTsolution de contournement

Pour contourner l’absence de prise en charge de @@ROWCOUNT, créer une procédure stockée qui va récupérer le dernier compte de lignes à partir de sys.dm_pdw_request_steps, puis exécutez `EXEC LastRowCount` après une instruction DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une liste complète de toutes les instructions T-SQL pris en charge, consultez [les rubriques de Transact-SQL][].

<!--Image references-->

<!--Article references-->
[Jointures en ANSI sur les mises à jour]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Jointures en ANSI sur les suppressions]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[instruction MERGE]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSÉRER... EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Rubriques de Transact-SQL]: ./sql-data-warehouse-reference-tsql-statements.md

[curseurs]: ./sql-data-warehouse-develop-loops.md
[SÉLECTIONNEZ... DANS]: ./sql-data-warehouse-develop-ctas.md#selectinto
[groupe par clause avec correctif cumulatif / cube / définit les options de regroupement]: ./sql-data-warehouse-develop-group-by-options.md
[niveaux d’imbrication au-delà de 8]: ./sql-data-warehouse-develop-transactions.md
[mise à jour par l’intermédiaire de vues]: ./sql-data-warehouse-develop-views.md
[utilisation de l’instruction select pour une affectation de variable]: ./sql-data-warehouse-develop-variable-assignment.md
[aucune donnée de MAX ne type pour les chaînes SQL dynamiques]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
