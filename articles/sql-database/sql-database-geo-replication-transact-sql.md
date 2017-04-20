<properties
    pageTitle="Configurer la géo-réplication de base de données SQL Azure avec Transact-SQL | Microsoft Azure"
    description="Configurer la réplication géographique pour SQL Azure de base de données à l’aide de Transact-SQL"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="NA"
    ms.date="10/13/2016"
    ms.author="carlrab"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configurer la géo-réplication de base de données SQL Azure avec Transact-SQL

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-geo-replication-overview.md)
- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Cet article vous indique comment configurer géo-réplication Active pour une base de données de SQL Azure avec Transact-SQL.

Pour déclencher le basculement à l’aide de Transact-SQL, reportez-vous à [initier un basculement planifié ou de base de données de SQL Azure avec Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

>[AZURE.NOTE] Geo-réplication Active (lisibles secondaires) est maintenant disponible pour toutes les bases de données de tous les niveaux de service. En avril 2017 est déclassé le type secondaire non lisible et bases de données non lisible passeront automatiquement à secondaires lisibles.

Pour configurer la réplication-Geo Active à l’aide de Transact-SQL, vous devez les éléments suivants :

- Un abonnement Azure.
- Un serveur de base de données de SQL Azure logique <MyLocalServer> et une base de données SQL <MyDB> -la base de données principale que vous souhaitez répliquer.
- Une ou plusieurs base de données de SQL Azure serveurs logiques < MySecondaryServer(n) > - les serveurs logiques qui seront les serveurs partenaire dans laquelle vous allez créer des bases de données secondaires.
- Une connexion d’accès DBManager sur le serveur primaire, ont db_ownership de la base de données locale que vous serez géo-réplication, et être DBManager sur l’ou les serveurs partenaire auquel vous allez configurer la réplication-Geo.
- SQL Server Management Studio (SSMS)

> [AZURE.IMPORTANT] Il est recommandé de toujours utiliser la dernière version du Management Studio reste synchronisé avec les mises à jour Microsoft Azure et de base de données SQL. [Mise à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="add-secondary-database"></a>Ajouter une base de données secondaire

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour créer une base de données secondaire géo-répliquées sur le serveur d’un partenaire. Vous exécutez cette instruction sur la base de données master du serveur contenant la base de données à répliquer. La base de données répliquée geo (la « base de données primaire ») aura le même nom que la base de données en cours de réplication et aura, par défaut, le même niveau de service que la base de données principale. La base de données secondaire peut être lisible ou illisible et peut être une base de données unique ou une databbase élastique. Pour plus d’informations, voir [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et les [Niveaux de Service](sql-database-service-tiers.md).
Une fois la base de données secondaire est créée et amorcé, données commenceront à répliquer de façon asynchrone à partir de la base de données principale. Les étapes ci-dessous expliquent comment configurer la réplication de la zone géographique à l’aide de Management Studio. Étapes de création secondaires non lisible et lisibles, avec une seule base de données ou une base de données élastique, sont fournis.

> [AZURE.NOTE] Si une base de données existe sur le serveur partenaire avec le même nom que la base de données principale, que la commande échouera.


### <a name="add-non-readable-secondary-single-database"></a>Ajouter secondaire non lisible (base de données unique)

Procédez comme suit pour créer un secondaire non lisible sous la forme d’une seule base de données.

1. À l’aide de la version 13.0.600.65 ou une version ultérieure de SQL Server Management Studio.

     > [AZURE.IMPORTANT] Télécharger la version la [plus récente](https://msdn.microsoft.com/library/mt238290.aspx) de SQL Server Management Studio. Il est recommandé de toujours utiliser la dernière version du Management Studio reste synchronisé avec les mises à jour sur le portail Azure.


2. Ouvrez le dossier bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur le **masque**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour rendre une base de données local dans une réplication de Geo primaire avec une base de données secondaire non lisible sur MySecondaryServer1 où MySecondaryServer1 est le nom de votre serveur convivial.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Cliquez sur **exécuter** pour exécuter la requête.


### <a name="add-readable-secondary-single-database"></a>Ajouter lisible secondaire (base de données unique)
Procédez comme suit pour créer un secondaire lisible sous la forme d’une seule base de données.

1. Dans Management Studio, connectez-vous à votre serveur de base de données de SQL Azure logique.

2. Ouvrez le dossier bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur le **masque**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour rendre une base de données local dans une réplication de Geo primaire avec une base de données secondaire lisible sur un serveur secondaire.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Cliquez sur **exécuter** pour exécuter la requête.



### <a name="add-non-readable-secondary-elastic-database"></a>Ajouter secondaire non lisible (élastique de base de données)

Procédez comme suit pour créer un secondaire non lisible sous la forme d’une base de données élastique.

1. Dans Management Studio, connectez-vous à votre serveur de base de données de SQL Azure logique.

2. Ouvrez le dossier bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur le **masque**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour rendre une base de données local dans une réplication de zone géographique primaire avec une base de données secondaire non lisible sur un serveur secondaire dans un pool élastique.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Cliquez sur **exécuter** pour exécuter la requête.



### <a name="add-readable-secondary-elastic-database"></a>Ajouter lisible secondaire (élastique de base de données)
Utilisez les étapes suivantes pour créer des secondaire lisible sous la forme d’une base de données élastique.

1. Dans Management Studio, connectez-vous à votre serveur de base de données de SQL Azure logique.

2. Ouvrez le dossier bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur le **masque**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour rendre une base de données local dans une réplication de zone géographique primaire avec une base de données secondaire lisible sur un serveur secondaire dans un pool élastique.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Cliquez sur **exécuter** pour exécuter la requête.



## <a name="remove-secondary-database"></a>Supprimer la base de données secondaire

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour définitivement mettre fin au partenariat de réplication entre une base de données secondaire et principal. Cette instruction est exécutée sur la base de données master sur lequel réside la base de données principale. Après la cessation de la relation, la base de données secondaire devient une base de données en lecture-écriture. Si la connectivité à la base de données secondaire est interrompue la commande a réussi, mais l’image secondaire devient en lecture-écriture lorsque la connectivité est restaurée. Pour plus d’informations, voir [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et les [Niveaux de Service](sql-database-service-tiers.md).

Utilisez les étapes suivantes pour supprimer des secondaire de réplication géographique d’un partenariat de réplication géographique.

1. Dans Management Studio, connectez-vous à votre serveur de base de données de SQL Azure logique.

2. Ouvrez le dossier bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur le **masque**, puis cliquez sur **Nouvelle requête**.

3. L’instruction **ALTER DATABASE** suivante permet de supprimer le secondaire géo-répliquées.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Cliquez sur **exécuter** pour exécuter la requête.

## <a name="monitor-geo-replication-configuration-and-health"></a>Surveiller la santé et la configuration de la réplication de la zone géographique

Tâches de surveillance comprennent la surveillance de la configuration de réplication géographique et la surveillance de l’état de la réplication de données.  Vous pouvez utiliser la vue de gestion dynamique **sys.dm_geo_replication_links** dans la base de données master pour renvoyer des informations sur tous les liens de réplication de sortie pour chaque base de données sur le serveur logique de base de données de SQL Azure. Cette vue contient une ligne pour chacun de la liaison de réplication entre les bases de données principales et secondaires. Vous pouvez utiliser la vue de gestion dynamique **sys.dm_replication_link_status** pour renvoyer une ligne pour chaque base de données SQL Azure est actuellement engagées dans un lien de réplication de réplication. Cela inclut les bases de données principales et secondaires. S’il existe plus d’un lien de réplication continue pour une base de données principale donnée, cette table contient une ligne pour chacune des relations. L’affichage est créé dans toutes les bases de données, y compris le maître logique. Toutefois, l’interrogation de cet affichage dans le masque de logique retourne un ensemble vide. Vous pouvez utiliser la vue de gestion dynamique **sys.dm_operation_status** pour afficher l’état de toutes les opérations de base de données, y compris de l’état des liens de réplication. Pour plus d’informations, consultez [sys.geo_replication_links (de base de données SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (de base de données SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx)et [sys.dm_operation_status (de base de données SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Procédez comme suit pour surveiller un partenariat de réplication géographique.

1. Dans Management Studio, connectez-vous à votre serveur de base de données de SQL Azure logique.

2. Ouvrez le dossier bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur le **masque**, puis cliquez sur **Nouvelle requête**.

3. L’instruction suivante permet d’afficher toutes les bases de données avec des liens de réplication géographique.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Cliquez sur **exécuter** pour exécuter la requête.
5. Ouvrez le dossier bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur **MyDB**, puis cliquez sur **Nouvelle requête**.
6. L’instruction suivante permet d’afficher les retards de réplication et de la dernière réplication de mes bases de données de MyDB secondaires.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Cliquez sur **exécuter** pour exécuter la requête.
8. L’instruction suivante permet d’afficher les opérations de réplication géographique plus récentes associées à la base de données MyDB.

        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC

9. Cliquez sur **exécuter** pour exécuter la requête.

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>Mise à niveau secondaire non lisible à lire

En avril 2017 est déclassé le type secondaire non lisible et bases de données non lisible passeront automatiquement à secondaires lisibles. Si vous utilisez secondaires non lisible aujourd'hui et que vous souhaitez mettre à niveau pour être lisible, vous pouvez utiliser les étapes suivantes pour chaque secondaire.

> [AZURE.IMPORTANT] Il n’y a pas de méthode libre-service de place la mise à niveau secondaire non lisible à lire. Si vous supprimez votre uniquement secondaire, la base de données principale restera non protégé jusqu'à ce que la nouvelle secondaire est entièrement synchronisée. Si les SLA de votre application exige que le serveur principal est toujours protégé, vous devez envisager la création parallèle secondaire dans un autre serveur avant d’appliquer la procédure de mise à niveau ci-dessus. Remarque chaque principal peut contenir jusqu'à 4 bases de données secondaires.


1. Tout d’abord, connectez-vous au serveur *secondaire* et supprimer la base de données secondaire non lisible :  
        
        DROP DATABASE <MyNonReadableSecondaryDB>;

2. Maintenant vous connecter au serveur *primaire* et ajouter un nouvelle secondaire lisible

        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);




## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur géo-réplication Active, consultez - [Géo-réplication](sql-database-geo-replication-overview.md) Active
- Pour une vue d’ensemble de la continuité de l’activité commerciale et scénarios, consultez [vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
