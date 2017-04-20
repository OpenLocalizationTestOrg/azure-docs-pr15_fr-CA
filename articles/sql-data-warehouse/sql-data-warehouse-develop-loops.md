<properties
   pageTitle="Boucles dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour les boucles de Transact-SQL et les curseurs de remplacement dans l’entrepôt de données SQL Azure pour le développement de solutions."
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

# <a name="loops-in-sql-data-warehouse"></a>Boucles dans l’entrepôt de données SQL
SQL Data Warehouse prend en charge la boucle [WHILE][] pour exécuter à plusieurs reprises des blocs d’instructions. Cela va continuer pour tant que les conditions spécifiées sont vraies, ou jusqu'à ce que le code spécifiquement met fin à la boucle à l’aide de la `BREAK` mot clé. Boucles sont particulièrement utiles pour remplacer des curseurs définies dans le code SQL. Heureusement, pratiquement tous les curseurs qui sont écrits en code SQL de l’avance rapide, lecture des diverses uniquement. Boucles [WHILE] sont donc une alternative intéressante si vous vous trouvez avoir à remplacer un.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Exploitation des boucles et le remplacement des curseurs SQL Data Warehouse
Toutefois, avant de plonger dans la tête tout d’abord vous devez vous poser la question suivante : « Pourrait ce curseur être ré-écrite pour utiliser opérations ensemblistes sur ? ». Dans de nombreux cas, la réponse est Oui et est souvent la meilleure approche. Souvent d’une opération de jeu de base s’exécute bien plus rapidement qu’une approche itérative, ligne par ligne.

Les curseurs en lecture seule avance rapide peuvent être facilement remplacés par une construction en boucle. Voici un exemple simple. Cet exemple de code met à jour les statistiques pour chaque table dans la base de données. En effectuant une itération sur les tables dans la boucle, nous sommes en mesure d’exécuter chaque commande dans la séquence.

Tout d’abord, créez une table temporaire contenant un numéro de ligne unique utilisé pour identifier les instructions individuelles :

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

En second lieu, initialiser les variables nécessaires pour exécuter la boucle :

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Maintenant une boucle sur les instructions exécutées une à la fois :

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Pour finir de supprimer la table temporaire créée dans la première étape

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Étapes suivantes
Pour plus de conseils de développement, consultez [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble du développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
