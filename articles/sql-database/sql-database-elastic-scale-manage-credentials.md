<properties 
    pageTitle="Gestion des informations d’identification dans la bibliothèque de client de base de données élastique | Microsoft Azure" 
    description="Comment faire pour définir le niveau d’informations d’identification, l’administrateur en lecture seule, pour les applications de base de données élastique" 
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

# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Informations d’identification utilisées pour accéder à la bibliothèque de client de base de données élastique

La [bibliothèque de client de base de données élastique](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) utilise trois différents types d’informations d’identification pour accéder au [Gestionnaire de table partagé](sql-database-elastic-scale-shard-map-management.md). Selon les besoins, utilisez les informations d’identification avec le niveau le plus bas d’accès possible.

* **Informations d’identification de gestion**: pour la création ou de la manipulation d’un gestionnaire de table partagé. (Voir le [Glossaire](sql-database-elastic-scale-glossary.md)). 
* **Informations d’accès**: pour accéder à un gestionnaire de mappage partagé existant pour obtenir des informations sur le milieu des fragments.
* **Informations d’identification de la connexion**: se connecter au milieu des fragments. 

Voir aussi [Gestion des bases de données et les connexions de base de données de SQL Azure](sql-database-manage-logins.md). 
 
## <a name="about-management-credentials"></a>Informations d’identification d’administration

Informations d’identification d’administration sont utilisées pour créer un objet [**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) pour les applications qui manipulent des mappages d’éclater. (Par exemple, voir [Ajouter un partagé à l’aide des outils de base de données élastique](sql-database-elastic-scale-add-a-shard.md) et le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)) L’utilisateur de la bibliothèque de client évolutivité élastique crée les utilisateurs SQL et les connexions d’accès SQL et permet de s’assurer que chacun reçoit les autorisations en lecture/écriture sur la base de données de mappage global partagé et toutes les bases partagé ainsi. Ces informations sont utilisées pour mettre à jour le mappage global partagé et les cartes local partagé lorsque les modifications apportées à la table partagé sont effectuées. Par exemple, utiliser les informations d’identification d’administration pour créer l’objet Gestionnaire de mappage partagé (à l’aide de [**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx): 

    // Obtain a shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmAdminConnectionString, 
            ShardMapManagerLoadPolicy.Lazy 
    ); 

La variable **smmAdminConnectionString** est une chaîne de connexion qui contient les informations d’identification d’administration. L’ID utilisateur et le mot de passe permet d’accéder en lecture/écriture au milieu des fragments individuels et de base de données partagé. La chaîne de connexion de gestion inclut également le nom du serveur et le nom de base de données pour identifier la base de données de mappage global partagé. Voici une chaîne de connexion classique à cet effet :

     "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

N’utilisez pas les valeurs sous forme de "username@server"—instead utiliser juste la valeur « username ».  C’est parce que les informations d’identification doivent travailler par rapport à la base de données de gestionnaire de mappage partagé et le milieu des fragments individuelles, qui peuvent être sur différents serveurs.

## <a name="access-credentials"></a>Informations d’accès
  
Lorsque vous créez un partagé Gestionnaire de carte dans une application qui ne pas administrer éclater cartes, utilisez les informations d’identification qui ont des autorisations en lecture seule sur le plan global partagé. Les informations extraites de la table de global partagé sous ces informations d’identification sont utilisées pour le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) et remplir le cache de mappage partagé sur le client. Comme indiqué ci-dessus, les informations d’identification sont fournies via le même modèle d’appel à **GetSqlShardMapManager** : 

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Notez l’utilisation de la **smmReadOnlyConnectionString** pour refléter l’utilisation des différentes informations d’identification d’accès pour le compte des utilisateurs **non-administrateurs** : ces informations d’identification ne doivent pas fournir de droits d’écriture sur le plan global partagé. 

## <a name="connection-credentials"></a>Informations d’identification de connexion 

Les informations d’identification supplémentaires sont nécessaires lors de l’utilisation de la méthode [**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) pour accéder à un partagé associé à une clé ont. Ces informations d’identification doivent fournir les autorisations pour l’accès en lecture seule dans les tables de mappage local partagé résidant sur l’éclater. Cela est nécessaire pour effectuer une validation de connexion pour le routage dépendant des données sur l’éclater. Cet extrait de code permet l’accès aux données dans le contexte de routage dépendant des données : 
 
    using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
    targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

Dans cet exemple, **smmUserConnectionString** contient la chaîne de connexion pour les informations d’identification de l’utilisateur. Pour la base de données SQL Azure, voici une chaîne de connexion classique pour les informations d’identification de l’utilisateur : 

    "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Comme avec les informations d’identification de l’administrateur, ne pas valeurs sous la forme de "username@server". Utilisez simplement le « username ».  Notez également que la chaîne de connexion ne contient-elle pas un nom de serveur et de base de données. C’est parce que l’appel de **OpenConnectionForKey** sera automatiquement la connexion directe à l’éclater correct en fonction de la clé. Par conséquent, le nom de la base de données et le nom du serveur ne sont pas fournies. 

## <a name="see-also"></a>Voir aussi
[Gestion des bases de données et des connexions de base de données de SQL Azure](sql-database-manage-logins.md)

[Sécurisation de votre base de données SQL](sql-database-security.md)

[Mise en route avec les tâches de base de données élastique](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 