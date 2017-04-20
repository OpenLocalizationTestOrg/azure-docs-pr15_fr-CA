<properties
    pageTitle="Compteurs de performance pour éclater les Gestionnaire de cartes"
    description="ShardMapManager classe et données dépendantes routage des compteurs de performance"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="SilviaDoomra"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="SilviaDoomra"/>

# <a name="performance-counters-for-shard-map-manager"></a>Compteurs de performance pour éclater les Gestionnaire de cartes

Vous pouvez capturer les performances d’un [Gestionnaire de mappage partagé](sql-database-elastic-scale-shard-map-management.md), en particulier lorsque vous utilisez le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md). Les compteurs sont créés avec des méthodes de la classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Les compteurs sont utilisés pour suivre les performances des opérations de [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) . Ces compteurs sont accessibles dans l’Analyseur de performances sous la catégorie « Élastique gestion de base de données : partagé ».

**Pour la version la plus récente :** Accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Voir aussi [mise à niveau d’une application pour utiliser la bibliothèque de client élastique de la base de données plus récente](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Conditions préalables

* Pour créer la catégorie performances et les compteurs, l’utilisateur doit faire partie du groupe **administrateurs** local sur l’ordinateur qui héberge l’application.  

* Pour créer une instance de compteur de performance et les compteurs de mise à jour, l’utilisateur doit être membre du groupe **administrateurs** ou **Utilisateurs du moniteur de performances** . 

## <a name="create-performance-category-and-counters"></a>Créer des compteurs et catégories de performances 

Pour créer les compteurs, appelez la méthode CreatePeformanceCategoryAndCounters de la [classe de ShardMapManagmentFactory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Seul un administrateur peut exécuter la méthode : 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Vous pouvez également utiliser [ce](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script PowerShell pour exécuter la méthode. La méthode crée des compteurs de performance suivants :  

* **Mappages de mise en cache**: nombre de mappages mis en cache pour le mappage partagé.
*  **Opérations de DDR/s**: taux d’opérations routage dépendant des données pour le mappage partagé. Ce compteur est mis à jour lorsqu’un appel à [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) les résultats dans une connexion réussie à le partagé de destination. 
*  **Mappage de recherche présences dans le cache/s**: taux d’opérations de recherche de réussies sur le cache de mappage dans le mappage d’éclater. 
*  **Mappage de recherche absences dans le cache/s**: taux d’opérations de recherche de cache a échoué pour les mappages dans le mappage d’éclater.
*  **Mappages ajoutés ou mis à jour dans le cache/s**: taux lesquelles mappages sont ajoutées ou mises à jour de cache pour le mappage partagé. 
*  **Les mappages sont supprimés du cache/s**: taux auquel les mappages sont supprimées du cache pour le mappage partagé. 

Les compteurs de performance sont créés pour chaque plan de mise en cache partagé par processus.  


## <a name="notes"></a>Notes
Les événements suivants déclenchent la création des compteurs de performance :  

* Initialisation de la [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) avec le [chargement hâtif](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), si le ShardMapManager contient les mappages d’éclater. Citons notamment les [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) et les méthodes de [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) .
* Recherche réussie d’un mappage partagé (à l’aide de [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 

* Création réussie de mappage partagé à l’aide de CreateShardMap().

Les compteurs de performance va être mis à jour par toutes les opérations de cache effectuées sur le mappage d’éclater et les mappages. Suppression réussie de la carte de partagé à l’aide de DeleteShardMap () reults à la suppression de l’instance des compteurs de performance.  

## <a name="best-practices"></a>Meilleures pratiques

* Création de la catégorie de performance et les compteurs doit être effectuée qu’une seule fois avant la création de l’objet de ShardMapManager. Chaque exécution de la commande CreatePerformanceCategoryAndCounters() efface les compteurs précédents (perte de données déclarées par toutes les instances) et crée de nouvelles.  

* Les instances de compteur de performance sont créés par le processus. Toute panne d’application ou de la suppression d’un mappage de partagé à partir du cache entraînera la suppression des instances de compteurs de performances.  

### <a name="see-also"></a>Voir aussi

[Vue d’ensemble des fonctionnalités de base de données élastique](sql-database-elastic-scale-introduction.md)  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

