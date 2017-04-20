<properties
   pageTitle="Configurer une vue d’ensemble SQL serveur pare-feu | Microsoft Azure"
   description="Apprenez à configurer un pare-feu de base de données SQL avec des règles de pare-feu au niveau du serveur et au niveau de la base de données pour gérer l’accès."
   keywords="pare-feu de base de données"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# <a name="configure-azure-sql-database-firewall-rules---overview"></a>Configurer les règles de pare-feu de base de données de SQL Azure \- vue d’ensemble


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-firewall-configure.md)
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Base de données SQL de Microsoft Azure fournit un service de base de données relationnelle pour Azure et d’autres applications basées sur Internet. Pour protéger vos données, pare-feux empêche tous les accès à votre serveur de base de données jusqu'à ce que vous spécifiez les ordinateurs ayant une autorisation. Le pare-feu accorde l’accès aux bases de données en fonction de l’adresse IP d’origine de chaque demande.

Pour configurer votre pare-feu, vous créez des règles de pare-feu qui spécifient les plages d’adresses acceptables. Vous pouvez créer des règles de pare-feu au niveau du serveur et de base de données.

- **Règles de pare-feu au niveau du serveur :** Ces règles permettent aux clients d’accéder l’intégralité du serveur SQL d’Azure, c'est-à-dire que toutes les bases de données au sein du même serveur logique. Ces règles sont stockées dans la base de données **master** . Règles de pare-feu de niveau serveur peuvent être configurés à l’aide du portail ou en utilisant des instructions Transact-SQL.
- **Règles de pare-feu au niveau de la base de données :** Ces règles permettent aux clients d’accéder aux bases de données individuelles au sein de votre serveur de base de données de SQL Azure. Vous pouvez créer ces règles pour chaque base de données, et ils sont stockés dans les bases de données individuelles. (Vous pouvez créer des règles de pare-feu au niveau de la base de données pour la base de données **master** ). Ces règles peuvent être utiles pour restreindre l’accès à certaines bases de données de (sécurisés) au sein du même serveur logique. Les règles de pare-feu au niveau de la base de données peuvent uniquement être configurés en utilisant des instructions Transact-SQL.

**Recommandation :** Microsoft recommande l’utilisation de règles de pare-feu au niveau de la base de données chaque fois que possible pour améliorer la sécurité et de rendre votre base de données plus portable. Utiliser des règles de pare-feu au niveau du serveur pour les administrateurs et lorsque vous avez plusieurs bases de données aient les mêmes besoins d’accès et que vous ne souhaitez pas consacrer de temps individuellement la configuration de chaque base de données.


## <a name="firewall-overview"></a>Vue d’ensemble du pare-feu

Initialement, tous les accès de Transact-SQL à votre serveur Azure SQL est bloqué par le pare-feu. Pour commencer à utiliser votre serveur Azure SQL, vous devez atteindre le portail Azure et spécifier une ou plusieurs règles au niveau du serveur pare-feu qui permettent l’accès à votre serveur SQL d’Azure. Les règles de pare-feu permet de spécifier les plages d’adresses IP à partir d’Internet sont autorisés, et si les applications Azure peuvent essayer de se connecter à votre serveur Azure SQL.

Pour accorder l’accès à une des bases de données de votre serveur Azure SQL de manière sélective, vous devez créer une règle de niveau de base de données pour la base de données requis. Spécifiez une plage d’adresses IP pour la règle de pare-feu de base de données est au-delà de la plage d’adresses IP spécifiée dans la règle de pare-feu au niveau du serveur et vérifiez que l’adresse IP du client se situe dans la plage spécifiée dans la règle de niveau de base de données.

Les tentatives de connexion à partir d’Internet et Azure doivent tout d’abord passer par le pare-feu avant d’atteindre votre serveur d’Azure SQL ou de la base de données de SQL, comme illustré dans le diagramme suivant.

   ![Diagramme décrivant configuration du pare-feu.][1]

## <a name="connecting-from-the-internet"></a>Connexion à partir d’Internet

Lorsqu’un ordinateur tente de se connecter à votre serveur de base de données à partir d’Internet, le pare-feu vérifie d’abord l’adresse IP d’origine de la demande par rapport à l’ensemble de règles de pare-feu :

- Si l’adresse IP de la demande est dans une des plages spécifiées dans les règles de pare-feu au niveau du serveur, la connexion est accordée à votre serveur de base de données de SQL Azure.

- Si l’adresse IP de la demande n’est pas dans une des plages spécifiées dans la règle de pare-feu au niveau du serveur, les règles de pare-feu au niveau de la base de données sont vérifiés. Si l’adresse IP de la demande est dans une des plages spécifiées dans les règles de pare-feu au niveau de la base de données, la connexion est accordée uniquement à la base de données qui possède une règle de correspondance au niveau de la base de données.

