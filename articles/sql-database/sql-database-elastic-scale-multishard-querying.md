<properties 
    pageTitle="Interrogation d’éclater multiples | Microsoft Azure" 
    description="Exécuter des requêtes sur le milieu des fragments à l’aide de la bibliothèque de client élastique de la base de données." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/12/2016" 
    ms.author="torsteng"/>

# <a name="multi-shard-querying"></a>Interrogation d’éclater multiples

## <a name="overview"></a>Vue d’ensemble

Avec les [Outils de base de données élastique](sql-database-elastic-scale-introduction.md), vous pouvez créer des solutions de base de données sharded. **Interrogation d’éclater multiples** est utilisé pour des tâches telles que la collection de données et reporting qui nécessitent l’exécution d’une requête qui s’étend sur plusieurs milieu des fragments. (Comparez ceci au [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md), qui effectue tout le travail sur une seule partagé.) 

## <a name="overview"></a>Vue d’ensemble

1. Obtenir un [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) ou un [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) à l’aide de la méthode [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) , le [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)ou le [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx). Voir la [**construction d’un ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) et [**d’obtenir un RangeShardMap ou un ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Créer un objet **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)** .
2. Créer un **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
3. Définissez la **[propriété CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)** à une commande T-SQL.
3. Exécutez la commande en appelant la **[méthode ExecuteReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
4. Permet d’afficher les résultats à l’aide de la **[classe de MultiShardDataReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## <a name="example"></a>Exemple

Le code suivant illustre l’utilisation de partagé plusieurs requêtes à l’aide d’une donnée **ShardMap** nommé *myShardMap*. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                } 
           } 
    } 

 
Une différence essentielle est la construction d’éclater plusieurs connexions. Où **SqlConnection** fonctionne sur une base de données unique, la **MultiShardConnection** prend une ***collection de milieu des fragments*** comme entrée. Remplir la collection de milieu des fragments d’un plan d’éclater. La requête est ensuite exécutée sur la collection de milieu des fragments à l’aide de la sémantique de **UNION ALL** pour assembler un résultat total unique. Le cas échéant, le nom de l’éclater où provient de la ligne peut être ajouté à la sortie à l’aide de la propriété **ExecutionOptions** sur la commande. 

Notez l’appel à **myShardMap.GetShards()**. Cette méthode récupère tous les milieu des fragments de la carte d’éclater et offre un moyen simple pour exécuter une requête sur toutes les bases de données appropriées. La collection de milieu des fragments pour une requête de plusieurs partagée peut être affinée davantage en effectuant une requête LINQ sur la collection retournée par l’appel à **myShardMap.GetShards()**. En combinaison avec la stratégie de résultats partiels, la capacité actuelle dans l’interrogation de plusieurs partagé a été conçue pour fonctionnent bien pour des dizaines, des centaines de milieu des fragments.

Une limitation à l’interrogation de plusieurs partagé est actuellement l’absence de validation pour le milieu des fragments et des shardlets qui sont interrogés. Tandis que le routage dépendant des données vérifie qu’un partagé donné est une partie de la table partagé au moment de l’interrogation, éclater plusieurs requêtes n’effectuent pas cette vérification. Cela peut conduire à des requêtes multiples partagés s’exécutant sur des bases de données qui ont été supprimés de la carte d’éclater.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Partagé de multiples requêtes et opérations de fusion et fractionnement

Les requêtes multiples partagés ne vérifient pas si shardlets sur la base de données interrogée participent à des opérations de fusion et fractionnement en cours. (Reportez-vous à la section [mise à l’échelle à l’aide de l’outil de fusion et fractionnement de base de données élastique](sql-database-elastic-scale-overview-split-and-merge.md).) Cela peut entraîner des incohérences où affichent les lignes à partir de la même shardlet de plusieurs bases de données dans la même requête éclater multiples. Tenez compte de ces limitations et envisagez de drainage opérations de fusion et fractionnement en cours et les modifications apportées à la table partagé lors de l’exécution des requêtes multiples partagés.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>Voir aussi
**[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)** classes et méthodes.


Gérer le milieu des fragments à l’aide de la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md). Inclut un espace de noms appelé [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) qui fournit la possibilité d’interroger plusieurs milieu des fragments à l’aide d’une seule requête et les résultats. Il fournit une abstraction de requête sur une collection de milieu des fragments. Il fournit également des stratégies d’exécution alternatifs, des résultats partiels en particulier, pour faire face aux défaillances lors de l’interrogation sur le milieu des fragments de nombreux.  

 