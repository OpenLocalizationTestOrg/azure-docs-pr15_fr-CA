<properties
    pageTitle="Règles de pare-feu au niveau du serveur et de base de données au niveau de la base de données SQL Azure à l’aide de T-SQL | Microsoft Azure"
    description="Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL d’Azure."
    services="sql-database"
    documentationCenter=""
    authors="BYHAM"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="rickbyh"/>


# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Configurer des règles de pare-feu au niveau du serveur et au niveau de la base de données de base de données de SQL Azure à l’aide de T-SQL


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-firewall-configure.md)
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Base de données SQL de Microsoft Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et les bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour la forme de base ou d’une base de données utilisateur sur votre serveur de base de données de SQL Azure pour autoriser l’accès à la base de données de façon sélective.

> [AZURE.IMPORTANT] Pour autoriser les applications Azure pour vous connecter à votre serveur de base de données, connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de base de données SQL Azure](sql-database-firewall-configure.md). Si vous effectuez des connexions à l’intérieur de la limite d’Azure cloud, vous devrez peut-être ouvrir certains ports TCP supplémentaires. Pour plus d’informations, consultez la **V12 de base de données de SQL : à l’extérieur et à l’intérieur de** section de [Ports autres que le 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="server-level-firewall-rules"></a>Règles de pare-feu de niveau serveur

Uniquement la connexion d’accès au niveau du serveur principale ou administrateur Azure Active Directory peut créer une règle de pare-feu de niveau serveur à l’aide de Transact-SQL.

1. Lancez une fenêtre de requête et de se connecter à la base de données master virtuel à l’aide de SQL Server Management Studio.
2. Règles de pare-feu de niveau serveur peuvent être sélectionnés, créés, mis à jour ou supprimés à partir de la fenêtre de requête.
3. Pour créer ou mettre à jour les règles de pare-feu au niveau du serveur, exécuter la `sp_set_firewall_rule` une procédure stockée. L’exemple suivant active une plage d’adresses IP sur le serveur de Contoso.<br/>Commencez par voir quelles règles existent déjà.

        SELECT * FROM sys.firewall_rules ORDER BY name;

    Ensuite, ajoutez une règle de pare-feu.

        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

    Pour supprimer une règle de pare-feu au niveau du serveur, exécutez la procédure stockée de sp_delete_firewall_rule. L’exemple suivant supprime la règle ContosoFirewallRule.
 
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Pour plus d’informations sur ces procédures stockées, consultez [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) et [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Règles de pare-feu au niveau de la base de données

Seul un utilisateur de base de données avec l’autorisation de **contrôle** sur la base de données (par exemple, le propriétaire de la base de données) peut créer une règle de pare-feu au niveau de la base de données.

1. Après avoir créé un pare-feu au niveau du serveur pour votre adresse IP, lancez une fenêtre de requête par le biais du portail standard ou SQL Server Management Studio.
2. Se connecter à la base de données pour lequel vous souhaitez créer une règle de pare-feu au niveau de la base de données.

    Pour créer un nouveau ou mettre à jour d’une règle de pare-feu au niveau de la base de données existante, vous devez exécuter la `sp_set_database_firewall_rule` une procédure stockée. L’exemple suivant crée une nouvelle règle de pare-feu nommée ContosoFirewallRule.
 
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
    Pour supprimer une règle de pare-feu au niveau de la base de données existante, vous devez exécuter la `sp_delete_database_firewall_rule` une procédure stockée. L’exemple suivant supprime la règle ContosoFirewallRule.
`
   EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Pour plus d’informations sur ces procédures stockées, consultez [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) et [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## <a name="next-steps"></a>Étapes suivantes

Pour les articles sur la création de règles de pare-feu de niveau serveur à l’aide d’autres méthodes, consultez : 

- [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure via le portail Azure](sql-database-configure-firewall-settings.md)
- [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

Pour un didacticiel sur la création d’une base de données, voir [Création d’une base de données SQL en minutes en utilisant le portail Azure](sql-database-get-started.md).
Pour une aide sur la connexion à une base de données Azure SQL à partir de la source ouverte ou des applications tierces, voir [exemples de code de base de données SQL-démarrage rapide du Client](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Pour comprendre comment accéder à des bases de données, consultez [gestion de la sécurité d’accès et de connexion de base de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Ressources supplémentaires

- [Sécurisation de votre base de données](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et de base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)
