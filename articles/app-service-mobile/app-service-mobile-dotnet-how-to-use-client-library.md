<properties
    pageTitle="Utilisation de la bibliothèque de client managé App Service Mobile Apps (Windows | Xamarin) | Microsoft Azure"
    description="Apprenez à utiliser un client .NET pour les applications de Mobile Service Azure application avec des applications Windows et Xamarin."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Comment faire pour utiliser le client géré pour les applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##<a name="overview"></a>Vue d’ensemble

Ce guide vous explique comment exécuter des scénarios courants, à l’aide de la bibliothèque client géré pour Azure Application Service Mobile applications pour Windows et les applications Xamarin. Si vous ne connaissez pas les applications Mobile, vous devez envisager de tout d’abord à la fin du [démarrage rapide d’applications de Mobile Azure] [ 1] didacticiel. Dans ce guide, nous concentrer sur le Kit de développement côté client managé. Pour plus d’informations sur les kits de développement côté serveur pour les applications mobiles, consultez la documentation pour le [Kit de développement .NET Server] [ 2] ou le [Kit de développement de serveur Node.js][3].

## <a name="reference-documentation"></a>Documentation de référence

La documentation de référence du Kit de développement logiciel du client se trouve ici : [référence du client .NET d’applications Azure Mobile][4].
Vous trouverez plusieurs exemples de clients dans le [référentiel d’Azure-exemples GitHub][5].

## <a name="supported-platforms"></a>Plates-formes prises en charge

La plate-forme .NET prend en charge les plates-formes suivantes :

* Versions de Xamarin Android pour API 19 à 24 (KitKat via la commande)
* Mises à jour d’e/s de Xamarin pour iOS 8.0 et versions ultérieures
* Plate-forme Windows universel
* Windows Phone 8.1
* 8.0 de Windows Phone, à l’exception des applications Silverlight

L’authentification de « serveur-flux » utilise un mode d’affichage Web pour l’interface utilisateur présentée.  Si le périphérique n’est pas en mesure de présenter un UI WebView, les autres méthodes d’authentification sont nécessaires.  Ce kit de développement logiciel n’est donc pas approprié pour le type d’espion ou de la même façon sur des périphériques restreints.

##<a name="setup"></a>Le programme d’installation et conditions requises

Nous supposons que vous avez déjà créé et publié votre projet Mobile application back-end, qui comprend au moins une table.  Dans le code utilisé dans cette rubrique, la table est nommée `TodoItem` et il comporte les colonnes suivantes : `Id`, `Text`, et `Complete`. Cette table est la table même créée lorsque vous effectuez le démarrage [Azure Mobile applications rapide].

Le type correspondant de côté client typé dans C# est la classe suivante :

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

La [JsonPropertyAttribute] [ 6] est utilisée pour définir le mappage *PropertyName* entre le type de client et de la table.

Pour savoir comment créer des tables dans votre back-end d’applications Mobile, consultez les informations de la [rubrique du Kit de développement .NET Server] [ 7] ou la [rubrique du Kit de développement de serveur Node.js][8]. Si vous avez créé votre back-end de l’application Mobile dans le portail Azure utilisant le démarrage rapide, vous pouvez également utiliser le paramètre de **tables faciles** dans [Azure portal].

###<a name="how-to-install-the-managed-client-sdk-package"></a>Comment : installer le package du Kit de développement logiciel client géré

Utilisez une des méthodes suivantes pour installer le package du Kit de développement logiciel client géré pour applications mobiles de [NuGet][9]:

+ **Visual Studio** Droit sur votre projet, cliquez sur **Gérer les Packages NuGet**, recherchez la `Microsoft.Azure.Mobile.Client` du package, puis cliquez sur **installer**.

+ **Xamarin Studio** Droit sur votre projet, cliquez sur **Ajouter** > **Ajouter des Packages NuGet**, recherche de la `Microsoft.Azure.Mobile.Client `du package, puis cliquez sur **Ajouter un Package**.

Dans le fichier de votre activité principale, pensez à ajouter l’instruction **using** suivante :

    using Microsoft.WindowsAzure.MobileServices;

###<a name="symbolsource"></a>Comment : utiliser des symboles de débogage dans Visual Studio

Les symboles de l’espace de noms Microsoft.Azure.Mobile sont disponibles sur [SymbolSource][10].  Reportez-vous aux [instructions de SymbolSource] [ 11] pour intégrer SymbolSource avec Visual Studio.

##<a name="create-client"></a>Créer le client applications Mobile

Le code suivant crée le [MobileServiceClient] [ 12] objet qui est utilisé pour accéder à votre back-end de l’application Mobile.

    var client = new MobileServiceClient("MOBILE_APP_URL");

Dans le code précédent, remplacez `MOBILE_APP_URL` par l’URL de la principale application Mobile, qui se trouve dans la lame pour la principale application Mobile dans le [portail Azure]. L’objet MobileServiceClient doit être un singleton.

## <a name="work-with-tables"></a>Travailler avec les Tables

La section suivante décrit en détail comment rechercher et extraire des enregistrements et modifier les données dans la table.  Les rubriques suivantes sont traitées :

