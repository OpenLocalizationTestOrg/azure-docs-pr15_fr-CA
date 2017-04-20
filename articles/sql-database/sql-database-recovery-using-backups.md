<properties
   pageTitle="La continuité d’activité en nuage - restaurer une base de données supprimé - de la base de données SQL | Microsoft Azure"
   description="Obtenir des informations sur la restauration de Point-à-temps, ce qui vous permet de restaurer une base de données de SQL Azure à un point antérieur dans le temps (jusqu'à 35 jours)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/01/2016"
   ms.author="sstein"/>

# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Restaurer une base de données Azure SQL à l’aide de sauvegardes de base de données automatisée

Base de données de SQL fournit trois options pour la récupération de base de données à l’aide de [que sauvegardes automatisées de la base de données SQL](sql-database-automated-backups.md). Vous pouvez restaurer une base de données à partir des sauvegardes exécutée par le service pendant leur [période de rétention](sql-database-service-tiers.md) pour :

- Une nouvelle base de données sur le même serveur logique récupéré à un point donné dans le temps dans la période de rétention. 
- Une base de données sur le même serveur logique restauré jusqu’au moment de la suppression d’une base de données supprimée.
- Une nouvelle base de données sur un serveur logique dans une région a été récupérée permettant les sauvegardes quotidiennes plus récentes dans le stockage blob de réplication géographique (RA-GRS).

Vous pouvez également utiliser des [sauvegardes automatisées de la base de données SQL](sql-database-automated-backups.md) pour créer une [copie de la base de données](sql-database-copy.md) sur un serveur logique dans une région qui est cohérente avec la base de données SQL en cours. Vous pouvez utiliser copie de base de données et [l’Exporter vers un type sac à DOS](sql-database-export.md) pour archiver une copie cohérente de façon transactionnelle d’une base de données pour le stockage à long terme au-delà de la période de rétention, ou à transférer une copie de votre base de données vers un site ou une instance d’Azure VM de SQL Server.

## <a name="recovery-time"></a>Temps de récupération

Le temps de récupération pour restaurer une base de données à l’aide de sauvegardes de base de données automatisée est percutée par un certain nombre de facteurs : 
 - La taille de la base de données
 - Le niveau de performance de la base de données
 - Le nombre de journaux de transactions concernées
 - Le volume d’activité devant être relus pour récupérer le point de restauration
 - La bande passante du réseau, si la restauration est dans une autre région 
 - Le nombre de demandes de restauration simultanés en cours de traitement dans la région cible. 
 
 Pour une base de données très volumineuses et/ou active la restauration peut prendre plusieurs heures. S’il existe une interruption prolongée dans une région, il est possible qu’il y aura des grands nombres géo-des demandes de restauration en cours de traitement par d’autres régions. S’il existe un grand nombre de demandes, que cela peut augmenter le temps de récupération des bases de données dans cette région. La majorité des restaurations de la base de données complètes dans les 12 heures.

 Il n’y a pas de fonctionnalités intégrées pour restaurer la majeure. Le [base de données de SQL Azure : récupération de serveur complète](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) script est un exemple d’une façon d’accomplir cette tâche.

> [AZURE.IMPORTANT] Pour récupérer à l’aide de sauvegardes automatisées, vous devez être membre du rôle Collaborateur de SQL Server dans l’abonnement, ou être le propriétaire de l’abonnement. Vous pouvez récupérer à l’aide du portail Azure, PowerShell ou l’API REST. Vous ne pouvez pas utiliser Transact-SQL. 

## <a name="point-in-time-restore"></a>Restauration de point-à-temps

