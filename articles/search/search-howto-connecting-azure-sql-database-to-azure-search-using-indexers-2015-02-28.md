<properties 
    pageTitle="Connexion de base de données SQL Azure pour Azure recherche à l’aide d’indexeurs | Microsoft Azure | Indexeurs" 
    description="Découvrez comment extraire des données à partir de la base de données de SQL Azure à un index de recherche d’Azure à l’aide d’indexeurs." 
    services="search" 
    documentationCenter="" 
    authors="chaosrealm" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="05/26/2016" 
    ms.author="eugenesh"/>

#<a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Connexion base de données SQL Azure à la recherche d’Azure à l’aide d’indexeurs

Service de recherche Azure est un service de recherche de nuage hébergé qui permet de fournir une expérience de recherche exceptionnelle. Vous pouvez rechercher, vous devez remplir un index de recherche d’Azure avec vos données. Si les données résident dans une base de données SQL d’Azure, le nouveau **indexeur Azure recherche pour base de données de SQL Azure** (ou **Azure SQL indexeur** en abrégé) dans la recherche d’Azure peut automatiser le processus d’indexation. Cela signifie que vous avez moins de code à écrire et une infrastructure plus se préoccuper.

Actuellement, les indexeurs ne fonctionnent qu’avec la base de données SQL Azure, SQL Server sur Azure VM et [Azure DocumentDB](../documentdb/documentdb-search-indexer.md). Dans cet article, nous allons nous concentrer sur les indexeurs qui fonctionnent avec une base de données de SQL Azure. Si vous souhaitez voir prise en charge des sources de données supplémentaires, veuillez fournir vos commentaires sur le [forum de commentaires recherche d’Azure](https://feedback.azure.com/forums/263029-azure-search/).

Cet article couvre les mécanismes de l’utilisation d’indexeurs, mais nous allons également approfondir fonctionnalités et comportements qui sont uniquement disponibles avec les bases de données SQL (par exemple, intégrée suivi des modifications).

## <a name="indexers-and-data-sources"></a>Indexeurs et sources de données

Pour installer et configurer un indexeur Azure SQL, vous pouvez appeler l' [API REST de recherche Azure](http://go.microsoft.com/fwlink/p/?LinkID=528173) pour créer et gérer des **sources de données**et des **indexeurs** . 

Vous pouvez également utiliser la [classe de l’indexeur](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) dans le [Kit de développement .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx), ou Assistant Importer des données dans le [portail Azure](https://portal.azure.com) pour créer et planifier un indexeur.

Une **source de données** spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données et les stratégies qui permettent la recherche Azure identifier correctement les modifications des données (nouveau, modifié ou supprimé des lignes). Il est défini en tant que ressources indépendantes afin qu’il peut être utilisé par plusieurs indexeurs.

Un **indexeur** est une ressource qui connecte des sources de données avec l’index de recherche de cible. Un indexeur est utilisé dans l’une des manières suivantes :
 
- Effectuer une copie ponctuelle de données pour remplir un index.
- Mettre à jour un index avec des modifications dans la source de données selon une planification.
- Exécuter à la demande à mettre à jour un index en fonction des besoins. 

## <a name="when-to-use-azure-sql-indexer"></a>Quand utiliser l’indexeur SQL Azure

En fonction de plusieurs facteurs relatifs à vos données, l’utilisation de l’indexeur d’Azure SQL peut ou peut ne pas convenir. Si vos données est adaptée à la configuration requise suivante, vous pouvez utiliser l’indexeur d’Azure SQL : 

- Toutes les données proviennent d’une seule table ou une vue
    - Si les données sont disséminées sur plusieurs tables, vous pouvez créer une vue et l’utilise avec l’indexeur. Toutefois, sachez que si vous utilisez une vue, vous ne pourrez utiliser la détection de modification SQL Server intégré. Consultez cette section pour plus de détails. 
- Les types de données utilisés dans la source de données sont pris en charge par l’indexeur. La plupart mais pas tous de la SQL types sont pris en charge. Pour plus d’informations, consultez [mappage des types de données de recherche d’Azure](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- Vous n’avez besoin près de mises à jour en temps réel à l’index lorsqu’une ligne est modifiée. 
    - L’indexeur permet de réindexer votre table toutes les 5 minutes au plus. Si vos données changent fréquemment et les modifications doivent être répercutées dans l’index de quelques secondes ou minutes unique, nous vous recommandons l’utilisation directe des [API d’Index Azure recherche](https://msdn.microsoft.com/library/azure/dn798930.aspx) . 
- Si vous avez un grand ensemble de données et que vous souhaitez exécuter l’indexeur selon une planification, votre schéma nous permet d’identifier efficacement modifiée (et supprimée, le cas échéant) lignes. Pour plus d’informations, consultez « Capture et supprimer lignes modifiées » ci-dessous. 
- La taille des champs indexés dans une ligne ne dépasse pas la taille maximale d’une recherche d’Azure indexation de demande, ce qui est de 16 Mo. 

## <a name="create-and-use-an-azure-sql-indexer"></a>Créer et utiliser un indexeur SQL Azure

Tout d’abord, créer la source de données : 

    POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


Vous pouvez obtenir la chaîne de connexion à partir du [Portail de Azure classique](https://portal.azure.com); Utilisez le `ADO.NET connection string` option.

Ensuite, créez l’index de recherche d’Azure cible si vous n’en avez pas déjà. Vous pouvez pour cela à partir du [portail de l’interface utilisateur](https://portal.azure.com) ou à l’aide de l' [API de Index créer](https://msdn.microsoft.com/library/azure/dn798941.aspx).  Assurez-vous que le schéma de votre index cible est compatible avec le schéma de la table source - voir la rubrique [mappage entre les types de données de recherche SQL et Azure](#TypeMapping) pour plus de détails.

Enfin, créez l’indexeur en lui donnant un nom et qui référencent la table de données source et cible :

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Une planification n’a pas d’indexeur créé de cette manière. Il s’exécute automatiquement une fois dès qu’elle est créée. Vous pouvez l’exécuter à tout moment à l’aide d’une demande **d’exécuter l’indexeur** :

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
    api-key: admin-key

Vous pouvez personnaliser plusieurs aspects du comportement d’indexeur, telles que la taille de lot et le nombre de documents peut être ignoré avant l’échec de l’exécution de l’indexeur. Pour plus d’informations, consultez [Créer un API indexeur](https://msdn.microsoft.com/library/azure/dn946899.aspx).
 
Vous devrez peut-être autoriser les services Azure pour vous connecter à votre base de données. Pour obtenir des instructions sur la procédure à suivre, reportez-vous à la section [Connexion à partir d’Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) .

Pour surveiller le statut de l’indexation et l’exécution de l’historique (nombre d’éléments indexés, échecs, etc.), utilisez une requête de **statut de l’indexation** : 

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
    api-key: admin-key

La réponse doit ressembler à ce qui suit : 

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null 
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null 
            },
            ... earlier history items 
        ]
    }

Historique d’exécution contient jusqu'à 50 des exécutions effectuées récemment, qui sont triées dans l’ordre chronologique inverse (de sorte que l’exécution du dernier vient en premier dans la réponse). Vous trouverez des informations supplémentaires sur la réponse dans le [Statut de l’indexation obtenir](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Exécuter des indexeurs sur une planification

Vous pouvez également réorganiser l’indexeur pour une exécution périodique d’une planification. Pour ce faire, il suffit d’ajouter la propriété de **planification** lors de la création ou la mise à jour de l’indexeur. L’exemple ci-dessous illustre une demande de mettre à jour l’indexeur :

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 

    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Le paramètre de **l’intervalle** est obligatoire. L’intervalle désigne le temps écoulé entre le début de deux des exécutions consécutives d’indexeur. L’intervalle minimum autorisé est de 5 minutes. la plus longue est d’un jour. Il doit être formaté comme une valeur de « dayTimeDuration » XSD (un sous-ensemble restreint d’une valeur de [durée de la norme ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle pour cela est : `P(nD)(T(nH)(nM))`. Exemples : `PT15M` pour toutes les 15 minutes, `PT2H` pour toutes les 2 heures.

L' option **startTime** indique lorsque les exécutions planifiées doivent commencer ; s’il est omis, l’heure UTC actuelle sera utilisé. Cette durée peut être dans le passé, dans lequel cas de la première exécution est prévue comme si l’indexeur est exécuté en continu depuis l’heure de début.  

Une seule exécution d’un indexeur donné peut s’exécuter à la fois. Si un indexeur est déjà en cours d’exécution lorsque suivant est censé pour démarrer, l’exécution est ignorée et reprend à l’intervalle suivant, en supposant que d’aucun autre travail d’indexeur est en cours d’exécution.

Prenons un exemple pour rendre cela plus concret. Supposons que nous la planification horaire suivante configurée : 

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Voici ce qui se passe : 

1. Démarrage de la première exécution de l’indexeur à ou autour du 1er mars 2015 12:00 a.m. HEURE UTC.
1. Supposons que cette exécution prend 20 minutes (ou à tout moment inférieure à 1 heure).
1. La seconde exécution démarre à ou autour du 1er mars 2015 1 h 00 du matin. 
1. Supposons maintenant que cette exécution prend plus d’une heure (cela nécessite une grande quantité de documents pour que cela se produise réellement, mais c’est une illustration utile) – par exemple, 70 minutes – afin qu’il se termine à environ 2 h 10 du matin.
1. Il est désormais de 2 h 00, heure de la troisième exécution Démarrer. Toutefois, étant donné que la deuxième exécution de 1 h est toujours en cours d’exécution, la troisième exécution est ignorée. La troisième exécution commence à 3 h 00.

Vous pouvez ajouter, modifier ou supprimer une planification pour un indexeur existant à l’aide d’une requête **PUT indexeur** . 

## <a name="capturing-new-changed-and-deleted-rows"></a>Capture de nouveau, modifier et à supprimer les lignes

Si vous utilisez une planification et votre table contient un nombre non négligeable de lignes, vous devez utiliser une stratégie de détection de modification de données, afin que l’indexeur peut récupérer uniquement les lignes nouvelles ou modifiées sans avoir à ré-indexer la table entière.

### <a name="sql-integrated-change-tracking-policy"></a>SQL intégré stratégie de suivi des modifications

Si votre base de données SQL prend en charge le [suivi des modifications](https://msdn.microsoft.com/library/bb933875.aspx), nous recommandons l’utilisation de **SQL intégré Modifier stratégie de suivi**. Cette stratégie active le suivi des modifications plus efficace, et il permet également de rechercher Azure identifier les lignes supprimées sans avoir à ajouter une colonne explicite « suppression temporaire » à votre table.

Suivi des modifications intégrée sont pris en charge dans les versions de base de données SQL Server suivantes :
 
- SQL Server 2008 R2 et versions ultérieures, si vous utilisez SQL Server sur Azure VM. 
- Azure SQL de base de données V12, si vous utilisez une base de données de SQL Azure.

Lorsqu’à l’aide de la stratégie, le suivi des modifications SQL intégré ne spécifiez pas une stratégie de détection de suppression de données - cette stratégie est prise en charge intégrée pour l’identification des lignes supprimées.

Cette stratégie peut uniquement être utilisée avec des tables ; Il ne peut pas être utilisé avec des vues. Vous devez activer le suivi des modifications pour la table que vous utilisez avant de pouvoir utiliser cette stratégie. Pour obtenir des instructions, reportez-vous à la section [activation et désactivation du suivi des modifications](https://msdn.microsoft.com/library/bb964713.aspx) . 

Pour utiliser cette stratégie, créer ou mettre à jour de la source de données comme suit :
 
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
      }
    }

### <a name="high-water-mark-change-detection-policy"></a>Stratégie de détection de modification de seuil supérieur

Bien que la stratégie de suivi des modifications SQL intégré est recommandée, il se peut que vous ne pourrez pas utiliser si vos données dans une vue, ou si vous utilisez une ancienne version de la base de données de SQL d’Azure. Dans ce cas, envisagez d’utiliser la stratégie seuil supérieur. Cette stratégie peut être utilisée si votre table contient une colonne qui répond aux critères suivants :

- Toutes les insertions permet de spécifier une valeur pour la colonne. 
- Les mises à jour pour un élément modifient également la valeur de la colonne. 
- La valeur de cette colonne augmente avec chaque modification.
- Des requêtes qui utilisent un `WHERE` clause semblable à `WHERE [High Water Mark Column] > [Current High Water Mark Value]` peuvent être efficacement exécutées.

Par exemple, une colonne indexée **rowversion** est un candidat idéal pour la colonne de seuil supérieur. Pour utiliser cette stratégie, créer ou mettre à jour de la source de données comme suit : 

    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
      }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Stratégie de détection de suppression de colonne Supprimer souple

Lorsque des lignes sont supprimées de la table source, vous souhaiterez probablement supprimer ces lignes à partir de l’index de recherche également. Si vous utilisez la stratégie de suivi des modifications SQL intégré, cela est prise en charge pour vous. Toutefois, la stratégie de suivi des modifications de seuil supérieur ne vous aident avec des lignes supprimées. Que faire ? 

Si les lignes sont physiquement supprimées de la table, vous avez pas de chance, il n’y a aucun moyen de déduire la présence d’enregistrements qui n’existent plus.  Toutefois, vous pouvez utiliser la technique du « soft-delete » pour marquer les lignes comme logiquement supprimé sans les supprimer de la table en ajoutant une colonne et en marquant les lignes comme étant supprimées à l’aide d’une valeur de marqueur de la colonne.

Lorsque vous utilisez la technique soft-supprimer, vous pouvez spécifier le soft Supprimer stratégie comme suit lors de la création ou la mise à jour de la source de données : 

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

Notez que le **softDeleteMarkerValue** doit être une chaîne, utilisez la représentation sous forme de chaîne de la valeur réelle. Par exemple, si vous avez une colonne d’entiers dans lequel les lignes supprimées sont marquées avec la valeur 1, utilisez `"1"`; Si vous avez une colonne BIT dans lequel les lignes supprimées sont marquées avec la valeur vrai booléen, utilisez `"True"`. 

<a name="TypeMapping"></a>
## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mappage entre les Types de données SQL et les types de données de recherche d’Azure

|Type de données SQL | Autorisé à index cible des types de champ |Notes 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| Real, float |Edm.Double, Edm.String | |
| smallmoney, numérique décimal de money | Edm.String| Recherche Azure ne gère pas la conversion des types decimal en Edm.Double, car cela peut perdre en précision |
| char, nchar, varchar, nvarchar | Edm.String<br/>Collection(EDM.String)|Transformation d’une colonne de type chaîne en Collection(Edm.String), vous devez utiliser un API version 2015-02-28-aperçu de l’aperçu. Consultez [cet article](search-api-indexers-2015-02-28-preview.md#CreateIndexer) pour plus de détails| 
|smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|emplacement géographique | Edm.GeographyPoint | Seules les instances de geography de type POINT avec 4326 SRID (qui est la valeur par défaut) sont pris en charge. | | 
|rowversion| N/A |Les colonnes de version de ligne ne peuvent pas être stockés dans l’index de recherche, mais ils peuvent être utilisés pour le suivi des modifications | |
| heure, timespan, binary, varbinary, image, xml, géométrie, les types CLR | N/A |Non pris en charge |


## <a name="frequently-asked-questions"></a>Forum aux questions

**Q:** Puis-je utiliser indexeur d’Azure SQL avec des bases de données SQL en cours d’exécution sur les ordinateurs virtuels de IaaS dans Azure ?

R : Oui. Toutefois, vous devez permettre à votre service de recherche pour vous connecter à votre base de données en effectuant le des deux choses suivantes. Consultez l’article [configurer une connexion à partir d’un indexeur de recherche d’Azure à SQL Server sur un ordinateur virtuel d’Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) pour plus d’informations.

1. Vous devrez peut-être configurer votre base de données avec un certificat approuvé afin que le service de recherche peut ouvrir des connexions SSL avec la base de données.
2. Configurer le pare-feu pour autoriser l’accès à l’adresse IP de votre service de recherche.

**Q:** Puis-je utiliser indexeur d’Azure SQL avec des bases de données SQL en cours d’exécution sur site ? 

R : nous ne vous recommandons ou prend en charge, car cela nécessiterait l’ouvrir vos bases de données au trafic Internet. 

**Q:** Puis-je utiliser indexeur d’Azure SQL avec des bases de données autres que SQL Server s’exécutant dans IaaS sur Azure ? 

R : nous ne prennent en charge ce scénario, car nous n’avons pas testé l’indexeur avec des bases de données autres que SQL Server.  

**Q:** Puis-je créer plusieurs indexeurs sont en cours d’exécution sur un planning ? 

R : Oui. Toutefois, seul un indexeur peut s’exécuter sur un nœud à la fois. Si vous avez besoin de plusieurs indexeurs qui s’exécutent simultanément, envisagez la mise à l’échelle votre service de recherche à plus d’une unité de recherche. 

**Q:** Exécute un indexeur affecte-t-il mon travail de requête ? 

R : Oui. Indexeur s’exécute sur l’un des nœuds de votre service de recherche, et les ressources de ce nœud sont partagés entre l’indexation et desservant le trafic des requêtes et autres demandes d’API. Si vous exécutez les charges de travail intensives d’indexation et de requête et que vous rencontrez un taux élevé d’erreurs ou d’augmentation des temps de réponse 503, envisagez la mise à l’échelle votre service de recherche.