* [Créer une référence de table](#instantiating)
* [Données de requête](#querying)
* [Filtrer les données retournées](#filtering)
* [Tri de données renvoyée](#sorting)
* [Renvoyer les données dans les pages](#paging)
* [Sélectionner des colonnes spécifiques](#selecting)
* [Rechercher un enregistrement par Id](#lookingup)
* [Traitement de requêtes non typés](#untypedqueries)
* [Insertion de données](#inserting)
* [Mise à jour de données](#updating)
* [Suppression de données](#deleting)
* [Résolution des conflits et l’accès concurrentiel optimiste](#optimisticconcurrency)
* [Liaison à une Interface utilisateur Windows](#binding)
* [Modification de la taille de la Page](#pagesize)

###<a name="instantiating"></a>Comment : créer une table de référence

Tout le code qui accède à ou modifie des données dans une table serveur principal appelle les fonctions sur le `MobileServiceTable` objet. Obtenir une référence à la table en appelant la méthode [GetTable] , comme suit :

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

L’objet utilise le modèle de sérialisation typé. Un modèle de sérialisation non typé est également pris en charge. D’exemple suivant [crée une référence à une table non typée]:

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Dans les requêtes non typés, vous devez spécifier la chaîne de requête OData sous-jacent.

###<a name="querying"></a>Comment : interroger des données à partir de votre application Mobile

Cette section décrit comment émettre des requêtes sur le serveur principal d’application Mobile, qui inclut les fonctionnalités suivantes :

- [Filtrer les données retournées](#filtering)
- [Tri de données renvoyée](#sorting)
- [Renvoyer les données dans les pages](#paging)
- [Sélectionner des colonnes spécifiques](#selecting)
- [Rechercher des données par ID](#lookingup)

>[AZURE.NOTE]Une taille de page de pilotée par serveur est mise en œuvre pour empêcher que toutes les lignes renvoyées.  Pagination empêche la conséquence négative sur le service des requêtes par défaut pour les grands ensembles de données.  Pour renvoyer plus de 50 lignes, utilisez la `Skip` et `Take` méthode, comme décrit dans [renvoyer les données dans les pages].

###<a name="filtering"></a>Comment : filtrer les données retournées

Le code suivant illustre comment filtrer les données en incluant une `Where` clause d’une requête. Il retourne tous les éléments de `todoTable` dont `Complete` propriété est égale à `false`. La fonction [dans laquelle] s’applique un prédicat de la requête sur la table de filtrage des lignes.

    // This query filters out completed TodoItems and items without a timestamp.
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

Vous pouvez afficher l’URI de la demande envoyée sur le serveur principal à l’aide du logiciel de contrôle du message, tels que les outils de développement de navigateur ou de [Fiddler]. Si vous examinez l’URI de requête, notez que la chaîne de requête est modifiée :

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Cette requête OData est traduite dans une requête SQL par le Kit de développement de serveur :

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0

La fonction qui est passée à la `Where` méthode peut avoir un nombre quelconque de conditions.

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
       .ToListAsync();

Cet exemple serait traduit dans une requête SQL par le Kit de développement de serveur :

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

Cette requête peut également être fractionnée en plusieurs clauses :

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

Les deux méthodes sont équivalentes et peuvent être utilisés indifféremment.  La première option&mdash;de la concaténation de plusieurs prédicats dans une seule requête&mdash;est plus compact et plus recommandé.

Le `Where` clause prend en charge les opérations d’être traduit dans le sous-ensemble OData. Les opérations sont les suivantes :

* Opérateurs relationnels (==, ! =, <, < =, >, > =),
* Opérateurs arithmétiques (+, -, /, *, %),
* Numéro de précision (Math.Floor, Math.Ceiling),
* Fonctions de chaîne (longueur, Substring, remplacer, IndexOf, StartsWith, EndsWith),
* Propriétés de la date (année, mois, jour, heure, Minute, seconde)
* Accéder aux propriétés d’un objet, et
* Expressions combinant une de ces opérations.

Lorsque vous envisagez ce que le Kit de développement de serveur prend en charge, vous pouvez envisager de la [Documentation de v3 OData].

###<a name="sorting"></a>Comment : trier les données retournées

Le code suivant montre comment trier les données en incluant une fonction [OrderBy] ou [OrderByDescending] dans la requête. Elle renvoie les éléments de `todoTable` trié par ordre croissant par le `Text` champ.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Comment : retourner des données dans les pages

Par défaut, le serveur principal renvoie uniquement les 50 premières lignes. Vous pouvez augmenter le nombre de lignes retournées par l’appel de la méthode de [prendre] . Utilisez `Take` ainsi que la méthode [Skip] pour demander un spécifique « page » du groupe de données total retourné par la requête. La requête suivante, lorsqu’elle est exécutée, retourne les éléments de trois principaux dans la table.

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

La requête modifiée suivante ignore les trois premiers résultats et renvoie les résultats de trois. Cette requête produit la deuxième « page » de données, où la taille de page est de trois éléments.

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

La méthode [IncludeTotalCount] demande le nombre total pour _tous_ les enregistrements qui aurait été retournés, en ignorant toute clause limite / d’échange spécifiée :

    query = query.IncludeTotalCount();

Dans une application réelle, vous pouvez utiliser des requêtes semblables à l’exemple précédent avec un contrôle pager ou d’une interface utilisateur comparable à naviguer entre les pages.

>[AZURE.NOTE]Pour remplacer la limite de 50-ligne dans un back-end de l’application Mobile, vous devez également appliquer [EnableQueryAttribute] à la méthode GET publique et spécifier le comportement de pagination. Lorsqu’il est appliqué à la méthode, l’exemple suivant définit le maximum renvoyé les lignes à 1000 :
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Comment : sélectionner des colonnes spécifiques

Vous pouvez spécifier le jeu de propriétés à inclure dans les résultats en ajoutant une clause [Select] à votre requête. Par exemple, le code suivant montre comment sélectionner qu’un seul champ et également comment sélectionner et mettre en forme plusieurs champs :

    // Select one field -- just the Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Select multiple fields -- both Complete and Text info
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}",
                        todoItem.Text.PadRight(30), todoItem.Complete ?
                        "Now complete!" : "Incomplete!"));
    List<string> items = await query.ToListAsync();

Toutes les fonctions décrites jusqu’ici sont additifs, donc nous pouvons conserver liant. Chaque appel chaînée affecte plus de la requête. Un autre exemple :

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Comment : rechercher des données par ID

La fonction [LookupAsync] peut être utilisée pour rechercher des objets à partir de la base de données avec un ID particulier.

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Comment : exécuter des requêtes non typés

Lors de l’exécution d’une requête à l’aide d’un objet table non typé, vous devez spécifier explicitement la chaîne de requête OData en appelant [ReadAsync], comme dans l’exemple suivant :

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vous obtenez en retour des valeurs JSON que vous pouvez utiliser comme un sac de propriétés. Pour plus d’informations sur JToken et Json.NET de Newtonsoft, consultez le site [Json.NET] .

### <a name="inserting"></a>Comment : insérer des données dans un back-end de l’application Mobile

Tous les types de client doivent contenir un membre nommé **Id**, qui est par défaut une chaîne. Ce **code** est requis pour effectuer des opérations CRUD et pour la synchronisation hors connexion. Le code suivant illustre comment utiliser la méthode [InsertAsync] pour insérer de nouvelles lignes dans une table. Le paramètre contient les données à insérer sous la forme d’un objet .NET.

    await todoTable.InsertAsync(todoItem);

Si une valeur d’ID unique personnalisée n’est pas incluse dans le `todoItem` au cours d’une opération d’insertion, un GUID est généré par le serveur.
Vous pouvez récupérer l’Id généré en examinant l’objet après le retour de l’appel.

Pour insérer les données non typées, vous pouvez tirer parti de Json.NET :

    JObject jo = new JObject();
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

Voici un exemple d’utilisation d’une adresse de messagerie sous la forme d’un id de chaîne unique :

    JObject jo = new JObject();
    jo.Add("id", "myemail@emaildomain.com");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

### <a name="working-with-id-values"></a>Utilisation de valeurs d’ID

Applications Mobile prend en charge les valeurs de chaîne personnalisées uniques pour la colonne **id** de la table. Une valeur de chaîne permet aux applications d’utiliser des valeurs personnalisées telles que les adresses de messagerie ou noms d’utilisateur pour le code.  ID de chaîne vous offrent les avantages suivants :

* ID sont générés sans effectuer un aller-retour à la base de données.
* Les enregistrements sont plus faciles de fusionner à partir de différentes tables ou bases de données.
* Les valeurs ID peuvent mieux intégrer avec une logique de l’application.

Lorsqu’une valeur d’ID de chaîne n’est pas définie sur un enregistrement inséré, le back-end de l’application Mobile génère une valeur unique pour le code. Vous pouvez utiliser la méthode [Guid.NewGuid] pour générer vos propres valeurs d’ID, soit sur le client, ou dans le système principal.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Comment : modifier des données dans un back-end de l’application Mobile

Le code suivant illustre comment utiliser la méthode [UpdateAsync] pour mettre à jour un enregistrement existant avec le même code avec les nouvelles informations. Le paramètre contient les données de mise à jour sous la forme d’un objet .NET.

    await todoTable.UpdateAsync(todoItem);

Pour mettre à jour de données non typées, vous pouvez profiter de [Json.NET] comme suit :

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Un `id` champ doit être spécifié lorsque vous effectuez une mise à jour. Le serveur principal utilise le `id` champ afin d’identifier les lignes à mettre à jour. Le `id` champ peut être obtenu à partir du résultat de la `InsertAsync` appeler. Un `ArgumentException` est levée si vous essayez de mettre à jour un élément sans fournir le `id` valeur.

###<a name="deleting"></a>Comment : supprimer des données dans un back-end de l’application Mobile

Le code suivant illustre comment utiliser la méthode [DeleteAsync] pour supprimer une instance existante. L’instance est identifiée par le `id` champ d’ensemble de le `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Pour supprimer les données non typées, vous pouvez profiter de Json.NET comme suit :

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Lorsque vous effectuez une demande de suppression, un ID doit être spécifié. Autres propriétés ne sont pas transmises au service ou sont ignorées au niveau du service. Le résultat d’une `DeleteAsync` appel est généralement `null`. L’ID de passer peut être obtenu à partir du résultat de la `InsertAsync` appeler. A `MobileServiceInvalidOperationException` est levée lorsque vous essayez de supprimer un élément sans spécifier le `id` champ.

###<a name="optimisticconcurrency"></a>Comment : utiliser l’accès concurrentiel optimiste pour la résolution de conflit

Deux ou plusieurs clients peuvent écrire des modifications au même élément en même temps. Sans la détection de conflit, la dernière écriture remplace les précédentes mises à jour. **Contrôle d’accès concurrentiel optimiste** suppose que chaque transaction peut valider et donc n’utilise pas de verrouillage de la ressource.  Avant de valider une transaction, contrôle d’accès concurrentiel optimiste vérifie qu’aucune autre transaction n’a modifié les données. Si les données ont été modifiées, la transaction de validation est annulée.

Applications Mobile prend en charge le contrôle de concurrence optimiste par le suivi des modifications pour chaque élément à l’aide de la `version` colonne système de propriété qui est définie pour chaque table de la principale application Mobile. Définit des applications Mobile de chaque mise à jour d’un enregistrement, le `version` à l’enregistrement d’une nouvelle valeur de propriété. Lors de chaque demande de mise à jour, la `version` propriété de l’enregistrement de la demande est comparée à la même propriété pour l’enregistrement sur le serveur. Si la version est passé avec la requête ne correspond pas à la principale, puis la bibliothèque cliente déclenche une `MobileServicePreconditionFailedException<T>` exception. Le type de l’exception est l’enregistrement à partir du serveur principal contenant la version des serveurs de l’enregistrement. L’application peut ensuite utiliser ces informations pour décider s’il faut exécuter la requête de mise à jour avec le bon `version` la valeur à partir du serveur principal pour valider les modifications.

Définir une colonne sur la classe de table pour le `version` propriété système pour activer l’accès concurrentiel optimiste. Par exemple :

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public string Version { set; get; }
    }


Les applications qui utilisent des tables non typés permettent l’accès concurrentiel optimiste en définissant le `Version` indicateur sur le `SystemProperties` de la table comme suit.

    //Enable optimistic concurrency by retrieving version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Outre l’activation de l’accès concurrentiel optimiste, vous devez également intercepter la `MobileServicePreconditionFailedException<T>` exception dans votre code lors de l’appel [UpdateAsync].  Résoudre le conflit en appliquant la bonne `version` pour l’enregistrement mis à jour et appel [UpdateAsync] avec l’enregistrement résolu. Le code suivant montre comment résoudre un conflit d’écriture a détecté qu’une seule fois :

    private async void UpdateToDoItem(TodoItem item)
    {
        MobileServicePreconditionFailedException<TodoItem> exception = null;

        try
        {
            //update at the remote table
            await todoTable.UpdateAsync(item);
        }
        catch (MobileServicePreconditionFailedException<TodoItem> writeException)
        {
            exception = writeException;
        }

        if (exception != null)
        {
            // Conflict detected, the item has changed since the last query
            // Resolve the conflict between the local and server item
            await ResolveConflict(item, exception.Item);
        }
    }


    private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
    {
        //Ask user to choose the resoltion between versions
        MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

        UICommand localBtn = new UICommand("Commit Local Text");
        UICommand ServerBtn = new UICommand("Leave Server Text");
        msgDialog.Commands.Add(localBtn);
        msgDialog.Commands.Add(ServerBtn);

        localBtn.Invoked = async (IUICommand command) =>
        {
            // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
            // catching a MobileServicePreConditionFailedException.
            localItem.Version = serverItem.Version;

            // Updating recursively here just in case another change happened while the user was making a decision
            UpdateToDoItem(localItem);
        };

        ServerBtn.Invoked = async (IUICommand command) =>
        {
            RefreshTodoItems();
        };

        await msgDialog.ShowAsync();
    }

Pour plus d’informations, consultez la rubrique de la [Synchronisation des données hors connexion dans les applications mobiles Azure] .

###<a name="binding"></a>Comment : données d’applications Mobile de liaison pour une interface utilisateur Windows

Cette section indique comment afficher les objets de données retournées à l’aide des éléments d’interface utilisateur dans une application Windows.  L’exemple de code suivant crée une liaison à la source de la liste avec une requête pour des éléments incomplets. Le [MobileServiceCollection] crée une collection de liaison Mobile applications prenant en charge.

    // This query filters out completed TodoItems.
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;

    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Certains contrôles dans le runtime managé prend en charge une interface appelée [ISupportIncrementalLoading]. Cette interface permet de demander des données supplémentaires lorsque l’utilisateur fait défiler les contrôles. Il existe une prise en charge intégrée pour cette interface pour les applications Windows universels via [MobileServiceIncrementalLoadingCollection], qui gère automatiquement les appels à partir des contrôles. Utilisez `MobileServiceIncrementalLoadingCollection` dans les applications Windows comme suit :

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Pour utiliser la nouvelle collection d’applications Windows Phone 8 et « Silverlight », les `ToCollection` les méthodes d’extension sur `IMobileServiceTableQuery<T>` et `IMobileServiceTable<T>`. Pour charger les données, appelez `LoadMoreItemsAsync()`.

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
    await items.LoadMoreItemsAsync();

Lorsque vous utilisez la collection créée par l’appel de `ToCollectionAsync` ou `ToCollection`, vous obtenez une collection qui peut être liée à des contrôles d’interface utilisateur.  Cette collection est prenant en charge la pagination.  Étant donné que la collection est du chargement de données à partir du réseau, parfois de chargement échoue. Pour gérer de tels échecs, substituer le `OnException` méthode sur `MobileServiceIncrementalLoadingCollection` pour gérer les exceptions levées par des appels à `LoadMoreItemsAsync`.

Tenez compte si votre table comporte plusieurs champs, mais vous souhaitez uniquement afficher certains d'entre eux dans votre contrôle. Vous pouvez utiliser les instructions dans la section précédente «[Sélectionner des colonnes spécifiques](#selecting)» pour sélectionner les colonnes spécifiques à afficher dans l’interface utilisateur.

###<a name="pagesize"></a>Modifier la taille de la Page

Les applications Mobile Azure renvoie un maximum de 50 articles par demande par défaut.  Vous pouvez modifier la taille de pagination en augmentant la taille de page maximale sur le client et le serveur.  Pour augmenter la taille de page demandée, spécifiez `PullOptions` lors de l’utilisation `PullAsync()`:

    PullOptions pullOptions = new PullOptions
        {
            MaxPageSize = 100
        };

En supposant que vous avez apporté la `PageSize` égal ou supérieur à 100 sur le serveur, une requête renvoie jusqu'à 100 éléments.

##<a name="#offlinesync"></a>Travailler avec des Tables en mode hors connexion

Les tables en mode hors connexion utilisent un à un magasin de données SQLite local pour une utilisation en mode hors connexion.  Tableau de toutes les opérations sont effectuées par rapport à l’ordinateur local SQLite stocker et non le magasin du serveur distant.  Pour créer une table en mode hors connexion, tout d’abord préparer votre projet :

1. Dans Visual Studio, la solution avec le bouton droit > **Manage NuGet Packages de Solution...**, puis de rechercher et d’installer le package NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** pour tous les projets dans la solution.

2. (Facultatif) Pour prendre en charge les périphériques Windows, installez un des packages de runtime de SQLite suivants :

    * **Windows 8.1 Runtime :** Installer [SQLite pour Windows 8.1][3].
    * **Windows Phone 8.1 :** Installer [SQLite pour Windows Phone 8.1][4].
    * **Plate-forme Windows universel** Installer [SQLite l’universel universel de Windows][5].

3. (Facultatif). Pour les périphériques Windows, cliquez sur **références** > **Ajouter une référence**, développez le dossier **Windows** > **Extensions**, puis activer approprié **SQLite pour Windows** SDK ainsi que le Kit de développement **2013 Runtime pour Windows de Visual C++** .
    Les noms de SQLite SDK varient légèrement chaque plate-forme Windows.

Avant de pouvoir créer une référence de table, le magasin local doit être préparé :

    var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
    store.DefineTable<TodoItem>();

    //Initializes the SyncContext using the default IMobileServiceSyncHandler.
    await this.client.SyncContext.InitializeAsync(store);

L’initialisation du magasin est normalement effectuée immédiatement après que le client est créé.  **OfflineDbPath** doit être un nom de fichier adapté à une utilisation sur toutes les plates-formes prises en charge.  Si le chemin d’accès est un chemin d’accès qualifié complet (autrement dit, il commence par une barre oblique), ce chemin d’accès est alors utilisée.  Si le chemin d’accès n’est pas qualifié, le fichier est placé dans un emplacement spécifique de la plate-forme.

* Pour les périphériques iOS ou Android, le chemin d’accès par défaut est le dossier « Fichiers personnels ».
* Pour les périphériques Windows, le chemin d’accès par défaut est le dossier « AppData » spécifiques à l’application.

Une référence de table peut être obtenue à l’aide de la `GetSyncTable<>` méthode :

    var table = client.GetSyncTable<TodoItem>();

Vous n’avez pas besoin de s’authentifier pour utiliser une table en mode hors connexion.  Il vous suffit de vous authentifier lorsque vous communiquez avec le service back-end.

###<a name="syncoffline"></a>La synchronisation d’une Table en mode hors connexion

Les tables en mode hors connexion ne sont pas synchronisées avec le serveur principal par défaut.  Synchronisation est divisée en deux.  Vous pouvez distribuer des modifications séparément de télécharger de nouveaux éléments.  Voici une méthode de synchronisation type :

    public async Task SyncAsync()
    {
        ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

        try
        {
            await this.client.SyncContext.PushAsync();

            await this.todoTable.PullAsync(
                //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
                //Use a different query name for each unique query in your program
                "allTodoItems",
                this.todoTable.CreateQuery());
        }
        catch (MobileServicePushFailedException exc)
        {
            if (exc.PushResult != null)
            {
                syncErrors = exc.PushResult.Errors;
            }
        }

        // Simple error/conflict handling. A real application would handle the various errors like network conditions,
        // server conflicts and others via the IMobileServiceSyncHandler.
        if (syncErrors != null)
        {
            foreach (var error in syncErrors)
            {
                if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                {
                    //Update failed, reverting to server's copy.
                    await error.CancelAndUpdateItemAsync(error.Result);
                }
                else
                {
                    // Discard local change.
                    await error.CancelAndDiscardItemAsync();
                }

                Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
            }
        }
    }

Si le premier argument de `PullAsync` a la valeur null, puis la synchronisation incrémentielle n’est pas utilisée.  Chaque opération de synchronisation récupère tous les enregistrements.

Le Kit de développement logiciel effectue un implicite `PushAsync()` avant l’extraction des enregistrements.

Gestion de conflit se produit sur un `PullAsync()` méthode.  Vous pouvez traiter les conflits de la même manière que les tables en ligne.  Le conflit se produit lors de la `PullAsync()` est appelée au lieu d’au cours de l’insert, update ou delete. Si plusieurs conflits se produisent, ils sont regroupés dans une seule MobileServicePushFailedException.  Gérer chaque défaillance séparément.

##<a name="#customapi"></a>Travailler avec une API personnalisée

Une API personnalisée vous permet de vous permet de définir des points de terminaison personnalisés qui exposent les fonctionnalités de serveur qui ne pas mapper à une opération d’insertion, mise à jour, supprimer ou opération de lecture. À l’aide d’une API personnalisée, vous avez davantage de contrôle sur la messagerie, notamment pour lire et définir des en-têtes HTTP message et définition d’un format de corps de message que JSON.

Vous appelez une API personnalisée en appelant une des méthodes [InvokeApiAsync] sur le client. Par exemple, la ligne de code suivante envoie une demande POST à l' **completeAll** API sur le serveur principal :

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Ce formulaire est un appel de méthode typée et nécessite que le type de retour de **MarkAllResult** est défini. Méthodes typés et non typés sont pris en charge.

##<a name="authentication"></a>Authentifier les utilisateurs

Applications Mobile prend en charge l’authentification et l’autorisation des utilisateurs de l’application à l’aide de différents fournisseurs d’identité externes : Facebook, Google, Microsoft Account, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables pour restreindre l’accès à des opérations spécifiques aux utilisateurs authentifiés uniquement. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour mettre en œuvre des règles d’autorisation dans les scripts de serveur. Pour plus d’informations, consultez le didacticiel [authentification ajouter à votre application].

Deux flux d’authentification sont pris en charge : flux _gérés par le client_ et le _serveur géré_ . Le flux géré par le serveur fournit l’expérience d’authentification la plus simple, car il repose sur l’interface du fournisseur web d’authentification. Le flux géré par le client permet une intégration plus poussée avec des fonctionnalités spécifiques au périphérique comme il s’appuie sur les kits de développement logiciel spécifique au fournisseur spécifique à l’appareil.

>[AZURE.NOTE] Nous vous recommandons d’utiliser un flux géré par le client dans vos applications de production.

Pour configurer l’authentification, vous devez inscrire votre application avec un ou plusieurs fournisseurs d’identité.  Le fournisseur d’identité génère un ID de client et un code secret client pour votre application.  Ces valeurs sont ensuite définies dans votre back-end pour activer le Service d’application Azure d’authentification/autorisation.  Pour plus d’informations, suivez les instructions détaillées dans le didacticiel [authentification ajouter à votre application].

Les rubriques suivantes sont traitées dans cette section :

+ [Authentification de client géré](#clientflow)
+ [Géré par le serveur de l’authentification](#serverflow)
+ [Le jeton d’authentification de la mise en cache](#caching)

###<a name="clientflow"></a>Authentification de client géré

Votre application peut indépendamment de contacter le fournisseur d’identité et ensuite fournir le jeton retourné lors de la connexion avec votre serveur principal. Ce flux client vous permet de fournir une expérience d’ouverture de session unique pour les utilisateurs ou pour extraire des données de l’utilisateur supplémentaires à partir du fournisseur d’identité. Flux client est préférée à l’utilisation d’un flux du serveur comme le fournisseur d’identité SDK fournit un aspect UX plus natif et permet la personnalisation supplémentaire.

Les exemples sont fournis pour les modèles d’authentification client-flux suivants :

+ [Bibliothèque de l’authentification Active Directory](#adal)
+ [Facebook ou Google](#client-facebook)
+ [Le Live SDK](#client-livesdk)

#### <a name="adal"></a>Authentifier les utilisateurs avec la bibliothèque d’authentification Active Directory

Vous pouvez utiliser la bibliothèque de l’authentification Active Directory (ADAL) pour l’authentification de l’utilisateur de lancer à partir du client à l’aide de l’authentification Active Directory de Azure.

1. Configurer votre back-end de l’application mobile pour DAS-ouverture de session en suivant le didacticiel [comment configurer le Service d’application pour la connexion de Active Directory] . Veillez à terminer l’étape facultative de l’inscription d’une application client natif.
2. Dans Visual Studio ou Xamarin Studio, ouvrez votre projet et ajoutez une référence à la `Microsoft.IdentityModel.CLients.ActiveDirectory` package NuGet. Lors de la recherche, inclure des versions préliminaires.
3. Ajoutez le code suivant à votre application, en fonction de la plate-forme que vous utilisez. Dans chaque cas, vérifiez les remplacements suivants :

    * Remplacer **l’Insertion-autorité-ici** avec le nom du locataire dans lequel vous déployé votre application. Le format doit être https://login.windows.net/contoso.onmicrosoft.com. Cette valeur peut être copiée à partir de l’onglet domaine dans Azure Active Directory dans [Azure portal classique].
    * Remplacez **Insertion-RESOURCE-ID-ici** l’ID client pour votre back-end de l’application mobile. Vous pouvez obtenir l’ID de client à partir de l’onglet **Avancé** , sous **Les paramètres Active Directory Azure** dans le portail.
    * Remplacer **l’Insertion-CLIENT-ID-ici** avec l’ID de client que vous avez copié à partir de l’application client natif.
    * Remplacer **l’Insertion-redirection-URI-ici** avec le point de terminaison de votre site _/.auth/login/done_ , à l’aide du schéma HTTPS. Cette valeur doit être similaire à _https://contoso.azurewebsites.net/.auth/login/done_.

    Le code nécessaire pour chaque plate-forme suit :

    **Windows :**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                    AuthenticationContext ac = new AuthenticationContext(authority);
                    AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                        new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                    JObject payload = new JObject();
                    payload["access_token"] = ar.AccessToken;
                    user = await App.MobileService.LoginAsync(
                        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    **Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(view));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    **Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(this));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.SetMessage(ex.Message);
                builder.SetTitle("You must log in. Login Required");
                builder.Create().Show();
            }
        }
        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
        }

####<a name="client-facebook"></a>De l’authentification unique à l’aide d’un jeton de Facebook ou Google

Vous pouvez utiliser le flux client comme indiqué dans cet extrait de code pour Facebook ou Google.

    var token = new JObject();
    // Replace access_token_value with actual value of your access token obtained
    // using the Facebook or Google SDK.
    token.Add("access_token", "access_token_value");

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Change MobileServiceAuthenticationProvider.Facebook
                // to MobileServiceAuthenticationProvider.Google if using Google auth.
                user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

####<a name="client-livesdk"></a>Seul signe à l’aide de Microsoft Account avec le SDK Live

Pour authentifier les utilisateurs, vous devez inscrire votre application au compte Microsoft Developer Center. Configurez les détails de l’enregistrement sur votre back-end de l’application Mobile. Pour créer un enregistrement de compte Microsoft et le connecter à votre back-end de l’application Mobile, effectuez les étapes dans [le livre de votre application pour utiliser une connexion de compte Microsoft]. Si vous avez des versions 8/Silverlight à la fois Windows Store et Windows Phone de votre application, tout d’abord par enregistrer la version du Windows Store.

Le code suivant s’authentifie à l’aide de Live SDK et utilise le jeton retourné pour vous connecter à votre back-end de l’application Mobile.

    private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
            // The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

Pour plus d’informations, consultez la documentation du [Kit de développement logiciel de Windows Live] .

###<a name="serverflow"></a>Géré par le serveur de l’authentification

Une fois que vous avez enregistré votre fournisseur d’identité, appelez la méthode [LoginAsync] sur le [MobileServiceClient] avec la valeur de [MobileServiceAuthenticationProvider] de votre fournisseur. Par exemple, le code suivant lance une serveur flux sign-in à l’aide de Facebook.

    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

Si vous utilisez un fournisseur d’identité que Facebook, modifiez la valeur de [MobileServiceAuthenticationProvider] à la valeur de votre fournisseur.

Dans un flux du serveur, Service d’application Azure gère le flux d’authentification OAuth en affichant la page de connexion du fournisseur sélectionné.  Une fois les retours fournisseur identité, Service d’application Azure génère un jeton d’authentification de Service de l’application. La méthode [LoginAsync] renvoie un [MobileServiceUser], qui fournit à la fois [nom d’utilisateur] de l’utilisateur authentifié et le [MobileServiceAuthenticationToken], en tant que JSON web jeton (JWT). Ce jeton peut être mis en cache et réutilisé jusqu'à son expiration. Pour plus d’informations, consultez [mise en cache du jeton d’authentification](#caching).

###<a name="caching"></a>Le jeton d’authentification de la mise en cache

Dans certains cas, l’appel à la méthode de connexion peut être évitée après la première authentification réussie en stockant le jeton d’authentification à partir du fournisseur.  Les applications Windows Store et UWP peuvent utiliser [PasswordVault] pour mettre en cache le jeton d’authentification en cours après une réussite sign-in, comme suit :

    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
        client.currentUser.MobileServiceAuthenticationToken));

La valeur de l’ID utilisateur est stockée comme le nom d’utilisateur de l’information d’identification et que le jeton est stockées en tant que le mot de passe. Sur les start-up, vous pouvez vérifier l' **PasswordVault** des informations d’identification mises en cache. L’exemple suivant utilise les informations d’identification mises en cache lorsqu’ils se trouvent et sinon essaie de s’authentifier à nouveau avec le serveur principal :

    // Try to retrieve stored credentials.
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        // Create the current user from the stored credentials.
        client.currentUser = new MobileServiceUser(creds.UserName);
        client.currentUser.MobileServiceAuthenticationToken =
            vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Regular login flow and cache the token as shown above.
    }

Lorsque vous déconnectez un utilisateur, vous devez également supprimer les informations d’identification stockées, comme suit :

    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));

Applications de Xamarin permet de stocker en toute sécurité les informations d’identification dans un objet de **compte** l’API [Xamarin.Auth] . Pour obtenir un exemple d’utilisation de ces API, consultez le fichier de code [AuthStore.cs] de la [photo de ContosoMoments exemple de partage].

Lorsque vous utilisez l’authentification de client géré, vous pouvez également mettre en cache le jeton d’accès obtenu à partir de votre fournisseur de Facebook ou Twitter. Ce jeton peut être fourni pour demander un nouveau jeton d’authentification du serveur principal, comme suit :

    var token = new JObject();
    // Replace <your_access_token_value> with actual value of your access token
    token.Add("access_token", "<your_access_token_value>");

    // Authenticate using the access token.
    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);

##<a name="pushnotifications"></a>Notifications de type Pousser

Les rubriques suivantes décrivent les pousser des Notifications :

* [S’inscrire pour les Notifications de type Pousser](#register-for-push)
* [Obtenir un identificateur de sécurité du package Windows Store](#package-sid)
* [Enregistrer avec les modèles multiplates-formes](#register-xplat)

###<a name="register-for-push"></a>Comment : inscrire pour les Notifications de type Pousser

Le client d’applications Mobile vous permet de vous inscrire pour les notifications de transmission avec Azure Notification concentrateurs. Lors de l’enregistrement, vous obtenez un descripteur que vous obtenez à partir de la propre à la plate-forme Push Notification Service (solution). Vous fournissez ensuite cette valeur avec des balises lorsque vous créez l’enregistrement. Le code suivant enregistre votre application Windows pour les notifications de transmission avec le Service de Notification de Windows (WNS) :

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Si vous poussez à WNS, vous devez [obtenir un package de Windows Store SID](#package-sid).  Pour plus d’informations sur les applications Windows, y compris l’inscription des enregistrements de modèle, reportez-vous à la section [Ajouter des notifications de type pousser à votre application].

Demande des balises à partir du client n’est pas pris en charge.  Demandes de balise sont supprimés en mode silencieux à partir de l’enregistrement.
Si vous souhaitez inscrire votre appareil avec des balises, créez une API personnalisée qui utilise l’API de concentrateurs de Notification pour effectuer l’inscription de votre part.  [Appeler l’API personnalisé](#customapi) à la place de la `RegisterNativeAsync()` méthode.

###<a name="package-sid"></a>Comment : obtenir un package de Windows Store SID

Un package de SID est nécessaire pour activer les notifications de transmission dans des applications du Windows Store.  Pour recevoir un lot SID, inscrire votre application avec le Windows Store.

Pour obtenir cette valeur :

1. Dans l’Explorateur de solutions de Visual Studio, le projet d’application Windows Store avec le bouton droit, cliquez sur **banque de** > **Associer une application à la banque...**.
2. Dans l’Assistant, cliquez sur **suivant**, connectez-vous à votre compte Microsoft, tapez un nom pour votre application en **réserve un nouveau nom de l’application**, puis cliquez sur **réserver**.
3. Après que l’inscription de l’application est créée avec succès, sélectionnez le nom de l’application et cliquez sur **suivant**, puis cliquez sur **associer**.
4. Ouvrez une session sur le [Centre de développement Windows] à l’aide de votre Account de Microsoft. Sous **Mes applications**, cliquez sur l’enregistrement de l’application que vous avez créé.
5. Cliquez sur **gestion de l’application** > **d’identité de l’application**et puis faites défiler vers le bas pour rechercher votre **Package SID**.

Nombreuses utilisations du package SID traitent comme un URI, auquel cas vous devez utiliser _ms-app : / /_ comme modèle. Prenez note de la version de votre package SID formé par la concaténation de cette valeur en tant que préfixe.

Les applications Xamarin requièrent du code supplémentaire pour pouvoir enregistrer une application en cours d’exécution sur l’iOS ou Android plates-formes. Pour plus d’informations, consultez la rubrique pour votre plate-forme :

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Comment : modèles de push de Registre pour envoyer des notifications de multiplates-formes

Pour enregistrer les modèles, utilisez la `RegisterAsync()` méthode avec les modèles, comme suit :

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

Vos modèles doivent être `JObject` types et peut contenir plusieurs modèles au format JSON suivant :

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

La méthode **RegisterAsync()** accepte également les vignettes secondaires :

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Toutes les balises sont supprimés lors de l’inscription pour la sécurité. Pour ajouter des balises aux installations ou aux modèles dans les installations, reportez-vous à la section [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure].

Pour envoyer des notifications utilisant ces modèles enregistrés, reportez-vous à l' [API de concentrateurs de Notification].

##<a name="misc"></a>Rubriques diverses

###<a name="errors"></a>Comment : gérer les erreurs

Lorsqu’une erreur se produit dans le système principal, le client SDK déclenche un `MobileServiceInvalidOperationException`.  L’exemple suivant montre comment gérer une exception qui est retournée par le serveur principal :

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // This code inserts a new TodoItem into the database. When the operation completes
        // and App Service has assigned an Id, the item is added to the CollectionView
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Handle error
        }
    }

Vous trouverez un autre exemple de traitement des erreurs dans les [Exemples de fichiers d’applications Mobile]. L’exemple [LoggingHandler] fournit un gestionnaire de délégué de journalisation (ci-dessous) pour enregistrer les demandes en cours sur le serveur principal.

###<a name="headers"></a>Comment : personnaliser les en-têtes de demande

Pour prendre en charge votre scénario d’application spécifique, vous devrez peut-être personnaliser la communication avec le serveur principal chargé de l’application Mobile. Par exemple, vous voulez ajouter un en-tête personnalisé à toutes les demandes sortantes ou même modifier les codes d’état de réponse. Vous pouvez utiliser un personnalisée [DelegatingHandler], comme dans l’exemple suivant :

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Ajouter l’authentification à votre application]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md
[Ajouter des notifications de type pousser à votre application]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Enregistrer votre application pour utiliser une connexion de compte Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Comment faire pour configurer le Service de l’application pour la connexion Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[Crée une référence à une table non typée]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[Prendre]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[Sélectionnez]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[Ignorer]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[ID utilisateur]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Où]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[Azure portal]: https://portal.azure.com/
[Azure portal classique]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centre de développement Windows]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Kit de développement logiciel Windows Live]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[API de concentrateurs de notification]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Exemples de fichiers d’applications mobiles]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Documentation de v3 OData]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: (https://github.com/azure-appservice-samples/ContosoMoments/blob/dev/src/Mobile/ContosoMoments/Helpers/AuthStore.cs)
[Exemple de partage de photos ContosoMoments]: https://github.com/azure-appservice-samples/ContosoMoments