Point-à-temps Restore vous permet de restaurer une base de données existant sous la forme d’une nouvelle base de données à un point antérieur dans le temps sur le même serveur logique à l’aide de [que sauvegardes automatisées de la base de données SQL](sql-database-automated-backups.md). Impossible de remplacer la base de données existante. Vous pouvez restaurer à un point antérieur dans le temps à l’aide du [portail Azure](sql-database-point-in-time-restore-portal.md), [PowerShell](sql-database-point-in-time-restore-powershell.md) ou l' [API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Restauration de point-à-temps : Portail d’Azure](sql-database-point-in-time-restore-portal.md)
- [Restauration de point-à-temps : PowerShell](sql-database-point-in-time-restore-powershell.md)

La base de données peut être restaurée à un niveau de performance ou un élastique pool. Vous devez vous disposez d’un contingent DTU suffisant sur le serveur logique ou d’un pool élastique. Gardez à l’esprit que la restauration crée une nouvelle base de données et que le niveau de performances et de la couche de service de la base de données restaurée peut être différent de celle de l’état actuel de la base de données active. Une fois terminé, la base de données restaurée est normal accessible en ligne facturés aux tarifs habituels, en fonction de son niveau de performances et les niveaux de service. Vous n’entraînez pas de frais jusqu'à ce que la restauration de la base de données est terminée.

En général, vous restaurez une base de données à un point antérieures à des fins de récupération. Lorsque vous procédez ainsi, vous pouvez traiter la base de données restaurée en remplacement de la base de données d’origine ou l’utiliser pour extraire des données et mettre à jour la base de données d’origine. 

- ***De base de données de remplacement :*** Si la base de données restaurée est conçu comme un remplacement pour la base de données d’origine, vous devez vérifier le niveau de performance, ou niveau de service appropriés et mettre à l’échelle la base de données si nécessaire. Vous pouvez renommer la base de données d’origine et puis attribuez à la base de données restaurée le nom d’origine à l’aide de la commande ALTER DATABASE dans T-SQL. 
- ***Récupération des données :*** Si vous souhaitez récupérer des données à partir de la base de données restaurée pour récupérer d’une erreur de l’utilisateur ou l’application, vous devrez séparément écrire et exécuter les scripts de récupération données vous avez besoin pour extraire des données de la base de données restaurée à la base de données d’origine. Bien que l’opération de restauration peut prendre un certain temps, la restauration de la base de données sera visible dans la liste base de données dans l’ensemble. Si vous supprimez la base de données pendant la restauration, cela permet d’annuler l’opération et vous ne sera pas débitée de la base de données qui ne s’est pas terminé la restauration. 

