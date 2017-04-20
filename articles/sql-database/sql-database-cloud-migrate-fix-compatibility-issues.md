<properties
   pageTitle="Résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers SQL de base de données | Microsoft Azure"
   description="Microsoft Azure SQL base de données, migration de base de données, la compatibilité, l’Assistant de Migration SQL Azure"
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

# <a name="use-sql-azure-migration-wizard-to-fix-sql-server-database-compatibility-issues-before-migration-to-azure-sql-database"></a>Utiliser l’Assistant de Migration de SQL Azure pour des problèmes de compatibilité de base de données de SQL Server corriger avant la migration de la base de données de SQL Azure

> [AZURE.SELECTOR]
- Utiliser l' [Assistant de Migration SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Utiliser [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Utiliser [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Dans cet article, vous apprenez à détecter et à résoudre les problèmes de compatibilité de base de données SQL Server à l’aide de l’Assistant de Migration SQL Azure avant la migration de la base de données de SQL Azure.

## <a name="using-sql-azure-migration-wizard"></a>À l’aide de l’Assistant Migration de SQL Azure

Utilisez l’outil de CodePlex de [l’Assistant Migration de SQL Azure](http://sqlazuremw.codeplex.com/) pour générer un script T-SQL à partir d’une base de données source incompatible. Ce script est ensuite transformé par l’Assistant pour le rendre compatible avec la base de données SQL. Ensuite, vous vous connectez à une base de données de SQL Azure pour exécuter le script. Cet outil analyse également les fichiers de suivi pour déterminer les problèmes de compatibilité. Le script peut être généré avec le schéma uniquement ou peut inclure des données au format BCP. La documentation supplémentaire, y compris les instructions pas à pas sont disponible sur CodePlex à [l’Assistant de Migration de SQL Azure](http://sqlazuremw.codeplex.com/).  

 ![Diagramme de migration SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] Pas tous les schéma incompatible qui sont détectés par l’Assistant peut être résolu par ses transformations intégrées. Script incompatible qui ne peuvent pas être résolu sont signalées comme des erreurs, avec des commentaires injectés le script généré. Si de nombreuses erreurs sont détectées, utiliser Visual Studio ou SQL Server Management Studio pour les parcourir et les corriger chaque erreur qui ne peut pas être résolu à l’aide de l’Assistant de Migration SQL Server.

## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Migrer une base de données SQL Server compatible à la base de données de SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migration des bases de données SQL Server non - à l’aide de l’Assistant Migration de SQL Server](http://blogs.msdn.com/b/ssma/)
