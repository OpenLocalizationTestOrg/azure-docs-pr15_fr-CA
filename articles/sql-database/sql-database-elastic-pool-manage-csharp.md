<properties
    pageTitle="Surveiller et de gérer un pool de la base de données élastique avec C# | Microsoft Azure"
    description="Techniques de développement C# de base de données permet de gérer un pool élastique de la base de données de base de données de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-cx23"></a>Surveiller et de gérer un pool élastique de la base de données C & #x 23 ; 

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Apprenez à gérer un [pool d’élastique de la base de données](sql-database-elastic-pool.md) à l’aide de C & #x 23 ;. 

>[AZURE.NOTE] De nombreuses nouvelles fonctionnalités de base de données de SQL sont pris en charge uniquement lorsque vous utilisez le [modèle de déploiement d’Azure le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md), donc vous devez toujours utiliser plus tard **bibliothèque de gestion de base de données de SQL Azure pour .NET ([documents](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Les anciennes [bibliothèques basé sur le modèle de déploiement classique](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) sont pris en charge pour la compatibilité ascendante, nous vous recommandons d’utiliser les bibliothèques de gestionnaire de ressources en fonction de plus récentes.

Pour terminer la procédure décrite dans cet article, vous devez les éléments suivants :

- Un pool élastique (le pool que vous voulez gérer). Pour créer un pool, voir [Création d’un pool de la base de données élastique avec C#](sql-database-elastic-pool-create-csharp.md).
- Visual Studio. Pour obtenir un exemplaire gratuit de Visual Studio, consultez la page [Téléchargements de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="move-a-database-into-an-elastic-pool"></a>Déplacer une base de données dans un pool élastique

Vous pouvez déplacer une base de données autonome dans ou en dehors d’un pool.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Liste des bases de données dans un pool élastique

Pour récupérer toutes les bases de données dans un pool, appelez la méthode [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Modifier les paramètres de performance d’un pool

Récupérer les propriétés de pool existantes. Modifiez les valeurs et exécuter la méthode CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Latence des opérations élastique

- Généralement la modification de l’eDTUs min par eDTUs de base de données ou max par base de données se termine dans les cinq minutes ou moins.
- Temps de modifier la taille du pool (eDTUs) dépend de la taille combinée de toutes les bases de données dans le pool. Modifications moyenne de 90 minutes ou moins par 100 Go. Par exemple, si l’espace total de toutes les bases de données dans le pool est 200 Go, puis le délai prévu pour la modification de l’eDTU du pool par pool est de 3 heures ou moins.




## <a name="additional-resources"></a>Ressources supplémentaires

- [Des codes d’erreur SQL pour les applications de client de base de données SQL : base de données d’erreur de connexion et d’autres problèmes](sql-database-develop-error-messages.md).
- [Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [API de gestion de ressources Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [Créer un nouveau pool d’élasticité de la base de données avec C#](sql-database-elastic-pool-create-csharp.md)
- [Quand un pool élastique de la base de données doit être utilisé ?](sql-database-elastic-pool-guidance.md)
- Reportez-vous à la section [mise à l’échelle des base de données de SQL Azure](sql-database-elastic-scale-introduction.md): outils de base de données élastique permet d’horizontale, déplacer des données, de requête ou de créer des transactions.

