<properties
   pageTitle="Transactions distribuées sur plusieurs bases de données de nuage"
   description="Vue d’ensemble des Transactions élastique de la base de données de la base de données SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="torsteng"
   manager="jhubbard"
   editor="torsteng"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="sql-database"
   ms.date="05/27/2016"
   ms.author="torsteng"/>

# <a name="distributed-transactions-across-cloud-databases"></a>Transactions distribuées sur plusieurs bases de données de nuage

Transactions élastique de la base de données pour SQL Azure (base de données SQL) de base de données vous permettent d’exécuter des transactions qui couvrent plusieurs bases de données dans la base de données SQL. Transactions élastique de la base de données pour la base de données SQL sont disponibles pour les applications .NET à l’aide de ADO .NET et intègrent l’expérience de programmation familier en utilisant les classes [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) . Pour obtenir la bibliothèque, consultez [.NET Framework 4.6.1 (programme d’installation Web)](https://www.microsoft.com/download/details.aspx?id=49981).

Dans les locaux, un tel scénario généralement requis exécutant Microsoft Distributed Transaction Coordinator (MSDTC). MSDTC n’étant pas disponible pour l’application de plate-forme en tant que Service dans Azure, la capacité à coordonner les transactions distribuées a maintenant été intégrée directement dans la base de données SQL. Les applications peuvent se connecter à une base de données SQL pour lancer des transactions distribuées, et une base de données en toute transparence coordonne les transactions distribuées, comme illustré dans la figure suivante. 

  ![Transactions distribuées avec SQL Azure de base de données à l’aide de transactions de base de données élastique ][1]

## <a name="common-scenarios"></a>Scénarios courants

Transactions élastique de la base de données pour la base de données SQL permettent aux applications d’apporter des modifications atomiques aux données stockées dans plusieurs bases de données SQL différent. L’aperçu se concentre sur les expériences de développement côté client dans C# et .NET. Une expérience côté serveur à l’aide de T-SQL est prévue pour une date ultérieure.  
Transactions de base de données élastique cible les scénarios suivants :

* Applications de bases de données multiples dans Azure : avec ce scénario, les données sont partitionnées verticalement sur plusieurs bases de données dans la base de données SQL tels que les différents types de données résident sur différentes bases de données. Certaines opérations requièrent des modifications apportées aux données qui sont conservées dans deux ou plusieurs bases de données. L’application utilise des transactions de base de données élastique pour coordonner les modifications entre les bases de données et de garantir l’atomicité.

* Les applications de base de données sharded dans Azure : avec ce scénario, la couche de données utilise la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md) ou self-ont pour partitionner horizontalement les données entre plusieurs bases de données dans la base de données SQL. Un cas d’utilisation importante est nécessaire pour effectuer les modifications atomiques pour une application mutualisée sharded lorsque les modifications couvrent les locataires. Imaginez par exemple un transfert à partir d’un client à un autre, les deux résidant sur des bases de données différentes. Un second cas est ont affinées en fonction des besoins de capacité pour un client volumineux, qui à son tour généralement implique que certaines opérations atomiques doit s’étendre à travers plusieurs bases de données utilisées pour le même client. Un troisième cas est atomiques mises à jour des données qui sont répliquées dans les bases de données de référence. Opérations atomiques, traitées, le long de ces lignes peuvent maintenant être coordonnées entre plusieurs bases de données à l’aide de l’aperçu.
Transactions élastique de bases de données permet de garantir l’atomicité de la transaction sur les bases de données de validation à deux phases. Il coûte parfaitement pour les transactions qui impliquent des bases de données moins de 100 à la fois au sein d’une transaction unique. Ces limites ne sont pas appliquées, mais on doit s’attendre à performances et le taux de réussite de base élastique à se dégrader si le dépassement de ces limites.


## <a name="installation-and-migration"></a>Installation et migration

