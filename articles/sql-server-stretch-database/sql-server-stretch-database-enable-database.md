<properties
    pageTitle="Activer l’extension de base de données pour une base de données | Microsoft Azure"
    description="Découvrez comment configurer une base de données pour la base de données de l’échelle."
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

# <a name="enable-stretch-database-for-a-database"></a>Activer l’extension de base de données pour une base de données

Pour configurer une base de données existante pour étirement de base de données, sélectionnez les tâches **| Étirement | Activer** pour une base de données dans SQL Server Management Studio pour ouvrir l’Assistant **Activer la base de données pour étirer** . Vous pouvez également utiliser Transact\-pour activer l’étirement de base de données pour une base de données SQL.

Si vous sélectionnez les tâches **| Étirement | Activer** pour une table individuelle et vous n'avez pas encore activé la base de données pour la base de données de l’étirement, l’Assistant configure la base de données pour la base de données de l’échelle et vous permet de sélectionner des tables en tant que partie du processus. Suivez les étapes décrites dans cette rubrique au lieu des étapes décrites dans [Activer la base de données Stretch pour une table](sql-server-stretch-database-enable-database.md).

L’activation d’étirement de base de données sur une base de données ou une table nécessite db\_les autorisations de propriétaire. L’activation d’extension de base de données sur une base de données nécessite également les autorisations de base de données du contrôle.

 >   [AZURE.NOTE] Ultérieurement, si vous désactivez la base de données de l’échelle, n’oubliez pas que la désactivation d’étirement de base de données pour une table ou une base de données ne supprime pas l’objet distant. Si vous souhaitez supprimer de la table distante ou la base de données distante, vous devez déplacer à l’aide du portail de gestion Azure. Les objets distants continuent à supporter des coûts d’Azure jusqu'à ce que vous les supprimez manuellement.

## <a name="before-you-get-started"></a>Avant de commencer

-   Avant de configurer une base de données pour étirer, nous vous conseillons d’exécuter le Gestionnaire de base de données Stretch pour identifier les bases de données et les tables qui sont éligibles pour étirer. Le Gestionnaire de base de données Stretch identifie également les problèmes de blocage. Pour plus d’informations, consultez [identification de bases de données et des tables de base de données de l’échelle](sql-server-stretch-database-identify-databases.md).

-   Examinez les [Limitations pour étirement la base de données](sql-server-stretch-database-limitations.md).

