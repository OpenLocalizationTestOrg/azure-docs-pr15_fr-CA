
<properties
   pageTitle="Exportation d’une base de données SQL Server vers un fichier de type sac à DOS à l’aide de SQL Server Management Studio | Microsoft Azure"
   description="Base de données de SQL Microsoft Azure, migration de base de données, exporter la base de données, exporter un fichier de type sac à DOS, Assistant Exporter une Application de couche données"
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
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Exportation d’une base de données SQL Server vers un fichier de type sac à DOS à l’aide de SQL Server Management Studio

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
Cet article explique comment exporter une base de données SQL Server vers un fichier de [type sac à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de l’Assistant Exportation d’une Application de couche données dans SQL Server Management Studio. 

1. Vérifiez que vous disposez de la dernière version de SQL Server Management Studio. Nouvelles versions de Management Studio sont mises à jour mensuelles de rester synchronisé avec les mises à jour sur le portail Azure.

     > [AZURE.IMPORTANT] Il est recommandé de toujours utiliser la dernière version du Management Studio reste synchronisé avec les mises à jour Microsoft Azure et de base de données SQL. [Mise à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Ouvrez Management Studio et connectez-vous à votre base de données source dans l’Explorateur d’objets.

    ![Exporter une application de couche données dans le menu tâches](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Avec le bouton droit de la base de données source dans l’Explorateur d’objets, pointez sur **tâches**et cliquez sur **Exporter les applications de couche données**

    ![Exporter une application de couche données dans le menu tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Dans l’Assistant Exportation, configurer l’exportation pour enregistrer le fichier de type sac à DOS soit un emplacement de disque local ou sur un Azure blob. Sac à DOS l’exporté inclut toujours le schéma de base de données complète et, par défaut, les données de toutes les tables. Utilisez l’onglet Avancé si vous souhaitez exclure certaines ou toutes les tables de données. Vous pourriez, par exemple, choisir d’exporter uniquement les données des tables de référence plutôt qu’à partir de toutes les tables.

***Important*** Lorsque vous exportez un type sac à DOS pour le stockage des objets blob Azure, utiliser le stockage standard. Importation d’un type sac à DOS à partir du stockage de la prime n’est pas pris en charge.

    ![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


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
