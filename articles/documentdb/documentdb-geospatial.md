<properties 
    pageTitle="Utilisation des données géographique dans Azure DocumentDB | Microsoft Azure" 
    description="Comprendre comment créer, d’indexer et d’interroger des objets spatiaux avec DocumentDB d’Azure." 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/25/2016" 
    ms.author="arramac"/>
    
# <a name="working-with-geospatial-data-in-azure-documentdb"></a>Utilisation des données géographique dans Azure DocumentDB

Cet article est une introduction aux fonctionnalités géographiques dans [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). Après avoir lu cette, vous serez en mesure de répondre aux questions suivantes :

- Comment stocker des données spatiales dans Azure DocumentDB ?
- Comment puis-je interroger des données géographiques dans DocumentDB d’Azure dans SQL et LINQ ?
- Comment activer ou désactiver l’indexation spatiale dans DocumentDB ?

Reportez-vous à ce [projet de Github](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) pour des exemples de code.

## <a name="introduction-to-spatial-data"></a>Présentation des données spatiales

Données spatiales décrivent la position et la forme des objets dans l’espace. Dans la plupart des applications, elles correspondent à des objets sur la terre, c'est-à-dire des données géographique. Données spatiales peuvent être utilisées pour représenter l’emplacement d’une personne, d’un point d’intérêt ou de la limite d’une ville ou un lac. Scénarios d’utilisation courants impliquent souvent des requêtes de proximité, pour, par exemple « rechercher toutes les cafés-restaurants près de mon emplacement actuel ». 

### <a name="geojson"></a>GeoJSON
DocumentDB prend en charge l’indexation et l’interrogation des données géographique qui sont représentées à l’aide de la [spécification de GeoJSON](http://geojson.org/geojson-spec.html). GeoJSON des structures de données sont toujours des objets JSON valides, afin qu’ils puissent être stockées et interrogé à l’aide de DocumentDB sans les outils spécialisés ou les bibliothèques. Les kits de développement DocumentDB fournissent des classes d’assistance et méthodes qui facilitent la manipulation des données spatiales. 

### <a name="points-linestrings-and-polygons"></a>Points, linestrings et polygones
Un **Point** désigne une position unique dans l’espace. Dans les données géographiques, un point représente l’emplacement exact, ce qui peut être une adresse de rue d’une épicerie, une borne, une voiture ou une ville.  Un point est représenté dans la paire GeoJSON (et DocumentDB) à l’aide de ses coordonnées ou de longitude et latitude. Voici un exemple JSON d’un point.

**Points de DocumentDB**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE] La spécification GeoJSON spécifie la longitude premier et latitude deuxième. Comme dans d’autres applications de mappage, le longitude et latitude sont des angles et exprimé en degrés. Les valeurs de longitude sont mesurées à partir du premier méridien et sont comprises entre -180 et 180.0 degrés et latitude valeurs sont mesurées à partir de l’Équateur et entre -90.0 et 90.0 degrés. 
>
> DocumentDB interprète les coordonnées représenté par le système de référence WGS-84. Voir ci-dessous pour plus de détails sur les systèmes de référence des coordonnées.

Cela peut être incorporé dans un document DocumentDB, comme illustré dans cet exemple, d’un profil utilisateur qui contient des données de localisation :

**Utiliser le profil avec l’emplacement stocké dans DocumentDB**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

En plus de points, GeoJSON prend également en charge les polygones et LineStrings. **LineStrings** représentent une série de deux ou plusieurs points dans l’espace et les segments de ligne qui les relient. Dans les données géographiques, linestrings sont généralement utilisées pour représenter les autoroutes ou cours d’eau. Un **Polygone** est une limite de points reliés qui forme un LineString fermé. Polygones sont couramment utilisées pour représenter des formations naturelles comme les lacs ou juridictions politiques telles que les villes et les États. Voici un exemple d’un polygone dans DocumentDB. 

**Polygones dans DocumentDB**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE] La spécification de GeoJSON nécessite que, pour les polygones valides, la dernière paire de coordonnées fournie doit être à l’identique au premier, pour créer une forme fermée.
>
>Points dans un polygone doivent être spécifiés en ordre antihoraire. Un polygone spécifié dans le sens horaire représente l’inverse de la région qu’il contient.

Point, LineString et polygone, GeoJSON spécifie également la représentation pour le regroupement de plusieurs emplacements géographiques, ainsi que comment associer des propriétés arbitraires en tant que **fonctionnalité**de géolocalisation. Étant donné que ces objets sont JSON valide, elles peuvent toutes stockées et traitées dans DocumentDB. Toutefois DocumentDB ne prend en charge l’indexation automatique de points.

### <a name="coordinate-reference-systems"></a>Repères de référence

Étant donné que la forme de la terre est irrégulière, les coordonnées des données géospatiales est représenté dans de nombreux systèmes de coordonnées de référence (SIR), chacun avec ses propres cadres de référence et les unités de mesure. Par exemple, la « National grille de Grande-Bretagne » est un système de référence est très précis pour le Royaume-Uni, mais pas à l’extérieur. 

