<properties
    pageTitle="Suspendre et reprendre la migration de données (Database Stretch) | Microsoft Azure"
    description="Découvrez comment suspendre ou reprendre la migration des données vers Azure."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="pause-and-resume-data-migration-stretch-database"></a>Suspendre et reprendre la migration de données (base de données de l’échelle)

Pour suspendre ou reprendre la migration des données vers Azure, sélectionnez **Stretch** pour une table dans SQL Server Management Studio et sélectionnez **Suspendre** pour suspendre la migration des données ou **reprendre** pour reprendre la migration des données. Vous pouvez également utiliser Transact\-SQL pour suspendre ou reprendre la migration des données.

Migration de données pause sur des tables individuelles lorsque vous souhaitez résoudre des problèmes sur le serveur local ou pour optimiser la bande passante réseau disponible.

## <a name="pause-data-migration"></a>Migration des données de pause

### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>SQL Server Management Studio permet de suspendre la migration des données

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez l’étirement de la\-activé la table pour laquelle vous souhaitez interrompre la migration des données.

2.  Droit\-cliquez sur et sélectionnez **Stretch**puis sélectionnez **Pause**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Utilisez Transact\-SQL pour suspendre la migration des données
Exécutez la commande suivante.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Reprendre la migration des données

### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Utilisez SQL Server Management Studio pour reprendre la migration des données

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez l’étirement de la\-activé la table pour laquelle vous souhaitez reprendre la migration des données.

2.  Droit\-cliquez sur et sélectionnez **Stretch**puis sélectionnez **reprendre**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Utilisez Transact\-SQL pour reprendre la migration des données
Exécutez la commande suivante.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Vérifier si la migration est actif ou en pause

### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Utilisez SQL Server Management Studio pour vérifier si la migration est actif ou en pause
Dans SQL Server Management Studio, ouvrez le **Moniteur de base de données de Stretch** et vérifier la valeur de la colonne **État de la Migration** . Pour plus d’informations, consultez [moniteur et résoudre les problèmes de migration de données](sql-server-stretch-database-monitor.md).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Utilisez Transact-SQL pour vérifier si la migration est actif ou en pause
La vue de catalogue **sys.remote_data_archive_tables** de la requête et vérifier la valeur de la colonne **is_migration_paused** . Pour plus d’informations, consultez [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Voir aussi

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[moniteur et résoudre les problèmes de migration de données](sql-server-stretch-database-monitor.md)
