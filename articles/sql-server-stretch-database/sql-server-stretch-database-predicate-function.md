<properties
    pageTitle="Sélectionner les lignes à migrer à l’aide d’une fonction de filtre (étirement de la base de données) | Microsoft Azure"
    description="Apprenez à sélectionner les lignes à migrer à l’aide d’une fonction de filtre."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="douglasl"/>

# <a name="select-rows-to-migrate-by-using-a-filter-function-stretch-database"></a>Sélectionner les lignes à migrer à l’aide d’une fonction de filtre (étirement de la base de données)

Si vous stockez des données dans une table distincte, vous pouvez configurer la base de données de l’étirement pour migrer l’ensemble du tableau. Si votre table contient des données à la fois à chaud et à froid, d’autre part, vous pouvez spécifier une fonction de filtre pour sélectionner les lignes à migrer. Le prédicat de filtre est un tableau inline\-valeur de fonction. Cette rubrique décrit comment écrire une table inline\-valeurs de fonction pour sélectionner les lignes à migrer.

>   [AZURE.NOTE] Si vous fournissez une fonction de filtre qui fonctionne mal, migration de données effectue également peu. Étirement de base de données applique la fonction de filtre à la table à l’aide de l’opérateur CROSS APPLY.

Si vous ne spécifiez pas une fonction de filtre, l’ensemble du tableau est migré.

Lorsque vous exécutez la base de données de l’activer pour étirer Assistant, vous pouvez migrer une table entière, ou vous pouvez spécifier une fonction de filtre simple dans l’Assistant. Si vous souhaitez utiliser un autre type de fonction de filtre pour sélectionner les lignes à migrer, effectuez l’une des opérations suivantes.

-   Quittez l’Assistant et exécutez l’instruction ALTER TABLE pour permettre l’étirement pour la table et spécifier une fonction de filtre.

-   Exécutez l’instruction ALTER TABLE pour spécifier une fonction de filtre lorsque vous quittez l’Assistant.

La syntaxe ALTER TABLE pour ajouter une fonction est décrite plus loin dans cette rubrique.

## <a name="basic-requirements-for-the-filter-function"></a>Exigences de base pour la fonction de filtre
Le tableau inline\-requise pour un prédicat de filtre de base de données de l’étirement de la fonction ressemble à l’exemple suivant.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE <predicate>
```
Les paramètres de la fonction doivent être des identificateurs pour les colonnes de la table.

La liaison de schéma est nécessaire pour éviter les colonnes qui sont utilisées par la fonction de filtre d’être supprimé ou modifié.

### <a name="return-value"></a>Valeur de retour
Si la fonction renvoie un non\-de résultats vide, la ligne est susceptible d’être migrés. Sinon, \- , si la fonction ne renvoie pas un résultat \- la ligne n’est pas susceptible d’être migrés.

### <a name="conditions"></a>Conditions
Le &lt; *prédicat* &gt; peut comporter une condition unique ou de plusieurs conditions reliées par l’opérateur logique et.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
Chaque condition peut à son tour se composent d’une condition primitif ou de plusieurs conditions primitifs jointes par l’opérateur logique OR.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### <a name="primitive-conditions"></a>Conditions primitifs
Une condition primitif effectuez l’une des comparaisons suivantes.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   Comparer un paramètre de fonction dans une expression de constante. Par exemple, `@column1 < 1000`.

    Voici un exemple qui vérifie si la valeur d’une colonne de *date* est &lt; 1/1/2016.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Appliquer l’opérateur IS NULL ou IS NOT NULL à un paramètre de fonction.

-   Utilisez l’opérateur IN pour comparer un paramètre de fonction à une liste de valeurs de constante.

    Voici un exemple qui vérifie si la valeur d’un *expédition\_état* colonne est `IN (N'Completed', N'Returned', N'Cancelled')`.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

### <a name="comparison-operators"></a>Opérateurs de comparaison
Les opérateurs de comparaison suivants sont pris en charge.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### <a name="constant-expressions"></a>Expressions de constante
L’une des constantes que vous utilisez dans une fonction de filtre peuvent être n’importe quelle expression déterministe qui peut être évaluée lors de la définition de la fonction. Les expressions de constante peuvent contenir les éléments suivants.

-   Littéraux. Par exemple, `N’abc’, 123`.

-   Par des expressions algébriques. Par exemple, `123 + 456`.

-   Fonctions déterministes. Par exemple, `SQRT(900)`.

-   Déterministes conversions qui utilisent la fonction CAST ou CONVERT. Par exemple, `CONVERT(datetime, '1/1/2016', 101)`.

### <a name="other-expressions"></a>Autres expressions
Si la fonction qui en résulte est conforme aux règles décrites ici après avoir remplacé le BETWEEN et non entre des opérateurs avec les expressions équivalente et et ou, vous pouvez utiliser l’entre opérateurs et NOT BETWEEN.

Vous ne pouvez pas utiliser de sous-requêtes ou non\-les fonctions déterministes RAND() ou GETDATE().

## <a name="add-a-filter-function-to-a-table"></a>Ajouter une fonction de filtre dans une table
Ajouter une fonction de filtre à une table en exécutant l’instruction **ALTER TABLE** et en spécifiant un tableau inline existant\-valeur de fonction comme valeur de la **filtre\_PRÉDICAT** paramètre. Par exemple :

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
) )
```
Après avoir lié la fonction à la table en tant que prédicat, les éléments suivants sont vrais.

