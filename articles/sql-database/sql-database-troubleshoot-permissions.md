<properties
    pageTitle="Comment effectuer des tâches d’administration, par exemple Réinitialiser mot de passe admin | Microsoft Azure"
    description="Décrit comment effectuer des tâches administratives dans une base de données de SQL. Par exemple, la réinitialisation de mot de passe administrateur, l’octroi et de retrait de l’accès."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="Réinitialiser le mot de passe administrateur"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>Comment effectuer des tâches administratives telles que la réinitialisation de mot de passe de l’administrateur de base de données de SQL Azure
Utilisez cette rubrique pour obtenir une procédure rapide d’accorder et de retirer l’accès à une base de données SQL d’Azure. Pour plus d’informations, voir :

- [Gestion des bases de données et des connexions de base de données de SQL Azure](sql-database-manage-logins.md)
- [Sécurisation de votre base de données SQL](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et de base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>Pour réinitialiser le mot de passe de l’administrateur d’un serveur logique

- Dans le [Portail Azure](https://portal.azure.com) sur **Les serveurs SQL**, sélectionnez le serveur à partir de la liste, puis cliquez sur **Réinitialiser le mot de passe**.

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>Pour faciliter des IP autorisée que seul les adresses sont autorisées à accéder au serveur
- Consultez [Comment : configurer les paramètres du pare-feu sur la base de données de SQL](sql-database-configure-firewall-settings.md).

## <a name="to-create-contained-database-users-in-the-user-database"></a>Pour créer des utilisateurs de base de données contenus dans la base de données de l’utilisateur
- Utilisez l’instruction [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) et voir des [Contenus de base de données utilisateurs - rendre votre base de données Portable](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>Pour authentifier les utilisateurs de la base de données de contenu à l’aide de Active Directory Azure
- Reportez-vous à la section [connexion à la base de données SQL à l’aide de l’authentification Azure Active Directory](sql-database-aad-authentication.md).

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>Pour créer des connexions supplémentaires pour les utilisateurs avec privilèges élevés dans la base de données master virtuel
- Utilisez l’instruction [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) et reportez-vous à la section Gestion des connexions de [bases de données de gestion des connexions de base de données de SQL Azure](sql-database-manage-logins.md) pour plus de détails.