-   Base de données étirement migre des données vers Azure. Par conséquent, vous devez avoir un compte Azure et un abonnement pour la facturation. Pour obtenir un compte Azure, [Cliquez ici](http://azure.microsoft.com/pricing/free-trial/).

-   Avoir les informations de connexion et d’ouverture de session vous avez besoin pour créer un nouveau serveur Azure ou pour sélectionner un serveur Azure.

## <a name="EnableTSQLServer"></a>Au préalable : Activer l’étirement de base de données sur le serveur
Avant d’activer la base de données de l’échelle sur une base de données ou une table, vous devez l’activer sur le serveur local. Cette opération requiert des autorisations de sysadmin ou serveradmin.

-   Si vous disposez d’autorisations d’administration, l’Assistant **Activer la base de données pour étirer** configure le serveur pour étirer.

-   Si vous n’avez pas les autorisations requises, un administrateur doit activer cette option manuellement en exécutant **sp\_configurer** avant d’exécuter l’Assistant, ou un administrateur doit exécuter l’Assistant.

Pour activer les extension de base de données sur le serveur manuellement, exécutez **sp\_configurer** et activer l’option **d’archivage de données à distance** . L’exemple suivant active l’option **d’archivage de données à distance** , définissez sa valeur sur 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
Pour plus d’informations, voir [sp_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx)et [configurer les données distantes archiver l’Option de Configuration de serveur](https://msdn.microsoft.com/library/mt143175.aspx) .

## <a name="Wizard"></a>Utilisez l’Assistant pour activer l’extension de base de données sur une base de données
Pour obtenir des informations sur la base de données permettent d’étirer Assistant, y compris les informations que vous entrez et les choix que vous avez à faire, reportez-vous à la section [mise en route en exécutant la base de données pour étirer Assistant Activer](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Utilisez Transact\-pour activer l’extension de base de données sur une base de données SQL
Avant d’activer la base de données de l’étirement sur des tables individuelles, vous devez l’activer sur la base de données.

L’activation d’étirement de base de données sur une base de données ou une table nécessite db\_les autorisations de propriétaire. L’activation d’extension de base de données sur une base de données nécessite également les autorisations de base de données du contrôle.

1.  Avant de commencer, choisissez un serveur Azure existant pour les données qui migrent d’étirement de la base de données de, ou créer un nouveau serveur Azure.

2.  Sur le serveur Azure, créez une règle de pare-feu avec la plage d’adresses IP de la SQL Server qui permet à SQL Server communiquent avec le serveur distant.

    Vous pouvez facilement trouver les valeurs que vous avez besoin et créez la règle de pare-feu en essayant de se connecter au serveur Azure à partir de l’Explorateur d’objets dans SQL Server Management Studio (SSMS). SSMS vous aide à créer la règle en ouvrant la boîte de dialogue qui inclut déjà les valeurs d’adresse IP requises.

    ![Créer une règle de pare-feu dans SSMS][FirewallRule]

3.  Pour configurer une base de données SQL Server pour la base de données de l’échelle, la base de données doit avoir une clé principale de base de données. La clé principale de base de données permet de sécuriser les informations d’identification à base de données de l’étirement utilise pour se connecter à la base de données distante. Voici un exemple qui crée une nouvelle clé principale de base de données.

    ```tsql
    USE <database>;
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
    GO
    ```

    Pour plus d’informations sur la clé principale de base de données, consultez [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) et [créer une clé principale de base de données](https://msdn.microsoft.com/library/aa337551.aspx).

4.  Lorsque vous configurez une base de données pour la base de données de l’échelle, vous devez fournir une information d’identification de base de données de l’étirement à utiliser pour la communication entre les locaux sur SQL Server et le serveur Azure. Vous disposez de deux options.

    -   Vous pouvez fournir des informations d’identification d’administrateur.

        -   Si vous activez l’étirement de base de données en exécutant l’Assistant, vous pouvez créer les informations d’identification à ce moment.

        -   Si vous envisagez d’activer l’étirement de base de données en exécutant **l’Instruction ALTER DATABASE**, vous devez créer les informations d’identification manuellement avant d’exécuter **ALTER DATABASE** pour activer l’extension de base de données.

        Voici un exemple qui crée de nouvelles informations d’identification.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>';
        GO
        ```

        Pour plus d’informations sur les informations d’identification, consultez [Création de base de données étendue d’informations d’identification (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Création d’informations d’identification requiert des autorisations de modifier les informations d’identification n’importe où.

    -   Vous pouvez utiliser un compte de service fédérée pour le SQL Server de communiquer avec le serveur Azure lorsque les conditions suivantes sont vraies.

        -   Le compte de service sous lequel s’exécute l’instance de SQL Server est un compte de domaine.

        -   Le compte de domaine appartient à un domaine, dont Active Directory est fédéré avec Azure Active Directory.

        -   Le serveur Azure distant est configuré pour prendre en charge de l’authentification Active Directory de Azure.

        -   Le compte de service sous lequel s’exécute l’instance de SQL Server doit être configuré comme un compte dbmanager ou sysadmin sur le serveur distant de Azure.

5.  Pour configurer une base de données pour la base de données de l’étirement, exécutez la commande ALTER DATABASE.

    1.  Pour l’argument de serveur, indiquez le nom d’un serveur Azure, y compris la `.database.windows.net` la partie du nom de \- , par exemple, `MyStretchDatabaseServer.database.windows.net`.

    2.  Fournir une information d’identification d’administrateur existant avec l’argument d’informations d’identification ou spécifiez fédéré\_SERVICE\_compte = ON. L’exemple suivant fournit des informations d’identification existantes.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO
    ```

## <a name="next-steps"></a>Étapes suivantes
-   [Activer la base de données Stretch pour une table](sql-server-stretch-database-enable-table.md) permettent de tables supplémentaires.

-   [Base de données de moniteur étirement de](sql-server-stretch-database-monitor.md) voir l’état de la migration des données.

-   [Suspendre et reprendre la base de données d’étirement](sql-server-stretch-database-pause.md)

-   [Gérer et dépanner l’étirement de base de données](sql-server-stretch-database-manage.md)

-   [Bases de données prenant en charge l’étirement de la sauvegarde](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Voir aussi

[Identifier les bases de données et les tables de base de données de l’échelle](sql-server-stretch-database-identify-databases.md)

[ALTER DATABASE SET Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png
