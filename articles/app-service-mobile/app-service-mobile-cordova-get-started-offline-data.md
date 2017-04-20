<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application Mobile de Azure (Cordova) | Microsoft Azure"
    description="Apprenez à utiliser l’application Service Mobile application de cache et la synchronisation des données hors connexion dans votre application de Cordova"
    documentationCenter="cordova"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-cordova-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Activer la synchronisation hors connexion pour votre application mobile Cordova

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Ce didacticiel présente la fonctionnalité de synchronisation hors ligne des applications de Mobile Azure pour Cordova. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;affichage, ajout ou modification de données&mdash;même lorsqu’il n’y a pas de connexion réseau. Les modifications sont stockées dans une base de données locale ; une fois que le périphérique est en ligne, ces modifications sont synchronisées avec le service distant.

Ce didacticiel est basé sur la solution de démarrage rapide de Cordova pour les applications mobiles que vous créez lorsque vous réalisez le didacticiel [rapide à Apache Cordova démarrer]. Dans ce didacticiel, vous mettrez à jour la solution de démarrage rapide pour ajouter les fonctionnalités hors ligne des applications de Mobile Azure. Il met également en surbrillance le code dans l’application hors connexion spécifiques.

Pour en savoir plus sur la fonctionnalité de synchronisation en mode hors connexion, reportez-vous à la rubrique [Synchronisation de données hors connexion dans les applications mobiles Azure]. Pour les détails de l’utilisation de l’API, consultez le fichier [README] dans le plug-in.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Ajouter la synchronisation hors connexion à la solution de démarrage rapide

Le code de synchronisation hors connexion doit être ajouté à l’application. Synchronisation hors connexion nécessite le plug-in cordova-sqlite-stockage, qui est automatiquement ajouté à votre application lorsque le plug-in Azure Mobile Apps est inclus dans le projet. Le projet de démarrage rapide inclut à la fois de ces plug-ins.

1. Dans l’Explorateur de solutions de Visual Studio, ouvrez index.js et remplacez le code suivant

        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend

    avec ce code :

        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context

2. Ensuite, remplacez le code suivant :

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    avec ce code :

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Les compléments de code précédent initialiser la banque locale et définir une table locale qui correspond aux valeurs de colonne utilisés dans votre Azure back-end. (Vous n’avez pas besoin d’inclure toutes les valeurs de colonne dans ce code.)

    Vous obtenez une référence au contexte de synchronisation en appelant **getSyncContext**. Le contexte de synchronisation permet de conserver les relations entre les tables en effectuant le suivi en exécutant un push de modifications dans toutes les tables, qu'une application cliente a modifié lors de la **commande** est appelée.

3. Mettre à jour l’URL de l’application pour l’URL de votre application Mobile App.

4. Ensuite, remplacez ce code :

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    avec ce code :

        // todoItemTable = client.getTable('todoitem');

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Le code précédent initialise le contexte de synchronisation, puis appelle getSyncTable (au lieu de getTable) pour obtenir une référence à la table locale.

    Ce code utilise la base de données locale pour tous les créer, lire, mettre à jour et supprimer des opérations de table (CRUD).

    Cet exemple effectue un simple traitement des erreurs sur les conflits de synchronisation. Une application réelle serait gérer les diverses erreurs telles que les conditions de réseau et d’autres conflits de serveur. Pour des exemples de code, consultez l' [exemple de synchronisation hors connexion].

5. Ensuite, ajoutez cette fonction pour effectuer la synchronisation réelle.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Vous décidez quand envoyer les modifications en appelant **push** sur l’objet **syncContext** utilisé par le client pour le back-end de l’application Mobile. Par exemple, vous pouvez ajouter un appel à **syncBackend** à un gestionnaire d’événements de bouton dans l’application comme un nouveau bouton de synchronisation, ou vous pouvez ajouter l’appel à la fonction **addItemHandler** à synchroniser chaque fois qu’un nouvel élément est ajouté.

##<a name="offline-sync-considerations"></a>Considérations relatives à la synchronisation hors connexion

Dans l’exemple, la méthode **push** de **syncContext** est uniquement appelée dans le démarrage de l’application de la fonction de rappel pour la connexion.  Dans une application réelle, vous pourriez également rendre cette fonctionnalité de synchronisation déclenchée manuellement ou lorsque l’état du réseau change.

Lorsqu’une extraction est exécutée sur une table qui est en attente de mises à jour locales suivis par le contexte, cette opération d’extraction déclenchera automatiquement une diffusion de contexte précédent. Lors de l’actualisation, ajout et l’achèvement des articles dans cet exemple, vous pouvez omettre l’appel explicite de **push** , puisqu’il peut être redondante (première vérification du [fichier Lisezmoi] pour l’état actuel de la fonctionnalité).

Dans le code fourni, tous les enregistrements dans la table todoItem à distance sont interrogés, mais il est également possible de filtrer les enregistrements en passant un id de requête et la requête **d’envoi**. Pour plus d’informations, reportez-vous à la section *Synchronisation incrémentielle* dans la [Synchronisation des données hors connexion dans les applications mobiles Azure].

