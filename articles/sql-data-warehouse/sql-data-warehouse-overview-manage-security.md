<properties
   pageTitle="Sécuriser une base de données SQL Data Warehouse | Microsoft Azure"
   description="Conseils sur la sécurisation d’une base de données dans l’entrepôt de données SQL Azure pour le développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="secure-a-database-in-sql-data-warehouse"></a>Sécuriser une base de données SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d’ensemble de la sécurité](sql-data-warehouse-overview-manage-security.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Cryptage (portail)](sql-data-warehouse-encryption-tde.md)
- [Cryptage (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Cet article décrit les principes fondamentaux de la sécurisation de votre base de données de l’entrepôt de données SQL Azure. En particulier, cet article va vous aider à démarrer avec les ressources pour limiter l’accès, la protection des données et la surveillance des activités sur une base de données.

## <a name="connection-security"></a>Sécurité de connexion

Sécurité de connexion fait référence à la restreindre et les connexions sécurisées à votre base de données à l’aide de règles de pare-feu et de chiffrement de la connexion.

Règles de pare-feu sont utilisés par le serveur et la base de données pour rejeter les tentatives de connexion à partir d’adresses IP qui n’ont pas été explicitement autorisés. Pour autoriser les connexions à partir de votre application ou l’adresse IP publique de l’ordinateur client, vous devez d’abord créer une règle de pare-feu au niveau du serveur à l’aide du portail Azure, API REST ou PowerShell. Pour obtenir les meilleurs résultats, vous devez restreindre les plages d’adresses IP autorisées par votre pare-feu de serveur autant que possibles.  Pour accéder à Azure SQL Data Warehouse à partir de votre ordinateur local, assurez-vous que le pare-feu de votre réseau et votre ordinateur local permet les communications sortantes sur le port TCP 1433.  Pour plus d’informations, voir [pare-feu de base de données de SQL Azure][], [sp_set_firewall_rule][]et [sp_set_database_firewall_rule][].

Par défaut, les connexions à votre magasin de données SQL sont cryptées.  Pour désactiver le cryptage, la modification des paramètres de connexion sont ignorées.

## <a name="authentication"></a>Authentification

L’authentification désigne comment vous prouver votre identité lors de la connexion à la base de données. SQL Data Warehouse prend actuellement en charge l’authentification SQL Server avec un nom d’utilisateur et mot de passe, ainsi qu’un Azure d’Active Directory. 

Lorsque vous avez créé le serveur logique pour votre base de données, vous avez spécifié une connexion « server admin » avec un nom d’utilisateur et un mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier à toute base de données sur ce serveur en tant que le propriétaire de la base de données, ou « dbo » par le biais de l’authentification SQL Server.

Toutefois, pour obtenir les meilleurs résultats, les utilisateurs de votre organisation doivent utiliser un compte différent pour l’authentification. De cette façon, vous pouvez limiter les autorisations accordées à l’application et réduire les risques des activités malveillantes dans le cas où votre code d’application est vulnérable à une attaque d’injection SQL. 

Pour créer un utilisateur authentifié de SQL Server, se connecter à la base de données **master** sur votre serveur avec votre nom d’utilisateur admin server et créez une nouvelle connexion serveur.  En outre, il est conseillé de créer un utilisateur dans la base de données master pour les utilisateurs de magasin de données SQL Azure. Création d’un utilisateur dans master permet à un utilisateur pour la connexion à l’aide d’outils tels que SSMS sans spécifier un nom de base de données.  Il leur permet également d’utiliser l’Explorateur d’objets pour afficher toutes les bases de données sur un serveur SQL.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ensuite, vous connecter à votre **base de données de l’entrepôt de données SQL** avec votre nom d’utilisateur admin server et créez un utilisateur de base de données en fonction de la connexion de serveur que vous venez de créer.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Si un utilisateur va faire d’autres opérations telles que la création de connexions d’accès ou la création de nouvelles bases de données, ils devront également être affecté à la `Loginmanager` et `dbmanager` des rôles dans la base de données master. Pour plus d’informations sur ces rôles supplémentaires et d’authentification pour une base de données SQL, consultez [Gestion des bases de données et les connexions de base de données de SQL Azure][].  Pour plus d’informations sur AD Azure pour SQL Data Warehouse, consultez [connexion à SQL données entrepôt par à l’aide d’Azure l’authentification Active Directory][].


## <a name="authorization"></a>Autorisation

L’autorisation fait référence à ce que vous pouvez faire dans une base de données de l’entrepôt de données de SQL Azure, et ceci est contrôlé par les autorisations et les appartenances aux rôles de votre compte d’utilisateur. Pour obtenir les meilleurs résultats, vous devez accorder aux utilisateurs les privilèges minimum nécessaires. Entrepôt de données SQL Azure facilite cette gérer les rôles dans T-SQL :

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Le compte d’administrateur de serveur que vous vous connectez avec est un membre qui a le droit de faire quoi que ce soit dans la base de données db_owner. Enregistrer ce compte pour le déploiement des mises à niveau de schéma et d’autres opérations de gestion. Pour vous connecter à partir de votre application à la base de données avec les privilèges minimum requis par votre application, utilisez le compte « ApplicationUser » avec des autorisations plus limitées.

Il existe des façons de limiter ce qu’un utilisateur peut faire avec la base de données de SQL Azure :

- Des [autorisations][] précises vous permettent de contrôler les opérations que vous pouvez effectuer sur des colonnes individuelles, des tables, des vues, des procédures et autres objets de la base de données. Utilisent des permissions granulaires pour avoir davantage de contrôle et accordez les autorisations minimales nécessaires. Le système d’autorisation granulaire est quelque peu compliqué et nécessite une étude pour utiliser efficacement.
- [Les rôles de base de données][] autre que db_datareader et db_datawriter peuvent être utilisés pour créer des comptes d’utilisateur application plus puissants ou des comptes de gestion puissants. Les rôles de base de données fixe intégré offrent un moyen simple pour accorder des autorisations, mais il peuvent entraîner l’octroi de davantage d’autorisations que nécessaire.
- [Les procédures stockées][] peut être utilisé pour limiter les actions qui peuvent être prises sur la base de données.

Gestion des bases de données et des serveurs logiques à partir du portail classique d’Azure, ou à l’aide de l’API du Gestionnaire de ressources Azure est contrôlé par les attributions de rôle de votre compte d’utilisateur du portail. Pour plus d’informations sur ce sujet, consultez [contrôle d’accès par rôle dans Azure Portal][].

## <a name="encryption"></a>Cryptage

Azure SQL données entrepôt Transparent données cryptage (TDE) protège contre la menace des activités malveillantes en effectuant le cryptage en temps réel et le déchiffrement de vos données au repos.  Lorsque vous cryptez votre base de données, les sauvegardes associées et les fichiers journaux des transactions sont cryptées sans nécessiter des modifications à vos applications. TDE crypte le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de cryptage de la base de données. La clé de chiffrement de base de données de la base de données SQL est protégée par un certificat de serveur intégré. Le certificat de serveur intégré est unique pour chaque serveur de base de données SQL. Microsoft fait automatiquement pivote ces certificats au moins tous les 90 jours. L’algorithme de cryptage utilisé par SQL Data Warehouse est AES-256. Pour une description générale du TDE, consultez [Chiffrement de données Transparent][].

Vous pouvez crypter votre base de données à l’aide du [Portail Azure] [ Encryption with Portal] ou [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations et d’exemples sur la connexion à votre magasin de données SQL avec différents protocoles, voir [se connecter à SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Se connecter au magasin de données SQL]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connexion au magasin de données SQL à l’aide de l’authentification Azure Active Directory]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Pare-feu de base de données SQL Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Rôles de base de données]: https://msdn.microsoft.com/library/ms189121.aspx
[Gestion des bases de données et des connexions de base de données de SQL Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[Autorisations]: https://msdn.microsoft.com/library/ms191291.aspx
[Procédures stockées]: https://msdn.microsoft.com/library/ms190782.aspx
[Chiffrement de données transparent]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Contrôle d’accès par rôle dans Azure Portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
