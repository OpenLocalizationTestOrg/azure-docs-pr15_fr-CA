<properties
    pageTitle="Copier une base de données SQL d’Azure | Microsoft Azure"
    description="Créer une copie d’une base de données SQL d’Azure"
    services="sql-database"
    documentationCenter=""
    authors="anosov1960"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/24/2016"
    ms.author="sstein; sashan"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="copy-an-azure-sql-database"></a>Copier une base de données SQL Azure

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-copy.md)
- [Azure portal](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Vous pouvez utiliser l' Azure [SQL de base de données des sauvegardes automatisées](sql-database-automated-backups.md) pour créer une copie de votre base de données SQL. La copie de la base de données utilise la même technologie que la fonctionnalité de réplication géographique. Mais contrairement à géo-réplication qu’elle termine le lien de réplication en tant que la fin de la phase d’amorçage. Par conséquent, la base de données de copie est un instantané de la base de données source au moment de la demande de copie.  
Vous pouvez créer la copie de la base de données sur le même serveur ou sur un autre serveur. Performances et le niveau de niveau de service (niveau de tarification) de la copie de la base de données sont les mêmes que la base de données source par défaut. Lors de l’utilisation de l’API, vous pouvez sélectionner un niveau de performances différentes dans le même niveau de service (édition). Une fois la copie terminée, la copie devient une base de données indépendant entièrement fonctionnel. À ce stade, vous pouvez promouvoir ou rétrograder il à n’importe quelle édition. Les connexions d’accès, les utilisateurs et les autorisations peuvent être gérées séparément.  

Lorsque vous copiez une base de données sur le même serveur logique, les connexions de mêmes peuvent être utilisées sur les deux bases de données. L’entité que permet de copier la base de données de sécurité devient le propriétaire de base de données (DBO) sur la nouvelle base de données. Tous les utilisateurs de la base de données, leurs autorisations et leurs identificateurs de sécurité (SID) sont copiés dans la copie de la base de données.  

Lorsque vous copiez une base de données vers un autre serveur logique, l’entité sur le nouveau serveur de sécurité devient le propriétaire de la base de données sur la nouvelle base de données. Si vous utilisez [les utilisateurs de base de données de contenu](sql-database-manage-logins.md) pour l’accès aux données garantit les bases de données primaires et secondaires ont toujours les mêmes informations d’identification de l’utilisateur afin que, une fois la copie terminée, vous pouvez l’accéder immédiatement avec les mêmes informations d’identification. Si vous utilisez [Azure Active Directory](../active-directory/active-directory-whatis.md), vous pouvez éliminer complètement la nécessité pour la gestion des informations d’identification dans la copie. Toutefois, lorsque vous copiez la base de données vers un nouveau serveur, l’accès en général ne fonctionnera pas, car les connexions d’accès n’existent pas sur le nouveau serveur. Voir [comment gérer la sécurité de base de données Azure SQL après un sinistre](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des connexions d’accès lors de la copie d’une base de données vers un autre serveur logique. 

Pour copier une base de données SQL, vous devez les éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure simplement cliquez sur **Version d’évaluation gratuite** en haut de cette page, puis revenir à la fin de cet article.
- Une base de données SQL à copier. Si vous ne disposez pas d’une base de données SQL, créez-en un en suivant les étapes décrites dans cet article : [créer votre première base de données de SQL Azure](sql-database-get-started.md).

## <a name="next-steps"></a>Étapes suivantes

- Voir la [copie d’une base de données Azure SQL via le portail Azure](sql-database-copy-portal.md) pour copier une base de données à l’aide du portail Azure.
- Voir la [copie d’une base de données Azure SQL à l’aide de PowerShell](sql-database-copy-powershell.md) pour copier une base de données à l’aide de PowerShell.
- Voir la [copie d’une base de données Azure SQL à l’aide de T-SQL](sql-database-copy-transact-sql.md) pour copier une base de données à l’aide de Transact-SQL.
- Voir [comment gérer la sécurité de base de données Azure SQL après un sinistre](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et des connexions d’accès lors de la copie d’une base de données vers un autre serveur logique.



## <a name="additional-resources"></a>Ressources supplémentaires

- [Gestion des accès](sql-database-manage-logins.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données pour un type sac à DOS](sql-database-export.md)
- [Vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- [Documentation de la base de données de SQL](https://azure.microsoft.com/documentation/services/sql-database/)
