 <properties
    pageTitle="Comment faire pour utiliser le traitement par lots pour améliorer les performances des applications de base de données de SQL Azure"
    description="La rubrique fournit la preuve que le traitement par lots des opérations de base de données fortement imroves la vitesse et l’évolutivité de vos applications de base de données de SQL Azure. Bien que ces techniques de traitement par lots fonctionnent pour les bases de données SQL Server, le focus de l’article est sur Azure."
    services="sql-database"
    documentationCenter="na"
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="07/12/2016"
    ms.author="annemill" />

# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Comment faire pour utiliser le traitement par lots pour améliorer les performances de l’application de base de données SQL

Le traitement par lots des opérations de base de données de SQL Azure considérablement améliore les performances et l’évolutivité de vos applications. Pour comprendre les avantages, la première partie de cet article couvre certains résultats de test qui comparent les demandes séquentielles et par lots à une base de données SQL. Le reste de cet article montre les techniques, les scénarios et les considérations pour vous aider à utiliser le traitement par lots avec succès dans vos applications Azure.

**Auteurs**: Jason Roth, Silvano, Coriani, Swanson de Trent (pleine échelle 180 Inc)

**Relecteurs**: Conor Cunningham, Michael Thomassy

## <a name="why-is-batching-important-for-sql-database"></a>Pourquoi est le traitement par lots importants pour SQL de base de données ?
Le traitement par lot des appels à un service à distance est une stratégie bien connue pour augmenter les performances et l’évolutivité. Les coûts de traitement pour les interactions avec un service distant, telles que la sérialisation et la désérialisation transfert réseau précises. Emballage de nombreuses transactions distinctes en un seul lot permet de réduire ces coûts.

Dans ce document, nous souhaitons examiner diverses bases de données SQL le traitement par lots des stratégies et des scénarios. Bien que ces stratégies sont également importantes pour les applications sur site qui utilisent SQL Server, il existe plusieurs raisons pour mettre en surbrillance de l’utilisation du traitement par lots pour la base de données de SQL :

- Est potentiellement plus grande latence du réseau dans l’accès à la base de données de SQL, surtout si vous accédez à l’extérieur du centre de données Microsoft Azure même de la base de données SQL à partir de.
- Les caractéristiques de base de données SQL partagés signifie que l’efficacité de la corrélation de couche accès aux données à l’évolutivité globale de la base de données. Base de données de SQL doit empêcher les clients/utilisateurs de monopoliser les ressources de base de données au détriment des autres locataires. En réponse à l’utilisation de plus de quotas prédéfinis, base de données de SQL peut réduire le débit ou répondre grâce à la limitation des exceptions. L’efficacité, telles que le traitement par lots, vous permettre d’effectuer plus de travail de la base de données SQL avant d’atteindre ces limites. 
- Le traitement par lots est également efficace pour les architectures qui utilisent plusieurs bases de données (ont). L’efficacité de votre interaction avec chaque unité de base de données est toujours un facteur clé dans votre évolutivité globale. 

Un des avantages de l’utilisation de la base de données SQL est que vous n’êtes pas obligé de gérer les serveurs qui hébergent la base de données. Cependant, cette infrastructure gérée signifie également que vous devez penser différemment les optimisations de la base de données. Vous pouvez ne plus rechercher pour améliorer l’infrastructure de réseau ou du matériel de base de données. Microsoft Azure contrôle ces environnements. La zone principale que vous pouvez contrôler est comment votre application interagit avec la base de données de SQL. Le traitement par lots est un de ces optimisations. 

La première partie du papier examine les différentes techniques de traitement par lots pour les applications .NET qui utilisent de la base de données SQL. Les deux dernières sections couvrent le traitement par lots les scénarios et les instructions.

## <a name="batching-strategies"></a>Le traitement par lots des stratégies

### <a name="note-about-timing-results-in-this-topic"></a>Remarque à propos des résultats de minutage dans cette rubrique.
>[AZURE.NOTE] Les résultats ne sont pas des bancs d’essai, mais sont conçus pour afficher les **performances relatives**. Les minutages sont basées sur une moyenne d’au moins 10 cycles de test. Les opérations sont les insertions dans une table vide. Ces tests ont été mesurées pre-V12, et ils ne correspondent pas nécessairement le débit des données que vous pouvez rencontrer dans une base de données V12 à l’aide de nouveaux [niveaux de service](sql-database-service-tiers.md). L’avantage relatif de la technique de traitement par lots doit être similaire.

### <a name="transactions"></a>Transactions
Il semble étrange pour commencer une révision de traitement par lot en expliquant les transactions. Mais l’utilisation de transactions côté client a un effet de traitement par lots côté serveur subtil qui améliore les performances. Et les transactions peuvent être ajoutées avec seulement quelques lignes de code, afin qu’ils fournissent un moyen rapide pour améliorer les performances des opérations séquentielles.

Observez le code C# suivant qui contient une séquence d’insertion et mise à jour des opérations sur une table simple.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

