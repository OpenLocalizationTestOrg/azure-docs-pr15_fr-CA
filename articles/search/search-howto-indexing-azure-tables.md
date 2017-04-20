<properties
pageTitle="L’indexation de stockage par Table Azure avec recherche Azure"
description="Obtenir des informations sur les données stockées dans les Tables Azure avec Azure recherche d’index"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="08/16/2016"
ms.author="eugenesh" />

# <a name="indexing-azure-table-storage-with-azure-search"></a>L’indexation de stockage par Table Azure avec recherche Azure

Cet article explique comment utiliser Azure recherche pour indexer les données stockées dans le stockage par Table Azure. Le nouvel indexeur de tableau Azure recherche rend ce processus rapide et transparente. 

> [AZURE.IMPORTANT] Cette fonctionnalité n’est actuellement en mode Aperçu. Il est disponible uniquement dans l’API REST à l’aide de la version **2015-02-28-aperçu** et version 2.0-aperçu du Kit de développement .NET. Veuillez mémoriser, afficher un aperçu des API sont destinés à des tests et d’évaluation et ne doit pas être utilisé dans des environnements de production.

## <a name="setting-up-azure-table-indexing"></a>Configuration de l’indexation de table Azure

Pour installer et configurer un indexeur de table Azure, vous pouvez utiliser l’API REST de recherche Azure pour créer et gérer des **indexeurs** et des **sources de données** comme décrit dans [les opérations d’indexeur](https://msdn.microsoft.com/library/azure/dn946891.aspx). Vous pouvez également utiliser [version 2.0-aperçu](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) du Kit de développement .NET. À l’avenir, prise en charge l’indexation de tableau est ajouté au portail Azure.

Une source de données spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données et les stratégies qui permettent la recherche Azure identifier correctement les modifications des données (nouveau, modifié ou supprimé des lignes).

Un indexeur lit les données à partir d’une source de données et les charge dans un index de recherche de cible.

Pour configurer l’indexation de tableau :

1. Créer une source de données
    - Définir le `type` paramètre`azuretable`
    - Passez dans votre chaîne de connexion de compte de stockage comme le `credentials.connectionString` paramètre
    - Spécifiez le nom de table à l’aide de la `container.name` paramètre
    - Si vous le souhaitez, spécifier une requête à l’aide de la `container.query` paramètre. Si possible, utilisez un filtre sur les PartitionKey pour des performances optimales ; toute autre requête provoquera une analyse de table complet, ce qui peut entraîner une dégradation des performances pour les tables volumineuses.
2. Créer un index de recherche avec le schéma correspondant aux colonnes de la table que vous souhaitez indexer. 
3. En connectant votre source de données à l’index de recherche, créez l’indexeur.

### <a name="create-data-source"></a>Créer la source de données

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Pour plus d’informations sur l’API créer de source de données, consultez [Créer la source de données](search-api-indexers-2015-02-28-preview.md#create-data-source).

### <a name="create-index"></a>Création d’index 

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-target-index",
        "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
        ]
    }

Pour plus d’informations sur l’API créer d’Index, voir [Create Index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>Créez l’indexeur 

Enfin, créez l’indexeur qui fait référence à la source de données et l’index de la cible. Par exemple :

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Pour plus d’informations sur l’API d’indexeur créer, consultez [Créer un indexeur](search-api-indexers-2015-02-28-preview.md#create-indexer).

C’est tout cela - indexation heureux !

## <a name="dealing-with-different-field-names"></a>Gérer les noms de champ différents

Souvent, les noms de champ dans votre index existant sera différents des noms de propriété dans votre table. Vous pouvez utiliser les **mappages de champs** pour mapper les noms de propriété pour les noms de champ dans votre index de recherche à partir de la table. Pour plus d’informations sur les mappages de champs, consultez [mappages de champs de recherche de Azure indexeur combler les différences entre les sources de données et les index de recherche](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Gestion des clés de document

Recherche d’Azure, la clé de document identifie de façon unique un document. Chaque index de recherche doit avoir exactement un champ de clé de type `Edm.String`. Le champ de clé est requis pour chaque document est ajouté à l’index (en fait, il est le seul champ obligatoire).

Étant donné que les lignes de la table ont une clé composée, Azure recherche génère un champ synthétique appelé `Key` qui est une concaténation de valeurs clés de partition clé et de la ligne. Par exemple, si une ligne de PartitionKey est `PK1` et RowKey `RK1`, puis `Key` valeur du champ sera `PK1RK1`. 

> [AZURE.NOTE] Le `Key` valeur peut contenir des caractères non valides dans les clés de document, comme des tirets. Vous pouvez traiter des caractères non valides à l’aide de la `base64Encode` [fonction de mappage de champ](search-indexer-field-mappings.md#base64EncodeFunction). Si vous le faites, n’oubliez pas d’utiliser également lorsque passe des clés de document dans l’API appelle par exemple recherche un codage Base64 de sécurisés pour les URL.

## <a name="incremental-indexing-and-deletion-detection"></a>Détection de suppression et de l’indexation incrémentielle
 
Lorsque vous configurez un indexeur de tableau pour s’exécuter selon une planification, il répertorie à nouveau les lignes nouvelles ou mises à jour uniquement, tel que déterminé par d’une ligne `Timestamp` valeur. Vous n’êtes pas obligé de spécifier une stratégie de détection de modification : indexation incrémentielle est activée automatiquement pour vous. 

Pour indiquer que certains documents doivent être supprimés de l’index, vous pouvez utiliser une stratégie de suppression temporaire – plutôt que de supprimer une ligne, ajoutez une propriété pour indiquer qu’il est supprimé et définir une stratégie de détection de suppression souple sur la source de données. Par exemple, la stratégie ci-dessous considère qu’une ligne est supprimée si elle possède une propriété `IsDeleted` avec la valeur `"true"`: 

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer la recherche d’Azure

Si vous avez des demandes de fonctionnalités ou des idées d’amélioration, veuillez contacter nous sur notre [site de UserVoice](https://feedback.azure.com/forums/263029-azure-search/).