- Si l’adresse IP de la demande n’est pas inclus dans la plage spécifiée dans les règles de pare-feu au niveau du serveur ou de niveau de base de données, la demande de connexion échoue.

> [AZURE.NOTE] Pour accéder à base de données de SQL Azure à partir de votre ordinateur local, assurez-vous que le pare-feu de votre réseau et votre ordinateur local permet les communications sortantes sur le port TCP 1433.


## <a name="connecting-from-azure"></a>Connexion à partir d’Azure

Lorsqu’une application dans Azure tente de se connecter à votre serveur de base de données, le pare-feu vérifie que Azure connexions sont autorisées. Un pare-feu avec début et de fin d’adresse égale à 0.0.0.0 indique que ces connexions sont autorisées. Si la tentative de connexion n’est pas autorisée, la demande n’atteint pas le serveur de base de données de SQL Azure.

> [AZURE.IMPORTANT] Cette option configure le pare-feu pour autoriser toutes les connexions à partir de connexions Azure, y comprises les abonnements des autres clients. Lorsque vous sélectionnez cette option, assurez-vous que votre nom d’utilisateur et les autorisations utilisateur limitent l’accès aux seuls utilisateurs autorisés.

Vous pouvez activer les connexions à partir d’Azure de deux manières :

- Dans le [portail Microsoft Azure](https://portal.azure.com/), activez la case à cocher **Autoriser les services azure au serveur d’accès** lors de la création d’un nouveau serveur.

- Dans le [portail de classique](http://go.microsoft.com/fwlink/p/?LinkID=161793), à partir de l’onglet **configurer** sur un serveur, dans la section **Services d’autorisés** , cliquez sur **Oui** pour les **Services de Microsoft Azure**.

## <a name="creating-the-first-server-level-firewall-rule"></a>Création de la première règle de pare-feu au niveau du serveur

Le premier paramètre au niveau du serveur pare-feu peut être créé à l’aide du [portail Azure](https://portal.azure.com/) ou par programme à l’aide de l’API REST ou Azure PowerShell. Les règles suivantes au niveau du serveur pare-feu peuvent être créées et gérées à l’aide de ces méthodes, ainsi que par le biais de Transact-SQL. Pour améliorer les performances, les règles de pare-feu de niveau serveur sont temporairement mis en cache au niveau de la base de données. Pour actualiser le cache, voir [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Pour plus d’informations sur les règles de pare-feu de niveau serveur, reportez-vous à la section [Comment : configurer un pare-feu de serveur Azure SQL via le portail Azure](sql-database-configure-firewall-settings.md).

## <a name="creating-database-level-firewall-rules"></a>Création de règles de pare-feu au niveau de la base de données

Après avoir configuré le premier pare-feu au niveau du serveur, vous souhaiterez peut-être restreindre l’accès à certaines bases de données. Si vous spécifiez une plage d’adresses IP dans la règle de pare-feu au niveau de la base de données qui est en dehors de la plage spécifiée dans la règle de pare-feu de niveau serveur, seuls les clients qui ont des adresses IP dans la plage de niveau de base de données peuvent accéder à la base de données. Vous pouvez avoir un maximum de 128 au niveau de la base de données des règles de pare-feu pour une base de données. Les règles de pare-feu au niveau de la base de données pour les bases de données master et utilisateur peuvent être créés et gérés par le biais de Transact-SQL. Pour plus d’informations sur la configuration des règles de pare-feu au niveau de la base de données, consultez [sp_set_database_firewall_rule (bases de données de SQL Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

## <a name="programmatically-managing-firewall-rules"></a>Gestion par programmation des règles de pare-feu

Outre le portail Azure, les règles de pare-feu peuvent être gérés par programmation à l’aide de Transact-SQL, API REST et Azure PowerShell. Les tableaux suivants décrivent l’ensemble des commandes disponibles pour chaque méthode.


### <a name="transact-sql"></a>Transact-SQL

| Vue de catalogue ou d’une procédure stockée                                                           | Niveau     | Description                                          |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [Sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx)                   | Serveur    | Affiche les règles de pare-feu au niveau du serveur en cours     |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx)             | Serveur    | Crée ou met à jour les règles de pare-feu de niveau serveur       |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)          | Serveur    | Supprime les règles de pare-feu de niveau serveur                  |
| [Sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx)        | Base de données  | Affiche les règles de pare-feu au niveau de la base de données en cours   |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx)    | Base de données  | Crée ou met à jour les règles de pare-feu au niveau de la base de données |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Bases de données | Supprime les règles de pare-feu au niveau de la base de données                |

### <a name="rest-api"></a>API REST

| API                  | Niveau  | Description                                                      |
|----------------------|--------|------------------------------------------------------------------|
| [Liste des règles de pare-feu](https://msdn.microsoft.com/library/azure/dn505715.aspx)  | Serveur | Affiche les règles de pare-feu au niveau du serveur en cours                 |
| [Créer la règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Serveur | Crée ou met à jour les règles de pare-feu de niveau serveur                   |
| [Définir la règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505707.aspx)    | Serveur | Met à jour les propriétés d’une règle de pare-feu au niveau du serveur existant |
| [Supprimer la règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Serveur | Supprime les règles de pare-feu de niveau serveur                              |


### <a name="azure-powershell"></a>PowerShell Azure

| Applet de commande                                                                                              | Niveau  | Description                                                      |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)    | Serveur | Retourne les règles de pare-feu au niveau du serveur en cours                  |
| [Nouvelle-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)    | Serveur | Crée une nouvelle règle de pare-feu au niveau du serveur                         |
| [Ensemble-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)    | Serveur | Met à jour les propriétés d’une règle de pare-feu au niveau du serveur existant |
| [Supprimer-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Serveur | Supprime les règles de pare-feu de niveau serveur                              |

> [AZURE.NOTE] Il peut y avoir des comme délai de cinq minutes pour que les modifications aux paramètres du pare-feu prennent effet.

## <a name="troubleshooting-the-database-firewall"></a>Dépannage du pare-feu de base de données

Tenez compte des points suivants lors de l’accès au service de base de données SQL de Microsoft Azure ne se comporte pas comme prévu :

- **Configuration du pare-feu local :** Avant de la base de données de SQL Azure peut accéder à votre ordinateur, vous devrez créer une exception de pare-feu sur votre ordinateur pour le port TCP 1433. Si vous effectuez des connexions à l’intérieur de la limite d’Azure cloud, vous devrez ouvrir des ports supplémentaires. Pour plus d’informations, consultez la **V12 de base de données de SQL : à l’extérieur et à l’intérieur de** section de [Ports autres que le 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Réseau de traduction d’adresses (NAT) :** En raison de NAT, l’adresse IP utilisée par votre ordinateur pour vous connecter à la base de données de SQL Azure peut être différente de l’adresse IP indiquée dans les paramètres de configuration IP de votre ordinateur. Pour afficher l’adresse IP à l’aide de votre ordinateur pour vous connecter à Azure, connectez-vous au portail et accédez à l’onglet **configurer** sur le serveur qui héberge votre base de données. Sous la section **Autorisé des adresses IP** , l' **Adresse IP du Client en cours** s’affiche. Cliquez sur **Ajouter** **Des adresses IP autorisées** pour permettre à cet ordinateur pour accéder au serveur.

- **Les modifications apportées à la liste verte n’ont pas pris effet encore :** Il peut y avoir comme délai de cinq minutes pour que les modifications à la configuration du pare-feu de base de données de SQL Azure prennent effet.

- **La connexion n’est pas autorisée ou un mot de passe incorrect a été utilisé :** Si une connexion n’a pas d’autorisations sur le serveur de base de données de SQL Azure, ou le mot de passe est incorrect, la connexion au serveur de base de données de SQL Azure est refusée. Création d’un paramètre de pare-feu fournit uniquement des clients à une opportunité pour essayer de se connecter à votre serveur. chaque client doit fournir les informations d’identification de sécurité nécessaires. Pour plus d’informations sur la préparation des connexions, voir gérer les bases de données, les connexions et les utilisateurs dans la base de données de SQL Azure.

- **Adresse IP dynamique :** Si vous avez une connexion Internet avec un adressage IP dynamique et que vous rencontrez des problèmes lors de l’obtention par le biais du pare-feu, vous pouvez essayer une des solutions suivantes :

 - Demandez à votre fournisseur de services Internet (ISP, Internet Service Provider) pour la plage d’adresses IP attribuée aux ordinateurs clients qui accèdent au serveur de base de données de SQL Azure et ajoutez la plage d’adresses IP sous la forme d’une règle de pare-feu.

 - Obtenir les adresses IP statiques à la place de vos ordinateurs clients et ajoutez les adresses IP en tant que règles de pare-feu.

## <a name="next-steps"></a>Étapes suivantes

Procédure pour les articles sur la création de règles de pare-feu au niveau du serveur et au niveau de la base de données, consultez :

- [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure via le portail Azure](sql-database-configure-firewall-settings.md)
- [Configurer des règles de pare-feu au niveau du serveur et au niveau de la base de données de base de données de SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md)
- [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurer des règles de pare-feu au niveau du serveur de base de données de SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

Pour un didacticiel sur la création d’une base de données, voir [Création d’une base de données SQL en minutes en utilisant le portail Azure](sql-database-get-started.md).
Pour une aide sur la connexion à une base de données Azure SQL à partir de la source ouverte ou des applications tierces, voir [exemples de code de base de données SQL-démarrage rapide du Client](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Pour comprendre comment accéder à des bases de données, consultez [gestion de la sécurité d’accès et de connexion de base de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).



## <a name="additional-resources"></a>Ressources supplémentaires

- [Sécurisation de votre base de données](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et de base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
