<properties
    pageTitle="Toutes les rubriques pour le service de base de données de SQL | Microsoft Azure"
    description="Tableau de toutes les rubriques du service Azure nommé de base de données SQL qui existent sur http://azure.microsoft.com/documentation/articles/, titre et description."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="genemi"/>


# <a name="all-topics-for-azure-sql-database-service"></a>Toutes les rubriques pour le service de base de données de SQL Azure

Cette rubrique répertorie toutes les rubriques qui s’applique directement au service **De base de données SQL** d’Azure. Vous pouvez rechercher cette page Web pour les mots clés à l’aide de **Ctrl + F**, pour trouver les sujets d’intérêt en cours.




## <a name="new"></a>Nouveau

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 1 | [Daxko/CSI utilisé Azure pour accélérer son cycle de développement et améliorer ses services clientèle et les performances](sql-database-implementation-daxko.md) | Découvrez comment Daxko/CSI utilise SQL de base de données pour accélérer le cycle de développement et améliorer ses services clientèle et les performances |
| 2 | [Azure offre portée GEP et une plus grande efficacité](sql-database-implementation-gep.md) | Découvrez comment le GEP utilise SQL de base de données pour atteindre les clients plus globales et plus efficaces |
| 3 | [Avec Azure, SnelStart a développé rapidement de ses services d’entreprise à un débit de 1 000 nouvelles Azure SQL bases de données par mois](sql-database-implementation-snelstart.md) | Découvrez comment SnelStart utilise de la base de données SQL de développé rapidement de ses services d’entreprise à un débit de 1 000 nouvelles Azure SQL bases de données par mois |
| 4 | [Umbraco utilise la base de données de SQL Azure pour rapidement des services de fourniture et de l’échelle pour des milliers de clients dans le nuage](sql-database-implementation-umbraco.md) | Découvrez comment Umbraco utilise de la base de données SQL pour rapidement provisionner et services d’échelle pour des milliers de clients dans le nuage |
| 5 | [Gérer la base de données SQL Azure avec PowerShell](sql-database-manage-powershell.md) | Gestion de la base de données SQL Azure avec PowerShell. |
| 6 | [Prise en charge SSMS pour AMF de publicité Azure avec SQL de base de données et SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) | Utilisez Multi-prises en charge de l’authentification avec SSMS pour la base de données SQL et l’entrepôt de données SQL. |
| 7 | [Expliquer les unités de Transaction de base de données (DTUs) et les unités de Transaction élastique de base de données (eDTUs)](sql-database-what-is-a-dtu.md) | Comprendre un Azure SQL base de données à l’unité de transaction est. |


## <a name="updated-articles-sql-database"></a>Articles mis à jour, de la base de données SQL

Cette section répertorie les articles qui ont été mis à jour récemment, où la mise à jour a été grand ou importantes. Pour chaque article mis à jour, un extrait de code approximative du texte démarque supplémentaire s’affiche. Les articles ont été mis à jour au sein de la plage de dates de **2016-08-22** pour **2016-10-05**.

