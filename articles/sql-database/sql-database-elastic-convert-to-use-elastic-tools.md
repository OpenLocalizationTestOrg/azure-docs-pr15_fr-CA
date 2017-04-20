<properties
   pageTitle="Migrer des bases de données à échelle | Microsoft Azure"
   description="Convertir des bases de données sharded pour utiliser des outils d’élastique de la base de données en créant un partagé Gestionnaire de mappage"
   services="sql-database"
   documentationCenter=""
   authors="ddove"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/24/2016"
   ms.author="ddove"/>

# <a name="migrate-existing-databases-to-scale-out"></a>Migration des bases de données existantes à l’horizontale

Gérer facilement votre plus grande échelle sharded bases de données existantes à l’aide des outils de base de données de base de données de SQL Azure (par exemple, la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md)). Vous devez d’abord convertir un jeu de bases de données pour utiliser le [Gestionnaire de cartes partagé](sql-database-elastic-scale-shard-map-management.md)existant. 

## <a name="overview"></a>Vue d’ensemble
Pour migrer une base de données sharded : 

1. Préparer la [base de données de gestionnaire de table partagé](sql-database-elastic-scale-shard-map-management.md).
2. Créer le mappage partagé.
3. Préparer les milieu des fragments individuels.  
2. Ajouter des mappages à la table partagé.

Ces techniques peuvent être implémentées à l’aide de la [bibliothèque client de.NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), ou les scripts PowerShell sur [Azure SQL DB - scripts d’outils élastique de la base de données](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Ces exemples utilisent les scripts PowerShell.

Pour plus d’informations sur le ShardMapManager, reportez-vous à la section [Éclater mapper la gestion](sql-database-elastic-scale-shard-map-management.md). Pour une vue d’ensemble des outils élastique de la base de données, consultez [vue d’ensemble des fonctionnalités d’élastique de la base de données](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Préparer la base de données du Gestionnaire de table partagé

Le Gestionnaire de table partagé est une base de données spéciale qui contient les données pour gérer les bases de données à grande échelle. Vous pouvez utiliser une base de données existante, ou créer une nouvelle base de données. Notez qu’une base de données agissant en tant que gestionnaire de mappage partagé ne doit pas être la même base de données sous la forme d’un partagé. Notez également que le script PowerShell ne crée pas la base de données pour vous. 

## <a name="step-1-create-a-shard-map-manager"></a>Étape 1 : créer un partagé Gestionnaire de mappage

    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Pour lequel récupérer le Gestionnaire de mappage partagé

Après sa création, vous pouvez récupérer le Gestionnaire de mappage partagé avec cette applet de commande. Cette étape est nécessaire chaque fois que vous avez besoin d’utiliser l’objet ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 

  
## <a name="step-2-create-the-shard-map"></a>Étape 2 : créer le mappage partagé

Vous devez sélectionner le type de mappage partagé à créer. Le choix dépend de l’architecture de base de données : 

1. Mono-utilisateur par base de données (pour les termes, voir le [Glossaire](sql-database-elastic-scale-glossary.md)). 
2. Plusieurs des locataires par base de données (deux types) :
    3. Mappage de la liste
    4. Mappage de plage
 

Pour un modèle mono-utilisateur, créez un mappage de partagé de **mappage de la liste** . Le modèle de mono-utilisateur attribue une base de données par les clients. Il s’agit d’un modèle efficace pour les développeurs de SaaS comme il simplifie la gestion.

![Mappage de la liste][1]

Le modèle mutualisée affecte plusieurs locataires à une seule base de données (et vous pouvez distribuer des groupes de clients dans plusieurs bases de données). Utilisez ce modèle lorsque vous pensez que chaque client pour que les besoins de données de petite taille. Dans ce modèle, nous affecter une tranche de clients à une base de données à l’aide du **mappage de la plage**. 
 

![Mappage de plage][2]

Ou vous pouvez implémenter un modèle de base de données partagée à l’aide d’un *mappage de la liste* pour affecter les différents utilisateurs à une seule base de données. Par exemple, DB1 est utilisé pour stocker des informations à propos des id clients 1 et 5, et DB2 stocke les données des clients 7 et 10 de clients. 

![Plusieurs des locataires sur la seule base de données][3] 

**En fonction de votre choix, choisissez une des options suivantes :**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Option 1 : créer un mappage partagé pour un mappage de liste
Créer un mappage de partagé à l’aide de l’objet ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 
 
 
### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Option 2 : créer un mappage partagé pour un mappage de plage

Notez que pour utiliser ce modèle de mappage, l’id de client valeurs doit être des plages continues, et il est acceptable d’avoir une discontinuité dans les plages en ignorant simplement la plage lors de la création de bases de données.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Option 3 : Répertorier les mappages sur une seule base de données
Configuration de ce modèle requiert également la création d’un mappage de liste comme indiqué à l’étape 2, option 1.

## <a name="step-3-prepare-individual-shards"></a>Étape 3 : Préparer le milieu des fragments individuels

Ajoutez chaque partagé (base de données) pour le Gestionnaire de table partagé. Il prépare les bases de données pour stocker les informations de mappage. Exécuter cette méthode sur chaque partagé.
     
    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.
 

## <a name="step-4-add-mappings"></a>Étape 4 : Ajouter des mappages

L’ajout de mappages varie selon le type de mappage partagé créé. Si vous avez créé une carte de la liste, vous ajoutez des mappages de liste. Si vous avez créé une carte de la plage, vous ajoutez les mappages de plage.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Option 1 : mappage des données pour un mappage de liste

Mapper les données en ajoutant un mappage de la liste pour chaque client.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Option 2 : mappage des données pour un mappage de plage

Ajoutez la plage des mappages pour toutes la plage d’id clients – associations de base de données :

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>Option de l’étape 4 3 : mappage des données pour différents utilisateurs sur une seule base de données

Pour chaque client, exécutez l’Add-ListMapping (d’option 1, ci-dessus). 


## <a name="checking-the-mappings"></a>Vérification des mappages

Informations sur les milieu des fragments existants et les mappages associés peuvent être interrogées à l’aide de commandes suivantes :  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Résumé

Une fois que vous avez terminé la configuration, vous pouvez commencer à utiliser la bibliothèque de client élastique de la base de données. Vous pouvez également utiliser le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) et [requête de multiples partagé](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Étapes suivantes


Obtenir les scripts PowerShell à partir de [sripts d’outils de base de données DB-élastique de SQL Azure](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Les outils se trouvent également sur GitHub : [Outils Azure/élastique-db](https://github.com/Azure/elastic-db-tools).

Utilisez l’outil de fusion et fractionnement pour déplacer des données vers ou à partir d’un modèle de plusieurs utilisateurs à un modèle de client unique. Consultez [l’outil de fusion de fractionnement](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations sur les modèles d’architecture de données commune d’applications de base de données mutualisées software as a service (SaaS), reportez-vous à la section [Modèles de Design pour les Applications SaaS mutualisée avec la base de données de SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Questions et demandes de fonctionnalités

Pour toute question, veuillez contacter nous sur le [forum de la base de données de SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) et de requêtes de fonctionnalités, veuillez les ajouter au [forum de commentaires de la base de données SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 
