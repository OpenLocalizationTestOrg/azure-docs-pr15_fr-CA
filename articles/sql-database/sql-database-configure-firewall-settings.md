<properties
    pageTitle="Configurer une règle de pare-feu au niveau du serveur de base de données SQL | Microsoft Azure"
    description="Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent Azure SQL server."
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
    ms.author="rickbyh;carlrab"/>


# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>Configurer une règle de pare-feu au niveau du serveur de base de données de SQL Azure en utilisant le portail Azure


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-firewall-configure.md)
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)

Serveur SQL Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et les bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour la forme de base ou d’une base de données de l’utilisateur dans votre serveur logique du serveur Azure SQL pour autoriser l’accès à la base de données de façon sélective. Cette rubrique décrit les règles de pare-feu de niveau serveur.

> [AZURE.IMPORTANT] Pour autoriser les applications à se connecter à votre serveur Azure SQL Azure, connexions Azure doivent être activées. Pour comprendre comment des règles de pare-feu, consultez [Comment faire pour configurer un pare-feu de serveur Azure SQL \- vue d’ensemble de](sql-database-firewall-configure.md). Si vous effectuez des connexions à l’intérieur de la limite d’Azure cloud, vous devrez peut-être ouvrir certains ports TCP supplémentaires. Pour plus d’informations, consultez la **V12 de base de données de SQL : à l’extérieur et à l’intérieur de** section de [Ports autres que le 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

**Recommandation :** Utiliser des règles de pare-feu au niveau du serveur pour les administrateurs et lorsque vous avez plusieurs bases de données aient les mêmes besoins d’accès, et que vous ne souhaitez pas consacrer de temps individuellement la configuration de chaque base de données. Microsoft recommande l’utilisation de règles de pare-feu au niveau de la base de données chaque fois que possible, pour améliorer la sécurité et de rendre votre base de données plus portable.

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gérer les règles de pare-feu de niveau serveur existantes via le portail Azure

Répétez les étapes pour gérer les règles de pare-feu au niveau du serveur.

- Pour ajouter l’ordinateur actuel, cliquez sur Ajouter IP du client.
- Pour ajouter des adresses IP supplémentaires, tapez dans le nom de la règle, une adresse IP de début et une adresse IP de fin.
- Pour modifier une règle existante, cliquez sur un des champs de la règle et les modifier.
- Pour supprimer une règle existante, placez le pointeur sur la règle jusqu'à ce que le X apparaît à la fin de la ligne. Cliquez sur X pour supprimer la règle.

Cliquez sur **Enregistrer** pour enregistrer les modifications.

## <a name="next-steps"></a>Étapes suivantes

Pour une procédure de l’article sur l’utilisation de Transact-SQL pour créer des règles de pare-feu au niveau du serveur et au niveau de la base de données, consultez [règles de pare-feu au niveau du serveur et au niveau de la base de données de configuration de base de données SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Pour les articles sur la création de règles de pare-feu de niveau serveur à l’aide d’autres méthodes, consultez : 

- [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

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

 
