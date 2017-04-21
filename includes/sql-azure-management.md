
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Gestion de base de données SQL Azure à l’aide de SQL Server Management Studio 

Vous pouvez utiliser SQL Server Management Studio (SSMS) pour administrer les bases de données et des serveurs logiques de la base de données de SQL Azure. Cette rubrique vous guide tout au long des tâches courantes avec SSMS. Vous disposez déjà d’un serveur logique et une base de données créée dans la base de données de SQL Azure, avant de commencer. Pour commencer, lisez l’article [créer votre première base de données de SQL Azure](sql-database-get-started.md) et puis revenir.

Il est recommandé d’utiliser la dernière version de SSMS chaque fois que vous travaillez avec une base de données de SQL Azure. Visitez [Télécharger SQL Server Management Studio](https://msdn.microsoft.com/en-us/library/mt238290.aspx) pour l’obtenir. 


## <a name="connect-to-a-sql-database-logical-server"></a>Se connecter à un serveur logique de la base de données SQL

Connexion à la base de données de SQL exige de connaître le nom du serveur sur Azure. Vous devrez peut-être vous connecter au portail pour obtenir ces informations.

1.  Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com).

2.  Dans le volet gauche, cliquez sur **Bases de données SQL**.

3.  Dans la page d’accueil de bases de données SQL, cliquez sur **serveurs** en haut de la page pour répertorier tous les serveurs associés à votre abonnement. Recherchez le nom du serveur auquel vous souhaitez vous connecter et copiez-le dans le Presse-papiers.

    Ensuite, configurez votre pare-feu de base de données SQL pour autoriser les connexions à partir de votre ordinateur local. Pour cela, en ajoutant l’adresse IP de machine locale à la liste des exceptions du pare-feu.

1.  Sur la page d’accueil de bases de données SQL, cliquez sur **serveurs** et puis cliquez sur le serveur auquel vous souhaitez vous connecter.

2.  Cliquez sur **configurer** dans la partie supérieure de la page.

3.  Copier l’adresse IP dans adresse IP du CLIENT en cours.

4.  Dans la page Configurer, **Des adresses IP autorisées** comprend trois zones où vous pouvez spécifier un nom de règle et d’une plage d’adresses IP en tant que valeurs initiales et finales. Pour un nom de règle, vous pouvez entrer le nom de votre ordinateur. Pour la plage de début et de fin, coller de l’adresse IP de votre ordinateur dans les deux zones, puis cliquez sur la case à cocher qui s’affiche.

    Le nom de la règle doit être unique. S’il s’agit de votre ordinateur de développement, vous pouvez entrer l’adresse IP dans la zone début de plage IP et dans la zone fin de plage IP. Dans le cas contraire, vous devrez entrer une plus large plage d’adresses IP pour prendre en charge les connexions à partir d’autres ordinateurs de votre organisation.
 
5. Cliquez sur **Enregistrer** en bas de la page.

    **Remarque :** Il peut y avoir des comme délai de cinq minutes pour que les modifications aux paramètres du pare-feu prennent effet.

    Vous êtes maintenant prêt à vous connecter à la base de données SQL à l’aide de Management Studio.

1.  Dans la barre des tâches, cliquez sur **Démarrer**, pointez sur **Tous les programmes**, pointez sur **Microsoft SQL Server 2014**, puis cliquez sur **SQL Server Management Studio**.

2.  Dans **connexion à un serveur**, spécifiez le nom de serveur complet comme *nom_serveur*. database.windows.net. Sur Azure, le nom du serveur est une chaîne générée automatiquement composée de caractères alphanumériques.

3.  Sélectionnez **l’authentification SQL Server**.

4.  Dans la zone de **connexion** , entrez la connexion de l’administrateur de SQL Server que vous avez spécifié dans le portail lorsque vous avez créé votre serveur.

5.  Dans la zone **mot de passe** , entrez le mot de passe que vous avez spécifié dans le portail lorsque vous avez créé votre serveur.

8.  Cliquez sur **connexion** pour établir la connexion.

