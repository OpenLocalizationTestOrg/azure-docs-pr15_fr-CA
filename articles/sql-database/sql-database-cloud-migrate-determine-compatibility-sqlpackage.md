<properties
   pageTitle="Déterminer la compatibilité de la base de données SQL à l’aide de SqlPackage.exe | Microsoft Azure"
   description="Microsoft Azure SQL base de données, migration de base de données, la compatibilité de la base de données SQL, SqlPackage"
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

# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>Déterminer la compatibilité de la base de données SQL à l’aide de SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Conseiller de mise à niveau](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Dans cet article, vous apprenez à déterminer si une base de données SQL Server est compatible pour migrer vers une base de données SQL à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .

## <a name="using-sqlpackageexe"></a>À l’aide de SqlPackage.exe

1. Ouvrez une invite de commande et modifier un répertoire contenant la version la plus récente de sqlpackage.exe. Cet utilitaire est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et les [Outils de données de SQL Server pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou vous pouvez télécharger la dernière version de [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) directement à partir du centre de téléchargement Microsoft.
2. Exécutez la commande suivante SqlPackage avec les arguments suivants pour votre environnement :

    ' sqlpackage.exe /Action:Export /ssn : /sdn de < nom_serveur > : < nom_base_de_données > /tf : < target_file > /p:TableData = < schema_name.table_name >>< fichier_de_sortie > 2 > & 1'

  	| Argument  | Description  |
  	|---|---|
  	| < nom_serveur >  | nom du serveur source  |
  	| < nom_base_données >  | nom de la base de données source  |
  	| < target_file >  | nom de fichier et un emplacement pour le fichier de type sac à DOS  |
  	| < schema_name.table_name >  | les tables pour lesquelles les données sont dans le fichier cible  |
  	| < fichier_de_sortie >  | le nom de fichier et l’emplacement du fichier de sortie avec des erreurs, le cas échéant  |

    La raison de l’argument /p:TableName est que nous ne souhaitez tester la compatibilité de base de données pour l’exportation vers V12 de base de données SQL Azure plutôt que d’exporter les données de toutes les tables. Malheureusement, l’argument de l’exportation de sqlpackage.exe ne gère pas l’extraction des tables de zéro. Vous devez spécifier au moins une table, par exemple une petite table unique. L’output_file < > contient le rapport d’erreurs. Le « 2 > > & 1 « chaîne de canaux à la fois la sortie standard et l’erreur standard résultant de l’exécution de la commande de fichier de sortie spécifié.

    ![Exporter une application de couche données dans le menu tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Ouvrez le fichier de sortie et examinez les erreurs de compatibilité, le cas échéant. 

    ![Exporter une application de couche données dans le menu tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
[version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Résoudre des problèmes de compatibilité de migration de base de données](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrer une base de données SQL Server compatible à la base de données de SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migration des bases de données SQL Server non - à l’aide de l’Assistant Migration de SQL Server](http://blogs.msdn.com/b/ssma/)
