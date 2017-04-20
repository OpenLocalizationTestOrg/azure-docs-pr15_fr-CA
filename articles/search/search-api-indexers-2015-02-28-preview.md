<properties 
pageTitle="Les opérations d’indexeur (API REST de Service recherche Azure : 2015-02-28-aperçu de) | Aperçu de recherche Azure API" 
description="Les opérations d’indexeur (API REST de Service recherche Azure : 2015-02-28-aperçu)" 
services="search" 
documentationCenter="" 
authors="chaosrealm" 
manager="pablocas"
editor="" />

<tags 
ms.service="search" 
ms.devlang="rest-api" 
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na" 
ms.date="09/07/2016" 
ms.author="eugenesh" />

#<a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>Les opérations d’indexeur (API REST de Service recherche Azure : 2015-02-28-aperçu)#

> [AZURE.NOTE] Cet article décrit les indexeurs dans l' [API REST de 2015-02-28-aperçu](search-api-2015-02-28-preview.md). Cette version de l’API ajoute des versions de prévisualisation de l’indexeur de stockage des objets Blob Azure avec extraction des documents et indexeur de stockage par Table Azure, et autres améliorations. L’API prend également en charge les indexeurs (GA) généralement disponibles, incluant des indexeurs pour la base de données de SQL Azure, SQL Server sur Azure VM et DocumentDB d’Azure.

## <a name="overview"></a>Vue d’ensemble ##

Recherche Azure peut intégrer directement avec des sources de données courantes, évite d’avoir à écrire du code pour vos données d’index. Pour configurer cela, vous pouvez appeler l’API de recherche Azure pour créer et gérer des **sources de données**et des **indexeurs** . 

Un **indexeur** est une ressource qui connecte des sources de données avec l’index de recherche de cible. Un indexeur est utilisé dans l’une des manières suivantes : 

- Effectuer une copie ponctuelle de données pour remplir un index.
- Synchroniser un index avec des modifications dans la source de données selon une planification. La planification est la partie de la définition de l’indexeur.
- L’appel à la demande à mettre à jour un index en fonction des besoins. 

