<properties
pageTitle="L’indexation de stockage Blob Azure avec recherche Azure"
description="Découvrez comment le stockage Blob Azure d’index et d’extraire du texte à partir de documents avec recherche d’Azure"
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
ms.date="10/16/2016"
ms.author="eugenesh" />

# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>L’indexation de Documents dans le stockage Blob Azure avec recherche Azure

Cet article montre comment utiliser la recherche d’Azure pour indexer des documents (par exemple un fichier PDF, les documents Microsoft Office et plusieurs autres formats courants) stockés dans le stockage des objets Blob Azure. Le nouvel indexeur de blob Azure recherche rend ce processus rapide et transparente. 

> [AZURE.IMPORTANT] Cette fonctionnalité n’est actuellement en mode Aperçu. Il est uniquement disponible dans l’API REST à l’aide de la version **2015-02-28-aperçu**. Veuillez mémoriser, afficher un aperçu des API sont destinés à des tests et d’évaluation et ne doit pas être utilisé dans des environnements de production.

## <a name="supported-document-formats"></a>Les formats de document pris en charge

L’indexeur de l’objet blob peut extraire du texte à partir de formats de document suivants :

- FICHIER PDF
- Formats de Microsoft Office : DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG (e-mails Outlook)  
- HTML
- XML
- LE CODE POSTAL
- EML
- Fichiers de texte brut  
- JSON (voir le [BLOB de JSON d’indexation](search-howto-index-json-blobs.md) pour plus de détails)
- CSV (voir [CSV d’indexation BLOB](search-howto-index-csv-blobs.md) )

## <a name="setting-up-blob-indexing"></a>Configuration de l’indexation

Vous pouvez définir un indexeur de stockage des objets Blob Azure à l’aide de :

