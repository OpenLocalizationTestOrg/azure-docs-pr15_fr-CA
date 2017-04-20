<properties
    pageTitle="Résoudre les problèmes de sauvegarde et de restauration avec la base de données de SQL Azure"
    description="Découvrez comment restaurer une base de données à partir des erreurs et des pannes à l’aide des réplicas et des sauvegardes de base de données de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="restore-a-database-to-a-previous-point-in-time-restore-a-deleted-database-or-recover-from-a-data-center-outage"></a>Restaurer une base de données à un point antérieur dans le temps, restauration d’une base de données supprimé ou récupérer d’une panne du centre de données

Base de données de SQL permet de conserver les réplicas de votre base de données afin de récupérer à partir des interruptions et des erreurs des utilisateurs. Les options disponibles dépendent du niveau de service de base de données et les options que vous choisissez. Consultez la [Vue d’ensemble de la continuité d’activité commerciale](sql-database-business-continuity.md) pour les détails et les considérations de conception.

## <a name="to-restore-a-database-to-a-previous-point-in-time"></a>Pour restaurer une base de données à un point antérieur dans le temps
1.  Dans le [Portail Azure](https://azure.microsoft.com/), cliquez sur **bases de données SQL**.
2.  Sélectionnez votre base de données à partir de la liste et cliquez sur **restaurer**.
3.  Tapez un nouveau nom pour la base de données, choisissez la date et l’heure à restaurer à partir de, puis cliquez sur **créer.**
4.  Effectuer des ajustements app que nécessaire pour faire référence à la nouvelle base de données. Reportez-vous à la section [restauration d’une base de données à un point dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="to-restore-an-accidentally-deleted-database"></a>Pour restaurer une base de données supprimé accidentellement
1.  Dans le [Portail Azure](https://azure.microsoft.com/), cliquez sur **les serveurs SQL**.
2.  Sélectionnez le serveur hébergeant la base de données à partir de la liste.
3.  Sur le serveur lame, faites défiler et cliquez sur **bases de données supprimées**.
4.  Sélectionnez la base de données à restaurer, puis cliquez sur **créer**.
5.  Effectuer des ajustements app que nécessaire pour faire référence à la nouvelle base de données. Reportez-vous à la section [restauration d’une base de données supprimée](sql-database-recovery-using-backups.md#deleted-database-restore).

## <a name="to-recover-from-a-regional-datacenter-outage"></a>Pour récupérer d’une panne du centre de données régional
Avec les bases de données Standard et Premium, si vous avez configuré la réplication geo secondaires, vous pouvez récupérer à l’aide de ces zones secondaires. Cela vous donne la possibilité de restaurer une base de données avec un moins de risque de perte de données. Pour plus d’informations, consultez [restaurer une base de données Azure SQL à l’aide de sauvegardes de base de données automatisée](sql-database-disaster-recovery.md) .

Azure fournit également des sauvegardes de chaque base de données dans une région différente (une sauvegarde geo redondante). Vous pouvez créer une nouvelle base de données à partir de ces sauvegardes, appelé géo-restauration, mais il est probable que vous allez perdre des données si vous utilisez cette méthode uniquement.

**Pour restaurer une base de données à l’aide de géo-restauration :**

- Dans le [Portail Azure](https://azure.microsoft.com/), cliquez sur **Nouveau**et cliquez sur **données et stockage**, cliquez sur **Base de données SQL**, puis sélectionnez **sauvegarde** comme source de base de données. Pour plus d’informations, consultez [restaurer une base de données Azure SQL en cas de panne](sql-database-disaster-recovery.md) .