<properties
    pageTitle="Score de profils (Azure recherche reste API Version 2015-02-28-aperçu de) | Microsoft Azure | Aperçu de recherche Azure API"
    description="Recherche Azure est un service de recherche de nuage hébergé qui prend en charge la réglage de résultats en fonction des profils de notation définis par l’utilisateur."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.author="heidist"
    ms.date="08/29/2016" />

# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>Score de profils (Azure reste API Version 2015-02-28-aperçu de la recherche)

> [AZURE.NOTE] Cet article décrit des profils de notation dans l' [2015-02-28-aperçu](search-api-2015-02-28-preview.md). Il n’existe actuellement aucune différence entre les `2015-02-28` version documentée sur [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) et le `2015-02-28-Preview` version décrite ici, mais nous proposons quand même ce document afin de fournir la couverture du document sur l’intégralité de l’API.

## <a name="overview"></a>Vue d’ensemble

Attribution de scores fait référence au calcul d’un score de recherche pour chaque élément dans les résultats de la recherche. Le score est un indicateur de la pertinence d’un article dans le contexte de l’opération de recherche en cours. Plus le score est élevé, plus les l’élément. Dans les résultats de la recherche, les éléments sont commandé d’élevé à faible, basé sur le score de recherche calculé pour chaque article de rang.

Recherche Azure utilise la valeur par défaut de l’attribution de scores pour calculer un score initial, mais vous pouvez personnaliser le calcul à travers un profil d’évaluation. Profils d’évaluation vous donnent un plus grand contrôle sur le classement des éléments dans les résultats de la recherche. Par exemple, vous pouvez souhaiter augmenter les éléments en fonction de leur potentiel de chiffre d’affaires, promouvoir les éléments les plus récents ou peut-être améliorer les éléments qui ont été en stock trop long.

Un profil d’évaluation est la partie de la définition de l’index, composée de champs, de fonctions et de paramètres.

Pour vous donner une idée de ce à quoi ressemble un profil d’évaluation, l’exemple suivant montre un simple profil nommé « zone géographique ». Celui-ci améliore les éléments qui ont le terme de recherche dans les `hotelName` champ. Il utilise également le `distance` fonction de favoriser les éléments qui se trouvent dans les dix kilomètres de l’emplacement actuel. Si quelqu'un effectue une recherche sur le terme « inn » et 'inn' fait partie du nom de l’hôtel, documents incluant des hôtels avec 'inn' apparaissent en hauts dans les résultats de recherche.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

Pour utiliser ce profil de score, votre requête est formulée pour spécifier le profil sur la chaîne de requête. Dans la requête ci-dessous, notez le paramètre de requête `scoringProfile=geo` dans la demande.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

