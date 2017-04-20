<properties
   pageTitle="Mise à niveau vers le Kit de développement Azure recherche .NET version 1.1 | Microsoft Azure | Service de recherche de nuage hébergé"
   description="Mise à niveau vers le Kit de développement Azure recherche .NET version 1.1"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/15/2016"
   ms.author="brjohnst"/>

# <a name="upgrading-to-the-azure-search-net-sdk-version-20-preview"></a>Mise à niveau vers la version Azure recherche .NET SDK 2.0-aperçu

Si vous utilisez la version 1.1 ou antérieure du [Kit de développement .NET de recherche Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx), cet article vous aidera à mettre à niveau de votre application pour utiliser la version majeure suivante, 2.0-aperçu.

Pour une procédure pas à pas plus générale du Kit de développement, notamment des exemples, voir [comment utiliser recherche Azure à partir d’une Application .NET](search-howto-dotnet-sdk.md).

Version 2.0-aperçu du SDK .NET recherche Azure contient certaines modifications de versions antérieures. Ce sont essentiellement secondaires, afin de modifier votre code doit exiger seulement un minimum d’efforts. Consultez les [étapes de mise à niveau](#UpgradeSteps) pour obtenir des instructions sur la façon de modifier votre code utiliser la nouvelle version du Kit de développement logiciel.

> [AZURE.NOTE] Si vous utilisez une version plus ancienne ou de 0,13 pouce-aperçu, vous devez mettre à niveau vers la version 1.1, premier et ensuite une mise à niveau vers 2.0-aperçu. Consultez [annexe : étapes de mise à niveau vers la version 1.1](#UpgradeStepsV1) pour obtenir des instructions.

<a name="WhatsNew"></a>
## <a name="whats-new-in-version-20-preview"></a>Quelles sont les nouveautés dans la version 2.0-aperçu

Version 2.0-aperçu est la première version du SDK .NET recherche Azure pour cibler une version préliminaire de l’API REST recherche Azure, spécifiquement 2015-02-28-aperçu. Cela permet d’utiliser de nombreuses fonctionnalités d’aperçu de recherche Azure à partir d’une application .NET, y compris les éléments suivants :

- [Analyseurs personnalisés](https://aka.ms/customanalyzers)
- Prise en charge d’indexeur [Entreposage de Table Azure](search-howto-indexing-azure-tables.md) et [Blob Azure](search-howto-indexing-azure-blob-storage.md)
- Personnalisation d’indexeur via les [mappages de champs](search-indexer-field-mappings.md)
- Prend en charge les ETags pour activer la mise à jour simultanées sûre des sources de données, les indexeurs et les définitions d’index
- Prise en charge de base de .NET et .NET, profil Portable 111

<a name="UpgradeSteps"></a>
## <a name="steps-to-upgrade"></a>Étapes à suivre pour mettre à niveau

Tout d’abord, mise à jour de votre référence de NuGet pour `Microsoft.Azure.Search` à l’aide de la Console du Gestionnaire de package NuGet, ou en cliquant sur les références de votre projet et en sélectionnant « Gérer... de Packages NuGet » dans Visual Studio. Veillez à activer les packages pre-release en sélectionnant « Inclure la version préliminaire » dans le NuGet « Gérer les Packages » fenêtre dans Visual Studio, ou à l’aide de la `-IncludePrerelease` changer si vous êtes à l’aide du Gestionnaire de package NuGet Console.

Une fois que NuGet a téléchargé les nouveaux packages et leurs dépendances, régénérez votre projet. En fonction de la structure de votre code, il peut reconstruire correctement. Dans ce cas, vous êtes prêt à l’emploi !

En cas de défaillance de votre build, vous devez voir une erreur de build comme suit :

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

L’étape suivante consiste à corriger cette erreur de build. Pour plus d’informations sur la cause de l’erreur et comment résoudre le problème, consultez [Nouveautés dans la version 2.0-aperçu](#ListOfChanges) .

Vous pouvez voir les avertissements de génération supplémentaires liées à des propriétés ou des méthodes obsolètes. Les avertissements contiendra les instructions sur les éléments à utiliser à la place de la fonctionnalité désapprouvée. Par exemple, si votre application utilise le `SearchRequestOptions.RequestId` propriété, vous devez obtenir un avertissement indiquant que`"This property is deprecated. Please use ClientRequestId instead."`

Une fois que vous avez résolu les erreurs de build, vous pouvez apporter des modifications à votre application pour tirer parti des nouvelles fonctionnalités, si vous le souhaitez. Nouvelles fonctionnalités du SDK sont détaillées dans [ce qui est nouveau dans la version 2.0-aperçu](#WhatsNew).

<a name="ListOfChanges"></a>
## <a name="breaking-changes-in-version-20-preview"></a>Modifications avec rupture dans la version 2.0-aperçu

Il existe uniquement une modification avec rupture dans la version 2.0-aperçu qui peut-être nécessiter des modifications de code en plus de la reconstruction de votre application.

### <a name="indexesgetclient-return-type"></a>Type de retour de Indexes.GetClient

Le `Indexes.GetClient` méthode dispose d’un nouveau type de retour. Auparavant, elle renvoyait `SearchIndexClient`, mais il a été remplacé par `ISearchIndexClient` dans la version 2.0-aperçu. Il s’agit de prendre en charge les clients qui souhaitent faire la `GetClient` méthode pour les tests unitaires en retournant une implémentation fictive de `ISearchIndexClient`.

#### <a name="example"></a>Exemple

Si votre code ressemble à ceci :

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

## <a name="conclusion"></a>Conclusion
Si vous avez besoin de plus d’informations sur l’utilisation du Kit de développement .NET de recherche Azure, consultez notre [savoir-faire](search-howto-dotnet-sdk.md)récemment mis à jour.

Nous espérons recevoir vos commentaires sur le Kit de développement logiciel. Si vous rencontrez des problèmes, n’hésitez pas à nous demander de l’aide sur le [forum MSDN de recherche Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuresearch). Si vous trouvez un bogue, vous pouvez classer un problème dans le [référentiel d’Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Veillez à faire précéder le titre de votre problème avec « Search SDK : ».

Merci d’avoir choisi Azure recherche !

<a name="UpgradeStepsV1"></a>
## <a name="appendix-steps-to-upgrade-to-version-11"></a>L’appendice : Les étapes mise à niveau vers la version 1.1 

> [AZURE.NOTE] Cette section s’applique uniquement aux utilisateurs de la version d’Azure recherche .NET SDK 0,13 pouce-aperçu et plus ancienne.

Tout d’abord, mise à jour de votre référence de NuGet pour `Microsoft.Azure.Search` à l’aide de la Console du Gestionnaire de package NuGet, ou en cliquant sur les références de votre projet et en sélectionnant « Gérer... de Packages NuGet » dans Visual Studio.

Une fois que NuGet a téléchargé les nouveaux packages et leurs dépendances, régénérez votre projet.

Si vous utilisiez précédemment 1.0.2-preview, 1.0.1-preview ou 1.0.0-preview de la version, la build doit réussir et vous êtes prêt à passer !

Si vous utilisiez précédemment version 0.13.0-preview ou antérieure, vous devriez voir générer des erreurs comme suit :

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

L’étape suivante consiste à corriger les erreurs de build un par un. La plupart nécessitent la modification de certains noms de classes et de méthodes qui ont été renommés dans le Kit de développement logiciel. [Liste des modifications dans la version 1.1](#ListOfChangesV1) contient une liste de ces changements de nom.

Si vous utilisez des classes personnalisées pour vos documents, et que ces classes possèdent des propriétés non nullable types primitifs (par exemple, `int` ou `bool` dans C#), il existe un correctif de bogue dans la version 1.1 du Kit de développement dont vous devez être conscient. Pour plus d’informations, reportez-vous à la section [correctifs de bogues dans la version 1.1](#BugFixesV1) .

Enfin, une fois que vous avez résolu les erreurs de build, vous pouvez apporter des modifications à votre application pour tirer parti des nouvelles fonctionnalités, si vous le souhaitez.

<a name="ListOfChangesV1"></a>
### <a name="list-of-breaking-changes-in-version-11"></a>Liste des modifications avec rupture dans la version 1.1

La liste suivante est classée par la probabilité que la modification n’affecte pas votre code d’application.

#### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch et IndexAction de modifications

`IndexBatch.Create`a été renommé en `IndexBatch.New` et n’a plus un `params` argument. Vous pouvez utiliser `IndexBatch.New` pour les lots qui combinent différents types d’actions (fusions, suppressions, etc.). En outre, il existe nouvelles méthodes statiques pour la création de lots où toutes les actions sont les mêmes : `Delete`, `Merge`, `MergeOrUpload`, et `Upload`.

`IndexAction`n’a plus les constructeurs publics et ses propriétés sont immuables. Vous devez utiliser les nouvelles méthodes statiques pour la création d’actions à des fins différentes : `Delete`, `Merge`, `MergeOrUpload`, et `Upload`. `IndexAction.Create`a été supprimé. Si vous avez utilisé la surcharge qui accepte uniquement un document, assurez-vous d’utiliser `Upload` à la place.

##### <a name="example"></a>Exemple

Si votre code ressemble à ceci :

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Si vous le souhaitez, vous pouvez les simplifier davantage à ce :

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>Modifications de IndexBatchException

Le `IndexBatchException.IndexResponse` propriété a été renommée en `IndexingResults`, et son type de `IList<IndexingResult>`.

##### <a name="example"></a>Exemple

Si votre code ressemble à ceci :

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
#### <a name="operation-method-changes"></a>Modifications de méthode d’opération

Chaque opération dans le Kit de développement .NET de recherche Azure est exposée sous la forme d’un ensemble de surcharges de la méthode aux appelants synchrones et asynchrones. Les signatures et tenant compte de ces surcharges de la méthode a changé dans la version 1.1.

Par exemple, l’opération « Obtenir des statistiques d’Index » dans les versions antérieures du Kit de développement exposées à ces signatures :

In `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

In `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Les signatures de méthode pour la même opération dans la version 1.1 se présente comme suit :

In `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

In `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

Depuis la version 1.1, le Kit de développement .NET de recherche Azure organise les méthodes d’opération différemment :

 - Les paramètres optionnels sont désormais modélisées comme valeur par défaut de paramètres plutôt que les surcharges de méthode supplémentaires. Cela réduit le nombre de surcharges de la méthode, parfois considérablement.
 - Les méthodes d’extension masquent maintenant un grand nombre de parasites des détails HTTP à partir de l’appelant. Par exemple, les anciennes versions du Kit de développement a retourné un objet de réponse avec un code d’état HTTP, vous n’avez pas souvent besoin pour vérifier dans la mesure où les méthodes d’opération de lever `CloudException` pour tout code d’état qui indique une erreur. Les méthodes d’extension retournent uniquement des objets de modèle, vous évite d’avoir à les désencapsuler dans votre code.
 - À l’inverse, les principales interfaces exposent maintenant les méthodes qui vous donnent plus de contrôle au niveau HTTP si vous en avez besoin. Vous pouvez maintenant passer dans les en-têtes HTTP personnalisés à inclure dans les demandes et la nouvelle `AzureOperationResponse<T>` type de retour vous donne un accès direct à la `HttpRequestMessage` et `HttpResponseMessage` pour l’opération. `AzureOperationResponse`est défini dans le `Microsoft.Rest.Azure` espace de noms et remplace `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>Modifications de ScoringParameters

Une nouvelle classe nommée `ScoringParameter` a été ajoutée dans la dernière version du SDK pour le rendre plus facile de fournir des paramètres de profils de score dans une requête de recherche. Précédemment le `ScoringProfiles` propriétés de la `SearchParameters` classe a été saisi en tant que `IList<string>`; Maintenant elle est typée en tant que `IList<ScoringParameter>`.

##### <a name="example"></a>Exemple

Si votre code ressemble à ceci :

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Vous pouvez le modifier à cette option pour corriger les erreurs de build : 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Modifications de classe de modèle

En raison de la signature change décrit dans la [méthode d’opération de change](#OperationMethodChanges), de nombreuses classes dans le `Microsoft.Azure.Search.Models` espace de noms ont été renommés ou supprimés. Par exemple :

 - `IndexDefinitionResponse`a été remplacé par`AzureOperationResponse<Index>`
 - `DocumentSearchResponse`a été renommé`DocumentSearchResult`
 - `IndexResult`a été renommé`IndexingResult`
 - `Documents.Count()`Renvoie à présent une `long` avec le nombre de document au lieu d’un`DocumentCountResponse`
 - `IndexGetStatisticsResponse`a été renommé`IndexGetStatisticsResult`
 - `IndexListResponse`a été renommé`IndexListResult`

Pour résumer, `OperationResponse`-classes dérivées qui existant uniquement pour encapsuler un objet de modèle ont été supprimés. Les autres classes ont eu leur suffixe modifié à partir de `Response` à `Result`.

##### <a name="example"></a>Exemple

Si votre code ressemble à ceci :

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>Les classes de réponse et IEnumerable

Une modification supplémentaire qui peut-être affecter votre code est que les classes de réponse qui contiennent des collections n’implémentent plus `IEnumerable<T>`. Au lieu de cela, vous pouvez accéder directement à la propriété de collection. Par exemple, si votre code ressemble à ceci :

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="important-note-for-web-applications"></a>Remarque importante pour les applications web

Si vous disposez d’une application web qui sérialise `DocumentSearchResponse` directement pour envoyer les résultats de la recherche dans le navigateur, vous devez modifier votre code ou les résultats ne sérialisent pas correctement. Par exemple, si votre code ressemble à ceci :

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Vous pouvez le modifier en obtenant le `.Results` propriétés de la réponse de la recherche pour corriger le rendu des résultats de recherche :

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Vous devrez rechercher ces cas dans votre code **Le compilateur ne vous avertira pas** car `JsonResult.Data` est de type `object`.

#### <a name="cloudexception-changes"></a>Modifications de CloudException

Le `CloudException` classe a été déplacée à partir de la `Hyak.Common` espace de noms à la `Microsoft.Rest.Azure` espace de noms. En outre, sa `Error` propriété a été renommée en `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>Modifications de SearchServiceClient et de SearchIndexClient

Le type de la `Credentials` propriété a été modifiée à partir de `SearchCredentials` à sa classe de base, `ServiceClientCredentials`. Si vous avez besoin pour accéder à la `SearchCredentials` d’un `SearchIndexClient` ou `SearchServiceClient`, utilisez la nouvelle `SearchCredentials` propriété.

Dans les versions antérieures du Kit de développement, `SearchServiceClient` et `SearchIndexClient` a des constructeurs qui ont eu une `HttpClient` paramètre. Ils ont été remplacés par les constructeurs qui prennent une `HttpClientHandler` et un tableau de `DelegatingHandler` objets. Cela rend plus facile à installer des gestionnaires personnalisés pour pré-traiter les requêtes HTTP si nécessaire.

Enfin, les constructeurs qui ont eu une `Uri` et `SearchCredentials` ont été modifiées. Par exemple, si vous avez un code qui ressemble à ceci :

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Notez également que le type du paramètre d’informations d’identification a changé à `ServiceClientCredentials`. Ce n’est pas susceptible d’affecter votre code depuis `SearchCredentials` est dérivée de `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>En passant un ID de demande

Dans les versions antérieures du Kit de développement, vous pouvez définir un ID de demande sur le `SearchServiceClient` ou `SearchIndexClient` et il serait inclus dans chaque demande pour l’API REST. Cela est utile pour la résolution des problèmes avec votre service de recherche si vous devez contacter le support technique. Toutefois, il est plus utile pour définir un ID de demande unique pour chaque opération plutôt que d’utiliser le même ID pour toutes les opérations. Pour cette raison, le `SetClientRequestId` méthodes de `SearchServiceClient` et `SearchIndexClient` ont été supprimés. Au lieu de cela, vous pouvez passer un ID de demande à chaque méthode de l’opération via l’option `SearchRequestOptions` paramètre.

> [AZURE.NOTE] Dans une version ultérieure du Kit de développement, nous allons ajouter un nouveau mécanisme pour un ID de demande global sur le client objets setting qui est cohérent avec l’approche utilisée par les autres kits de développement logiciel Azure.

#### <a name="example"></a>Exemple

Si vous avez un code qui ressemble à ceci :

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Modification du nom d’interface

Les noms d’interface groupe opération ont changé pour être cohérents avec les noms de propriété correspondants :

 - Le type de `ISearchServiceClient.Indexes` a été renommé à partir de `IIndexOperations` à `IIndexesOperations`.
 - Le type de `ISearchServiceClient.Indexers` a été renommé à partir de `IIndexerOperations` à `IIndexersOperations`.
 - Le type de `ISearchServiceClient.DataSources` a été renommé à partir de `IDataSourceOperations` à `IDataSourcesOperations`.
 - Le type de `ISearchIndexClient.Documents` a été renommé à partir de `IDocumentOperations` à `IDocumentsOperations`.

Cette modification est peu susceptible d’affecter votre code, sauf si vous avez créé des simulacres de ces interfaces pour des fins de test.

<a name="BugFixesV1"></a>
### <a name="bug-fixes-in-version-11"></a>Correctifs de bogues dans la version 1.1

Il a un bogue dans les versions antérieures du SDK .NET recherche Azure liée à la sérialisation des classes de modèle personnalisé. Le bogue peut se produire si vous avez créé une classe de modèle personnalisé à une propriété d’un type valeur non nullable.

#### <a name="steps-to-reproduce"></a>Étapes pour reproduire

Créez une classe de modèle personnalisé avec une propriété de type valeur non nullable. Par exemple, ajouter un public `UnitCount` propriété de type `int` au lieu de `int?`.

Si vous indexez un document avec la valeur par défaut de ce type (par exemple, 0 pour `int`), le champ sera null dans Azure recherche. Si vous recherchez par la suite de ce document, le `Search` appel lèvera `JsonSerializationException` se plaindre qu’il est impossible de convertir `null` à `int`.

En outre, les filtres peuvent ne pas fonctionnent comme prévu car la valeur null a été écrite dans l’index au lieu de la valeur prévue.

#### <a name="fix-details"></a>Corriger les détails

Nous avons résolu ce problème dans la version 1.1 du Kit de développement. Maintenant, si vous avez une classe de modèle comme suit :

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

et que vous définissez `IntValue` à 0, cette valeur est désormais correctement sérialisée en tant que 0 sur le réseau et stockée en tant que 0 dans l’index. Réeffectuez fonctionne également comme prévu.

Il existe un problème potentiel d’être conscient de cette approche : Si vous utilisez un type de modèle à une propriété non nullable, vous devez **garantir** qu’aucun document dans votre index ne contienne une valeur null pour le champ correspondant. Le Kit de développement logiciel, ni l’API REST de recherche Azure vous aidera à appliquer ceci.

Ce n’est pas seulement un problème hypothétique : imaginez un scénario où vous ajoutez un nouveau champ à un index qui est de type `Edm.Int32`. Après la mise à jour de la définition d’index, tous les documents aura une valeur null pour ce champ (puisque tous les types sont nullables dans Azure recherche). Si vous utilisez une classe de modèle avec un non-nullable `int` propriété de ce champ, vous obtiendrez un `JsonSerializationException` comme suit lors de la tentative de récupération de documents :

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Pour cette raison, nous recommandons que vous utilisez des types nullables dans vos classes de modèle en tant que meilleure pratique.

Pour plus d’informations sur ce bogue et le corriger, veuillez consulter [ce problème sur GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).
