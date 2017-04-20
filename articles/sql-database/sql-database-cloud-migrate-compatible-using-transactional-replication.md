<properties
   pageTitle="Migrer vers une base de données SQL à l’aide de la réplication transactionnelle | Microsoft Azure"
   description="Base de données de SQL Microsoft Azure, migration de base de données, Importer base de données, la réplication transactionnelle"
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
   ms.date="08/23/2016"
   ms.author="carlrab"/>

# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Migration de la base de données SQL Server pour la base de données de SQL Azure à l’aide de la réplication transactionnelle

> [AZURE.SELECTOR]
- [Assistant de Migration de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exporter vers un fichier de type sac à DOS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importer depuis un fichier de type sac à DOS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Réplication transactionnelle](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Dans cet article, vous apprenez à migrer une base de données SQL Server compatible à la base de données de SQL Azure avec très peu de temps à l’aide de la réplication transactionnelle de SQL Server.

## <a name="understanding-the-transactional-replication-architecture"></a>Présentation de l’architecture de la réplication transactionnelle

Lorsque vous ne peut pas vous permettre de supprimer de votre base de données SQL Server de production pendant la migration, vous pouvez utiliser la réplication transactionnelle de SQL Server comme solution de migration. Pour utiliser cette solution, vous configurez votre base de données de SQL Azure en tant qu’abonné à l’instance locale de SQL Server que vous souhaitez migrer. Sur site distributeur de réplication transactionnelle synchronise les données de la base de données locale à synchroniser (l’éditeur) alors que les nouvelles transactions continuent de se produire. 

Vous pouvez également utiliser la réplication transactionnelle pour faire migrer un sous-ensemble de votre base de données sur site. La composition que vous répliquez à la base de données de SQL Azure peut être limitée à un sous-ensemble des tables dans la base de données en cours de réplication. Pour chaque table répliquée, vous pouvez limiter les données à un sous-ensemble de lignes ou un sous-ensemble de colonnes.

Avec la réplication transactionnelle, toutes les modifications apportées à vos données ou de schéma s’affiche dans votre base de données de SQL Azure. Une fois que la synchronisation est terminée et vous êtes prêt à migrer, modifiez la chaîne de connexion de vos applications afin de les pointer vers votre base de données de SQL Azure. Une fois la réplication transactionnelle s’échappe des modifications sur votre base de données sur site et toutes vos applications point sur base de données Azure, vous pouvez désinstaller la réplication transactionnelle. Votre base de données de SQL Azure est à présent votre système de production.

 ![Diagramme de SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="transactional-replication-requirements"></a>Exigences en matière de réplication transactionnelles

La réplication transactionnelle est une technologie intégrées à SQL Server depuis SQL Server 6.5. Il s’agit d’une technologie mature et éprouvée que la plupart des administrateurs connaître avec lesquelles ils ont l’expérience. Avec le [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server), il est désormais possible de configurer votre base de données de SQL Azure en tant qu' [abonné de réplication transactionnelle](https://msdn.microsoft.com/library/mt589530.aspx) à votre publication sur site. L’expérience que vous obtenez sa configuration à partir de Management Studio est le même que si vous définissez un abonné de réplication transactionnelle sur un serveur local. Prise en charge de ce scénario est pris en charge lorsque l’éditeur et le distributeur sont au moins une des versions suivantes de SQL Server :

 - SQL Server 2016 et versions supérieures 
 - CU3 de SQL Server 2014 SP1 et versions ultérieures
 - SQL Server 2014 RTM CU10 et versions ultérieures
 - CU8 de SQL Server 2012 SP2 et versions ultérieures
 - Le Service Pack 3 de SQL Server 2012 et les versions ultérieures


> [AZURE.IMPORTANT] La dernière version de SQL Server Management Studio permet de rester synchronisé avec les mises à jour Microsoft Azure et de base de données SQL. Des versions antérieures de SQL Server Management Studio ne peut pas définir de la base de données SQL comme un abonné. [Mise à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Réplication transactionnelle](https://msdn.microsoft.com/library/mt589530.aspx)
- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migration des bases de données SQL Server non - à l’aide de l’Assistant Migration de SQL Server](http://blogs.msdn.com/b/ssma/)
