<properties
   pageTitle="Non pris en charge dans T-SQL de base de données SQL Azure | Microsoft Azure"
   description="Instructions Transact-SQL qui sont moins entièrement pris en charge dans la base de données de SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/30/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="azure-sql-database-transact-sql-differences"></a>Différences de Transact-SQL de base de données SQL Azure


La plupart des fonctions Transact-SQL qui dépendent d’applications est pris en charge dans Microsoft SQL Server et de base de données de SQL Azure. Une liste partielle des fonctionnalités prises en charge pour les applications suivant :

- Types de données.
- Opérateurs.
- Fonctions de chaîne, arithmétiques, logiques et curseur.

Cependant, la base de données de SQL Azure est conçu pour isoler des fonctions à partir de toute dépendance sur la base de données **master** . En conséquence de nombreuses activités au niveau du serveur sont inappropriées pour la base de données SQL et non pris en charge. Les fonctionnalités qui sont déconseillées dans SQL Server ne sont généralement pas pris en charge dans la base de données de SQL.

> [AZURE.NOTE]
> Cette rubrique traite des fonctionnalités qui sont disponibles avec la base de données de SQL lors de la mise à niveau vers la version actuelle ; V12 de base de données SQL. Pour plus d’informations sur V12, consultez [du SQL de base de données V12 que nouveau](sql-database-v12-whats-new.md).

Les sections suivantes répertorient les fonctionnalités qui sont partiellement prises en charge et les fonctionnalités qui sont complètement non pris en charge.


## <a name="features-partially-supported-in-sql-database-v12"></a>Fonctionnalités partiellement prises en charge dans V12 de base de données SQL

V12 de base de données SQL prend en charge certains mais pas tous les arguments qui existent dans les instructions SQL Server 2016 Transact-SQL correspondantes. Par exemple, l’instruction CREATE PROCEDURE est disponible mais toutes les options de création de procédure ne sont pas disponibles. Reportez-vous aux rubriques syntaxe lié pour plus d’informations sur les fonctionnalités prises en charge de chaque instruction.