Les fonctionnalités de base élastique dans la base de données SQL sont fournies par le biais de mises à jour pour les bibliothèques .NET System.Data.dll et System.Transactions.dll. Les DLL de garantissent que cette validation à deux phases est utilisée si nécessaire pour garantir l’atomicité. Pour commencer à développer des applications à l’aide de transactions de base de données élastique, installez [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou une version ultérieure. Lors de l’exécution sur une version antérieure du .NET framework, les transactions ne pourra pas promouvoir en une transaction distribuée et une exception sera levée.

Après l’installation, vous pouvez utiliser l’API de transaction distribuée dans System.Transactions avec des connexions à la base de données SQL. Si vous avez des applications MSDTC existantes à l’aide de ces API, simplement de reconstruire vos applications existantes pour .NET 4.6 après avoir installé le 4.6.1 Framework. Si vos projets ciblent .NET 4.6, ils utiliseront automatiquement les DLL mis à jour à partir de la nouvelle version de .NET Framework et les appels API en combinaison avec les connexions à la base de données SQL de transaction distribuée maintenant réussira.

N’oubliez pas que les transactions de la base de données élastique ne nécessitent pas l’installation de MSDTC. Au lieu de cela, les transactions de la base de données élastique sont directement gérées par et au sein de la base de données SQL. Cela simplifie considérablement des scénarios de nuage dans la mesure où un déploiement de MSDTC n’est pas nécessaire d’utiliser les transactions distribuées avec la base de données SQL. Section 4 explique en détail comment déployer les transactions de la base de données élastique et .NET framework requis avec vos applications Azure cloud.

## <a name="development-experience"></a>Expérience de développement

### <a name="multi-database-applications"></a>Applications de bases de données multiples

L’exemple de code suivant utilise l’expérience de programmation familier avec System.Transactions de .NET. La classe TransactionScope établit une transaction ambiante dans .NET. (« Une transaction ambiante » est celui qui se trouve dans le thread actuel). Toutes les connexions ouvertes à l’intérieur de TransactionScope impliquée dans la transaction. Si la participation de différentes bases de données, la transaction est automatiquement élevée à une transaction distribuée. Le résultat de la transaction est contrôlé en définissant la portée pour indiquer une instruction commit.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Applications de base de données sharded
 
Transactions élastique de la base de données pour la base de données SQL prennent également en charge la coordination des transactions distribuées, dans lequel vous utilisez la méthode OpenConnectionForKey de la bibliothèque de client de base de données élastique pour ouvrir des connexions de données de mise à l’échelle des niveaux. Prendre en compte si vous avez besoin pour garantir la cohérence des modifications sur plusieurs valeurs clés ont différents. Connexions au milieu des fragments hébergeant les valeurs de clés ont différents sont demandées à l’aide de OpenConnectionForKey. Dans le cas général, les connexions peuvent être au milieu des fragments différentes telles que s’assurer des garanties transactionnelles nécessite une transaction distribuée. L’exemple de code suivant illustre cette approche. Il suppose qu’une variable appelée shardmap est utilisée pour représenter un mappage partagé à partir de la bibliothèque de client élastique de la base de données :

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }
        
        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Installation de .NET pour les Services en nuage Azure

Azure propose plusieurs offres d’héberger des applications .NET. Une comparaison des différentes offres est disponible dans le [Service d’application Azure, les Services en nuage et comparaison des Machines virtuelles](../app-service-web/choose-web-site-cloud-service-vm.md). Si la système d’exploitation invité de l’offre est plus petite que .NET 4.6.1 requis pour les transactions élastiques, vous devez mettre à niveau la système d’exploitation invité 4.6.1. 

