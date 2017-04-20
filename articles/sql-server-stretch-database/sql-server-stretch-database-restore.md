<properties
    pageTitle="Restaurer les bases de données compatibles avec Stretch | Microsoft Azure"
    description="Apprenez à restaurer Stretch\-activé des bases de données."
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
    ms.date="08/01/2016"
    ms.author="douglasl"/>

# <a name="restore-stretch-enabled-databases"></a>Restaurer les bases de données prenant en charge l’étirement

Restaurez une sauvegarde de base de données lorsque cela est nécessaire pour récupérer à partir de nombreux types de défaillances, les erreurs et les catastrophes naturelles.

Pour plus d’informations sur la sauvegarde, consultez [les bases de données prenant en charge l’étirement de la sauvegarde](sql-server-stretch-database-backup.md).

>   [AZURE.NOTE] La sauvegarde n'est qu’une partie d’une solution de continuité et d’une haute disponibilité totale. Pour plus d’informations sur la disponibilité élevée, consultez [Solutions de haute disponibilité](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="restore-your-sql-server-data"></a>Restaurez vos données de SQL Server
Pour récupérer à partir de défaillance matérielle ou de corruption, restaurer l’étirement de la\-activée de la base de données SQL Server à partir d’une sauvegarde. Vous pouvez continuer à utiliser les méthodes de restauration de SQL Server que vous utilisez actuellement. Pour plus d’informations, consultez [vue d’ensemble de la récupération et de restauration](https://msdn.microsoft.com/library/ms191253.aspx).

Après avoir restauré la base de données SQL Server, vous devez exécuter la procédure stockée **sys.sp_rda_reauthorize_db** pour rétablir la connexion entre l’étirement de la\-activé une base de données de SQL Server et la base de données Azure à distance. Pour plus d’informations, voir [restauration de la connexion entre la base de données SQL Server et la base de données Azure à distance](#restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database).

## <a name="restore-your-remote-azure-data"></a>Restaurez vos données Azure à distance

### <a name="recover-a-live-azure-database"></a>Restaurer une base de données Azure live
La base de données SQL Server étirement de service sur des snapshots Azure toutes les données en temps réel au moins toutes les 8 heures à l’aide d’instantanés de stockage Azure. Ces instantanés sont conservés pendant 7 jours. Cela vous permet de restaurer les données à un de moins de 21 points dans le temps au sein de ces 7 derniers jours à la fois lorsque l’instantané a été pris.

Pour restaurer une base de données Azure direct à un point antérieur dans le temps à l’aide du portail Azure, effectuer les opérations suivantes.

1. Connectez-vous au portail Azure.
2. Sur le côté gauche de l’écran, sélectionnez **Parcourir** et sélectionnez **Les bases de données SQL**.
3. Naviguez jusqu'à votre base de données et le sélectionner.
4. En haut de la lame de la base de données, cliquez sur **restaurer**.
5. Spécifiez un nouveau **nom de la base de données**, sélectionnez un **Point de restauration** et puis cliquez sur **créer**.
6. Le processus de restauration de base de données commence et peuvent être surveillé via des **NOTIFICATIONS**.

### <a name="recover-a-deleted-azure-database"></a>Restaurer une base de données Azure supprimé
Le service de base de données de SQL Server étirer sur Azure prend un instantané de la base de données avant une base de données est ignoré et il conserve pendant 7 jours. Après cela, il conserve ne sont plus des captures instantanées à partir de la base de données active. Cela vous permet de restaurer une base de données supprimé au point lorsqu’il a été supprimé.

Pour restaurer une base de données Azure supprimé le point lorsqu’il a été supprimé à l’aide du portail Azure, effectuer les opérations suivantes.

1. Connectez-vous au portail Azure.
2. Sur le côté gauche de l’écran, sélectionnez **Parcourir** et sélectionnez **Les serveurs SQL**.
3. Accédez à votre serveur et sélectionnez-le.
4. Faites défiler jusqu'à des opérations sur les lames de votre serveur, cliquez sur la mosaïque de **Bases de données supprimées** .
5. Sélectionnez la base de données supprimé que vous souhaitez restaurer.
5. Spécifiez un nouveau **nom de la base de données** et cliquez sur **créer**.
6. Le processus de restauration de base de données commence et peuvent être surveillé via des **NOTIFICATIONS**.

## <a name="restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database"></a>Restaurer la connexion entre la base de données SQL Server et la base de données Azure à distance

1.  Si vous souhaitez vous connecter à une base de données Azure restaurée avec un nom différent ou dans une région différente, exécutez la procédure stockée [sys.sp_rda_deauthorize_db](https://msdn.microsoft.com/library/mt703716.aspx) pour vous déconnecter de la base de données Azure précédente.  

2.  Exécutez la procédure stockée [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) pour reconnecter l’étirement local\-activé la base de données à la base de données Azure.  

    -   Fournir les informations d’identification de base de données de portée existant sous la forme d’un type sysname ou type varchar\(128\) valeur. \(N’utilisez pas varchar\(max\).\) Vous pouvez rechercher le nom d’informations d’identification dans la vue **sys.database\_étendue\_informations d’identification de**.  

    -   Spécifiez s’il faut effectuer une copie des données à distance et se connecter à la copie (recommandée).  

    ```tsql  
    USE <Stretch-enabled database name>;
    GO
    EXEC sp_rda_reauthorize_db
        @credential = N'<existing_database_scoped_credential_name>',
        @with_copy = 1 ;  
    GO
    ```  

## <a name="see-also"></a>Voir aussi

[Gérer et dépanner l’étirement de base de données](sql-server-stretch-database-manage.md)

[Sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Sauvegarder et restaurer des bases de données SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
