<properties
    pageTitle="Téléchargement des données de recherche Azure à l’aide du Kit de développement .NET | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Apprenez à télécharger des données à un index de recherche Azure à l’aide du Kit de développement .NET."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Télécharger des données à la recherche d’Azure à l’aide du Kit de développement .NET
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTE](search-import-data-rest-api.md)

Cet article vous explique comment utiliser le [Kit de développement .NET de recherche Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx) pour importer des données dans un index de recherche d’Azure.

Avant de commencer cette procédure pas à pas, vous disposez déjà [créé un index de recherche d’Azure](search-what-is-an-index.md). On considère que vous avez déjà créé un `SearchServiceClient` objet, comme illustré dans la [Création d’un index de recherche d’Azure à l’aide du Kit de développement .NET](search-create-index-dotnet.md#CreateSearchServiceClient).

Notez que tous les exemples de code dans cet article est écrit en C#. Vous trouverez la source complète de code [sur GitHub](http://aka.ms/search-dotnet-howto).

Pour distribuer des documents dans l’index à l’aide du Kit de développement .NET, vous devez :

  1. Créer un `SearchIndexClient` objet pour se connecter à votre index de recherche.
  2. Créer un `IndexBatch` contenant les documents à être ajouté, modifié ou supprimé.
  3. Appelez le `Documents.Index` méthode de votre `SearchIndexClient` pour envoyer le `IndexBatch` à votre index de recherche.

## <a name="i-create-an-instance-of-the-searchindexclient-class"></a>I. Créez une instance de la classe SearchIndexClient
Pour importer des données dans l’index à l’aide du SDK de .NET recherche Azure, vous devez créer une instance de le `SearchIndexClient` classe. Vous pouvez construire cette instance vous-même, mais il plus facile si vous disposez déjà d’une `SearchServiceClient` instance pour appeler sa `Indexes.GetClient` méthode. Par exemple, voici comment vous devez obtenir un `SearchIndexClient` pour l’index nommé « hôtels » à partir d’un `SearchServiceClient` nommé `serviceClient`:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] Dans une application de recherche classique, de remplissage et la gestion des index est géré par un composant distinct à partir de requêtes de recherche. `Indexes.GetClient`pratique pour le remplissage d’un index, car il vous évite les problèmes de fourniture d’un autre `SearchCredentials`. Il effectue cette opération en passant la clé administrateur que vous avez utilisé pour créer le `SearchServiceClient` à la nouvelle `SearchIndexClient`. Toutefois, dans le cadre de votre application qui exécute des requêtes, il est préférable de créer le `SearchIndexClient` directement afin que vous puissiez passer dans une clé de requête au lieu d’une clé de l’administration. Ceci est cohérent avec le [principe du moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege) et contribuera à renforcer la sécurité de votre application. Vous trouverez plus d’informations sur les clés de l’admin et de requête dans la [référence de l’API de reste Azure recherche sur MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

`SearchIndexClient`a un `Documents` propriété. Cette propriété fournit toutes les méthodes que vous devez ajouter, modifier, supprimer ou interroger des documents dans l’index.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Décidez de l’action d’indexation à utiliser
Pour importer des données à l’aide du Kit de développement .NET, vous devez empaqueter vos données dans un `IndexBatch` objet. Un `IndexBatch` encapsule une collection de `IndexAction` objets, dont chacun contient un document et une propriété qui indique à Azure recherche action à effectuer sur ce document (téléchargement, fusion, suppression, etc.). En fonction de la sous actions vous choisissez, certains champs doivent être inclus pour chaque document :

Action | Description | Champs nécessaires pour chaque document | Notes
--- | --- | --- | ---
`Upload` | Un `Upload` action est similaire à un « upsert » dans lequel le document sera inséré s’il est nouveau et mis à jour/remplacé s’il existe. | touche, ainsi que tous les autres champs que vous souhaitez définir | Lors de la mise à jour/remplacement d’un document existant, un champ qui n’est pas spécifié dans la demande aura son champ la valeur `null`. Cela se produit même lorsque le champ a été précédemment défini sur une valeur non null.
`Merge` | Met à jour un document existant avec les champs spécifiés. Si le document n’existe pas dans l’index, la fusion échoue. | touche, ainsi que tous les autres champs que vous souhaitez définir | N’importe quel champ que vous spécifiez dans une fusion remplacent le champ existant dans le document. Cela inclut les champs de type `DataType.Collection(DataType.String)`. Par exemple, si le document contient un champ `tags` avec la valeur `["budget"]` et que vous exécutez une fusion avec une valeur `["economy", "pool"]` de `tags`, la valeur finale de la `tags` champ sera `["economy", "pool"]`. Il ne sera pas `["budget", "economy", "pool"]`.
`MergeOrUpload` | Cette action se comporte comme `Merge` si un document avec la clé spécifiée existe déjà dans l’index. Si le document n’existe pas, il se comporte comme `Upload` avec un nouveau document. | touche, ainsi que tous les autres champs que vous souhaitez définir | -
`Delete` | Supprime le document spécifié à partir de l’index. | clé uniquement | Tous les champs que vous spécifiez que le champ clé sera ignoré. Si vous souhaitez supprimer un champ d’un document, utilisez `Merge` à la place et simplement la valeur du champ explicitement null.

Vous pouvez spécifier l’action à utiliser avec les différentes méthodes statiques de la `IndexBatch` et `IndexAction` des classes, comme indiqué dans la section suivante.

## <a name="iii-construct-your-indexbatch"></a>III. Construire votre IndexBatch
Maintenant que vous connaissez les actions à effectuer sur vos documents, vous êtes prêt à construire la `IndexBatch`. L’exemple ci-dessous montre comment créer un lot avec quelques actions différentes. Notez que notre exemple utilise une classe personnalisée appelée `Hotel` qui mappe à un document dans l’index de « hôtels ».

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

Dans ce cas, nous utilisons `Upload`, `MergeOrUpload`, et `Delete` comme nos actions de recherche, tel que spécifié par les méthodes appelées sur le `IndexAction` classe.

Supposons que cet exemple « hôtels » index est déjà rempli avec un nombre de documents. Notez la façon dont nous ne possédait pas de spécifier tous les champs document possible lors de l’utilisation `MergeOrUpload` et comment nous uniquement la clé de document (`HotelId`) lors de l’utilisation `Delete`.

Notez également que vous ne pouvez inclure que jusqu'à 1000 documents dans une seule demande d’indexation.

> [AZURE.NOTE] Dans cet exemple, nous appliquons les différentes actions pour différents documents. Si vous souhaitez effectuer les mêmes actions sur tous les documents du lot, au lieu d’appeler `IndexBatch.New`, vous pouvez utiliser les autres méthodes statiques de `IndexBatch`. Par exemple, vous pouvez créer des lots en appelant `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, ou `IndexBatch.Delete`. Ces méthodes utilisent un ensemble de documents (objets de type `Hotel` dans cet exemple) à la place de `IndexAction` objets.

## <a name="iv-import-data-to-the-index"></a>IV. Importer des données dans l’index
Maintenant que vous avez initialisé `IndexBatch` objet, vous pouvez l’envoyer à l’index en appelant `Documents.Index` sur votre `SearchIndexClient` objet. L’exemple suivant montre comment appeler `Index`, ainsi que certains vous devez effectuer des étapes supplémentaires :

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Remarque le `try` / `catch` autour de l’appel à la `Index` méthode. Le bloc catch gère un cas d’erreur importants pour l’indexation. En cas de défaillance de votre service de recherche d’Azure indexer des documents dans le traitement par lots, un `IndexBatchException` est levée par `Documents.Index`. Cela peut se produire si vous indexez des documents lorsque votre service est surchargé. **Nous vous recommandons vivement d’explicitement traitant ce dossier dans votre code.** Vous pouvez retarder et recommencez l’indexation de documents qui ont échoué, ou vous pouvez ouvrir une session et continuer l’exemple effectue, ou vous pouvez faire quelque chose d’autre en fonction des exigences de cohérence des données de votre application.

Enfin, le code dans l’exemple ci-dessus retards pendant deux secondes. L’indexation de manière asynchrone dans votre service de recherche d’Azure, l’exemple d’application doit attendre un peu de temps pour s’assurer que les documents sont disponibles pour la recherche. Retards ainsi ne sont en général nécessaires dans les démonstrations, les tests et les exemples d’applications.

<a name="HotelClass"></a>
### <a name="how-the-net-sdk-handles-documents"></a>Comment le Kit de développement .NET gère les documents

Vous vous demandez peut-être comment le SDK de .NET recherche Azure est en mesure de télécharger des instances d’une classe définie par l’utilisateur comme `Hotel` à l’index. Pour vous aider à répondre à cette question, examinons le `Hotel` classe qui mappe sur le schéma d’index défini dans la [Création d’un index de recherche d’Azure à l’aide du Kit de développement .NET](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

La première chose à noter est que chaque propriété publique des `Hotel` correspond à un champ dans la définition de l’index, mais une différence essentielle : le nom de chaque champ commence par une lettre minuscule (« la casse mixte »), tandis que le nom de chaque propriété publique des `Hotel` commence par une lettre majuscule (« casse Pascal »). Il s’agit d’un scénario courant dans les applications .NET qui effectuent des liaison de données où le schéma de la cible est à l’extérieur du contrôle, le développeur d’applications. Au lieu de devoir violent les règles de dénomination en rendant la propriété noms casse mixte .NET, vous pouvez demander le Kit de développement pour mapper les noms de propriété à casse mixte automatiquement avec le `[SerializePropertyNamesAsCamelCase]` attribut.

> [AZURE.NOTE] Le Kit de développement .NET de recherche Azure utilise la bibliothèque de [JSON.NET de NewtonSoft](http://www.newtonsoft.com/json/help/html/Introduction.htm) de sérialiser et de désérialiser vos objets modèle personnalisé vers et à partir de JSON. Vous pouvez personnaliser cette sérialisation si nécessaire. Vous trouverez plus de détails dans la [mise à niveau vers le Kit de développement Azure recherche .NET version 1.1](search-dotnet-sdk-migration.md#WhatsNew). Un exemple de cela est l’utilisation de la `[JsonProperty]` d’attribut sur le `DescriptionFr` propriété dans le code ci-dessus.

La deuxième chose importante sur le `Hotel` classe sont les types de données des propriétés publiques. Les types de .NET de ces propriétés correspondent à leurs types de champ équivalent dans la définition d’index. Par exemple, le `Category` chaîne les mappages des propriétés de la `category` champ, ce qui est de type `DataType.String`. Il existe des mappages de type similaire entre `bool?` et `DataType.Boolean`, `DateTimeOffset?` et `DataType.DateTimeOffset`, etc.. Les règles spécifiques pour le mappage de type sont documentées avec le `Documents.Get` méthode sur [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

Possibilité d’utiliser vos propres classes en tant que documents fonctionne dans les deux sens ; Vous pouvez également récupérer les résultats de la recherche et le Kit de développement logiciel automatiquement désérialiser à un type de votre choix, comme indiqué dans l' [article suivant](search-query-dotnet.md).

> [AZURE.NOTE] Le Kit de développement .NET de recherche Azure prend également en charge les documents typées dynamiquement à l’aide de la `Document` (classe), qui est un mappage de clé/valeur de champs aux valeurs de champ. Cela est utile dans les scénarios où vous ne connaissez pas le schéma d’index au moment du design ou dans le cas où il serait pratique de pouvoir lier aux classes de modèle spécifique. Toutes les méthodes dans le Kit de développement logiciel qui traitent les documents ont des surcharges qui fonctionnent avec le `Document` classe, ainsi que les surcharges fortement typée qui prennent un paramètre de type générique. Seules ces dernières sont utilisées dans l’exemple de code dans cet article. Vous pouvez trouver plus d’informations sur le `Document` de classe [sur MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Une remarque importante à propos des types de données**

Lorsque vous créez vos propres classes de modèle à mapper à un index de recherche d’Azure, il est recommandé de déclarer des propriétés de types de valeur tels que `bool` et `int` Nullable (par exemple, `bool?` à la place de `bool`). Si vous utilisez une propriété non nullable, vous devez **garantir** qu’aucun document dans votre index ne contienne une valeur null pour le champ correspondant. Le Kit de développement logiciel, ni le service de recherche d’Azure vous aidera à appliquer ceci.

Ce n’est pas seulement un problème hypothétique : imaginez un scénario où vous ajoutez un nouveau champ à un index qui est de type `DataType.Int32`. Après la mise à jour de la définition d’index, tous les documents aura une valeur null pour ce champ (puisque tous les types sont nullables dans Azure recherche). Si vous utilisez une classe de modèle avec un non-nullable `int` propriété de ce champ, vous obtiendrez un `JsonSerializationException` comme suit lors de la tentative de récupération de documents :

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Pour cette raison, nous vous recommandons d’utiliser des types nullables dans vos classes de modèle en tant que meilleure pratique.

## <a name="next"></a>Suivant
Après avoir rempli votre index de recherche d’Azure, vous serez prêt à commencer l’exécution de requêtes pour rechercher des documents. Pour plus d’informations, consultez [Index de recherche Azure votre requête](search-query-overview.md) .
