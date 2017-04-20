<properties
pageTitle="L’indexation des objets BLOB CSV avec l’indexeur de blob Azure recherche | Microsoft Azure"
description="Obtenir des informations sur les blobs CSV avec Azure recherche d’index"
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
ms.date="07/12/2016"
ms.author="eugenesh" />

# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>L’indexation des objets BLOB CSV avec l’indexeur de blob Azure recherche 

Par défaut, analyse [d’indexeur de blob Azure recherche](search-howto-indexing-azure-blob-storage.md) délimités par des blobs de texte sous la forme d’un segment de texte unique. Toutefois, de BLOB contenant des données CSV, vous souhaitez souvent de traiter chaque ligne de l’objet blob comme un document séparé. Par exemple, étant donné le texte délimité suivant : 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Vous souhaiterez peut-être analyser dans les 2 documents, chacun contenant les champs « balises », « datePublished » et « id ».

Dans cet article, vous apprendrez à analyser les blobs CSV avec un indexeur d’objet blob Azure recherche. 

> [AZURE.IMPORTANT] Cette fonctionnalité est actuellement en mode Aperçu. Il est uniquement disponible dans l’API REST à l’aide de la version **2015-02-28-aperçu**. Veuillez mémoriser, afficher un aperçu des API sont destinés à des tests et d’évaluation et ne doit pas être utilisé dans des environnements de production. 

## <a name="setting-up-csv-indexing"></a>Configuration de l’indexation de CSV

Pour indexer des blobs CSV, de créer ou de mettre à jour la définition d’un indexeur avec la `delimitedText` mode d’analyse :  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Pour plus d’informations sur l’API d’indexeur créer, consultez [Créer un indexeur](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders`Indique que la première ligne (non vide) de chaque objet blob contient des en-têtes.
Si une ligne d’en-tête initiale ne contiennent pas de BLOB, les en-têtes doivent être spécifiés dans la configuration de l’indexeur : 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Actuellement, seul le codage UTF-8 prend en charge. En outre, seulement la virgule `','` caractère est pris en charge comme délimiteur. Si vous avez besoin de la prise en charge d’autres codages ou les délimiteurs, faites-le nous savoir sur [notre site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [AZURE.IMPORTANT] Lorsque vous utilisez le mode d’analyse de texte délimité, recherche d’Azure suppose que tous les objets BLOB dans votre source de données sera CSV. Si vous avez besoin prendre en charge un mélange de blobs CSV et non-CSV dans la source de données, faites-le nous savoir sur [notre site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="request-examples"></a>Exemples de demande

Cela mettre tous les ensemble, voici les exemples complets de charge utile. 

Source de données : 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexeur :

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer la recherche d’Azure

Si vous avez des demandes de fonctionnalités ou des idées d’amélioration, veuillez contacter nous sur notre [site de UserVoice](https://feedback.azure.com/forums/263029-azure-search/).