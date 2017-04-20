<properties
    pageTitle="Connexion DocumentDB avec recherche Azure à l’aide d’indexeurs | Microsoft Azure"
    description="Cet article vous montre comment utiliser d’indexeur Azure recherche avec DocumentDB comme source de données."
    services="documentdb"
    documentationCenter=""
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"/>

<tags
    ms.service="documentdb"
    ms.devlang="rest-api"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="07/08/2016"
    ms.author="denlee"/>

#<a name="connecting-documentdb-with-azure-search-using-indexers"></a>DocumentDB de connexion avec recherche Azure à l’aide d’indexeurs

Si vous avez besoin pour mettre en œuvre des expériences de recherche exceptionnelle sur vos données de DocumentDB, utiliser l’indexeur de recherche d’Azure pour DocumentDB ! Dans cet article, nous allons vous montrer d’intégrer Azure DocumentDB Azure recherche sans avoir à écrire du code pour gérer l’infrastructure d’indexation !

Pour ce faire, vous avez [un compte Azure recherche d’installation](../search/search-create-service-portal.md) (vous n’avez pas besoin de mise à niveau de recherche standard) et ensuite appelez l' [API REST de recherche Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour créer une **source de données** de DocumentDB et un **indexeur** pour cette source de données.

Dans les demandes d’ordre d’envoi pour interagir avec les API du reste, vous pouvez utiliser [Postman](https://www.getpostman.com/), [Fiddler](http://www.telerik.com/fiddler)ou n’importe quel outil de votre choix.


##<a id="Concepts"></a>Concepts d’indexeur recherche Azure

Azure Search prend en charge la création et la gestion des données sources (y compris les DocumentDB) et les indexeurs opérant par rapport à ces sources de données.

Une **source de données** spécifie les données qui doivent être indexé, les informations d’identification pour accéder aux données et les stratégies pour activer la recherche d’Azure identifier correctement les modifications des données (tel que modifié ou supprimé les documents à l’intérieur de votre collection). La source de données est définie en tant que ressources indépendantes de sorte qu’il peut être utilisé par plusieurs indexeurs.

Un **indexeur** décrit les flux des données à partir de votre source de données dans un index de recherche de cible. Vous devez planifier la création d’un indexeur pour chaque combinaison de source de données et d’index cible. Vous pouvez avoir plusieurs indexeurs écriture dans l’index même, un indexeur ne peut écrire que dans un seul index. Un indexeur est utilisé pour :

- Effectuer une copie ponctuelle de données pour remplir un index.
- Synchroniser un index avec des modifications dans la source de données selon une planification. La planification est la partie de la définition de l’indexeur.
- L’appel de mises à jour à la demande pour un index en fonction des besoins.

##<a id="CreateDataSource"></a>Étape 1 : Créer une source de données

Émettre une demande HTTP POST pour créer une nouvelle source de données dans votre service de recherche d’Azure, y compris les en-têtes de la demande suivante.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Le `api-version` est requis. Les valeurs valides sont `2015-02-28` ou une version ultérieure. Visitez les [versions API dans Azure recherche](../search/search-api-versions.md) pour voir toutes les API de recherche versions supportées.

Le corps de la requête contient la définition de source de données, qui doit inclure les champs suivants :

- **nom**: choisissez un nom pour représenter votre base de données DocumentDB.

- **type**: utilisation `documentdb`.

- **informations d’identification**:

    - **connectionString**: obligatoire. Spécifiez les informations de connexion à votre base de données Azure DocumentDB dans le format suivant :`AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **conteneur**:

    - **nom**: obligatoire. Spécifier l’id de l’indexation de la collection DocumentDB.

    - **requête**: facultatif. Vous pouvez spécifier une requête pour aplatir un document JSON arbitraire dans un schéma à plat qui recherche d’Azure peut indexer.

- **dataChangeDetectionPolicy**: facultatif. Consultez les [données modifier la stratégie de détection](#DataChangeDetectionPolicy) ci-dessous.

- **dataDeletionDetectionPolicy**: facultatif. Consultez la [stratégie de détection de suppression de données](#DataDeletionDetectionPolicy) ci-dessous.

Voir ci-dessous pour un [corps de demande d’exemple](#CreateDataSourceExample).

###<a id="DataChangeDetectionPolicy"></a>Capture de documents modifiés

L’objectif d’une stratégie de détection de modification de données est d’identifier correctement les éléments de données modifiées. Actuellement, la seule stratégie de prise en charge est la `High Water Mark` à l’aide de la stratégie du `_ts` propriété d’horodatage de dernière modification fournie par DocumentDB - qui est spécifiée comme suit :

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Vous devrez également ajouter `_ts` dans la projection et `WHERE` clause à votre requête. Par exemple :

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>Capture de documents supprimés

Lorsque des lignes sont supprimées de la table source, vous devez supprimer les lignes de l’index de recherche ainsi. L’objectif d’une stratégie de détection de suppression de données est d’identifier correctement les éléments de données supprimés. Actuellement, la seule stratégie de prise en charge est la `Soft Delete` stratégie (suppression est marquée avec un indicateur quelconque), qui est spécifiée comme suit :

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [AZURE.NOTE] Vous devez inclure la propriété softDeleteColumnName dans votre clause SELECT si vous utilisez une projection personnalisée.

###<a id="CreateDataSourceExample"></a>Exemple de corps de requête

L’exemple suivant crée une source de données avec un indicateurs de requête et stratégie personnalisée :

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###<a name="response"></a>Réponse

Vous recevrez une réponse HTTP créé de 201 si la source de données a été créée avec succès.

##<a id="CreateIndex"></a>Étape 2 : Création d’un index

Créer un index de recherche d’Azure cible si vous n’en avez pas déjà. Vous pouvez procéder de la sorte à partir de l' [Interface utilisateur de portail Azure](../search/search-create-index-portal.md) ou à l’aide de l' [API de Index créer](https://msdn.microsoft.com/library/azure/dn798941.aspx).

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


Vérifiez que le schéma de votre index cible est compatible avec le schéma des documents JSON source ou le résultat de votre projection de requête personnalisée.

>[AZURE.NOTE] Pour les collections partitionnées, la clé de document par défaut est de DocumentDB `_rid` propriété qui obtient renommée en `rid` recherche d’Azure. En outre, DocumentDB `_rid` valeurs contiennent des caractères qui ne sont pas valides dans les clés de la recherche d’Azure ; Par conséquent, les `_rid` les valeurs sont codées en Base64.

###<a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>Figure a : mappage entre les Types de données JSON et les Types de données de recherche Azure

| TYPE DE DONNÉES JSON|   TYPES DE CHAMP D’INDEX CIBLE COMPATIBLE|
|---|---|
|Bool|Edm.Boolean, Edm.String|
|Nombres qui ressemblent à des nombres entiers|Edm.Int32, Edm.Int64, Edm.String|
|Nombres similaires à des points flottants|Edm.Double, Edm.String|
|Chaîne|Edm.String|
|Types de tableaux de type primitif, par exemple « a », « b », « c » |Collection(EDM.String)|
|Chaînes qui ressemblent à des dates| Edm.DateTimeOffset, Edm.String|
|GeoJSON des objets, par exemple {« type » : « Point », « coordonnées » : [long, table d’adresses locales]} | Edm.GeographyPoint |
|Autres objets JSON|N/A|

###<a id="CreateIndexExample"></a>Exemple de corps de requête

L’exemple suivant crée un index avec un champ id et une description :

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###<a name="response"></a>Réponse

Vous recevrez une réponse HTTP créé de 201 si l’index a été créé avec succès.

##<a id="CreateIndexer"></a>Étape 3 : Créer un indexeur

Vous pouvez créer un nouvel indexeur dans un service de recherche d’Azure à l’aide d’une demande HTTP POST avec les en-têtes suivants.

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Le corps de la requête contient la définition de l’indexeur, qui doit inclure les champs suivants :

- **nom**: obligatoire. Le nom de l’indexeur.

- **dataSourceName**: obligatoire. Le nom d’une source de données existante.

- **targetIndexName**: obligatoire. Le nom d’un index existant.

- **planification**: facultatif. Reportez-vous à la section [planification d’indexation](#IndexingSchedule) ci-dessous.

###<a id="IndexingSchedule"></a>Indexeurs en cours d’exécution d’une planification

Un indexeur peut éventuellement spécifier une planification. Si une planification est présente, l’indexeur s’exécute périodiquement selon une planification. Planification possède les attributs suivants :

- **intervalle**: obligatoire. Une valeur de durée qui spécifie un intervalle ou une période d’indexation s’exécute. L’intervalle minimum autorisé est de 5 minutes. la plus longue est d’un jour. Il doit être formaté comme une valeur de « dayTimeDuration » XSD (un sous-ensemble restreint d’une valeur de [durée de la norme ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle pour cela est : `P(nD)(T(nH)(nM))`. Exemples : `PT15M` pour toutes les 15 minutes, `PT2H` pour toutes les 2 heures.

- **heure de début**: obligatoire. Un datetime UTC qui spécifie quand l’indexeur doit commencer en cours d’exécution.

###<a id="CreateIndexerExample"></a>Exemple de corps de requête

L’exemple suivant crée un indexeur qui copie les données à partir de la collection référencée par la `myDocDbDataSource` de source de données pour le `mySearchIndex` index selon un calendrier qui débute le 1er janvier 2015 UTC et exécute toutes les heures.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###<a name="response"></a>Réponse

Vous recevrez une réponse HTTP créé de 201 si l’indexeur a été créé avec succès.

##<a id="RunIndexer"></a>Étape 4 : Exécution d’un indexeur

En plus d’exécuter régulièrement sur une planification, un indexeur peut également être appelé à la demande à l’aide de la requête HTTP POST suivante :

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Réponse

Vous recevrez une réponse HTTP accepté de 202 si l’indexeur a été appelé avec succès.

##<a name="GetIndexerStatus"></a>Étape 5 : Obtenir le statut de l’indexation

Vous pouvez émettre une demande HTTP GET pour extraire l’historique actuel sur le statut et l’exécution d’un indexeur :

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Réponse

Vous verrez une réponse HTTP 200 OK renvoyée avec un corps de réponse qui contient des informations sur l’état de santé global indexeur, le dernier appel de l’indexeur, ainsi que l’historique des appels d’indexeur récente (le cas échéant).

La réponse doit ressembler à ce qui suit :

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Historique d’exécution contient jusqu'à les 50 exécutions terminées plus récentes, qui sont triées dans l’ordre chronologique inverse (de sorte que l’exécution du dernier vient en premier dans la réponse).

##<a name="NextSteps"></a>Étapes suivantes

Félicitations ! Vous venez d’apprendre comment intégrer Azure DocumentDB recherche Azure pour DocumentDB à l’aide de l’indexeur.

 - Pour savoir comment DocumentDB d’Azure, consultez la [page du service DocumentDB](https://azure.microsoft.com/services/documentdb/).

 - Pour savoir comment plus sur la recherche d’Azure, consultez la [page de service de recherche](https://azure.microsoft.com/services/search/).
