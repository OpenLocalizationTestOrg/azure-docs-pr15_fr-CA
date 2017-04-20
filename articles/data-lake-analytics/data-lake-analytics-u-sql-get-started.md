<properties
   pageTitle="Développer des scripts SQL-U à l’aide des outils de données LAC pour Visual Studio | Azure"
   description="Apprenez à installer les outils de LAC de données pour Visual Studio, le développement et les scripts de test SQL-U. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>Didacticiel : Mise en route de la langue du lac de données Azure Analytique U-SQL

U-SQL est un langage qui unifie les avantages de SQL avec la puissance expressive de votre propre code pour traiter toutes les données à n’importe quelle échelle. Évolutive de requête distribuée capacité du U-SQL vous permet d’analyser efficacement les données dans le magasin et dans les bases relationnelles comme base de données de SQL Azure.  Il permet au processus de données non structurées en appliquant le schéma à lire, insérer une logique personnalisée et du fichier UDF et inclut l’extensibilité pour activer l’exécution à l’échelle de contrôler finement granulé. Pour en savoir plus sur la philosophie de conception derrière U-SQL, reportez-vous à ce [billet de blog de Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Il existe quelques différences dans ANSI SQL ou T-SQL. Par exemple, ses mots-clés telles que SELECT doivent être en majuscules.

Il s’agit de langage de type système et expression dans les clauses select, où les prédicats etc. sont en C#.
Cela signifie que les types de données sont les types C# et les types de données utilisent la sémantique C# NULL et les opérations de comparaison à l’intérieur d’un prédicat suivent la syntaxe C# (par exemple, un == « foo »).  Cela signifie aussi, que les valeurs sont des objets .NET complètes, ce qui vous permet d’utiliser facilement n’importe quelle méthode sur l’objet (par exemple, « f o o o ». Split(' ')).

Pour plus d’informations, consultez [Référence de U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

###<a name="prerequisites"></a>Conditions préalables

Vous devez terminer [didacticiel : développement de scripts U-SQL à l’aide des outils de données LAC pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

Dans le didacticiel, vous avez exécuté une tâche de données lac Analytique avec le script U-SQL suivant :

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Ce script ne possède pas toutes les étapes de transformation. Il lit à partir du fichier source appelé **SearchLog.tsv**, il schematizes et renvoie le jeu de lignes dans un fichier appelé **SearchLog-premier-u-sql.csv**.

Notez le point d’interrogation à côté du type de données du champ de durée. Cela signifie que le champ de durée peut être null.

Certains concepts et mots clés dans le script :

- **Variables de l’ensemble de lignes**: chaque expression de requête qui produit un ensemble de lignes peut être assignée à une variable. U-SQL suit le modèle de dénomination de variable de T-SQL, par exemple, **@searchlog** dans le script.
    Notez que l’affectation ne force pas l’exécution. Il nomme l’expression et vous donne la possibilité d’accumulation simplement des expressions plus complexes.
- **Extraire** vous donne la possibilité de définir un schéma lors de la lecture. Le schéma est spécifié par un nom de colonne et d’une paire nom de type C# par colonne. Il utilise un dite **Extracteur**, par exemple, **Extractors.Tsv()** pour extraire les fichiers tsv. Vous pouvez développer des extracteurs personnalisés.
- Prend un ensemble de lignes de **sortie** et sérialise. Le Outputters.Csv() de sortie un fichier séparé par des virgules dans l’emplacement spécifié. Vous pouvez également développer Outputters personnalisés.
- Notez les deux chemins chemins d’accès relatifs. Vous pouvez également utiliser des chemins d’accès absolus.  Par exemple

        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Vous devez utiliser le chemin d’accès absolu à accéder aux fichiers dans les comptes de stockage liés.  La syntaxe pour les fichiers stockés dans le compte de stockage Azure lié est :

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Conteneur de Blob Azure avec BLOB public ou d’autorisations d’accès de conteneurs publics ne sont pas actuellement pris en charge.

## <a name="use-scalar-variables"></a>Utiliser des variables scalaires

Vous pouvez utiliser des variables scalaires ainsi afin de faciliter la maintenance de votre script. Le script SQL-U précédent peut également s’écrire comme suit :

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformer des ensembles de lignes

Permet de **Sélectionner** pour transformer les jeux de lignes :

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

La clause WHERE utilise [C# expression booléenne](https://msdn.microsoft.com/library/6a71f45d.aspx). Vous pouvez utiliser le langage des expressions C# pour faire vos propres fonctions et expressions. Vous pouvez même effectuer un filtrage plus complexes en les combinant avec un conjonctions logiques (et) et disjunctions (ORs).

Le script suivant utilise la méthode DateTime.Parse() et une association.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

Notez que la seconde requête fonctionne sur le résultat de l’ensemble de lignes du premier, et donc le résultat est une composition des deux filtres. Vous pouvez également réutiliser un nom de variable et les noms ont une portée limitées sur le plan lexical.

## <a name="aggregate-rowsets"></a>Jeux de lignes du regroupement

U-SQL vous propose des familiariser **ORDER BY**, **GROUP BY** et les agrégations.

La requête suivante recherche la durée totale par région et renvoie ensuite le haut 5 durées dans l’ordre.

Les ensembles de lignes U-SQL ne conservent pas leur ordre pour la prochaine requête. Pour une sortie de la commande, vous devez donc ajouter ORDER BY à l’instruction OUTPUT, comme illustré ci-dessous :

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

U-SQL ORDER BY clause doit être combiné avec la clause FETCH dans une expression SELECT.

U-SQL comportant une clause peut être utilisée pour restreindre la sortie aux groupes qui remplissent la condition HAVING :

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>Joindre des données

U-SQL fournit les opérateurs de jointure communes telles que jointure interne, les jointure externe gauche/droite/FULL, SEMI JOIN, joindre non seulement les tables mais les jeux de lignes (même celles produites à partir de fichiers).

Le script suivant la searchlog avec un journal d’impression annonce les jointures et nous donne les publications associées à la chaîne de requête pour une date donnée.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL prend uniquement en charge la syntaxe de jointure compatible ANSI : prédicat de jointure de Rowset1 Rowset2 suite. L’ancienne syntaxe d’à partir de Rowset1, où Rowset2 de prédicat n’est pas pris en charge.
Le prédicat de jointure est une jointure d’égalité et aucune expression. Si vous souhaitez utiliser une expression, l’ajouter à la clause select d’une précédente le jeu de lignes. Si vous souhaitez effectuer une comparaison de différente, vous pouvez le déplacer dans la clause WHERE.


## <a name="create-databases-table-valued-functions-views-and-tables"></a>Créer des bases de données, des fonctions table, des vues et des tables

U-SQL vous permet d’utiliser des données dans le contexte d’une base de données et le schéma. Par conséquent, vous n’avez pas toujours lire ou écrire dans des fichiers.

Chaque script U-SQL s’exécute avec une base de données par défaut (maître) et du schéma par défaut (DBO) comme son contexte par défaut. Vous pouvez créer votre propre base de données et/ou le schéma. Pour modifier le contexte, utilisez l’instruction **USE** pour changer de contexte.


### <a name="create-a-table-valued-function-tvf"></a>Créer une fonction table (TVF)

Dans le script SQL-U précédent, vous répétées à l’aide d’extrait de lecture à partir du même fichier source. Fonction table SQL-U vous permet d’encapsuler les données pour une réutilisation ultérieure.   

Le script suivant crée une table appelée *Searchlog()* dans la base de données et le schéma :

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

Le script suivant vous montre comment utiliser la fonction table définie dans le script précédent :

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>Créer des vues

Si vous n’avez qu’une expression de requête que vous souhaitez extraire et que vous ne voulez pas paramétrer, vous pouvez créer une vue au lieu d’une fonction table.

Le script suivant crée une vue appelée *SearchlogView* dans la base de données et le schéma :

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

Le script suivant illustre l’utilisation de la vue définie :

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>Créer des tables

Semblable à la table de base de données relationnelle, U-SQL vous permet de créer une table avec un schéma prédéfini ou créer une table et déduit le schéma à partir de la requête qui remplit la table (également appelé CREATE TABLE AS SELECT ou SACT).

Le script suivant créer une base de données et deux tables :

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    PARTITIONED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>Tables de requête

Vous pouvez interroger les tables (créés dans le script précédent) de la même façon en tant que requête vous sur les fichiers de données. Au lieu de créer un ensemble de lignes à l’aide d’extrait, vous maintenant pouvez simplement faire référence au nom de table.

Le script de transformation que vous avez utilisé précédemment est modifié pour lire à partir des tables :

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Notez qu’actuellement impossible d’exécuter une instruction SELECT sur une table dans le même script que le script où vous créez cette table.


##<a name="conclusion"></a>Conclusion

Ce qui est couvert dans le didacticiel est uniquement une petite partie de U-SQL. En raison de la portée de ce didacticiel, il ne peut pas couvrir tous les éléments, tels que :

- Utilisation CROSS APPLY pour décompresser des parties de cartes, des tableaux et des chaînes en lignes.
- Fonctionnement partitionnée d’ensembles de données (tables partitionnées et ensembles de fichiers).
- Développer les opérateurs définis par l’utilisateur telles que les extracteurs, outputters, processeurs, défini par l’utilisateur des agrégateurs dans C#.
- Utiliser les fonctions de fenêtrage U-SQL.
- Gérer le code de U-SQL avec les procédures stockées, des fonctions table et des vues.
- Exécuter du code personnalisé arbitraire sur les nœuds de traitement.
- Se connecter aux bases de données SQL Azure et aux requêtes federate entre eux et vos données SQL-U et lac de données Azure.

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble de Microsoft Azure données lac Analytique](data-lake-analytics-overview.md)
- [Développer des scripts SQL-U à l’aide des outils de données LAC pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [À l’aide des fonctions de fenêtre U-SQL pour les travaux d’Azure données lac Analytique](data-lake-analytics-use-window-functions.md)
- [Surveiller et résoudre les problèmes de travaux Azure données lac Analytique à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>Faites-nous savoir ce que vous pensez

- [Soumettre une demande de fonctionnalité](http://aka.ms/adlafeedback)
- [Obtenir de l’aide dans les forums](http://aka.ms/adlaforums)
- [Fournir des commentaires sur SQL-U](http://aka.ms/usqldiscuss)
