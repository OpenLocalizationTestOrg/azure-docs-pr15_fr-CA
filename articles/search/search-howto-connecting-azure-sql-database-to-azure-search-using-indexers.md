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
    ms.date="10/27/2016" 
    ms.author="eugenesh"/>

#<a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Connexion base de données SQL Azure à la recherche d’Azure à l’aide d’indexeurs

Service de recherche Azure est un service de recherche de nuage hébergé qui permet de fournir une expérience de recherche exceptionnelle. Vous pouvez rechercher, vous devez remplir un index de recherche d’Azure avec vos données. Si les données résident dans une base de données SQL d’Azure, **l’indexeur de recherche d’Azure pour la base de données de SQL Azure** nouveau (ou **Azure SQL indexeur** en abrégé) peut automatiser le processus d’indexation. Cela signifie que vous avez moins de code à écrire et une infrastructure plus se préoccuper.

Cet article décrit les mécanismes de l’utilisation d’indexeurs, mais il décrit également les fonctionnalités disponibles uniquement avec les bases de données SQL d’Azure (par exemple, intégrée suivi des modifications). Recherche Azure prend également en charge les autres sources de données, comme DocumentDB d’Azure, stockage blob et le stockage de la table. Si vous souhaitez voir prise en charge des sources de données supplémentaires, entrez vos commentaires sur le [forum de commentaires recherche d’Azure](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexeurs et sources de données

Vous pouvez définir et configurer un indexeur Azure SQL à l’aide : 

- Assistant Importation de données dans le [portail Azure](https://portal.azure.com)
- Recherche Azure [SDK .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx)
- Recherche Azure [API REST](http://go.microsoft.com/fwlink/p/?LinkID=528173)

Dans cet article, nous allons utiliser l’API REST pour vous montrer comment créer et gérer des **sources de données**et des **indexeurs** . 

Une **source de données** spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données et stratégies d’identifient correctement les modifications des données (lignes nouvelles, modifiées ou supprimées). Il est défini en tant que ressources indépendantes afin qu’il peut être utilisé par plusieurs indexeurs.

Un **indexeur** est une ressource qui connecte des sources de données avec l’index de recherche de cible. Un indexeur est utilisé dans l’une des manières suivantes :
 
- Effectuer une copie ponctuelle de données pour remplir un index.
- Mettre à jour un index avec des modifications dans la source de données selon une planification.
- Exécuter à la demande à mettre à jour un index en fonction des besoins. 

## <a name="when-to-use-azure-sql-indexer"></a>Quand utiliser l’indexeur SQL Azure

En fonction de plusieurs facteurs relatifs à vos données, l’utilisation de l’indexeur d’Azure SQL peut ou peut ne pas convenir. Si vos données est adaptée à la configuration requise suivante, vous pouvez utiliser l’indexeur d’Azure SQL : 

- Toutes les données proviennent d’une seule table ou une vue
    - Si les données sont disséminées sur plusieurs tables, vous pouvez créer une vue et l’utilise avec l’indexeur. Toutefois, si vous utilisez une vue, vous ne pourrez pas utiliser la détection de modification SQL Server intégré. Pour plus d’informations, consultez [Cette section](#CaptureChangedRows). 
- Les types de données utilisés dans la source de données sont pris en charge par l’indexeur. La plupart mais pas tous les types SQL sont pris en charge. Pour plus d’informations, consultez [mappage des types de données de recherche d’Azure](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
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

Ensuite, créez l’index de recherche d’Azure cible si vous n’en avez pas déjà. Vous pouvez créer un index à l’aide de l' [interface utilisateur de portail](https://portal.azure.com) ou l' [API de Index créer](https://msdn.microsoft.com/library/azure/dn798941.aspx). Assurez-vous que le schéma de votre index cible est compatible avec le schéma de la table source - voir la rubrique [mappage entre les types de données de recherche SQL et Azure](#TypeMapping).

Enfin, créez l’indexeur en lui donnant un nom et qui référencent la table de données source et cible :

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Une planification n’a pas d’indexeur créé de cette manière. Il s’exécute automatiquement une fois lorsqu’il est créé. Vous pouvez l’exécuter à tout moment à l’aide d’une demande **d’exécuter l’indexeur** :

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

Vous pouvez également réorganiser l’indexeur pour une exécution périodique d’une planification. Pour ce faire, ajoutez la propriété de **planification** lors de la création ou la mise à jour de l’indexeur. L’exemple ci-dessous illustre une demande de mettre à jour l’indexeur :

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 

    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Le paramètre de **l’intervalle** est obligatoire. L’intervalle désigne le temps écoulé entre le début de deux des exécutions consécutives d’indexeur. L’intervalle minimum autorisé est de 5 minutes. la plus longue est d’un jour. Il doit être formaté comme une valeur de « dayTimeDuration » XSD (un sous-ensemble restreint d’une valeur de [durée de la norme ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle pour cela est : `P(nD)(T(nH)(nM))`. Exemples : `PT15M` pour toutes les 15 minutes, `PT2H` pour toutes les 2 heures.

L' option **startTime** indique lorsque les exécutions planifiées doivent commencer. S’il est omis, l’heure UTC actuelle est utilisée. Cette durée peut être dans le passé, dans laquelle le cas de la première exécution est planifiée comme si l’indexeur est exécuté en continu depuis l’heure de début.  

Une seule exécution d’un indexeur peut s’exécuter à la fois. Si un indexeur est en cours d’exécution lors de la planification de son exécution, l’exécution est retardée jusqu'à ce que la prochaine heure planifiée.

Prenons un exemple pour rendre cela plus concret. Supposons que nous la planification horaire suivante configurée : 

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Voici ce qui se passe : 

1. Démarrage de la première exécution de l’indexeur à ou autour du 1er mars 2015 12:00 a.m. HEURE UTC.
1. Supposons que cette exécution prend 20 minutes (ou à tout moment inférieure à 1 heure).
1. La seconde exécution démarre à ou autour du 1er mars 2015 1 h 00 du matin. 
1. Supposons maintenant que cette exécution prend plus d’une heure – par exemple, 70 minutes – afin qu’il se termine à environ 2 h 10 du matin.
1. Il est désormais de 2 h 00, heure de la troisième exécution Démarrer. Toutefois, étant donné que la deuxième exécution de 1 h est toujours en cours d’exécution, la troisième exécution est ignorée. La troisième exécution commence à 3 h 00.

Vous pouvez ajouter, modifier ou supprimer une planification pour un indexeur existant à l’aide d’une requête **PUT indexeur** . 

<a name="CaptureChangedRows">, /a >
## <a name="capturing-new-changed-and-deleted-rows"></a>Capture de nouveau, modifié et les lignes supprimées

Si votre table comporte plusieurs lignes, vous devez utiliser une stratégie de détection de modification de données. Détection de modification permet une récupération efficace des uniquement sur les lignes nouvelles ou modifiées sans avoir à ré-indexer la table entière.

### <a name="sql-integrated-change-tracking-policy"></a>SQL intégré stratégie de suivi des modifications

Si votre base de données SQL prend en charge le [suivi des modifications](https://msdn.microsoft.com/library/bb933875.aspx), nous recommandons l’utilisation de **SQL intégré Modifier stratégie de suivi**. Il s’agit de la stratégie la plus efficace. En outre, il permet la recherche Azure identifier les lignes supprimées sans avoir à ajouter une colonne explicite « suppression temporaire » à votre table.

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

<a name="HighWaterMarkPolicy"></a>
### <a name="high-water-mark-change-detection-policy"></a>Stratégie de détection de modification de seuil supérieur

Bien que la stratégie de suivi des modifications SQL intégré est recommandée, il utilisable uniquement avec les tables, les vues pas. Si vous utilisez une vue, envisagez d’utiliser la stratégie seuil supérieur. Cette stratégie peut être utilisée si votre table ou la vue contient une colonne qui répond aux critères suivants :

- Toutes les insertions permet de spécifier une valeur pour la colonne. 
- Les mises à jour pour un élément modifient également la valeur de la colonne. 
- La valeur de cette colonne augmente avec chaque modification.
- Les requêtes suivantes où et les clauses ORDER BY peuvent être efficacement exécutées : `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`.

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

> [AZURE.WARNING] Si la table source ne dispose pas d’un index sur la colonne de seuil supérieur, les requêtes utilisées par l’indexeur SQL peuvent expirer. En particulier, les `ORDER BY [High Water Mark Column]` clause requiert un index afin de s’exécuter lorsque le tableau contient plusieurs lignes. 

Si vous rencontrez des erreurs de délai d’attente, vous pouvez utiliser la `queryTimeout` de configuration indexeur pour définir le délai d’expiration de la requête à une valeur plus élevée que le délai d’expiration de 5 minutes par défaut. Par exemple, pour définir le délai d’attente de 10 minutes, créer ou mettre à jour l’indexeur avec la configuration suivante : 

    {
      ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Vous pouvez également désactiver la `ORDER BY [High Water Mark Column]` clause. Toutefois, cela est déconseillé, car si l’exécution de l’indexeur est interrompue par une erreur, l’indexeur a nouveau traiter toutes les lignes si elle s’exécute plus tard, même si l’indexeur a déjà traité de presque toutes les lignes au moment où qu'elle a été interrompue. Pour désactiver le `ORDER BY` clause, utilisation du `disableOrderByHighWaterMarkColumn` dans la définition de l’indexeur :  

    {
     ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Stratégie de détection de suppression de colonne Supprimer souple

Lorsque des lignes sont supprimées de la table source, vous souhaiterez probablement supprimer ces lignes à partir de l’index de recherche également. Si vous utilisez la stratégie de suivi des modifications SQL intégré, cela est prise en charge pour vous. Toutefois, la stratégie de suivi des modifications de seuil supérieur ne vous aident avec des lignes supprimées. Que faire ? 

Si les lignes sont physiquement supprimées de la table, recherche d’Azure n’a aucun moyen de déduire la présence d’enregistrements qui n’existent plus.  Toutefois, vous pouvez utiliser la technique du « soft-supprimer » supprimer logiquement des lignes sans les supprimer de la table. Ajouter une colonne pour les lignes de la table ou de vue et de marquer comme supprimé à l’aide de cette colonne.

Lorsque vous utilisez la technique soft-supprimer, vous pouvez spécifier le soft Supprimer stratégie comme suit lors de la création ou la mise à jour de la source de données : 

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

Le **softDeleteMarkerValue** doit être une chaîne, utilisez la représentation sous forme de chaîne de la valeur réelle. Par exemple, si vous avez une colonne d’entiers dans lequel les lignes supprimées sont marquées avec la valeur 1, utilisez `"1"`. Si vous avez une colonne BIT dans lequel les lignes supprimées sont marquées avec la valeur vrai booléen, utilisez `"True"`. 

<a name="TypeMapping"></a>
## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mappage entre les Types de données SQL et les types de données de recherche d’Azure

|Type de données SQL | Autorisé à index cible des types de champ |Notes 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| Real, float |Edm.Double, Edm.String | |
| smallmoney, numérique décimal de money | Edm.String| Recherche Azure ne gère pas la conversion des types decimal en Edm.Double, car cela peut perdre en précision |
| char, nchar, varchar, nvarchar | Edm.String<br/>Collection(EDM.String)|Une chaîne SQL peut être utilisée pour remplir un champ Collection(Edm.String) si la chaîne représente un tableau JSON de chaînes :`["red", "white", "blue"]` | 
|smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|emplacement géographique | Edm.GeographyPoint | Seules les instances de geography de type POINT avec 4326 SRID (qui est la valeur par défaut) sont pris en charge. | | 
|rowversion| N/A |Les colonnes de version de ligne ne peuvent pas être stockés dans l’index de recherche, mais ils peuvent être utilisés pour le suivi des modifications | |
| heure, timespan, binary, varbinary, image, xml, géométrie, les types CLR | N/A |Non pris en charge |

## <a name="configuration-settings"></a>Paramètres de configuration

Indexeur SQL expose plusieurs paramètres de configuration : 

|Paramètre |Type de données | Objectif | Valeur par défaut |
|--------|----------|---------|---------------|
| queryTimeout | chaîne | Définit le délai d’attente pour l’exécution de requêtes SQL | 5 minutes ("00 : 05:00 ») |
| disableOrderByHighWaterMarkColumn | bool | Génère la requête SQL utilisée par la politique de seuil supérieur à omettre la clause ORDER BY. Consultez [la stratégie de seuil supérieur](#HighWaterMarkPolicy) | False | 

Ces paramètres sont utilisés dans les `parameters.configuration` l’objet dans la définition de l’indexeur. Par exemple, pour définir le délai d’expiration de requête à 10 minutes, créer ou mettre à jour l’indexeur avec la configuration suivante : 

    {
      ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="frequently-asked-questions"></a>Forum aux questions

**Q:** Puis-je utiliser indexeur d’Azure SQL avec des bases de données SQL en cours d’exécution sur les ordinateurs virtuels de IaaS dans Azure ?

R : Oui. Toutefois, vous devez permettre à votre service de recherche pour vous connecter à votre base de données. Pour plus d’informations, consultez [configurer une connexion à partir d’un indexeur de recherche d’Azure à SQL Server sur un ordinateur virtuel d’Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).


**Q:** Puis-je utiliser indexeur d’Azure SQL avec des bases de données SQL en cours d’exécution sur site ? 

R : nous ne vous recommandons ou prend en charge, car cela nécessiterait l’ouvrir vos bases de données au trafic Internet. 

**Q:** Puis-je utiliser indexeur d’Azure SQL avec des bases de données autres que SQL Server s’exécutant dans IaaS sur Azure ? 

R : nous ne prennent en charge ce scénario, car nous n’avons pas testé l’indexeur avec des bases de données autres que SQL Server.  

**Q:** Puis-je créer plusieurs indexeurs sont en cours d’exécution sur un planning ? 

R : Oui. Toutefois, seul un indexeur peut s’exécuter sur un nœud à la fois. Si vous avez besoin de plusieurs indexeurs qui s’exécutent simultanément, envisagez la mise à l’échelle votre service de recherche à plus d’une unité de recherche. 

**Q:** Exécute un indexeur affecte-t-il mon travail de requête ? 

R : Oui. Indexeur s’exécute sur l’un des nœuds de votre service de recherche, et les ressources de ce nœud sont partagés entre l’indexation et desservant le trafic des requêtes et autres demandes d’API. Si vous exécutez les charges de travail intensives d’indexation et de requête et que vous rencontrez un taux élevé d’erreurs ou d’augmentation des temps de réponse 503, envisagez la mise à l’échelle votre service de recherche.
