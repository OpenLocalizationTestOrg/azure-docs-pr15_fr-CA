<properties 
    pageTitle="Évolutivité élastique de SQL Azure FAQ | Microsoft Azure" 
    description="Questions fréquemment posées à propos de l’évolutivité élastique de base de données SQL Azure." 
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
    ms.date="05/03/2016" 
    ms.author="ddove"/>

# <a name="elastic-database-tools-faq"></a>Forum aux questions sur des outils de base de données élastique 

#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Si j’ai un mono-utilisateur par éclater et aucune clé ont, comment remplir la clé ont pour les informations de schéma ?

L’objet d’informations de schéma est uniquement utilisé pour séparer les scénarios de fusion. Si une application est par nature mono-utilisateur, il ne requiert pas l’outil de fusion de fractionnement, puis ainsi il n’est pas nécessaire de remplir l’objet d’informations de schéma.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>J’ai configuré une base de données et je possède déjà un gestionnaire de mappage partagé, comment enregistrer cette nouvelle base de données sous la forme d’un partagé ?

Consultez **[Ajout d’un partagé à une application à l’aide de la bibliothèque de client élastique de la base de données](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Des outils de base de données élastique coût ?

À l’aide de la bibliothèque de client élastique de la base de données n’entraîne pas de coûts. Allocation des coûts uniquement pour les bases de données Azure SQL que vous utilisez pour le milieu des fragments et le Gestionnaire de table partagé, ainsi que les rôles de web/travailleur que vous fournir de l’outil de fusion de fractionnement.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Pourquoi mes informations d’identification ne fonctionnent pas lorsque vous ajoutez un partagé à partir d’un autre serveur ?
N’utilisez pas d’informations d’identification sous la forme de « utilisateur ID=username@servername”, simplement utiliser à la place « ID utilisateur = nom d’utilisateur ».  Veillez également à ce que la connexion d’accès « username » dispose des autorisations sur l’éclater.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Je dois créer un gestionnaire de mappage partagé et remplir le milieu des fragments chaque démarrage de mes applications ?

Non, la création du Gestionnaire de table partagé (par exemple, **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) est une opération unique.  Votre application doit utiliser l’appel de **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** au démarrage de l’application.  Il ne convient qu’un tel appel par domaine d’application.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>J’ai des questions sur l’utilisation des outils de base de données élastique, comment obtenir les réponses ? 

Veuillez contacter nous sur le [forum de la base de données de SQL Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Lorsque j’obtenir une connexion de base de données à l’aide d’une clé ont, je peux toujours les données de requête pour les autres touches ont la même partagé.  Cela est-il normal ?

Les API d’échelle élastique vous donnent une connexion à la base de données correct pour votre clé ont, mais ne fournissent pas de filtrage ont des clés.  Ajouter des clauses **WHERE** de votre requête pour limiter l’étendue de la clé ont fourni, si nécessaire.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Puis-je utiliser une version différente de la base de données Azure pour chaque partagé dans mon jeu partagé ?

Oui, un partagé est une base de données individuel, et donc un partagé peut être une édition Premium, tandis que l’autre est une édition Standard. En outre, l’édition d’un partagé peut évoluer vers le haut ou vers le bas plusieurs fois pendant la durée de vie de l’éclater.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Est la fourniture d’outil de fusion de fractionnement (ou supprimer) une base de données pendant une opération de fractionnement ou de fusion ? 

N° Pour les opérations de **fractionnement** , la base de données cible doit exister avec le schéma approprié et être inscrit avec le Gestionnaire de table partagé.  Pour les opérations de **fusion** , vous devez supprimer le partagé à partir du Gestionnaire de table partagé et puis supprimer la base de données.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 