<properties
    pageTitle="Interroger votre Index de recherche Azure à l’aide du Kit de développement .NET | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Créer une requête de recherche dans recherche Azure et utilisez les paramètres de recherche pour filtrer et trier les résultats de recherche."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Interroger votre index de recherche d’Azure à l’aide du Kit de développement .NET
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-query-overview.md)
- [Portail](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTE](search-query-rest-api.md)

Cet article vous explique comment interroger un index en utilisant le [Kit de développement .NET de recherche Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Avant de commencer cette procédure pas à pas, vous devez déjà avoir [créé un index de recherche d’Azure](search-what-is-an-index.md) et [rempli avec les données](search-what-is-data-import.md).

Notez que tous les exemples de code dans cet article est écrit en C#. Vous trouverez la source complète de code [sur GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>I. Identifier la clé de votre service de recherche d’Azure requête api
Maintenant que vous avez créé un index de recherche d’Azure, vous êtes presque prêt à émettre des requêtes à l’aide du Kit de développement .NET. Tout d’abord, vous devez obtenir une des requêtes api-clés qui a été généré pour le service de recherche que vous mis en service. Le Kit de développement .NET enverra cette clé d’api sur chaque demande pour votre service. Ayant une clé valide établit l’approbation, sur une base par demande, entre l’application d’envoi de la demande et le service qui la gère.

1. Pour trouver les touches de votre service api vous devez vous connecter au [Portail Azure](https://portal.azure.com/)
2. Atteindre les lames de votre service de recherche d’Azure
3. Cliquez sur l’icône « Clés »

Votre service aura les *clés de l’administration* et de *requête*.

  - Votre principal et secondaire *admin clés* accorder tous les droits sur toutes les opérations, y compris la possibilité de gérer le service, de créer et de supprimer des index, des indexeurs et des sources de données. Il existe deux clés afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et vice versa.
  - Vos *clés de requête* accorder l’accès en lecture seule aux index et aux documents et sont en général distribuées aux applications émettant des requêtes de recherche.

Aux fins de l’interrogation d’un index, vous pouvez utiliser une des clés de votre requête. Vos clés d’administration peuvent également servir pour les requêtes, mais vous devez utiliser une clé de la requête dans le code de votre application comme cela suit mieux le [principe du moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-create-an-instance-of-the-searchindexclient-class"></a>II. Créez une instance de la classe SearchIndexClient
Pour émettre des requêtes avec le Kit de développement .NET de recherche Azure, vous devez créer une instance de le `SearchIndexClient` classe. Cette classe a plusieurs constructeurs. Celui que vous souhaitez prend votre nom de service de recherche, le nom de l’index et un `SearchCredentials` objet en tant que paramètres. `SearchCredentials`encapsule la clé de l’api.

Le code ci-dessous crée un nouveau `SearchIndexClient` de l’index « hôtels » (créé dans la [Création d’un index de recherche d’Azure à l’aide du Kit de développement .NET](search-create-index-dotnet.md)) à l’aide de valeurs pour le nom de service de recherche et de la clé api qui sont stockés dans le fichier de configuration de l’application (`app.config` ou `web.config`) :

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient`a un `Documents` propriété. Cette propriété fournit toutes les méthodes que vous devez interroger l’index de recherche d’Azure.

## <a name="iii-query-your-index"></a>III. Interrogez votre index.
Effectuez des recherches avec le Kit de développement .NET est aussi simple que l’appel du `Documents.Search` méthode sur votre `SearchIndexClient`. Cette méthode accepte quelques paramètres, y compris le texte de recherche, avec une `SearchParameters` objet qui peut être utilisé pour affiner la requête.

#### <a name="types-of-queries"></a>Types de requêtes
Les deux principaux [types de requêtes](search-query-overview.md#types-of-queries) que vous allez utiliser sont `search` et `filter`. A `search` recherche un ou plusieurs termes dans tous les champs de _recherche_ dans l’index de la requête. A `filter` requête évalue une expression booléenne sur toutes les _campagnes_ champs dans un index.

Les filtres et les recherches sont effectuées à l’aide de la `Documents.Search` méthode. Une requête de recherche peut être passée dans le `searchText` paramètre, le tout en une expression de filtre peut être passée dans le `Filter` propriété de la `SearchParameters` classe. Pour filtrer sans rechercher, il vous suffit de passer `"*"` pour la `searchText` paramètre. Pour effectuer une recherche sans filtrage, laissez le `Filter` propriété non définie, ou ne passez pas dans une `SearchParameters` instance du tout.

#### <a name="example-queries"></a>Exemples de requêtes

L’exemple de code suivant montre différentes manières d’interroger l’index « hôtels » définie dans la [Création d’un index de recherche d’Azure à l’aide du Kit de développement .NET](search-create-index-dotnet.md#DefineIndex). Notez que les documents renvoyés avec les résultats de recherche sont des instances de la `Hotel` (classe), qui a été défini dans [l’Importation de données dans la recherche d’Azure à l’aide du Kit de développement .NET](search-import-data-dotnet.md#HotelClass). L’exemple de code utilise un `WriteDocuments` méthode pour sortir les résultats de la recherche à la console. Cette méthode est décrite dans la section suivante.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="iv-handle-search-results"></a>IV. Gérer les résultats de la recherche
Le `Documents.Search` méthode retourne un `DocumentSearchResult` objet qui contient les résultats de la requête. L’exemple de la section précédente a utilisé une méthode appelée `WriteDocuments` pour sortir les résultats de la recherche sur la console :

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Voici à quoi ressemblent les résultats pour les requêtes dans la section précédente, en supposant que l’index « hôtels » est rempli avec les données de [l’Importation de données dans la recherche d’Azure à l’aide du Kit de développement .NET](search-import-data-dotnet.md):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

L’exemple de code ci-dessus utilise la console pour sortir les résultats de la recherche. De même, vous devez afficher les résultats de la recherche dans votre propre application. Consultez [Cet exemple sur GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) pour obtenir un exemple de la façon de rendre les résultats de la recherche dans une application web basée sur ASP.NET MVC.
