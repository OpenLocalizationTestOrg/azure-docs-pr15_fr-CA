<properties
    pageTitle="Restaurer une base de données Azure SQL à partir d’une sauvegarde automatique (Azure portal) | Microsoft Azure"
    description="Restaurer une base de données Azure SQL à partir d’une sauvegarde automatique (Azure portal)."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/18/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>Restaurer une base de données Azure SQL à partir d’une sauvegarde automatique via le portail Azure


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-recovery-using-backups.md#geo-restore)
- [Geo-restauration : PowerShell](sql-database-geo-restore-powershell.md)

Cet article vous explique comment restaurer votre base de données à partir d’une [sauvegarde automatique](sql-database-automated-backups.md) dans un nouveau serveur avec [Géo-restauration](sql-database-recovery-using-backups/.md#geo-restore) via le portail Azure.

## <a name="select-a-database-to-restore"></a>Sélectionnez une base de données à restaurer

Pour restaurer une base de données dans le portail Azure, procédez comme suit :

1.  Accédez au [portail Azure](https://portal.azure.com).
2.  Sur le côté gauche de l’écran, sélectionnez **+ nouvelle** > **bases de données** > **De la base de données SQL**:

    ![Restaurer une base de données SQL d’Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)

3.  Sélectionnez **la sauvegarde** comme source et sélectionnez la sauvegarde à restaurer. Spécifiez un nom de base de données, un serveur que vous souhaitez restaurer la base de données, et puis cliquez sur **créer**:
  
    ![Restaurer une base de données SQL d’Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

Surveiller l’état de l’opération de restauration en cliquant sur l’icône de notification dans le coin supérieur droit de la page. 


## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de l’activité commerciale et scénarios, consultez [vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- Pour obtenir des informations sur les sauvegardes de base de données SQL Azure automatisé, consultez [SQL de base de données des sauvegardes automatisées](sql-database-automated-backups.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [restauration d’une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Active-géo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, voir [copie de la base de données](sql-database-copy.md)