SQL Server 2014 SSMS avec les dernières mises à jour propose une prise en charge étendue pour des tâches telles que la création et la modification des bases de données SQL d’Azure. En outre, vous pouvez également utiliser les instructions Transact-SQL pour accomplir ces tâches. Les étapes suivantes présentent des exemples de ces instructions. Pour plus d’informations sur l’utilisation de Transact-SQL avec une base de données SQL, y compris les détails sur les commandes sont prises en charge, consultez [Référence de Transact-SQL (SQL de base de données)](http://msdn.microsoft.com/library/bb510741.aspx).

## <a name="create-and-manage-azure-sql-databases"></a>Créer et gérer des bases de données SQL d’Azure

Lorsque vous êtes connecté à la base de données **master** , vous pouvez créer de nouvelles bases de données sur le serveur et modifier ou supprimer des bases de données existantes. Les étapes ci-dessous décrivent comment effectuer plusieurs tâches courantes de gestion de base de données par le biais de Management Studio. Pour effectuer ces tâches, assurez-vous que vous êtes connecté à la base de données **master** avec le nom d’accès principal au niveau du serveur que vous avez créé lorsque vous configurez votre serveur.

Pour ouvrir une fenêtre de requête dans Management Studio, ouvrez le dossier bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur le **maître**, puis cliquez sur **Nouvelle requête**.

-   Utilisez l’instruction **CREATE DATABASE** pour créer une nouvelle base de données. Pour plus d’informations, consultez [Créer base de données (SQL)](https://msdn.microsoft.com/library/dn268335.aspx). L’instruction ci-dessous crée une nouvelle base de données nommée **myTestDB** et spécifie qu’il s’agit d’une base de données Standard Edition S0 avec une taille maximale de la valeur par défaut de 250 Go.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Cliquez sur **exécuter** pour exécuter la requête.

-   Utilisez l’instruction **ALTER DATABASE** pour modifier une base de données existante, par exemple, si vous souhaitez modifier le nom et la version de la base de données. Pour plus d’informations, voir [ALTER DATABASE (base de données de SQL)](https://msdn.microsoft.com/library/ms174269.aspx). L’instruction ci-dessous modifie la base de données que vous avez créé à l’étape précédente pour modifier S1 Standard edition.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Utilisez **L’instruction DROP DATABASE** instruction pour supprimer une base de données existante.
    Pour plus d’informations, consultez [DROP DATABASE (base de données de SQL)](https://msdn.microsoft.com/library/ms178613.aspx). L’instruction ci-dessous supprime la base de données **myTestDB** , mais ne pas le supprimer maintenant puisque vous allez utiliser il crée des connexions d’accès à l’étape suivante.

        DROP DATABASE myTestBase;

-   La base de données master a la vue **sys.databases** que vous pouvez utiliser pour afficher des détails sur toutes les bases de données. Pour afficher toutes les bases de données, exécutez l’instruction suivante :

        SELECT * FROM sys.databases;

-   Dans la base de données de SQL, l’instruction **USE** n’est pas pris en charge pour basculer entre les bases de données. Au lieu de cela, vous devez établir une connexion directe à la base de données cible.

>[AZURE.NOTE] La plupart des instructions Transact-SQL qui créent ou modifient une base de données doivent être exécutées au sein de leur propre et ne peuvent pas être regroupées avec d’autres instructions Transact-SQL. Pour plus d’informations, consultez les informations de l’instruction spécifiques disponibles dans les liens ci-dessus.

## <a name="create-and-manage-logins"></a>Créer et gérer des connexions

La base de données **maître** assure le suivi des connexions d’accès et les noms de connexion sont autorisé à créer des bases de données ou d’autres connexions. Gestion des accès en se connectant à la base de données **master** avec le nom d’accès principal au niveau du serveur que vous avez créé lorsque vous configurez votre serveur. Vous pouvez utiliser les instructions **CREATE LOGIN**, **ALTER LOGIN**ou **DROP LOGIN** pour exécuter des requêtes sur la base de données master qui va gérer les connexions d’accès à l’ensemble du serveur. Pour plus d’informations, voir [Gérer les bases de données et les connexions de base de données de SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx). 


-   Pour créer une nouvelle connexion au niveau du serveur, utilisez l’instruction **CREATE LOGIN** . Pour plus d’informations, voir [CREATE LOGIN (de base de données SQL)](https://msdn.microsoft.com/library/ms189751.aspx). L’instruction ci-dessous crée une nouvelle connexion appelée **login1**. Remplacez le **mot de passe1** avec le mot de passe de votre choix.

        CREATE LOGIN login1 WITH password='password1';

-   Pour accorder des autorisations au niveau de la base de données, utilisez l’instruction **CREATE USER** . Toutes les connexions doivent être créées dans la base de données **master** , mais pour une connexion d’accès pour vous connecter à une base de données différent, vous devez lui accorder des autorisations au niveau de la base de données à l’aide de l’instruction **CREATE USER** sur cette base de données. Pour plus d’informations, consultez [Créer un utilisateur (de base de données SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 

-   Pour accorder des autorisations de Connexion1 à une base de données appelée **myTestDB**, procédez comme suit :

 1.  Pour actualiser l’Explorateur d’objets pour afficher la base de données **myTestDB** que vous venez de créer, cliquez sur le nom du serveur dans l’Explorateur d’objets et puis cliquez sur **Actualiser**.  

     Si vous avez fermé la connexion, vous pouvez vous reconnecter en sélectionnant **Explorateur d’objets connexion** dans le menu fichier.

 2. Droit **myTestDB** de base de données et sélectionnez **Nouvelle requête**.

    3.  Exécutez l’instruction suivante sur la base de données myTestDB pour créer un utilisateur de base de données nommé **login1User** qui correspond à la connexion d’accès au niveau du serveur **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Utilisez le **sp\_addrolemember** une procédure stockée à attribuer le compte d’utilisateur le niveau approprié d’autorisations sur la base de données. Pour plus d’informations, consultez [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). L’instruction ci-dessous donne des autorisations en lecture seule à la base de données **login1User** en ajoutant **login1User** à le **db\_datareader** rôle.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Pour modifier une connexion existante, par exemple, si vous souhaitez modifier le mot de passe pour la connexion, utilisez l’instruction **ALTER LOGIN** . Pour plus d’informations, voir [ALTER LOGIN (de base de données SQL)](https://msdn.microsoft.com/library/ms189828.aspx). L’instruction **ALTER LOGIN** doit être exécutée sur la base de données **master** . Revenez à la fenêtre de requête qui est connectée à cette base de données. 

    L’instruction ci-dessous modifie la connexion **login1** pour réinitialiser le mot de passe.
    Remplacez **newPassword** avec le mot de passe de votre choix et **oldPassword** avec le mot de passe pour la connexion.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Utilisez l’instruction **DROP le LOGIN** pour supprimer une connexion existante.
    Suppression d’une connexion d’accès au niveau du serveur supprime également les comptes d’utilisateur de base de données associée. Pour plus d’informations, consultez [DROP DATABASE (base de données de SQL)](https://msdn.microsoft.com/library/ms178613.aspx). L’instruction **DROP LOGIN** doit être exécutée sur la base de données **master** . L’instruction ci-dessous supprime la connexion **login1** .

        DROP LOGIN login1;

-   La base de données master a la **sys.sql\_connexion** afficher que vous pouvez utiliser pour afficher les connexions. Pour afficher toutes les connexions d’accès existantes, exécutez l’instruction suivante :

        SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-viewsh2"></a>Surveiller l’utilisation des vues de gestion dynamique de la base de données SQL</h2>

Base de données de SQL prend en charge plusieurs vues de gestion dynamiques que vous pouvez utiliser pour surveiller une base de données individuel. Voici quelques exemples du type de données que vous pouvez récupérer par le biais de ces affichages de l’analyseur. Pour plus de détails et plus d’exemples d’utilisation, consultez [analyse de la base de données SQL à l’aide de vues de gestion dynamique](https://msdn.microsoft.com/library/azure/ff394114.aspx).

-   Interrogation d’une vue de gestion dynamique requiert des autorisations de **Base de données de l’état d’affichage** . Pour accorder l’autorisation de **l’État d’affichage de base de données** à un utilisateur de base de données spécifique, se connecter à la base de données que vous souhaitez gérer à l’aide de votre nom d’utilisateur au niveau du serveur principe et exécutez l’instruction suivante sur la base de données :

        GRANT VIEW DATABASE STATE TO login1User;

-   Calculer la taille de la base de données à l’aide du **sys.dm\_db\_partition\_statistiques** affichage. La **sys.dm\_db\_partition\_statistiques** affichage retourne des informations de page et le nombre de lignes pour chaque partition dans la base de données, que vous pouvez utiliser pour calculer la taille de la base de données. La requête suivante renvoie la taille de votre base de données en Mo :

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Utilisez la **sys.dm\_exec\_connexions** et **sys.dm\_exec\_sessions** vues pour récupérer les informations sur les connexions utilisateur actuelles et tâches internes associés à la base de données. La requête suivante renvoie des informations sur la connexion en cours :

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Utilisez la **sys.dm\_exec\_requête\_statistiques** qui permet de récupérer les statistiques de performances agrégées pour mise en cache des plans de requête. La requête suivante renvoie des informations sur les cinq premières requêtes les classés par temps processeur moyen.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;