| &nbsp; | Article | Texte mis à jour, l’extrait de code | Mise à jour lorsque |
| --: | :-- | :-- | :-- |
| 8 | [Gérer la base de données SQL Azure avec PowerShell](sql-database-command-line-tools.md) | Créer un groupe de ressources pour les nos de la base de données SQL et les ressources Azure associées avec l’applet de commande New-AzureRmResourceGroup (https://msdn.microsoft.com/library/azure/mt759837.aspx). *$resourceGroupName = « resourcegroup1 » $resourceGroupLocation = « northcentralus » New-AzureRmResourceGroup-Name $resourceGroupName-emplacement $resourceGroupLocation* Pour plus d’informations, reportez-vous à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure (... / powershell-azure-ressource-manager.md). Pour un exemple de script, voir Création d’une base de données SQL (sql-base de données-get-démarré-powershell.md create-a-sql-database-powershell-script) de script PowerShell. **Créer un serveur de base de données SQL** Créer un serveur de base de données SQL avec l’applet de commande New-AzureRmSqlServer (https://msdn.microsoft.com/library/azure/mt603715.aspx). Remplacez *server1* par le nom de votre serveur. Les noms de serveur doivent être uniques sur tous les serveurs de base de données de SQL Azure. Si le nom du serveur est déjà utilisé, vous obtenez une erreur. Cette commande peut prendre plusieurs minutes. La resou | 2016-09-14 |
| 9 | [Copier une base de données Azure SQL à l’aide de PowerShell](sql-database-copy-powershell.md) |   SQL de base de données source (les fichiers de base de données pour copier)--$sourceDbName = « db1 » $sourceDbServerName = « Serveur1 » $sourceDbResourceGroupName = copie de base de données SQL « rg1 » (la nouvelle base de données à créer)--$copyDbName = « db1_copy » $copyDbServerName = « Serveur2 » $copyDbResourceGroupName = « rg2 » copie une base de données sur le même serveur--New-AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - nom du serveur $sourceDbServerName - DatabaseName $sourceDbName - CopyDatabaseName $copyDbName copier une base de données sur un serveur différent : New-AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - nom du serveur $sourceDbServerName - DatabaseName $sourceDbName - CopyResourceGroupName $copyDbResourceGroupName - CopyServerName $copyDbServerName - CopyDatabaseName $copyDbName copier une base de données dans un pool d’élasticité de la base de données--$poolName = « pool1 » New-AzureRmSqlDatabaseCopy - ResourceGroupName $ sourceDbResourceGroupName - nom du serveur $sourceDbServerName - nom_base_données $sourceDbName - CopyReso | 2016-09-08 |
| 10 | [Créer un pool de la base de données élastique avec C#](sql-database-elastic-pool-create-csharp.md) |   Console.WriteLine ("serveur pare-feu...") ;  FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule (_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress) ;  Console.WriteLine ("pare-feu du serveur :" + fwr. FirewallRule.Id) ;  Console.WriteLine ("élastique pool...") ;  Les epr ElasticPoolCreateOrUpdateResponse = CreateOrUpdateElasticDatabasePool (_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus) ;  Console.WriteLine ("pool élastique :" + epr. ElasticPool.Id) ;  Console.WriteLine("Database...") ;  Dbr de DatabaseCreateOrUpdateResponse = CreateOrUpdateDatabase (_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName) ;  Console.WriteLine ("base de données :" + dbr. Database.Id) ;  Console.WriteLine ("Appuyez sur une touche pour continuer...") ;  Console.ReadKey() ;  } statique ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa | 2016-09-14 |
| 11 | [Script PowerShell pour identifier les bases de données appropriées pour un pool d’élasticité de la base de données](sql-database-elastic-pool-database-assessment-powershell.md) | Les candidats **pool de base de données pour élastique, nous exclure maître et les bases de données qui se trouvent déjà dans un pool. Vous pouvez ajouter plusieurs bases de données à la liste des exclus ci-dessous en fonction des besoins** $ListOfDBs = Get-AzureRmSqlDatabase - nom_serveur $servername. Split('.') 0 - ResourceGroupName $ResourceGroupName / Where-Object {$_. Notin - DatabaseName (« master ») - et $_. CurrentServiceLevelObjectiveName - notin (« ElasticPool »)- et $_. CurrentServiceObjectiveName - notin (« ElasticPool »)} $outputConnectionString = « Source de données = $outputServerName ; Integrated Security = false ; Initial catalogue = $outputdatabaseName ; Id utilisateur = $outputDBUsername ; Mot de passe = $outputDBpassword » $destinationTableName = « resource_stats_output » pour **créer une table dans la base de données de sortie pour la collecte de métriques** $sql = "Si NOT EXISTS (sélectionnez * à partir de sys.objects où object_id = OBJECT_ID(N'$($destinationTableName)') et tapez dans (N'U')) $($destinationTableName) de créer une Table BEGIN (nom_base_de_données varchar (128), slo varchar (20), end_time datetime, float d’avg_cpu, avg_ | 2016-09-29 |
| 12 | [Didacticiel de la base de données de SQL : création d’une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md) |   ! Nouvelle base de données sql 1 (. / media/sql-database-get-started/sql-database-new-database-1.png) 3. Cliquez sur **De base de données SQL** pour ouvrir la lame de la base de données SQL. Le contenu de cette lame varie en fonction du nombre de vos abonnements et vos objets existants (tels que les serveurs existants).  ! Nouvelle base de données sql 2 (. / media/sql-database-get-started/sql-database-new-database-2.png) 4. Dans la zone de texte **nom de la base de données** , fournissez un nom pour votre base de données premier - comme « ma base de données ». Une coche verte indique que vous avez fourni un nom valide.  ! Nouvelle base de données sql 3 (. / media/sql-database-get-started/sql-database-new-database-3.png) 5. Si vous disposez de plusieurs abonnements, sélectionnez un abonnement. 6. Sous le **groupe de ressources**, cliquez sur **Créer nouveau** et fournir un nom pour votre premier groupe de ressources - tels que « mes--groupe de ressources ». Une coche verte indique que vous avez fourni un nom valide.  ! Nouvelle base de données sql 4 (. / media/sql-database-get-started/sql-database-new-database-4.png) 7. Sous **Sélectionnez source* | 2016-09-08 |
| 13 | [Essayez de la base de données SQL : Utiliser C# pour créer une base de données SQL avec la bibliothèque de base de données SQL pour .NET](sql-database-get-started-csharp.md) | **Créer un service principal pour accéder aux ressources** Le script PowerShell suivant crée l’application d’Active Directory (AD) et l’entité du service dont nous avons besoin pour authentifier notre application C. Le script renvoie les valeurs que nous avons besoin pour l’exemple précédent de la C. Pour plus d’informations, reportez-vous à la section PowerShell d’Azure utilisation pour créer une entité de sécurité du service d’accès aux ressources (... / resource-groupe-authentification-service-principal.md).  Se connecter à Azure.  Ajouter-AzureRmAccount si vous disposez de plusieurs abonnements, ne commentez pas et à l’abonnement que vous souhaitez utiliser.  $subscriptionId = « {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx} » Set-AzureRmContext - SubscriptionId $subscriptionId fournir ces valeurs pour votre nouvelle application DAS.  $appName est le nom complet de votre application, il doit être unique dans votre répertoire.  $uri n’a pas besoin d’être un uri réel.  $secret est un mot de passe que vous créez.  $appName = « {app-name} » $uri = « http://{app-name »} $secret = « {app-mot de passe} » créer une application DAS $azureAdApplication = New-AzureRmADApp | 2016-09-23 |
| 14 | [Gestion des bases de données SQL Azure via le portail Azure](sql-database-manage-portal.md) | Pour afficher ou mettre à jour les paramètres de votre base de données, cliquez sur le paramètre de votre choix sur la lame de la base de données SQL : ! Paramètres de base de données SQL (. / media/sql-database-manage-portal/settings.png) **comment pour trouver un nom de serveur complet de bases de données SQL ?** Pour afficher le nom de votre serveur de bases de données, cliquez sur **vue d’ensemble** sur la lame de la **base de données SQL** et notez le nom du serveur : ! Paramètres de base de données SQL (. / media/sql-database-manage-portal/server-name.png) **comment gérer les règles de pare-feu pour contrôler l’accès à mon serveur SQL et la base de données ?** Pour afficher, créer ou mettre à jour les règles de pare-feu, cliquez sur **pare-feu de serveur** sur la lame de la **base de données SQL** . Pour plus d’informations, consultez configurer une règle de pare-feu au niveau du serveur de base de données de SQL Azure via le portail Azure (sql-base de données-configuration-pare-feu-settings.md). ! règles de pare-feu (. / media/sql-database-manage-portal/sql-database-firewall.png) **comment pour modifier mon SQL de base de données service niveau niveau de performance ou ?** Pour mettre à jour le niveau de service niveau ou les performances d’une base de données SQL, cliquez sur ** niveau de tarification (s | 2016-09-20 |





## <a name="get-started"></a>Mise en route

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 15 | [Génère des applications mutualisées avec base de données SQL Azure avec efficacité et d’Isolation](sql-database-build-multi-tenant-apps.md) | Découvrez comment la base de données de SQL génère mutualisées apps |
| 16 | [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md) | Découvrez comment se connecter à la base de données SQL Azure en utilisant SQL Server Management Studio (SSMS). Ensuite, exécutez un exemple de requête à l’aide de Transact-SQL (T-SQL). |
| 17 | [Se connecter à la base de données de SQL à l’aide de .NET (C#)](sql-database-develop-dotnet-simple.md) | Utilisez l’exemple de code dans cette rapide commencent à générer une application moderne avec C# et soutenue par une puissante base de données relationnelle dans le nuage avec la base de données de SQL Azure. |
| 18 | [Créer un nouveau pool d’élasticité de la base de données avec le portail Azure](sql-database-elastic-pool-create-portal.md) | Comment ajouter un pool élastique évolutive de la base de données à votre configuration de base de données SQL pour faciliter l’administration et le partage entre plusieurs bases de données des ressources. |
| 19 | [Découvrez des didacticiels de base de données SQL Azure](sql-database-explore-tutorials.md) | En savoir plus sur les capacités et les fonctionnalités de base de données SQL |
| 20 | [Didacticiel de la base de données de SQL : création d’une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md) | Apprenez à configurer un serveur de logique de la base de données SQL, règle de pare-feu de serveur, de base de données SQL et exemples de données. En outre, comment se connecter avec les outils clients, configurer des utilisateurs et définir une règle de pare-feu de base de données. |
| 21 | [Base de données SQL Azure permet de sécuriser et de protéger](sql-database-helps-secures-and-protects.md) | Découvrez comment la base de données de SQL contribue à sécuriser et protéger |
| 22 | [Base de données SQL Azure apprend &amp; adapte](sql-database-learn-and-adapt.md) | Découvrez comment la base de données de SQL apprend et s’adapte |
| 23 | [Choisir un nuage option de SQL Server : base de données d’Azure, SQL (PaaS) ou de SQL Server sur Azure VM (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | Découvrez l’option SQL Server de nuage adaptée à votre application : de la base de données Azure SQL (PaaS) ou de SQL Server dans le nuage Azure machines virtuelles en fonctionnement. |
| 24 | [Échelles de base de données SQL Azure à la volée](sql-database-scale-on-the-fly.md) | Découvrez comment la base de données de SQL s’adapte à la volée |
| 25 | [Explorez les Démarrages rapides de Solution de base de données SQL Azure](sql-database-solution-quick-starts.md) | En savoir plus sur les Solutions de base de données SQL Azure |
| 26 | [Base de données SQL Azure fonctionne dans votre environnement](sql-database-works-in-your-environment.md) | Découvrez comment les base de données de SQL permet, sécurise et protège |



## <a name="build-an-app"></a>Créez une application.

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 27 | [Dépanner, diagnostiquer et éviter les erreurs transitoires et les erreurs de connexion SQL pour la base de données de SQL](sql-database-connectivity-issues.md) | Apprenez à dépanner, diagnostiquer et empêcher une erreur de connexion SQL ou transitoire dans la base de données de SQL Azure.  |
| 28 | [Se connecter à une base de données SQL avec Visual Studio](sql-database-connect-query.md) | Écrire un programme en C# pour interroger et se connecter à la base de données SQL. Informations sur les adresses IP, les chaînes de connexion, connexion sécurisée et gratuite de Visual Studio. |
| 29 | [Ports autres que le 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md) | Parfois, les connexions client à partir de ADO.NET pour V12 de base de données SQL Azure ignorer le proxy et interagissent directement avec la base de données. Ports de 1433 sont importants. |
| 30 | [Codes d’erreur SQL pour les applications de client de base de données SQL : base de données d’erreur de connexion et d’autres problèmes](sql-database-develop-error-messages.md) | Obtenir des informations sur les codes d’erreur SQL pour les applications clientes de la base de données SQL, comme les erreurs courantes de connexion de base de données, les problèmes de copie de base de données et erreurs générales.  |
| 31 | [Se connecter à la base de données de SQL à l’aide de PHP sous Windows](sql-database-develop-php-simple.md) | Présente un exemple de programme PHP qui se connecte à la base de données de SQL Azure à partir d’un client Windows et fournit des liens vers les composants logiciels nécessaires requises par le client. |
| 32 | [Essayez de la base de données SQL : Utiliser C# pour créer une base de données SQL avec la bibliothèque de base de données SQL pour .NET](sql-database-get-started-csharp.md) | Essayez de la base de données SQL pour le développement d’applications SQL et C# et créer une base de données de SQL Azure avec C# à l’aide de la bibliothèque de base de données SQL pour .NET. |
| 33 | [Mise en route avec les fonctionnalités JSON dans la base de données de SQL Azure](sql-database-json-features.md) | Une base de données SQL Azure permet d’analyse, des requêtes et des données au format de notation de Notation JSON (JavaScript Object). |
| 34 | [Résoudre les problèmes de connexion à la base de données de SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Étapes pour identifier et résoudre des erreurs courantes de connexion pour la base de données de SQL Azure. |
| 35 | [Erreur « Base de données serveur n’est pas disponible actuellement » lors de la connexion à la base de données sql](sql-database-troubleshoot-connection.md) | Résoudre les problèmes de la base de données sur serveur n’est pas actuellement disponible erreur lorsqu’une application se connecte à la base de données de SQL. |



## <a name="develop"></a>Développer

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 36 | [Obtenir les valeurs requises pour l’authentification d’une application à accéder de la base de données SQL à partir du code](sql-database-client-id-keys.md) | Créez un principal de service pour accéder à la base de données SQL à partir du code. |
| 37 | [Se connecter à la base de données de SQL avec JDBC sur Windows à l’aide de Java](sql-database-develop-java-simple.md) | Présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données de SQL Azure. L’exemple utilise JDBC, et il s’exécute sur un ordinateur client Windows. |
| 38 | [Se connecter à la base de données de SQL à l’aide de Node.js](sql-database-develop-nodejs-simple.md) | Présente un exemple de code Node.js que vous pouvez utiliser pour vous connecter à la base de données de SQL Azure. |
| 39 | [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md) | Obtenir des informations sur les bibliothèques de connectivité disponibles et les meilleures pratiques pour les applications se connectant à la base de données de SQL. |
| 40 | [Se connecter à la base de données de SQL en utilisant les Python](sql-database-develop-python-simple.md) | Présente un exemple de code Python que vous pouvez utiliser pour vous connecter à la base de données de SQL Azure. |
| 41 | [Se connecter à la base de données de SQL à l’aide de Ruby](sql-database-develop-ruby-simple.md) | Donnez un exemple de code Ruby que vous pouvez exécuter pour vous connecter à la base de données de SQL Azure. |
| 42 | [Mise en route avec les Tables temporelles dans la base de données SQL Azure](sql-database-temporal-tables.md) | Découvrez comment mettre en route à l’aide de Tables temporelles dans la base de données de SQL Azure. |



## <a name="performance-and-scale"></a>Performances et évolutivité

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 43 | [Gestionnaire de base de données SQL](sql-database-advisor.md) | Le Gestionnaire de base de données SQL Azure fournit des recommandations pour les bases de données SQL existantes qui peuvent améliorer les performances de la requête en cours. |
| 44 | [Gestionnaire de base de données SQL via le portail Azure](sql-database-advisor-portal.md) | Le Gestionnaire de base de données SQL Azure dans Azure portal vous permet de consulter et de mettre en œuvre des recommandations pour les bases de données SQL existantes qui peuvent améliorer les performances de la requête en cours. |
| 45 | [Vue d’ensemble du point de référence de base de données SQL Azure](sql-database-benchmark-overview.md) | Cette rubrique décrit la référence de base de données SQL Azure utilisé pour mesurer les performances de la base de données de SQL Azure. |
| 46 | [Quand un pool élastique de la base de données doit être utilisé ?](sql-database-elastic-pool-guidance.md) | Un pool d’élasticité de la base de données est une collection de ressources disponibles qui sont partagées par un groupe de bases de données élastiques. Ce document fournit des conseils pour vous aider à évaluer la pertinence de l’utilisation d’un pool élastique de la base de données pour un groupe de bases de données. |
| 47 | [Mise en route avec les outils de base de données élastique](sql-database-elastic-scale-get-started.md) | Principes fondamentaux de la fonctionnalité des outils élastique de la base de données de base de données SQL Azure, y compris facile pour exécuter l’exemple d’application. |
| 48 | [Base de données SQL Forum aux questions](sql-database-faq.md) | Réponses aux clients de questions courantes demandez nuage de bases de données et base de données de SQL Azure, système de gestion de base de données relationnelle (SGBDR) de Microsoft et de base de données en tant que service dans le nuage. |
| 49 | [Mise en route dans la mémoire (aperçu) de la base de données SQL](sql-database-in-memory.md) | Technologies de mémoire dans SQL améliorent considérablement les performances de transactionnelle et les charges de travail analytique. Apprenez à tirer parti de ces technologies. |
| 50 | [Utilisation en mémoire OLTP (aperçu) pour améliorer les performances de votre application de base de données de SQL](sql-database-in-memory-oltp-migration.md) | Adopter les OLTP en mémoire afin d’améliorer les performances transactionnelles dans une base de données SQL existante. |
| 51 | [Stockage en mémoire OLTP de moniteur](sql-database-in-memory-oltp-monitoring.md) | Estimation et moniteur de stockage en mémoire XTP utilisent, capacité ; résoudre l’erreur de capacité 41823 |
| 52 | [Fonctionnement de la banque de requête de base de données SQL Azure](sql-database-operate-query-store.md) | Découvrez comment utiliser le magasin de requête de base de données de SQL Azure |
| 53 | [Analyse de performances de base de données SQL](sql-database-performance.md) | La base de données de SQL Azure propose des outils de performance pour vous aider à identifier les zones qui peuvent améliorer les performances de la requête en cours. |
| 54 | [Base de données de SQL Azure et de performances pour les bases de données unique](sql-database-performance-guidance.md) | Cet article peut vous aider à déterminer le niveau de service à choisir pour votre application. Il recommande également des solutions d’optimisation de votre application pour tirer le maximum de la base de données de SQL Azure. |
| 55 | [Aperçu de performances de requête de base de données SQL Azure](sql-database-query-performance.md) | Analyse des performances de requête d’identifie la plupart des requêtes d’utilisation du processeur pour une base de données de SQL Azure. |
| 56 | [Modifier le niveau et les performances niveau de service (couche de tarification) d’une base de données SQL](sql-database-scale-up.md) | Modifier le niveau de service et le niveau de performances d’une base de données Azure SQL indique comment faire évoluer votre base de données SQL vers le haut ou vers le bas. Modification du niveau de tarification d’une base de données SQL d’Azure. |
| 57 | [Surveillance des performances de base de données dans la base de données de SQL Azure](sql-database-single-database-monitor.md) | Obtenir des informations sur les options d’analyse de votre base de données avec des outils Azure et les vues de gestion dynamiques. |
| 58 | [Conseils de réglage de performances de la base de données SQL](sql-database-troubleshoot-performance.md) | Conseils pour l’optimisation dans la base de données de SQL Azure, grâce à l’évaluation et l’amélioration des performances. |
| 59 | [Comment faire pour utiliser le traitement par lots pour améliorer les performances de l’application de base de données SQL](sql-database-use-batching-to-improve-performance.md) | La rubrique fournit la preuve que le traitement par lots des opérations de base de données fortement imroves la vitesse et l’évolutivité de vos applications de base de données de SQL Azure. Bien que ces techniques de traitement par lots fonctionnent pour les bases de données SQL Server, le focus de l’article est sur Azure. |



## <a name="tools-for-scaling-out"></a>Outils pour la mise à l’échelle

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 60 | [Concevoir des modèles pour les applications SaaS partagées et la base de données de SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) | Cet article décrit la configuration requise et les modèles d’architecture de données commun partagé de base de données ont besoin des applications s’exécutant dans un environnement de cloud à prendre en compte et les compromis différentes associées à ces modèles. Il explique également comment base de données de SQL Azure, avec ses pools élastiques et les outils élastiques, aider à répondre à ces exigences de manière sans compromis. |
| 61 | [Migration des bases de données existantes à l’horizontale](sql-database-elastic-convert-to-use-elastic-tools.md) | Convertir des bases de données sharded pour utiliser des outils d’élastique de la base de données en créant un partagé Gestionnaire de mappage |
| 62 | [Création de bases de données évolutif de nuage](sql-database-elastic-database-client-library.md) | Créez des applications de base de données évolutives .NET avec la bibliothèque de client de base de données élastique |
| 63 | [Compteurs de performance pour éclater les Gestionnaire de cartes](sql-database-elastic-database-perf-counters.md) | ShardMapManager classe et données dépendantes routage des compteurs de performance |
| 64 | [Ajouter un partagé à l’aide des outils de base de données élastique](sql-database-elastic-scale-add-a-shard.md) | Comment utiliser les API d’échelle élastique pour ajouter de nouveau milieu des fragments à un partagé défini. |
| 65 | [Déployer un service fusion et fractionnement](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | Le fractionnement et la fusion avec les outils de base de données élastique |
| 66 | [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) | L’utilisation de la classe ShardMapManager pour une fonctionnalité de bases de données élastiques pour la base de données de SQL Azure, le routage dépendant des données dans les applications .NET |
| 67 | [Forum aux questions sur des outils de base de données élastique](sql-database-elastic-scale-faq.md) | Questions fréquemment posées à propos de l’évolutivité élastique de base de données SQL Azure. |
| 68 | [Glossaire d’outils de base de données élastique](sql-database-elastic-scale-glossary.md) | Explication des termes utilisés pour les outils de base de données élastique |
| 69 | [Mise à l’échelle avec la base de données de SQL Azure](sql-database-elastic-scale-introduction.md) | Logiciel en tant que développeurs d’un Service (SaaS) peut facilement créer élastiques et évolutives des bases de données dans le cloud à l’aide de ces outils |
| 70 | [Informations d’identification utilisées pour accéder à la bibliothèque de client de base de données élastique](sql-database-elastic-scale-manage-credentials.md) | Comment faire pour définir le niveau d’informations d’identification, l’administrateur en lecture seule, pour les applications de base de données élastique |
| 71 | [Interrogation d’éclater multiples](sql-database-elastic-scale-multishard-querying.md) | Exécuter des requêtes sur le milieu des fragments à l’aide de la bibliothèque de client élastique de la base de données. |
| 72 | [Déplacement de données entre les bases de données à grande échelle cloud](sql-database-elastic-scale-overview-split-and-merge.md) | Explique comment manipuler le milieu des fragments et déplacer les données via un service auto-hébergé, à l’aide des API de bases de données élastique. |
| 73 | [Évolution horizontale des bases de données avec le Gestionnaire de mappage partagé](sql-database-elastic-scale-shard-map-management.md) | L’utilisation de la ShardMapManager, la bibliothèque de client de base de données élastique |
| 74 | [Configuration de la sécurité de la fusion et fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md) | Configurer x409 certificats pour le chiffrement |
| 75 | [Mise à niveau d’une application pour utiliser la bibliothèque de client plus récente élastique de la base de données](sql-database-elastic-scale-upgrade-client-library.md) | Mise à niveau des applications et des bibliothèques à l’aide de Nuget |
| 76 | [Bibliothèque de client de base de données élastique avec Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Utilisez la bibliothèque de client de base de données élastique et Entity Framework pour le codage des bases de données |
| 77 | [À l’aide de la bibliothèque de client de base de données élastique avec Dapper](sql-database-elastic-scale-working-with-dapper.md) | À l’aide de bibliothèque client de base de données élastique avec Dapper. |
| 78 | [Applications mutualisées avec les outils de base de données élastique et la sécurité de niveau ligne](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Apprenez à utiliser les outils d’élastique de la base de données avec la sécurité de niveau ligne pour générer une application avec une couche de données hautement évolutif sur la base de données SQL Azure qui prend en charge le milieu des fragments mutualisées. |



## <a name="elastic-jobs"></a>Travaux élastiques

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 79 | [Créer et gérer la mise à l’échelle des bases de données SQL Azure en utilisant des traitements élastiques (aperçu)](sql-database-elastic-jobs-create-and-manage.md) | Passez en revue la création et la gestion d’un travail de l’élasticité de la base de données. |
| 80 | [Mise en route avec les tâches de base de données élastique](sql-database-elastic-jobs-getting-started.md) | comment utiliser les tâches de base de données élastique |
| 81 | [Gestion des bases de données à grande échelle cloud](sql-database-elastic-jobs-overview.md) | Illustre le service de travail élastique de la base de données |
| 82 | [Créer et gérer un travaux d’élastique de la base de données de la base de données SQL à l’aide de PowerShell (aperçu)](sql-database-elastic-jobs-powershell.md) | PowerShell est utilisé pour gérer les pools de la base de données de SQL Azure |
| 83 | [Présentation de travaux élastique de la base de données de l’installation](sql-database-elastic-jobs-service-installation.md) | Passez en revue l’installation de la fonctionnalité de travail élastique. |
| 84 | [Désinstaller les composants de tâches élastique de la base de données](sql-database-elastic-jobs-uninstall.md) | Comment faire pour désinstaller l’outil tâches élastique de la base de données |



## <a name="elastic-pools"></a>Pools élastiques

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 85 | [Quel est un pool élastique Azure ?](sql-database-elastic-pool.md) | Gérer des centaines ou des milliers de bases de données à l’aide d’un pool. Un seul prix pour un ensemble d’unités de performances peut être distribué sur le pool. Déplacer de seront des bases de données d’entrée ou de sortie. |
| 86 | [Créer un pool de la base de données élastique avec C#](sql-database-elastic-pool-create-csharp.md) | Utilisez les techniques de développement C# de base de données pour créer un pool élastique évolutive de la base de données dans la base de données de SQL Azure, vous pouvez partager des ressources entre plusieurs bases de données. |
| 87 | [Créer un nouveau pool de la base de données élastique avec PowerShell](sql-database-elastic-pool-create-powershell.md) | Découvrez comment utiliser PowerShell pour la base de données de SQL Azure cloisonnée ressources en créant un pool élastique évolutive de la base de données pour gérer plusieurs bases de données. |
| 88 | [Script PowerShell pour identifier les bases de données appropriées pour un pool d’élasticité de la base de données](sql-database-elastic-pool-database-assessment-powershell.md) | Un pool d’élasticité de la base de données est une collection de ressources disponibles qui sont partagées par un groupe de bases de données élastiques. Ce document fournit un script Powershell pour vous aider à évaluer la pertinence de l’utilisation d’un pool élastique de la base de données pour un groupe de bases de données. |
| 89 | [Surveiller et de gérer un pool de la base de données élastique avec C#](sql-database-elastic-pool-manage-csharp.md) | Techniques de développement C# de base de données permet de gérer un pool élastique de la base de données de base de données de SQL Azure. |
| 90 | [Surveiller et de gérer un pool élastique de la base de données avec le portail Azure](sql-database-elastic-pool-manage-portal.md) | Apprenez à utiliser le portail Azure et une intelligence intégrée de SQL base de données pour gérer, surveiller et bonne taille un pool élastique évolutive de la base de données pour optimiser les performances de la base de données et de gérer les coûts. |
| 91 | [Surveiller et de gérer un pool de la base de données élastique avec PowerShell](sql-database-elastic-pool-manage-powershell.md) | Découvrez comment utiliser PowerShell pour gérer un pool élastique de la base de données. |
| 92 | [Surveiller et de gérer un pool de la base de données élastique avec Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | T-SQL permet de créer une base de données Azure SQL dans un pool élastique. Ou permet de pénétrer dans la datbase pools de T-SQL. |
| 93 | [Facturation de pool élastique de la base de données et les informations de tarification](sql-database-elastic-pool-price.md) | Informations de tarification spécifiques pour les pools élastique de la base de données. |



## <a name="elastic-query"></a>Requête élastique

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 94 | [Dans d’autres bases de données à grande échelle cloud (aperçu)](sql-database-elastic-query-getting-started.md) | comment utiliser les requêtes de base de données de base de données |
| 95 | [Mise en route avec des requêtes de bases de données croisées (partitionnement vertical) (aperçu)](sql-database-elastic-query-getting-started-vertical.md) | Comment faire pour utiliser une requête élastique avec les bases de données partitionnées verticalement |
| 96 | [Reporting sur les bases de données à grande échelle cloud (aperçu)](sql-database-elastic-query-horizontal-partitioning.md) | le paramétrage des requêtes élastiques sur les partitions horizontales |
| 97 | [Azure présentation de requête élastique de la base de données de la base de données SQL (aperçu)](sql-database-elastic-query-overview.md) | Vue d’ensemble de la fonctionnalité de requête élastique |
| 98 | [Exécuter des requêtes dans des bases de données de nuage avec différents schémas (aperçu)](sql-database-elastic-query-vertical-partitioning.md) | le paramétrage des requêtes de bases de données croisées sur les partitions verticales |



## <a name="elastic-transaction"></a>Transaction élastique

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 99 | [Transactions distribuées sur plusieurs bases de données de nuage](sql-database-elastic-transactions-overview.md) | Vue d’ensemble des Transactions élastique de la base de données de la base de données SQL Azure |



## <a name="backup-and-recovery"></a>Sauvegarde et récupération

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 100 | [Sauvegardes de base de données SQL](sql-database-automated-backups.md) | En savoir plus sur SQL de la base de données intégrée des sauvegardes de base de données qui vous permettent de restaurer une base de données de SQL Azure en différé à un point antérieur dans le temps ou copier une base de données pour une nouvelle base de données dans une région géographique (35 jours). |
| 101 | [Vue d’ensemble de la continuité d’activité avec une base de données de SQL Azure](sql-database-business-continuity.md) | Découvrez comment prend en charge la base de données de SQL Azure cloud une continuité d’activité et de récupération de la base de données et vous permet de conserve les applications en nuage critiques en cours d’exécution. |
| 102 | [Comment faire pour restaurer une table unique à partir d’une sauvegarde de base de données de SQL Azure](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Découvrez comment restaurer la table unique à partir de la sauvegarde de la base de données de SQL Azure. |
| 103 | [Conception d’une application de reprise après sinistre de cloud à l’aide de géo-réplication Active dans la base de données de SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Apprenez à concevoir des solutions de reprise après sinistre de nuage pour la planification de la continuité d’activité commerciale à l’aide de la réplication géographique pour la sauvegarde de données d’application avec la base de données de SQL Azure. |
| 104 | [Restaurer une base de données de SQL Azure ou le basculement sur incident sur un site secondaire](sql-database-disaster-recovery.md) | Découvrez comment restaurer une base de données à partir d’un centre de données régional ou panne Azure SQL de base de données Active géo-réplication, possibilités d’et géo-restauration. |
| 105 | [Exécution de perçage de récupération après sinistre](sql-database-disaster-recovery-drills.md) | Découvrez les conseils et meilleures pratiques pour l’utilisation de la base de données de SQL Azure pour effectuer les exercices de récupération d’urgence qui vous aide à maintenir votre mission critique les applications professionnelles résistants aux pannes et des interruptions de service. |
| 106 | [Stratégies de récupération après sinistre pour les applications utilisant le Pool élastique de base de données SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Apprenez à concevoir votre solution de cloud de reprise après sinistre en choisissant le modèle de basculement de droite. |
| 107 | [À l’aide de la classe RecoveryManager pour résoudre les problèmes de mappage partagé](sql-database-elastic-database-recovery-manager.md) | La classe RecoveryManager permet de résoudre les problèmes avec les cartes partagé |
| 108 | [Importer un fichier de type sac à DOS pour créer une base de données SQL d’Azure](sql-database-import.md) | Créer une base de données Azure SQL en important un fichier de type sac à DOS existant. |
| 109 | [Restauration d’une base de données de SQL Azure à un point antérieur dans le temps avec le portail Azure](sql-database-point-in-time-restore-portal.md) | Restaurer une base de données de SQL Azure à un point antérieur dans le temps. |
| 110 | [Restauration d’une base de données de SQL Azure à un point antérieur dans le temps avec PowerShell](sql-database-point-in-time-restore-powershell.md) | Restaurer une base de données de SQL Azure à un point antérieur dans le temps |
| 112 | [Restaurer une base de données Azure SQL à l’aide de sauvegardes de base de données automatisée](sql-database-recovery-using-backups.md) | Obtenir des informations sur la restauration de Point-à-temps, ce qui vous permet de restaurer une base de données de SQL Azure à un point antérieur dans le temps (jusqu'à 35 jours). |
| 113 | [Restaurer une base de données Azure SQL supprimé, l’utilisation du portail Azure](sql-database-restore-deleted-database-portal.md) | Restaurer une base de données Azure SQL supprimé (Azure Portal). |
| 114 | [Restaurer une base de données de SQL Azure supprimés à l’aide de PowerShell](sql-database-restore-deleted-database-powershell.md) | Restaurer une base de données SQL Azure supprimés (PowerShell). |
| 115 | [Restaurer une base de données à un point antérieur dans le temps, restauration d’une base de données supprimé ou récupérer d’une panne du centre de données](sql-database-troubleshoot-backup-and-restore.md) | Découvrez comment restaurer une base de données à partir des erreurs et des pannes à l’aide des réplicas et des sauvegardes de base de données de SQL Azure. |



## <a name="migrate"></a>Effectuer la migration

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 116 | [Exportation d’une base de données SQL Server vers un fichier de type sac à DOS à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Base de données de SQL Microsoft Azure, migration de base de données, exporter la base de données, exporter un fichier de type sac à DOS, sqlpackage |
| 117 | [Exportation d’une base de données SQL Server vers un fichier de type sac à DOS à l’aide de SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Base de données de SQL Microsoft Azure, migration de base de données, exporter la base de données, exporter un fichier de type sac à DOS, Assistant Exporter une Application de couche données |
| 118 | [Importer dans la base de données de SQL à partir d’un fichier de type sac à DOS à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Base de données de SQL Microsoft Azure, migration de base de données, Importer base de données, importer un fichier de type sac à DOS, sqlpackage |
| 119 | [Importer à partir de type sac à DOS à l’aide de SSMS de la base de données SQL](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Base de données SQL Azure de Microsoft, déploiement de base de données, migration de base de données, importation de base de données, base de données de l’exportation, l’Assistant migration |
| 120 | [Migration de la base de données SQL Server pour la base de données SQL à l’aide de déployer la base de données de l’Assistant base de données Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Base de données de SQL Microsoft Azure, migration de base de données, l’Assistant base de données Microsoft Azure |
| 121 | [Migration de la base de données SQL Server pour la base de données de SQL Azure à l’aide de la réplication transactionnelle](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Base de données de SQL Microsoft Azure, migration de base de données, Importer base de données, la réplication transactionnelle |
| 122 | [Déterminer la compatibilité de la base de données SQL à l’aide de SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Microsoft Azure SQL base de données, migration de base de données, la compatibilité de la base de données SQL, SqlPackage |
| 123 | [Utilisez SQL Server Management Studio, à la compatibilité de déterminer la base de données SQL avant la migration de la base de données de SQL Azure](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Microsoft Azure SQL base de données, migration de base de données, la compatibilité de la base de données SQL, Assistant Exportation d ' Application de couche données |
| 124 | [Utiliser l’Assistant de Migration de SQL Azure pour des problèmes de compatibilité de base de données de SQL Server corriger avant la migration de la base de données de SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md) | Microsoft Azure SQL base de données, migration de base de données, la compatibilité, l’Assistant de Migration SQL Azure |
| 125 | [Migrer une base de données de SQL Server à l’aide des outils de données de SQL Server pour Visual Studio de la base de données SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Microsoft Azure SQL base de données, migration de base de données, la compatibilité, l’Assistant de Migration SQL Azure, SSDT |
| 126 | [Résoudre les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration vers SQL de base de données](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Microsoft Azure SQL base de données, migration de base de données, la compatibilité, l’Assistant de Migration SQL Azure |
| 127 | [Archiver une base de données Azure SQL dans un fichier de type sac à DOS à l’aide de PowerShell](sql-database-export-powershell.md) | Archiver une base de données Azure SQL dans un fichier de type sac à DOS à l’aide de PowerShell |
| 128 | [Importer un fichier de type sac à DOS pour créer une base de données Azure SQL à l’aide de PowerShell](sql-database-import-powershell.md) | Importer un fichier de type sac à DOS pour créer une base de données Azure SQL à l’aide de PowerShell |
| 129 | [Charge les données CSV dans l’entrepôt de données SQL Azure (fichiers plats)](sql-database-load-from-csv-with-bcp.md) | Pour une taille de données de petite taille, utilise bcp pour importer des données dans la base de données de SQL Azure. |
| 130 | [Déplacer les bases de données entre serveurs, entre les abonnements et Azure](sql-database-troubleshoot-moving-data.md) | Étapes à suivre pour copier, déplacer et migrer les données et les bases de données dans la base de données de SQL Azure. |



## <a name="move-data-in-and-out"></a>Déplacer les données d’entrée et de sortie

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 131 | [Migration de base de données SQL Server pour la base de données SQL dans le nuage](sql-database-cloud-migrate.md) | Découvrez est-il local SQL Server migration base de données à la base de données de SQL Azure dans le nuage. Outils de migration de base de données permet de tester la compatibilité avant la migration de la base de données. |
| 132 | [Copier une base de données SQL Azure](sql-database-copy.md) | Créer une copie d’une base de données SQL d’Azure |
| 133 | [Archiver une base de données Azure SQL dans un fichier de type sac à DOS à l’aide du portail Azure](sql-database-export.md) | Archiver une base de données Azure SQL dans un fichier de type sac à DOS à l’aide du portail Azure |



## <a name="security"></a>Sécurité

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 134 | [Connexion de base de données SQL ou SQL Data Warehouse à l’aide de l’authentification Azure Active Directory](sql-database-aad-authentication.md) | Découvrez comment se connecter à la base de données de SQL à l’aide de l’authentification Active Directory Azure. |
| 135 | [Toujours cryptées : Protéger les données sensibles dans la base de données de SQL et de stocker vos clés de chiffrement dans le magasin de certificats Windows](sql-database-always-encrypted.md) | Protéger les données sensibles de votre base de données SQL en quelques minutes. |
| 136 | [Toujours cryptées : Protéger les données sensibles dans la base de données de SQL et de stocker vos clés de chiffrement dans Azure clé coffre-fort](sql-database-always-encrypted-azure-key-vault.md) | Protéger les données sensibles de votre base de données SQL en quelques minutes. |
| 137 | [Base de données SQL - prise en charge des clients de bas niveau et les modifications de point de terminaison IP pour l’audit](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | En savoir plus sur IP et la prise en charge des clients de niveau inférieur de la base de données SQL des modifications de point de terminaison pour l’audit. |
| 138 | [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL d’Azure. |
| 139 | [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL d’Azure. |
| 140 | [Configurer des règles de pare-feu au niveau du serveur et au niveau de la base de données de base de données de SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL d’Azure. |
| 141 | [Mise en route de SQL de base de données données masque dynamique (Azure Portal)](sql-database-dynamic-data-masking-get-started.md) | La mise en route avec SQL de base de données dynamique données masquage dans le portail Azure |
| 142 | [Mise en route de SQL de base de données données masque dynamique (Azure classique Portal)](sql-database-dynamic-data-masking-get-started-portal.md) | La mise en route avec SQL de base de données dynamique données masquage dans le portail classique Azure |
| 143 | [Configurer les règles de pare-feu de base de données de SQL Azure \- vue d’ensemble](sql-database-firewall-configure.md) | Apprenez à configurer un pare-feu de base de données SQL avec des règles de pare-feu au niveau du serveur et au niveau de la base de données pour gérer l’accès. |
| 144 | [Didacticiel de la base de données de SQL : créez des comptes d’utilisateurs pour accéder et gérer une base de données de la base de données SQL](sql-database-get-started-security.md) | Apprenez à créer des comptes d’utilisateurs pour accéder et gérer une base de données. |
| 145 | [SQL, base de données d’authentification et d’autorisation : autorisation de l’accès](sql-database-manage-logins.md) | En savoir plus sur la gestion de la sécurité de la base de données SQL, en particulier comment gérer la sécurité de l’accès et la connexion de base de données via le compte principal au niveau du serveur. |
| 146 | [Limitations et les consignes de sécurité de la base de données SQL azure](sql-database-security-guidelines.md) | Obtenir des informations sur les directives de la base de données SQL de Microsoft Azure et des limitations liées à la sécurité. |
| 147 | [Mise en route de la détection des menaces de base de données SQL](sql-database-threat-detection-get-started.md) | La mise en route avec la détection des menaces de base de données SQL dans le portail Azure |
| 148 | [Comment effectuer des tâches administratives telles que la réinitialisation de mot de passe de l’administrateur de base de données de SQL Azure](sql-database-troubleshoot-permissions.md) | Décrit comment effectuer des tâches administratives dans une base de données de SQL. Par exemple, la réinitialisation de mot de passe administrateur, l’octroi et de retrait de l’accès. |



## <a name="manage-your-database"></a>Gérer votre base de données

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 149 | [Mise en route de l’audit de base de données SQL](sql-database-auditing-get-started.md) | Mise en route de l’audit de base de données SQL |
| 150 | [Configurer une règle de pare-feu au niveau du serveur de base de données de SQL Azure en utilisant le portail Azure](sql-database-configure-firewall-settings.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent Azure SQL server. |
| 151 | [Copier une base de données de SQL Azure en utilisant le portail Azure](sql-database-copy-portal.md) | Créer une copie d’une base de données SQL d’Azure |
| 152 | [Gestion des bases de données SQL Azure à l’aide d’Automation d’Azure](sql-database-manage-automation.md) | Découvrez comment le service Azure Automation permet de gérer des bases de données SQL d’Azure à grande échelle. |
| 153 | [La vue d’ensemble : outils de gestion pour une base de données de SQL](sql-database-manage-overview.md) | Compare les outils et options de gestion de base de données de SQL Azure |
| 154 | [Surveillance de base de données SQL Azure à l’aide des vues de gestion dynamique](sql-database-monitoring-with-dmvs.md) | Apprenez à détecter et diagnostiquer les problèmes de performances courants à l’aide des vues de gestion dynamique pour analyser la base de données SQL de Microsoft Azure. |
| 155 | [Sécurisation de votre base de données SQL](sql-database-security.md) | En savoir plus sur la sécurité de base de données de SQL Azure et SQL Server, notamment les différences entre le nuage et SQL Server locaux lorsqu’il s’agit de l’authentification, autorisation, sécurité de la connexion, le cryptage et la conformité. |



## <a name="extended-events"></a>Événements étendus

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 156 | [Code de cible de fichier événement pour les événements étendus dans la base de données de SQL](sql-database-xevent-code-event-file.md) | Pour obtenir un exemple de code en deux phases qui montre la cible du fichier de l’événement dans un événement étendue sur la base de données de SQL Azure, fournit PowerShell et Transact-SQL. Le stockage Azure est une partie obligatoire de ce scénario. |
| 157 | [Anneau de code cible de mémoire tampon d’événements étendues dans la base de données de SQL](sql-database-xevent-code-ring-buffer.md) | Fournit un exemple de code Transact-SQL qui est facile et rapide à l’aide de la cible de la mémoire tampon d’anneau, dans la base de données de SQL Azure. |
| 158 | [Événements étendus dans SQL de base de données](sql-database-xevent-db-diff-from-svr.md) | Décrit les événements étendus (XEvents) dans la base de données de SQL Azure, et la session d’événements diffère légèrement de sessions d’événements dans Microsoft SQL Server. |



## <a name="geo-replication"></a>Réplication de la zone géographique

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 159 | [Initier un basculement planifié ou de base de données de SQL Azure avec le portail Azure](sql-database-geo-replication-failover-portal.md) | Initier un basculement planifié ou de base de données de SQL Azure en utilisant le portail Azure |
| 160 | [Initier un basculement planifié ou de base de données de SQL Azure avec PowerShell](sql-database-geo-replication-failover-powershell.md) | Initier un basculement planifié ou de base de données de SQL Azure à l’aide de PowerShell |
| 161 | [Initier un basculement planifié ou de base de données de SQL Azure avec Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Initier un basculement planifié ou de base de données de SQL Azure à l’aide de Transact-SQL |
| 162 | [Vue d’ensemble : SQL de base de données Active géo-réplication](sql-database-geo-replication-overview.md) | Geo-réplication Active vous permet de configurer 4 réplicas de votre base de données dans un des centres de données Azure. |
| 163 | [Configurer géo-réplication de base de données de SQL Azure avec le portail Azure](sql-database-geo-replication-portal.md) | Configurer géo-réplication de base de données de SQL Azure en utilisant le portail Azure |
| 164 | [Configurer la géo-réplication de base de données SQL Azure avec PowerShell](sql-database-geo-replication-powershell.md) | Configurer géo-réplication Active pour la base de données de SQL Azure à l’aide de PowerShell |
| 165 | [Comment gérer la sécurité de la base de données de SQL Azure après la reprise après sinistre](sql-database-geo-replication-security-config.md) | Cette rubrique décrit les considérations de sécurité pour la gestion de la sécurité après une restauration de base de données ou un basculement sur incident. |
| 166 | [Configurer la géo-réplication de base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-transact-sql.md) | Configurer la réplication géographique pour SQL Azure de base de données à l’aide de Transact-SQL |
| 167 | [Geo-restaurer une base de données de SQL Azure depuis une sauvegarde redondante geo via le portail Azure](sql-database-geo-restore-portal.md) | Geo-restaurer une base de données de SQL Azure à partir d’une sauvegarde redondante geo (Azure Portal). |
| 168 | [Restaurer une base de données de SQL Azure à partir d’une sauvegarde geo redondant à l’aide de PowerShell](sql-database-geo-restore-powershell.md) | Restaurer une base de données de SQL Azure dans un nouveau serveur à partir d’une sauvegarde geo redondant |



## <a name="upgrade"></a>Mise à niveau

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 169 | [Amélioration des performances de la requête avec 130 du niveau de compatibilité de base de données de SQL Azure](sql-database-compatibility-level-query-performance-130.md) | Étapes et les outils permettant de déterminer le niveau de compatibilité est la mieux adapté à votre base de données sur la base de données de SQL Azure ou Microsoft SQL Server |
| 170 | [Gestion des mises à niveau des applications en nuage à l’aide de SQL Active géo-réplication](sql-database-manage-application-rolling-upgrade.md) | Découvrez comment utiliser géo-réplication de base de données de SQL Azure pour prendre en charge les mises à niveau en ligne de votre application en nuage. |
| 171 | [Mise à niveau V12 de base de données SQL Azure via le portail Azure](sql-database-upgrade-server-portal.md) | Explique comment mettre à niveau vers V12 de base de données SQL Azure, y compris la mise à niveau des bases de données Web et de l’entreprise et comment mettre à niveau un serveur V11 migrer ses bases de données directement dans un pool élastique de la base de données à l’aide du portail Azure. |
| 172 | [Mise à niveau V12 de base de données SQL Azure à l’aide de PowerShell](sql-database-upgrade-server-powershell.md) | Explique comment mettre à niveau vers V12 de base de données SQL Azure, y compris la mise à niveau des bases de données Web et de l’entreprise et comment mettre à niveau un serveur V11 migrer ses bases de données directement dans un pool d’élasticité de la base de données à l’aide de PowerShell. |
| 173 | [Planifier et préparer la mise à niveau vers V12 de base de données SQL](sql-database-v12-plan-prepare-upgrade.md) | Décrit les préparations et les limitations de mise à niveau vers la version V12 d’Azure SQL de base de données. |
| 174 | [Quelles sont les nouveautés dans V12 de base de données SQL](sql-database-v12-whats-new.md) | Explique pourquoi des systèmes métier qui sont à l’aide de base de données de SQL Azure dans le nuage pourra bénéficier d’une mise à niveau vers la version V12. |
| 175 | [Forum aux questions du coucher de soleil Business Edition et Web](sql-database-web-business-sunset-faq.md) | Découvrez les bases de données Web de SQL Azure et de l’entreprise seront déclassés et en savoir plus sur les fonctionnalités des nouveaux niveaux de service. |



## <a name="tools"></a>Outils

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 176 | [Gérer la base de données SQL Azure avec PowerShell](sql-database-command-line-tools.md) | Gestion de la base de données SQL Azure avec PowerShell. |
| 177 | [Didacticiel de la base de données de SQL : Excel de se connecter à une base de données Azure SQL et créer un rapport](sql-database-connect-excel.md) | Découvrez comment Microsoft Excel de se connecter à la base de données SQL d’Azure dans le nuage. Importer des données dans Excel pour l’exploration de données et de rapports. |
| 178 | [Créer une base de données SQL et exécuter les tâches courantes de configuration de base de données avec les applets de commande PowerShell](sql-database-get-started-powershell.md) | Découvrez dès maintenant créer une base de données SQL avec PowerShell. Les tâches courantes de configuration de base de données peuvent être gérés via les applets de commande PowerShell. |
| 179 | [Mise en route de la synchronisation de données SQL Azure (aperçu)](sql-database-get-started-sql-data-sync.md) | Ce didacticiel vous aide à mettre en route la synchronisation de données SQL Azure (aperçu). |
| 180 | [Gestion de base de données SQL Azure à l’aide de SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Apprenez à utiliser SQL Server Management Studio pour gérer les bases de données et les serveurs de base de données SQL. |
| 181 | [Gestion des bases de données SQL Azure via le portail Azure](sql-database-manage-portal.md) | Apprenez à utiliser le portail Azure pour gérer une base de données relationnelle dans le nuage en utilisant le portail Azure. |
| 182 | [Modifier le niveau du service niveau et les performances (niveau de tarification) d’une base de données SQL avec PowerShell](sql-database-scale-up-powershell.md) | Modifier le niveau de service et le niveau de performances d’une base de données Azure SQL indique comment mettre à l’échelle votre base de données SQL vers le haut ou vers le bas avec PowerShell. Modification du niveau de tarification d’une base de données Azure SQL avec PowerShell. |
| 183 | [Utilisez SQL Server Management Studio dans Azure RemoteApp à se connecter à la base de données de SQL](sql-database-ssms-remoteapp.md) | Utilisez ce didacticiel pour apprendre à utiliser SQL Server Management Studio dans Azure RemoteApp pour la sécurité et les performances lors de la connexion à la base de données de SQL |



## <a name="reference"></a>Référence

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 184 | [Bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md) | Affiche le numéro de version minimale pour chaque pilote que les programmes clients peuvent utiliser pour se connecter à la base de données de SQL Azure, ou à Microsoft SQL Server. Un lien est fourni pour les informations de version des pilotes publiés par la Communauté et non par Microsoft. |
| 185 | [Limites des ressources de base de données SQL Azure](sql-database-resource-limits.md) | Cette page décrit certaines limites communes de la ressource de base de données de SQL Azure. |
| 186 | [Différences de Transact-SQL de base de données SQL Azure](sql-database-transact-sql-information.md) | Instructions Transact-SQL qui sont moins entièrement pris en charge dans la base de données de SQL Azure |



## <a name="miscellaneous"></a>Divers

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 187 | [Copier une base de données Azure SQL à l’aide de PowerShell](sql-database-copy-powershell.md) | Créer une copie d’une base de données Azure SQL à l’aide de PowerShell |
| 188 | [Copier une base de données Azure SQL à l’aide de Transact-SQL](sql-database-copy-transact-sql.md) | Créer une copie d’une base de données Azure SQL à l’aide de Transact-SQL |
| 189 | [Les instructions et les restrictions générales de la base de données SQL Azure](sql-database-general-limitations.md) | Cette page décrit certaines limites générales de la base de données de SQL Azure, ainsi que des zones de l’interopérabilité et la prise en charge. |
| 190 | [Recommandations de niveau de prix de base de données SQL](sql-database-service-tier-advisor.md) | Lors de la modification de tarification niveaux dans le portail Azure, recommandations de niveau de tarification est fournis recommander le niveau qui correspond le mieux adapté à la charge de travail un existant Azure SQL de base de données en cours d’exécution. Niveaux de tarification décrire le niveau de performances et le niveau de service d’une base de données SQL. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>Extras

<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>Articles connexes à partir d’autres services Azure

- [Sauvegarder votre application de Services d’application Azure dans Azure](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>Outils de documentation

- [Mises à jour annoncées pour la base de données de SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

- [Rechercher tous les types de documentation de Microsoft Azure, avec des filtres](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>Graphique du chemin d’accès de la formation

- [Graphique de chemin d’accès de formation pour tous les services Microsoft Azure](http://azure.microsoft.com/documentation/learning-paths/)

- [Cursus : Obtenir des informations de base de données SQL Azure](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [Cursus : Évolutivité élastique de la base de données SQL](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->


