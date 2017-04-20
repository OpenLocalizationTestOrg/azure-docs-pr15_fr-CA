<properties
   pageTitle="Démarrages rapides de Solution de base de données SQL Azure | Microsoft Azure"
   description="En savoir plus sur les Solutions de base de données SQL Azure"
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
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# <a name="explore-azure-sql-database-solution-quick-starts"></a>Explorez les Démarrages rapides de Solution de base de données SQL Azure

Cet article contient une vue d’ensemble de l’Azure SQL de base de données Solution rapide commence. Ces Démarrages rapides se trouvent dans le référentiel d’exemples de SQL Server GitHub et illustrent l’utilisation de la base de données de SQL dans une solution complète basée sur des scénarios concrets. Pour simples didacticiels pas à pas qui illustrent l’utilisation d’une fonction SQL de base de données particulière, consultez [les didacticiels Explorer de base de données SQL Azure](sql-database-explore-tutorials.md).

## <a name="try-the-wingtiptickets-demo-and-hands-on-lab"></a>Essayez la démonstration de WingTipTickets et aux ateliers pratiques

La démonstration de [WingTipTickets de base de données SQL Azure](https://github.com/microsoft/wingtiptickets) et ce laboratoire pratique vous montrer une base de données de SQL Azure basée sur la recherche d’Azure application et qui permet de vendre des tickets de concert.


## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools"></a>Collecter et analyser les données d’utilisation des ressources sur plusieurs pools

[Solution de démarrage rapide : télémétrie élastique Pool à l’aide de PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) fournit une solution de collecte et d’analyse de l’utilisation des ressources de base de données SQL sur des pools multiples dans un abonnement. Lorsque vous avez un grand nombre de bases de données dans un abonnement, il est difficile à contrôler chaque pool élastique séparément.

Pour résoudre ce problème, vous pouvez combiner les applets de commande PowerShell de base de données SQL et de requêtes T-SQL pour collecter les données d’utilisation des ressources à partir de plusieurs pools et leurs bases de données. Cela vous permet de surveiller et d’analyser l’utilisation des ressources plus efficacement.

Ce démarrage rapide de fournit un ensemble de scripts PowerShell et requêtes T-SQL ainsi que la documentation sur ce que fait la solution et comment l’implémenter.

## <a name="get-started-with-elastic-database-in-an-saas-scenario"></a>Mise en route de l’élasticité de la base de données dans un scénario SaaS

 [Solution de démarrage rapide : tableau de bord personnalisé de Pool élastique pour SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) fournit une solution pour un scénario en-un-Solution logicielle (SaaS) qui tire parti de la fonctionnalité élastique de la base de données de base de données SQL afin de fournir un back-end économique et évolutive de la base de données pour une application SaaS.

Dans cette solution, vous guidera dans la mise en oeuvre d’une application web. Cette application web vous permet de visualiser la charge qui est créée sur une base de données élastique par un générateur de charge qui utilise un tableau de bord personnalisé qui complètent le portail Azure.

Ce démarrage rapide fournit un générateur de charge et l’application web de surveillance ainsi que la documentation sur ce que fait l’application et comment l’utiliser.

## <a name="create-an-azure-sql-database-by-using-code-first-development-and-the-entity-framework"></a>Créer une base de données Azure SQL à l’aide de développement Code First et Entity Framework

La vidéo et l’exemple de [Code First pour une nouvelle base de données](https://msdn.microsoft.com/data/jj193542.aspx) fournit une introduction au développement Code First ciblant une nouvelle base de données. Ce scénario cible une base de données qui n’existe pas, mais qui sera créé par Code First. Vous pouvez également le scénario crée une base de données vide, où le premier Code ajoute les nouvelles tables.

Code premier vous permet de définir votre modèle en utilisant les classes C# ou Visual Basic .NET. Vous pouvez effectuer une configuration supplémentaire facultative en utilisant des attributs sur les classes et les propriétés ou à l’aide d’une API fluent.

## <a name="integrate-elastic-database-tools-into-an-entity-framework-application"></a>Intégrer les outils de l’élasticité de la base de données dans une application Entity Framework

L’exemple de [bibliothèque de client de base de données élastique avec Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) montre les modifications que vous devez apporter à une application Entity Framework pour l’intégration avec [les outils de base de données élastique](sql-database-elastic-scale-get-started.md). Le focus est sur la composition de [Éclater mapper la gestion](sql-database-elastic-scale-shard-map-management.md) et le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) avec l’approche Entity Framework Code First.

Le [Premier Code à une nouvelle base de données exemple pour EF](http://msdn.microsoft.com/data/jj193542.aspx) sert de notre exemple en cours d’exécution tout au long de cet exemple. L’exemple de code qui accompagne ce document fait partie de l’ensemble d’outils élastique de la base de données d’exemples dans les exemples de code Visual Studio.

## <a name="integrate-elastic-database-tools-with-row-level-security"></a>Intégrer les outils d’élastique de la base de données avec la sécurité de niveau ligne

[Partagé avec les outils de base de données élastique et la sécurité de niveau ligne](sql-database-elastic-tools-multi-tenant-row-level-security.md) présente les modifications que vous devez apporter à une application Entity Framework pour intégrer des [Outils de base de données élastique](sql-database-elastic-scale-get-started.md) avec la [sécurité de niveau ligne](https://msdn.microsoft.com/library/dn765131). Cet exemple illustre l’utilisation de ces technologies ensemble pour générer une application avec un niveau de données hautement évolutive qui prend en charge le milieu des fragments partagés.

Pour cela, à l’aide de ADO.NET SqlClient ou Entity Framework. Cet exemple étend la [bibliothèque de client de base de données élastique avec Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) en ajoutant la prise en charge des bases de données partagé partagé.
Il crée une application console simple pour la création de blogs et les publications, avec quatre locataires et deux bases de données partagé partagé.

## <a name="create-online-surveys-with-the-tailspin-surveys-application"></a>Créer des enquêtes en ligne avec l’application Tailspin enquêtes

Cet [exemple d’application Tailspin enquêtes](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) est une application web partagé, appelée enquêtes, qui permet aux utilisateurs de créer des enquêtes en ligne. L’exemple répond à certaines préoccupations clées sur la gestion des identités utilisateur dans une application partagée, y compris d’inscription, d’authentification, d’autorisation et les rôles d’application.

## <a name="learn-about-the-latest-security-features-of-sql-database-with-the-contoso-clinic-demo-application"></a>En savoir plus sur les dernières fonctionnalités de sécurité de base de données SQL avec l’Application de démonstration de Contoso clinique

Cette [application de démonstration pratique de Contoso](https://github.com/Microsoft/azure-sql-security-sample) présente les dernières fonctionnalités de sécurité de la base de données SQL.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez des didacticiels de la base de données de SQL Azure](sql-database-explore-tutorials.md)