-   La migration des données de temps suivante se produit, uniquement les lignes pour lesquelles la fonction retourne un non\-valeur vide sont migrés.

-   Les colonnes utilisées par la fonction sont liée au schéma. Vous ne pouvez pas modifier ces colonnes dans la mesure où une table est à l’aide de la fonction en tant que le prédicat de filtre.

Vous ne pouvez pas supprimer la table inline\-valeur de fonction dans la mesure où une table est à l’aide de la fonction en tant que le prédicat de filtre.

>   [AZURE.NOTE] Pour améliorer les performances de la fonction de filtre, créez un index sur les colonnes utilisées par la fonction.

### <a name="passing-column-names-to-the-filter-function"></a>Noms de colonnes en passant à la fonction de filtre
Lorsque vous affectez une fonction de filtre à une table, spécifiez les noms de colonne est passé à la fonction de filtre avec un nom d’une seule partie. Si vous spécifiez un nom en trois parties lorsque vous passez de la colonne des noms, les requêtes suivantes par rapport à l’étirement de la\-table activée échouera.

Par exemple, si vous spécifiez un nom de colonne de trois parties comme indiqué dans l’exemple suivant, l’instruction s’exécute avec succès, mais les requêtes suivantes par rapport à la table échoue.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

Spécifiez plutôt la fonction de filtre avec un nom de colonne d’une seule partie, comme illustré dans l’exemple suivant.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="addafterwiz"></a>Ajouter une fonction de filtre après l’exécution de l’Assistant  