Pour plus d’informations sur l’utilisation de la restauration de Point-à-temps pour récupérer les erreurs des utilisateurs et des applications, reportez-vous à la section Restauration de [Point-à-temps](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="deleted-database-restore"></a>Restauration de bases de données supprimées

Restauration de bases de données supprimées vous permet de restaurer une base de données supprimé à l’heure de la suppression d’une base de données supprimé sur le même serveur logique à l’aide de [que sauvegardes automatisées de la base de données SQL](sql-database-automated-backups.md). 

> [AZURE.IMPORTANT] Si vous supprimez une instance de serveur de base de données de SQL Azure, toutes ses bases de données sont également supprimées et ne peuvent pas être récupérés. Il n’y a aucune prise en charge pour restaurer un serveur supprimé pour l’instant.

Vous pouvez utiliser le même ou un nouveau nom de base de données pour la base de données restaurée. Vous pouvez utiliser le [portail Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) ou le [reste (mode de création = restauration)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [AZURE.SELECTOR]
- [Supprimer la restauration de la base de données : Azure portal](sql-database-restore-deleted-database-portal.md)
- [Supprimer la restauration de la base de données : PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="geo-restore"></a>Geo-restauration

Geo-restauration vous permet de restaurer une base de données SQL sur un serveur quelconque dans n’importe quelle région Azure à partir de la plus récente géo-réplication [automatisée de sauvegarde quotidienne](sql-database-automated-backups.md). Geo-restauration utilise une sauvegarde redondante geo comme source et peut être utilisé pour restaurer une base de données, même si la base de données ou d’un centre de données est inaccessible en raison d’une panne. Vous pouvez utiliser le [portail Azure](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md), ou la [reste (mode de création = récupération)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 

> [AZURE.SELECTOR]
- [Géo-restauration : Portail d’Azure](sql-database-geo-restore-portal.md)
- [Geo-restauration : PowerShell](sql-database-geo-restore-powershell.md)

Geo-restauration est l’option de récupération par défaut lorsque votre base de données n’est pas disponible en raison d’un incident dans la région où est hébergée la base de données. Si un incident de grande échelle dans un conduit de région à l’indisponibilité de votre application de base de données, vous pouvez utiliser géo-la fonction Restaurer pour restaurer une base de données à partir de la sauvegarde la plus récente d’un serveur dans une autre région. Toutes les sauvegardes sont répliquées geo et peut avoir un retard entre lorsque la sauvegarde est prise et géo-répliquées vers la Azure blob dans une région différente. Ce délai peut être jusqu'à une heure en cas de sinistre il peut y avoir des pertes de données 1 heure, c'est-à-dire les RPO de jusqu'à 1 heure. L’exemple suivant montre la restauration de la base de données à partir de la dernière sauvegarde quotidienne.

![geo-restauration](./media/sql-database-geo-restore/geo-restore-2.png)

Pour plus d’informations sur l’utilisation de géo-la fonction Restaurer pour récupérer en cas de panne, consultez [récupérer en cas de panne](sql-database-disaster-recovery.md)

> [AZURE.IMPORTANT] Geo-restauration est disponible avec tous les niveaux de service, il est le plus élémentaire de la reprise après sinistre disponible dans la base de données SQL avec les points et les temps de récupération d’estimation (insérer) la plus longue. Pour les bases de données de base avec une taille maximale de 2 Go géo-restauration fournit une solution de reprise après sinistre raisonnable avec un Insér de 12 heures. Pour les grandes bases de données Standard ou Premium, si considérablement les temps de récupération plus courts sont souhaitées ou pour réduire le risque de perte de données vous devez envisager d’utiliser géo-réplication Active. Geo-réplication Active offre un RPO inférieur et un Insér car elle ne requiert que vous initier un basculement vers le secondaire répliqué en continu. Pour plus d’informations, consultez [Géo-réplication](sql-database-geo-replication-overview.md)Active.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programme en effectuant récupération des sauvegardes automatisées

Comme indiqué plus haut, addiition vers le portail Azure, récupération de la base de données peut être effectuée en programmically à l’aide de PowerShell d’Azure et l’API REST. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles.

### <a name="powershell"></a>PowerShell

|Applet de commande|Description|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Obtient une ou plusieurs bases de données.|
|[Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/en-us/library/azure/mt693387.aspx)|Obtient une base de données supprimé, vous pouvez restaurer.|
|[Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx)|Obtient une sauvegarde redondante géographique d’une base de données.|
|[Restauration-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx)|Restaure une base de données SQL.|
||||

### <a name="rest-api"></a>API REST

|API|Description|
|---|-----------|
|[RESTE (mode de création = récupération)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Restaure une base de données|
|[Obtenir de créer ou de mettre à jour le statut de la base de données](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Retourne l’état au cours d’une opération de restauration|
||||



## <a name="summary"></a>Résumé

Sauvegardes automatiques protègent vos bases de données de l’utilisateur et des erreurs d’application, suppression de la base de données accidentelle et interruptions prolongées. Cette solution de coût zéro zéro-admin est disponible avec toutes les bases de données SQL. 

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de l’activité commerciale et scénarios, consultez [vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- Pour obtenir des informations sur les sauvegardes de base de données SQL Azure automatisé, consultez [SQL de base de données des sauvegardes automatisées](sql-database-automated-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Active-géo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, voir [copie de la base de données](sql-database-copy.md)
