<properties
    pageTitle="Déplacer les bases de données entre serveurs, entre les abonnements dans Azure."
    description="Étapes à suivre pour copier, déplacer et migrer les données et les bases de données dans la base de données de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>Déplacer les bases de données entre serveurs, entre les abonnements et Azure

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]
##<a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Pour déplacer une base de données vers un autre serveur dans le même abonnement
- Dans [Azure Portal](https://portal.azure.com), cliquez sur **bases de données SQL**, sélectionnez une base de données dans la liste, puis cliquez sur **Copier**. Pour plus d’informations, voir [Copier une base de données SQL d’Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Pour déplacer une base de données entre les abonnements
- Dans le [Portail Azure](https://portal.azure.com), cliquez sur **les serveurs SQL** et sélectionnez le serveur qui héberge votre base de données à partir de la liste. Cliquez sur **déplacer**et choisissez les ressources pour déplacer et l’abonnement à atteindre.

## <a name="to-migrate-a-sql-database-into-azure"></a>Pour migrer une base de données SQL dans Azure
- Déterminer la compatibilité de la base de données, puis sélectionnez la méthode de migration adéquats en fonction de vos besoins. Suivez les instructions et les options de [migration d’une base de données SQL Server](sql-database-cloud-migrate.md).

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Pour créer une copie d’une base de données pour une utilisation en dehors d’Azure
- [Exporter un fichier de type sac à DOS.](sql-database-export.md)