Si vous souhaitez utiliser une fonction que vous ne pouvez pas créer de l’Assistant **Activer la base de données pour étirer** , vous pouvez exécuter l’instruction ALTER TABLE pour spécifier une fonction, une fois que vous quittez l’Assistant. Avant d’appliquer une fonction, toutefois, vous devez arrêter la migration de données qui est déjà en cours et de restaurer des données migrées. (Pour plus d’informations sur pourquoi cela est nécessaire, consultez [Remplacer une fonction de filtre existant](#replacePredicate).  

1. Inverser le sens de la migration et restaurer que les données déjà migrées. Vous ne pouvez pas annuler cette opération après son démarrage. Vous encourez également les coûts d’Azure pour les transferts de données sortantes \(sortie\). Pour plus d’informations, consultez [comment Azure tarification works](https://azure.microsoft.com/pricing/details/data-transfers/).  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  

2. Attendez terminer la migration. Vous pouvez vérifier le statut dans le **Moniteur de base de données Stretch** à partir de SQL Server Management Studio, ou vous pouvez interroger la vue **sys.dm_db_rda_migration_status** . Pour plus d’informations, consultez [moniteur et résoudre les problèmes de migration de données](sql-server-stretch-database-monitor.md) ou [sys.dm_db_rda_migration_status](https://msdn.microsoft.com/library/dn935017.aspx).  

3. Créez la fonction de filtre que vous souhaitez appliquer à la table.  

4. Ajoutez la fonction dans la table et redémarrez la migration de données vers Azure.  

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## <a name="filter-rows-by-date"></a>Filtrer les lignes par date
L’exemple suivant effectue la migration des lignes dont la colonne **date** contient une valeur antérieure à la date du 1 janvier 2016.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## <a name="filter-rows-by-the-value-in-a-status-column"></a>Filtrer par la valeur dans une colonne d’état des lignes
L’exemple suivant fait migrer les lignes où la colonne de **statut** contient une des valeurs spécifiées.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## <a name="filter-rows-by-using-a-sliding-window"></a>Filtrer les lignes à l’aide d’une fenêtre glissante
Pour filtrer les lignes à l’aide d’une fenêtre glissante, gardez à l’esprit les besoins suivants pour la fonction de filtre.

-   La fonction doit être déterministe. Par conséquent, vous ne peut pas créer une fonction qui recalcule automatiquement la fenêtre coulissante avec le temps.

-   La fonction utilise la liaison de schéma. Par conséquent vous ne peut pas simplement mettre à jour la fonction « sur place » tous les jours en appelant l' **Instruction ALTER FUNCTION** pour déplacer la fenêtre glissante.

Démarrez avec une fonction de filtre comme dans l’exemple suivant, qui permet de migrer les lignes dont la colonne **systemEndTime** contient une valeur antérieure à la date du 1 janvier 2016.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

Appliquer la fonction de filtre à la table.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

Lorsque vous souhaitez mettre à jour la fenêtre coulissante, effectuer les opérations suivantes.

1.  Créer une nouvelle fonction qui spécifie la nouvelle fenêtre glissante. L’exemple suivant sélectionne les dates au plus tôt le 2 janvier 2016, au lieu du 1 janvier 2016.

2.  Remplacez la fonction de filtre précédente par le nouveau en appelant **ALTER TABLE**, comme illustré dans l’exemple suivant.

3. Si vous le souhaitez, supprimer la fonction filtre précédent que vous utilisez n’est plus en appelant **DROP FUNCTION**. (Cette étape n’est pas affichée dans l’exemple).

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as the filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## <a name="more-examples-of-valid-filter-functions"></a>Obtenir d’autres exemples de fonctions de filtre valide

-   L’exemple suivant combine deux conditions primitives à l’aide de l’opérateur logique et.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   L’exemple suivant utilise plusieurs conditions et une conversion déterministe à convertir.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   L’exemple suivant utilise des fonctions et des opérateurs mathématiques.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   L’exemple suivant utilise l’entre opérateurs et NOT BETWEEN. Cette utilisation est valide car la fonction qui en résulte est conforme aux règles décrites ici après avoir remplacé le BETWEEN et non entre des opérateurs avec les expressions équivalente et et ou.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 BETWEEN 0 AND 100
                AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    La fonction précédente est équivalente à la fonction suivante après avoir remplacé l’entre opérateurs et NOT BETWEEN avec les expressions équivalente et et ou.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## <a name="examples-of-filter-functions-that-arent-valid"></a>Exemples de fonctions de filtre ne sont pas valides

-   La fonction suivante n’est pas valide car il contient un élément non\-conversion déterministe.

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   La fonction suivante n’est pas valide car il contient un élément non\-appel de fonction déterministe.

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   La fonction suivante n’est pas valide car il contient une sous-requête.

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   Les fonctions suivantes ne sont pas valides, car les expressions qui utilisent des opérateurs algébriques ou généré\-dans les fonctions doit correspondre à une constante lors de la définition de la fonction. Impossible d’inclure des références de colonne dans des expressions algébriques ou des appels de fonction.

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE SQRT(@column1) = 30
    GO
    ```

-   La fonction suivante n’est pas valide car il ne respecte pas les règles décrites ici, une fois que vous remplacez l’opérateur entre l’expression et équivalente.

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    La fonction précédente est équivalente à la fonction suivante une fois que vous remplacez l’opérateur entre l’expression et équivalente. Cette fonction n’est pas valide car les conditions primitives peuvent uniquement utiliser l’opérateur logique OR.

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## <a name="how-stretch-database-applies-the-filter-function"></a>La base de données de l’étirement de s’applique la fonction de filtre
Étirement de base de données applique la fonction de filtre à la table et détermine les lignes éligibles à l’aide de l’opérateur CROSS APPLY. Par exemple :

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Si la fonction renvoie un non\-de résultats vide de la ligne, la ligne est susceptible d’être migrés.

## <a name="replacePredicate"></a>Remplacer une fonction de filtre existant
Vous pouvez remplacer une fonction de filtre précédemment spécifié en exécutant l’instruction **ALTER TABLE** à nouveau et en spécifiant une nouvelle valeur pour le **filtre\_PRÉDICAT** paramètre. Par exemple :

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
```
La nouvelle table inline\-fonction requise est la suivante.

-   La nouvelle fonction doit être moins restrictives que la fonction précédente.

-   Tous les opérateurs qui existaient dans l’ancienne fonction doivent exister dans la nouvelle fonction.

-   La nouvelle fonction ne peut pas contenir d’opérateurs qui n’existent pas dans l’ancienne fonction.

-   Impossible de modifier l’ordre des arguments de l’opérateur.

-   Uniquement les valeurs de constante qui font partie d’une `<, <=, >, >=` comparaison peut être modifié d’une manière qui rend la fonction moins restrictives.

### <a name="example-of-a-valid-replacement"></a>Exemple d’un remplacement valide
Supposons que la fonction suivante est le prédicat de filtre en cours.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
La fonction suivante est un remplacement valide, car la constante de la nouvelle date (ce qui indique une date de démarcation ultérieurement) rend la fonction moins restrictives.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
            AND (@column2 < -50 OR @column2 > 50)
GO
```

### <a name="examples-of-replacements-that-arent-valid"></a>Exemples de remplacement qui ne sont pas valides
La fonction suivante n’est pas un remplacement valide, car la constante de la nouvelle date (ce qui indique une date de démarcation antérieure) ne rend pas la fonction moins restrictif.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
La fonction suivante n’est pas un remplacement valide car un des opérateurs de comparaison a été supprimé.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -50)
GO
```
La fonction suivante n’est pas un remplacement valide car une nouvelle condition a été ajoutée avec l’opérateur logique AND.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
            AND (@column2 <> 0)
GO
```

## <a name="remove-a-filter-function-from-a-table"></a>Supprimer une fonction de filtre d’une table
Pour migrer l’intégralité du tableau au lieu des lignes sélectionnées, supprimer la fonction existante en définissant **filtre\_PRÉDICAT** avec la valeur null. Par exemple :

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = NULL,
    MIGRATION_STATE = <desired_migration_state>
) )
```
Après la suppression de la fonction de filtre, toutes les lignes de la table sont éligibles à la migration. Par conséquent, vous ne pouvez spécifier une fonction de filtre pour la même table ultérieurement, sauf si vous afficher à nouveau toutes les données à distance pour la table à partir d’Azure tout d’abord. Cette restriction existe pour éviter les situations où les lignes qui ne sont pas éligibles à la migration lorsque vous fournissez une nouvelle fonction de filtre ont déjà été migrés vers Azure.

## <a name="check-the-filter-function-applied-to-a-table"></a>Vérifiez la fonction de filtre appliquée à un tableau
Pour vérifier la fonction de filtre appliquée à une table, ouvrez la vue de catalogue **sys.remote\_données\_archive\_tableaux** et vérifiez la valeur de la **filtre\_prédicat** colonne. Si la valeur est null, toute la table est éligible pour l’archivage. Pour plus d’informations, consultez [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="security-notes-for-filter-functions"></a>Notes de sécurité pour les fonctions de filtre  
Un compte compromis, avec des privilèges db_owner peut effectuer les opérations suivantes.  

-   Créez et appliquez une fonction table qui consomme beaucoup de ressources serveur ou attend pendant une période prolongée, ce qui entraîne un déni de service.  

-   Créer et appliquer une fonction à valeur de table qui permet de déduire le contenu d’une table pour laquelle l’utilisateur a été explicitement refusé l’accès en lecture.  

## <a name="see-also"></a>Voir aussi

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
