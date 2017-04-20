<properties
   pageTitle="Exportation d’une base de données SQL Server vers un fichier de type sac à DOS à l’aide de SqlPackage | Microsoft Azure"
   description="Base de données de SQL Microsoft Azure, migration de base de données, exporter la base de données, exporter un fichier de type sac à DOS, sqlpackage"
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

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Exportation d’une base de données SQL Server vers un fichier de type sac à DOS à l’aide de SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

Cet article explique comment exporter une base de données SQL Server vers un fichier de [type sac à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Cet utilitaire est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et les [Outils de données de SQL Server pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou vous pouvez télécharger la dernière version de [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) directement à partir du centre de téléchargement Microsoft.

1. Ouvrez une invite de commande et modifier un répertoire contenant l’utilitaire de ligne de commande sqlpackage.exe, cet utilitaire est fourni avec Visual Studio et SQL Server. Utilisez la recherche sur votre ordinateur pour rechercher le chemin d’accès dans votre environnement.
2. Exécutez la commande suivante de la sqlpackage.exe avec les arguments suivants pour votre environnement :

    ' sqlpackage.exe /Action:Export /ssn : /sdn de < nom_serveur > : < nom_base_de_données > /tf : < target_file >

  	| Argument  | Description  |
  	|---|---|
  	| < nom_serveur >  | nom du serveur source  |
  	| < nom_base_données >  | nom de la base de données source  |
  	| < target_file >  | nom de fichier et un emplacement pour le fichier de type sac à DOS  |

    ![Exporter une application de couche données dans le menu tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importer un type sac à DOS pour SQL Azure de base de données à l’aide de SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importer un type sac à DOS pour SqlPackage de base de données SQL Azure](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importer un type sac à DOS pour portail Azure de base de données SQL Azure](sql-database-import.md)
- [Importer un type sac à DOS pour PowerShell de base de données SQL Azure](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migration des bases de données SQL Server non - à l’aide de l’Assistant Migration de SQL Server](http://blogs.msdn.com/b/ssma/)
