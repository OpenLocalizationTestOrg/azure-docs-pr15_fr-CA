<properties
    pageTitle="Désactiver l’extension de base de données et restaurer des données à distance | Microsoft Azure"
    description="Découvrez comment désactiver l’étirement de base de données pour une table et éventuellement remettre les données à distance."
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
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="disable-stretch-database-and-bring-back-remote-data"></a>Désactiver l’extension de base de données et restaurer des données à distance

Pour désactiver l’étirement de base de données pour une table, sélectionnez **Stretch** pour une table dans SQL Server Management Studio. Sélectionnez ensuite une des options suivantes.

-   Désactivation de **| Restaurer les données à partir d’Azure**. Copie des données à distance pour la table à partir d’Azure à SQL Server, puis désactiver l’étirement de base de données pour la table. Cette opération entraîne des coûts de transfert de données, et elle ne peut pas être annulée.

-   Désactivation de **| Laisser les données dans Azure**. Désactiver la base de données de l’étirement de la table.  Abandonner les données distantes pour la table dans Azure.

Vous pouvez également utiliser Transact\-pour désactiver l’extension de base de données pour une table ou une base de données SQL.

Après avoir désactivé l’étirement de base de données pour une table, cesse de migration des données et des résultats de la requête n’incluront plus de résultats à partir de la table distante.

Si vous souhaitez simplement interrompre la migration des données, consultez [Suspendre et reprendre étirement de base de données](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] La désactivation d’étirement de base de données pour une table ou une base de données ne supprime pas l’objet distant. Si vous souhaitez supprimer de la table distante ou la base de données distante, vous devez déplacer à l’aide du portail de gestion Azure. Les objets distants continuent à supporter des coûts d’Azure jusqu'à ce que vous les supprimiez. Pour plus d’informations, consultez [Tarification de base de données SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-table"></a>Désactiver l’étirement de base de données pour une table

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>SQL Server Management Studio permet de désactiver l’étirement de base de données pour une table

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la table pour laquelle vous souhaitez désactiver l’étirement de base de données.

2.  Droit\-cliquez sur et sélectionnez **Stretch**puis sélectionnez une des options suivantes.

    -   Désactivation de **| Restaurer les données à partir d’Azure**. Copie des données à distance pour la table à partir d’Azure à SQL Server, puis désactiver l’étirement de base de données pour la table. Cette commande ne peut pas être annulée.

        >   [AZURE.NOTE] Copie des données à distance pour la table à partir d’Azure précédent pour SQL Server supporte les coûts de transfert de données. Pour plus d’informations, consultez les [Détails de la tarification de transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

        Une fois que toutes les données à distance a été copiées à partir de Azure sauvegardez sur SQL Server, étirement est désactivé pour la table.

    -   Désactivation de **| Laisser les données dans Azure**. Désactiver la base de données de l’étirement de la table.  Abandonner les données distantes pour la table dans Azure.

    >   [AZURE.NOTE] Désactivation de l’étirement de base de données pour une table ne supprime pas les données à distance ou la table distante. Si vous souhaitez supprimer de la table distante, vous devez déplacer à l’aide du portail de gestion Azure. La table distante continue à supporter des coûts d’Azure jusqu'à ce que vous les supprimiez. Pour plus d’informations, consultez [Tarification de base de données SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>Utilisez Transact\-pour désactiver l’extension de base de données pour une table SQL

-   Pour désactiver l’étirement pour une table et une copie des données à distance pour la table à partir d’Azure à SQL Server, exécutez la commande suivante. Une fois que toutes les données à distance a été copiées à partir de Azure sauvegardez sur SQL Server, étirement est désactivé pour la table.

    Cette commande ne peut pas être annulée.

    ```tsql
    USE <Stretch-enabled database name>;
    GO
    ALTER TABLE <Stretch-enabled table name>  
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    GO
    ```
    >   [AZURE.NOTE] Copie des données à distance pour la table à partir d’Azure précédent pour SQL Server supporte les coûts de transfert de données. Pour plus d’informations, consultez les [Détails de la tarification de transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

-   Pour désactiver l’étirement pour une table et abandonner les données à distance, exécutez la commande suivante.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] Désactivation de l’étirement de base de données pour une table ne supprime pas les données à distance ou la table distante. Si vous souhaitez supprimer de la table distante, vous devez déplacer à l’aide du portail de gestion Azure. La table distante continue à supporter des coûts d’Azure jusqu'à ce que vous les supprimiez. Pour plus d’informations, consultez [Tarification de base de données SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-database"></a>Désactiver l’étirement de base de données pour une base de données
Avant de pouvoir désactiver étirement de base de données pour une base de données, vous devez désactiver la base de données de l’étirement sur l’étirement des\-activé les tables dans la base de données.

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>SQL Server Management Studio permet de désactiver l’étirement de base de données pour une base de données

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la base de données pour lequel vous souhaitez désactiver l’étirement de base de données.

2.  Droit\-cliquez sur et sélectionnez les **tâches**, puis **Étirer**et sélectionnez puis sélectionnez **désactiver**.

>   [AZURE.NOTE] Désactivation de l’étirement de base de données pour une base de données ne supprime pas la base de données distante. Si vous souhaitez supprimer de la base de données distante, vous devez déplacer à l’aide du portail de gestion Azure. La base de données distant continue à supporter des coûts d’Azure jusqu'à ce que vous les supprimiez. Pour plus d’informations, consultez [Tarification de base de données SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>Utilisez Transact\-pour désactiver l’extension de base de données pour une base de données SQL
Exécutez la commande suivante.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] Désactivation de l’étirement de base de données pour une base de données ne supprime pas la base de données distante. Si vous souhaitez supprimer de la base de données distante, vous devez déplacer à l’aide du portail de gestion Azure. La base de données distant continue à supporter des coûts d’Azure jusqu'à ce que vous les supprimiez. Pour plus d’informations, consultez [Tarification de base de données SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="see-also"></a>Voir aussi

[ALTER DATABASE SET Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Suspendre et reprendre la base de données d’étirement](sql-server-stretch-database-pause.md)