## <a name="optional-disable-authentication"></a>(Facultatif) Désactivation de l’authentification

Si vous n’avez pas déjà défini l’authentification et que vous ne souhaitez pas configurer une authentification avant de tester la synchronisation en mode hors connexion, commentez la fonction de rappel pour la connexion, mais laissez le code à l’intérieur de la fonction de rappel supprimées.

Le code doit ressembler à ceci après leur transformation en commentaire les lignes de connexion.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Maintenant, l’application sera synchronisée avec le serveur principal Azure automatiquement lorsque vous exécutez l’application et non lorsque vous êtes connecté.

## <a name="run-the-client-app"></a>Exécutez l’application client

Avec la synchronisation hors connexion maintenant activée, vous pouvez maintenant exécuter l’application cliente au moins une fois sur chaque plate-forme pour remplir la base de données de la banque locale. Par la suite, vous simuler un scénario en mode hors connexion et modifier les données dans le magasin local lors de l’application est en mode hors connexion.

## <a name="optional-test-the-sync-behavior"></a>(Facultatif) Tester le comportement de synchronisation

Dans cette section, vous allez modifier le projet de client pour simuler un scénario en mode hors connexion à l’aide d’une URL d’application non valide pour votre serveur principal. Lorsque vous ajoutez ou modifiez des éléments de données, ces modifications seront stockées dans le magasin local, mais non synchronisées au magasin de données back-end, jusqu'à ce que la connexion est rétablie.

1. Dans l’Explorateur de solutions, ouvrez le fichier de projet index.js et modifiez l’URL de l’application pour pointer vers une URL non valide, comme suit :

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Index.html, de mettre à jour le fournisseur de services cryptographiques `<meta>` élément avec la même URL non valide.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Générer et exécuter l’application cliente et notez qu’une exception est enregistrée dans la console lorsque l’application tente d’effectuer une synchronisation avec le serveur principal après la connexion. Les nouveaux éléments que vous ajoutez existe uniquement dans le magasin local jusqu'à ce qu’ils peuvent être installés sur le serveur principal mobile. L’application cliente se comporte comme si est connectée au back-end, prenant en charge que tous les créent, lire, mettre à jour, les opérations de suppression (CRUD).

4. Fermez l’application et redémarrez pour vérifier que les nouveaux éléments que vous avez créés sont rendues persistantes dans le magasin local.

5. (Facultatif) Utilisez Visual Studio pour afficher votre table de base de données de SQL Azure pour voir que les données de la base de données back-end n’a pas changé.

    Dans Visual Studio, ouvrez **l’Explorateur de serveurs**. Naviguez jusqu'à votre base de données dans **Azure**->**Les bases de données SQL**. Avec le bouton droit de votre base de données et sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Vous pouvez désormais parcourir à votre table de base de données SQL et de son contenu.


## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Facultatif) Tester la reconnexion à votre serveur principal mobile

Dans cette section vous allez vous reconnecter l’application sur le serveur principal mobile, ce qui simule l’application revenant à un état en ligne. Lorsque vous vous connectez, données seront synchronisées avec votre back-end mobile.

1. Rouvrez index.js et corrigez l’URL de l’application pour pointer vers l’URL correcte.

2. Rouvrez index.html et corrigez l’URL de l’application dans le CSP `<meta>` élément.

3. Regénérez et exécutez l’application cliente. L’application tente d’effectuer une synchronisation avec le serveur principal d’une application mobile après la connexion. Vérifiez que l’option aucune exception n’est consignées dans la console de débogage.

4. (Facultatif) Permet d’afficher les données mises à jour à l’aide de l’Explorateur d’objets SQL Server, ou un outil de reste comme Fiddler. Notez que les données ont été synchronisées entre la base de données back-end et le magasin local.

    Notez les données a été synchronisées entre la base de données et le magasin local et contient les éléments que vous avez ajouté pendant que votre application a été déconnectée.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation de données hors connexion dans les applications mobiles Azure]

* [Couverture nuageuse : synchronisation hors connexion dans Azure Services mobiles] \(Remarque : la vidéo est sur Services mobiles, mais la synchronisation hors connexion fonctionne de la même manière dans les applications Azure Mobile\)

* [Visual Studio Tools pour Apache Cordova]

## <a name="next-steps"></a>Étapes suivantes

* Examinez les fonctionnalités plus avancées de synchronisation hors connexion tels que la résolution du conflit dans l' [exemple de synchronisation hors connexion]
* Examinez la synchronisation en mode hors connexion dans le [fichier Lisezmoi] de référence sur l’API

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Démarrage rapide d’Apache Cordova]: app-service-mobile-cordova-get-started.md
[FICHIER LISEZ-MOI]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[exemple de synchronisation hors connexion]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md
[Couverture de nuage : La synchronisation hors connexion dans Azure Services mobiles]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools pour Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