- [Azure portal](https://ms.portal.azure.com)
- Recherche Azure [API REST](https://msdn.microsoft.com/library/azure/dn946891.aspx)
- Azure Search .NET SDK [version 2.0-aperçu](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) 

> [AZURE.NOTE] Certaines fonctionnalités (par exemple, les mappages de champs) ne sont pas encore disponibles dans le portail et utiliser par programme. 

Dans cet article, nous allons créer un indexeur à l’aide de l’API REST en suivant trois étapes : créer une source de données, créer un index, configurer l’indexeur.

### <a name="step-1-create-a-data-source"></a>Étape 1 : Créer une source de données

Une source de données spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données et des stratégies pour identifier correctement les modifications des données (lignes nouvelles, modifiées ou supprimées). Une source de données peut être utilisée par plusieurs indexeurs dans le même service de recherche.

Pour l’indexation, la source de données doit avoir les propriétés suivantes : 

- **nom** est le nom unique de la source de données au sein de votre service de recherche. 

- **le type** doit être `azureblob`. 

- Fournit des **informations d’identification de** la chaîne de connexion de compte de stockage comme le `credentials.connectionString` paramètre. Vous pouvez obtenir la chaîne de connexion à partir du portail Azure en accédant à la lame de compte de stockage voulu > **paramètres** > **clés** et utilisez la valeur « Chaîne de connexion primaire » ou « Chaîne de connexion secondaire ». 

- **conteneur** spécifie un conteneur dans votre compte de stockage. Par défaut, tous les objets BLOB dans le conteneur sont récupérables. Si vous souhaitez uniquement les objets BLOB d’index d’un répertoire virtuel particulier, vous pouvez spécifier ce répertoire en utilisant le paramètre de l’option de **requête** . 

L’exemple suivant illustre une définition de source de données :

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Pour plus d’informations sur l’API créer de source de données, consultez [Créer la source de données](search-api-indexers-2015-02-28-preview.md#create-data-source).

### <a name="step-2-create-an-index"></a>Étape 2 : Création d’un index 

L’index spécifie les champs dans un document, les attributs, et rencontrer d’autres constructions qui forme la recherche.  

Pour l’indexation, veillez à ce que l’index a une recherche `content` champ pour stocker le blob.

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-target-index",
        "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
        ]
    }

Pour plus d’informations sur l’API créer d’Index, voir [Create Index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="step-3-create-an-indexer"></a>Étape 3 : Créer un indexeur 

Un indexeur connecte des sources de données avec l’index de recherche de cible et fournit des informations de planification qui vous pouvez d’automatiser l’actualisation des données. Une fois la source de données et d’index ont été créés, il est relativement simple de créer un indexeur qui fait référence à la source de données et un index de la cible. Par exemple :

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Cet indexeur s’exécutera toutes les deux heures (intervalle de planification est définie à « PT2H »). Pour exécuter un indexeur toutes les 30 minutes, définissez l’intervalle à « PT30M ». Le plus court intervalle pris en charge est de 5 minutes. Planification est facultative - en cas d’omission, d’un indexeur s’exécute une seule fois lors de la création. Toutefois, vous pouvez exécuter un indexeur sur la demande à tout moment.   

Pour plus d’informations sur l’API d’indexeur créer, consultez [Créer un indexeur](search-api-indexers-2015-02-28-preview.md#create-indexer).


## <a name="document-extraction-process"></a>Processus d’extraction de document

Recherche Azure indexe chaque document (blob) comme suit :

- Tout le contenu textuel du document est extraite dans un champ de type chaîne nommé `content`. Actuellement, nous n’en charge pour l’extraction de plusieurs documents à partir d’un objet blob unique :
    - Par exemple, un fichier CSV est indexé sous la forme d’un document unique. Si vous avez besoin traiter chaque ligne dans un fichier CSV comme un document séparé, votez pour [cette suggestion de UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/13865325-please-treat-each-line-in-a-csv-file-as-a-separate).
    - Un document composé ou incorporé (par exemple, une archive ZIP ou un document Word avec embedded Outlook email message électronique contenant des pièces jointes) est également indexé sous la forme d’un document unique.

- Propriétés de métadonnées de défini par l’utilisateur présentes sur le blob, le cas échéant, sont extraits textuellement. Les propriétés de métadonnées peuvent également être utilisées pour contrôler certains aspects du processus d’extraction de document – pour plus d’informations, reportez-vous à la section [à l’aide de métadonnées personnalisées pour une Extraction de Document de contrôle](#CustomMetadataControl) .

- Les propriétés de métadonnées standard blob sont extraits dans les champs suivants :

    - **métadonnées\_stockage\_nom de** (Edm.String) - le nom de fichier de l’objet blob. Par exemple, si vous disposez d’un objet blob de /my-container/my-folder/subfolder/resume.pdf, la valeur de ce champ est `resume.pdf`.

    - **métadonnées\_stockage\_chemin d’accès** (Edm.String) - l’URI complet de l’objet blob, y compris le compte de stockage. Par exemple,`https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

    - **métadonnées\_stockage\_contenu\_type de** (Edm.String) - type, comme spécifié par le code que vous avez utilisé pour charger le blob de contenu. Par exemple, `application/octet-stream`.

    - **métadonnées\_stockage\_dernier\_modifié** (Edm.DateTimeOffset) - dernière modification de timestamp pour le blob. Recherche Azure utilise l’horodatage pour identifier les objets BLOB modifiées, afin d’éviter tout réindexation après l’indexation initiale.

    - **métadonnées\_stockage\_taille** (Edm.Int64) - taille en octets de la tache.

    - **métadonnées\_stockage\_contenu\_md5** (Edm.String) - hachage MD5 du contenu blob, si disponible.

- Propriétés de métadonnées spécifiques à chaque format de document sont extraits dans le champs répertoriés [ici](#ContentSpecificMetadata).

Vous n’avez pas besoin de définir des champs pour toutes les propriétés ci-dessus dans votre index de recherche, contentez-vous de capturer les propriétés dont vous avez besoin pour votre application. 

> [AZURE.NOTE] Souvent, les noms de champ dans votre index existant sera différents des noms de champ générés lors de l’extraction du document. Vous pouvez utiliser les **mappages de champs** pour mapper les noms de propriété fournies par Azure recherche aux noms de champ dans votre index de recherche. Vous verrez un exemple de champ à l’aide de mappages ci-dessous. 

## <a name="picking-the-document-key-field-and-dealing-with-different-field-names"></a>Le champ clé de document de prélèvement et le traitement des noms de champ différents

Recherche d’Azure, la clé de document identifie de façon unique un document. Chaque index de recherche doit avoir exactement un champ de clé de type Edm.String. Le champ de clé est requis pour chaque document est ajouté à l’index (il est réellement le seul champ obligatoire).  
   
Vous devez réfléchir soigneusement le champ extrait doit être mappé sur le champ de clé de votre index. Les candidats sont :

- **métadonnées\_stockage\_nom de** - il peut s’agir d’un candidat pratique, mais notez que 1) les noms ne sont pas nécessairement uniques, comme vous pouvez avoir des objets BLOB avec le même nom dans des dossiers différents et 2) le nom peut contenir des caractères qui ne sont pas valides dans les clés de document, tels que des tirets. Vous pouvez traiter des caractères non valides à l’aide de la `base64Encode` [fonction de mappage de champ](search-indexer-field-mappings.md#base64EncodeFunction) - n’oubliez pas que si vous le faites coder les clés de document lorsque les transmettre dans l’API appelle par exemple de recherche. (Par exemple, dans .NET vous pouvez utiliser la [méthode de UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) à cet effet).

- **métadonnées\_stockage\_chemin d’accès** - le chemin d’accès complet garantit l’unicité, mais le chemin d’accès contient sans aucun doute `/` les caractères qui ne sont [pas valides dans une clé de document](https://msdn.microsoft.com/library/azure/dn857353.aspx).  Comme indiqué ci-dessus, vous avez l’option de codage les clés à l’aide de la `base64Encode` [fonction](search-indexer-field-mappings.md#base64EncodeFunction).

- Si aucune des options ci-dessus fonctionne pour vous, vous pouvez ajouter une propriété de métadonnées personnalisée pour les objets BLOB. Cette option oblige, toutefois, le processus de téléchargement de blob pour ajouter cette propriété de métadonnées pour tous les BLOB. Dans la mesure où la clé est une propriété requise, tous les objets BLOB qui n’ont pas cette propriété ne pourra pas être indexée.

> [AZURE.IMPORTANT] S’il n’existe aucun mappage explicite pour le champ clé dans l’index, la recherche de Azure utilise automatiquement `metadata_storage_path` sous la clé et de la base 64 code les valeurs de clé (la deuxième option ci-dessus).

Pour cet exemple, nous allons choisir le `metadata_storage_name` champ comme la clé de document. Supposons également votre index a un champ de clé nommé `key` et un champ `fileSize` pour le stockage de la taille du document. Pour relier les éléments comme vous le souhaitez, spécifier les mappages de champs suivantes lors de la création ou la mise à jour de votre indexeur :

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Pour faire cela ensemble, voici comment vous pouvez ajouter des mappages de champs et activer le codage en base 64 de clés pour un indexeur existant :

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [AZURE.NOTE] Pour plus d’informations sur les mappages de champs, consultez [cet article](search-indexer-field-mappings.md).

## <a name="incremental-indexing-and-deletion-detection"></a>Détection de suppression et de l’indexation incrémentielle

Lorsque vous configurez un indexeur de blob à exécuter selon une planification, il réindexe uniquement les blobs modifiés, tel que déterminé par du blob `LastModified` timestamp.

> [AZURE.NOTE] Vous n’êtes pas obligé de spécifier une stratégie de détection de modification : indexation incrémentielle est activée automatiquement pour vous.

Pour prendre en charge la suppression des documents, utiliser une approche de « suppression temporaire ». Si vous supprimez les objets BLOB à titre définitif, les documents correspondants ne seront pas supprimés à partir de l’index de recherche. Au lieu de cela, procédez comme suit :  

1. Ajouter une propriété de métadonnées personnalisées à l’objet blob pour indiquer à la recherche d’Azure, qu’il est logiquement supprimé
2. Configurer une stratégie de détection de suppression souple sur la source de données
3. Une fois que l’indexeur a traité le blob (comme indiqué par le statut de l’indexation API), vous pouvez supprimer physiquement le blob

Par exemple, la stratégie suivante considère comme un objet blob à être supprimé s’il a une propriété de métadonnées `IsDeleted` avec la valeur `true`:

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",   
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Propriétés de métadonnées spécifiques au type de contenu

Le tableau suivant résume le traitement effectué pour chaque format de document et décrit les propriétés de métadonnées extraites par recherche d’Azure.

Format de document / type de contenu | Type de contenu des propriétés de métadonnées | Les détails de traitement
-------------------------------|-------------------------------------------|-------------------
HTML (`text/html`) | `metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` | Supprimer les balises HTML et d’extraire du texte
PDF (`application/pdf`) | `metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`| Extraire du texte, y compris les documents intégrés (à l’exclusion des images)
DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Extraire du texte, y compris les documents intégrés
DOC (application/msword) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Extraire du texte, y compris les documents intégrés
XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Extraire du texte, y compris les documents intégrés
XLS (application/vnd.ms-excel) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Extraire du texte, y compris les documents intégrés
Format PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Extraire du texte, y compris les documents intégrés
PPT (application/vnd.ms-powerpoint) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Extraire du texte, y compris les documents intégrés
MSG (application/vnd.ms-outlook) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` | Extraire du texte, y compris les pièces jointes
ZIP (application/zip) | `metadata_content_type` | Extrait du texte de tous les documents dans l’archive
XML (application/xml) | `metadata_content_type`</br>`metadata_content_encoding`</br> | Supprimer des balises XML et extraire du texte
JSON (application/json) | `metadata_content_type`</br>`metadata_content_encoding` | Extraire du texte<br/>Remarque : Si vous avez besoin extraire plusieurs champs de document à partir d’un objet blob de JSON, consultez [blobs de JSON d’indexation](search-howto-index-json-blobs.md) pour plus de détails
EML (message/rfc822) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` | Extraire du texte, y compris les pièces jointes
Texte brut (texte/brut) | `metadata_content_type`</br>`metadata_content_encoding`</br> | 

<a name="CustomMetadataControl"></a>
## <a name="using-custom-metadata-to-control-document-extraction"></a>À l’aide de métadonnées personnalisées pour l’extraction des documents de contrôle

Vous pouvez ajouter des propriétés de métadonnées pour un objet blob à contrôler certains aspects du processus d’extraction d’indexation et de document blob. Les propriétés suivantes sont actuellement prises en charge :

Nom de la propriété | Valeur de la propriété | Explication
--------------|----------------|------------
AzureSearch_Skip | « true » | Indique à l’indexeur de l’objet blob complètement ignorer le blob ; tentative d’extraction de contenu ni de métadonnées. Cela est utile lorsque vous souhaitez ignorer certains types de contenu, ou lorsqu’un blob donné échoue à plusieurs reprises et interrompt le processus d’indexation.
AzureSearch_SkipContent | « true » | Indique à l’indexeur de l’objet blob uniquement les index les métadonnées et ignorer l’extraction de contenu de l’objet blob. Cela est utile si le contenu de l’objet blob n’est pas intéressant, mais vous souhaitez indexer les métadonnées jointes au blob.

<a name="IndexerParametersConfigurationControl"></a>
## <a name="using-indexer-parameters-to-control-document-extraction"></a>À l’aide des paramètres d’indexeur pour contrôler l’extraction des documents

Configuration d’indexer plusieurs paramètres sont disponibles pour contrôler qui BLOB et les parties de contenu et les métadonnées d’un objet blob qui sont indexés. 

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Index uniquement les objets BLOB avec les extensions de fichier spécifiques

Vous pouvez indexer uniquement les objets BLOB avec les extensions de nom fichier que vous spécifiez à l’aide de la `indexedFileNameExtensions` le paramètre de configuration indexeur. La valeur est une chaîne contenant une liste séparée par des virgules d’extensions de fichier (avec un point de début). Par exemple, pour l’index seulement le. PDF et. Objets BLOB DOCX, procédez comme suit : 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions-from-indexing"></a>Exclure les objets BLOB avec les extensions de fichier spécifiques de l’indexation

Vous pouvez exclure des objets BLOB avec les extensions de nom de fichier spécifique de l’indexation à l’aide de la `excludedFileNameExtensions` le paramètre de configuration. La valeur est une chaîne contenant une liste séparée par des virgules d’extensions de fichier (avec un point de début). Par exemple, pour les index de tous les BLOB, à l’exception de ceux dont la. PNG et. Extensions JPEG, procédez comme suit : 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Si les deux `indexedFileNameExtensions` et `excludedFileNameExtensions` paramètres sont présents, Azure recherche cherche en premier `indexedFileNameExtensions`, puis à `excludedFileNameExtensions`. Cela signifie que si la même extension de fichier est présente dans les deux listes, elle sera exclue de l’indexation. 

### <a name="index-storage-metadata-only"></a>Index uniquement les métadonnées de stockage

Vous pouvez indexer uniquement les métadonnées de stockage et complètement ignorer le processus de d’extraction de document à l’aide du `indexStorageMetadataOnly` propriété de configuration. Cela est utile lorsque vous n’avez pas besoin du contenu du document, ni vous avez besoin de toutes les propriétés de métadonnées spécifiques au type de contenu. Pour ce faire, définissez la `indexStorageMetadataOnly` propriété `true`: 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexStorageMetadataOnly" : true } }
    }

### <a name="index-both-storage-and-content-type-metadata-but-skip-content-extraction"></a>Index de stockage et métadonnées de type de contenu, mais ignorer l’extraction de contenu

Si vous devez extraire toutes les métadonnées, mais ignorer l’extraction de contenu pour tous les BLOB, vous pouvez demander ce comportement à l’aide de la configuration de l’indexeur, au lieu de devoir ajouter `AzureSearch_SkipContent` chacune métadonnées blob individuellement. Pour ce faire, définissez la `skipContent` propriété d’indexeur de configuration pour `true`: 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "skipContent" : true } }
    }

## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer la recherche d’Azure

Si vous avez des demandes de fonctionnalités ou des idées d’amélioration, veuillez contacter nous sur notre [site de UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
