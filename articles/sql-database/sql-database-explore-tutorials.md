<properties
   pageTitle="Découvrez des didacticiels de base de données SQL Azure | Microsoft Azure"
   description="En savoir plus sur les capacités et les fonctionnalités de base de données SQL"
   keywords=""
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
   ms.workload="data-management"
   ms.date="08/24/2016"
   ms.author="carlrab"/>
   
# <a name="explore-azure-sql-database-tutorials"></a>Découvrez des didacticiels de base de données SQL Azure

Les liens ci-dessous vous prennent pour une vue d’ensemble de chaque zone de fonctionnalité répertoriés et un didacticiel étape-par-start simple pour chaque zone. Pour une solution de portée Démarrages rapides qui montrent l’utilisation de la base de données de SQL dans une solution complète basée sur les scénarios du monde réel, consultez [Azure SQL de base de données Solution rapide commence](sql-database-solution-quick-starts.md).

## <a name="using-sql-server-management-studio"></a>À l’aide de SQL Server Management Studio

Dans les didacticiels suivants, vous allez découvrir à l’aide de SQL Server Management Studio pour gérer et interroger la base de données de SQL Azure.


> [AZURE.IMPORTANT] Il est recommandé de toujours utiliser la dernière version du Management Studio reste synchronisé avec les mises à jour Microsoft Azure et de base de données SQL. [Mise à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


| Didacticiel  | Description  |
|---|---|---|
| [Se connecter à la base de données de SQL Azure à l’aide d’une connexion d’accès au niveau du serveur principale](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Dans ce didacticiel, vous allez apprendre à vous connecter à la base de données de SQL Azure à l’aide d’une connexion d’accès au niveau du serveur principale.|
| [Se connecter à la base de données de SQL Azure en tant qu’utilisateur](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données Azure SQL à l’aide d’un compte d’utilisateur de base de données.|
||||

## <a name="elastic-pools"></a>Pools élastiques

Dans les didacticiels suivants, vous allez découvrir à l’aide de [pools élastiques](sql-database-elastic-pool.md) pour gérer les objectifs de performance pour plusieurs bases de données qui ont des modèles d’utilisation imprévisibles et très variables.

| Didacticiel  | Description  |
|---|---|---|
| [Créer un pool élastique](sql-database-elastic-pool-create-portal.md) | Dans ce didacticiel, vous allez apprendre à créer un pool évolutif de bases de données SQL d’Azure. |
| [Analyser une base de données élastique](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | Dans ce didacticiel, vous allez apprendre à surveiller une seule base élastique pour des problèmes potentiels. |
| [Ajouter une alerte à une ressource de pool](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | Dans ce didacticiel, vous allez apprendre à ajouter des règles aux ressources qui envoient un e-mail à des personnes ou des chaînes d’alerte aux points de terminaison URL lorsque la ressource atteint un seuil d’utilisation que vous définissez. |
| [Déplacer une base de données dans un pool élastique](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | Dans ce didacticiel, vous allez apprendre à déplacer une base de données dans un pool élastique. |
| [Déplacer une base de données sur un pool élastique](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | Dans ce didacticiel, vous allez apprendre à déplacer une base de données sur un pool élastique. |
| [Modifier les paramètres de performance d’un pool](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | Dans ce didacticiel, vous allez apprendre à ajuster les limites de stockage et les performances d’un pool. |
||||

## <a name="elastic-database-jobs"></a>Tâches de base de données élastique

Dans les didacticiels suivants, vous allez découvrir les traitements [élastique de la base de données](sql-database-elastic-jobs-overview.md).

| Didacticiel  | Description  |
|---|---|---|
| [Mise en route avec les outils de base de données élastique](sql-database-elastic-scale-get-started.md) | Dans ce didacticiel, vous allez apprendre à utiliser les fonctionnalités d’outils élastique de la base de données à l’aide d’une simple application sharded. |
| [Mise en route avec les tâches de base de données de SQL Azure élastiques](sql-database-elastic-jobs-getting-started.md)  | Dans ce didacticiel, vous apprendrez comment pour comment créer et gérer des travaux qui gérer un groupe de bases de données liées.  | 
||||

## <a name="elastic-queries"></a>Requêtes élastiques

Dans les didacticiels suivants, vous allez découvrir [élastique de requêtes](sql-database-elastic-query-overview.md)en cours d’exécution. 

| Didacticiel  | Description  |
|---|---|---|
| [Interrogation sur une base de données (sharded) partitionnée horizontalement)](sql-database-elastic-query-getting-started.md) | Dans ce didacticiel, vous apprendrez comment créer des rapports à partir de toutes les bases de données dans une base de données (sharded) partitionnée horizontalement à l’aide [d’une requête élastique](sql-database-elastic-query-overview.md) |
| [Interrogation sur une base de données partitionnée verticalement)](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | Dans ce didacticiel, vous apprendrez comment créer des rapports à partir de toutes les bases de données dans un verticalement les base de données à l’aide [d’une requête élastique](sql-database-elastic-query-overview.md) |
| [Migrer une base de données existante à l’horizontale](sql-database-elastic-convert-to-use-elastic-tools.md)| Dans ce didacticiel, vous découvrez une évolutivité horizontale de la base de données (partagé) un SQL Azure. |
||||

## <a name="performance-optimization"></a>Optimisation des performances

Dans les didacticiels suivants, vous allez apprendre à optimiser les [performances des bases de données unique](sql-database-performance-guidance.md). Pour optimiser les performances de plusieurs bases de données, voir [pools élastiques](#elastic-pools).

| Didacticiel  | Description  |
|---|---|---|
| [Modifier le niveau de performances et de la couche de service de votre base de données](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | Dans ce didacticiel, vous allez apprendre à redimensionner ou mettre à l’échelle les performances d’une base de données Azure SQL à l’aide de niveaux de service. |
| [Insight de performances requête SQL de base de données Gestionnaire d’accès](sql-database-performance.md#performance-overview) | Dans ce didacticiel, vous allez apprendre à ouvrir et utiliser Insight de performances requête SQL de base de données Gestionnaire d’accès.|
| [Recommandations relatives aux performances de gestionnaire de base de données SQL](sql-database-advisor.md#viewing-recommendations) | Dans ce didacticiel, vous allez apprendre à afficher et appliquer les recommandations relatives aux performances de gestionnaire de base de données SQL. |
| [Examinez les UC supérieur utilisant des requêtes](sql-database-query-performance.md#review-top-cpu-consuming-queries)| Dans ce didacticiel, vous allez apprendre à Insight de performances des requête SQL de base de données Advisor permet d’examiner les UC supérieur utilisant des requêtes.|
| [Affichage des détails d’une requête individuelle](sql-database-query-performance.md#viewing-individual-query-details)| Dans ce didacticiel, vous allez apprendre à Insight de performances des requête SQL de base de données Advisor permet d’afficher les détails des performances de requête individuelle.|
||||

## <a name="sql-database-migration-and-archive"></a>Archives et Migration de base de données SQL 

Dans les didacticiels suivants, vous allez apprendre sur la [migration d’une base de données SQL Server existante à la base de données de SQL Azure](sql-database-cloud-migrate.md).

| Didacticiel  | Description  |
|---|---|---|
| [Détection des problèmes de compatibilité à l’aide des outils de données de SQL Server pour Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Dans ce didacticiel, vous allez apprendre à utiliser les outils de données de SQL Server de Visual Studio pour déterminer la compatibilité de la base de données de SQL Azure. |
| [Résolution des problèmes de compatibilité à l’aide des outils de données de SQL Server pour Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Dans ce didacticiel, vous allez apprendre à utiliser les outils de données de SQL Server de Visual Studio pour corriger des problèmes de compatibilité de base de données de SQL Azure. |
| [Déterminer la compatibilité de la base de données SQL à l’aide de SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | Dans ce didacticiel, vous allez apprendre à utiliser l’utilitaire de ligne de commande SQLPackage.exe pour déterminer la compatibilité de la base de données de SQL Azure.|
| [Déterminer la compatibilité de la base de données SQL à l’aide de SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |Dans ce didacticiel, vous allez apprendre à utiliser SQL Server Management Studio pour déterminer la compatibilité de la base de données de SQL Azure.|
| [Migration de la base de données SQL Server pour la base de données SQL à l’aide de déployer la base de données de l’Assistant base de données Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | Dans ce didacticiel, vous allez apprendre comment migrer une base de données SQL Server compatible pour SQL Azure de base de données à l’aide de la base de données de l’Assistant base de données Microsoft Azure déployer dans SQL Server Management Studio.
| [Exportation d’une base de données SQL Server vers un fichier de type sac à DOS à l’aide de SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Dans ce didacticiel, vous allez apprendre comment exporter une base de données SQL Server compatible dans un fichier de type sac à DOS à l’aide de l’Assistant Exportation d’une Application de couche données dans SQL Server Management Studio.|
| [Exportation d’une base de données SQL Server vers un fichier de type sac à DOS à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Dans ce didacticiel, vous allez apprendre comment exporter une base de données SQL Server compatible dans un fichier de type sac à DOS à l’aide de l’utilitaire de ligne de commande SQLPackage.exe.|
| [Importer un fichier de type sac à DOS dans SQL Azure de base de données à l’aide de SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Dans ce didacticiel, vous allez apprendre à importer une base de données dans la base de données de SQL Azure à partir d’un fichier de type sac à DOS à l’aide de l’Assistant Exportation d’une Application de couche données dans SQL Server Management Studio. |
| [Importer un fichier de type sac à DOS dans SQL Azure de base de données à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | Dans ce didacticiel, vous allez apprendre à importer une base de données dans la base de données de SQL Azure à partir d’un fichier de type sac à DOS à l’aide de l’utilitaire de ligne de commande SQLPackage. |
| [Importer un fichier de type sac à dos de base de données SQL Azure via le portail Azure](sql-database-import.md) | Dans ce didacticiel, vous allez apprendre à importer une base de données dans la base de données de SQL Azure à partir d’un fichier de type sac à DOS qui est stocké dans un blob Azure via le portail d’Azure.|
| [Importer un fichier de type sac à DOS dans SQL Azure de base de données à l’aide de PowerShell](sql-database-import-powershell.md) | Dans ce didacticiel, vous allez apprendre à importer une base de données dans la base de données de SQL Azure à partir d’un fichier de type sac à DOS à l’aide de PowerShell.|
| [Archiver une base de données Azure SQL via le portail Azure](sql-database-export.md#export-your-database) | Dans ce didacticiel, vous allez apprendre à archiver une base de données Azure SQL dans un fichier de type sac à DOS à l’aide du portail Azure. |
| [Archiver une base de données Azure SQL à l’aide de PowerShell](sql-database-export-powershell.md) | Dans ce didacticiel, vous allez apprendre à archiver une base de données Azure SQL dans un fichier de type sac à DOS à l’aide de PowerShell. |
| [Copier une base de données Azure SQL via le portail Azure](sql-database-copy.md#copy-your-sql-database) | Dans ce didacticiel, vous allez apprendre à copier une base de données Azure SQL via le portail Azure. |
| [Copier une base de données Azure SQL à l’aide de PowerShell](sql-database-copy-powershell#copy-your-sql-database) | Dans ce didacticiel, vous allez apprendre à copier une base de données Azure SQL à l’aide de PowerShell. |
| [Copier une base de données Azure SQL à l’aide de Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) | Dans ce didacticiel, vous allez apprendre à copier une base de données Azure SQL à l’aide de Transact-SQL. |
||||

##<a name="develop"></a>Développer

Dans les didacticiels suivants, vous allez découvrir à l’aide des [bibliothèques de connectivité](sql-database-libraries.md)et de [Développement de base de données SQL](sql-database-develop-overview.md) .

| Didacticiel  | Description  |
|---|---|---|
| [Se connecter à la base de données de SQL à l’aide de .NET (C#)](sql-database-develop-dotnet-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données Azure SQL à l’aide de C#. |
| [Se connecter à la base de données de SQL à l’aide de Java](sql-database-develop-java-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données Azure SQL à l’aide de Java. |
| [Se connecter à la base de données de SQL à l’aide de Node.js](sql-database-develop-nodejs-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données Azure SQL à l’aide de Node.js. |
| [Se connecter à la base de données de SQL à l’aide de PHP](sql-database-develop-php-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données Azure SQL à l’aide de PHP. |
| [Se connecter à la base de données de SQL en utilisant les Python](sql-database-develop-python-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données Azure SQL à l’aide de Python. |
| [Se connecter à la base de données de SQL à l’aide de Ruby](sql-database-develop-ruby-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données Azure SQL à l’aide de Ruby. |
||||
 
## <a name="database-access"></a>Base de données Access

Dans les didacticiels suivants, vous allez découvrir la [Création et la gestion des connexions et les utilisateurs](sql-database-manage-logins.md).

| Didacticiel  | Description  |
|---|---|---|
| [Créer une règle de pare-feu au niveau du serveur de base de données de SQL Azure via le portail Azure](sql-database-configure-firewall-settings.md)  | Dans ce didacticiel, vous allez apprendre à configurer un pare-feu au niveau du serveur de base de données de SQL via le portail Azure.  |
| [Créer une règle de pare-feu au niveau de la base de données à l’aide de Transact-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | Dans ce didacticiel, vous allez apprendre à créer une règle de pare-feu au niveau de la base de données à l’aide de Transact-SQL.|
| [Gérer les règles de pare-feu de niveau serveur à l’aide de Transact-SQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | Dans ce didacticiel, vous allez apprendre à gérer un pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de Transact-SQL.|
| [Gérer les règles de pare-feu de niveau serveur à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | Dans ce didacticiel, vous allez apprendre à gérer un pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de PowerShell.|
| [Gérer les règles de pare-feu de niveau serveur à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | Dans ce didacticiel, vous allez apprendre à gérer un pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de l’API de réinitialiser.|
| [Se connecter à la base de données de SQL Azure à l’aide d’une connexion d’accès au niveau du serveur principale](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Dans ce didacticiel, vous allez apprendre à vous connecter à la base de données de SQL Azure à l’aide d’une connexion d’accès au niveau du serveur principale.|
| [L’octroi de l’accès à une connexion de base de données] (sql-database-manage-logins.md#granting-database-access-to-a-login() | Dans ce didacticiel, vous allez apprendre à accorder l’accès à une connexion d’accès au niveau du serveur de base de données.|
| [Créer un nouvel utilisateur de base de données à l’aide de SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) | Dans ce didacticiel, vous allez apprendre à créer un nouvel utilisateur de base de données dans une base de données existante à l’aide de SSMS.|
| [Octroyer des autorisations db_owner de nouvelle base de données utilisateur](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | Dans ce didacticiel, vous allez apprendre à accorder des autorisations db_owner d’utilisateurs une base de données existante.|
| [Se connecter à la base de données de SQL Azure en tant qu’utilisateur](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données Azure SQL à l’aide d’un compte d’utilisateur de base de données.|
||||


## <a name="data-security"></a>Sécurité des données

Dans les didacticiels suivants, vous allez apprendre à propos de la [sécurisation des données de la base de données de SQL Azure](sql-database-security.md). 

| Didacticiel  | Description  |
|---|---|---|
| [Activer la détection de menaces pour votre base de données à l’aide du portail Azure](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | Dans ce didacticiel, vous allez apprendre à configurer la détection de menaces dans le portail Azure pour votre base de données.|
| [Sécuriser en utilisant les données sensibles toujours cryptées](sql-database-always-encrypted-azure-key-vault.md) | Dans ce didacticiel, vous utiliserez l’Assistant toujours crypté pour sécuriser les données sensibles dans une base de données SQL d’Azure.|
| [Sécuriser les données de senstive à l’aide du chiffrement de données transparent](https://msdn.microsoft.com/library/dn948096.aspx)| Dans ce didacticiel, vous allez apprendre à utiliser le chiffrement de données transparent pour sécuriser les données de senstive.|
| [Chiffrer une colonne de données](https://msdn.microsoft.com/library/ms179331.aspx)| Dans ce didacticiel, vous allez apprendre à crypter une colonne de données à l’aide de Transact-SQL.|
| [Configurer le masquage de données dynamiques](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal)  | Dans ce didacticiel, vous allez apprendre à configurer le masquage de données dynamiques pour votre base de données SQL d’Azure. |
||||

## <a name="business-continuity-and-query-scale-out"></a>La continuité d’activité et de montée de requête

Dans les didacticiels suivants, vous allez découvrir l’utilisation [géo-restauration et géo-réplication Active](sql-database-business-continuity.md) pour reccover des erreurs, de la continuité d’activité et de montée de requête.

| Didacticiel  | Description  |
|---|---|---|
| [Restauration d’une base de données de SQL Azure à un point antérieur dans le temps avec le portail Azure](sql-database-point-in-time-restore-portal.md)| Dans ce didacticiel, vous allez apprendre à restaurer votre base de données à un point antérieur dans le temps à l’aide du portail Azure.|
| [Restauration d’une base de données de SQL Azure à un point antérieur dans le temps avec PowerShell](sql-database-point-in-time-restore-powershell.md) | Dans ce didacticiel, vous apprendrez comment restaurer votre base de données à un point antérieur dans le temps à l’aide de PowerShell|
| [Restaurer une base de données Azure SQL supprimé, l’utilisation du portail Azure](sql-database-restore-deleted-database-portal.md) | Dans ce didacticiel, vous allez apprendre à restaurer une base de données supprimé via le portail Azure.|
| [Restaurer une base de données SQL d’Azure supprimé, à l’aide de PowerShell](sql-database-restore-deleted-database-powershell.md) | Dans ce didacticiel, vous allez apprendre à restaurer une base de données supprimé à l’aide de PowerShell.|
| [Configurer géo-réplication de base de données de SQL Azure en utilisant le portail Azure](sql-database-geo-replication-portal.md)| Dans ce didacticiel, vous allez apprendre à configurer l’utilisation du portail Azure géo-réplication Active.|
| [Configurer la réplication géographique pour SQL Azure de base de données à l’aide de PowerShell](sql-database-geo-replication-powershell.md)| Dans ce didacticiel, vous apprendrez comment configurer géo-réplication Active à l’aide de PowerShell.|
| [Configurer la réplication géographique pour SQL Azure de base de données à l’aide de Transact-SQL](sql-database-geo-replication-transact-sql.md)| Dans ce didacticiel, vous apprendrez comment configurer géo-réplication Active à l’aide de Transact-SQL.|
| [Initier un basculement planifié ou de base de données de SQL Azure en utilisant le portail Azure](sql-database-geo-replication-failover-portal.md) | Dans ce didacticiel, vous allez apprendre comment le basculement vers un réplica secondaire géo-répliquées via le portail Azure.|
| [Initier un basculement planifié ou de base de données de SQL Azure à l’aide de PowerShell](sql-database-geo-replication-failover-powershell.md) | Dans ce didacticiel, vous allez apprendre comment le basculement vers un réplica secondaire géo-répliquées à l’aide de PowerShell.|
| [Initier un basculement planifié ou de base de données de SQL Azure à l’aide de Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Dans ce didacticiel, vous allez apprendre comment le basculement vers un réplica secondaire géo-répliquées à l’aide de Transact-SQL.|
||||

## <a name="data-sync"></a>Synchronisation de données

Dans ce didacticiel, vous allez apprendre à propos de la [Synchronisation de données](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Didacticiel  | Description  |
|---|---|---| 
| [Mise en route de la synchronisation de données SQL Azure (aperçu)](sql-database-get-started-sql-data-sync.md)  | Dans ce didacticiel, vous découvrez les principes fondamentaux de la synchronisation de données SQL Azure via le portail classique d’Azure. |
||||

## <a name="next-steps"></a>Étapes suivantes

[Explorez les Démarrages rapides de Solution de base de données SQL Azure](sql-database-solution-quick-starts.md)
