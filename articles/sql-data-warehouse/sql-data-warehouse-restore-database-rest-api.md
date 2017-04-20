<properties
   pageTitle="Restauration d’un entrepôt de données SQL Azure (API REST) | Microsoft Azure"
   description="Tâches d’API REST de restauration d’un entrepôt de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Restauration d’un entrepôt de données SQL Azure (API REST)

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Portail][]
- [PowerShell][]
- [RESTE][]

Dans cet article, vous apprendrez comment restaurer un entrepôt de données de SQL Azure à l’aide de l’API REST.

## <a name="before-you-begin"></a>Avant de commencer

**Vérifiez votre capacité DTU.** Chaque entrepôt de données SQL est hébergé par un serveur SQL (par exemple, myserver.database.windows.net), qui dispose d’un quota DTU par défaut.  Avant de pouvoir restaurer un Data Warehouse de SQL, vérifiez que la votre serveur SQL a quota DTU restant pour la base de données en cours de restauration. Pour savoir comment calculer DTU nécessaire ou pour demander plus de DTU, reportez-vous à [demander une modification du quota DTU][].

## <a name="restore-an-active-or-paused-database"></a>Restaurer une base de données active ou suspendu

Pour restaurer une base de données :

1. Obtenir la liste des points de restauration de base de données à l’aide de l’opération d’obtenir les Points de restauration de base de données.
2. Commencer la restauration à l’aide de l’opération de [requête de restauration de la base de données créer][] .
3. Suivre l’état de la restauration à l’aide de l’opération de [l’état de l’opération de base de données][] .

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer votre base de données récupérée par suivantes [configurer votre base de données après la restauration][].

## <a name="restore-a-deleted-database"></a>Restaurer une base de données supprimé

Pour restaurer une base de données supprimé :

1.  Liste de toutes vos bases de données supprimées peuvent être restaurées à l’aide de l’opération de [bases de données supprimées liste peuvent être restaurées][] .
2.  Obtenir les détails de la base de données supprimé que vous souhaitez restaurer en utilisant l’opération [Get peuvent être restaurée de la base de données supprimée][] .
3.  Commencer la restauration à l’aide de l’opération de [requête de restauration de la base de données créer][] .
4.  Suivre l’état de la restauration à l’aide de l’opération de [l’état de l’opération de base de données][] .

>[AZURE.NOTE] Pour configurer votre base de données une fois la restauration terminée, consultez [configurer votre base de données après la restauration][]. 


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les fonctionnalités de continuité d’activité d’entreprise des éditions de base de données de SQL Azure, consultez la [vue d’ensemble de base de données de SQL Azure business continuity][].

<!--Image references-->

<!--Article references-->
[Présentation de la continuité d’activité de business de la base de données SQL Azure]: ./sql-database-business-continuity.md
[Demander une modification du quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurer votre base de données après la restauration]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
[Vue d’ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTE]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Créer une demande de restauration de base de données]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[État de l’opération de base de données]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obtenir une base de données supprimée restaurable]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Liste restaurable supprimées des bases de données]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