Le code ADO.NET suivant effectue dans l’ordre de ces opérations.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
    
        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

La meilleure façon d’optimiser ce code doit implémenter une certaine forme de traitement par lot du côté client de ces appels. Mais il existe un moyen simple pour augmenter les performances de ce code en encapsulant simplement la séquence d’appels dans une transaction. Voici le même code que celui qui utilise une transaction.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
        SqlTransaction transaction = conn.BeginTransaction();
    
        foreach (string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
            cmd.ExecuteNonQuery();
        }
    
        transaction.Commit();
    }

Les transactions sont réellement utilisées dans ces deux exemples. Dans le premier exemple, chaque appel individuelle est une transaction implicite. Dans le deuxième exemple, une transaction explicite encapsule tous les appels. Par la documentation de [l’écriture anticipée journal des transactions](https://msdn.microsoft.com/library/ms186259.aspx), les enregistrements du journal sont vidées sur le disque lorsque la transaction est validée. Ainsi, en incluant plusieurs appels dans une transaction, l’écriture dans le journal des transactions peut différer jusqu'à ce que la transaction est validée. En effet, vous activez le traitement par lots pour les écritures de journal de transactions du serveur.

Le tableau suivant présente certains résultats de tests ad hoc. Les tests effectués les insertions séquentielles mêmes avec et sans transactions. Pour plus de perspective, la première série de tests a exécuté à distance à partir d’un ordinateur portable sur la base de données dans Microsoft Azure. La deuxième série de tests est exécuté à partir d’un service en nuage et les que les deux résident dans le centre de données Microsoft Azure même (Ouest américaine) de la base de données. Le tableau suivant indique la durée en millisecondes d’insertions séquentielles avec et sans transactions.

**Sur site pour Azure**:

| Opérations | Aucune Transaction (ms) | Transaction (ms) |
|---|---|---|
| 1 | 130 | 402 |
| 10 | 1208 | 1226 |
| 100 | 12662 | 10395 |
| 1000 | 128852 | 102917 |


**Azure pour Azure (même centre de données)**:

| Opérations | Aucune Transaction (ms) | Transaction (ms) |
|---|---|---|
| 1 | 21 | 26 |
| 10 | 220 | 56 |
| 100 | 2145 | 341 |
| 1000 | 21479 | 2756 |

>[AZURE.NOTE] Les résultats ne sont pas des bancs d’essai. Consultez la [Remarque à propos des résultats de minutage dans cette rubrique](#note-about-timing-results-in-this-topic).

Basé sur les résultats des tests précédents, en fait une seule opération d’habillage dans une transaction diminue les performances. Mais si vous augmentez le nombre d’opérations dans une transaction unique, l’amélioration des performances est plus marquée. La différence de performances est également plus évident lorsque toutes les opérations qui se produisent dans le centre de données Microsoft Azure. Le temps de latence accrue à l’aide de la base de données SQL à l’extérieur du centre de données Microsoft Azure masque en partie le gain de performances de l’utilisation de transactions.

Bien que l’utilisation des transactions peut augmenter les performances, continuer de [respecter les meilleures pratiques pour les connexions et les transactions](https://msdn.microsoft.com/library/ms187484.aspx). Conserver la transaction aussi courte que possible et fermer la connexion de base de données une fois le travail terminé. L’à l’aide d’instruction dans l’exemple précédent permet de s’assurer que la connexion est fermée lorsque le bloc de code suivant se termine.

L’exemple précédent montre que vous pouvez ajouter une transaction locale à n’importe quel code ADO.NET avec deux lignes. Les transactions offrent un moyen rapide pour améliorer les performances de code séquentiel insérer, mettre à jour et supprimer des opérations. Toutefois, pour de meilleures performances, envisagez de modifier le code à la suite de tirer parti du traitement par lots côté client, tels que des paramètres table.

Pour plus d’informations sur les transactions dans ADO.NET, consultez [Transactions locales dans ADO.NET](https://msdn.microsoft.com/library/vstudio/2k2hy99x.aspx).

### <a name="table-valued-parameters"></a>Paramètres table
Paramètres table prend en charge les types de tables définis par l’utilisateur en tant que paramètres dans les instructions Transact-SQL, des procédures stockées et des fonctions. Cette technique de traitement par lots côté client vous permet d’envoyer plusieurs lignes de données dans le paramètre de la table. Pour utiliser des paramètres table, tout d’abord définir un type de table. L’instruction Transact-SQL suivante crée un type de tableau nommé **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
 

Dans le code, vous créez un **DataTable** avec les noms de mêmes exactes et types de type table. Passer ce **DataTable** dans un paramètre dans une requête de texte ou l’appel de procédure stockée. L’exemple suivant illustre cette technique :

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        DataTable table = new DataTable();
        // Add columns and rows. The following is a simple example.
        table.Columns.Add("mytext", typeof(string));
        table.Columns.Add("num", typeof(int));    
        for (var i = 0; i < 10; i++)
        {
            table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
        }
    
        SqlCommand cmd = new SqlCommand(
            "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
            connection);
                    
        cmd.Parameters.Add(
            new SqlParameter()
            {
                ParameterName = "@TestTvp",
                SqlDbType = SqlDbType.Structured,
                TypeName = "MyTableType",
                Value = table,
            });
    
        cmd.ExecuteNonQuery();
    }

Dans l’exemple précédent, l’objet **SqlCommand** insère des lignes à partir d’un paramètre table, **@TestTvp**. L’objet **DataTable** créé précédemment est attribuée à ce paramètre avec la méthode **SqlCommand.Parameters.Add** . Le traitement par lots les insertions en un seul appel beaucoup d’améliore les performances sur les insertions séquentielles.

Pour améliorer les approfondir l’exemple précédent, utilisez une procédure stockée au lieu d’une commande de texte. La commande Transact-SQL suivante crée une procédure stockée qui prend le paramètre table **SimpleTestTableType** .

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Puis modifiez la déclaration de l’objet **SqlCommand** dans l’exemple de code précédent à la suivante.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

Dans la plupart des cas, les paramètres table ont des performances équivalentes ou supérieures à d’autres techniques de traitement par lots. Les paramètres table sont souvent préférables, car ils sont plus flexibles que les autres options. Par exemple, les autres techniques, telles que la copie en bloc SQL, autorisent uniquement l’insertion de nouvelles lignes. Mais avec des paramètres de table, vous pouvez utiliser logique dans la procédure stockée pour déterminer quelles lignes sont mises à jour et les insère. Le type de table peut également être modifié pour contenir une colonne « Opération » qui indique si la ligne spécifiée doit être insérée, mis à jour ou supprimée.

Le tableau suivant montre les résultats des tests ad hoc pour l’utilisation de paramètres table en millisecondes.

| Opérations | Sur le site vers Azure (ms)  | Centre de données même Azure (ms) |
|---|---|---|
| 1 | 124 | 32 |
| 10 | 131 | 25 |
| 100 | 338 | 51 |
| 1000 | 2615 | 382 |
| 10000 | 23830 | 3586 |

>[AZURE.NOTE] Les résultats ne sont pas des bancs d’essai. Consultez la [Remarque à propos des résultats de minutage dans cette rubrique](#note-about-timing-results-in-this-topic).

Le gain de performance dans le traitement par lots est immédiatement évident. Dans le test précédent séquentiel, 1000 prenait 129 secondes à l’extérieur du centre de données et 21 secondes à partir de dans le centre de données. Mais avec des paramètres de table, des opérations de 1000 seulement 2,6 secondes à l’extérieur du centre de données et de 0,4 seconde dans le centre de données.

Pour plus d’informations sur les paramètres table, consultez [Paramètres de la table](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Copie en bloc SQL
Copie en bloc SQL est une autre façon d’insérer de grandes quantités de données dans une base de données cible. Les applications .NET peuvent utiliser la classe **SqlBulkCopy** pour effectuer des opérations d’insertion en bloc. **SqlBulkCopy** est similaire à l’instruction Transact-SQL **BULK INSERT**, **Bcp.exe**ou l’outil de ligne de commande. L’exemple de code suivant montre comment copier en bloc les lignes dans la source de **table de données**, table, dans la table de destination de SQL Server, MyTable.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
        {
            bulkCopy.DestinationTableName = "MyTable";
            bulkCopy.ColumnMappings.Add("mytext", "mytext");
            bulkCopy.ColumnMappings.Add("num", "num");
            bulkCopy.WriteToServer(table);
        }
    }

Il existe certains cas où il copie en bloc est préférable paramètres table. Consultez le tableau de comparaison des paramètres table par rapport à des opérations de BULK INSERT dans la [Table paramètres](https://msdn.microsoft.com/library/bb510489.aspx)de la rubrique.

Les résultats des tests ad hoc suivantes montrent les performances de traitement par lot avec **SqlBulkCopy** en millisecondes.

| Opérations | Sur le site vers Azure (ms)  | Centre de données même Azure (ms) |
|---|---|---|
| 1 | 433 | 57 |
| 10 | 441 | 32 |
| 100 | 636 | 53 |
| 1000 | 2535 | 341 |
| 10000 | 21605 | 2737 |

>[AZURE.NOTE] Les résultats ne sont pas des bancs d’essai. Consultez la [Remarque à propos des résultats de minutage dans cette rubrique](#note-about-timing-results-in-this-topic).

Dans le traitement par lots plus petits, utilisez les paramètres table surpassé la classe **SqlBulkCopy** . Toutefois, **SqlBulkCopy** effectuée plus rapidement que les paramètres table % de 12 – 31 pour les essais de lignes de 1 000 et 10 000. Comme les paramètres table, **SqlBulkCopy** est une bonne option pour les insertions en batch, en particulier par rapport à la performance des opérations de non-batch.

Pour plus d’informations sur la copie en bloc dans ADO.NET, consultez [Opérations de copie en bloc dans SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Plusieurs lignes paramétrable des instructions INSERT
Une alternative pour petits lots consiste à construire une grand paramétrée instruction INSERT qui insère plusieurs lignes. L’exemple de code suivant illustre cette technique.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
            "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";
    
        SqlCommand cmd = new SqlCommand(insertCommand, connection);
    
        for (int i = 1; i <= 10; i += 2)
        {
            cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
            cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
        }
    
        cmd.ExecuteNonQuery();
    }
 

Cet exemple est destiné à afficher le concept de base. Un scénario plus réaliste serait en boucle les entités requises pour construire la chaîne de requête et les paramètres de commande de simultanément. Vous êtes limité à un total de paramètres de requête de 2100, cela limite le nombre total de lignes qui peuvent être traitées de cette façon.

Les résultats des tests ad hoc ci-dessous indiquent les performances de ce type d’instruction insert en millisecondes.

| Opérations | Paramètres table (ms) | Seule instruction INSERT (ms) |
|---|---|---|
| 1 | 32 | 20 |
| 10 | 30 | 25 |
| 100 | 33 | 51 |

>[AZURE.NOTE] Les résultats ne sont pas des bancs d’essai. Consultez la [Remarque à propos des résultats de minutage dans cette rubrique](#note-about-timing-results-in-this-topic).

Cette approche peut être légèrement plus rapide pour les lots qui sont moins de 100 lignes. Bien que l’amélioration est petite, cette technique est une autre option qui peut fonctionner correctement dans votre scénario d’application spécifique.

### <a name="dataadapter"></a>DataAdapter
La classe **DataAdapter** vous permet de modifier un objet **DataSet** et l’envoyer les modifications comme INSERT, UPDATE et les opérations de suppression. Si vous utilisez le **DataAdapter** de cette manière, il est important de noter que les appels séparés sont effectuées pour chaque opération distincte. Pour améliorer les performances, utilisez la propriété **UpdateBatchSize** et le nombre d’opérations qui doivent être regroupés par lots à la fois. Pour plus d’informations, consultez [Exécution des opérations de lot à l’aide de DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Infrastructure d’entité
Entity Framework ne prend pas actuellement en charge le traitement par lots. Différents développeurs dans la Communauté ont tenté de présenter des solutions de contournement, par exemple de substitution de la méthode **SaveChanges** . Mais les solutions sont généralement complexes et personnalisé à l’application et le modèle de données. Le projet codeplex Entity Framework a actuellement une page discussion sur cette demande de fonctionnalité. Pour afficher cette discussion, consultez [Création des Notes de réunion – 2 août 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
Par souci d’exhaustivité, nous pensons qu’il est important de parler de XML sous la forme d’une stratégie de traitement par lots. Toutefois, l’utilisation de XML a aucun avantages par rapport aux autres méthodes et plusieurs inconvénients. L’approche est similaire aux paramètres table, mais un fichier XML ou une chaîne est passée à une procédure stockée au lieu d’une table définie par l’utilisateur. La procédure stockée traite les commandes dans la procédure stockée.

Il existe plusieurs inconvénients de cette approche :

- Utilisation de XML peut être fastidieuse et source d’erreurs.
- L’analyse XML sur la base de données peut être de consommateurs de ressources processeur.
- Dans la plupart des cas, cette méthode est plus lente que des paramètres table.

Pour ces raisons, l’utilisation de XML pour les requêtes de traitement par lots n’est pas recommandée.

## <a name="batching-considerations"></a>Considérations sur le traitement par lots
Les sections suivantes fournissent plus de conseils pour l’utilisation du traitement par lots dans les applications de base de données SQL.

### <a name="tradeoffs"></a>Compromis
En fonction de votre architecture, le traitement par lots implique un compromis entre les performances et la résilience. Par exemple, considérez le scénario où votre rôle affiche inopinément la section vers le bas. Si vous perdez une ligne de données, l’impact est plus petite que l’impact de la perte d’un grand lot de lignes non soumises. Il existe un risque plus élevé lorsque vous tampon des lignes avant de les envoyer à la base de données dans un laps de temps spécifié.

En raison de ce compromis, évaluez le type d’opérations de ce lot vous. Lot de manière plus agressive (lots plus grandes et plus longues périodes) avec des données qui sont moins critiques.

### <a name="batch-size"></a>Taille de lot
Dans nos tests, il a été généralement aucun avantage à la rupture par lots importants en segments plus petits. En fait, cette subdivision souvent a entraîné des performances plus lentes que l’envoi d’un grand lot. Par exemple, envisagez un scénario où vous souhaitez insérer les 1000 lignes. Le tableau suivant indique le temps nécessaire pour utiliser des paramètres table d’insertion de 1000 lignes lorsqu’ils sont divisés en lots plus petits.

| Taille de lot | Itérations | Paramètres table (ms) |
| -------- | --- | --- |
| 1000 | 1 | 347 |
| 500 | 2 | 355 |
| 100 | 10 | 465 |
| 50 | 20 | 630 |

>[AZURE.NOTE] Les résultats ne sont pas des bancs d’essai. Consultez la [Remarque à propos des résultats de minutage dans cette rubrique](#note-about-timing-results-in-this-topic).

Vous pouvez voir que les meilleures performances pour 1000 lignes sont de les soumettre à la fois. Dans d’autres tests (non illustrés ici) il y a un gain de performances pour un lot de 10000 lignes en deux lots de 5 000. Mais le schéma de table pour ces tests est relativement simple, vous devez effectuer des tests sur vos données spécifiques et les tailles de lots pour vérifier ces conclusions.

Un autre facteur à prendre en compte est que si le lot total devient trop volumineux, base de données de SQL peut limiter et refuser de valider le lot. Pour de meilleurs résultats, testez votre scénario spécifique afin de déterminer s’il existe une taille de lot idéal. Vérifiez la taille de lot configurable lors de l’exécution pour activer l’ajustement rapide basé sur les performances ou les erreurs.

Enfin, équilibrer la taille du lot avec les risques avec le traitement par lots. Si des erreurs transitoires ou le rôle échoue, envisagez les conséquences d’une nouvelle tentative de l’opération ou de la perte de données dans le lot.

### <a name="parallel-processing"></a>Traitement en parallèle
Que se passe-t-il si vous a l’approche de réduction de la taille de lot mais utiliser plusieurs threads pour exécuter le travail ? Encore une fois, nos tests ont montré que plusieurs lots plus petits multithreads généralement exécutées pire qu’un seul lot plus grand. Le test suivant tente d’insérer des lignes de 1000 dans un ou plusieurs lots parallèles. Ce test indique comment d’autres lots simultanées diminue réellement les performances.

| Taille de lot [itérations] | Deux threads (ms) | Quatre threads (ms) | Six threads (ms) |
| -------- | --- | --- | --- |
| 1000 [1] | 277 | 315 | 266 |
| 500 [2] | 548 | 278 | 256 |
| 250 [4] | 405 | 329 | 265 |
| 100 [10] | 488 | 439 | 391 |

>[AZURE.NOTE] Les résultats ne sont pas des bancs d’essai. Consultez la [Remarque à propos des résultats de minutage dans cette rubrique](#note-about-timing-results-in-this-topic).

Il existe plusieurs raisons possibles à la dégradation des performances en raison du parallélisme :

- Il existe plusieurs appels simultanés de réseau au lieu d’un.
- Plusieurs opérations dans une seule table peuvent entraîner de conflit et de blocage.
- Il existe des frais généraux associés le multithreading.
- Les dépenses de l’ouverture de plusieurs connexions neutralise l’avantage du traitement en parallèle.

Si vous ciblez différentes tables ou bases de données, il est possible de voir les performances de gain avec cette stratégie. Ont de la base de données ou les fédérations serait un scénario pour cette approche. Ont utilise plusieurs bases de données et les achemine des données différentes à chaque base de données. Si chaque lot de petite taille est destiné à une autre base de données, les opérations en parallèle peuvent être plus efficace. Cependant, le gain de performances n’est pas assez important pour l’utiliser comme base de décision ont de la base de données dans votre solution.

Dans certains modèles, l’exécution en parallèle de plus petits lots peut entraîner un débit amélioré des requêtes dans un système sous charge. Dans ce cas, même s’il est plus rapide de traiter un plus grand lot, traitement de plusieurs lots en parallèle peut être plus efficace.

Si vous n’utilisez pas l’exécution en parallèle, envisagez de contrôler le nombre maximal de threads de travail. Un nombre plus petit peut entraîner un temps d’exécution plus rapide et de réduire les conflits. Tenez également compte de la charge supplémentaire que cela place sur la base de données cible à la fois dans les connexions et les transactions.

### <a name="related-performance-factors"></a>Facteurs de performance associés
Orientation par défaut sur les performances de la base de données affecte également le traitement par lots. Par exemple, insérez les performances sont réduites pour les tables qui ont une grande clé primaire ou nombreux index non ordonnés en clusters.

Si les paramètres table utilisent une procédure stockée, vous pouvez utiliser la commande **SET NOCOUNT ON** au début de la procédure. Cette instruction supprime le retour du nombre de lignes affectées dans la procédure. Toutefois, au cours de nos tests, l’utilisation de **SET NOCOUNT ON** n’avait aucun effet ou diminue les performances. La procédure d’essai que stockée était simple avec un seul **Insérer** le paramètre de la table commande. Il est possible que les procédures stockées plus complexes pourraient bénéficier de cette instruction. Mais ne pensez pas que **SET NOCOUNT ON** l’ajout à votre procédure stockée automatiquement améliore les performances. Pour comprendre l’effet, tester votre procédure stockée, avec et sans l’instruction **SET NOCOUNT ON** .

## <a name="batching-scenarios"></a>Le traitement par lots des scénarios
Les sections suivantes décrivent comment utiliser des paramètres table dans trois scénarios d’application. Le premier scénario illustre comment le traitement par lots et de mise en mémoire tampon peuvent collaborer. Le deuxième scénario améliore les performances en effectuant des opérations de maître / détail dans un appel de procédure stockée unique. Le dernier scénario montre comment utiliser des paramètres table dans une opération de « UPSERT ».

### <a name="buffering"></a>Mise en mémoire tampon
Bien qu’il existe des scénarios dans lesquels sont évident candidat pour le traitement par lots, il existe de nombreux scénarios qui pourraient bénéficier de traitement par lot par le retard de traitement. Toutefois, le retard de traitement comporte également un plus grand risque que les données sont perdues en cas d’interruption inattendue. Il est important de comprendre ce risque et à prendre en compte les conséquences.

Par exemple, considérez une application web qui effectue le suivi de l’historique de navigation de chaque utilisateur. À chaque demande de page, l’application peut appeler une base de données pour enregistrer la page vue de l’utilisateur. Mais plus élevés de performances et d’évolutivité peuvent être obtenus par la mise en mémoire tampon d’activités de navigation leurs et puis envoyer ces données à la base de données dans des lots. Vous pouvez déclencher la mise à jour de la base de données en temps écoulé ou de la taille de mémoire tampon. Par exemple, une règle pourrait indiquer que le lot doit être traité après 20 secondes ou lorsque la mémoire tampon atteint 1000 éléments.

L’exemple de code suivant utilise les [Extensions réactives - Rx](https://msdn.microsoft.com/data/gg577609) pour traiter les mises en mémoire tampon des événements déclenchés par une classe de contrôle. Lorsque le tampon est plein ou un délai d’attente est atteint, le traitement par lots de données de l’utilisateur est envoyé à la base de données avec un paramètre table.

La classe NavHistoryData suivante modélise des informations de navigation de l’utilisateur. Il contient des informations de base telles que l’identificateur d’utilisateur, l’URL accédé et le temps d’accès.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

La classe NavHistoryDataMonitor est responsable de la mise en mémoire tampon des données de navigation utilisateur à la base de données. Il contient une méthode RecordUserNavigationEntry, qui répond en déclenchant un événement **OnAdded** . Le code suivant illustre la logique du constructeur qui utilise Rx pour créer une collection observable en fonction de l’événement. Puis, il souscrit à cette collection observable avec la méthode de la mémoire tampon. La surcharge spécifie que la mémoire tampon doit être envoyé toutes les 20 secondes ou 1000 entrées.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

Le gestionnaire convertit tous les éléments mis en mémoire tampon dans un type de table multi-instructions, puis transmet ce type à une procédure stockée qui traite le lot. Le code suivant illustre la définition complète pour les classes NavHistoryDataMonitor et le NavHistoryDataEventArgs.

    public class NavHistoryDataEventArgs : System.EventArgs
    {
        public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
        public NavHistoryData Data { get; set; }
    }
    
    public class NavHistoryDataMonitor
    {
        public event EventHandler<NavHistoryDataEventArgs> OnAdded;
    
        public NavHistoryDataMonitor()
        {
            var observableData =
                Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
            observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
        }
    
        public void RecordUserNavigationEntry(NavHistoryData data)
        {    
            if (OnAdded != null)
                OnAdded(this, new NavHistoryDataEventArgs(data));
        }
    
        protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
        {
            DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
            navHistoryBatch.Columns.Add("UserId", typeof(int));
            navHistoryBatch.Columns.Add("URL", typeof(string));
            navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
            foreach (EventPattern<NavHistoryDataEventArgs> item in items)
            {
                NavHistoryData data = item.EventArgs.Data;
                navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
            }
    
            using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
            {
                connection.Open();
    
                SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
                cmd.CommandType = CommandType.StoredProcedure;
    
                cmd.Parameters.Add(
                    new SqlParameter()
                    {
                        ParameterName = "@NavHistoryBatch",
                        SqlDbType = SqlDbType.Structured,
                        TypeName = "NavigationHistoryTableType",
                        Value = navHistoryBatch,
                    });
    
                cmd.ExecuteNonQuery();
            }
        }
    }

Pour utiliser cette classe de mise en mémoire tampon, l’application crée un objet NavHistoryDataMonitor statique. Chaque fois qu’un utilisateur accède à une page, l’application appelle la méthode NavHistoryDataMonitor.RecordUserNavigationEntry. La logique de mise en mémoire tampon continue à prendre en charge l’envoi de ces entrées à la base de données dans des lots.

### <a name="master-detail"></a>Maître / détail
Les paramètres table sont utiles pour les scénarios simples de INSERT. Toutefois, il peut être plus difficile pour les insertions de lot qui impliquent plus d’une table. Le scénario de « maître/détail » est un bon exemple. La table principale identifie l’entité principale. Une ou plusieurs tables de détail stockent plus de données sur l’entité. Dans ce scénario, les relations de clé étrangère appliquent la relation des détails à une entité principale unique. Envisagez une version simplifiée d’une table PurchaseOrder et sa table OrderDetail associé. Le Transact-SQL suivante crée la table PurchaseOrder avec quatre colonnes : OrderID, OrderDate, CustomerID et état.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Chaque ordre contenait une ou plusieurs des achats de produits. Ces informations sont capturées dans la table PurchaseOrderDetail. Le Transact-SQL suivante crée la table PurchaseOrderDetail avec cinq colonnes : OrderID, OrderDetailID, ProductID, UnitPrice et OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

La colonne OrderID de la table PurchaseOrderDetail doit faire référence à une commande à partir de la table PurchaseOrder. La définition suivante d’une clé étrangère applique cette contrainte.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Pour utiliser des paramètres table, vous devez disposer d’un type défini par l’utilisateur de table pour chaque table de la cible.

    CREATE TYPE PurchaseOrderTableType AS TABLE 
    ( OrderID INT,
      OrderDate DATETIME,
      CustomerID INT,
      Status NVARCHAR(50) );
    GO
    
    CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
    ( OrderID INT,
      ProductID INT,
      UnitPrice MONEY,
      OrderQty SMALLINT );
    GO

Définissez ensuite une procédure stockée qui accepte des tableaux de ces types. Cette procédure permet à une application à un ensemble de commandes et Détails commandes en un seul appel de lot localement. Le Transact-SQL suivante fournit la déclaration de la procédure stockée complète pour que cet exemple de commande d’achat.

    CREATE PROCEDURE sp_InsertOrdersBatch (
    @orders as PurchaseOrderTableType READONLY,
    @details as PurchaseOrderDetailTableType READONLY )
    AS
    SET NOCOUNT ON;
    
    -- Table that connects the order identifiers in the @orders
    -- table with the actual order identifiers in the PurchaseOrder table
    DECLARE @IdentityLink AS TABLE ( 
    SubmittedKey int, 
    ActualKey int, 
    RowNumber int identity(1,1)
    );
     
          -- Add new orders to the PurchaseOrder table, storing the actual
    -- order identifiers in the @IdentityLink table   
    INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
    OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
    SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;
    
    -- Match the passed-in order identifiers with the actual identifiers
    -- and complete the @IdentityLink table for use with inserting the details
    WITH OrderedRows As (
    SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
    FROM @orders
    )
    UPDATE @IdentityLink SET SubmittedKey = M.OrderID
    FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;
    
    -- Insert the order details into the PurchaseOrderDetail table, 
          -- using the actual order identifiers of the master table, PurchaseOrder
    INSERT INTO PurchaseOrderDetail (
    [OrderID],
    [ProductID],
    [UnitPrice],
    [OrderQty] )
    SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
    FROM @details D
    JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
    GO

Dans cet exemple, défini localement @IdentityLink table stocke les valeurs OrderID réels à partir des lignes récemment insérées. Ces identificateurs de commande sont différents des valeurs OrderID temporaires dans la @orders et @details paramètres table. Pour cette raison, le @IdentityLink table connecte ensuite les valeurs OrderID de la @orders paramètre les valeurs OrderID réel pour les nouvelles lignes dans la table PurchaseOrder. Après cette étape, le @IdentityLink table peut faciliter l’insertion des détails de la commande avec le n° de commande réel qui satisfait à la contrainte de clé étrangère.

Cette procédure stockée peut être utilisée à partir du code ou d’autres appels de Transact-SQL. Consultez la section Paramètres table de ce document pour obtenir un exemple de code. Le Transact-SQL suivante montre comment appeler le sp_InsertOrdersBatch.

    declare @orders as PurchaseOrderTableType
    declare @details as PurchaseOrderDetailTableType
    
    INSERT @orders 
    ([OrderID], [OrderDate], [CustomerID], [Status])
    VALUES(1, '1/1/2013', 1125, 'Complete'),
    (2, '1/13/2013', 348, 'Processing'),
    (3, '1/12/2013', 2504, 'Shipped')
    
    INSERT @details
    ([OrderID], [ProductID], [UnitPrice], [OrderQty])
    VALUES(1, 10, $11.50, 1),
    (1, 12, $1.58, 1),
    (2, 23, $2.57, 2),
    (3, 4, $10.00, 1)
    
    exec sp_InsertOrdersBatch @orders, @details

Cette solution permet de chaque lot à utiliser un ensemble de valeurs OrderID qui commence à 1. Ces valeurs OrderID temporaires décrivent les relations dans le lot, mais les valeurs OrderID sont déterminées au moment de l’opération d’insertion. Vous pouvez exécuté de manière répétitive les mêmes instructions dans l’exemple précédent et générer des commandes uniques dans la base de données. Pour cette raison, pensez à utiliser plus logique de base de données ou du code qui empêche les commandes en double lors de l’utilisation de cette technique de traitement par lots.

Cet exemple montre que les opérations de base de données encore plus complexes, telles que les opérations de maître / détail, peuvent être regroupés par lots à l’aide des paramètres table.

### <a name="upsert"></a>UPSERT
Un autre scénario de traitement par lots implique la mise à jour simultanée de l’insertion de nouvelles lignes et les lignes existantes. Cette opération est parfois appelée une opération « UPSERT » (mise à jour + insertion). Au lieu d’effectuer des appels séparés à insérer et mettre à jour, l’instruction MERGE est mieux adaptée pour cette tâche. L’instruction MERGE peut effectuer les deux insertion et mise à jour des opérations dans un seul appel.

Paramètres table peuvent être utilisés avec l’instruction MERGE pour effectuer des insertions et des mises à jour. Par exemple, considérez une table Employee simplifiée qui contient les colonnes suivantes : EmployeeID, FirstName, LastName, SocialSecurityNumber :

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))
 
Dans cet exemple, vous pouvez utiliser le fait que le SocialSecurityNumber est unique pour effectuer une fusion de plusieurs employés. Commencez par créer le type de table définie par l’utilisateur :

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

Ensuite, créez une procédure stockée ou écrire du code qui utilise l’instruction de fusion pour effectuer la mise à jour et d’insertion. L’exemple suivant utilise l’instruction MERGE sur un paramètre table, @employees, de type EmployeeTableType. Le contenu de la @employees table ne sont pas indiqués ici.

    MERGE Employee AS target
    USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
    AS source ([FirstName], [LastName], [SocialSecurityNumber])
    ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
    WHEN MATCHED THEN 
    UPDATE SET
    target.FirstName = source.FirstName, 
    target.LastName = source.LastName
    WHEN NOT MATCHED THEN
       INSERT ([FirstName], [LastName], [SocialSecurityNumber])
       VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);

Pour plus d’informations, consultez la documentation et des exemples de l’instruction MERGE. Bien que le travail même pu être effectué en plusieurs étapes stockées appel de procédure à séparer les insérer et les opérations de mise à jour, l’instruction MERGE est plus efficace. Code de base de données peut également construire des appels de Transact-SQL qui utilisent l’instruction MERGE directement sans nécessiter deux appels de base de données pour INSERT et UPDATE.

## <a name="recommendation-summary"></a>Résumé de la recommandation

La liste suivante fournit un résumé des recommandations présentées dans cette rubrique le traitement par lots :

- Utilisation de la mémoire tampon et le traitement par lots pour améliorer les performances et l’évolutivité des applications de base de données de SQL.
- Comprendre les compromis entre le traitement par lots/mise en mémoire tampon et de résilience. Lors d’un échec de rôle, le risque de perte d’un lot non traité des données critiques de l’entreprise peut compenser les avantages des performances de traitement par lot.
- Essayez de conserver tous les appels à la base de données au sein d’un centre de données unique pour réduire la latence.
- Si vous choisissez une technique le traitement par lots, les paramètres table offrent les meilleures performances et flexibilité.
- Pour de meilleures performances d’insertion, suivez ces indications générales, mais votre scénario de test :
    - Pour < 100 lignes, utilisez une seule commande INSERT paramétrée.
    - Pour < 1000 lignes, utilisez les paramètres table.
    - Pour > = 1000 lignes, utilisez SqlBulkCopy.
- Pour mettre à jour et supprimer des opérations, utiliser des paramètres table avec la logique de la procédure stockée qui détermine le bon fonctionnement de chaque ligne dans le paramètre table.
- Instructions de taille de lot :
    - Utilisez la plus grande taille des lots ayant un sens pour votre application et les besoins de l’entreprise.
    - Équilibrer le gain de performances de lots volumineux avec les risques de défaillances temporaires ou catastrophiques. Quelle est la conséquence de tentatives ou de perte des données du lot ? 
    - Test de la plus grande taille de lot pour vérifier que base de données de SQL ne rejette pas il.
    - Créer des paramètres de configuration qui contrôle le traitement par lots, tels que la taille de lot ou de la période de mise en mémoire tampon. Ces paramètres garantissent la flexibilité. Vous pouvez modifier le comportement de traitement de production sans redéployer le service en nuage.
- Éviter l’exécution en parallèle des lots qui fonctionnent sur une seule table dans une base de données. Si vous choisissez de diviser un lot unique sur plusieurs threads de production, exécuter des tests pour déterminer le nombre idéal de threads. Après un seuil spécifié, plus de threads seront diminuer les performances au lieu de l’augmenter.
- Envisagez la mise en mémoire tampon de taille et de temps comme un moyen d’implémenter le traitement par lots pour d’autres scénarios.

## <a name="next-steps"></a>Étapes suivantes

Cet article se concentre sur la conception de la base de données et liés pour le traitement par lots des techniques de codage peuvent améliorer vos performances de l’application et de l’évolutivité. Mais ce n’est qu’un facteur dans votre stratégie globale. D’autres façons d’améliorer les performances et l’évolutivité, consultez le [Guide de performances de base de données de SQL Azure des bases de données unique](sql-database-performance-guidance.md) et de [Considérations de prix et de performances pour un pool d’élasticité de la base de données](sql-database-elastic-pool-guidance.md).
