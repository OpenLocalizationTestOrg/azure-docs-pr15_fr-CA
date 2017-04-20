<properties 
    pageTitle="À l’aide de la bibliothèque de client de base de données élastique avec Dapper | Microsoft Azure" 
    description="À l’aide de bibliothèque client de base de données élastique avec Dapper." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="using-elastic-database-client-library-with-dapper"></a>À l’aide de la bibliothèque de client de base de données élastique avec Dapper 

Ce document est destiné aux développeurs qui s’appuient sur Dapper pour créer des applications, mais également d’adopter les [Outils de base de données élastique](sql-database-elastic-scale-introduction.md) pour créer des applications qui implémentent ont pour horizontale de leur couche de données.  Ce document illustre les modifications dans les applications basées sur les Dapper qui sont nécessaires pour l’intégration avec les outils de base de données élastique. Nous nous concentrons sur la composition de la gestion d’éclater élastique de la base de données et dépendante des données distribue avec Dapper. 

**Exemple de Code**: [Outils élastique de la base de données pour la base de données SQL Azure - intégration Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).
 
Il est facile d’intégrer des **Dapper** et **DapperExtensions** avec la bibliothèque de client élastique de la base de données pour la base de données de SQL Azure. Vos applications peuvent utiliser des données dépendantes de routage par la modification de la création et l’ouverture de nouveaux objets [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) d’utiliser l’appel de [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) à partir de la [bibliothèque cliente](http://msdn.microsoft.com/library/azure/dn765902.aspx). Cela limite les modifications dans votre application pour seulement lorsque de nouvelles connexions sont créées et ouvert. 

## <a name="dapper-overview"></a>Vue d’ensemble dapper
**Dapper** est un mappeur relationnel objet. Il mappe les objets .NET à partir de votre application à une base de données relationnelle (et inversement). La première partie de l’exemple de code illustre comment vous pouvez intégrer la bibliothèque cliente élastique de la base de données avec des applications Dapper. La deuxième partie de l’exemple de code illustre comment intégrer lors de l’utilisation à la fois Dapper et DapperExtensions.  

La fonctionnalité de mappeur de Dapper fournit des méthodes d’extension sur les connexions de base de données qui simplifient l’envoi instructions T-SQL pour l’exécution ou l’interrogation de la base de données. Par exemple, Dapper facilite une correspondance entre vos objets .NET et les paramètres des instructions SQL pour **exécuter** les appels, ou pour utiliser les résultats de vos requêtes SQL dans des objets .NET à l’aide d’appels de **requête** à partir de Dapper. 

Lorsque vous utilisez DapperExtensions, vous n’avez plus besoin de fournir les instructions SQL. Les méthodes d’extensions telles que **GetList** ou **Insérer** via la connexion de base de données de créer les instructions SQL en arrière-plan.
 
Un autre avantage de Dapper et de DapperExtensions est que l’application contrôle la création de la connexion de base de données. Cela vous permet d’interagir avec la bibliothèque élastique de la base de données client en fonction du mappage de shardlets pour les bases de données des connexions de la base de données des courtiers.

Pour obtenir les assemblys Dapper, voir [point Dapper net](http://www.nuget.org/packages/Dapper/). Pour les extensions Dapper, consultez [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Un bref aperçu de la bibliothèque de client de base de données élastique

Avec la bibliothèque de client de base de données élastique, vous définissez des partitions de vos données d’application appelées *shardlets* les mappez aux bases de données et les identifiez par *des clés ont*. Vous pouvez avoir autant de bases de données que vous avez besoin et distribuez votre shardlets dans ces bases de données. Le mappage de valeurs de clé ont les bases de données est stocké par un mappage partagé fourni par les API de la bibliothèque. Cette fonctionnalité est appelée **Éclater mapper la gestion**. Le mappage partagé sert également le service Broker pour les connexions de base de données pour les requêtes comportant une clé ont. Cette fonctionnalité est appelée **routage dépendant des données**.

![Mappages d’éclater et le routage dépendant des données][1]

Le Gestionnaire de mappage partagé protège les utilisateurs à partir de vues incohérentes dans les données de shardlet qui peuvent se produire lors des opérations de gestion shardlet simultanés sont produisent sur les bases de données. Pour ce faire, les mappages d’éclater broker pour une application générée avec la bibliothèque de connexions de base de données. Lorsque les opérations de gestion partagé a un impact sur le shardlet, ainsi, la fonctionnalité de mappage partagé pour automatiquement mettre fin à une connexion de base de données. 

Au lieu d’utiliser la façon traditionnelle de créer des connexions pour Dapper, nous devons utiliser la [méthode de OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn824099.aspx). Cela garantit que toute la validation a lieu et les connexions sont gérées correctement lorsque toutes les données se déplacent entre le milieu des fragments.

### <a name="requirements-for-dapper-integration"></a>Configuration requise pour l’intégration Dapper

Lorsque vous travaillez avec la bibliothèque de client élastique de la base de données et les API Dapper, nous souhaitons conserver les propriétés suivantes :

* **Évolution**: nous souhaitons ajouter ou supprimer des bases de données de la couche de données de l’application sharded que nécessaire pour les demandes de capacité de l’application. 

-    **Cohérence**: étant donné que notre application est mise à l’échelle à l’aide d’ont, nous devons effectuer le routage dépendant des données. Nous souhaitons utiliser les fonctionnalités de la bibliothèque de routage données dépendantes au pour faire. En particulier, nous souhaitons conserver la validation et la cohérence des garanties fournies par les connexions qui sont demandées par le biais du Gestionnaire de table partagé afin d’éviter la corruption ou les résultats de la requête incorrect. Cela garantit que les connexions à un shardlet donné sont rejetées ou arrêtées si (par exemple) de la shardlet est actuellement déplacé à un autre partagé à l’aide des API de fractionnement et de fusion.

-    **Mappage d’objet**: nous souhaitons conserver la commodité des mappages fournis par Dapper à traduire entre les classes dans l’application et les structures de base de données sous-jacente. 

La section suivante fournit des conseils pour satisfaire à ces exigences pour les applications basées sur **Dapper** et **DapperExtensions**.

## <a name="technical-guidance"></a>Conseils techniques
### <a name="data-dependent-routing-with-dapper"></a>Données dépendantes de routage avec Dapper 

Avec Dapper, l’application est généralement responsable de la création et l’ouverture des connexions à la base de données sous-jacente. Étant donné un type T par l’application, Dapper renvoie les résultats de la requête que les collections .NET de type T. Dapper effectue le mappage à partir des lignes de résultat de T-SQL pour les objets de type T. De même, Dapper mappe les objets .NET dans les valeurs SQL ou de paramètres pour les instructions de langage (DML) de manipulation de données. Dapper offre cette fonctionnalité via les méthodes d’extension sur l’objet [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) régulière dans les bibliothèques ADO .NET SQL Client. La connexion SQL retournée par l’API d’échelle élastique de DDR sont également des objets [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) régulières. Ceci nous permet d’utiliser directement les extensions Dapper sur le type retourné par l’API de DDR de la bibliothèque client, qu’il s’agit également d’une simple connexion SQL Client.

Ces observations rendent facile à utiliser de connexions demandées par la bibliothèque de client de base de données élastique pour Dapper.

Cet exemple de code (à partir de l’exemple fourni) illustre l’approche où la clé ont est fournie par l’application à la bibliothèque à négocier la connexion vers la droite partagé.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

L’appel à l’API [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) remplace la création de valeur par défaut et l’ouverture d’une connexion SQL Client. L’appel de [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) prend les arguments qui sont requis pour le routage dépendant des données : 

-    Le mappage partagé pour accéder aux interfaces de routage dépendant données
-    La clé ont pour identifier le shardlet
-    Les informations d’identification (nom d’utilisateur et mot de passe) pour vous connecter à l’éclater.

L’objet de mappage partagé crée une connexion à la partagé qui contient le shardlet de la clé ont donné. Le client de base de données élastique API balise également la connexion pour implémenter sa garantie de la cohérence. Dans la mesure où l’appel à [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) renvoie un objet de connexion SQL Client normal, l’appel suivant à la méthode **Execute** d’extension à partir de Dapper suit la pratique Dapper standard.

Les requêtes fonctionnent très bien de la même façon : vous ouvrez d’abord la connexion à l’aide de [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) à partir de l’API cliente. Puis, vous utilisez les méthodes d’extension de Dapper régulières pour mapper les résultats de votre requête SQL dans des objets .NET :

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Notez que **à l’aide de** bloquer la connexion de DDR portant sur toutes les opérations de base de données dans le bloc de l’un éclater où tenantId1 est conservé. La requête renvoie uniquement les blogs stockés sur l’actif partagé, mais pas ceux stockés sur n’importe quel autres milieu des fragments. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Données dépendantes de routage avec Dapper et DapperExtensions

Dapper est livré avec un écosystème des extensions supplémentaires qui peut fournir plus de commodité et d’abstraction à partir de la base de données lors du développement d’applications de base de données. DapperExtensions est un exemple. 

À l’aide de DapperExtensions dans votre application ne modifie pas la façon dont les connexions de base de données sont créées et gérées. Il incombe toujours l’application pour ouvrir des connexions et les objets de connexion SQL Client régulières sont attendus par les méthodes d’extension. Nous pouvons compter sur [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) comme indiqué ci-dessus. Les exemples de code suivants montrent la seule modification consiste à n’a plus qu’à écrire les instructions T-SQL :

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Et Voici l’exemple de code pour la requête : 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Gestion des défaillances temporaires

L’équipe Microsoft Patterns & Practices publié le [Bloc d’Application de gestion des pannes transitoires](http://msdn.microsoft.com/library/hh680934.aspx) pour aider les développeurs d’applications atténuer les conditions de défaillance transitoire communes rencontrées lors de l’exécution dans le nuage. Pour plus d’informations, consultez [Perseverance, le Secret de toutes les luttes : utilisant le bloc d’Application de gestion des pannes transitoires](http://msdn.microsoft.com/library/dn440719.aspx).

L’exemple de code s’appuie sur la bibliothèque de pannes transitoires pour se protéger contre les défaillances temporaires. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** dans le code ci-dessus est défini comme un **SqlDatabaseTransientErrorDetectionStrategy** avec un nombre de tentatives de 10 et 5 secondes délai d’attente entre les tentatives. Si vous utilisez des transactions, assurez-vous que la portée de votre nouvelle tentative revient au début de la transaction dans le cas d’une défaillance temporaire.

## <a name="limitations"></a>Limitations

Les approches décrites dans ce document impliquent un certain nombre de limitations :

* L’exemple de code pour ce document n’indique pas comment gérer le schéma sur le milieu des fragments.
* Étant donné une demande, nous partons du principe que tout son traitement de base de données est contenu au sein d’un unique partagé comme identifié par la clé ont fourni par la demande. Cependant, cette hypothèse ne pas toujours contient, par exemple, lorsqu’il n’est pas possible de faire une clé ont disponibles. Pour contourner ce problème, la bibliothèque cliente élastique de la base de données inclut la [classe de MultiShardQuery](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). La classe implémente une abstraction de la connexion pour l’interrogation sur plusieurs milieu des fragments. L’utilisation de MultiShardQuery en combinaison avec Dapper est au-delà de la portée de ce document.

## <a name="conclusion"></a>Conclusion

Applications à l’aide de Dapper et DapperExtensions peuvent facilement bénéficier à partir des outils de base de données élastique pour la base de données de SQL Azure. Les étapes décrites dans ce document, ces applications peuvent utiliser des fonctionnalité de l’outil de données dépendantes de routage par la modification de la création et l’ouverture de nouveaux objets [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) d’utiliser l’appel de [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) de la bibliothèque de client élastique de la base de données. Cela permet de limiter les modifications d’application requises pour les emplacements où les nouvelles connexions sont créées et ouvert. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
 