- Bases de données : [Création](https://msdn.microsoft.com/library/dn268335.aspx )/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMV est généralement disponibles pour les fonctionnalités qui sont disponibles.
- Fonctions : [créer](https://msdn.microsoft.com/library/ms186755.aspx)/[Modifier la fonction](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 
- Connexions : [Création](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- Procédures stockées : [créer](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tableaux : [Création](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- Types (personnalisé) : [Créer un TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- Utilisateurs : [Création](https://msdn.microsoft.com/library/ms173463.aspx)/[Modifier utilisateur](https://msdn.microsoft.com/library/ms176060.aspx)
- Vues : [créer](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## <a name="features-not-supported-in-sql-database"></a>Fonctionnalités non prises en charge dans la base de données de SQL

- Classement des objets système
- Connexion liée : instructions de point de terminaison, ORIGINAL_DB_NAME. Base de données de SQL ne prend pas en charge l’authentification Windows, mais ne prend pas en charge l’authentification d’Azure Active Directory similaire. Certains types d’authentification nécessitent la version la plus récente de SSMS. Pour plus d’informations, consultez [connexion de base de données SQL ou SQL données entrepôt par à l’aide d’Azure l’authentification Active Directory](sql-database-aad-authentication.md).
- Entre les requêtes de base de données à l’aide de trois ou quatre noms de partie. (Les requêtes inter-base de données en lecture seule sont pris en charge à l’aide [d’une requête élastique](sql-database-elastic-query-overview.md).)
- Chaînage des propriétés de base de données croisée, paramètre digne de confiance
- Collecteur de données
- Diagrammes de base de données
- Messagerie de base de données
- DATABASEPROPERTY (utilisez plutôt du DATABASEPROPERTYEX)
- Connexions de EXECUTE AS
- Cryptage : gestion des clés extensible
- Gestion des événements : événements, notifications d’événements, les notifications de requête
- Fonctionnalités liées à l’emplacement du fichier de base de données, la taille et les fichiers de base de données qui sont gérées automatiquement par Microsoft Azure.
- Fonctionnalités liées à la haute disponibilité, qui est gérée par le biais de votre compte Microsoft Azure : sauvegarde, restauration, AlwaysOn, la mise en miroir de base de données, l’envoi de journaux, les modes de récupération. Pour plus d’informations, voir sauvegarde de base de données SQL Azure et la restauration.
- Les fonctionnalités qui s’appuient sur le lecteur de journal en cours d’exécution sur la base de données de SQL : la réplication par émission, la Capture de données modifiées.
- Les fonctionnalités qui s’appuient sur l’Agent de SQL Server ou la base de données MSDB : travaux, des alertes, des opérateurs, gestion basée sur des stratégies, de base de données, serveurs d’administration centrale.
- FILESTREAM
- Fonctions : fn_get_sql fn_virtualfilestats, fn_virtualservernodes
- Tables temporaires globales
- Paramètres liés au matériel de serveur : mémoire, des threads de travail, l’affinité du processeur, les indicateurs de trace, etc.. Utilisez à la place des niveaux de service.
- HAS_DBACCESS
- KILL STATS JOB
- Serveurs liés, OPENQUERY, OPENROWSET, OPENDATASOURCE, BULK INSERT et les noms en quatre parties
- Serveurs maître/cible
- .NET Framework, [intégration du CLR avec SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Gouverneur de ressources
- Recherche sémantique
- Informations d’identification du serveur. USE base de données étendue des informations d’identification à la place.
- Les éléments au niveau du serveur : sys.login_token, IS_SRVROLEMEMBER, les rôles de serveur. Les autorisations de niveau serveur ne sont pas disponibles, même si certains sont remplacés par des autorisations au niveau de la base de données. Certains DMV au niveau du serveur n’est pas disponibles, même si certains sont remplacés par les DMV au niveau de la base de données.
- Sans serveur express : localdb, les instances d’utilisateur
- Courtier de service
- INSTRUCTION SET REMOTE_PROC_TRANSACTIONS
- ARRÊT
- sp_addmessage
- options de sp_configure et RECONFIGURE. Certaines options sont disponibles à l’aide de [ALTER DATABASE CONFIGURATION étendue](https://msdn.microsoft.com/library/mt629158.aspx).
- sp_helpuser
- sp_migrate_user_to_contained
- Audit de SQL Server. Utilisez à la place d’audit de base de données SQL.
- SQL Server Profiler
- Trace de SQL Server
- Indicateurs de trace. Certains éléments d’indicateur de trace ont été déplacés vers les modes de compatibilité.
- Débogage de Transact-SQL
- Déclencheurs : À portée de serveur ou de déclencheurs d’ouverture de session
- L’instruction USE : pour modifier le contexte de la base de données à une autre base de données, vous devez effectuer une nouvelle connexion à la base de données.


## <a name="full-transact-sql-reference"></a>Référence de Transact-SQL complète

Pour plus d’informations sur la grammaire Transact-SQL, l’utilisation et des exemples, consultez [Référence de Transact-SQL (moteur de base de données)](https://msdn.microsoft.com/library/bb510741.aspx) dans la documentation en ligne de SQL Server. 

### <a name="about-the-applies-to-tags"></a>Sur les balises « S’applique à »

La référence de Transact-SQL comprend des rubriques relatives aux versions de SQL Server 2008 à la présente. Sous le titre de la rubrique est une icône de la barre, répertoriant les quatre plates-formes de SQL Server et indiquant les conditions d’application. Par exemple, les groupes de disponibilité ont été introduites dans SQL Server 2012. La rubrique [Créer un groupe AVAILABILTY](https://msdn.microsoft.com/library/ff878399.aspx) indique que l’instruction s’applique aux ** de SQL Server (à partir de 2012). L’instruction ne s’applique pas à SQL Server 2008, SQL Server 2008 R2, base de données de SQL Azure, entrepôt de données de SQL Azure ou Parallel Data Warehouse.

Dans certains cas, le sujet général de la rubrique peut être utilisé dans un produit, mais il existe des différences mineures entre les produits. Les différences sont indiquées dans les milieux de la rubrique selon le cas.

