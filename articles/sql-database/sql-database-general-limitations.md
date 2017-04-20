<properties
   pageTitle="Les instructions et les restrictions générales de la base de données SQL Azure"
   description="Cette page décrit certaines limites générales de la base de données de SQL Azure, ainsi que des zones de l’interopérabilité et la prise en charge."
   services="sql-database"
   documentationCenter="na"
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/06/2016"
   ms.author="carlrab" />

# <a name="azure-sql-database-general-limitations-and-guidelines"></a>Les instructions et les restrictions générales de la base de données SQL Azure

Cette rubrique fournit des limites d’ordre général et des lignes directrices pour la base de données de SQL Azure. Pour une présentation complète des quotas, de gestion des ressources et de prise en charge, consultez les [ressources supplémentaires](#additional-guidelines) à la fin de cette rubrique.

## <a name="connectivity-and-authentication"></a>Connectivité et l’authentification

  - L’authentification Windows n’est pas pris en charge. Consultez [Gestion des bases de données et les connexions de base de données SQL Azure](sql-database-manage-logins.md). Toutefois, l’authentification Active Directory Azure est pris en charge avec certaines limitations. Voir [se connecter à la base de données SQL avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).

  - Base de données SQL de Microsoft Azure prend en charge les données tabulaires stream (TDS) protocole client version 7.3 ou ultérieure.

  - Seules les connexions TCP/IP sont autorisées.

  - L’Explorateur SQL Server de SQL Server 2008 n’est pas pris en charge, car la base de données SQL de Microsoft Azure n’a pas de ports dynamiques, seulement le ports 1433.

## <a name="sql-server-agentjobs"></a>Travaux SQL Server Agent

Base de données SQL de Microsoft Azure n’autorise pas l’Agent de SQL Server, mais vous pouvez utiliser les travaux élastiques pour exécuter des tâches sur un grand nombre de bases de données. Pour plus d’informations sur les tâches élastiques, voir [travaux élastiques](sql-database-elastic-jobs-overview.md).

## <a name="sql-server-collation-support"></a>Prise en charge du classement SQL Server

Le classement de base de données par défaut utilisé par la base de données SQL de Microsoft Azure est **SQL_LATIN1_GENERAL_CP1_CI_AS**, où **LATIN1_GENERAL** est anglais (États-Unis), **CP1** est la page de code 1252, **CI** respecte la casse et **des accents est** . Il n’est pas possible de modifier le classement de bases de données V12. Pour plus d’informations sur la façon de définir le classement, voir [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="naming-requirements"></a>Exigences de noms

Certains noms d’utilisateur ne sont pas autorisées pour des raisons de sécurité. Vous ne pouvez pas utiliser les noms suivants :

 - **Admin**
 - **administrateur**
 - **invité**
 - **racine**
 - **association de sécurité**

Les noms de tous les nouveaux objets doivent respecter les règles SQL Server gouvernant les identificateurs. Pour plus d’informations, voir [identificateurs](https://msdn.microsoft.com/library/ms175874.aspx).

En outre, les noms de connexion et l’utilisateur ne peut pas contenir le \ caractère (l’authentification Windows n’est pas pris en charge).

## <a name="additional-guidelines"></a>Instructions supplémentaires

- Outre les restrictions générales décrites dans cet article, base de données de SQL a des quotas de ressource spécifique et limitations en fonction de votre **niveau de service**. Pour une vue d’ensemble des niveaux de service, consultez [niveaux de service de la base de données SQL](sql-database-service-tiers.md).

- Pour les autres limites de base de données SQL, consultez [Les limites des ressources de base de données SQL Azure](sql-database-resource-limits.md).

- Pour la sécurité des directives apparentées, voir [directives de sécurité de base de données SQL Azure et Limitations](sql-database-security-guidelines.md).

- Une autre zone connexe entoure la compatibilité base de données de SQL Azure a avec les versions sur site de SQL Server, comme SQL Server 2014 et 2016 de SQL Server. La dernière version de V12 de base de données de SQL Azure a apporté de nombreuses améliorations dans ce domaine. Pour plus d’informations, consultez [Nouveautés de V12 de base de données SQL](sql-database-v12-whats-new.md).

- Pour plus d’informations sur la disponibilité des pilotes et prise en charge de la base de données SQL, consultez [Bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md).