Un **indexeur** est utile lorsque vous souhaitez que les mises à jour régulières à un index. Vous pouvez définir un calendrier en ligne dans le cadre d’une définition d’indexeur ou exécuter sur demande à l’aide de [Exécuter un indexeur](#RunIndexer). 

Une **source de données** spécifie les données qui doivent être indexé, les informations d’identification pour accéder aux données et les stratégies pour activer la recherche d’Azure identifier correctement les modifications dans les données (tel que modifié ou supprimé des lignes d’une table de base de données). Il est défini en tant que ressources indépendantes afin qu’il peut être utilisé par plusieurs indexeurs.

Sources de données suivantes sont actuellement prises en charge :

- **Base de données SQL Azure** et **SQL Server sur Azure VM**. Pour une vue d’ensemble ciblé, consultez [cet article](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md). 
- **DocumentDB azure**. Pour une vue d’ensemble ciblé, consultez [cet article](../documentdb/documentdb-search-indexer.md). 
- **Stockage des objets Blob azure**, y compris les formats de document suivants : (y compris JSON) des fichiers Microsoft Office (DOCX/DOC, XSLX/XLS, PPTX/PPT, MSG), PDF, HTML, XML, ZIP et texte brut. Pour une vue d’ensemble ciblé, consultez [cet article](search-howto-indexing-azure-blob-storage.md).
- **Stockage par Table azure**. Pour une vue d’ensemble ciblé, consultez [cet article](search-howto-indexing-azure-tables.md).
     
Nous allons envisage d’ajouter la prise en charge pour les sources de données supplémentaires à l’avenir. Pour nous aider à hiérarchiser ces décisions, veuillez fournir vos commentaires sur le [forum de commentaires recherche d’Azure](http://feedback.azure.com/forums/263029-azure-search).

Voir [Les limites de Service](search-limits-quotas-capacity.md) pour les limites maximales liées aux ressources de source de données et indexeur.

## <a name="typical-usage-flow"></a>Flux d’utilisation classique ##

Vous pouvez créer et gérer des indexeurs et sources de données via des requêtes HTTP simples (POST, GET, PUT, DELETE) par rapport à une donnée `data source` ou `indexer` ressource.

Configuration de l’indexation automatique est généralement un processus en quatre étapes :

1. Identifier la source de données qui contient les données qui doivent être indexés. Gardez à l’esprit que recherche d’Azure ne peut pas prendre en charge tous les types de données présents dans votre source de données. Voir [types de données pris en charge](https://msdn.microsoft.com/library/azure/dn798938.aspx) pour la liste.

2. Créer un index de recherche d’Azure dont le schéma est compatible avec votre source de données.
  
3. Créez une source de données de recherche d’Azure comme décrit dans [Création de Source de données](#CreateDataSource).
  
4. Créer un indexeur de recherche d’Azure décrit [Créer un indexeur](#CreateIndexer).

Vous devez planifier la création d’un indexeur pour chaque combinaison de source de données et d’index cible. Vous pouvez avoir plusieurs indexeurs écriture dans l’index même, et vous pouvez réutiliser la même source de données pour plusieurs indexeurs. Toutefois, un indexeur peut uniquement utiliser une source de données à la fois et ne peut écrire que dans un seul index. 

Après avoir créé un indexeur, vous pouvez récupérer son état d’exécution à l’aide de l’opération [d’Obtenir l’état indexeur](#GetIndexerStatus) . Vous pouvez également exécuter un indexeur à tout moment (à la place ou en plus d’exécuter régulièrement sur une planification) à l’aide de l’opération à [Exécuter un indexeur](#RunIndexer) .

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>Créer la Source de données ##

Dans Rechercher d’Azure, une source de données est utilisée avec des indexeurs, en fournissant les informations de connexion pour l’actualisation des données ad hoc ou planifiés d’un index de la cible. Vous pouvez créer une nouvelle source de données dans un service de recherche d’Azure à l’aide d’une demande HTTP POST.
    
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Sinon, vous pouvez utiliser l’instruction PUT et spécifier le nom de source de données de l’URI. Si la source de données n’existe pas, il sera créé.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**Remarque**: le nombre maximal de sources de données autorisée varie selon le niveau de prix. Le service gratuit permet à des sources de données 3. Service standard permet 50 sources de données. Pour plus d’informations, reportez-vous à la section [Limites de Service](search-limits-quotas-capacity.md) .

**Demande**

HTTPS est requis pour toutes les demandes de service. La demande de **Créer une Source de données** peut être construite à l’aide de la méthode un POST ou PUT. Lorsque vous utilisez POST, vous devez fournir un nom de source de données dans le corps de la demande ainsi que la définition de source de données. Avec l’instruction PUT, le nom fait partie de l’URL. Si la source de données n’existe pas, il est créé. S’il existe déjà, il est mis à jour à la nouvelle définition. 

Le nom de source de données doit être en minuscule, commencer par une lettre ou un chiffre, ont sans barres obliques ou points et comporter moins de 128 caractères. Après avoir démarré le nom de source de données avec une lettre ou un chiffre, le reste du nom peut inclure toute lettre, nombre et des tirets, tant que les tirets ne sont pas consécutifs. Pour plus d’informations, consultez [les règles d’attribution de noms](https://msdn.microsoft.com/library/azure/dn857353.aspx) .

Le `api-version` est requis. La version actuelle est `2015-02-28`.

**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs. 

- `Content-Type`: Obligatoire. Cette valeur`application/json`
- `api-key`: Obligatoire. Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à votre service. La demande de **Créer une Source de données** doit comprendre un `api-key` en-tête défini à votre clé admin (et non d’une clé de la requête). 
 
Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le [Portail Azure](https://portal.azure.com/). Consultez l’aide de [créer un service de recherche dans le portail](search-create-service-portal.md) de navigation entre les pages.

<a name="CreateDataSourceRequestSyntax"></a>
**Syntaxe de corps de requête**

Le corps de la demande contient une définition de source de données, qui inclut le type de la source de données, les informations d’identification pour lire les données, ainsi que d’une données facultatives de détection de changement et les stratégies de détection de suppression de données qui sont utilisées pour identifier de manière efficace, modifié ou supprimé des données de la source de données lorsqu’elle est utilisée avec un indexeur régulièrement planifié. 

La syntaxe de structuration de la charge utile de demande est comme suit. Une demande d’exemple est fournie plus loin dans cette rubrique.

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

Demande contient les propriétés suivantes : 

- `name`: Obligatoire. Le nom de la source de données. Un nom de source de données doit uniquement contenir des lettres minuscules, des chiffres ou des tirets, ne peut pas commencer ou finir par tirets et est limité à 128 caractères.
- `description`: Une description facultative. 
- `type`: Obligatoire. Doit être un des types de source de données pris en charge :
    - `azuresql`-De la base de données SQL azure ou SQL Server sur Azure VM
    - `documentdb`-DocumentDB azure
    - `azureblob`-Stockage des objets Blob azure
    - `azuretable`-Stockage de Table azure
- `credentials`:
    - Requis pour le `connectionString` propriété spécifie la chaîne de connexion pour la source de données. Le format de la chaîne de connexion dépend du type de source de données : 
        - Pour les SQL Azure, il s’agit de la chaîne de connexion SQL Server habituelle. Si vous utilisez le portail Azure pour récupérer la chaîne de connexion, le `ADO.NET connection string` option.
        - Pour DocumentDB, la chaîne de connexion doit être au format suivant : `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Toutes les valeurs sont nécessaires. Vous les trouverez dans le [portail Azure](https://portal.azure.com/).  
        - Pour les Blob Azure et le stockage de la Table, il s’agit de la chaîne de connexion de compte de stockage. Le format est décrit [ici](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). Protocole de point de terminaison HTTPS est requis.  
- `container`, obligatoire : Spécifie les données d’index à l’aide de la `name` et `query` propriétés : 
    - `name`Obligatoire :
        - SQL Azure : Spécifie la table ou la vue. Vous pouvez utiliser des noms qualifiés par schéma, tel que `[dbo].[mytable]`.
        - DocumentDB : Spécifie la collection. 
        - Stockage de Blob Azure : Spécifie le conteneur de stockage.
        - Stockage par Table Azure : Spécifie le nom de la table. 
    - `query`, facultatif :
        - DocumentDB : vous permet de spécifier une requête qui aplatit une mise en page de document JSON arbitraire dans un schéma à plat qui recherche d’Azure peut indexer.  
        - Stockage de Blob Azure : vous permet de spécifier un dossier virtuel dans le conteneur de l’objet blob. Par exemple, pour un chemin d’accès de l’objet blob `mycontainer/documents/blob.pdf`, `documents` peut être utilisé en tant que le dossier virtuel.
        - Stockage par Table Azure : vous permet de spécifier une requête qui permet de filtrer l’ensemble de lignes à importer.
        - SQL Azure : requête n’est pas pris en charge. Si vous avez besoin de cette fonctionnalité, veuillez votez pour [cette suggestion](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
- L’option `dataChangeDetectionPolicy` et `dataDeletionDetectionPolicy` propriétés sont décrits ci-dessous.

<a name="DataChangeDetectionPolicies"></a>
**Stratégies de détection de modification de données**

L’objectif d’une stratégie de détection de modification de données est d’identifier correctement les éléments de données modifiées. Stratégies prises en charge varient en fonction du type de source de données. Les sections ci-dessous décrivent chaque stratégie. 

***Stratégie de détection de changement de limite supérieure*** 

Utilisez cette stratégie si votre source de données contient une colonne ou une propriété qui répond aux critères suivants :
 
- Toutes les insertions permet de spécifier une valeur pour la colonne. 
- Les mises à jour pour un élément modifient également la valeur de la colonne. 
- La valeur de cette colonne augmente avec chaque modification.
- Les requêtes qui utilisent une clause de filtre, similaire à la suivante `WHERE [High Water Mark Column] > [Current High Water Mark Value]` peuvent être efficacement exécutées.

Par exemple, lors de l’utilisation de sources de données SQL d’Azure, un `rowversion` colonne est le candidat idéal pour une utilisation avec la stratégie seuil supérieur. 

Cette stratégie peut être spécifiée comme suit :

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

> [AZURE.NOTE] Lorsque vous utilisez DocumentDB des sources de données, vous devez utiliser le `_ts` propriété fournie par DocumentDB. 

> [AZURE.NOTE] Lors de l’utilisation de sources de données Azure Blob Azure recherche automatiquement utilise une limite Modifier stratégie de détection en fonction de l’horodatage de dernière modification d’un objet blob ; vous n’avez pas besoin de spécifier d’une telle stratégie vous-même.   

***Stratégie de détection de modification intégrée à SQL***

Si votre base de données SQL prend en charge le [suivi des modifications](https://msdn.microsoft.com/library/bb933875.aspx), nous recommandons l’utilisation de SQL intégré Modifier stratégie de suivi. Cette stratégie permet le suivi des modifications plus efficace et permet d’identifier les lignes supprimées sans avoir à avoir une colonne explicite « suppression temporaire » dans votre schéma de recherche Azure.

Suivi des modifications intégrée sont pris en charge dans les versions de base de données SQL Server suivantes : 
- SQL Server 2008 R2, si vous utilisez SQL Server sur Azure VM.
- Azure SQL de base de données V12, si vous utilisez une base de données de SQL Azure.  

Lorsqu’à l’aide de la stratégie de suivi des modifications SQL intégré, ne spécifiez pas une stratégie de détection de suppression de données - cette stratégie est prise en charge intégrée pour l’identification des lignes supprimées. 

Cette stratégie peut uniquement être utilisée avec des tables ; Il ne peut pas être utilisé avec des vues. Vous devez activer le suivi des modifications pour la table que vous utilisez avant de pouvoir utiliser cette stratégie. Pour obtenir des instructions, reportez-vous à la section [activation et désactivation du suivi des modifications](https://msdn.microsoft.com/library/bb964713.aspx) .    
 
Lorsque la structuration de la demande de **Créer une Source de données** , la stratégie de suivi de modification SQL intégré peut être spécifié comme suit :

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**Stratégies de détection de suppression de données**

L’objectif d’une stratégie de détection de suppression de données est d’identifier correctement les éléments de données supprimés. Actuellement, la seule stratégie de prise en charge est la `Soft Delete` stratégie, qui permet l’identification des éléments supprimés, en fonction de la valeur d’une `soft delete` colonne ou propriété dans la source de données. Cette stratégie peut être spécifiée comme suit :

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

**Remarque :** Seules les colonnes avec les valeurs booléennes, entier ou chaîne sont pris en charge. La valeur utilisée comme `softDeleteMarkerValue` doit être une chaîne, même si la colonne correspondante contienne des entiers ou des valeurs booléennes. Par exemple, si la valeur qui apparaît dans votre source de données est 1, utilisez `"1"` comme le `softDeleteMarkerValue`.    

<a name="CreateDataSourceRequestExamples"></a>
**Exemples de corps de requête**

Si vous avez l’intention d’utiliser la source de données avec un indexeur qui s’exécute sur une planification, cet exemple montre comment spécifier des stratégies de détection de modification et de suppression : 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

Si vous souhaitez uniquement utiliser la source de données pour une copie unique des données, les stratégies peuvent être omis :

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Réponse**

Pour une demande réussie : 201 créé. 

<a name="UpdateDataSource"></a>
## <a name="update-data-source"></a>Mettre à jour la Source de données ##

Vous pouvez mettre à jour une source de données existante à l’aide d’une requête HTTP PUT. Vous spécifiez le nom de la source de données pour mettre à jour sur l’URI de requête :

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Le `api-version` est requis. La version actuelle est `2015-02-28`. [Versioning de recherche d’Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) a des détails et plus d’informations sur les autres versions.

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

**Demande**

La syntaxe de corps de demande est la même que pour [les demandes de créer une Source de données](#CreateDataSourceRequestSyntax).

> [AZURE.NOTE] Certaines propriétés ne peuvent pas être mis à jour sur une source de données existante. Par exemple, vous ne pouvez pas modifier le type de source de données existante.  

> [AZURE.NOTE] Si vous ne souhaitez pas modifier la chaîne de connexion pour une source de données existante, vous pouvez spécifier le littéral `<unchanged>` pour la chaîne de connexion. Cette fonction est utile dans les situations où vous devez mettre à jour les données de la source mais n’avez pas facilite l’accès à la chaîne de connexion car il s’agit des données de sécurité sensibles.

**Réponse**

Pour une demande réussie : 201 créé si une source de données a été créée et 204 sans contenu si une source de données existante a été mis à jour.

<a name="ListDataSource"></a>
## <a name="list-data-sources"></a>Liste des Sources de données ##

L’opération de la **Liste des Sources de données** renvoie une liste des sources de données dans votre service de recherche d’Azure. 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

Le `api-version` est requis. La version actuelle est `2015-02-28`. 

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

**Réponse**

Pour une demande réussie : 200 OK.

Voici un corps de réponse d’exemple :

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

Notez que vous pouvez filtrer la réponse à uniquement les propriétés qui que vous intéressent. Par exemple, si vous souhaitez uniquement une liste des noms de source de données, utilisez le OData `$select` option de requête :

    GET /datasources?api-version=205-02-28&$select=name

Dans ce cas, la réponse à partir de l’exemple ci-dessus apparaît comme suit : 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Il s’agit d’une technique utile pour économiser la bande passante si vous disposez d’un grand nombre de sources de données dans votre service de recherche.

<a name="GetDataSource"></a>
## <a name="get-data-source"></a>Obtenir la Source de données ##

L’opération **Obtenir la Source de données** Obtient la définition de source de données de recherche d’Azure.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

Le `api-version` est requis. La version actuelle est `2015-02-28`. 

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 200 OK est retourné pour une réponse réussie.

La réponse est similaire aux exemples de [requêtes d’exemple montre comment créer une Source de données](#CreateDataSourceRequestExamples): 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [AZURE.NOTE] Ne définissez pas le `Accept` en-tête de demande pour `application/json;odata.metadata=none` lorsque l’appel de cette API comme cela provoque `@odata.type` attribut pouvant être omis dans la réponse et que vous ne pourra pas faire la différence entre la modification des données et des règles de détection de suppression de données de types différents. 

<a name="DeleteDataSource"></a>
## <a name="delete-data-source"></a>Supprimer la Source de données ##

L’opération de **Source de données de supprimer** supprime une source de données à partir de votre service de recherche d’Azure.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Si les indexeurs font référence à la source de données que vous souhaitez supprimer, l’opération de suppression continue. Toutefois, les indexeurs ne passera dans un état d’erreur lors de leur prochaine exécution.  

Le `api-version` est requis. La version actuelle est `2015-02-28`. 

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 204 sans contenu est retourné pour une réponse réussie.

<a name="CreateIndexer"></a>
## <a name="create-indexer"></a>Créez l’indexeur ##

Vous pouvez créer un nouvel indexeur dans un service de recherche d’Azure à l’aide d’une demande HTTP POST.
    
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Sinon, vous pouvez utiliser l’instruction PUT et spécifier le nom de source de données de l’URI. Si la source de données n’existe pas, il sera créé.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [AZURE.NOTE] Le nombre maximal d’indexeurs autorisée varie en fonction du niveau de prix. Le service gratuit permet jusqu'à 3 indexeurs. Service standard permet 50 indexeurs. Pour plus d’informations, reportez-vous à la section [Limites de Service](search-limits-quotas-capacity.md) .

Le `api-version` est requis. La version actuelle est `2015-02-28`. 

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.


<a name="CreateIndexerRequestSyntax"></a>
**Syntaxe de corps de requête**

Le corps de la requête contient une définition de l’indexeur, qui spécifie la source de données et d’index cible pour l’indexation, ainsi que de planification d’indexation facultative et de paramètres. 


La syntaxe de structuration de la charge utile de demande est comme suit. Une demande d’exemple est fournie plus loin dans cette rubrique.

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**Planification de l’indexeur**

Un indexeur peut éventuellement spécifier une planification. Si une planification est présente, l’indexeur s’exécute périodiquement selon une planification. Planification possède les attributs suivants :

- `interval`: Obligatoire. Une valeur de durée qui spécifie un intervalle ou une période d’indexation s’exécute. L’intervalle minimum autorisé est de 5 minutes. la plus longue est d’un jour. Il doit être formaté comme une valeur de « dayTimeDuration » XSD (un sous-ensemble restreint d’une valeur de [durée de la norme ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle pour cela est : `"P[nD][T[nH][nM]]"`. Exemples : `PT15M` pour toutes les 15 minutes, `PT2H` pour toutes les 2 heures. 

- `startTime`: Obligatoire. Un datetime UTC lorsque l’indexeur doit démarrer en cours d’exécution. 

**Paramètres d’indexeur**

Un indexeur permet d’indiquer plusieurs paramètres qui affectent son comportement. Tous les paramètres sont facultatifs.  

- `maxFailedItems`: Le nombre d’éléments qui peut ne pas être indexés avant toute une série d’indexeur est considéré comme un échec. Valeur par défaut est 0. Informations sur les articles défectueux sont retournées par l’opération [d’Obtenir l’état indexeur](#GetIndexerStatus) . 

- `maxFailedItemsPerBatch`: Le nombre d’éléments qui peut ne pas être indexés dans chaque lot avant une exécution d’indexeur est considéré comme un échec. Valeur par défaut est 0.

- `base64EncodeKeys`: Indique si les clés de document doivent être codé en base 64. Recherche Azure impose des restrictions sur les caractères qui peuvent être présents dans une clé de document. Toutefois, les valeurs de vos données sources peuvent contenir des caractères non valides. S’il est nécessaire de ces valeurs en tant que clés de document d’index, cet indicateur peut être défini sur true. Valeur par défaut est `false`.

- `batchSize`: Spécifie le nombre d’éléments qui sont lus dans la source de données et indexés sous la forme d’un seul lot pour améliorer les performances. La valeur par défaut dépend du type de source de données : il est 1000 pour Azure SQL et DocumentDB et 10 pour le stockage des objets Blob Azure.

**Mappages de champs**

Vous pouvez utiliser les mappages de champs pour mapper un nom de champ dans la source de données à un autre nom de champ dans l’index de la cible. Par exemple, considérez une table avec un champ de la source `_id`. Recherche Azure n’autorise pas un nom de champ commençant par un trait de soulignement, afin que le champ doit être renommé. Cela peut être effectué à l’aide de la `fieldMappings` propriété de l’indexeur comme suit : 
    
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

Vous pouvez spécifier plusieurs mappages de champs : 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

Les noms de champs sources et cibles respectent la casse.

<a name="FieldMappingFunctions"></a>
***Fonctions de mappage de champ***

Mappages de champs peuvent également être utilisées pour transformer à l’aide de *fonctions de mappage*de champs source.

Qu’une telle fonction est pris en charge actuellement : `jsonArrayToStringCollection`. Il analyse un champ qui contient une chaîne mise en forme sous la forme d’un tableau JSON en un champ Collection(Edm.String) dans l’index de la cible. Elle est destinée aux indexeur d’Azure SQL en particulier, dans la mesure où SQL n’a pas un type de données natif de collection. Elle peut être utilisée comme suit : 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Par exemple, si le champ source contient la chaîne de `["red", "white", "blue"]`, puis le champ cible de type `Collection(Edm.String)` sera rempli avec les valeurs de trois `"red"`, `"white"` et `"blue"`.

Notez que la `targetFieldName` propriété est facultative ; Si omis, la `sourceFieldName` valeur est utilisée. 

<a name="CreateIndexerRequestExamples"></a>
**Exemples de corps de requête**

L’exemple suivant crée un indexeur qui copie les données de la table référencée par la `ordersds` de source de données pour le `orders` index selon un calendrier qui débute le 1er janvier 2015 UTC et exécute toutes les heures. Chaque appel d’indexeur ne sera réussie si pas plus de 5 articles échouent à indexer dans chaque lot, et pas plus de 10 articles échouent à indexer dans le total. 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**Réponse**

201 créé pour une demande réussie.


<a name="UpdateIndexer"></a>
## <a name="update-indexer"></a>Indexeur de mise à jour ##

Vous pouvez mettre à jour un indexeur existant à l’aide d’une requête HTTP PUT. Vous spécifiez le nom de l’indexeur de la mise à jour sur l’URI de requête :

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Le `api-version` est requis. La version actuelle est `2015-02-28`. 

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

**Demande**

La syntaxe de corps de demande est la même que pour les [demandes de créer un indexeur](#CreateIndexerRequestSyntax).

**Réponse**

Pour une demande réussie : 201 créé si un nouvel indexeur a été créée et 204 No Content si un indexeur existant a été mis à jour.


<a name="ListIndexers"></a>
## <a name="list-indexers"></a>Liste des indexeurs ##

L’opération de **Liste indexeurs** renvoie la liste des indexeurs dans votre service de recherche d’Azure. 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


Le `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. [Versioning de recherche d’Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) a des détails et plus d’informations sur les autres versions.

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

**Réponse**

Pour une demande réussie : 200 OK.

Voici un corps de réponse d’exemple :

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

Notez que vous pouvez filtrer la réponse à uniquement les propriétés qui que vous intéressent. Par exemple, si vous ne souhaitez qu’une liste de noms de l’indexeur, utilisez OData `$select` option de requête :

    GET /indexers?api-version=2014-10-20-Preview&$select=name

Dans ce cas, la réponse à partir de l’exemple ci-dessus apparaît comme suit : 

    {
      "value" : [ { "name": "myindexer" } ]
    }

Il s’agit d’une technique utile pour économiser la bande passante si vous disposez d’un grand nombre d’indexeurs dans votre service de recherche.


<a name="GetIndexer"></a>
## <a name="get-indexer"></a>Obtenir l’indexeur ##

L’opération **Obtenir l’indexeur** Obtient la définition de l’indexeur de recherche d’Azure.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Le `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. 

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 200 OK est retourné pour une réponse réussie.

La réponse est similaire aux exemples de [demandes d’exemple montre comment créer un indexeur](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>
## <a name="delete-indexer"></a>Supprimer l’indexeur ##

L’opération **Supprimer l’indexeur** supprime un indexeur de votre service de recherche d’Azure.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Lors de la suppression d’un indexeur, les exécutions d’indexeur en cours à ce moment seront exécute, mais aucun autres exécutions ne seront planifiées. Tente d’utiliser un indexeur inexistant entraînera le code d’état HTTP 404 introuvable. 
 
Le `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. 

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 204 sans contenu est retourné pour une réponse réussie.

<a name="RunIndexer"></a>
## <a name="run-indexer"></a>Exécuter l’indexeur ##

En plus d’exécuter régulièrement sur une planification, l’indexeur peut également être appelé à la demande via l’opération à **Exécuter un indexeur** : 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

Le `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. 

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 202 accepté est retournée pour une réponse réussie.

<a name="GetIndexerStatus"></a>
## <a name="get-indexer-status"></a>Obtenir le statut de l’indexation ##

L’opération **Obtenir l’état indexeur** récupère l’historique actuel sur le statut et l’exécution d’un indexeur : 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


Le `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. 

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : les 200 OK pour une réponse réussie.

Le corps de réponse contient des informations sur l’état de santé global indexeur, le dernier appel de l’indexeur, ainsi que l’historique des appels d’indexeur récente (le cas échéant). 

Un corps de réponse exemple ressemble à ceci : 

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

**Statut de l’indexation**

Statut de l’indexation peut être une des valeurs suivantes :

- `running`Indique que l’indexeur s’exécute normalement. Notez que certaines exécutions de l’indexeur peuvent être échoue de nouveau, il est judicieux de vérifier les `lastResult` propriété ainsi. 

- `error`Indique que l’indexeur a rencontré une erreur qui ne peuvent pas être corrigée sans intervention humaine. Par exemple, les informations d’identification de source de données a peut-être expiré, ou que le schéma de la source de données ou d’index cible a changé dans une minute moyen. 

**Résultat de l’exécution d’indexeur**

Résultat de l’exécution un indexeur contient des informations sur l’exécution d’un indexeur unique. Le dernier résultat est surfacé comme le `lastResult` propriété de l’état de l’indexeur. Autres résultats récents, le cas échéant, sont retournés comme le `executionHistory` propriété de l’état de l’indexeur. 

Résultat de l’exécution d’indexeur contient les propriétés suivantes : 

- `status`: l’état de l’exécution. Pour plus d’informations, consultez le [Statut de l’exécution de l’indexation](#IndexerExecutionStatus) ci-dessous. 

- `errorMessage`: message d’erreur pour un échec de l’exécution. 

- `startTime`: l’heure UTC lorsque cette exécution est démarrée.

- `endTime`: l’heure UTC lorsque cette exécution s’est terminée. Cette valeur n’est pas définie si l’exécution est toujours en cours.

- `errors`: un tableau d’erreurs de niveau d’élément, le cas échéant. Chaque entrée contient une clé de document (`key` propriété) et un message d’erreur (`errorMessage` propriété). 

- `itemsProcessed`: le nombre de données d’éléments (par exemple, les lignes de table) et que l’indexeur a tenté d’index au cours de l’exécution de cette source. 

- `itemsFailed`: le nombre d’éléments ayant échoué au cours de cette exécution.  
 
- `initialTrackingState`: toujours `null` pour la première exécution de l’indexeur, ou si les données changent de stratégie de suivi n’est pas activé sur la source de données utilisée. Si une telle stratégie est activée, cette valeur indique la première modification (minimum) valeur traitée par cette exécution de suivi dans les exécutions suivantes. 

- `finalTrackingState`: toujours `null` si les données changent de stratégie de suivi n’est pas activé sur la source de données utilisée. Dans le cas contraire, indique la dernière modification (maximum) suivi de valeur traitée avec succès par cette exécution. 

<a name="IndexerExecutionStatus"></a>
**Statut d’exécution de l’indexation**

L’état d’exécution indexeur capture l’état d’une exécution unique d’indexeur. Il peut avoir les valeurs suivantes :

- `success`Indique que l’exécution de l’indexeur est terminée.

- `inProgress`Indique que l’exécution de l’indexeur est en cours. 

- `transientFailure`Indique que l’exécution de l’indexeur a échoué. Voir `errorMessage` pour plus d’informations. L’échec peut ou peut ne pas exiger l’intervention humaine à résoudre : par exemple, fixation d’une incompatibilité de schéma entre la source de données et d’index cible requiert une action de l’utilisateur, n’est pas le cas d’une interruption de source de données temporaires. Appels d’indexeur continue par la planification, si celui-ci est présent. 

- `persistentFailure`Indique que l’indexeur a échoué d’une manière qui nécessite une intervention humaine. Arrêt d’exécutions planifiées d’indexeur. Après le traitement du problème, permet de réinitialiser les API indexeur pour redémarrer les exécutions planifiées. 

- `reset`Indique que l’indexeur a été réinitialisée par un appel à une API d’indexeur réinitialiser (voir ci-dessous). 

<a name="ResetIndexer"></a>
## <a name="reset-indexer"></a>Indexeur de réinitialisation ##

L’opération de **l’Indexeur de réinitialiser** rétablit l’état associé à l’indexeur de suivi des modifications. Cela vous permet de déclencher la réindexation à partir de zéro (par exemple, si votre schéma de source de données a été modifié), ou pour modifier la stratégie de détection de modification de données pour une source de données associée à l’indexeur.   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

Le `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. 

Le `api-key` doit être une clé admin (et non d’une clé de la requête). Reportez-vous à la section authentification dans l' [API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 204 sans contenu pour une réponse réussie.

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mappage entre les Types de données SQL et les Types de données de recherche Azure ##

<table style="font-size:12">
<tr>
<td>Type de données SQL</td>  
<td>Autorisé à index cible des types de champ</td>
<td>Notes</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Real, float</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, money<br>décimal<br>numérique
</td>
<td>Edm.String</td>
<td>Recherche Azure ne gère pas la conversion des types decimal en Edm.Double, car cela peut perdre en précision
</td>
</tr>
<tr>
<td>char, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(EDM.String)</td>
<td>Consultez les [Fonctions de mappage de champ](#FieldMappingFunctions) dans ce document pour plus d’informations sur la façon de transformer une colonne de type chaîne dans un Collection(Edm.String)</td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>emplacement géographique</td>
<td>Edm.GeographyPoint</td>
<td>Seules les instances de geography de type POINT avec 4326 SRID (qui est la valeur par défaut) sont pris en charge.</td>
</tr>
<tr>
<td>rowversion</td>
<td>N/A</td>
<td>Les colonnes de version de ligne ne peuvent pas être stockés dans l’index de recherche, mais ils peuvent être utilisés pour le suivi des modifications</td>
</tr>
<tr>
<td>heure, timespan<br>binary, varbinary, image,<br>XML, géométrie, les types CLR</td>
<td>N/A</td>
<td>Non pris en charge</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mappage entre les Types de données JSON et les Types de données de recherche Azure ##

<table style="font-size:12">
<tr>
<td>Type de données JSON</td> 
<td>Autorisé à index cible des types de champ</td>
<td>Notes</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Nombres entiers</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Nombres à virgule flottante</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>chaîne</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>les types de tableaux de type primitif, par exemple [« a », « b », « c »]</td>
<td>Collection(EDM.String)</td>
<td></td>
</tr>
<tr>
<td>Chaînes qui ressemblent à des dates</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Objets de point de GeoJSON</td>
<td>Edm.GeographyPoint</td>
<td>Les points GeoJSON sont des objets JSON dans le format suivant : {« type » : « Point », « coordonnées » : [long, table d’adresses locales]} </td>
</tr>
<tr>
<td>Autres objets JSON</td>
<td>N/A</td>
<td>Pas de prise en charge ; Recherche Azure prend actuellement en charge uniquement les types primitifs et les collections de chaînes</td>
</tr>
</table>