Pour les Services d’application Azure, mises à niveau du système d’exploitation invité ne sont actuellement pas pris en charge. Pour les ordinateurs virtuels Azure, connectez-vous à l’ordinateur virtuel et exécuter le programme d’installation pour le .NET framework le plus récent. Pour les Services en nuage Azure, vous devez inclure l’installation d’une version plus récente de .NET dans les tâches de démarrage de votre déploiement. Les concepts et les étapes sont documentés dans [l’Installation de .NET dans un rôle de Service de Cloud](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Notez que le programme d’installation pour .NET 4.6.1 peut nécessiter plus de stockage temporaire pendant le processus de démarrage sur les services en nuage Azure que le programme d’installation de .NET 4.6. Pour garantir la réussite de l’installation, vous devez augmenter le stockage temporaire de votre service cloud Azure dans votre fichier ServiceDefinition.csdef dans la section LocalResources et les paramètres de l’environnement de votre tâche de démarrage, comme illustré dans l’exemple suivant :

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
    
## <a name="transactions-across-multiple-servers"></a>Transactions sur plusieurs serveurs

Transactions de base de données élastique sont pris en charge sur différents serveurs logiques dans la base de données de SQL Azure. Lorsque les transactions franchissent les limites de serveur logique, les serveurs participants tout d’abord être entrées dans une relation de communication mutuelle. Une fois la relation de communication a été établie, une base de données dans un des deux serveurs peut participer aux transactions élastiques avec les bases de données à partir de l’autre serveur. Avec les transactions réparties sur plus de deux serveurs logiques, une relation de communication doit être en place pour une paire de serveurs logiques.

Pour gérer les communications entre serveurs des relations pour les transactions de base de données élastique, utilisez les applets de commande PowerShell suivante :

* **Nouveau-AzureRmSqlServerCommunicationLink**: cette applet de commande permet de créer une nouvelle relation de communication entre deux serveurs logiques dans la base de données SQL Azure. La relation est symétrique, qui signifie que les deux serveurs peuvent initier des transactions avec l’autre serveur.
* **Get-AzureRmSqlServerCommunicationLink**: cette applet de commande permet d’extraire les relations existantes de communication et de leurs propriétés.
* **Supprimer-AzureRmSqlServerCommunicationLink**: cette applet de commande permet de supprimer une relation de communication existant. 


## <a name="monitoring-transaction-status"></a>Surveillance de l’état de la transaction

Utiliser des vues de gestion dynamique (DMV) dans la base de données SQL pour surveiller l’état et la progression de vos transactions élastique en continu de la base de données. Tous les DMV relatives aux transactions est pertinents pour les transactions distribuées dans la base de données SQL. Vous trouverez ici la liste correspondante de DMV : [Transaction connexes vues de gestion dynamique et fonctions (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).
 
Ces DMV est particulièrement utiles :

* **sys.dm\_tran\_active\_transactions**: répertorie les transactions en cours et leur état. La colonne de l’UOW (unité de travail) peut identifier les transactions de différents enfants qui appartiennent à la même transaction distribuée. Toutes les transactions dans la même transaction distribuée effectue la même valeur UOW. Consultez la [documentation de la DMV](https://msdn.microsoft.com/library/ms174302.aspx) pour plus de détails.
* **sys.dm\_tran\_base de données\_transactions**: fournit des informations supplémentaires sur les transactions, telles que le placement de la transaction dans le journal. Consultez la [documentation de la DMV](https://msdn.microsoft.com/library/ms186957.aspx) pour plus de détails.
* **sys.dm\_tran\_verrous**: fournit des informations sur les verrous actuellement détenues par des transactions en cours. Consultez la [documentation de la DMV](https://msdn.microsoft.com/library/ms190345.aspx) pour plus de détails.

## <a name="limitations"></a>Limitations 

Les limitations suivantes s’appliquent actuellement aux transactions élastique de la base de données dans la base de données SQL :

* Seules les transactions entre bases de données dans la base de données SQL sont pris en charge. Autres [X / Open XA](https://en.wikipedia.org/wiki/X/Open_XA) fournisseurs de ressources et de bases de données en dehors de la base de données SQL ne peut pas participer à des transactions de base de données élastique. Cela signifie que les transactions élastique de la base de données ne peut pas s’étendre à travers sur les installations de SQL Server et les bases de données SQL Azure. Pour les transactions distribuées dans les locaux de continuer à utiliser MSDTC. 
* Seules les transactions coordonnées client à partir d’une application .NET sont pris en charge. Prise en charge côté serveur pour T-SQL, telles que BEGIN DISTRIBUTED TRANSACTION est planifiée, mais n’est pas encore disponible. 
* Seules les bases de données sur V12 de base de données SQL Azure sont pris en charge.
* Les transactions via des services WCF ne sont pas pris en charge. Par exemple, vous disposez d’une méthode de service WCF qui s’exécute une transaction. Incluant l’appel dans une portée de transaction échoue sous la forme d’un [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="additional-resources"></a>Ressources supplémentaires

Pas encore en utilisant les fonctionnalités de base de données élastique pour vos applications Azure ? Consultez notre [Carte de Documentation](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/). Pour toute question, veuillez contacter nous sur le [forum de la base de données de SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) et de requêtes de fonctionnalités, veuillez les ajouter au [forum de commentaires de la base de données SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



