<properties 
    pageTitle="À l’aide de la bibliothèque de client de base de données élastique avec Entity Framework | Microsoft Azure" 
    description="Utilisez la bibliothèque de client de base de données élastique et Entity Framework pour le codage des bases de données" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="elastic-database-client-library-with-entity-framework"></a>Bibliothèque de client de base de données élastique avec Entity Framework 
 
Ce document présente les modifications dans une application Entity Framework qui sont nécessaires à l’intégration avec les [Outils de base de données élastique](sql-database-elastic-scale-introduction.md). Le focus est sur la composition de [Éclater mapper la gestion](sql-database-elastic-scale-shard-map-management.md) et le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) avec l’approche Entity Framework **Code First** . Le didacticiel de [Code First – nouvelle base de données](http://msdn.microsoft.com/data/jj193542.aspx) pour EF sert de notre exemple en cours d’exécution tout au long de ce document. L’exemple de code présent le document d’accompagnement est partie des outils de base de données élastique jeu d’échantillons dans les exemples de Code Visual Studio.
  
## <a name="downloading-and-running-the-sample-code"></a>Téléchargement et exécution de l’exemple de Code
Pour télécharger le code de cet article :

* Visual Studio 2012 ou une version ultérieure est requis. 
* Démarrez Visual Studio. 
* Dans Visual Studio, sélectionnez Fichier -> Nouveau projet. 
* Dans la boîte de dialogue « Nouveau projet », accédez aux **Exemples en ligne** pour **Visual C#** et tapez « db élastique » dans la zone de recherche dans le coin supérieur droit.
    
    ![Entity Framework et élastique de la base de données exemple d’application][1] 

    Sélectionnez l’échantillon appelé **Élastique outils de base de données pour SQL Azure – intégration de Entity Framework**. Après avoir accepté la licence, l’exemple charge. 

Pour exécuter l’exemple, vous devez créer trois bases de données vides dans la base de données de SQL Azure :

* Base de données du Gestionnaire de table partagé
* Base de données d’éclater 1
* Base de données d’éclater 2

Une fois que vous avez créé ces bases de données, remplir les espaces réservés dans **Program.cs** avec le nom de votre serveur de base de données SQL Azure, les noms de base de données et vos informations d’identification pour se connecter aux bases de données. Générez la solution dans Visual Studio. Visual Studio télécharge les requis packages NuGet la bibliothèque cliente élastique de la base de données, Entity Framework et traitement dans le cadre du processus de génération de l’erreur transitoire. Vérifiez que la restauration de packages NuGet est activée pour votre solution. Vous pouvez activer ce paramètre en cliquant sur le fichier de solution dans l’Explorateur de solutions Visual Studio. 

## <a name="entity-framework-workflows"></a>Flux de travail Entity Framework 

Les développeurs de Entity Framework s’appuient sur l’un des suivant quatre flux de travail pour générer des applications et assurer la persistance des objets d’application : 

* **Code First (nouvelle base de données)**: développeur d’EF le crée le modèle dans le code d’application et EF génère ensuite la base de données à partir de celui-ci. 
* **Code First (base de données existante)**: le développeur permet de générer le code d’application pour le modèle à partir d’une base de données existante EF.
* **Le premier modèle**: le développeur crée le modèle dans le Concepteur EF et EF crée la base de données à partir du modèle.
* **Première base de données**: le développeur utilise EF outillage pour déduire le modèle à partir d’une base de données existante. 

Toutes ces approches reposent sur la classe DbContext pour gérer en toute transparence les connexions de base de données et le schéma de base de données pour une application. Que nous allons aborder plus en détail plus loin dans le document, les différents constructeurs de la classe de base DbContext autorisent différents niveaux de contrôle sur la création de la connexion, la base de données d’amorçage et le schéma de création. Problèmes proviennent surtout du fait que la gestion des connexions de base de données fournie par EF croise avec les fonctions de gestion de connexion les données dépendant d’interfaces de routage fournies par la bibliothèque cliente élastique de la base de données. 

## <a name="elastic-database-tools-assumptions"></a>Hypothèses d’outils de base de données élastique 

Pour les définitions des termes, reportez-vous au [Glossaire des outils élastique de la base de données](sql-database-elastic-scale-glossary.md).

Avec la bibliothèque de client de base de données élastique, vous définissez les partitions de vos données d’application appelées shardlets. Shardlets sont identifiées par une clé ont et sont mappés sur les bases de données spécifiques. Une application peut avoir autant de bases de données en fonction des besoins et distribuer le shardlets afin de fournir suffisamment de capacité ou les performances étant donné les besoins de l’entreprise en cours. Le mappage de valeurs de clé ont les bases de données est stocké par un mappage partagé fourni par le client de base de données élastique API. Nous appelons cette fonctionnalité de **Gestion de carte partagé**ou SMM en abrégé. Le mappage partagé sert également le service Broker pour les connexions de base de données pour les requêtes comportant une clé ont. Nous appelons cette fonctionnalité comme le **routage dépendant des données**. 
 
Le Gestionnaire de mappage partagé protège les utilisateurs à partir de vues incohérentes dans les données de shardlet qui peuvent se produire lorsque les opérations de gestion shardlet simultanés (par exemple, le déplacement des données à partir d’un éclater à l’autre) sont produisent. Pour ce faire, les cartes partagé géré par l’intermédiaire de la bibliothèque client les connexions de base de données pour une application. Cela permet à la fonctionnalité de mappage partagé supprimer automatiquement une connexion de base de données lors des opérations de gestion partagé a un impact sur le shardlet que la connexion a été créée pour. Cette approche a besoin d’intégrer des fonctionnalités d’EF, telles que la création de nouvelles connexions à partir d’un existant pour vérifier l’existence de la base de données. En règle générale, nous avons observé que les constructeurs de DbContext standard seulement fonctionnent correctement pour les connexions de base de données fermée peuvent être clonées en toute sécurité pour EF fonctionnent. À la place, le principe de conception de base de données élastique est à négocier uniquement des connexions ouvertes. On pourrait penser que la fermeture d’une connexion demandée par la bibliothèque cliente avant immobiliers à la DbContext EF peut résoudre ce problème. Toutefois, par la fermeture de la connexion et s’appuyant sur EF à rouvrez-la, une foregoes les contrôles de validation et de la cohérence effectuées par la bibliothèque. La fonctionnalité de migrations dans EF, utilise toutefois, ces connexions pour gérer le schéma de base de données sous-jacente d’une manière qui soit transparente pour l’application. Idéalement, nous souhaitons conserver et combiner ces fonctions de la bibliothèque de client de base de données élastique et EF dans la même application. La section suivante décrit ces propriétés et ces exigences plus en détail. 


## <a name="requirements"></a>Configuration requise 

Lorsque vous travaillez avec la bibliothèque de client de base de données élastique et API Entity Framework, que nous souhaitons conserver les propriétés suivantes : 

* **Horizontale**: pour ajouter ou supprimer des bases de données de la couche de données de l’application sharded que nécessaire pour les demandes de capacité de l’application. Cela signifie le contrôle sur le mappent les API manager pour gérer les bases de données et les mappages de shardlets la création et la suppression des bases de données à l’aide de l’éclater élastique de la base de données. 

* **Cohérence**: l’application utilise ont et utilise les capacités routage de données dépendant de la bibliothèque cliente. Pour éviter la corruption ou les résultats de la requête incorrect, les connexions sont demandées via le Gestionnaire de table partagé. Cela conserve également la validation et la cohérence.
 
* **Code First**: conserver la commodité du premier paradigme de code de EF. Dans le premier Code, les classes dans l’application sont mappées en toute transparence avec les structures sous-jacentes de la base de données. Le code d’application interagit avec le DbSets qui masque la plupart des aspects impliqués dans le traitement de base de données sous-jacente.
 
* **Schéma**: Entity Framework gère la création de schémas de base de données initiale et de l’évolution de schéma suivants par le biais de migrations. En conservant ces capacités, adapter votre application est facile à mesure que les données évoluent. 

Le guide suivant indique comment répondre à ces exigences pour les applications du premier Code à l’aide des outils de base de données élastique. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Données dépendantes de routage à l’aide de DbContext EF 

Connexions de base de données avec Entity Framework sont généralement gérées par l’intermédiaire de sous-classes de **DbContext**. Créer ces sous-classes en dérivant de **DbContext**. C’est ici que vous définissez votre **DbSets** qui implémentent les collections reposant sur la base de données d’objets CLR pour votre application. Dans le contexte de routage dépendant des données, nous pouvons identifier plusieurs propriétés utiles qui ne contiennent pas nécessairement pour d’autres scénarios d’application première du code EF : 

* La base de données existe déjà et a été enregistré dans la table d’éclater élastique de la base de données. 
* Le schéma de l’application a déjà été déployé sur la base de données (voir ci-dessous). 
* Les connexions de routage dépendant des données pour la base de données sont demandées par le mappage partagé. 

Pour intégrer le **DbContexts** avec le routage dépendant des données pour l’évolutivité :

1. Créer des connexions de base de données physique via les interfaces de client élastique de la base de données du Gestionnaire de table partagé, 
2. Retour à la ligne de la connexion avec la sous-classe **DbContext**
3. Passer de la connexion vers le bas dans les classes de base de **DbContext** pour assurer que tout le traitement sur le côté d’EF se produit également. 

L’exemple de code suivant illustre cette approche. (Ce code est également dans le projet Visual Studio qui l’accompagne)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Points principaux
* Un nouveau constructeur remplace le constructeur par défaut dans la sous-classe DbContext 
* Le nouveau constructeur prend les arguments qui sont requis pour le routage dépendant des données via la bibliothèque de client de base de données élastique :
    * le mappage partagé pour accéder aux interfaces de routage dépendant des données,
    * la clé ont pour identifier le shardlet,
    * une chaîne de connexion avec les informations d’identification pour la connexion de routage dépendant des données pour l’éclater. 
 
* L’appel au constructeur de classe de base prend un détour dans une méthode statique qui effectue toutes les étapes nécessaires pour le routage dépendant des données. 
   * Il utilise l’appel de OpenConnectionForKey des interfaces client élastique de la base de données sur la carte partagé pour établir une connexion ouverte.
   * Le mappage partagé crée la connexion ouverte pour le partagé qui contient le shardlet de la clé ont donné.
   * Cette connexion ouverte est passée sur le constructeur de classe de base de DbContext pour indiquer que cette connexion doit être utilisée par EF au lieu de laisser EF à créer une nouvelle connexion automatiquement. Ainsi, la connexion a été marqué par le client de base de données élastique API afin qu’il peut garantir la cohérence dans les opérations de gestion de table partagé.
 
  
Utiliser le constructeur new pour votre sous-classe DbContext plutôt que le constructeur par défaut dans votre code. Voici un exemple : 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

Le constructeur new ouvre la connexion à la partagé qui contient les données pour le shardlet, identifié par la valeur de **tenantid1**. Le code dans le bloc **à l’aide de** reste inchangé à accéder le **DbSet** pour les blogs à l’aide d’EF sur l’éclater pour **tenantid1**. Cela change pour le code à l’aide de la sémantique de bloc tels que toutes les opérations de base de données sont maintenant étendues à l’un éclater où **tenantid1** est conservé. Par exemple, une requête LINQ sur un **DbSet** blogs renvoie uniquement blogs stockés sur l’actif partagé, mais pas ceux qui sont stockés sur un autres milieu des fragments.  

#### <a name="transient-faults-handling"></a>Gestion de défaillances temporaires
L’équipe Microsoft Patterns & Practices publié [Le transitoire de gestion des applications bloc Fault](https://msdn.microsoft.com/library/dn440719.aspx). La bibliothèque est utilisée avec la bibliothèque de client évolutivité élastique en combinaison avec EF. Toutefois, assurez-vous que toute exception passagère renvoie à un endroit où nous pouvons garantir que le constructeur new est utilisé après une défaillance transitoire afin que toute nouvelle tentative de connexion à l’aide des constructeurs, que nous avons modifié. Dans le cas contraire, une connexion à l’éclater correct n’est pas garantie, et il n’y a aucun cas, que la connexion est maintenue au gré de changements à la table partagé. 

L’exemple de code suivant illustre comment une stratégie de nouvelles tentatives SQL peut être utilisée dans les constructeurs de la sous-classe **DbContext** nouvelles : 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** dans le code ci-dessus est défini comme un **SqlDatabaseTransientErrorDetectionStrategy** avec un nombre de tentatives de 10 et 5 secondes délai d’attente entre les tentatives. Cette approche est similaire aux conseils EF et les transactions effectuées par l’utilisateur (voir [Limitations avec une nouvelle tentative de stratégies d’exécution (EF6 et supérieur)](http://msdn.microsoft.com/data/dn307226). Les deux situations nécessitent que le programme d’application contrôle l’étendue à laquelle renvoie l’exception passagère : rouvrir la transaction, soit (comme indiqué), recréer le contexte à partir du constructeur approprié, qui utilise la bibliothèque cliente élastique de la base de données.

La nécessité de contrôler où les exceptions transitoire nous prennent dans la portée empêche également l’utilisation de la **SqlAzureExecutionStrategy** intégrés qui accompagne EF. **SqlAzureExecutionStrategy** rouvrir une connexion mais pas utilisez **OpenConnectionForKey** et par conséquent de contourner la validation est effectuée dans le cadre de l’appel de **OpenConnectionForKey** . Au lieu de cela, l’exemple de code utilise le **DefaultExecutionStrategy** intégré qui est également fourni avec EF. Au lieu de **SqlAzureExecutionStrategy**, il fonctionne en combinaison avec la stratégie de nouvelle tentative de la gestion des pannes transitoires. La stratégie d’exécution est définie dans la classe **ElasticScaleDbConfiguration** . Notez que nous avons décidé de ne pas utiliser le **DefaultSqlExecutionStrategy** car il propose pour utiliser **SqlAzureExecutionStrategy** en cas d’exceptions temporaires - qui entraînerait un comportement incorrect comme indiqué. Pour plus d’informations sur les tentatives de différentes stratégies et EF, consultez [Souplesse de connexion dans EF](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Constructeur réécritures
Les exemples de code ci-dessus illustrent la par défaut constructeur réécrit requis pour votre application pour utiliser les données dépendantes de routage avec Entity Framework. Le tableau suivant généralise cette approche aux autres constructeurs. 


Constructeur en cours  | Constructeur réécrite pour les données | Constructeur de base | Notes
---------- | ----------- | ------------|----------
MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |La connexion doit être une fonction de la carte d’éclater et la clé de routage dépendant des données. Vous devez création de connexion automatique de dérivation par EF et plutôt utilisez le mappage de partagé à négocier la connexion. 
MyContext(string)|ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |La connexion est fonction de la carte d’éclater et la clé de routage dépendant des données. Une chaîne de connexion ou le nom de la base de données fixe ne fonctionne pas comme elles validation de dérivation par le mappage partagé. 
MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |La connexion sera créée pour la clé de mappage et ont donné partagé avec le modèle fourni. Le modèle compilé sera passé à la c'tor de base.
MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |La connexion doit être déduit à partir de la table partagé et la clé. Il ne peut être fournie sous la forme d’une entrée (sauf si cette entrée a été déjà utilisant le mappage partagé et la clé). La valeur booléenne est passée. 
MyContext (string, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |La connexion doit être déduit à partir de la table partagé et la clé. Il ne peut être fournie sous la forme d’une entrée (sauf si cette entrée a été à l’aide de la carte partagé et la clé). Est passé le modèle compilé. 
MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |Le nouveau constructeur doit s’assurer que les connexions de ObjectContext passé comme entrée sont ré-acheminée vers une connexion gérée par évolutivité élastique. Une description détaillée de la ObjectContexts est au-delà de la portée de ce document.
MyContext (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool)| DbContext (DbConnection, DbCompiledModel, bool) ; |La connexion doit être déduit à partir de la table partagé et la clé. La connexion ne peut être fournie sous la forme d’une entrée (sauf si cette entrée a été déjà utilisant le mappage partagé et la clé). Modèle et Boolean sont transmis au constructeur de classe de base. 

## <a name="shard-schema-deployment-through-ef-migrations"></a>Déploiement du schéma partagé par le biais de migrations d’EF 

Gestion des schémas automatique est pratique fourni par Entity Framework. Dans le contexte d’applications à l’aide des outils de base de données élastique, nous souhaitons conserver cette fonctionnalité pour configurer automatiquement le schéma au milieu des fragments nouvellement créés lorsque des bases de données sont ajoutés à l’application sharded. Le cas d’usage principal est d’augmenter la capacité au niveau de la couche de données pour les applications sharded l’utilisation d’EF. S’appuyant sur les capacités d’EF pour la gestion des schémas permet de réduire l’effort d’administration de base de données avec une application de sharded basée sur EF. 

Déploiement du schéma par le biais de migrations d’EF fonctionne mieux sur **des connexions non ouverts**. Ceci est à la différence du scénario pour dépendante des données de routage qui s’appuie sur la connexion ouverte fournie par le client de base de données élastique API. Une autre différence est la nécessité de la cohérence : lors souhaitable pour assurer la cohérence de toutes les connexions de routage dépendant des données à protéger contre toute manipulation de mappage partagé simultané, il n’est pas un problème avec le déploiement de schéma initiale pour une nouvelle base de données qui a ne pas encore été enregistré dans la table partagé et ne pas encore été allouée pour contenir shardlets. Nous pouvons donc reposent sur les connexions régulières de la base de données pour cette scénarios, et non le routage dépendant des données.  

Cela conduit à une approche où déploiement du schéma par le biais de migrations d’EF est étroitement couplé avec l’enregistrement de la nouvelle base de données sous la forme d’un partagé dans mappage partagé de l’application. Il s’appuie sur les conditions préalables suivantes : 

* La base de données a déjà été créé. 
* La base de données est vide : il conserve aucun schéma de l’utilisateur et les données utilisateur.
* La base de données ne sont pas encore accessibles par le biais de l’API de client élastique de la base de données de routage dépendant des données. 

Avec ces conditions préalables en place, nous pouvons créer un **SqlConnection** non ouvert régulière et tuer des migrations d’EF pour le déploiement de schéma. L’exemple de code suivant illustre cette approche. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 
 

Cet exemple illustre la méthode **RegisterNewShard** qui enregistre l’éclater dans le mappage partagé, déploie le schéma via les migrations EF et stocke un mappage une clé ont l’éclater. Il s’appuie sur un constructeur de la sous-classe de **DbContext** (**ElasticScaleContext** dans l’exemple) qui accepte une chaîne de connexion SQL en tant qu’entrée. Le code de ce constructeur est directe, comme le montre l’exemple suivant : 


        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 
 
Un avez peut-être utilisé la version du constructeur héritée de la classe de base. Mais le code doit garantir que l’initialiseur par défaut pour EF est utilisé lors de la connexion. Par conséquent court détour dans la méthode statique avant d’appeler le constructeur de classe de base avec la chaîne de connexion. Notez que l’enregistrement du milieu des fragments doit s’exécuter dans un domaine d’application différent ou un processus afin de garantir que les paramètres de l’initialiseur d’EF n’entrent pas en conflit. 


## <a name="limitations"></a>Limitations 

Les approches décrites dans ce document impliquent un certain nombre de limitations : 

* Les applications EF qui utilisent **LocalDb** doivent tout d’abord migrer vers une base de données SQL Server avant d’utiliser la bibliothèque de client de base de données élastique. Mise à l’échelle une application via ont avec évolutivité élastique n’est pas possible avec **LocalDb**. Notez que le développement permet toujours **LocalDb**. 

* Des modifications à l’application qui impliquent la modification de schéma de base de données doivent passer par les migrations EF sur tous les milieu des fragments. L’exemple de code pour ce document ne présente pas du faire. Envisagez d’utiliser la mise à jour de bases de données avec un paramètre ConnectionString pour itérer sur tous les milieu des fragments ; ou extraire le script T-SQL pour la migration en attente à l’aide de la mise à jour de bases de données avec l’option – Script option et appliquer le script T-SQL à votre milieu des fragments.  

* Étant donné une demande, il est supposé que son traitement de base de données est contenu dans un unique partagé comme identifié par la clé ont fourni par la demande. Cependant, cette hypothèse n’est pas toujours vraie. Par exemple, lorsqu’il n’est pas possible de faire une clé ont disponibles. Pour contourner ce problème, la bibliothèque cliente fournit la classe **MultiShardQuery** qui implémente une abstraction de la connexion pour l’interrogation sur plusieurs milieu des fragments. Formation à l’utilisation de l' **MultiShardQuery** en combinaison avec EF est au-delà de la portée de ce document

## <a name="conclusion"></a>Conclusion

Les étapes décrites dans ce document, les applications de EF peuvent utiliser les fonctionnalités de la bibliothèque cliente élastique de la base de données de routage dépendant des données par les constructeurs de sous-classes **DbContext** utilisées dans l’application EF de refactorisation. Cela permet de limiter les modifications requises pour les emplacements où les classes **DbContext** existent déjà. En outre, les applications de EF peuvent continuer à bénéficier de déploiement de schéma automatique en combinant les étapes qui appellent les migrations d’EF nécessaires avec l’inscription de nouveau milieu des fragments et des mappages dans le mappage d’éclater. 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
 