<properties
    pageTitle="Créer un index de recherche d’Azure à l’aide du Kit de développement .NET | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Créer un index dans le code à l’aide du SDK de .NET recherche Azure."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Créer un index de recherche d’Azure à l’aide du Kit de développement .NET
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-an-index.md)
- [Portail](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTE](search-create-index-rest-api.md)


Cet article vous guidera tout au long du processus de création d’une recherche d’Azure [index de](https://msdn.microsoft.com/library/azure/dn798941.aspx) l’aide du [Kit de développement .NET Azure recherche](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Avant de suivre ce guide et création d’un index, vous devez avoir déjà [créé un service de recherche d’Azure](search-create-service-portal.md).

Notez que tous les exemples de code dans cet article est écrit en C#. Vous trouverez la source complète de code [sur GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>I. Identifier la clé api de l’administrateur de votre service de recherche d’Azure
Maintenant que vous avez configuré un service de recherche d’Azure, vous êtes presque prêt à émettre des requêtes par rapport à votre point de terminaison de service à l’aide du Kit de développement .NET. Tout d’abord, vous devez obtenir une des admin api-clés qui a été généré pour le service de recherche que vous mis en service. Le Kit de développement .NET enverra cette clé d’api sur chaque demande pour votre service. Ayant une clé valide établit l’approbation, sur une base par demande, entre l’application d’envoi de la demande et le service qui la gère.

1. Pour trouver les touches de votre service api vous devez vous connecter au [Portail Azure](https://portal.azure.com/)
2. Atteindre les lames de votre service de recherche d’Azure
3. Cliquez sur l’icône « Clés »

Votre service aura les *clés de l’administration* et de *requête*.

  - Votre principal et secondaire *admin clés* accorder tous les droits sur toutes les opérations, y compris la possibilité de gérer le service, de créer et de supprimer des index, des indexeurs et des sources de données. Il existe deux clés afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et vice versa.
  - Vos *clés de requête* accorder l’accès en lecture seule aux index et aux documents et sont en général distribuées aux applications émettant des requêtes de recherche.

Pour les besoins de la création d’un index, vous pouvez utiliser votre clé d’administrateur principal ou secondaire.

<a name="CreateSearchServiceClient"></a>
## <a name="ii-create-an-instance-of-the-searchserviceclient-class"></a>II. Créez une instance de la classe SearchServiceClient
Pour commencer à utiliser le Kit de développement .NET de recherche Azure, vous devez créer une instance de le `SearchServiceClient` classe. Cette classe a plusieurs constructeurs. Celui que vous souhaitez prend le nom de votre service de recherche et un `SearchCredentials` objet en tant que paramètres. `SearchCredentials`encapsule la clé de l’api.

Le code ci-dessous crée un nouveau `SearchServiceClient` à l’aide de valeurs pour le nom de service de recherche et de la clé api qui sont stockés dans le fichier de configuration de l’application (`app.config` ou `web.config`) :

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient`a un `Indexes` propriété. Cette propriété fournit toutes les méthodes dont vous avez besoin pour créer, afficher, mettre à jour ou supprimer des index de recherche d’Azure.

> [AZURE.NOTE] Le `SearchServiceClient` classe gère les connexions à votre service de recherche. Afin d’éviter d’ouvrir trop de connexions, vous devez tenter de partager une seule instance de `SearchServiceClient` dans votre application, si possible. Ses méthodes sont thread-safe pour activer ce type de partage.

<a name="DefineIndex"></a>
## <a name="iii-define-your-azure-search-index-using-the-index-class"></a>III. Définir votre index de recherche d’Azure à l’aide de la `Index` classe
Un seul appel à la `Indexes.Create` méthode crée votre index. Cette méthode prend comme paramètre une `Index` objet qui définit votre index de recherche d’Azure. Vous devez créer un `Index` objet et de l’initialiser comme suit :

1. Définir le `Name` propriété de la `Index` objet pour le nom de l’index.
2. Définir le `Fields` propriété de la `Index` objet à un tableau de `Field` objets. Chacun de la `Field` objets définit le comportement d’un champ dans l’index. Vous pouvez fournir le nom du champ dans le constructeur, avec le type de données (ou un analyseur pour les champs de chaîne). Vous pouvez également définir d’autres propriétés telles que `IsSearchable`, `IsFilterable`, etc..

Il est important de le maintenir votre recherche utilisateur expérience et des besoins à l’esprit lorsque vous concevez votre index que chaque `Field` doivent être affectés les [propriétés appropriées](https://msdn.microsoft.com/library/azure/dn798941.aspx). Ces contrôles de propriétés, qui recherche les fonctionnalités (filtrage, faceting, tri de recherche de texte intégral, etc.) s’appliquent à des champs. Pour les propriétés que vous ne définissez pas explicitement, le `Field` de classe par défaut, la désactivation de la fonctionnalité de recherche correspondante, sauf si vous l’activer.

Dans notre exemple, nous avons nommé notre index « hôtels » et défini nos champs comme suit :

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Nous avons soigneusement choisi les valeurs de propriété pour chaque `Field` basée sur la manière dont nous ils seront utilisés dans une application. Par exemple, il est probable que les personnes recherchant des hôtels sera intéressés par les correspondances de mots-clés sur le `description` champ, afin de nous permettre la recherche de texte intégral de ce champ en définissant `IsSearchable` à `true`.

Veuillez noter qu’exactement un champ dans l’index de type `DataType.String` doit être le désigné comme champ _clé_ en définissant `IsKey` à `true` (voir `hotelId` dans l’exemple ci-dessus).

La définition d’index utilise un analyseur de langage personnalisé pour le `description_fr` , car il est conçu pour stocker du texte Français. Consultez [la langue prise en charge de la rubrique sur le site MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) , ainsi que le correspondant [le billet de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) pour plus d’informations sur les analyseurs de langage.

> [AZURE.NOTE]  Notez qu’en passant `AnalyzerName.FrLucene` dans le constructeur, le `Field` sera automatiquement de type `DataType.String` et `IsSearchable` la valeur `true`.

## <a name="iv-create-the-index"></a>IV. Création de l’index
Maintenant que vous avez initialisé `Index` objet, vous pouvez créer l’index simplement en appelant `Indexes.Create` sur votre `SearchServiceClient` objet :

```csharp
serviceClient.Indexes.Create(definition);
```

Pour une demande réussie, la méthode retourne normalement. S’il existe un problème avec la requête comme un paramètre non valide, la méthode lèvera `CloudException`.

Lorsque vous avez terminé avec un index et que vous souhaitez supprimer, vous devez simplement appeler le `Indexes.Delete` méthode sur votre `SearchServiceClient`. Par exemple, c’est comment nous supprime l’index de « hôtels » :

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] L’exemple de code dans cet article utilise les méthodes synchrones du SDK .NET recherche Azure pour plus de simplicité. Nous vous recommandons d’utiliser les méthodes asynchrones dans vos propres applications tout en conservant les évolutives et réactives. Par exemple, dans les exemples ci-dessus, vous pouvez utiliser `CreateAsync` et `DeleteAsync` au lieu de `Create` et `Delete`.

## <a name="next"></a>Suivant
Création d’un index de recherche d’Azure, vous serez prêt à [charger votre contenu dans l’index](search-what-is-data-import.md) afin que vous pouvez lancer la recherche de vos données.
