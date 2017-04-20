<properties 
    pageTitle="Ajouter un partagé à l’aide des outils de base de données élastique | Microsoft Azure" 
    description="Comment utiliser les API d’échelle élastique pour ajouter de nouveau milieu des fragments à un partagé défini." 
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
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="adding-a-shard-using-elastic-database-tools"></a>Ajouter un partagé à l’aide des outils de base de données élastique

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Pour ajouter un partagé pour une nouvelle plage ou d’une clé  

Les applications doivent souvent simplement à ajouter nouveau milieu des fragments pour gérer des données qui sont prévues à partir de nouvelles clés ou des plages de clés pour un mappage partagé qui existe déjà. Par exemple, une application sharded par ID de clients peut nécessiter de prévoir d’un nouveau partagé pour un nouveau client ou tous les mois sharded données peut-être un nouveau partagé mis en service avant le début de chaque nouveau mois. 

Si la nouvelle plage de valeurs de clé n’est pas déjà partie d’un mappage existant, il est très simple d’ajouter le nouveau partagé et l’associer à la nouvelle clé ou la plage pour que partagé. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exemple : ajout d’un partagé et sa plage de valeurs pour un mappage partagé existant
Cet exemple utilise la [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx) la [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx), [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0})) , méthodes et crée une instance de la classe [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.) . Dans l’exemple ci-dessous, une base de données nommée **sample_shard_2** et tous les objets de schéma nécessaire qu’elle ont été créés pour contenir la plage [300, 400).  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


Comme alternative, vous pouvez utiliser Powershell pour créer un nouveau gestionnaire de table partagé. Un exemple est disponible [ici](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).
## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Pour ajouter un partagé pour une partie vide d’une plage existante  

Dans certains cas, vous avez déjà mappé une plage à un partagé et partiellement remplies avec les données, mais vous souhaitez maintenant les données à venir vers un autre partagé. Par exemple, vous partagé par intervalle de jours et déjà alloués 50 jours à un partagé, mais jour 24, vous souhaitez que les données futures des terres dans un autre partagé. [L’outil de fusion et fractionnement](sql-database-elastic-scale-overview-split-and-merge.md) élastique de la base de données peut effectuer cette opération, mais si le déplacement de données n’est pas nécessaire (par exemple, les données de la plage de jours [25, 50), c'est-à-dire, jour 25 inclus à 50 exclusif, n’existe pas encore) vous pouvez effectuer cette entièrement utilisant directement les API de gestion de table partagé.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exemple : fractionnement d’une plage et en assignant la partie vide à un partagé récemment ajoutés

Une base de données nommée « sample_shard_2 » et tous les objets de schéma nécessaire qu’elle a été créée.  

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Important**: utilisez cette technique seulement si vous êtes sûr que la plage de la mise à jour du mappage est vide.  Les méthodes ci-dessus ne vérifient pas les données pour la plage qui est déplacée, il est préférable d’inclure des vérifications dans votre code.  Si il existe des lignes de la plage en cours de déplacement, la répartition réelle des données ne correspondra pas le mappage partagé mis à jour. Pour effectuer l’opération dans ces cas, utilisez l' [outil de fusion et fractionnement](sql-database-elastic-scale-overview-split-and-merge.md) .  


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
