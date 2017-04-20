<properties
   pageTitle="Consignes de sécurité de base de données SQL Azure et Limitations | Microsoft Azure"
   description="Obtenir des informations sur les directives de la base de données SQL de Microsoft Azure et des limitations liées à la sécurité."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/18/2016"
   ms.author="rickbyh"/>

# <a name="azure-sql-database-security-guidelines-and-limitations"></a>Limitations et les consignes de sécurité de la base de données SQL azure

Cette rubrique décrit les directives de base de données SQL de Microsoft Azure et les limitations liées à la sécurité. Tenez compte des points suivants lors de la gestion de la sécurité de vos bases de données SQL Azure.

## <a name="access-to-the-virtual-master-database"></a>Accès à la base de données master virtuel

En général, seuls les administrateurs ont besoin d’accéder à la base de données master. Routine d’accès à chaque base de données utilisateur doit être par les utilisateurs de base de données de contenu non administrateur créés dans chaque base de données. Lorsque vous utilisez les utilisateurs de base de données de contenu, vous n’avez pas besoin créer des connexions dans la base de données master. Pour plus d’informations, voir le [Contenu de base de données utilisateurs - rendre votre base de données Portable](https://msdn.microsoft.com/library/ff929188.aspx).


## <a name="firewall"></a>Pare-feu

Le pare-feu de SQL Server, qui a une étendue pour la totalité de SQL Server Azure est généralement configuré via le portail et doit admettre seulement les adresses IP utilisées par les administrateurs. Pour créer une règle de pare-feu de base de données de portée qui s’ouvre à la nécessaire plage d’adresses IP pour chaque base de données, vous connecter à une base de données utilisateur et utilisez ensuite l’instruction de Transact-SQL [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) .

Le service de base de données de SQL Azure est uniquement disponible via le port TCP 1433. Pour accéder à une base de données SQL à partir de votre ordinateur, assurez-vous que votre pare-feu d’ordinateur client autorise les communications TCP sortantes sur le port TCP 1433. Si ne pas nécessaire pour les autres applications, les connexions entrantes sur le port TCP 1433. 

Dans le cadre du processus de connexion, les connexions à partir des ordinateurs virtuels Azure sont redirigées vers une autre adresse IP et un port unique pour chaque rôle de travail. Le numéro de port est dans la plage de 11000 à 11999. Pour plus d’informations sur les ports TCP voir [Ports autres que le 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="authentication"></a>Authentification

Utiliser l’authentification Active Directory (sécurité intégrée) chaque fois que possible. Pour plus d’informations sur la configuration de l’authentification Active Directory, consultez [connexion à SQL de base de données à l’aide de Azure l’authentification Active Directory](sql-database-aad-authentication.md)et le [choix d’un Mode d’authentification](https://msdn.microsoft.com/library/ms144284.aspx) dans la documentation en ligne de SQL Server. 

Les utilisateurs de base de données de contenu permet d’améliorer l’évolutivité. Pour plus d’informations, voir le [Contenu de base de données utilisateurs - rendre votre base de données Portable](https://msdn.microsoft.com/library/ff929188.aspx), [Créer un utilisateur (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx)et [Bases de données de contenu](https://technet.microsoft.com/library/ff929071.aspx).

Le moteur de base de données ferme les connexions qui restent inactives pendant plus de 30 minutes. La connexion doit à nouveau l’ouverture de session avant de pouvoir être utilisé.

En continu les connexions actives à la base de données de SQL nécessitent une nouvelle autorisation (opération effectuée par le moteur de base de données) au moins toutes les dix heures. Le moteur de base de données tente d’un renouvellement d’autorisation à l’aide du mot de passe soumis et aucune entrée d’utilisateur n’est requise. Pour des raisons de performances, lors de la réinitialisation d’un mot de passe dans la base de données de SQL, la connexion n’est pas être authentifiés, même si la connexion est réinitialisée en raison du regroupement de connexions. Cela est différent du comportement de local SQL Server. Si le mot de passe a été modifié dans la mesure où la connexion a été initialement autorisée, la connexion doit être fermée et une nouvelle connexion établie à l’aide du nouveau mot de passe. Un utilisateur disposant de l’autorisation de supprimer la connexion de base de données peut explicitement mettre fin à une connexion de base de données SQL à l’aide de la commande [KILL](https://msdn.microsoft.com/library/ms173730.aspx) .

## <a name="logins-and-users"></a>Connexions et les utilisateurs

Lors de la gestion des connexions et les utilisateurs dans la base de données de SQL, il existe des restrictions.


- Vous devez être connecté à la base de données **maître** lors de l’exécution du ``CREATE/ALTER/DROP DATABASE`` instructions. -L’utilisateur de la base de données dans la base de données master, correspondant à la connexion d’accès au niveau du serveur principal ne peut pas être modifié ou supprimé. 
- États-Unis-anglais est la langue par défaut de la connexion d’accès au niveau du serveur principale.
- Seuls les administrateurs (ouverture de session au niveau du serveur principal ou administrateur AD Azure) et les membres du rôle de base de données de **dbmanager** dans la base de données **master** ont l’autorisation d’exécuter la ``CREATE DATABASE`` et ``DROP DATABASE`` instructions.
- Vous devez être connecté à la base de données maître lors de l’exécution du ``CREATE/ALTER/DROP LOGIN`` instructions. Toutefois l’utilisation de logins est déconseillée. Utilisez à la place des utilisateurs de la base de données de contenu.
- Pour vous connecter à une base de données de l’utilisateur, vous devez fournir le nom de la base de données dans la chaîne de connexion.
- Seuls les membres du rôle de base de données **loginmanager** dans la base de données **master** et la connexion d’accès au niveau du serveur principale ont l’autorisation d’exécuter le ``CREATE LOGIN``, ``ALTER LOGIN``, et ``DROP LOGIN`` instructions.
- Lors de l’exécution du ``CREATE/ALTER/DROP LOGIN`` et ``CREATE/ALTER/DROP DATABASE`` instructions dans une application ADO.NET, à l’aide des commandes paramétrées n’est pas autorisé. Pour plus d’informations, consultez [commandes et paramètres](https://msdn.microsoft.com/library/ms254953.aspx).
- Lors de l’exécution du ``CREATE/ALTER/DROP DATABASE`` et ``CREATE/ALTER/DROP LOGIN`` instructions, chacune de ces instructions doivent être la seule instruction dans un lot Transact-SQL. Dans le cas contraire, une erreur se produit. Par exemple, le Transact-SQL suivant vérifie l’existence de la base de données. S’il en existe une, un ``DROP DATABASE`` instruction est appelée pour supprimer de la base de données. Étant donné que la ``DROP DATABASE`` instruction n’est pas la seule instruction du lot, l’exécution de l’instruction de Transact-SQL génère une erreur.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Lors de l’exécution du ``CREATE USER`` instruction avec le ``FOR/FROM LOGIN`` option, elle doit être la seule instruction dans un lot Transact-SQL.
- Lors de l’exécution du ``ALTER USER`` instruction avec le ``WITH LOGIN`` option, elle doit être la seule instruction dans un lot Transact-SQL.
- Pour ``CREATE/ALTER/DROP`` un utilisateur requiert le ``ALTER ANY USER`` autorisation sur la base de données.
- Lorsque le propriétaire d’un rôle de base de données tente d’ajouter ou de supprimer un autre utilisateur de base de données vers ou à partir de ce rôle de base de données, l’erreur suivante peut se produire : **utilisateur ou le rôle 'Nom' n’existe pas dans cette base de données.** Cette erreur se produit car l’utilisateur n’est pas visible par le propriétaire. Pour résoudre ce problème, accordez le propriétaire du rôle de la ``VIEW DEFINITION`` autorisation de l’utilisateur. 

Pour plus d’informations sur les connexions et les utilisateurs, consultez [Gestion des bases de données et les connexions de base de données de SQL Azure](sql-database-manage-logins.md).


## <a name="see-also"></a>Voir aussi

[Pare-feu de base de données SQL Azure](sql-database-firewall-configure.md)

[Comment : configurer les paramètres du pare-feu (de base de données SQL Azure)](sql-database-configure-firewall-settings.md)

[Gestion des bases de données et des connexions de base de données de SQL Azure](sql-database-manage-logins.md)

[Centre de sécurité pour le moteur de base de données SQL Server et de base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)