Cette requête effectue une recherche sur le terme « inn » et passe dans l’emplacement en cours. Notez que cette requête contient les autres paramètres, tels que `scoringParameter`. Paramètres de la requête sont décrits dans les [Documents de recherche (API de recherche Azure)](search-api-2015-02-28-preview.md#SearchDocs).

Cliquez sur l' [exemple](#example) pour consulter un exemple plus détaillé d’un profil d’évaluation.

## <a name="what-is-default-scoring"></a>Ce qui est de score par défaut ?

Attribution de scores calcule un score de recherche pour chaque élément dans un jeu de classement de résultats classés. Chaque élément dans un jeu de résultats de recherche est attribué un score de recherche, puis classement le plus élevé au plus bas. Les éléments dont le score le plus élevé sont renvoyées à l’application. Par défaut, les 50 premiers sont renvoyées, mais vous pouvez utiliser la `$top` paramètre pour retourner le nombre d’éléments (jusqu'à 1000 dans une seule réponse) plus petit ou plus grand.

Par défaut, un score de recherche est calculé en fonction des propriétés statistiques des données et la requête. Recherche Azure trouve des documents qui contiennent les termes de recherche dans la chaîne de requête (tout ou partie, selon `searchMode`), favorise les documents qui contiennent de nombreuses instances du terme recherché. Le score de recherche augmente encore plus élevé si le terme est rare sur le corpus de données, mais commune dans le document. La base de cette approche à la pertinence informatique est appelée TF-IDF ou (fréquence de document de l’inverse de la fréquence de terme).

Résultats en supposant qu’il n’y a aucun tri personnalisé, sont ensuite classées par score de recherche avant d’être renvoyés à l’application appelante. Si `$top` n’est pas spécifié, 50 éléments ayant la plus élevée recherche score est retournés.

Valeurs de résultat de recherche peuvent être répétées tout au long d’un jeu de résultats. Par exemple, peut-être de 10 éléments dont le score de 1.2, 20 articles avec un score de 1.0 et 20 articles avec un score de 0,5. Lorsque plusieurs demandes d’accès ont le même score de recherche, le classement des mêmes éléments évaluées n’est pas défini et n’est pas stable. Exécuter à nouveau la requête et vous pouvez voir les éléments de position de décalage. Étant donné deux éléments avec un score identique, aucune garantie n’est celui qui apparaît en premier.

## <a name="when-to-use-custom-scoring"></a>Quand utiliser une notation personnalisée

Vous devez créer un ou plusieurs profils de score lorsque la valeur par défaut de comportement de classement ne passez suffisamment loin en objectifs de votre entreprise. Par exemple, vous pouvez décider de pertinence de la recherche doit de préférence les éléments nouvellement ajoutés. De même, vous pouvez avoir un champ qui contient la marge, ou tout autre champ indiquant le potentiel de revenus. AMPLIFICATION d’accès qui offrent des avantages pour votre entreprise, un facteur important dans le choix d’utiliser les profils d’évaluation peut être.

Basée sur la pertinence de classement est également implémentée par profils de score. Prendre en compte les pages de résultats de recherche que vous avez utilisé dans le passé qui vous permettent de trier par prix, par date, classement ou pertinence. Recherche d’Azure, profils de notation lecteur l’option « pertinence ». La définition de la pertinence est contrôlée par vous, basée sur les objectifs de l’entreprise et le type de l’expérience de recherche que vous souhaitez diffuser.

<a name="example"></a>
## <a name="example"></a>Exemple

Comme indiqué, score personnalisé est implémenté par le biais de profils définis dans un schéma d’index de la notation.

Cet exemple montre le schéma d’un index avec deux profils d’évaluation (`boostGenre`, `newAndHighlyRated`). Toute requête par rapport à cet index qui inclut un profil comme un paramètre de requête utilise le profil pour évaluer le score du jeu de résultats.

[Essayez cet exemple](search-get-started-scoring-profiles.md).

    {
      "name": "musicstoreindex",
      "fields": [
        { "name": "key", "type": "Edm.String", "key": true },
        { "name": "albumTitle", "type": "Edm.String" },
        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
        { "name": "genre", "type": "Edm.String" },
        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
        { "name": "artistName", "type": "Edm.String" },
        { "name": "orderableOnline", "type": "Edm.Boolean" },
        { "name": "rating", "type": "Edm.Int32" },
        { "name": "tags", "type": "Collection(Edm.String)" },
        { "name": "price", "type": "Edm.Double", "filterable": false },
        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
        { "name": "inventory", "type": "Edm.Int32" },
        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
      ],
      "scoringProfiles": [
        {
          "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
        },
        {
          "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>Flux de travail

Pour implémenter un comportement de score personnalisé, ajouter un profil d’évaluation dans le schéma qui définit l’index. Vous pouvez posséder plusieurs profils de notation dans un index, mais vous ne pouvez spécifier qu’un seul profil au moment dans n’importe quelle requête donnée.

Démarrer avec le [modèle](#bkmk_template) fourni dans cette rubrique.

Indiquez un nom. Profils de score sont facultatifs, mais si vous en ajoutez un, le nom est obligatoire. Veillez à respecter les conventions d’affectation de noms pour les champs (commence par une lettre, permet d’éviter les mots réservés et les caractères spéciaux). Pour plus d’informations, reportez-vous à la section [Règles d’appellation](http://msdn.microsoft.com/library/azure/dn857353.aspx) .

Le corps du profil d’évaluation est construit à partir des fonctions et des champs pondérées.

### <a name="weights"></a>Poids ###

Le `weights` propriétés d’un profil d’évaluation spécifie les paires nom-valeur que vous attribuez un poids relatif à un champ. Dans l' [exemple](#example), les champs albumTitle, le genre et artistName sont amplifié 1.5, 5 et 2, respectivement. Pourquoi est genre amplifié donc beaucoup plus élevée que les autres ? Si la recherche est effectuée sur des données est quelque peu homogènes (comme c’est le cas avec 'genre' dans le `musicstoreindex`), vous pouvez avoir besoin d’une plus grande variation dans la pondération relative. Par exemple, dans le `musicstoreindex`, « rock » s’affiche à la fois un genre et dans les descriptions du genre de manière identique exprimée. Si vous souhaitez que le genre compensent la description du genre, le champ de genre devra bien une pondération relative est importante.

### <a name="functions"></a>Fonctions ###

Les fonctions sont utilisées lors des calculs supplémentaires sont nécessaires pour des contextes spécifiques. Types de fonctions valides sont `freshness`, `magnitude`, `distance` et `tag`. Chaque fonction a des paramètres qui sont uniques à elle.

  - `freshness`doit être utilisée lorsque vous souhaitez améliorer par la nouvel ou l’ancien un élément. Cette fonction peut uniquement être utilisée avec les champs datetime (`Edm.DataTimeOffset`). Remarque le `boostingDuration` attribut est utilisé uniquement avec la fonction d’actualisation.
  - `magnitude`doit être utilisée lorsque vous souhaitez améliorer en fonction de la hauteur ou de faible est d’une valeur numérique. Les scénarios qui appellent cette fonction incluent l’amplification par marge, prix le plus élevé, prix le plus bas ou un nombre de téléchargements. Vous pouvez inverser la plage, d’élevé à faible, si vous souhaitez que le modèle inverse (par exemple, pour les éléments de boost chers supérieur à un prix plus élevé d’éléments). Étant donné une fourchette de prix de 100 $ à $1, vous devez définir `boostingRangeStart` à 100 et `boostingRangeEnd` à 1 pour améliorer les articles chers. Cette fonction peut être utilisée uniquement avec les champs entier et double.
  - `distance`doit être utilisée lorsque vous souhaitez augmenter de proximité ou l’emplacement géographique. Cette fonction peut uniquement être utilisée avec `Edm.GeographyPoint` champs.
  - `tag`doit être utilisée lorsque vous souhaitez augmenter de balises en commun entre les documents et les requêtes de recherche. Cette fonction peut uniquement être utilisée avec `Edm.String` et `Collection(Edm.String)` champs.
  
#### <a name="rules-for-using-functions"></a>Règles d’utilisation des fonctions ####

  - Type de fonction (fraîcheur, amplitude, distance, balise) doit être en minuscule.
  - Fonctions ne peuvent pas inclure des valeurs null ou vides. En particulier, si vous incluez fieldname, vous devez définir une valeur.
  - Fonctions peuvent être appliquées uniquement aux champs filtrables. Pour plus d’informations sur les champs filtrables, voir [Create Index](search-api-2015-02-28-preview.md#CreateIndex) .
  - Fonctions peuvent uniquement être appliquées aux champs qui sont définis dans la collection fields d’un objet index.

Une fois que l’index est défini, générer l’index en téléchargeant le schéma d’index, suivi de documents. Pour plus d’informations sur ces opérations, voir [Create Index](search-api-2015-02-28-preview.md#CreateIndex) et [Ajouter ou mettre à jour les Documents](search-api-2015-02-28-preview.md#AddOrUpdateDocuments) . Une fois l’index créé, vous devez avoir un profil d’évaluation fonctionnel qui fonctionne avec les données de votre recherche.

<a name="bkmk_template"></a>
## <a name="template"></a>Modèle
Cette section explique la syntaxe et le modèle de score de profils. Reportez-vous à la [référence de l’attribut Index](#bkmk_indexref) , dans la section suivante pour obtenir des descriptions des attributs.

    ...
    "scoringProfiles": [
      {
        "name": "name of scoring profile",
        "text": (optional, only applies to searchable fields) {
          "weights": {
            "searchable_field_name": relative_weight_value (positive #'s),
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
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>
## <a name="scoring-profile-property-reference"></a>Référence des propriétés de profil de score

**Remarque** Une fonction de notation peut uniquement être appliquée aux champs filtrables.

| Propriété | Description |
|----------|-------------|
| `name`   | Obligatoire. C’est le nom du profil d’évaluation. Il suit les mêmes conventions d’affectation de noms d’un champ. Il doit commencer par une lettre, ne peut pas contenir de points, deux-points ou @ des symboles et ne peut pas démarrer avec la phrase « azureSearch » (sensible à la casse). |
| `text` | Contient la propriété de poids. |
| `weights` | Facultatif. Une paire nom-valeur qui spécifie un nom de champ et le poids relatif. Poids relatif doit être un nombre à virgule flottante ou un entier positif. Vous pouvez spécifier le nom du champ sans un poids correspondant. Poids sont utilisés pour indiquer l’importance d’un champ par rapport à un autre. |
| `functions` | Facultatif. Notez qu’une fonction de notation seulement peut être appliquée qu’aux champs filtrables. |
| `type` | Obligatoire pour les fonctions de score. Indique le type de fonction à utiliser. Les valeurs valides sont `magnitude`, `freshness`, `distance` et `tag`. Vous pouvez inclure plus d’une fonction dans chaque profil de score. Le nom de la fonction doit être en minuscule. |
| `boost` | Obligatoire pour les fonctions de score. Un nombre positif, utilisé comme multiplicateur de score brut. Il ne peut pas être égal à 1. |
| `fieldName` | Obligatoire pour les fonctions de score. Une fonction de notation peut uniquement être appliquée aux champs qui font partie de la collection de champs de l’index, et qui sont filtrables. De plus, chaque type de fonction introduit des restrictions supplémentaires (fraîcheur est utilisé avec les champs datetime, amplitude avec les champs integer ou double, la distance et les champs emplacement et balise avec chaîne ou les champs de chaîne de collection). Vous ne pouvez spécifier qu’un seul champ par la définition de fonction. Par exemple, pour utiliser l’ordre de grandeur deux fois dans le même profil, vous devez inclure deux grandeur de définitions, un pour chaque champ. |
| `interpolation` | Obligatoire pour les fonctions de score. Définit la pente dont le score de renforcement augmente à partir du début de la plage à la fin de la plage. Les valeurs valides sont `linear` (par défaut), `constant`, `quadratic`, et `logarithmic`. Pour plus d’informations, consultez [définir les interpolations](#bkmk_interpolation) . |
| `magnitude` | L’amplitude de fonction de score est utilisée pour modifier les classements en fonction de la plage de valeurs pour un champ numérique. Des exemples d’utilisation plus courantes de ce sont les suivants :<ul><li>Nombre d ' étoiles : modifier le système d’évaluation basé sur la valeur dans le champ « Étoiles ». Lorsque deux éléments sont pertinentes, s’affiche tout d’abord l’élément avec le contrôle d’accès supérieur.</li><li>Marge : Lorsque deux documents sont pertinentes, un détaillant peut souhaiter améliorer les documents qui ont tout d’abord des marges plus élevées.</li><li>Cliquez sur nombres : pour les applications qui effectuent le suivi cliquez sur via des actions à des produits ou des pages, vous pouvez utiliser la grandeur pour amplifier les éléments qui ont tendance à obtenir le plus de trafic.</li><li>Télécharger des décomptes : pour les applications que téléchargements de piste, la fonction permet à amplitude vous améliorez les éléments qui ont le plus de téléchargements.</li></ul> |
| `magnitude:boostingRangeStart` | Définit la valeur de début de la plage sur laquelle l’amplitude est catégorisée. La valeur doit être un entier ou un nombre à virgule flottante. Pour les évaluations de 1 à 4, ce serait de 1. Marges de plus de 50 %, il s’agit de 50. |
| `magnitude:boostingRangeEnd` | Définit la valeur de fin de la plage sur laquelle l’amplitude est catégorisée. La valeur doit être un entier ou un nombre à virgule flottante. Pour les évaluations de 1 à 4, ce serait 4. |
| `magnitude:constantBoostBeyondRange` | Les valeurs valides sont true ou false (par défaut). Lorsque la valeur true, le gain total continuera de s’appliquer aux documents qui ont une valeur pour le champ cible qui est supérieur à la limite supérieure de la plage. Si la valeur est false, l’augmentation de cette fonction ne seront pas appliquée aux documents ayant une valeur pour le champ cible qui se situe en dehors de la plage. |
| `freshness` | La nouveauté de fonction de score est utilisée pour modifier les scores de classement pour les éléments en fonction des valeurs des champs de DateTimeOffset. Par exemple, un élément avec une date plus récente peut être classé plus élevé que les éléments les plus anciens. (Notez qu’il est également possible d’éléments rangs comme des événements de calendrier avec des dates futures telles que les éléments plus près du présent peuvent être classés supérieure à celle des éléments supplémentaires à l’avenir). Une des extrémités de la plage seront corrigée dans la version actuelle de service, à l’heure actuelle. L’autre extrémité est d’une heure dans le passé, en fonction de la `boostingDuration`. Pour augmenter une plage d’heures à l’avenir utiliser un négatif `boostingDuration`. La vitesse à laquelle l’amplification remplace un maximum et plage minimale est déterminée par l’Interpolation appliqué pour le profil d’évaluation (voir la figure ci-dessous). Pour inverser le facteur d’amplification appliqué, choisissez un facteur de gain inférieur à 1. |
| `freshness:boostingDuration` | Définit une période d’expiration après laquelle l’amplification s’arrête pour un document particulier. Voir [jeu boostingDuration] [bkmk_boostdur #] dans la section suivante pour la syntaxe et des exemples. |
| `distance` | Ferme de la distance de fonction de notation est utilisée pour affecter le score de documents en fonction de la, ou bien ils sont par rapport à un emplacement géographique de référence. L’emplacement de référence est donnée dans le cadre de la requête dans un paramètre (à l’aide de la `scoringParameter` paramètre de requête) sous la forme d’un lon, l’argument de la table des adresses locales. |
| `distance:referencePointParameter` | Un paramètre à passer dans les requêtes à utiliser comme emplacement de référence. scoringParameter est un paramètre de requête. Pour obtenir une description des paramètres de requête, consultez [Rechercher des Documents](search-api-2015-02-28-preview.md#SearchDocs) . |
| `distance:boostingDistance` | Un nombre qui indique la distance en kilomètres à partir de l’emplacement de référence où l’augmentation se termine. |
| `tag` | La balise de fonction de score est utilisée pour affecter le score de base sur les balises dans des documents et des requêtes de recherche de documents. Les documents qui ont des balises avec la requête de recherche va être augmentées. Les balises de la requête de recherche est fourni comme paramètre score dans chaque demande de recherche (à l’aide de la `scoringParameter` paramètre de requête). |
| `tag:tagsParameter` | Un paramètre à passer dans les requêtes pour spécifier les balises pour une demande particulière. `scoringParameter`est un paramètre de requête. Pour obtenir une description des paramètres de requête, consultez [Rechercher des Documents](search-api-2015-02-28-preview.md#SearchDocs) . |
| `functionAggregation` | Facultatif. S’applique uniquement lorsque les fonctions sont spécifiées. Les valeurs valides sont : `sum` (par défaut), `average`, `minimum`, `maximum`, et `firstMatching`. Un score de recherche est une valeur unique qui est calculée à partir de plusieurs variables, y compris plusieurs fonctions. Cette attributs indique comment les renforcements de toutes les fonctions sont combinées pour former une amélioration globale unique qui est ensuite appliquée à la note de document de base. Le score de base est basé sur la valeur tf-idf calculée à partir du document et la requête de recherche. |
| `defaultScoringProfile` | Lors de l’exécution d’une demande de recherche, si aucun profil de score n’est spécifié, l’attribution de scores par défaut est alors utilisé (tf-idf uniquement). Nom du profil de score par défaut peut être définie ici, à l’origine de la recherche d’Azure à utiliser ce profil si aucun profil spécifique n’est indiquée dans la demande de recherche. |

<a name="bkmk_interpolation"></a>
## <a name="set-interpolations"></a>Interpolations de jeu

Interpolations permettent de définir la pente dont le score de renforcement augmente à partir du début de la plage à la fin de la plage. Les interpolations suivantes peuvent être utilisées :

  - `Linear`: Pour les éléments qui sont dans la plage de max et min, le gain appliqué à l’élément sera effectué à un montant de réduction en permanence. Linéaire est l’interpolation par défaut pour un profil d’évaluation.
  - `Constant`: Pour les éléments qui sont dans le début et la fin de la plage, une augmentation constante s’appliquera le classement des résultats.
  - `Quadratic`: Par rapport à une interpolation linéaire ayant un renforcement de diminuer constamment, quadratique initialement diminue à un rythme plus petit et il approche de la fin de la tranche, il revient ensuite à un intervalle plu importante. Cette option d’interpolation n’est pas autorisée dans les fonctions d’attribution de scores de balise.
  - `Logarithmic`: Par rapport à une interpolation linéaire ayant un renforcement de diminuer constamment, logarithmique initialement diminue au rythme supérieur et puis il approche de la fin de la tranche, il réduit les intervalles beaucoup plus petites. Cette option d’interpolation n’est pas autorisée dans les fonctions d’attribution de scores de balise.

<a name="Figure1"></a>
 ![][1]

<a name="bkmk_boostdur"></a>
## <a name="set-boostingduration"></a>Jeu de boostingDuration

`boostingDuration`est un attribut de la fonction d’actualisation. Il permet de définir une date d’expiration période après laquelle l’amplification s’arrête pour un document particulier. Par exemple, pour augmenter une ligne ou une marque pour une période de promotion de 10 jours, vous devez spécifier la période de 10 jours en tant que « P10D » de ces documents. Ou pour spécifient des événements à venir de la semaine suivante «-P7D ».

`boostingDuration`doit être mis en forme comme une valeur de « dayTimeDuration » XSD (un sous-ensemble restreint d’une valeur de durée ISO 8601). Le modèle pour cela est : `[-]P[nD][T[nH][nM][nS]]`.

Le tableau suivant fournit quelques exemples.

| Durée | boostingDuration |
|----------|------------------|
| 1 jour | « P1D » |
| 2 jours et 12 heures | « P2DT12H » |
| 15 minutes | « PT15M » |
| 30 jours, 5 heures 10 minutes, et 6.334 secondes | « P30DT5H10M6.334S » |

Pour plus d’exemples, consultez [XML Schema : Datatypes (site web de W3.org)](http://www.w3.org/TR/xmlschema11-2/).

**Voir aussi**
[recherche Azure Service reste API](http://msdn.microsoft.com/library/azure/dn798935.aspx) sur MSDN <br/>
[Création d’Index (API de recherche Azure)](http://msdn.microsoft.com/library/azure/dn798941.aspx) sur MSDN<br/>
[Ajouter un profil de score à un index de recherche](http://msdn.microsoft.com/library/azure/dn798928.aspx) sur MSDN<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
