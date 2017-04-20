<properties
   pageTitle="Migration de base de données SQL Server à SQL de base de données | Microsoft Azure"
   description="Découvrez est-il local SQL Server migration base de données à la base de données de SQL Azure dans le nuage. Outils de migration de base de données permet de tester la compatibilité avant la migration de la base de données."
   keywords="migration, migration de base de données sql server, les outils de migration de base de données de la base de données, migration de base de données, migrer la base de données sql"
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
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migration de base de données SQL Server pour la base de données SQL dans le nuage

Dans cet article, vous apprendrez à comment migrer un local SQL Server 2005 ou une base de données à la base de données de SQL Azure. Dans ce processus de migration de base de données, vous migrez votre schéma et vos données à partir de la base de données SQL Server dans votre environnement actuel dans la base de données de SQL. Pour réussir, la base de données existante doit d’abord transiter un test de compatibilité. Avec [V12 de base de données SQL](sql-database-v12-whats-new.md), il existe quelques problèmes de compatibilité restants, autres que les problème liés aux opérations au niveau du serveur et les bases de données croisées. Bases de données et des applications qui s’appuient sur [partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md) devez certains réingénierie pour résoudre ces incompatibilités avant de la base de données SQL Server peut être migré.

Pour effectuer la migration, les éléments suivants sont les étapes vous pourrez :

- **Test de compatibilité**: valider la compatibilité de base de données avec [V12 de base de données SQL](sql-database-v12-whats-new.md). 
- **Résoudre des problèmes de compatibilité, le cas échéant**: si la validation échoue, vous devez corriger les erreurs de validation.  
- **Effectuer la migration** Une fois votre base de données est compatible, vous pouvez utiliser une ou plusieurs méthodes pour effectuer la migration. 

SQL Server fournit plusieurs méthodes pour effectuer chacune de ces tâches. Cet article fournit une vue d’ensemble des méthodes disponibles pour chaque tâche. Le diagramme suivant illustre les étapes et les méthodes.

  ![Diagramme de migration VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] Pour migrer une base de données SQL Server non-, y compris Microsoft Access, Sybase, MySQL Oracle et DB2 à la base de données de SQL Azure, consultez [Assistant de Migration SQL Server](http://blogs.msdn.com/b/ssma/).

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Outils de migration de base de données testent de compatibilité de base de données SQL Server avec la base de données de SQL

Pour tester les problèmes de compatibilité de la base de données SQL avant de commencer le processus de migration de base de données, utilisez une des méthodes suivantes :

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Conseiller de mise à niveau](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- [Outils de données de SQL Server de Visual Studio (« SSDT »)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT utilise les règles de compatibilité la plus récentes afin de détecter les incompatibilités V12 de base de données SQL. Si des incompatibilités sont détectées, vous pouvez résoudre les problèmes détectés directement dans cet outil. Cette méthode est la méthode recommandée pour tester et résoudre les problèmes de compatibilité V12 de base de données SQL. 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage est un utilitaire de ligne de commande de tests pour les problèmes de compatibilité et génère un rapport contenant des problèmes de compatibilité détectés. Si vous utilisez cet outil, assurez-vous que vous utilisez la version la plus récente pour utiliser les règles de compatibilité la plus récentes. Si des erreurs sont détectées, vous devez utiliser un autre outil de résoudre les problèmes de compatibilité détectés - SSDT est recommandé.  
- [Assistant d’application de la couche de données Exporter dans SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): cet Assistant détecte et signale les erreurs à l’écran. Si des erreurs sont détectées, vous pouvez continuer et effectuer la migration de la base de données SQL. Si des erreurs sont détectées, vous devez utiliser un autre outil de résoudre les problèmes de compatibilité détectés - SSDT est recommandé.
- [Le Microsoft SQL Server 2016 Upgrade Advisor aperçu](http://www.microsoft.com/download/details.aspx?id=48119): cet outil autonome, ce qui est actuellement en mode Aperçu, détecte et génère un rapport des incompatibilités de V12 de base de données SQL. Cet outil n’a pas encore les règles de compatibilité la plus récentes. Si aucune erreur n’est détectée, vous pouvez continuer et effectuer la migration de la base de données SQL. Si des erreurs sont détectées, vous devez utiliser un autre outil de résoudre les problèmes de compatibilité détectés - SSDT est recommandé. 
- [Assistant de Migration SQL Azure (« SAMW »)](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW est un outil de codeplex qui utilise les règles de compatibilité V11 de base de données SQL Azure afin de détecter les incompatibilités V12 de base de données SQL Azure. Si des incompatibilités sont détectées, certains problèmes peuvent être résolus directement dans cet outil. Cet outil peut rechercher les incompatibilités qui ne doivent pas être résolu. Il a été la première base de données de SQL Azure migration outil d’assistance disponible et est activement pris en charge par la Communauté SQL Server. En outre, cet outil pouvez effectuer la migration à partir de l’outil lui-même. 

## <a name="fix-database-migration-compatibility-issues"></a>Résoudre des problèmes de compatibilité de migration de base de données

Si des problèmes de compatibilité sont détectés, vous devez les corriger avant de procéder à la migration de la base de données SQL Server. Il existe une grande variété de problèmes de compatibilité que vous pourriez rencontrer, selon les deux sur la version de SQL Server dans la base de données source et de la complexité de la base de données à laquelle vous effectuez la migration. Les versions antérieures de SQL Server ont plus de problèmes de compatibilité. Utilisez les ressources suivantes, en plus d’une recherche ciblée sur Internet à l’aide de votre moteur de recherche de choix :

- [Fonctionnalités de base de données SQL Server non pris en charge dans la base de données de SQL Azure](sql-database-transact-sql-information.md)
- [Fonctionnalité de moteur de base de données abandonnés dans SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Fonctionnalité de moteur de base de données abandonnés dans SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Fonctionnalité du moteur de base de données abandonnés dans SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Fonctionnalité du moteur de base de données abandonnés dans SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Fonctionnalité du moteur de base de données abandonnés dans SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

En plus des recherches sur Internet et l’utilisation de ces ressources, utilisez les [forums de la Communauté MSDN SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) ou [StackOverflow](http://stackoverflow.com/).

Pour résoudre les problèmes détectés, utilisez un des outils de migration de base de données suivants :

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- Utilisez les [Outils de données de SQL Server de Visual Studio (« SSDT »)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): pour utiliser SSDT, vous importez votre schéma de base de données dans les outils de données de SQL Server pour Visual Studio « SSDT ») et générez le projet pour un déploiement V12 de base de données SQL. Résoudre tous les problèmes de compatibilité détectés dans SSDT. Lorsque vous avez terminé, vous synchronisez les modifications apportées à la base de données source (ou une copie de la base de données source. SSDT est actuellement la méthode recommandée pour tester et résoudre les problèmes de compatibilité V12 de base de données SQL. Cliquez sur le lien d’une [procédure à l’aide de SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
- Utilisez [SQL Server Management Studio (« SSMS »)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): pour utiliser SSMS, vous exécutez les commandes Transact-SQL pour corriger les erreurs détectées à l’aide d’un autre outil. Cette méthode est principalement destinée aux utilisateurs avancés de modifier le schéma de base de données directement dans la base de données source. 
- Utiliser [l’Assistant de Migration SQL Azure (« SAMW »)](sql-database-cloud-migrate-fix-compatibility-issues.md): pour utiliser SAMW, vous générez un script Transact-SQL à partir de la base de données source. L’Assistant convertit le script, chaque fois que possible, de rendre le schéma compatible avec le V12 de base de données SQL. Lorsque vous avez terminé, SAMW peut se connecter à V12 de base de données SQL pour exécuter le script. Cet outil analyse également les fichiers de suivi pour déterminer les problèmes de compatibilité. Le script peut être généré avec le schéma uniquement ou peut inclure des données au format BCP.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Migrer une base de données SQL Server compatible à la base de données de SQL

Pour migrer une base de données SQL Server compatible, Microsoft fournit plusieurs méthodes de migration pour divers scénarios. La méthode que vous choisissez dépend de votre tolérance pour les temps d’arrêt, la taille et la complexité de votre base de données SQL Server et votre connectivité au nuage Microsoft Azure.  

> [AZURE.SELECTOR]
- [Assistant de Migration de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exporter vers un fichier de type sac à DOS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importer depuis un fichier de type sac à DOS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Réplication transactionnelle](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Pour choisir votre méthode de migration, la première question à poser est si vous pouvez vous permettre de prendre la base de données de production pendant la migration. Migration d’une base de données pendant des transactions actives peut entraîner des incohérences de base de données et la base de données possible. Il existe de nombreuses méthodes pour mettre en veille pour une base de données, de désactiver la connectivité des clients à la création d’une [capture instantanée de base de données](https://msdn.microsoft.com/library/ms175876.aspx).

Pour migrer très peu de temps, utilisez [la réplication de transactions SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) si votre base de données répond à la configuration requise pour la réplication transactionnelle. Si vous pouvez vous permettre une interruption de service ou si vous effectuez un test de migration d’une base de données de production pour la migration d’une version ultérieure, envisagez l’une des trois méthodes suivantes :

- [Assistant de Migration de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): pour les petites et moyennes bases de données, la migration d’une compatible SQL Server 2005 ou une version ultérieure de la base de données est aussi simple qu’exécutant la [Déployer la base de données pour l’Assistant Microsoft Azure de base de données](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) dans SQL Server Management Studio.
- [Exporter vers un fichier de type sac à DOS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) , puis sur [Importer depuis un fichier de type sac à DOS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): Si vous avez des problèmes de connectivité (aucune connectivité, faible bande passante ou des problèmes de délai d’attente) et pour les moyennes et grandes bases de données, utilisez un fichier de [type sac à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) . Avec cette méthode, vous exportez les données et le schéma de SQL Server vers un fichier de type sac à DOS. Ensuite, vous importez le fichier de type sac à DOS dans la base de données SQL à l’aide de l’Assistant Exportation de données couche Application dans SQL Server Management Studio ou l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .
- Utiliser conjointement le sac à DOS et BCP : utilisez un [sac à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) et [BCP](https://msdn.microsoft.com/library/ms162802.aspx) pour les bases de données beaucoup plus grandes pour atteindre la parallélisation supérieure pour augmente les performances, bien qu’avec la plus grande complexité. Avec cette méthode, effectuer la migration du schéma et les données séparément.
 - [Exporter le schéma uniquement avec un fichier de type sac à DOS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importer le schéma uniquement à partir du fichier de type sac à DOS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) dans la base de données de SQL.
 - [BCP](https://msdn.microsoft.com/library/ms162802.aspx) permet d’extraire les données dans des fichiers plats, puis [charge parallèle](https://technet.microsoft.com/library/dd425070.aspx) ces fichiers dans la base de données de SQL Azure.

     ![SQL Server de base de données de migration : migration de base de données SQL vers le nuage.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Étapes suivantes

- [L’aperçu du Conseiller de mise à niveau de Microsoft SQL Server 2016](http://www.microsoft.com/download/details.aspx?id=48119)
- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

##<a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
[Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migration des bases de données SQL Server non - à l’aide de l’Assistant Migration de SQL Server](http://blogs.msdn.com/b/ssma/)
