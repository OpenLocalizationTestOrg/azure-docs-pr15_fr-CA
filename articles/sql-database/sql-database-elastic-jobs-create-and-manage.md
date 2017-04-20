<properties
    pageTitle="Créer et gérer la mise à l’échelle des bases de données SQL Azure en utilisant des traitements élastiques | Microsoft Azure"
    description="Passez en revue la création et la gestion d’un travail de l’élasticité de la base de données."
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="ddove"/>

# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Créer et gérer la mise à l’échelle des bases de données SQL Azure en utilisant des traitements élastiques (aperçu)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)


**Les travaux élastique de la base de données** simplifient la gestion des groupes de bases de données en exécutant des opérations d’administration telles que les modifications de schéma, gestion des informations d’identification, mises à jour des données de référence, collecte des données de performances ou collection de télémétrie de clients (client). Les tâches de base de données élastiques est actuellement disponible via le portail Azure et les applets de commande PowerShell. Toutefois, les surfaces de portail Azure réduit la fonctionnalité limitée à l’exécution dans toutes les bases de données dans une [base de données élastique pool (aperçu)](sql-database-elastic-pool.md). Pour accéder à des fonctionnalités supplémentaires et l’exécution des scripts sur un groupe de bases de données, y compris un ensemble personnalisé ou un ensemble partagé (créé à l’aide de la [bibliothèque de client de base de données élastique](sql-database-elastic-scale-introduction.md)), reportez-vous à la section [Création et gestion des tâches à l’aide de PowerShell](sql-database-elastic-jobs-powershell.md). Pour plus d’informations sur les tâches, consultez [vue d’ensemble des tâches élastique de la base de données](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Pour une version d’évaluation gratuite, consultez [un mois gratuite](https://azure.microsoft.com/pricing/free-trial/).
* Un pool d’élasticité de la base de données. Voir [pools de base de données sur élastique](sql-database-elastic-pool.md)
* Installation des composants de service de travail élastique de la base de données. Reportez-vous à la section [installation du service de travail élastique de la base de données](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Création de travaux

1. À l’aide du [portail Azure](https://portal.azure.com), à partir d’un pool de travail élastique de la base de données existant, cliquez sur **créer le travail**.
2. Tapez dans le nom d’utilisateur et le mot de passe de l’administrateur de base de données (créée lors de l’installation de travaux) pour la base de données de contrôle de travaux (stockage des métadonnées pour les travaux).

    ![Nom de la tâche, tapez ou collez dans le code, puis sur Exécuter][1]
2. De la lame de **Créer une tâche** , tapez un nom pour la tâche.
3. Tapez le nom d’utilisateur et le mot de passe pour se connecter aux bases de données cible avec les autorisations suffisantes pour l’exécution du script de réussir.
4. Collez ou tapez dans le script T-SQL.
5. Cliquez sur **Enregistrer** , puis sur **exécuter**.

    ![Créer des tâches et exécuter][5]

## <a name="run-idempotent-jobs"></a>Exécuter des travaux d’idempotent

Lorsque vous exécutez un script sur un ensemble de bases de données, il se peut que vous devez vous assurer que le script est idempotent. Autrement dit, le script doit être en mesure d’exécuter plusieurs fois, même si elle a échoué avant dans un état incomplet. Par exemple, lorsqu’un script échoue, la tâche sera automatiquement retentée jusqu'à ce qu’il réussisse (dans des limites, comme la nouvelle tentative logique finalement cesse de la nouvelle tentative). La façon de procéder est d’utiliser l’une clause « IF EXISTS » et supprimez toute instance trouvée avant de créer un nouvel objet. Un exemple est illustré ici :

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Sinon, utiliser une clause « IF NOT EXISTS » avant de créer une nouvelle instance :

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Ce script met à jour la table créée précédemment.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Vérification du statut de la tâche

Une fois un projet a commencé, vous pouvez vérifier sa progression.

1. À partir de la page pool élastique de la base de données, cliquez sur **Gérer les travaux**.

    ![Cliquez sur « Gérer les travaux »][2]

2. Cliquez sur le nom de (a) d’une tâche. L' **état** peut être « Terminée » ou « Échec ». Les détails de la tâche apparaissent (b) avec la date et l’heure de la création et l’exécution. La liste ci-dessous le (c) affiche la progression du script sur chaque base de données dans le pool, précisant la date et l’heure.

    ![Vérification d’un projet terminé][3]


## <a name="checking-failed-jobs"></a>Échec des travaux de vérification

Si un travail échoue, un journal de son exécution peut être trouvé. Cliquez sur le nom de la tâche a échoué pour afficher ses détails.

![Vérifiez l’échec d’un travail][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 
