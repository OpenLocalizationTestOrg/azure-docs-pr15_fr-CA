<properties
   pageTitle="Azure Service reste API Version 2015-02-28-aperçu de la recherche | Microsoft Azure | Aperçu de recherche Azure API"
   description="Azure Service reste API Version 2015-02-28-aperçu de la recherche inclut les fonctionnalités expérimentales telles que les recherches analyseurs de langage naturel et moreLikeThis."
   services="search"
   documentationCenter="na"
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search"
   ms.date="09/07/2016"
   ms.author="brjohnst"/>

# <a name="azure-search-service-rest-api-version-2015-02-28-preview"></a>API de recherche Azure Service REST : Version 2015-02-28-aperçu

Cet article est la documentation de référence de `api-version=2015-02-28-Preview`. Cet aperçu étend la version généralement disponible actuelle, [-version de l’api = 2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), en fournissant les fonctionnalités expérimentales suivantes :

- `moreLikeThis`une requête paramètre dans les API de [Rechercher des Documents](#SearchDocs) . Il recherche les autres documents qui sont pertinentes à un autre document spécifique.

Quelques éléments supplémentaires de la `2015-02-28-Preview` API REST sont documentées séparément. Celles-ci comprennent :

- [Profils de score](search-api-scoring-profiles-2015-02-28-preview.md)
- [Indexeurs](search-api-indexers-2015-02-28-preview.md)

Service de recherche Azure est disponible en plusieurs versions. Pour plus d’informations, reportez-vous à [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

## <a name="apis-in-this-document"></a>API de ce document

API de service de recherche Azure prend en charge deux syntaxes d’URL pour les opérations de l’API : simple et OData (voir la [prise en charge pour OData (API de recherche Azure)](http://msdn.microsoft.com/library/azure/dn798932.aspx) pour plus de détails). La liste suivante montre la syntaxe simple.

[Création d’Index](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Mettre à jour les Index](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Obtenir des Index](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Liste des index](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Obtenir des statistiques d’Index](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Analyseur de test de](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[Supprimer un Index](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Ajouter, supprimer et mettre à jour les données dans un Index](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Rechercher des Documents](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Recherche Document](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Nombre de Documents](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Suggestions](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

________________________________________
<a name="IndexOps"></a>
## <a name="index-operations"></a>Opérations d’indexation

Vous pouvez créer et gérer des index dans le service de recherche d’Azure via des requêtes HTTP simples (POST, GET, PUT, DELETE) pour une ressource de l’index donné. Pour créer un index, vous VALIDEZ tout d’abord un document JSON qui décrit le schéma d’index. Le schéma définit les champs de l’index, leurs types de données, et comment ils peuvent être utilisés (par exemple, dans les recherches de texte intégral, des filtres, tri ou faceting). Il définit également les profils d’évaluation, suggesters et d’autres attributs pour configurer le comportement de l’index.

L’exemple suivant fournit une illustration d’un schéma utilisé pour la recherche sur les informations sur l’hôtel avec le champ de Description défini dans les deux langues. Notez comment les attributs contrôlent la façon dont le champ est utilisé. Par exemple le `hotelId` est utilisé comme clé de document (`"key": true`) et est exclu de la recherche de texte intégral (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
      {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Une fois l’index créé, vous devez télécharger les documents remplir l’index. Pour cette étape suivante, consultez [Ajout ou mise à jour de Documents](#AddOrUpdateDocuments) .

Pour l’indexation dans Azure recherche une présentation vidéo, consultez l' [épisode de couvrir de Channel 9 nuage sur Azure recherche](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
## <a name="create-index"></a>Création d’Index

Un index est le principal moyen d’organisation et de recherche de documents dans la recherche d’Azure, similaire à la manière dont une table organise des enregistrements dans une base de données. Chaque index a un ensemble de documents que sont tous conformes au schéma d’index (noms de champs, les types de données et propriétés), mais également spécifient des constructions supplémentaires (suggesters, les profils de score et les options CORS) qui définissent d’autres comportements de recherche des index.

Vous pouvez créer un nouvel index dans un service de recherche d’Azure à l’aide d’une demande HTTP POST ou PUT. Le corps de la demande est un schéma JSON qui spécifie les informations d’index et de configuration.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Également, vous pouvez utiliser l’instruction PUT et spécifier le nom de l’index de l’URI. Si l’index n’existe pas, il sera créé.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

Création d’un index détermine la structure des documents stockés et utilisés dans les opérations de recherche. Remplissage de l’index est une opération distincte. Pour cette étape, vous pouvez utiliser un [indexeur](https://msdn.microsoft.com/library/azure/mt183328.aspx) (disponible pour les sources de données prises en charge) ou une opération [d’Ajout, mise à jour ou supprimer les Documents](https://msdn.microsoft.com/library/azure/dn798930.aspx) . L’index inversé est généré lorsque les documents sont validés.

**Remarque**: le nombre maximal d’index autorisé varie selon le niveau de prix. Le service gratuit permet de 3 index. Service standard permet 50 d’index par le service de recherche. Pour plus d’informations, reportez-vous à la section [contraintes et les limites](http://msdn.microsoft.com/library/azure/dn798934.aspx) .

**Demande**

HTTPS est requis pour toutes les demandes de service. La demande de **Create Index** peut être construite à l’aide de la méthode un POST ou PUT. Lors de l’utilisation de POST, vous devez fournir un nom d’index dans le corps de la demande ainsi que la définition de schéma d’index. Avec l’instruction PUT, le nom de l’index fait partie de l’URL. Si l’index n’existe pas, il est créé. S’il existe déjà, il est mis à jour à la nouvelle définition.

Le nom de l’index doit être en minuscule, commencer par une lettre ou un chiffre, ont sans barres obliques ou points et comporter moins de 128 caractères. Après avoir démarré le nom d’index par une lettre ou un chiffre, le reste du nom peut inclure toute lettre, nombre et des tirets, tant que les tirets ne sont pas consécutifs.

Le `api-version` est requis. Pour obtenir la liste des versions disponibles, reportez-vous à la section [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs.

- `Content-Type`: Obligatoire. Cette valeur`application/json`
- `api-key`: Obligatoire. Le `api-key` est utilisé pour
- authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à votre service. La demande de **Créer un Index** doit comporter une `api-key` en-tête défini à votre clé admin (et non d’une clé de la requête).

Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

<a name="RequestData"></a>
**Syntaxe de corps de requête**

Le corps de la demande contient une définition de schéma, qui inclut la liste de champs de données dans les documents qui sont réinjectées dans cet index, types de données, les attributs, ainsi qu’une liste facultative d’évaluation des profils qui sont utilisées pour évaluer les documents correspondants au moment de la requête.

Notez que pour une demande POST, vous devez spécifier le nom de l’index dans le corps de la demande.

Il peut être uniquement un champ clé dans l’index. Il doit être un champ de type chaîne. Ce champ représente l’identificateur unique pour chaque document stocké dans l’index.

Les parties principales d’un index sont les suivants :

- `name`
- `fields`qui sont réinjectées dans cet index, y compris le nom, le type de données et les propriétés qui définissent les actions autorisées sur ce champ.
- `suggesters`utilisé pour les requêtes semi-automatique ou saisie semi-automatique.
- `scoringProfiles`utilisé pour le classement de score de recherche personnalisée. Pour plus d’informations, reportez-vous à la section [Ajouter des profils score](https://msdn.microsoft.com/library/azure/dn798928.aspx) .
- `analyzers`, `charFilters`, `tokenizers`, `tokenFilters` permet de définir comment vos documents/requêtes sont classés par les jetons de recherche/indexables. Pour plus d’informations, consultez [analyse de recherche d’Azure](https://aka.ms//azsanalysis) .
- `defaultScoringProfile`permet de remplacer les scores des comportements par défaut.
- `corsOptions`Pour autoriser les requêtes d’origine croisée par rapport à votre index.

La syntaxe de structuration de la charge utile de demande est comme suit. Une demande d’exemple est fournie plus loin dans cette rubrique.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,              
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**Attributs d’index**

Les attributs suivants peuvent être définies lors de la création d’un index. Pour plus d’informations sur les profils d’évaluation et de notation, voir [Ajouter des profils de score](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name`-Définit le nom du champ.

`type`-Définit le type de données pour le champ. Pour une liste des types pris en charge, consultez [Types de données pris en charge](#DataTypes) .

`searchable`-Marque le champ en tant que texte intégral accessibles en recherche. Cela signifie qu’il est soumises à analyse de césure pendant l’indexation. Si vous définissez un `searchable` champ une valeur comme « jour de soleil », en interne, il est divisé en jetons individuels « soleil » et « jour ». Cela permet des recherches de texte intégral pour ces conditions. Les champs de type `Edm.String` ou `Collection(Edm.String)` sont `searchable` par défaut. D’autres types ne peuvent pas être `searchable`.

  - **Remarque**: `searchable` champs consomment de l’espace supplémentaire dans votre index car Azure recherche stocke une version sous forme de jeton supplémentaire de la valeur de champ pour les recherches de texte intégral. Si vous souhaitez économiser de l’espace dans votre index, et vous n’avez pas besoin d’un champ à inclure dans les recherches, définissez `searchable` à `false`.

`filterable`-Permet d’être référencé dans le champ `$filter` requêtes. `filterable`diffère de `searchable` gestion des chaînes. Les champs de type `Edm.String` ou `Collection(Edm.String)` des `filterable` ne subissent pas de césure, afin que les comparaisons sont uniquement les correspondances exactes. Par exemple, si vous définissez ce type de champ `f` à « jour de soleil », `$filter=f eq 'sunny'` ne trouverez aucune correspondance, mais `$filter=f eq 'sunny day'` sera. Tous les champs sont `filterable` par défaut.

`sortable`-Par défaut le système trie les résultats par score, mais dans de nombreuses expériences utilisateurs souhaiteront trier les champs dans les documents. Les champs de type `Collection(Edm.String)` ne peut pas être `sortable`. Tous les autres champs sont `sortable` par défaut.

`facetable`-Généralement utilisée dans une présentation des résultats de recherche qui inclut le nombre d’accès par catégorie (par exemple, rechercher des appareils photo numériques et des accès voir par marque, par mégapixels, par le prix, etc.). Cette option ne peut pas être utilisée avec des champs de type `Edm.GeographyPoint`. Tous les autres champs sont `facetable` par défaut.

  - **Remarque**: les champs de type `Edm.String` des `filterable`, `sortable`, ou `facetable` soit au maximum 32 Ko en longueur. C’est parce que ces champs sont traités comme un terme de recherche unique, et que la longueur maximale d’un terme de recherche d’Azure est de 32 Ko. Si vous devez stocker plus de texte dans un champ de chaîne unique, vous devez définir explicitement `filterable`, `sortable`, et `facetable` de `false` dans votre définition d’index.

  - **Remarque**: si un champ n’a aucun des attributs ci-dessus la valeur `true` (`searchable`, `filterable`, `sortable`, ou`facetable`) efficacement, le champ est exclu de l’index inversé. Cette option est utile pour les champs qui ne sont pas utilisés dans les requêtes, mais sont nécessaires dans les résultats de la recherche. À l’exclusion de ces champs à partir de l’index améliore les performances.

`key`-Marque le champ comme contenant des identificateurs uniques pour les documents dans l’index. Exactement un champ doit être choisi comme le `key` champ et il doivent être de type `Edm.String`. Champs clés peuvent être utilisés pour rechercher des documents directement par l’intermédiaire de l' [API de recherche](#LookupAPI).

`retrievable`-Définit si le champ peut être retourné dans un résultat de recherche.  Cela est utile lorsque vous souhaitez utiliser un champ (par exemple, marge) en tant que filtre, de tri ou de mécanisme d’attribution de scores mais que vous ne souhaitez pas que le champ soit visible à l’utilisateur final. Cet attribut doit être `true` de `key` champs.

`analyzer`-Définit le nom de l’analyseur à utiliser pour le champ au moment de la recherche et d’indexation. Pour le jeu autorisé des valeurs voir [analyseurs](https://msdn.microsoft.com/library/mt605304.aspx). Cette option peut être utilisée uniquement avec `searchable` champs et il ne peut être définies avec une ou l’autre `searchAnalyzer` ou `indexAnalyzer`.  Une fois que l’analyseur est choisi, il ne peut pas être modifié pour le champ.

`searchAnalyzer`-Définit le nom de l’analyseur utilisé au moment de la recherche pour le champ. Pour le jeu autorisé des valeurs voir [analyseurs](https://msdn.microsoft.com/library/mt605304.aspx). Cette option peut être utilisée uniquement avec les `searchable` les champs. Il doit être défini avec `indexAnalyzer` et il ne peut pas être défini avec la `analyzer` option. Cet analyseur peut être mis à jour sur un champ existant.

`indexAnalyzer`-Définit le nom de l’analyseur utilisé au moment de l’indexation pour le champ. Pour le jeu autorisé des valeurs voir [analyseurs](https://msdn.microsoft.com/library/mt605304.aspx). Cette option peut être utilisée uniquement avec les `searchable` les champs. Il doit être défini avec `searchAnalyzer` et il ne peut pas être défini avec la `analyzer` option. Une fois que l’analyseur est choisi, il ne peut pas être modifié pour le champ.

`suggesters`-Définit le mode de recherche et les champs qui sont à l’origine du contenu pour obtenir des suggestions. Pour plus d’informations, consultez [Suggesters](#Suggesters) .

`scoringProfiles`-Définit le score des comportements personnalisés qui vous permettent d’influencer les éléments s’affichent dans les résultats de la recherche. Profils de score sont constituées des fonctions et des poids de champ. Pour plus d’informations sur les attributs utilisés dans un profil d’évaluation, consultez [Ajouter le score de profils](https://msdn.microsoft.com/library/azure/dn798928.aspx) .

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a>
**Prise en charge linguistique**

Champs de recherche sont soumis à l’analyse que la plupart implique souvent la césure de mots, la normalisation du texte et le filtrage des termes. Par défaut, des champs de recherche dans recherche d’Azure sont analysées avec l' [Analyseur Apache Lucene Standard](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) qui rompt le texte dans les éléments qui suivent les règles de[« Segmentation du texte Unicode »](http://unicode.org/reports/tr29/) . En outre, l’analyseur standard convertit tous les caractères dans leur forme de minuscules. Les documents indexés et des termes de recherche accéder par le biais de l’analyse lors de l’indexation et de traitement des requêtes.

Recherche Azure prend en charge une variété de langues. Chaque langue nécessite un analyseur de texte non standard qui tient compte des caractéristiques d’une langue donnée. Recherche Azure propose deux types d’analyseurs :

- analyseurs de 35 par Lucene.
- analyseurs de 50 par propriétaire langage naturel de Microsoft utilisée dans Office et Bing de technologie de traitement.

Certains développeurs préfèrent la solution plus familière, simple, open source Lucene. Analyseurs de Lucene sont plus rapides, mais les analyseurs Microsoft ont avancées, telles que lemmatisation, word decompounding (dans des langages comme allemand, danois, néerlandais, suédois, norvégien, estonien, terminer, hongrois, slovaque) et de reconnaissance d’entité (URL, e-mails, dates, nombres). Si possible, vous devez exécuter des comparaisons de Microsoft et Lucene analyseurs pour déterminer celle qui est la plus adaptée.

***Comparatif***

L’analyseur Lucene pour l’anglais s’étend l’analyseur standard. Supprime les possessifs (du fin) de mots, s’applique la recherche de radical en fonction de [l’algorithme de recherche de radical Porter](http://tartarus.org/~martin/PorterStemmer/)et supprime l’anglais [mots vides](http://en.wikipedia.org/wiki/Stop_words).

En comparaison, l’analyseur Microsoft effectue lemmatisation au lieu de la recherche de radical. Cela signifie qu’il peut gérer formes fléchies et irrégulières beaucoup mieux les résultats dans les résultats de recherche plus pertinents (espion module 7 [d’Azure recherche MVA présentation](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) pour plus de détails).

L’indexation avec les analyseurs de Microsoft est en moyenne deux à trois fois plus lente que leurs équivalents Lucene, en fonction de la langue. Performances de la recherche ne doivent pas être considérablement affectées pour les requêtes de taille moyenne.

***Configuration de***

Pour chaque champ dans la définition d’index, vous pouvez définir le `analyzer` propriété un nom de l’analyseur qui spécifie la langue et le fournisseur. Le même analyseur sera appliqué lors de l’indexation et de recherche pour ce champ.
Par exemple, peuvent avoir des champs distincts pour les descriptions d’hôtel en anglais, en espagnol et en Français qui existent côte à côte dans le même index. Pour spécifier quel champ spécifique à la langue à rechercher dans vos requêtes, utilisez le [paramètre de requête « searchFields »](#SearchQueryParameters) . Vous pouvez consulter les exemples de requête qui contiennent les `analyzer` propriété dans les [Documents de recherche](#SearchDocs). 

***Liste de l’analyseur***

Vous trouverez ci-dessous la liste des langues prises en charge avec les noms analyzer Lucene et Microsoft.

<table style="font-size:12">
    <tr>
        <th>Langue</th>
        <th>Nom de l’analyseur Microsoft</th>
        <th>Nom de l’analyseur Lucene</th>
    </tr>
    <tr>
        <td>Arabe</td>
        <td>ar.Microsoft</td>
        <td>ar.Lucene</td>      
    </tr>
    <tr>
        <td>Arménien</td>
        <td></td>
        <td>HY.Lucene</td>
    </tr>
    <tr>
        <td>Bangla</td>
        <td>bn.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Basque</td>
        <td></td>
        <td>eu.Lucene</td>
    </tr>
    <tr>
        <td>Bulgare</td>
        <td>BG.Microsoft</td>
        <td>BG.Lucene</td>
    </tr>
    <tr>
        <td>Catalan</td>
        <td>CA.Microsoft</td>
        <td>CA.Lucene</td>          
    </tr>
    <tr>
        <td>Chinois simplifié</td>
        <td>zh-Hans.microsoft</td>
        <td>zh-Hans.lucene</td>     
    </tr>
    <tr>
        <td>Chinois traditionnel</td>
        <td>zh-Hant.microsoft</td>
        <td>zh-Hant.lucene</td>     
    <tr>
    <tr>
        <td>Croate</td>
        <td>HR.Microsoft</td>
        <td/></td>
    </tr>
    <tr>
        <td>Tchèque</td>
        <td>cs.Microsoft</td>
        <td>cs.Lucene</td>      
    </tr>    
    <tr>
        <td>Danois</td>
        <td>DA.Microsoft</td>
        <td>DA.Lucene</td>      
    </tr>    
    <tr>
        <td>Néerlandais</td>
        <td>NL.Microsoft</td>
        <td>NL.Lucene</td>  
    </tr>    
    <tr>
        <td>Anglais</td>        
        <td>en.Microsoft</td>
        <td>en.Lucene</td>      
    </tr>
    <tr>
        <td>Estonien</td>
        <td>et.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Finnois</td>
        <td>Fi.Microsoft</td>
        <td>Fi.Lucene</td>      
    </tr>    
    <tr>
        <td>Français</td>
        <td>fr.Microsoft</td>
        <td>fr.Lucene</td>      
    </tr>
    <tr>
        <td>Galicien</td>
        <td></td>
        <td>GL.Lucene</td>      
    </tr>
    <tr>
        <td>Allemand</td>
        <td>de.Microsoft</td>
        <td>de.Lucene</td>      
    </tr>
    <tr>
        <td>Grec</td>
        <td>EL.Microsoft</td>
        <td>EL.Lucene</td>      
    </tr>
    <tr>
        <td>Gujarâtî</td>
        <td>gu.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hébreu</td>
        <td>HE.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hindi</td>
        <td>Hi.Microsoft</td>
        <td>Hi.Lucene</td>      
    </tr>
    <tr>
        <td>Hongrois</td>      
        <td>HU.Microsoft</td>
        <td>HU.Lucene</td>
    </tr>
    <tr>
        <td>Islandais</td>
        <td>is.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Indonésien (Bahasa)</td>
        <td>ID.Microsoft</td>
        <td>ID.Lucene</td>      
    </tr>
    <tr>
        <td>Irlandais</td>
        <td></td>
        <td>GA.Lucene</td>
    </tr>
    <tr>
        <td>Italien</td>
        <td>IT.Microsoft</td>
        <td>IT.Lucene</td>      
    </tr>
    <tr>
        <td>Japonais</td>
        <td>ja.Microsoft</td>
        <td>ja.Lucene</td>
        
    </tr>
    <tr>
        <td>Canara</td>
        <td>KA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Coréen</td>
        <td>Ko.Microsoft</td>
        <td>Ko.Lucene</td>
    </tr>
    <tr>
        <td>Letton</td>        
        <td>LV.Microsoft</td>
        <td>LV.Lucene</td>  
    </tr>
    <tr>
        <td>Lituanien</td>
        <td>lt.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malayalam</td>
        <td>ml.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malais (Latin)</td>
        <td>MS.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Marathi</td>
        <td>MR.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Norvégien</td>
        <td>Nb.Microsoft</td>
        <td>no.Lucene</td>      
    </tr>
    <tr>
        <td>Perse</td>
        <td></td>
        <td>FA.Lucene</td>      
    </tr>
    <tr>
        <td>Polonais</td>
        <td>PL.Microsoft</td>
        <td>PL.Lucene</td>      
    </tr>
    <tr>
        <td>Portugais (Brésil)</td>
        <td>pt-Br.microsoft</td>
        <td>pt-Br.lucene</td>       
    </tr>
    <tr>
        <td>Portugais (Portugal)</td>
        <td>pt-Pt.microsoft</td>        
        <td>pt-Pt.lucene</td>
    </tr>
    <tr>
        <td>Punjabi</td>
        <td>PA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Roumain</td>
        <td>RO.Microsoft</td>
        <td>RO.Lucene</td>
    </tr>
    <tr>
        <td>Russe</td>
        <td>RU.Microsoft</td>
        <td>RU.Lucene</td>  
    </tr>
    <tr>
        <td>Serbe (cyrillique)</td>
        <td>SR-cyrillic.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Serbe (Latin)</td>
        <td>SR-latin.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Slovaque</td>
        <td>SK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Slovène</td>
        <td>sl.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Espagnol</td>
        <td>es.Microsoft</td>
        <td>es.Lucene</td>
    </tr>
    <tr>
        <td>Suédois</td>
        <td>SV.Microsoft</td>
        <td>SV.Lucene</td>
    </tr>

    <tr>
        <td>Tamil</td>
        <td>TA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Telugu</td>
        <td>te.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Thaï</td>
        <td>th.Microsoft</td>
        <td>th.Lucene</td>
    </tr>
    <tr>
        <td>Turc</td>
        <td>TR.Microsoft</td>
        <td>TR.Lucene</td>      
    </tr>
    <tr>
        <td>Ukrainien</td>
        <td>UK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Urdu</td>
        <td>Your.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Vietnamien</td>
        <td>VI.Microsoft</td>
        <td></td>
    </tr>
    <td colspan="3">En outre recherche d’Azure fournit des configurations d’analyseur d’indépendant du langage</td>
    <tr>
        <td>ASCII standard de pliage</td>
        <td>standardasciifolding.Lucene</td>
        <td>
        <ul>
            <li>Segmentation de texte Unicode (Générateur de jetons de Standard)</li>
            <li>Filtre de pliage ASCII - convertit les caractères Unicode qui n’appartiennent pas à l’ensemble des 127 premiers caractères ASCII en leurs équivalents ASCII. Cela est utile pour supprimer des signes diacritiques.</li>
        </ul>
        </td>
    </tr>
</table>

Tous les analyseurs avec noms annotées avec <i>lucene</i> sont alimentés par des [analyseurs de langage d’Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Vous trouverez plus d’informations sur ASCII pliage filtre [ici](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Suggesters**

A `suggester` définit les champs dans un index sont utilisés pour prendre en charge la saisie semi-automatique dans les recherches. En règle générale les chaînes de recherche partielle sont envoyés à l' [API de Suggestions](#Suggestions) pendant que l’utilisateur tape une requête de recherche, l’API retourne un ensemble d’expressions suggérées. Un suggester que vous définissez dans l’index détermine les champs utilisés pour générer les termes de recherche anticipée. Pour plus d’informations, consultez [Suggesters](#Suggesters) .

**Profils de score**

A `scoringProfile` définit le score des comportements personnalisés qui vous permettent d’influencer les éléments s’affichent plus dans les résultats de recherche. Profils de score sont constituées des fonctions et des poids de champ. Pour les utiliser, vous spécifiez un profil par un nom dans la chaîne de requête.

Profil de score par défaut fonctionne en arrière-plan pour calculer un score pour chaque élément dans un jeu de résultats de recherche. Vous pouvez utiliser interne, sans nom de profil d’évaluation. Vous pouvez également définir `defaultScoringProfile` pour utiliser un profil personnalisé par défaut, appelé chaque fois qu’un profil personnalisé n’est pas spécifié dans la chaîne de requête.

Pour plus d’informations, reportez-vous à la section [Ajouter des profils score à un index de recherche (recherche de Azure Service API REST)](search-api-scoring-profiles-2015-02-28-preview.md) .

**Options de CORS**

Javascript côté client ne peut pas appeler les API par défaut car le navigateur empêche toutes les demandes d’origine croisée. Activer les CORS (partage des ressources Cross-Origin) en définissant le `corsOptions` attribut pour autoriser les requêtes d’origine croisée à votre index. Remarque Cette requête uniquement prise en charge des API CORS pour des raisons de sécurité. Les options suivantes peuvent être définies pour CORS :

- `allowedOrigins`(obligatoire) : il s’agit d’une liste des origines qui auront accès à l’index. Cela signifie que n’importe quel code Javascript provenant de ces origines pourront interroger l’index (en supposant qu’il fournit la clé API). Chaque origine est en général de l’écran `protocol://fully-qualified-domain-name:port` bien que le port est souvent omis. Consultez [cet article](http://go.microsoft.com/fwlink/?LinkId=330822) pour plus de détails.
 - Si vous souhaitez autoriser l’accès à toutes les origines, incluez `*` sous la forme d’un élément unique dans le `allowedOrigins` tableau. Notez que **pratique pour les services de recherche de production n’est pas conseillée.** Toutefois, il peut être utile pour le développement ou à des fins de débogage.
- `maxAgeInSeconds`(facultatif) : navigateurs utilisent cette valeur pour déterminer la durée (en secondes) en cache les réponses CORS contrôle en amont. Ce doit être un entier non négatif. Plus cette valeur est, meilleures sont les performances seront, mais plus prendre pour CORS les modifications de stratégie prennent effet. Si elle n’est pas définie, une durée par défaut de 5 minutes sera utilisée.

<a name="CreateUpdateIndexExample"></a>
**Exemple de corps de requête**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Réponse**

Pour une demande réussie : « 201 créé ».

Par défaut, le corps de la réponse contient le JSON pour la définition de l’index a été créé. Si le `Prefer` en-tête de requête est définie sur `return=minimal`, le corps de réponse est vide et que le code de statut de réussite sera « 204 sans contenu » au lieu de « 201 créé ». Ceci est vrai que PUT ou POST a été utilisé pour créer l’index.

**Remarques**

Il est actuellement prise en charge limitée pour les mises à jour de schéma index. Les mises à jour de schéma qui nécessiteraient la réindexation telles que la modification des types de champs ne sont pas actuellement pris en charge. Bien que les champs existants ne peuvent pas être modifiées ou supprimées, les nouveaux champs peuvent être ajoutés à un index existant à tout moment. Lorsque vous ajoutez un nouveau champ, tous les documents existants dans l’index auront automatiquement une valeur nulle pour ce champ. Aucun espace de stockage supplémentaire ne sera consommée jusqu'à ce que les nouveaux documents sont ajoutés à l’index.

<a name="Suggesters"></a>
## <a name="suggesters"></a>Suggesters

La fonctionnalité de suggestions de recherche d’Azure est une fonctionnalité de requête de type transfert ou semi-automatique, fournissant une liste de termes de recherche potentiels en réponse à des entrées de chaîne partiel entré dans une zone de recherche. Vous avez probablement remarqué des suggestions de requête lors de l’utilisation de moteurs de recherche web commerciale : en tapant «.NET » dans Bing génère une liste de termes pour « .NET 4.5 », « de.NET Framework 3,5", et ainsi de suite. Lorsque vous utilisez le service de recherche API REST, mise en œuvre des suggestions dans une application Azure recherche personnalisée requiert les éléments suivants :

- Activer les suggestions en ajoutant une construction **suggester** dans votre index, en donnant le nom, le mode de recherche et une liste de champs pour lequel l’option Saisie semi-automatique est appelé. Par exemple, si vous spécifiez « Nom_ville » sous la forme d’un champ de la source, en tapant une chaîne de recherche partielle de « Mer » entraînera « Seattle », « Bord de mer » et « Seatac » (toutes les trois sont des noms de ville réelle) proposé suggestions de requête sous la forme à l’utilisateur.

- Appeler des suggestions par l' [API de Suggestions](#Suggestions) dans votre code d’application. En général les chaînes de recherche partielle sont envoyées au service pendant que l’utilisateur tape une requête de recherche, cette API retourne un ensemble d’expressions suggérées.

Cet article explique comment configurer un **suggester**. Vous devez également examiner l' [API de Suggestions](#Suggestions) pour plus d’informations sur l’utilisation d’un suggester.

**Utilisation de**

`Suggesters`sont créés dans l’index et de travailler mieux lorsqu’il est utilisé pour suggérer des documents spécifiques plutôt que des termes en vrac ou des expressions. Les champs de candidat meilleures sont les titres, les noms et les autres phrases relativement courtes qui peuvent identifier un élément. Moins efficaces sont des champs répétitives, telles que les catégories et les balises, ou très longue tels que les champs de description ou de commentaires.

Dans le cadre de la définition d’index, vous pouvez ajouter un seul suggester à la `suggesters` collection. Les propriétés qui définissent un suggester sont les suivantes :

- `name`: Nom de le suggester. Vous utilisez le nom de le suggester lors de l’appel du `suggest` API.
- `searchMode`: La stratégie utilisée pour rechercher des expressions du candidat. L’unique mode pris en charge actuellement est `analyzingInfixMatching`, qui effectue la mise en correspondance souple des phrases au début ou au milieu des phrases.
- `sourceFields`: Une liste d’un ou plusieurs champs qui sont à l’origine du contenu pour obtenir des suggestions. Seuls les champs de type `Edm.String` et `Collection(Edm.String)` peuvent être sources pour obtenir des suggestions. Seuls les champs qui n’ont pas un analyseur de langage personnalisé défini peuvent être utilisés.

**Exemple de suggester**

Un suggester est la partie de l’index. Seul suggester peut exister dans le `suggesters` collection dans la version actuelle, à côté de la collection fields et `scoringProfiles`.

        {
          "name": "hotels",
          "fields": [
             . . .
           ],
          "suggesters": [
            {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields: ["hotelName", "category"]
            }
          ],
          "scoringProfiles": [
             . . .
          ]
        }

> [AZURE.NOTE]  Si vous avez utilisé la version de présentation publique de recherche d’Azure, `suggesters` remplace une ancienne propriété booléenne (`"suggestions": false`) qui pris en charge uniquement les suggestions de préfixe pour les chaînes courtes (3-25 caractères). Son remplacement, `suggesters`, prise en charge d’infixe qui recherche les termes correspondants au début ou au milieu du contenu du champ, avec une meilleure tolérance d’erreurs dans des chaînes de recherche correspondant. À partir de la version disponible, il s’agit désormais la seule implémentation de l’API de suggestions. L’ancien `suggestions` propriété qui a été introduite dans `api-version=2014-07-31-Preview` continue à fonctionner dans cette version, mais n’est pas opérationnel dans le `2015-02-28` ou une version ultérieure de recherche d’Azure.

<a name="UpdateIndex"></a>
## <a name="update-index"></a>Mettre à jour les Index

Vous pouvez mettre à jour un index existant dans la recherche d’Azure à l’aide d’une requête HTTP PUT. Mises à jour peuvent inclure l’ajout de nouveaux champs au schéma existant, la modification des options de CORS et modifié des profils d’évaluation. Pour plus d’informations, consultez [Ajouter le score de profils](https://msdn.microsoft.com/library/azure/dn798928.aspx) . Vous spécifiez le nom de l’index à jour sur l’URI de requête :

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Important :** Prise en charge des mises à jour de schéma index est limité aux opérations qui ne nécessitent pas la reconstruction de l’index de recherche. Les mises à jour de schéma qui nécessiteraient la réindexation, telles que la modification des types de champs, ne sont actuellement pas pris en charge. Nouveaux champs peuvent être ajoutés à tout moment, bien que les champs existants ne peuvent pas être modifiées ou supprimées. La même remarque vaut pour `suggesters`. Nouveaux champs peuvent être ajoutés à un suggester au moment où les champs sont ajoutés, mais les champs ne peuvent pas être supprimés à partir de `suggesters` et champs existants ne peuvent pas être ajoutés à `suggesters`.

Lorsque vous ajoutez un nouveau champ à un index, tous les documents existants dans l’index auront automatiquement une valeur nulle pour ce champ. Aucun espace de stockage supplémentaire ne sera consommée jusqu'à ce que les nouveaux documents sont ajoutés à l’index.

**Demande**

HTTPS est requis pour toutes les demandes de service. La demande de **Mise à jour de Index** est construite à l’aide de HTTP PUT. Avec l’instruction PUT, le nom de l’index fait partie de l’URL. Si l’index n’existe pas, il est créé. Si l’index existe déjà, il est mis à jour à la nouvelle définition.

Le nom de l’index doit être en minuscule, commencer par une lettre ou un chiffre, ont sans barres obliques ou points et comporter moins de 128 caractères. Après avoir démarré le nom d’index par une lettre ou un chiffre, le reste du nom peut inclure toute lettre, nombre et des tirets, tant que les tirets ne sont pas consécutifs.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs.

- `Content-Type`: Obligatoire. Cette valeur`application/json`
- `api-key`: Obligatoire. Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à votre service. La demande de **Mise à jour de Index** doit comprendre un `api-key` en-tête défini à votre clé admin (et non d’une clé de la requête).

Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

**Syntaxe de corps de requête**

Lors de la mise à jour d’un index existant, le corps doit inclure la définition de schéma d’origine, ainsi que les nouveaux champs que vous ajoutez, ainsi que les profils de score modifiés, suggesters CORS options et, le cas échéant. Si vous ne modifiez pas les options de CORS et le score des profils, vous devez inclure les originaux à partir de la création de l’index. En général, le meilleur modèle à utiliser pour les mises à jour est de récupérer la définition de l’index avec une opération d’obtention, de le modifier, puis mettre à jour avec l’instruction PUT.

La syntaxe de schéma utilisée pour créer un index est reproduite ici par commodité. Pour plus d’informations, voir [Create Index](#CreateIndex) .

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**Réponse**

Pour une demande réussie : « 204 sans contenu ».

Par défaut, le corps de réponse est vide. Toutefois, si le `Prefer` en-tête de requête est définie sur `return=representation`, le corps de la réponse contient le JSON, définition de l’index qui a été mis à jour. Dans ce cas, le code de statut de réussite sera « 200 OK ».

**Mise à jour de la définition de l’index avec les analyseurs personnalisés**

Une fois un analyseur, un générateur de jetons, un jeton filtre ou char est défini, il ne peut pas être modifié. Nouveaux peut être ajoutés à un index que si les `allowIndexDowntime` est défini à true dans la demande de mise à jour d’index : 

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

Notez que cette opération mettra votre index hors connexion au moins quelques secondes, à l’origine de votre indexation et les requêtes à échouer. Disponibilité d’écriture et les performances de l’index peut être compromise pendant plusieurs minutes après la mise à jour de l’index, ou plus long pour l’index de très grande taille.

<a name="ListIndexes"></a>
## <a name="list-indexes"></a>Index de liste

L’opération **d’Index de liste** renvoie une liste des index actuellement dans votre service de recherche d’Azure.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Demande**

HTTPS est requis pour toutes les demandes de service. La demande de **l’Index de la liste** peut être construite à l’aide de la méthode GET.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs.

- `api-key`: Obligatoire. Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à votre service. La demande de **l’Index de la liste** doit inclure un `api-key` définie pour une clé admin (et non d’une clé de la requête).

Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

**Corps de la demande**

Aucun.

**Réponse**

Code d’état : 200 OK est retourné pour une réponse réussie.

Voici un corps de réponse d’exemple :

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Notez que vous pouvez filtrer la réponse à uniquement les propriétés qui que vous intéressent. Par exemple, si vous souhaitez uniquement une liste des noms d’index, utilisez OData `$select` option de requête :

    GET /indexes?api-version=2015-02-28-Preview&$select=name

Dans ce cas, la réponse à partir de l’exemple ci-dessus apparaît comme suit :

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Il s’agit d’une technique utile pour économiser la bande passante si vous disposez d’un grand nombre d’index dans votre service de recherche.

<a name="GetIndex"></a>
## <a name="get-index"></a>Obtenir des Index

L’opération **Obtenir un Index** Obtient la définition d’index de recherche d’Azure.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Demande**

HTTPS est requis pour les demandes de service. La demande **d’Obtenir un Index** peut être construite à l’aide de la méthode GET.

Le [nom de l’index] dans l’URI de requête spécifie l’index à retourner à partir de la collection indexes.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs.

- `api-key`: Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à votre service. La demande **d’Obtenir un Index** doit comporter une `api-key` définie pour une clé admin (et non d’une clé de la requête).

Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

**Corps de la demande**

Aucun.

**Réponse**

Code d’état : 200 OK est retourné pour une réponse réussie.

Consultez l’exemple JSON dans [Création et la mise à jour d’un Index](#CreateUpdateIndexExample) pour obtenir un exemple de la charge utile de réponse.

<a name="DeleteIndex"></a>
## <a name="delete-index"></a>Supprimer des Index

L’opération **Supprimer l’Index** supprime un index et les documents associés à partir de votre service de recherche d’Azure. Vous pouvez obtenir le nom de l’index du tableau de bord de service dans le portail Azure, ou à partir de l’API. Pour plus d’informations, consultez [Index de la liste](#ListIndexes) .

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Demande**

HTTPS est requis pour les demandes de service. La demande de **Supprimer l’Index** peut être construite à l’aide de la méthode DELETE.

Le [nom de l’index] dans l’URI de requête spécifie l’index à supprimer de la collection indexes.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs.

- `api-key`: Obligatoire. Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à l’URL de votre service. La demande de **Supprimer l’Index** doit comprendre un `api-key` en-tête défini à votre clé admin (et non d’une clé de la requête).

Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

**Corps de la demande**

Aucun.

**Réponse**

Code d’état : 204 sans contenu est retourné pour une réponse réussie.

<a name="GetIndexStats"></a>
## <a name="get-index-statistics"></a>Obtenir des statistiques d’Index

L’opération **Obtenir des statistiques d’Index** renvoie d’Azure recherche document pour l’index en cours, ainsi que l’utilisation du stockage.

    GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Statistiques sur la taille du nombre et de stockage de document sont collectés à intervalles de quelques minutes, pas en temps réel. Par conséquent, les statistiques renvoyées par cette API ne peuvent pas refléter modifications causé par les opérations d’indexation récentes.

**Demande**

HTTPS est requis pour toutes les demandes de services. La demande **d’Obtenir des statistiques d’Index** peut être construite à l’aide de la méthode GET.

Le [nom de l’index] dans l’URI de requête indique au service pour renvoyer les statistiques d’index pour l’index spécifié.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .


**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs.

- `api-key`: Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à votre service. La demande **d’Obtenir des statistiques d’Index** doit comprendre un `api-key` définie pour une clé admin (et non d’une clé de la requête).

Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

**Corps de la demande**

Aucun.

**Réponse**

Code d’état : 200 OK est retourné pour une réponse réussie.

Le corps de réponse est dans le format suivant :

    {
      "documentCount": number,
      "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>
## <a name="test-analyzer"></a>Analyseur de test de

L' **API d’analyse** montre comment un analyseur de sauts dans des jetons.

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Demande**

HTTPS est requis pour toutes les demandes de services. La demande de **l’API d’analyse** peut être construite à l’aide de la méthode POST.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .


**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs.

- `api-key`: Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à votre service. La demande de **l’API d’analyse** doit inclure un `api-key` définie pour une clé admin (et non d’une clé de la requête).

Vous devez également le nom de l’index et le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

**Corps de la demande**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

ou

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

Le `analyzer_name`, `tokenizer_name`, `token_filter_name` et `char_filter_name` doivent être les noms valides des analyseurs prédéfinis ou personnalisés, générateurs, jetons filtres et char pour l’index. Pour en savoir que plus sur le processus d’analyse lexicale, voir [analyse de recherche d’Azure](https://aka.ms/azsanalysis).

**Réponse**

Code d’état : 200 OK est retourné pour une réponse réussie.

Le corps de réponse est dans le format suivant :

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**Analyser l’exemple d’API**

**Demande**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**Réponse**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

________________________________________
<a name="DocOps"></a>
## <a name="document-operations"></a>Opérations de document

Recherche d’Azure, un index est stocké dans le nuage et remplie à l’aide de documents JSON que vous téléchargez dans le service. Tous les documents que vous téléchargez comprennent le corpus de vos données de recherche. Les documents contiennent des champs, dont certaines sont sous forme de jetons en termes de recherche qu’ils sont chargés. Le `/docs` segment d’URL dans l’API de recherche Azure représente la collection de documents dans un index. Toutes les opérations exécutées sur la collection telle que le chargement, la fusion, la suppression ou l’interrogation de documents prennent place dans le contexte d’un index unique, donc les URL pour ces opérations démarre toujours avec `/indexes/[index name]/docs` pour le nom d’un index donné.

Votre code d’application doit générer des documents JSON à télécharger pour la recherche d’Azure, ou vous pouvez utiliser un [indexeur](https://msdn.microsoft.com/library/dn946891.aspx) pour charger des documents si la source de données est une base de données de SQL Azure, ou DocumentDB. En règle générale, les index sont remplies à partir d’un unique groupe de données que vous fournissez.

Vous devez planifier sur la présence d’un document pour chaque élément que vous souhaitez rechercher. Une application de location de film peut avoir un document par film, une application de vitrine peut avoir un document par référence, une application de didacticiels en ligne peut avoir un seul document par cours, un cabinet d’études peut avoir un seul document pour chaque document d’éducation dans leur référentiel et ainsi de suite.

Les documents se composent d’un ou plusieurs champs. Les champs peuvent contenir le texte qui est sous forme de jetons par Azure recherche en termes de recherche, ainsi que des non-sous forme de jetons ou les valeurs non textuelles qui peuvent être utilisés dans les profils de score ou de filtres. Les noms, les types de données et les fonctionnalités de recherche prises en charge pour chaque champ sont déterminées par le schéma d’index. L’un des champs dans chaque schéma d’index doit être désigné en tant qu’ID, et chaque document doit avoir une valeur pour le champ d’ID qui identifie de manière unique ce document dans l’index. Tous les autres champs de document sont facultatifs et par défaut à une valeur null si omis. Notez que les valeurs null n’occupent pas d’espace dans l’index de recherche.

Avant que vous pouvez télécharger des documents, vous devez avoir déjà créé l’index sur le service. Pour plus d’informations sur cette première étape, voir [Create Index](#CreateIndex) .

<a name="AddOrUpdateDocuments"></a>
## <a name="add-update-or-delete-documents"></a>Mise à jour, ajouter ou supprimer des Documents

Vous pouvez télécharger, fusionner, fusion ou téléchargement ou supprimer des documents à partir d’un index spécifié, à l’aide de HTTP POST. Un grand nombre de mises à jour, le traitement par lots des documents (documents de 1000 par lot) ou 16 Mo par lot est recommandé.

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Demande**

HTTPS est requis pour toutes les demandes de service. Vous pouvez télécharger, fusionner, fusion ou téléchargement ou supprimer des documents à partir d’un index spécifié, à l’aide de HTTP POST.

L’URI de requête inclut [nom de l’index], spécifiant l’index pour valider des documents. Vous ne pouvez valider qu’un seul index des documents à la fois.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs.

- `Content-Type`: Obligatoire. Cette valeur`application/json`
- `api-key`: Obligatoire. Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à votre service. La demande **d’Ajouter des Documents** doit comprendre un `api-key` en-tête défini à votre clé admin (et non d’une clé de la requête).

Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

**Corps de la demande**

Le corps de la requête contient un ou plusieurs documents à indexer. Les documents sont identifiées par une clé unique. Chaque document est associé à une action : téléchargement, de fusion, de mergeOrUpload ou de suppression. Les demandes de téléchargement doivent inclure les données du document sous la forme d’un ensemble de paires clé/valeur.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [AZURE.NOTE] Clés de document ne peuvent contenir que des lettres, des chiffres, des tirets («- »), des traits de soulignement (« _ ») et les signes égal (« = »). Pour plus d’informations, consultez [les règles d’attribution de noms](https://msdn.microsoft.com/library/azure/dn857353.aspx).

**Actions de document**

- `upload`: Une action de téléchargement est similaire à un « upsert » où sera inséré le document s’il est nouveau et mis à jour/remplacé s’il existe. Notez que tous les champs sont remplacés dans le cas de la mise à jour.
- `merge`: Fusion met à jour un document existant avec les champs spécifiés. Si le document n’existe pas, la fusion échoue. N’importe quel champ que vous spécifiez dans une fusion remplacent le champ existant dans le document. Cela inclut les champs de type `Collection(Edm.String)`. Par exemple, si le document contient un champ « balises » avec la valeur `["budget"]` et que vous exécutez une fusion avec une valeur `["economy", "pool"]` pour les « balises », la valeur finale du champ « balises » sera `["economy", "pool"]`. Il sera **pas** être `["budget", "economy", "pool"]`.
- `mergeOrUpload`: se comporte comme `merge` si un document avec la clé spécifiée existe déjà dans l’index. Si le document n’existe pas qu’il se comporte comme `upload` avec un nouveau document.
- `delete`: Supprimer supprime le document spécifié à partir de l’index. Notez que tous les champs que vous spécifiez dans une `delete` opération autre que le champ clé sera ignorée. Si vous souhaitez supprimer un champ d’un document, utilisez `merge` à la place et simplement la valeur du champ explicitement à `null`.

**Réponse**

Code d’état 200 (OK) est retournée pour une réponse réussie, c'est-à-dire que tous les éléments ont été indexés. Cela est indiqué par le `status` de propriété qui est définie true pour tous les éléments, ainsi que la `statusCode` à la propriété 201 (pour les documents récemment téléchargées), soit 200 (pour les documents fusionnés ou supprimés) :

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

Code d’état 207 (Multi-état) est retourné lorsqu’au moins un article n’a pas été indexé. Les éléments qui n’ont pas été indexés ont le `status` champ a la valeur false. La `errorMessage` et `statusCode` propriétés indique la raison de l’erreur d’indexation :

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

Le tableau suivant décrit les codes d’état différents par document qui peuvent être retournés dans la réponse. Notez que certains indiquent des problèmes avec la demande elle-même, alors que d’autres conditions d’erreur temporaire. Ce dernier que vous devriez réessayer après un certain délai.

<table style="font-size:12">
    <tr>
        <th>Code d’état</th>
        <th>Signification</th>
        <th>Renouvelable</th>
        <th>Notes</th>
    </tr>
    <tr>
        <td>200</td>
        <td>Document a été correctement modifié ou supprimé.</td>
        <td>n/a</td>
        <td>Les opérations de suppression sont <a href="https://en.wikipedia.org/wiki/Idempotence">idempotents</a>. Autrement dit, même si une clé de document n’existe pas dans l’index, essayez d’une opération de suppression avec cette clé entraîne un code de 200 état.</td>
    </tr>
    <tr>
        <td>201</td>
        <td>Document a été créé avec succès.</td>
        <td>n/a</td>
        <td></td>
    </tr>
    <tr>
        <td>400</td>
        <td>Une erreur s’est produite dans le document qui l’a empêché d’en cours d’indexation.</td>
        <td>N°</td>
        <td>Le message d’erreur dans la réponse indique quel est le problème avec le document.</td>
    </tr>
    <tr>
        <td>404</td>
        <td>Le document n’a pas pu être fusionné, car la clé spécifiée n’existe pas dans l’index.</td>
        <td>N°</td>
        <td>Cette erreur ne se produit pas pour les téléchargements, dans la mesure où ils créent de nouveaux documents, et il ne se produit pas pour les suppressions, car ils sont <a href="https://en.wikipedia.org/wiki/Idempotence">idempotents</a>.</td>
    </tr>
    <tr>
        <td>409</td>
        <td>Un conflit de version a été détecté lors de l’indexation d’un document.</td>
        <td>Oui</td>
        <td>Cela peut se produire lorsque vous essayez d’indexer le même document plusieurs fois simultanément.</td>
    </tr>
    <tr>
        <td>422</td>
        <td>L’index est temporairement indisponible, car il a été mis à jour avec l’indicateur 'allowIndexDowntime' à 'true'.</td>
        <td>Oui</td>
        <td></td>
    </tr>
    <tr>
        <td>503</td>
        <td>Votre service de recherche est temporairement indisponible, probablement en raison d’une charge importante.</td>
        <td>Oui</td>
        <td>Votre code doit attendre avant une nouvelle tentative dans ce cas, ou vous risquez de prolonger l’indisponibilité de service.</td>
    </tr>
</table> 

**Remarque**: Si votre code client rencontre fréquemment une réponse 207, il se peut que le système est sous charge. Vous pouvez le vérifier en vérifiant la `statusCode` propriété 503. Si c’est le cas, nous vous recommandons de ***la limitation de requêtes d’indexation***. Dans le cas contraire, si l’indexation de trafic ne perdure, le système a pu démarrer rejette toutes les demandes avec 503 erreur.

Code d’état 429 indique que vous avez dépassé votre quota sur le nombre de documents par index. Vous devez créer un nouvel index ou mise à niveau pour les limites de capacité supérieures.

**Exemple :**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
          "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
          "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
          "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
          "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
          "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
          "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## <a name="search-documents"></a>Rechercher des Documents

Une opération de **recherche** est émise sous la forme d’une demande GET ou POST et spécifie les paramètres qui permettent des critères de sélection des documents correspondants.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quand utiliser POST plutôt que GET**

Lorsque vous utilisez la commande HTTP GET pour appeler l’API de **recherche** , vous devez être conscient que la longueur de l’URL de la demande ne peut pas dépasser 8 Ko. Il s’agit généralement suffisante pour la plupart des applications. Toutefois, certaines applications produisent des requêtes très volumineux ou des expressions de filtre d’OData. Pour ces applications, à l’aide de HTTP POST est un meilleur choix, car elle permet d’agrandir des filtres et des requêtes que GET. Le nombre de termes du contrat ou des clauses dans une requête POST, est le facteur de limitation, pas la taille de la requête brute, depuis la limite de taille de demande POST est d’environ 16 Mo.

> [AZURE.NOTE] Bien que la limite de taille de demande POST est très volumineux, les requêtes de recherche et des expressions de filtre ne peut pas être arbitrairement complexes. Pour plus d’informations sur les limitations de la complexité de requêtes et de filtre de recherche, reportez-vous à la section [syntaxe de requête Lucene](https://msdn.microsoft.com/library/mt589323.aspx) et [syntaxe d’expression de OData](https://msdn.microsoft.com/library/dn798921.aspx) .

**Demande**

HTTPS est requis pour les demandes de service. La demande de **recherche** peut être construite à l’aide des méthodes GET ou POST.

L’URI de requête spécifie l’index à la requête, tous les documents qui correspondent aux paramètres. Les paramètres sont spécifiés dans la chaîne de requête pour les requêtes GET, et dans la demande de corps dans le cas de POST demande.

En tant que meilleure pratique lors de la création des requêtes GET, n’oubliez pas [d’encodage URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) les paramètres de requête spécifiques lors de l’appel de l’API REST directement. Pour les opérations de **recherche** , cela inclut :

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

Codage de l’URL n’est recommandé que dans les paramètres de la requête ci-dessus. Si vous l’encodage URL par inadvertance la chaîne de requête entière (tout ce qui suit la ?), rompt des demandes.

En outre, le codage URL est nécessaire uniquement lors de l’appel de l’API REST directement à l’aide de GET. Aucun codage d’URL n’est nécessaire lors de l’appel de **recherche** à l’aide de POST, ou lors de l’utilisation de la [bibliothèque cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx), qui gère le codage de l’URL pour vous.

<a name="SearchQueryParameters"></a>
**Paramètres de la requête**

**Recherche** accepte plusieurs paramètres qui fournissent des critères de requête et également spécifient le comportement de recherche. Vous fournissez que ces paramètres dans l’URL de la chaîne de requête lors de l’appel de **recherche** via GET et en tant que propriétés JSON dans le corps de la demande lors de l’appel de **recherche** via le POST. La syntaxe de certains paramètres est légèrement différente entre GET et POST. Ces différences sont indiquées le cas échéant ci-dessous :

`search=[string]`(facultatif) - le texte à rechercher. Tous les `searchable` champs de recherche par défaut, sauf si `searchFields` est spécifié. Lors de la recherche `searchable` jetons de champs, le texte de recherche lui-même, afin que plusieurs termes peuvent être séparés par un espace blanc (par exemple : `search=hello world`). Pour faire correspondre n’importe quel terme, utilisez `*` (Cela peut être utile pour les requêtes de filtre booléen). L’omission de ce paramètre a le même effet que la valeur `*`. Pour informations sur la syntaxe de recherche, reportez-vous à la section [Syntaxe de requête Simple](https://msdn.microsoft.com/library/dn798920.aspx) .

  - **Remarque**: les résultats peuvent parfois être surpris lors de l’interrogation sur `searchable` champs. Le Générateur de jetons inclut une logique pour gérer les cas courants pour le texte anglais, tels que les apostrophes, les virgules dans les nombres, etc.. Par exemple, `search=123,456` correspond à un seul terme 123,456 plutôt que les termes individuels 123 et 456, car des virgules sont utilisés comme séparateurs de milliers-pour les grands nombres en anglais. Pour cette raison, nous recommandons d’utiliser des espaces plutôt que des signes de ponctuation pour séparer les termes de la `search` paramètre.

`searchMode=any|all`(facultatif, par défaut, `any`) - si un ou tous les termes de recherche doivent correspondre afin de compter le document comme une correspondance.

`searchFields=[string]`(facultatif) - la liste séparée par des virgules de noms de champs pour rechercher le texte spécifié. Champs de cible doivent être marqués en tant que `searchable`.

`queryType=simple|full`(facultatif, par défaut, `simple`) - lorsque le texte de recherche « simple » est interprétée à l’aide d’un langage de requête simple qui permet à des symboles tels que +, * et « ». Les requêtes sont évaluées sur tous les champs de recherche (ou les champs indiqués dans `searchFields`) dans chaque document par défaut. Lorsque le type de requête est défini sur `full` recherche texte est interprété en utilisant le langage de requête Lucene qui permet des recherches de champ spécifiques et pondérés. Pour des détails sur les syntaxes de recherche, reportez-vous à la section [Syntaxe de requête Simple](https://msdn.microsoft.com/library/dn798920.aspx) et [d’Une syntaxe de requête Lucene](https://msdn.microsoft.com/library/mt589323.aspx) . 
 
> [AZURE.NOTE] Plage de recherche dans le langage de requête n’est pas pris en charge par $filter qui offre des fonctionnalités similaires de Lucene.

`moreLikeThis=[key]`(facultatif) **Important :** Cette fonctionnalité est uniquement disponible dans `2015-02-28-Preview`. Cette option ne peut pas être utilisée dans une requête qui contient le paramètre de recherche de texte, `search=[string]`. Le `moreLikeThis` paramètre trouve tous les documents qui sont similaires pour le document spécifié par la clé de document. Lorsqu’une demande de recherche est effectuée avec des `moreLikeThis`, une liste de termes de recherche est générée en fonction de la fréquence et la rareté de termes dans le document source. Ces termes sont ensuite utilisés pour effectuer la demande. Par défaut, le contenu de tous les `searchable` champs sont pris en compte sauf si `searchFields` est utilisé pour restreindre des champs de recherche.  

`$skip=#`(facultatif) - le nombre de résultats de la recherche à ignorer ; Ne peut pas être supérieur à 100 000. Si vous devez numériser des documents dans l’ordre mais que vous ne pouvez pas utiliser `$skip` en raison de cette limitation, utilisez `$orderby` sur une clé entièrement commandé et `$filter` avec une plage de la requête à la place.

> [AZURE.NOTE] Lors de l’appel de **recherche** à l’aide de POST, ce paramètre est nommé `skip` au lieu de `$skip`.

`$top=#`(facultatif) - le nombre de résultats de recherche pour récupérer. Cela peut être utilisé en association avec `$skip` pour implémenter la pagination côté client des résultats de recherche.

> [AZURE.NOTE] Lors de l’appel de **recherche** à l’aide de POST, ce paramètre est nommé `top` au lieu de `$top`.

`$count=true|false`(facultatif, par défaut, `false`)-Spécifie s’il faut extraire le nombre total de résultats. Ceci est le nombre de tous les documents qui correspondent à la `search` et `$filter` paramètres, en ignorant les `$top` et `$skip`. La valeur `true` peut avoir un impact sur les performances. Notez que le nombre renvoyé est une approximation.

> [AZURE.NOTE] Lors de l’appel de **recherche** à l’aide de POST, ce paramètre est nommé `count` au lieu de `$count`.

`$orderby=[string]`(facultatif) - une liste d’expressions séparées par des virgules de trier les résultats par. Chaque expression peut être un nom de champ ou d’un appel à la `geo.distance()` fonction. Chaque expression peut être suivie par `asc` pour indiquer l’ordre croissant, et `desc` pour indiquer l’ordre décroissant. La valeur par défaut est l’ordre croissant. Attaches, seront subdivisées par les scores de correspondance des documents. Si aucune `$orderby` est spécifié, l’ordre de tri par défaut est décroissant par score de correspondance de document. Il existe une limite de 32 clauses pour `$orderby`.

> [AZURE.NOTE] Lors de l’appel de **recherche** à l’aide de POST, ce paramètre est nommé `orderby` au lieu de `$orderby`.

`$select=[string]`(facultatif) - liste des séparées par des virgules des champs à récupérer. Si non spécifié, tous les champs marqués comme récupérables dans le schéma sont inclus. Vous pouvez demander explicitement de tous les champs en définissant ce paramètre `*`.

> [AZURE.NOTE] Lors de l’appel de **recherche** à l’aide de POST, ce paramètre est nommé `select` au lieu de `$select`.

`facet=[string]`(zéro ou plus) - facette par un champ. Vous pouvez également la chaîne peut contenir des paramètres pour personnaliser le faceting exprimée en virgule `name:value` paires. Les paramètres valides sont les suivants :

- `count`(max nombre de termes de facette ; valeur par défaut est 10). Il n’y a pas de maximum, mais des valeurs plus élevées correspondantes les performances diminuent, surtout si le champ à facettes contient un grand nombre de termes uniques.
  - Par exemple : `facet=category,count:5` Obtient les catégories des cinq premiers dans les résultats de la facette.  
  - **Remarque**: si le `count` paramètre est inférieur au nombre de termes uniques, les résultats ne sont ne peut-être pas précises. Il s’agit à la manière dont les requêtes de faceting sont distribués sur milieu des fragments. Augmentation de la `count` généralement augmente la précision des nombres à terme, mais à un coût de performances.
- `sort`(une des `count` de tri *décroissant* en fonction du nombre, `-count` pour effectuer un tri *dans l’ordre croissant* en fonction du nombre, `value` pour effectuer un tri *croissant* par valeur, ou `-value` tri *décroissant* par valeur)
  - Par exemple : `facet=category,count:3,sort:count` Obtient les catégories de trois principaux dans les résultats de facette dans l’ordre décroissant en fonction du nombre de documents avec chaque nom de la ville. Par exemple, si les trois catégories sont Budget, hôtels et articles de luxe, Budget a 5 accès, Motel a 6 et luxe a 4, puis les compartiments sera dans l’ordre d’hôtels, de Budget, de luxe.
  - Par exemple : `facet=rating,sort:-value` produit des compartiments pour toutes les évaluations possibles, dans l’ordre décroissant par valeur. Par exemple, si les niveaux sont de 1 à 5, les compartiments sont commandés 5, 4, 3, 2, 1, quel que soit le nombre de documents correspondant à chaque niveau.
- `values`(délimitée par canal de numérique ou `Edm.DateTimeOffset` valeurs spécifiant un ensemble dynamique de valeurs d’entrée facette)
  - Par exemple : `facet=baseRate,values:10|20` produit trois compartiments : un pour les taux de base 0 jusqu'à, mais non compris jusqu'à 10, l’autre pour 10 mais ne pas y compris 20 et un pour 20 ou supérieur.
  - Par exemple : `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` produit deux compartiments : un pour les hôtels rénovés avant février 2010 et une pour les hôtels rénovéent février 1er, 2010 ou version ultérieure.
- `interval`(intervalle d’entier supérieur à 0 pour les nombres, ou `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` pour les valeurs d’heure date)
  - Par exemple : `facet=baseRate,interval:100` produit des compartiments en fonction des plages de taux de base de la taille de 100. Par exemple, si le taux de base sont toutes comprises entre 60 $ et 600 $, il y aura compartiments pour 0-100, 100-200, 200-300, 300-400, 400 à 500 et 600-500.
  - Par exemple : `facet=lastRenovationDate,interval:year` produit un compartiment pour chaque année lorsque hôtels ont été rénovés.
- `timeoffset`([+-] HH : mm, [+-] hhmm, ou [+-] hh) `timeoffset` est facultatif. Il peut uniquement être combinée avec la `interval` option et uniquement lorsque appliqué à un champ de type `Edm.DateTimeOffset`. La valeur spécifie le décalage UTC au compte pour lors de la définition des limites de temps.
  - Par exemple : `facet=lastRenovationDate,interval:day,timeoffset:-01:00` utilise la limite de la journée qui commence à 01:00:00 UTC (minuit dans le fuseau horaire cible)
- **Remarque**: `count` et `sort` peuvent être combinées dans la même spécification de facette, mais ils ne peuvent pas être combinés avec `interval` ou `values`, et `interval` et `values` ne peuvent pas être combinées.
- **Remarque**: facettes d’intervalle de date heure sont calculées en fonction de l’heure UTC si `timeoffset` n’est pas spécifié. Par exemple : pour `facet=lastRenovationDate,interval:day`, le jour limite commence à 00:00:00 UTC. 

> [AZURE.NOTE] Lors de l’appel de **recherche** à l’aide de POST, ce paramètre est nommé `facets` au lieu de `facet`. En outre, vous le spécifiez en tant que JSON tableau de chaînes où chaque chaîne est une expression séparée de facette.

`$filter=[string]`(facultatif) - une expression de recherche structurée dans la syntaxe standard d’OData. Pour plus d’informations sur le sous-ensemble de la grammaire d’expression OData qui prend en charge la recherche d’Azure, reportez-vous à la section [Syntaxe d’Expression de OData](#ODataExpressionSyntax) .

> [AZURE.NOTE] Lors de l’appel de **recherche** à l’aide de POST, ce paramètre est nommé `filter` au lieu de `$filter`.

`highlight=[string]`met en surbrillance (facultatif) - un ensemble séparé par des virgules de noms de champs utilisés pour le positionnement. Seulement `searchable` champs peuvent être utilisés pour la mise en surbrillance.

`highlightPreTag=[string]`(facultatif, par défaut, `<em>`) - une chaîne de balise qui ajoute pour atteindre les tons clairs. Doit être défini avec `highlightPostTag`.

> [AZURE.NOTE] Lorsque l’appel de **recherche** à l’aide de GET, les caractères réservés dans l’URL doit être encodés en pourcentage (par exemple, % 23 au lieu de #).

`highlightPostTag=[string]`(facultatif, par défaut, `</em>`)-une balise de chaîne qui ajoute pour atteindre les tons clairs. Doit être défini avec `highlightPreTag`.

> [AZURE.NOTE] Lorsque l’appel de **recherche** à l’aide de GET, les caractères réservés dans l’URL doit être encodés en pourcentage (par exemple, % 23 au lieu de #).

`scoringProfile=[string]`(facultatif) - le nom d’un profil d’évaluation pour évaluer correspondent aux scores pour les documents correspondants pour trier les résultats.

`scoringParameter=[string]`(zéro ou plus) - indique les valeurs pour chaque paramètre défini dans une fonction de notation (par exemple, `referencePointParameter`) en utilisant le format `name-value1,value2,...`.

- Par exemple, si le profil de définition de score définit une fonction avec un paramètre appelé « mylocation » l’option de chaîne de requête est `&scoringParameter=mylocation--122.2,44.8`. Le premier tiret sépare le nom de la liste de valeurs, tandis que le second tiret fait partie de la première valeur (longitude dans cet exemple).
- Pour les paramètres de calcul des points tels que pour la balise AMPLIFICATION qui peut contenir des virgules, vous pouvez échapper à toutes ces valeurs dans la liste à l’aide de guillemets simples. Si les valeurs elles-mêmes contiennent des guillemets simples, vous pouvez y échapper en doublant.
  - Par exemple, si vous avez une balise amplifier le paramètre appelé « mytag » et que vous souhaitez améliorer sur la balise les valeurs « Hello, o ' Brien » et « Smith », la requête serait d’option de chaîne de `&scoringParameter=mytag-'Hello, O''Brien',Smith`. Notez que les guillemets sont uniquement nécessaires pour les valeurs qui contiennent des points-virgules.

> [AZURE.NOTE] Lors de l’appel de **recherche** à l’aide de POST, ce paramètre est nommé `scoringParameters` au lieu de `scoringParameter`. En outre, vous spécifiez en tant que JSON tableau de chaînes où chaque chaîne est distincte `name-values` paire.

`minimumCoverage`(facultatif, par défaut, 100)-un nombre compris entre 0 et 100 qui indique le pourcentage de l’index qui doit être couverte par une requête de recherche dans l’ordre de la requête doivent être déclarées comme étant réussi. Par défaut, la totalité de l’index doit être disponible ou `Search` renvoie le code d’état HTTP 503. Si vous définissez `minimumCoverage` et `Search` réussit, elle sera de retour HTTP 200 et inclure une `@search.coverage` la valeur de la réponse qui indique le pourcentage de l’index qui a été inclus dans la requête.

> [AZURE.NOTE] En définissant ce paramètre à une valeur inférieure à 100 peut être utile pour garantir la disponibilité de recherche même pour les services avec un seul réplica. Toutefois, pas tous les documents correspondants sont garantis être présent dans les résultats de la recherche. Si le rappel de recherche est plus important de votre application à la disponibilité, puis il est préférable de laisser `minimumCoverage` à sa valeur par défaut de 100.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

Remarque : pour cette opération, le `api-version` est spécifié comme paramètre de requête dans l’URL que si vous appelez la **recherche** POST ou GET.

**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs.

- `api-key`: Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à l’URL de votre service. La demande de **recherche** peut spécifier une clé d’administration ou de la clé de requête pour `api-key`.

Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

**Corps de la demande**

Pour l’obtenir : aucun.

Pour valider :

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**Maintien de REPONSES de recherche partielle**

Parfois Azure recherche ne peut pas renvoyer tous les résultats requis dans une seule réponse de la recherche. Cela peut se produire pour différentes raisons, par exemple lorsque la requête demande trop de documents en spécifiant ne pas `$top` ou en spécifiant une valeur pour `$top` qui est trop grande. Dans ce cas, Azure recherche inclura le `@odata.nextLink` annotation dans le corps de la réponse et également `@search.nextPageParameters` s’il s’agissait d’une requête POST. Vous pouvez utiliser les valeurs de ces annotations de formuler une autre demande de recherche afin d’obtenir la partie suivante de la réponse de la recherche. Il s’agit d’une ***continuation*** de la demande de recherche d’origine et les annotations sont généralement appelées des ***jetons de continuation***. Voir [l’exemple ci-dessous](#SearchResponse) pour plus d’informations sur la syntaxe de ces annotations et dans lequel ils apparaissent dans le corps de réponse. 

Les raisons pourquoi Azure recherche peut retourner des jetons de continuation sont spécifiques à l’implémentation et susceptibles d’être modifiées. Les clients robustes doivent toujours être prêts à traiter les cas où les documents moins que prévu sont retournés et un jeton de continuation est fourni pour continuer la récupération des documents. Notez également que vous devez utiliser la même méthode HTTP en tant que la demande d’origine afin de pouvoir pour continuer. Par exemple, si vous avez envoyé une demande GET, toutes les demandes de continuation vous envoyez doivent également utiliser GET (et de la même manière pour valider).

<a name="SearchResponse"></a>
**Réponse**

Code d’état : 200 OK est retourné pour une réponse réussie.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**Exemples :**

Vous trouverez des exemples supplémentaires sur la page de la [Syntaxe de l’Expression de recherche d’Azure OData](https://msdn.microsoft.com/library/azure/dn798921.aspx) .

1)  L’Index de recherche triés par ordre décroissant par date.


    GET /indexes/hotels/docs ? recherche = * & $orderby = lastRenovationDate desc & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « * », « orderby » : « lastRenovationDate desc »}

2)  Dans une recherche à facettes, recherchez dans l’index et de récupérer des facettes pour les catégories, évaluation, balises, ainsi que les éléments avec baseRate dans des plages spécifiques :


    GET /indexes/hotels/docs ? recherche = test & facette = catégorie & facette = indice de & facette = balises & facette = baseRate, valeurs : 80 | 150 | 220 & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « test », « facettes » : [« catégorie », « évaluation », « balises », « baseRate, valeurs : 80 | 150 | 220"]}

3)  À l’aide d’un filtre, de limiter les résultats de la requête à facettes précédent une fois que l’utilisateur clique sur note 3 et la catégorie « Hôtels » :


    GET /indexes/hotels/docs ? recherche = test & facette = balises & facette = baseRate, valeurs : 80 | 150 | 220 & $filter = eq de niveau 3 et catégorie eq 'Motel' & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « test », « facettes » : [« balises », « baseRate, valeurs : 80 | 150 | 220"], « filtre » : « eq de niveau 3 et category eq 'Motel' »}

4) Dans une recherche à facettes, définir une limite supérieure sur les termes uniques renvoyés dans une requête. La valeur par défaut est 10, mais vous pouvez augmenter ou diminuer cette valeur à l’aide de la `count` paramètre sur le `facet` attribut :


    GET /indexes/hotels/docs ? recherche = test & facette = ville, count : 5 & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « test », « facettes » : [« ville, count : 5 »]}

5)  Rechercher de l’Index dans des domaines spécifiques. Par exemple, il s’agit d’un champ spécifique à la langue :


    GET /indexes/hotels/docs ? recherche = hôtel & searchFields = description_fr & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « hôtel », « searchFields » : « description_fr »}

6) Rechercher l’Index sur plusieurs champs. Par exemple, vous pouvez stocker et interroger des champs de recherche en plusieurs langues, toutes dans le même index.  Si les descriptions anglais et Français coexister dans le même document, vous pouvez retourner tout ou partie de la requête :


    GET /indexes/hotels/docs ? recherche = hotel & searchFields = description, description_fr & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « hôtel », « searchFields » : « description, description_fr »}

Notez que vous ne pouvez interroger qu’un seul index à la fois. Ne créez pas plusieurs index pour chaque langue, sauf si vous prévoyez d’interroger un à la fois.

7)  Pagination - obtenir la 1ère page d’éléments (taille de page est de 10) :


    GET /indexes/hotels/docs ? recherche = * & $skip = 0 & $top = 10 & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « * », « skip » : 0, « top » : 10}

8)  Pagination - obtenir la 2e page d’éléments (taille de page est de 10) :


    GET /indexes/hotels/docs ? recherche = * & $skip = 10 & $top = 10 & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « * », « skip » : 10, « top » : 10}

9)  Récupérer un ensemble spécifique de champs :


    GET /indexes/hotels/docs ? recherche = * & $select = hotelName, description et version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « * », « select » : « hotelName, description »}

10)  Récupérer les documents correspondant à une expression de filtre spécifique :


    GET /indexes/hotels/docs ? $filter = (baseRate ge 60 et baseRate lt 300) ou eq hotelName « Séjour fantaisie » & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« filter » : "(baseRate ge 60 et baseRate lt 300) ou hotelName eq 'Séjour fantaisie' »}

11) Rechercher les fragments d’index et de retour avec des points d’accès


    GET /indexes/hotels/docs ? recherche quelque chose = & highlight = description & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « quelque chose », « mettre en surbrillance » : « description »}

12) Recherchez dans l’index et renvoyer les documents classés du plus proche de s ' éloignent une référence emplacement


    GET /indexes/hotels/docs ? recherche quelque chose = & $orderby=geo.distance (emplacement, geography'POINT(-122.12315 47.88121)') & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « quelque chose », « orderby » : « geo.distance (emplacement, geography'POINT(-122.12315 47.88121)') »}

13) Recherchez dans l’index en supposant un score profil appelé « geo » avec deux fonctions de score de distance, une définition d’un paramètre appelé « currentLocation » et une définition d’un paramètre appelé « lastLocation »


    GET /indexes/hotels/docs ? recherche quelque chose = & scoringProfile = géo & scoringParameter = currentLocation : 122.123,44.77233 & scoringParameter = lastLocation : 121.499,44.2113 & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « chaîne », « scoringProfile » : « geo », « scoringParameters » : [« currentLocation--122.123,44.77233 », « lastLocation--121.499,44.2113 »]}

14) Recherche de documents dans l’index à l’aide de la [syntaxe de requête simple](https://msdn.microsoft.com/library/dn798920.aspx). Cette requête renvoie les hôtels où les champs de recherche contiennent les termes « confort » et les « emplacement » mais pas « hôtels » :


    GET /indexes/hotels/docs ? recherche = confort + emplacement-hôtels & searchMode = all & version de l’api = 2015-02-28-aperçu

    POST /indexes/hotels/docs/search ?-version de l’api = 2015-02-28-aperçu {« recherche » : « confort + emplacement-hôtels », « searchMode » : « tout »}

Notez l’utilisation de `searchMode=all` ci-dessus. Ce paramètre remplace la valeur par défaut de `searchMode=any`, afin d’assurer que `-motel` signifie « Et non » et non « Ou pas ». Sans `searchMode=all`, vous obtenez « Ou pas » qui se développe et non restreint les résultats de la recherche, et peut être intuitif pour certains utilisateurs.

15) Recherche de documents dans l’index à l’aide de la [syntaxe de requête lucene](https://msdn.microsoft.com/library/mt589323.aspx). Cette requête renvoie les hôtels où le champ de catégorie contient le terme « budget » et de tous les champs de recherche contenant l’expression « récemment rénovée ». Les documents contenant l’expression « récemment rénovée » sont classées par ordre croissant par la valeur de boost de terme (3)

    GET /indexes/hotels/docs?search = catégorie : budget et \"récemment rénové\"^ 3 & searchMode = all & version de l’api = 2015-02-28-Aperçu & querytype = complet

    POST /indexes/hotels/docs/search?api-version = 2015-02-28-aperçu {« recherche » : « catégorie : budget et \"récemment rénové\"^ 3", « queryType » : « full », « searchMode » : « tout »}

<a name="LookupAPI"></a>
## <a name="lookup-document"></a>Recherche Document

L’opération de **Recherche de Document** récupère un document de recherche d’Azure. Cela est utile lorsqu’un utilisateur clique sur un résultat de recherche spécifiques, et que vous souhaitez rechercher des détails spécifiques sur ce document.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Demande**

HTTPS est requis pour les demandes de service. La demande de **Document de recherche** peut être construite comme suit.

    GET /indexes/[index name]/docs/key?[query parameters]

Également, vous pouvez utiliser la syntaxe OData traditionnelle pour la recherche de clé :

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

L’URI de requête inclut un [nom de l’index] et [clé], spécifiant le document à récupérer à partir de l’index. Vous ne pouvez disposer que d’un seul document à la fois. Utilisez la **recherche** pour obtenir plusieurs documents dans une seule demande.

**Paramètres de la requête**

`$select=[string]`(facultatif) - liste des séparées par des virgules des champs à récupérer. Si non spécifié ou la valeur `*`, tous les champs marqués comme récupérables dans le schéma sont incluses dans la projection.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

Remarque : pour cette opération, le `api-version` est spécifié comme paramètre de requête.

**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs.

- `api-key`: Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à l’URL de votre service. La demande de **Recherche de Document** peut spécifier une clé d’administration ou de la clé de requête pour `api-key`.

Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

**Corps de la demande**

Aucun.

**Réponse**

Code d’état : 200 OK est retourné pour une réponse réussie.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Exemple**

Recherche le document qui contient la clé '2'

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Recherche le document qui contient la clé '3' à l’aide de la syntaxe d’OData :

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
## <a name="count-documents"></a>Nombre de Documents

L’opération de **Nombre Documents** récupère le nombre de documents dans un index de recherche. Le `$count` syntaxe fait partie du protocole OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Demande**

HTTPS est requis pour les demandes de service. La **Nombre de Documents** de demande peut être construite à l’aide de la méthode GET.

Le [nom de l’index] dans l’URI de requête indique au service pour renvoyer le nombre de tous les éléments dans la collection de documents de l’index spécifié.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs.

- `Accept`: Cette valeur doit être définie sur `text/plain`.
- `api-key`: Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à l’URL de votre service. La demande du **Nombre de Documents** peut spécifier une clé d’administration ou de la clé de requête pour `api-key`.

Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

**Corps de la demande**

Aucun.

**Réponse**

Code d’état : 200 OK est retourné pour une réponse réussie.

Le corps de la réponse contient la valeur du compteur sous la forme d’un entier mis en forme en texte brut.

<a name="Suggestions"></a>
## <a name="suggestions"></a>Suggestions

L’opération de **Suggestions** récupère des suggestions basées sur la saisie de la recherche partielle. Il est généralement utilisé dans les zones de recherche pour fournir des suggestions de saisie semi-automatique que les utilisateurs entrent les termes de la recherche.

Demandes de suggestions visent suggérant les documents cibles, afin que le texte suggéré peut être répété si plusieurs documents candidat correspond à la même recherche d’entrée. Vous pouvez utiliser `$select` pour récupérer d’autres champs du document (y compris la clé de document) afin que vous sachiez quel document est la source pour chaque suggestion.

Une opération de **Suggestions** est émise sous forme de demande GET ou POST.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quand utiliser POST plutôt que GET**

Lorsque vous utilisez la commande HTTP GET pour appeler les **Suggestions** API, vous devez être conscient que la longueur de l’URL de la demande ne peut pas dépasser 8 Ko. Il s’agit généralement suffisante pour la plupart des applications. Toutefois, certaines applications produisent des requêtes de très grande taille, OData spécifiquement les expressions de filtre. Pour ces applications, à l’aide de HTTP POST est un meilleur choix, car elle permet à des filtres de plus grands que GET. Avec la publication, le nombre de clauses d’un filtre est le facteur de limitation, pas la taille de la chaîne de filtre raw car la limite de taille de demande POST est d’environ 16 Mo.

> [AZURE.NOTE] Bien que la limite de taille de demande POST est très volumineux, les expressions de filtre ne peut pas être arbitrairement complexes. Pour plus d’informations sur les limitations de la complexité de filtre, consultez [syntaxe de l’expression OData](https://msdn.microsoft.com/library/dn798921.aspx) .

**Demande**

HTTPS est requis pour les demandes de service. La demande de **Suggestions** de peut être construite à l’aide des méthodes GET ou POST.

L’URI de la demande spécifie le nom de l’index à la requête. Paramètres, tels que le terme de recherche partiellement d’entrée, sont spécifiés dans la chaîne de requête pour les requêtes GET et dans la demande de demandes de corps pour les valider.

En tant que meilleure pratique lors de la création des requêtes GET, n’oubliez pas [d’encodage URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) les paramètres de requête spécifiques lors de l’appel de l’API REST directement. Pour les opérations de **Suggestions** , cela inclut :

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

Codage de l’URL n’est recommandé que dans les paramètres de la requête ci-dessus. Si vous l’encodage URL par inadvertance la chaîne de requête entière (tout ce qui suit la ?), rompt des demandes.

En outre, le codage URL est nécessaire uniquement lors de l’appel de l’API REST directement à l’aide de GET. Aucun codage d’URL n’est nécessaire lors de l’appel de **propositions** , à l’aide de POST, ou lors de l’utilisation de la [bibliothèque cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx), qui gère le codage de l’URL pour vous.

**Paramètres de la requête**

**Suggestions** accepte plusieurs paramètres qui fournissent des critères de requête et également spécifient le comportement de recherche. Vous fournissez que ces paramètres dans l’URL de la chaîne de requête lors de l’appel de **Suggestions** via GET et, en tant que propriétés JSON dans le corps de la demande lors de l’appel de **Suggestions** via POST. La syntaxe de certains paramètres est légèrement différente entre GET et POST. Ces différences sont indiquées le cas échéant ci-dessous :

`search=[string]`-le texte de recherche à utiliser pour suggérer des requêtes. Doit être au moins 1 caractère et pas plus de 100 caractères.

`highlightPreTag=[string]`(facultatif) - une chaîne de balise qui ajoute pour rechercher des correspondances. Doit être défini avec `highlightPostTag`.

> [AZURE.NOTE] Lorsque l’appel de **propositions** , à l’aide de GET, les caractères réservés dans l’URL doit être encodés en pourcentage (par exemple, % 23 au lieu de #).

`highlightPostTag=[string]`(facultatif) - une chaîne de balise qui ajoute pour rechercher des correspondances. Doit être défini avec `highlightPreTag`.

> [AZURE.NOTE] Lorsque l’appel de **propositions** , à l’aide de GET, les caractères réservés dans l’URL doit être encodés en pourcentage (par exemple, % 23 au lieu de #).

`suggesterName=[string]`-le nom de la suggester comme spécifié dans le `suggesters` collection qui fait partie de la définition d’index. A `suggester` détermine quels champs sont analysés pour les termes de requête suggéré. Pour plus d’informations, consultez [Suggesters](#Suggesters) .

`fuzzy=[boolean]`(facultatif, par défaut = false)-lorsqu’elle est définie sur true cette API trouveront suggestions même s’il existe un caractère substitué ou manquant dans le texte de recherche. Alors que cela fournit une meilleure expérience dans certains scénarios, il a un coût comme suggestion floue les recherches sont plus lentes et consomment plus de ressources de performances.

`searchFields=[string]`(facultatif) - la liste séparée par des virgules de noms de champs pour rechercher le texte spécifié. Champs de cible doivent être activés pour obtenir des suggestions.

`$top=#`(facultatif, par défaut = 5)-le nombre de suggestions à récupérer. Doit être un nombre compris entre 1 et 100.

> [AZURE.NOTE] Lors de l’appel à **propositions** , à l’aide de POST, ce paramètre est nommé `top` au lieu de `$top`.

`$filter=[string]`(facultatif) - une expression qui permet de filtrer les documents pris en compte pour obtenir des suggestions.

> [AZURE.NOTE] Lors de l’appel à **propositions** , à l’aide de POST, ce paramètre est nommé `filter` au lieu de `$filter`.

`$orderby=[string]`(facultatif) - une liste d’expressions séparées par des virgules de trier les résultats par. Chaque expression peut être un nom de champ ou d’un appel à la `geo.distance()` fonction. Chaque expression peut être suivie par `asc` pour indiquer l’ordre croissant, et `desc` pour indiquer l’ordre décroissant. La valeur par défaut est l’ordre croissant. Il existe une limite de 32 clauses pour `$orderby`.

> [AZURE.NOTE] Lors de l’appel à **propositions** , à l’aide de POST, ce paramètre est nommé `orderby` au lieu de `$orderby`.

`$select=[string]`(facultatif) - liste des séparées par des virgules des champs à récupérer. Si non spécifié, que le document clé et suggestion du texte est retourné. Vous pouvez demander de manière explicite tous les champs en définissant ce paramètre `*`.

> [AZURE.NOTE] Lors de l’appel à **propositions** , à l’aide de POST, ce paramètre est nommé `select` au lieu de `$select`.

`minimumCoverage`(facultatif, par défaut, 80)-un nombre compris entre 0 et 100 qui indique le pourcentage de l’index qui doit être couverte par une requête de suggestions dans l’ordre de la requête doivent être déclarées comme étant réussi. Par défaut, au moins 80 % de l’index doit être disponible ou `Suggest` renvoie le code d’état HTTP 503. Si vous définissez `minimumCoverage` et `Suggest` réussit, elle sera de retour HTTP 200 et inclure une `@search.coverage` la valeur de la réponse qui indique le pourcentage de l’index qui a été inclus dans la requête.

> [AZURE.NOTE] En définissant ce paramètre à une valeur inférieure à 100 peut être utile pour garantir la disponibilité de recherche même pour les services avec un seul réplica. Toutefois, pas toutes les suggestions correspondantes sont garanties être présent dans les résultats. Si le rappel est plus important de votre application à la disponibilité, puis il est préférable de ne pas réduire `minimumCoverage` inférieur à leur valeur par défaut de 80.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Gestion des versions du Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

Remarque : pour cette opération, le `api-version` est spécifié comme paramètre de requête dans l’URL que si vous appelez des **Suggestions** POST ou GET.

**En-têtes de demande**

La liste suivante décrit les en-têtes de demande requis et facultatifs

- `api-key`: Le `api-key` est utilisé pour authentifier la demande pour votre service de recherche. Il s’agit d’une valeur de chaîne unique à l’URL de votre service. La demande de **Suggestions** de pouvez spécifier une clé d’admin ou clé de requête en tant que le `api-key`.

Vous devez également le nom du service pour construire l’URL de requête. Vous pouvez obtenir le nom du service et de `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez l’aide de [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) de navigation entre les pages.

**Corps de la demande**

Pour l’obtenir : aucun.

Pour valider :

    {
      "filter": "odata_filter_expression",
      "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
      "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Réponse**

Code d’état : 200 OK est retourné pour une réponse réussie.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Si l’option de projection est utilisée pour récupérer des champs qu’ils sont inclus dans chaque élément du tableau :

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**Exemple**

Récupérer 5 suggestions où l’entrée de recherche partielle est 'lux'

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }
