<properties
    pageTitle="Règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de l’API REST | Microsoft Azure"
    description="Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL d’Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="08/09/2016"
    ms.author="sstein"/>


#  <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de l’API REST


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-firewall-configure.md)
- [Azure portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Base de données SQL de Microsoft Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et les bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour la forme de base ou d’une base de données utilisateur sur votre serveur de base de données de SQL Azure pour autoriser l’accès à la base de données de façon sélective.

> [AZURE.IMPORTANT] Pour autoriser les applications Azure pour vous connecter à votre serveur de base de données, connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de base de données SQL Azure](sql-database-firewall-configure.md). Si vous effectuez des connexions à l’intérieur de la limite d’Azure cloud, vous devrez peut-être ouvrir certains ports TCP supplémentaires. Pour plus d’informations, consultez la **V12 de base de données de SQL : à l’extérieur et à l’intérieur de** section de [Ports autres que le 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Gérer les règles de pare-feu au niveau du serveur via l’API REST
1. Gestion des règles de pare-feu via l’API REST doivent être authentifiés. Pour plus d’informations, consultez le [guide du développeur d’autorisation avec l’API du Gestionnaire de ressources Azure](../resource-manager-api-authentication.md).
2. Les règles de niveau serveur peuvent être créés, mis à jour ou supprimées à l’aide des API REST

    Pour créer ou mettre à jour une règle au niveau du serveur pare-feu, exécutez la méthode PUT à l’aide de la commande suivante :
 
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
    
    Corps de la demande

        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 
 

    Pour supprimer une règle de pare-feu au niveau du serveur existant, exécutez la méthode DELETE à l’aide de la commande suivante :
     
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Gérer les règles de pare-feu à l’aide de l’API REST

* [Créer ou mettre à jour la règle de pare-feu](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Supprimer la règle de pare-feu](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Obtenir la règle de pare-feu](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Liste de toutes les règles de pare-feu](https://msdn.microsoft.com/library/azure/mt604478.aspx)
 
## <a name="next-steps"></a>Étapes suivantes

Pour une procédure de l’article sur l’utilisation de Transact-SQL pour créer des règles de pare-feu au niveau du serveur et au niveau de la base de données, consultez [règles de pare-feu au niveau du serveur et au niveau de la base de données de configuration de base de données SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Pour les articles sur la création de règles de pare-feu de niveau serveur à l’aide d’autres méthodes, consultez : 

- [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure via le portail Azure](sql-database-configure-firewall-settings.md)
- [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)

Pour un didacticiel sur la création d’une base de données, voir [Création d’une base de données SQL en minutes en utilisant le portail Azure](sql-database-get-started.md).
Pour une aide sur la connexion à une base de données Azure SQL à partir de la source ouverte ou des applications tierces, voir [exemples de code de base de données SQL-démarrage rapide du Client](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Pour comprendre comment accéder à des bases de données, consultez [gestion de la sécurité d’accès et de connexion de base de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Ressources supplémentaires

- [Sécurisation de votre base de données](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et de base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 
