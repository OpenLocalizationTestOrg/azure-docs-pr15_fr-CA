<properties
   pageTitle="Authentification SQL Azure Data Warehouse | Microsoft Azure"
   description="Authentification Active Directory (DAS) et SQL Server Azure à l’entrepôt de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/24/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# <a name="authentication-to-azure-sql-data-warehouse"></a>Authentification à l’entrepôt de données SQL Azure

> [AZURE.SELECTOR]
- [Vue d’ensemble de la sécurité](sql-data-warehouse-overview-manage-security.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Cryptage (portail)](sql-data-warehouse-encryption-tde.md)
- [Cryptage (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Pour vous connecter à l’entrepôt de données SQL, vous devez passer les informations d’identification de sécurité à des fins d’authentification. Lors de l’établissement d’une connexion, certains paramètres de connexion sont configurés dans le cadre de l’établissement de la session de requête.  

Pour plus d’informations sur la sécurité et comment faire pour activer les connexions à votre magasin de données, reportez-vous à [sécuriser une base de données SQL Data Warehouse][].

## <a name="sql-authentication"></a>Authentification SQL
Pour vous connecter à l’entrepôt de données SQL, vous devez fournir les informations suivantes :

- Nom qualifié complet du serveur
- Spécifier l’authentification SQL
- Nom d’utilisateur
- Mot de passe
- Base de données par défaut (facultatif)

Par défaut, votre connexion se connecte à la base de données *master* et non votre base de données de l’utilisateur. Pour vous connecter à votre base de données de l’utilisateur, vous pouvez choisir une des deux choses :

- Spécifiez la base de données par défaut lors de l’enregistrement de votre serveur avec l’Explorateur d’objets SQL Server dans SSDT, SSMS, ou dans votre chaîne de connexion d’application. Par exemple, inclure le paramètre InitialCatalog pour une connexion ODBC.
- Mettez en surbrillance la base de données de l’utilisateur avant de créer une session dans SSDT.

> [AZURE.NOTE] L’instruction Transact-SQL **Utilisation, MaBasedeDonnées ;** n’est pas pris en charge pour la modification de la base de données pour une connexion. Pour se connecter au magasin de données SQL avec SSDT des instructions, reportez-vous à l’article de la [requête avec Visual Studio][] .

## <a name="azure-active-directory-aad-authentication"></a>Authentification Active Directory (DAS) Azure

[Azure Active Directory] [ What is Azure Active Directory] l’authentification est un mécanisme de connexion à Microsoft Azure SQL Data Warehouse à l’aide des identités dans Azure Active Directory (AD Azure). Avec l’authentification Azure Active Directory, vous pouvez gérer de façon centralisée l’identité des utilisateurs de la base de données et autres services de Microsoft dans un emplacement central. Gestion centrale des ID fournit un emplacement unique pour gérer les utilisateurs de magasin de données SQL et simplifie la gestion des autorisations. 

### <a name="benefits"></a>Avantages

Avantages d’Active Directory Azure :

- Fournit une alternative à l’authentification SQL Server.
- Permet de bloquer la prolifération des identités des utilisateurs sur les serveurs de base de données.
- Permet la rotation de mot de passe dans un endroit unique
- Gérer les autorisations de base de données à l’aide de groupes d’externes (DAS).
- Élimine le stockage des mots de passe en activant l’authentification intégrée de Windows et d’autres formes d’authentification pris en charge par Active Directory de Azure.
- Utilisateurs de base de données utilise contenues pour authentifier les identités au niveau de la base de données.
- Prend en charge l’authentification basée sur un jeton pour les applications se connectant à SQL Data Warehouse.
- Prend en charge plusieurs facteurs d’authentification par le biais de l’authentification Active Directory universel pour SQL Server Management Studio. Pour obtenir une description de l’authentification multicritères, consultez [prise en charge de SSMS pour AMF de publicité Azure avec SQL de base de données et SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [AZURE.NOTE] Azure Active Directory est encore relativement nouvelle et présente certaines limitations. Pour vous assurer que Azure Active Directory est une bonne solution pour votre environnement, consultez [AD Azure fonctionnalités et limites][], en particulier sur les considérations supplémentaires.

### <a name="configuration-steps"></a>Étapes de configuration

Suivez ces étapes pour configurer l’authentification d’Azure Active Directory.

1. Créer et remplir un Azure d’Active Directory
2. Associer facultatif : Ou modifier active directory qui est actuellement associé à votre abonnement Azure
3. Créer un administrateur Azure Active Directory pour l’entrepôt de données SQL Azure.
4. Configurez les ordinateurs clients
5. Créer des utilisateurs de base de données contenus dans votre base de données mis en correspondance avec des identités AD Azure
6. Se connecter à votre data warehouse à l’aide des identités AD Azure

Actuellement les utilisateurs Azure Active Directory ne sont pas affichés dans l’Explorateur d’objets SSDT. Pour résoudre ce problème, afficher les utilisateurs dans [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
### <a name="find-the-details"></a>Découvrez les détails
- Les étapes détaillées. Les étapes détaillées pour configurer et utiliser l’authentification Azure Active Directory sont presque identiques pour la base de données de SQL Azure et entrepôt de données SQL Azure. Suivez les étapes détaillées dans la rubrique [connexion à la base de données de SQL ou SQL données entrepôt par à l’aide d’Azure l’authentification Active Directory](../sql-database/sql-database-aad-authentication.md).
- Créer des rôles de base de données personnalisée et ajoutez des utilisateurs aux rôles. Accordez ensuite des autorisations précises pour les rôles. Pour plus d’informations, consultez [Mise en route avec les autorisations du moteur de base de données](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Étapes suivantes

Pour démarrer interrogeant le data warehouse avec Visual Studio et d’autres applications, consultez [requêtes avec Visual Studio][].

<!-- Article references -->
[Sécuriser une base de données SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Requête avec Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Fonctionnalités d’AD azure et limites]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
