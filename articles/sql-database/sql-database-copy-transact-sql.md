<properties 
    pageTitle="Copier une base de données Azure SQL à l’aide de Transact-SQL | Microsoft Azure" 
    description="Créer une copie d’une base de données Azure SQL à l’aide de Transact-SQL" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/19/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Copier une base de données Azure SQL à l’aide de Transact-SQL


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-copy.md)
- [Azure portal](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)


Cette étapes suivantes vous indiquent comment copier une base de données SQL avec Transact-SQL sur le même serveur ou sur un autre serveur. L’opération de copie de base de données utilise l’instruction [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) .

Pour terminer la procédure décrite dans cet article, vous devez les éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure simplement cliquez sur **Version d’évaluation gratuite** en haut de cette page, puis revenir à la fin de cet article.
- Une base de données SQL Azure. Si vous ne disposez pas d’une base de données SQL, créez-en un en suivant les étapes décrites dans cet article : [créer votre première base de données de SQL Azure](sql-database-get-started.md).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Si vous n’avez pas SSMS, ou si les fonctionnalités décrites dans cet article ne sont pas disponibles, [Téléchargez la dernière version](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="copy-your-sql-database"></a>Copiez votre base de données SQL

Connectez-vous à la base de données master à l’aide de la connexion d’accès au niveau du serveur principale ou de la connexion qui a créé la base de données que vous souhaitez copier. Les connexions qui ne sont pas à l’entité de sécurité au niveau du serveur doivent être membres du rôle dbmanager afin de copier des bases de données. Pour plus d’informations sur les noms d’accès et de la connexion au serveur, voir [Gérer les connexions](sql-database-manage-logins.md).

Démarrez la copie de la base de données source avec l’instruction [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . L’exécution de cette instruction démarre la base de données, processus de copie. Copie d’une base de données étant un processus asynchrone, l’instruction CREATE DATABASE renvoie avant la fin de la base de données copie.


### <a name="copy-a-sql-database-to-the-same-server"></a>Copier une base de données SQL sur le même serveur

Connectez-vous à la base de données master à l’aide de la connexion d’accès au niveau du serveur principale ou de la connexion qui a créé la base de données que vous souhaitez copier. Les connexions qui ne sont pas à l’entité de sécurité au niveau du serveur doivent être membres du rôle dbmanager afin de copier des bases de données.

Cette copie de commande Database1 à une nouvelle base de données nommée Base2 sur le même serveur. Selon la taille de votre base de données de l’opération de copie peut prendre un certain temps pour terminer.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copier une base de données SQL vers un autre serveur

Connectez-vous à la base de données master du serveur de destination, le serveur de base de données de SQL Azure où la nouvelle base de données doit être créée. Utiliser une connexion qui a le même nom et le mot de passe que le propriétaire de la base de données (DBO) de la base de données source sur le serveur de base de données de SQL Azure source. L’ouverture de session sur le serveur de destination doit également être un membre du rôle dbmanager ou la connexion d’accès au niveau du serveur principale.

Cette commande copie Database1 sur server1 - d’une nouvelle base de données nommée Base2 sur server2. Selon la taille de votre base de données de l’opération de copie peut prendre un certain temps pour terminer.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## <a name="monitor-the-progress-of-the-copy-operation"></a>Surveiller la progression de l’opération de copie

Surveiller le processus de copie en interrogeant les vues sys.databases et sys.dm_database_copies. Lorsque la copie est en cours, la colonne state_desc de la vue sys.databases pour la nouvelle base de données est définie pour la copie.


- Si la copie échoue, la colonne state_desc de la vue sys.databases pour la nouvelle base de données a la valeur suspecte. Dans ce cas, exécutez l’instruction DROP sur la nouvelle base de données et réessayer plus tard.
- Si la copie réussit, la colonne state_desc de la vue sys.databases pour la nouvelle base de données est définie en ligne. Dans ce cas, la copie est terminée et que la nouvelle base de données est une base de données, peuvent être modifiés indépendamment de la base de données source.

> [AZURE.NOTE] -Si vous décidez d’annuler la copie alors qu’il est en cours, exécutez l’instruction [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) sur la nouvelle base de données. Vous pouvez également l’exécution de l’instruction DROP DATABASE sur la base de données source annule également le processus de copie.


## <a name="resolve-logins-after-the-copy-operation-completes"></a>Résoudre les connexions d’accès à l’issue de l’opération de copie

Une fois la nouvelle base de données en ligne sur le serveur de destination, utilisez l’instruction [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) pour remapper les utilisateurs à partir de la nouvelle base de données pour les connexions d’accès sur le serveur de destination. Pour résoudre les problèmes d’utilisateurs orphelins, consultez [Dépannage des utilisateurs orphelins](https://msdn.microsoft.com/library/ms175475.aspx). Voir aussi [comment gérer la sécurité de base de données Azure SQL après un sinistre](sql-database-geo-replication-security-config.md).

Tous les utilisateurs dans la nouvelle base de données mettre à jour les autorisations qu’il avait dans la base de données source. L’utilisateur qui a lancé la copie de la base de données devient le propriétaire de la base de données de la nouvelle base de données et reçoit un nouvel identificateur de sécurité (SID). Une fois la copie effectuée et avant que les autres utilisateurs sont remappés, seule la connexion qui a initié la copie, le propriétaire de la base de données (DBO), pouvez vous connecter à la nouvelle base de données.


## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la copie d’une base de données de SQL Azure, consultez [copie d’une base de données SQL d’Azure](sql-database-copy.md) .
- Voir la [copie d’une base de données Azure SQL via le portail Azure](sql-database-copy-portal.md) pour copier une base de données à l’aide du portail Azure.
- Voir la [copie d’une base de données Azure SQL à l’aide de PowerShell](sql-database-copy-powershell.md) pour copier une base de données à l’aide de PowerShell.
- Voir [comment gérer la sécurité de base de données Azure SQL après un sinistre](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et des connexions d’accès lors de la copie d’une base de données vers un autre serveur logique.



## <a name="additional-resources"></a>Ressources supplémentaires

- [Gestion des accès](sql-database-manage-logins.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données pour un type sac à DOS](sql-database-export.md)
- [Vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- [Documentation de la base de données de SQL](https://azure.microsoft.com/documentation/services/sql-database/)


