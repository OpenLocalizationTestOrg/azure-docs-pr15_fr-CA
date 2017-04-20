<properties
   pageTitle="Présentation de la sécurité de base de données SQL"
   description="En savoir plus sur la sécurité de base de données de SQL Azure et SQL Server, notamment les différences entre le nuage et SQL Server locaux lorsqu’il s’agit de l’authentification, autorisation, sécurité de la connexion, le cryptage et la conformité."
   services="sql-database"
   documentationCenter=""
   authors="tmullaney"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="thmullan;jackr"/>


# <a name="securing-your-sql-database"></a>Sécurisation de votre base de données SQL

## <a name="overview"></a>Vue d’ensemble

Cet article décrit les principes fondamentaux de la sécurisation de la couche de données d’une application à l’aide de la base de données de SQL Azure. En particulier, cet article va vous aider à démarrer avec des ressources pour limiter l’accès, la protection des données, et la surveillance des activités sur une base de données créée dans la [mise en route avec le didacticiel de la base de données de SQL](sql-database-get-started.md). Pour une vue d’ensemble complète des fonctionnalités de sécurité disponibles sur toutes les versions de SQL, consultez le [Centre de sécurité pour le moteur de base de données SQL Server et la base de données de SQL Azure](https://msdn.microsoft.com/library/bb510589). Informations supplémentaires sont également disponibles dans le [livre blanc technique de sécurité et la base de données de SQL Azure](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="connection-security"></a>Sécurité de connexion

Sécurité de connexion fait référence à la restreindre et les connexions sécurisées à votre base de données à l’aide de règles de pare-feu et de chiffrement de la connexion.

Règles de pare-feu sont utilisés par le serveur et la base de données pour rejeter les tentatives de connexion à partir d’adresses IP qui n’ont pas été explicitement autorisés. Pour permettre à votre application ou adresse IP publique de l’ordinateur client tente de se connecter à une base de données, vous devez d’abord créer une règle de pare-feu au niveau du serveur à l’aide du portail classique d’Azure, API REST ou PowerShell. Pour obtenir les meilleurs résultats, vous devez restreindre les plages d’adresses IP autorisées par votre pare-feu de serveur autant que possibles. Pour plus d’informations, voir [Pare-feu de base de données SQL Azure](https://msdn.microsoft.com/library/ee621782).

Toutes les connexions à la base de données de SQL Azure exigent le cryptage (SSL/TLS) à tout moment pendant des données « en transit » vers et à partir de la base de données. Dans la chaîne de connexion de votre application, vous devez spécifier des paramètres pour crypter la connexion et *pas* d’approuver le certificat de serveur (ceci pour vous si vous copiez votre chaîne de connexion sur le portail classique Azure), sinon la connexion ne vérifie pas l’identité du serveur et sera exposé aux attaques de « man in the middle ». Pour le pilote ADO.NET, par exemple, ces paramètres de chaîne de connexion sont **Encrypt = True** et **TrustServerCertificate = False**. Pour plus d’informations, consultez [cryptage des connexions de base de données SQL Azure et la Validation des certificats](https://msdn.microsoft.com/library/azure/ff394108#encryption).


## <a name="authentication"></a>Authentification

L’authentification désigne comment vous prouver votre identité lors de la connexion à la base de données. Base de données de SQL prend en charge deux types d’authentification :

 - **L’authentification SQL**, qui utilise un nom d’utilisateur et un mot de passe
 - **Azure l’authentification Active Directory**, qui utilise des identités gérées par Azure Active Directory et est pris en charge pour les domaines gérés et intégrés

Lorsque vous avez créé le serveur logique pour votre base de données, vous avez spécifié une connexion « server admin » avec un nom d’utilisateur et un mot de passe. À l’aide de ces informations d’identification, vous pouvez authentifier à toute base de données sur ce serveur en tant que le propriétaire de la base de données, ou « dbo. » Si vous souhaitez utiliser l’authentification Active Directory Azure, vous devez créer un autre serveur admin, appelé la « admin AD Azure » qui est autorisée à gérer des groupes et des utilisateurs AD Azure. Cet administrateur peut également effectuer toutes les opérations qu’un administrateur de serveur ordinaire. Pour une procédure pas à pas comment créer un administrateur AD Azure pour activer l’authentification Active Directory Azure, consultez [connexion à SQL de base de données à l’aide de Azure l’authentification Active Directory](sql-database-aad-authentication.md) .

Comme meilleure pratique, votre application doit utiliser un compte différent pour l’authentification : de cette manière, vous pouvez limiter les autorisations accordées à l’application et réduire les risques des activités malveillantes dans le cas où votre code d’application est vulnérable à une attaque d’injection SQL. L’approche recommandée consiste à créer un [utilisateur de base de données de contenu](https://msdn.microsoft.com/library/ff929188), qui permet à votre application authentifier directement à une base de données unique. Vous pouvez créer un utilisateur de base de données de contenu qui utilise l’authentification SQL en exécutant la commande T-SQL suivante lorsque vous êtes connecté à votre base de données de l’utilisateur sous la connexion d’administration de serveur :

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Si vous avez créé un administrateur AD Azure, vous pouvez créer un utilisateur de base de données de contenu qui utilise l’authentification Active Directory Azure en exécutant la commande T-SQL suivante lorsque vous êtes connecté à votre base de données de l’utilisateur comme l’administrateur AD Azure :

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

Dans les deux cas, la chaîne de connexion de votre application doit spécifier ces informations d’identification de l’utilisateur, plutôt que la connexion admin server, pour vous connecter à la base de données.

Pour plus d’informations sur l’authentification pour une base de données SQL, voir [Gérer les bases de données et les connexions de base de données de SQL Azure](sql-database-manage-logins.md).


## <a name="authorization"></a>Autorisation
L’autorisation fait référence à ce que vous pouvez faire dans une base de données de SQL Azure, et ceci est contrôlé par les autorisations et les appartenances aux rôles de votre compte d’utilisateur. Pour obtenir les meilleurs résultats, vous devez accorder aux utilisateurs les privilèges minimum nécessaires. Une base de données SQL Azure facilite cette gérer les rôles dans T-SQL :

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

Le compte d’administrateur de serveur que vous vous connectez avec est un membre qui a le droit de faire quoi que ce soit dans la base de données db_owner. Enregistrer ce compte pour le déploiement des mises à niveau de schéma et d’autres opérations de gestion. Pour vous connecter à partir de votre application à la base de données avec les privilèges minimum requis par votre application, utilisez le compte « ApplicationUser » avec des autorisations plus limitées.

Il existe des façons de limiter ce qu’un utilisateur peut faire avec la base de données de SQL Azure :

* [Les rôles de base de données](https://msdn.microsoft.com/library/ms189121) autre que db_datareader et db_datawriter peuvent être utilisés pour créer des comptes d’utilisateur application plus puissants ou des comptes de gestion puissants.
* Des [autorisations](https://msdn.microsoft.com/library/ms191291) précises vous permettent de contrôler les opérations que vous pouvez effectuer sur des colonnes individuelles, des tables, des vues, des procédures et autres objets de la base de données.
* [L’emprunt d’identité](https://msdn.microsoft.com/library/vstudio/bb669087) et la [signature de module](https://msdn.microsoft.com/library/bb669102) peuvent être utilisés à élever en toute sécurité les autorisations temporairement.
* [Sécurité de niveau ligne](https://msdn.microsoft.com/library/dn765131) peuvent servir limite les lignes d’un utilisateur peut accéder.
* [Masquage de données](sql-database-dynamic-data-masking-get-started.md) peut servir à limiter l’exposition des données sensibles.
* [Les procédures stockées](https://msdn.microsoft.com/library/ms190782) peut être utilisé pour limiter les actions qui peuvent être prises sur la base de données.

Gestion des bases de données et des serveurs logiques à partir du portail classique d’Azure, ou à l’aide de l’API du Gestionnaire de ressources Azure est contrôlé par les attributions de rôle de votre compte d’utilisateur du portail. Pour plus d’informations sur ce sujet, consultez [contrôle d’accès par rôle dans Azure Portal](../active-directory./role-based-access-control-configure.md).


## <a name="encryption"></a>Cryptage

Une base de données SQL Azure peut vous aider à protéger vos données en les cryptant vos données lorsqu’elle est « au repos », ou stockées dans des fichiers de base de données et de sauvegardes, à l’aide du [Chiffrement de données Transparent](http://go.microsoft.com/fwlink/?LinkId=526242). Pour crypter votre base de données, vous connecter en tant que propriétaire d’une base de données et exécuter :

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Pour d’autres méthodes crypter les secrets de vos données, prenez en compte :

* [Cryptage de niveau cellule](https://msdn.microsoft.com/library/ms179331.aspx) pour chiffrer des colonnes spécifiques ou même les cellules de données avec différentes clés de cryptage.
* Si vous avez besoin d’un Module de sécurité matériel ou d’une gestion centralisée de votre hiérarchie de clés de cryptage, envisagez d’utiliser le [Coffre-fort de clé Azure avec SQL Server dans un ordinateur virtuel d’Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Toujours cryptées](https://msdn.microsoft.com/library/mt163865.aspx) (en aperçu) rend le cryptage transparent pour les applications et permet aux clients de crypter les données sensibles à l’intérieur des applications clientes sans partager les clés de cryptage avec la base de données de SQL.

## <a name="auditing"></a>L’audit

L’audit et le suivi des événements de la base de données peuvent vous aider à assurer la conformité réglementaire et à identifier toute activité suspecte. L’audit de base de données SQL permet de que vous permet de consigner les événements dans votre base de données à un audit ouvrir une session dans votre compte de stockage Azure. L’audit de base de données SQL s’intègre également avec Microsoft BI de puissance afin de faciliter les analyses et les rapports d’exploration. Pour plus d’informations, consultez [mise en route de l’audit de base de données SQL](sql-database-auditing-get-started.md).

Détection des menaces de base de données SQL fournit une couche supplémentaire de sécurité audit. Il vous permet de répondre aux menaces lorsqu’elles se produisent en fournissant des alertes de sécurité sur les activités anormales. Pour plus d’informations, consultez [mise en route de la détection des menaces de base de données SQL](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Respect de la réglementation

Outre les fonctionnalités et les fonctionnalités permettant à votre application de répondre aux différentes exigences de respect de la réglementation de sécurité, base de données de SQL Azure à également ci-dessus participe à des audits réguliers et ont été certifiés par rapport à un certain nombre de normes de conformité. Pour plus d’informations, consultez le [Centre d’approbation Microsoft Azure](https://azure.microsoft.com/support/trust-center/), où vous trouverez la liste actualisée des [certifications de la conformité de la base de données SQL](https://azure.microsoft.com/support/trust-center/services/).
