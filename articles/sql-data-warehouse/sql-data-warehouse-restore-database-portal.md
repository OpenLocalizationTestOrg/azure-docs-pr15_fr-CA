<properties
   pageTitle="Restauration d’un entrepôt de données SQL Azure (portail) | Microsoft Azure"
   description="Tâches du portail Azure pour la restauration d’un entrepôt de données SQL Azure."
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

# <a name="restore-an-azure-sql-data-warehouse-portal"></a>Restauration d’un entrepôt de données SQL Azure (portail)

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Portail][]
- [PowerShell][]
- [RESTE][]

Dans cet article, vous apprendrez comment restaurer un entrepôt de données SQL Azure via le portail d’Azure.

## <a name="before-you-begin"></a>Avant de commencer

**Vérifiez votre capacité DTU.** Chaque entrepôt de données SQL est hébergé par un serveur SQL (par exemple, myserver.database.windows.net), qui dispose d’un quota DTU par défaut.  Avant de pouvoir restaurer un Data Warehouse de SQL, vérifiez que la votre serveur SQL a quota DTU restant pour la base de données en cours de restauration. Pour savoir comment calculer DTU nécessaire ou pour demander plus de DTU, reportez-vous à [demander une modification du quota DTU][].


## <a name="restore-an-active-or-paused-database"></a>Restaurer une base de données active ou suspendu

Pour restaurer une base de données :

1. Connectez-vous au [portail Azure][]
2. Sur le côté gauche de l’écran Sélectionnez **Parcourir** , puis sélectionnez **les serveurs SQL**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. Accédez à votre serveur et sélectionnez-le
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. Rechercher l’entrepôt de données SQL que vous souhaitez restaurer à partir d’et sélectionner
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. En haut de la lame d’entrepôt de données, cliquez sur **restaurer**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. Spécifiez un nouveau **nom de la base de données**
7. Sélectionnez le dernier **Point de restauration**
    1. Assurez-vous que vous cliquez sur le dernier point de restauration.  Dans la mesure où les points de restauration sont affichées au format UTC, l’option par défaut affichée est parfois pas le dernier point de restauration.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. Cliquez sur **OK**
9. Le processus de restauration de base de données va commencer et peut être surveillé à l’aide de **NOTIFICATIONS**

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer votre base de données récupérée par suivantes [configurer votre base de données après la restauration][].


## <a name="restore-a-deleted-database"></a>Restaurer une base de données supprimé

Pour restaurer une base de données supprimé :

1. Connectez-vous au [portail Azure][]
2. Sur le côté gauche de l’écran Sélectionnez **Parcourir** , puis sélectionnez **les serveurs SQL**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. Accédez à votre serveur et sélectionnez-le
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. Faites défiler jusqu'à la section opérations lame de votre serveur
5. Cliquez sur la mosaïque de **Bases de données supprimées**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. Sélectionnez la base de données supprimé que vous souhaitez restaurer
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. Spécifiez un nouveau **nom de la base de données**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. Cliquez sur **OK**
9. Le processus de restauration de base de données va commencer et peut être surveillé à l’aide de **NOTIFICATIONS**

>[AZURE.NOTE] Pour configurer votre base de données une fois la restauration terminée, consultez [configurer votre base de données après la restauration][]. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les fonctionnalités de continuité d’activité d’entreprise des éditions de base de données de SQL Azure, consultez la [vue d’ensemble de base de données de SQL Azure business continuity][].

<!--Image references-->

<!--Article references-->
[Présentation de la continuité d’activité de business de la base de données SQL Azure]: ./sql-database-business-continuity.md
[Vue d’ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTE]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurer votre base de données après la restauration]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Demander une modification du quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