Les plus populaires CRS en cours d’utilisation est aujourd'hui le système géodésique du monde [WGS-84](http://earth-info.nga.mil/GandG/wgs84/). Les périphériques GPS et nombreux services de mappage, y compris des cartes Google et Bing Maps API utilisent WGS-84. DocumentDB prend en charge l’indexation et l’interrogation des données géographique à l’aide de la CRS WGS-84 uniquement. 

## <a name="creating-documents-with-spatial-data"></a>Création de documents avec les données spatiales
Lorsque vous créez des documents qui contiennent des valeurs de GeoJSON, ils sont automatiquement indexés avec un index spatial conformément à la politique d’indexation de la collection. Si vous travaillez avec un SDK DocumentDB dans un langage typé dynamiquement comme les Python ou Node.js, vous devez créer GeoJSON valide.

**Créer un Document avec des données géographique dans Node.js**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
        // additional code within the callback
    });

Si vous travaillez avec le .NET (ou Java) SDK, vous pouvez utiliser les nouvelles classes de Point et de polygone dans l’espace de noms Microsoft.Azure.Documents.Spatial pour incorporer des informations d’emplacement dans les objets de votre application. Ces classes permettent de simplifier la sérialisation et la désérialisation des données spatiales dans GeoJSON.

**Créer un Document avec des données géographique dans .NET**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Si vous n’avez pas les informations de latitude et de longitude, mais que l’adresses physiques ou le nom de l’emplacement comme ville ou pays, vous pouvez rechercher les coordonnées réelles à l’aide d’un service de géocodage comme Bing Maps reste Services. En savoir plus sur le géocodage de Bing Maps [ici](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Interrogation des types spatiales

Maintenant que nous avons un coup de œil comment insérer des données géographiques, examinons comment interroger ces données à l’aide de DocumentDB à l’aide de SQL et LINQ.

### <a name="spatial-sql-built-in-functions"></a>Fonctions intégrées SQL spatiales
DocumentDB prend en charge les fonctions intégrées de Open Geospatial Consortium (OGC) suivantes pour l’interrogation de géographique. Pour plus d’informations sur l’ensemble des fonctions intégrées dans le langage SQL, reportez-vous à la [DocumentDB de la requête](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Utilisation de</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Renvoie la distance entre les deux expressions du point GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Retourne une expression booléenne indiquant si le point de GeoJSON spécifié dans le premier argument est dans le polygone de GeoJSON dans le deuxième argument.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Retourne une valeur Boolean indiquant si l’expression de point ou de polygone GeoJSON spécifiée est valide.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Renvoie la valeur d’une valeur JSON qui contient une valeur booléenne si l’expression de point ou de polygone GeoJSON spécifiée est valide et si elle est non valide, en outre la raison sous la forme d’une valeur de chaîne.</td>
</tr>
</table>

Fonctions de spatiales peuvent être utilisées pour effectuer des requêtes de proximité par rapport à des données spatiales. Par exemple, voici une requête qui renvoie tous les documents de famille situés à 30 kilomètres de l’emplacement spécifié à l’aide de la fonction intégrée de ST_DISTANCE. 

**Requête**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Résultats**

    [{
      "id": "WakefieldFamily"
    }]

Si vous incluez une indexation spatiale dans votre stratégie d’indexation, puis « requêtes à distance » seront servis efficacement par le biais de l’index. Pour plus d’informations sur l’indexation spatiale, reportez-vous à la section ci-dessous. Si vous n’avez pas un index spatial pour les chemins d’accès spécifiés, vous pouvez toujours effectuer des recherches spatiales en spécifiant `x-ms-documentdb-query-enable-scan` en-tête de requête avec la valeur définie sur « true ». Dans .NET, cela est possible en passant l’argument facultatif **FeedOptions** requêtes avec [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) avec la valeur true. 

ST_WITHIN peut être utilisé pour vérifier si un point se trouve dans un polygone. Polygones sont utilisés pour représenter des limites telles que des codes postaux, les frontières des États ou des formations. À nouveau si vous incluez une indexation spatiale dans votre stratégie d’indexation, puis « dans » requêtes seront servis efficacement par le biais de l’index. 

Arguments de polygone dans la ST_WITHIN peuvent contenir uniquement une seule sonnerie, c'est-à-dire les polygones ne doivent pas contenir de trous. 

**Requête**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Résultats**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Similaire à works de types comment incompatibles dans la requête de DocumentDB, si la valeur de l’emplacement spécifié dans l’argument est incorrecte ou non valide, puis il prend la valeur **non défini** et le document évalué soit ignorée à partir des résultats de la requête. Si votre requête ne renvoie aucun résultat, l’exécution du débogage à la ST_ISVALIDDETAILED pourquoi le type spatail n’est pas valide.     

DocumentDB prend également en charge l’exécution de requêtes inverses, par exemple, vous pouvez indexer des polygones ou des lignes de DocumentDB, puis de requête pour les zones qui contiennent un point spécifié. Ce modèle est généralement utilisé dans logistique pour identifier, par exemple lorsqu’un camion entre ou quitte une zone désignée. 

**Requête**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Résultats**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID et ST_ISVALIDDETAILED peuvent être utilisé pour vérifier si un objet spatial est valide. Par exemple, la requête suivante vérifie la validité d’un point avec une valeur hors limites latitude (-132.8). ST_ISVALID retourne simplement une valeur booléenne et ST_ISVALIDDETAILED renvoie la valeur booléenne et une chaîne qui contient la raison pour laquelle il est non valide.

**Requête**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Résultats**

    [{
      "$1": false
    }]

Ces fonctions peuvent également être utilisées pour valider des polygones. Par exemple, ici nous utilisons ST_ISVALIDDETAILED pour valider un polygone qui n’est pas fermé. 

**Requête**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Résultats**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
### <a name="linq-querying-in-the-net-sdk"></a>LINQ interroge le Kit de développement .NET

Le Kit de développement .NET DocumentDB également les fournisseurs de méthodes de stub `Distance()` et `Within()` pour une utilisation dans des expressions LINQ. Le fournisseur LINQ de DocumentDB se traduit par ces appels de méthode pour les appels de fonction intégrée SQL équivalentes (ST_DISTANCE et ST_WITHIN respectivement). 

Voici un exemple d’une requête LINQ qui trouve tous les documents dans la collection DocumentDB dont la valeur « location » est dans un rayon de 30 kilomètres du point à l’aide de LINQ.

**Requête LINQ de Distance**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

De même, voici une requête pour rechercher tous les documents dont le « emplacement » se trouve dans la zone/polygone spécifié. 

**La requête LINQ pour dans**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Maintenant que nous avons un coup de œil comment interroger des documents à l’aide de LINQ et SQL, examinons comment configurer DocumentDB pour indexation spatiale.

## <a name="indexing"></a>L’indexation

Comme décrit dans le document de [Schéma agnostique indexation avec DocumentDB d’Azure](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , nous avons conçu le moteur de base de données de DocumentDB pour être véritablement indépendant de schéma et fournir une assistance de première classe de JSON. Le moteur de base de données d’écriture optimisé de DocumentDB comprend en mode natif les données spatiales (points, lignes et polygones) représentées dans la norme GeoJSON.

En bref, la géométrie est projetée à partir des coordonnées géodésiques sur un plan 2D, puis divisée progressivement les cellules à l’aide d’un **quadtree**. Ces cellules sont mappées à 1D, en fonction de l’emplacement de la cellule au sein d’une **courbe de remplissage espace de Hilbert**, qui préserve la localité de points. En outre lorsque les données de localisation sont indexées, il passe par un processus appelé **polygonalisation**, c'est-à-dire toutes les cellules qui se recoupent un emplacement sont identifiés et stockés en tant que clés dans l’index DocumentDB. Au moment de la requête, arguments tels que les points et les polygones sont également fractionnées pour extraire les plages d’ID de cellule appropriée, puis utilisées pour récupérer des données à partir de l’index.

Si vous spécifiez une politique d’indexation qui inclut un index spatial pour / * (tous les chemins d’accès), puis trouvés dans la collection de tous les points sont indexées pour les recherches spatiales efficaces (ST_WITHIN et ST_DISTANCE). Index spatiaux ne pas avoir une valeur de précision et utilisez toujours une valeur de précision par défaut.

>[AZURE.NOTE] DocumentDB prend en charge l’indexation automatique des Points, des polygones et LineStrings

L’extrait suivant de JSON affiche une politique d’indexation avec indexation spatiale activé, c'est-à-dire n’importe quel point de GeoJSON trouvée dans les documents pour l’interrogation spatiale d’index. Si vous modifiez la stratégie d’indexation de l’utilisation du portail Azure, vous pouvez spécifier le JSON suivant pour une stratégie d’indexation pour activer indexation sur votre collection spatiale.

**Collection d’indexation de stratégie JSON avec Spatial activé pour les points et les polygones**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Voici un extrait de code dans .NET qui montre comment créer une collection avec indexation spatiale est activée pour tous les chemins contenant des points. 

**Créer une collection avec indexation spatiale**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

Et Voici comment vous pouvez modifier une collection existante pour tirer parti de l’indexation spatiale sur tous les points qui sont stockés dans les documents.

**Modifier une collection existante avec indexation spatiale**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE] Si la valeur de GeoJSON dans le document de l’emplacement est incorrect ou non valide, puis il ne sera pas obtenir indexée pour la recherche spatiale. Vous pouvez valider les valeurs d’emplacement à l’aide de ST_ISVALID et ST_ISVALIDDETAILED.
>
> Si la définition de votre collection inclut une clé de partition, il n’est pas signalé d’indexation de progression de la transformation. 

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris comment démarrer avec prise en charge de géographiques dans DocumentDB, vous pouvez :

- Démarrer le codage avec les [exemples de code géographique .NET sur Github](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
- Découvrir avec géographique interrogeant sur le [Terrain de requête DocumentDB](http://www.documentdb.com/sql/demo#geospatial)
- En savoir plus sur la [Requête de DocumentDB](documentdb-sql-query.md)
- Pour en savoir plus sur les [Stratégies d’indexation DocumentDB](documentdb-indexing-policies.md)
