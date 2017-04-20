<properties
   pageTitle="Migration d’une base de données SQL Server pour la base de données de SQL Azure | Microsoft Azure"
   description="Base de données SQL Azure de Microsoft, déploiement de base de données, migration de base de données, importation de base de données, base de données de l’exportation, l’Assistant migration"
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

# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>Importer à partir de type sac à DOS à l’aide de SSMS de la base de données SQL

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Cet article explique comment importer à partir d’un fichier de [type sac à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) pour SQL de base de données à l’aide de l’Assistant Exportation d’une Application de couche données dans SQL Server Management Studio.

> [AZURE.NOTE] Les étapes suivantes supposent que vous avez déjà déployé de votre instance de logique Azure SQL et disposez les informations de connexion.

1. Vérifiez que vous disposez de la dernière version de SQL Server Management Studio. Nouvelles versions de Management Studio sont mises à jour mensuelles de rester synchronisé avec les mises à jour sur le portail Azure.

     > [AZURE.IMPORTANT] Il est recommandé de toujours utiliser la dernière version du Management Studio reste synchronisé avec les mises à jour Microsoft Azure et de base de données SQL. [Mise à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Se connecter à votre serveur de base de données de SQL Azure, cliquez sur le dossier **bases de données** , puis cliquez sur **Application de couche données d’importation...**

    ![Importer l’élément de menu d’application de couche données](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.  Pour créer la base de données dans la base de données de SQL Azure, importer un fichier de type sac à DOS à partir de votre disque local, ou sélectionnez le compte de stockage Azure et un conteneur dans lequel vous avez téléchargé votre fichier de sac à DOS.

    ![Paramètres d’importation](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

     > [AZURE.IMPORTANT] Lorsque vous importez un type sac à DOS depuis le stockage blob Azure, utiliser le stockage standard. Importation d’un type sac à DOS à partir du stockage de la prime n’est pas pris en charge.

4.  Fournir le **nouveau nom de base de données** pour la base de données sur la base de données SQL Azure, définissez l' **Édition de Microsoft Azure SQL de base de données** (niveau de service), **taille maximale de la base de données**et **Objectif de Service** (niveau de performance).

    ![Paramètres de la base de données](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.  Cliquez sur **suivant** , puis cliquez sur **Terminer** pour importer le fichier de type sac à DOS dans une nouvelle base de données sur le serveur de base de données de SQL Azure.

6. Explorateur d’objets, vous connecter à votre base de données migrée dans votre serveur de base de données de SQL Azure.

6.  Utilisation du portail Azure, permet d’afficher votre base de données et ses propriétés.

## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migration des bases de données SQL Server non - à l’aide de l’Assistant Migration de SQL Server](http://blogs.msdn.com/b/ssma/)
