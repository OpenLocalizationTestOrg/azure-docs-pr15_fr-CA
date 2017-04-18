##<a name="create-client"></a>Créer une connexion Client

Créer une connexion client en créant un `WindowsAzure.MobileServiceClient` objet.  Remplacer `appUrl` par l’URL de votre application Mobile.

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

##<a name="table-reference"></a>Travailler avec les Tables

À l’accès ou mise à jour des données, créer une référence à la table principale. Remplacer `tableName` avec le nom de votre table.

```
var table = client.getTable(tableName);
```

Une fois que vous avez une référence de table, vous pouvez travailler avec la table :

* [Une Table de requête](#querying)
  * [Filtrage des données](#table-filter)
  * [Pagination de données](#table-paging)
  * [Tri des données](#sorting-data)
* [Insertion de données](#inserting)
* [Modification de données](#modifying)
* [Suppression de données](#deleting)

###<a name="querying"></a>Comment : interroger une référence de Table

Une fois que vous avez une référence de table, vous pouvez l’utiliser pour rechercher des données sur le serveur.  Les requêtes sont effectuées dans un langage de type « LINQ ».
Pour renvoyer toutes les données de la table, utilisez la commande suivante :

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

La fonction de réussite est appelée avec les résultats.   N’utilisez pas `for (var i in results)` dans la réussite fonctionner comme qui effectuera une itération sur les informations incluses dans les résultats lorsque autres fonctions de requête (tels que `.includeTotalCount()`) sont utilisés.

Pour plus d’informations sur la syntaxe de requête, reportez-vous à la [documentation d’objet de requête].

####<a name="table-filter"></a>Filtrage des données sur le serveur

Vous pouvez utiliser un `where` clause sur la référence de table :

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Vous pouvez également utiliser une fonction qui permet de filtrer l’objet.  Dans ce cas le `this` variable est assignée à l’objet actuel est en cours de filtrage.  Le texte suivant est fonctionnellement équivalent à l’exemple précédent :

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

####<a name="table-paging"></a>Pagination de données

Utiliser les méthodes take() et skip().  Par exemple, si vous souhaitez fractionner la table en ligne-100 enregistrements :

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

Le `.includeTotalCount()` méthode est utilisée pour ajouter un champ totalCount à l’objet de résultats.  Le champ totalCount est rempli avec le nombre total d’enregistrements qui serait retournée si aucune pagination n’est utilisée.

Vous pouvez ensuite utiliser la variable de pages et certains boutons de l’interface utilisateur pour fournir une liste de pages ; Utilisez loadPage() pour charger les nouveaux enregistrements pour chaque page.  Vous devez implémenter une sorte de mise en cache pour accélérer l’accès aux enregistrements qui ont déjà été chargées.


####<a name="sorting-data"></a>Comment : retourner les données triées

Utilisez les méthodes de requête .orderBy() ou .orderByDescending() :

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Pour plus d’informations sur l’objet de requête, reportez-vous à la [documentation d’objet de requête].

###<a name="inserting"></a>Comment : insérer des données

Créer un objet JavaScript avec la date appropriée et appeler de façon asynchrone table.insert() :

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Sur la réussite de l’insertion, l’élément inséré est retourné avec les champs supplémentaires qui sont requis pour les opérations de synchronisation.  Vous devez mettre à jour votre cache avec ces informations pour les mises à jour ultérieures.

Notez que les applications Azure Mobile Node.js Server SDK prend en charge le schéma dynamique à des fins de développement.
Dans le cas d’un schéma dynamique, le schéma de la table est mis à jour à la volée, ce qui vous permet d’ajouter des colonnes à la table simplement en les spécifiant dans une insertion ou opération de mise à jour.  Nous vous recommandons de désactiver le schéma dynamique avant de passer votre application en production.

###<a name="modifying"></a>Comment : modifier des données

Similaire à la méthode .insert(), vous devez créer un objet de la mise à jour, puis appelez .update().  L’objet de la mise à jour doit contenir l’ID de l’enregistrement à mettre à jour - il est obtenu lors de la lecture de l’enregistrement ou lors de l’appel de .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

###<a name="deleting"></a>Comment : supprimer des données

Appelez la méthode .del() pour supprimer un enregistrement.  Passez l’ID d’une référence d’objet :

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
