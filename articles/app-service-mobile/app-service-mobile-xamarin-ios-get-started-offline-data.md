<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application Mobile de Azure (iOS Xamarin)"
    description="Apprenez à utiliser l’application Service Mobile application de cache et la synchronisation des données hors connexion dans votre application d’e/s Xamarin"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Activer la synchronisation hors connexion pour votre application mobile Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel présente la fonctionnalité de synchronisation hors ligne des applications de Mobile Azure pour Xamarin.iOS. Synchronisation hors connexion permet aux utilisateurs d’interagir avec une application mobile, affichage, ajout ou modification de données, même lorsqu’il n’y a pas de connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois que le périphérique est en ligne, ces modifications sont synchronisées avec le service distant.

Dans ce didacticiel, mettre à jour le projet d’application de Xamarin.iOS de [créer une application iOS de Xamarin] en charge les fonctionnalités hors ligne des applications de Mobile Azure. Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour en savoir plus sur la fonctionnalité de synchronisation en mode hors connexion, reportez-vous à la rubrique [Synchronisation de données hors connexion dans les applications mobiles Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Mise à jour de l’application client pour prendre en charge les fonctionnalités hors connexion

Des fonctionnalités hors connexion Mobile application Azure vous autorisent à interagir avec une base de données locale lorsque vous êtes dans un scénario en mode hors connexion. Pour utiliser ces fonctionnalités dans votre application, initialiser un [SyncContext] dans un magasin local. Faire référence à la table par le biais de l’interface de [IMobileServiceSyncTable]. SQLite est utilisé comme le magasin local du périphérique.

1. Ouvrir le Gestionnaire de package NuGet dans le projet que vous avez effectuées dans le didacticiel de [créer une application iOS de Xamarin] , puis recherchez et installez le package NuGet de **Microsoft.Azure.Mobile.Client.SQLiteStore** .

2. Ouvrez le fichier QSTodoService.cs et supprimez les commentaires de la `#define OFFLINE_SYNC_ENABLED` définition.

3. Regénérez et exécutez l’application cliente. L’application fonctionne de la même manière qu’avant l’activation de la synchronisation hors connexion. Toutefois, la base de données locale est maintenant rempli avec des données qui peuvent être utilisées dans un scénario en mode hors connexion.

## <a name="update-sync"></a>Mise à jour de l’application à se déconnecter du serveur principal

Dans cette section, vous rompez la connexion à votre back-end de l’application Mobile pour simuler une situation hors connexion. Lorsque vous ajoutez des éléments de données, votre gestionnaire d’exception indique que l’application est en mode hors connexion. Dans cet état, les nouveaux éléments ajoutés dans le magasin local et seront synchronisés avec le serveur principal d’une application mobile lors de la prochaine exécution push dans un état connecté.

1. Modifier QSToDoService.cs dans le projet partagé. Modifier **ApplicationUrl ne** pour pointer vers une URL non valide :

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Vous pouvez également montrer le comportement hors connexion en désactivant le Wi-Fi et les réseaux cellulaires sur le périphérique ou en utilisant le mode avion.

2. Générez et exécutez l’application. Remarquez la synchronisation a échoué lors de l’actualisation lors du lancée de l’application.

3. Entrer de nouveaux articles et notez que push échoue avec l’état [CancelledByNetworkError] à chaque fois que vous cliquez sur **Enregistrer**. Toutefois, les nouveaux éléments todo existent dans le magasin local jusqu'à ce qu’ils peuvent être installés sur le serveur principal d’une application mobile.  Dans une application de production, si vous supprimez ces exceptions de l’application cliente se comporte comme s’il est toujours connecté sur le serveur principal d’une application mobile.

4. Fermez l’application et redémarrez pour vérifier que les nouveaux éléments que vous avez créés sont rendues persistantes dans le magasin local.

5. (Facultatif) Si vous avez installé sur un PC de Visual Studio, ouvrez **l’Explorateur de serveurs**. Naviguez jusqu'à votre base de données dans **Azure**-> **Les bases de données SQL**. Avec le bouton droit de votre base de données et sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Vous pouvez désormais parcourir à votre table de base de données SQL et de son contenu. Vérifiez que les données de la base de données back-end n’a pas changé.

6. (Facultatif) Employer un utilitaire reste Fiddler ou Postman pour interroger votre mobile back-end, à l’aide d’une requête GET sous la forme `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Mise à jour de l’application à se reconnecter votre back-end de l’application Mobile

Dans cette section, reconnectez l’application sur le serveur principal d’une application mobile. Cela simule l’application déplacement à partir d’un état hors connexion pour un état en ligne avec le serveur principal d’une application mobile.   Si vous simuler la rupture du réseau en désactivant la connectivité réseau, aucune modification de code n’est nécessaires.
Réactiver le réseau.  Lorsque vous exécutez l’application, le `RefreshDataAsync` méthode est appelée. Il appelle ensuite `SyncAsync` de synchroniser votre magasin local avec la base de données back-end.

1. Ouvrez QSToDoService.cs dans le projet partagé, et rétablir la modification de la propriété **ApplicationUrl ne** .

2. Regénérez et exécutez l’application. L’application synchronise vos modifications locales avec le serveur Azure Mobile application principal à l’aide des opérations push et pull lors de la `OnRefreshItemsSelected` méthode s’exécute.

3. (Facultatif) Permet d’afficher les données mises à jour à l’aide de l’Explorateur d’objets SQL Server, ou un outil de reste comme Fiddler. Notez les données ont été synchronisée entre la base de données back-end de Azure Mobile application et le magasin local.

4. Dans l’application, cliquez sur la case à cocher en regard des quelques éléments à leur réalisation dans le magasin local.

  `CompleteItemAsync`appels `SyncAsync` à élément de synchronisation chaque terminée avec le back-end de l’application Mobile. `SyncAsync`appelle push et extraction.
  **Chaque fois que vous exécutez une extraction sur une table que le client a effectué des modifications, un push sur le contexte de synchronisation du client est toujours exécutée en premier automatiquement**. La diffusion implicite garantit que toutes les tables dans le magasin local ainsi que les relations restent cohérentes. Pour plus d’informations sur ce comportement, consultez [Synchronisation des données hors connexion dans les applications mobiles Azure].

## <a name="review-the-client-sync-code"></a>Examinez le code de synchronisation du client

Le projet de client de Xamarin que vous avez téléchargé lorsque vous est déjà terminé le didacticiel de [créer une application iOS de Xamarin] contient un code prenant en charge la synchronisation hors connexion à l’aide d’une base de données locale de SQLite. Voici un bref aperçu de ce qui est déjà inclus dans le code du didacticiel. Pour une vue d’ensemble conceptuelle de la fonctionnalité, consultez [Synchronisation des données hors connexion dans les applications mobiles Azure].

* Avant de pouvoir effectuer des opérations de la table, la banque locale doit être initialisée. La base de données de la banque locale est initialisée lorsque `QSTodoListViewController.ViewDidLoad()` exécute `QSTodoService.InitializeStoreAsync()`. Cette méthode crée un nouveau local SQLite de base de données à l’aide de la `MobileServiceSQLiteStore` classe fournie par le client de l’application Mobile de Azure SDK.

    Le `DefineTable` méthode crée une table dans le magasin local qui correspond à des champs dans le type fourni, `ToDoItem` dans ce cas. Le type ne doit inclure toutes les colonnes qui se trouvent dans la base de données distante. Il est possible de stocker simplement un sous-ensemble de colonnes.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }


* Le `todoTable` membre de `QSTodoService` de la `IMobileServiceSyncTable` tapez à la place de `IMobileServiceTable`. La IMobileServiceSyncTable indique tous les créer, lire, mettre à jour et opérations de suppression (CRUD) table dans la base de données de la banque locale.

    Vous décidez lorsque ces modifications sont envoyées sur le serveur principal d’Azure Mobile application en appelant `IMobileServiceSyncContext.PushAsync()`. Le contexte de synchronisation permet de conserver des relations entre les tables en effectuant le suivi en exécutant un push de modifications dans une application cliente a modifié lorsque toutes les tables de `PushAsync` est appelée.

    Le code fourni appelle `QSTodoService.SyncAsync()` la synchronisation à chaque actualisation de la liste todoitem ou un todoitem est ajouté ou terminé. Les synchronisations app après chaque modification locale. Si un collecteur est exécutée sur une table qui est en attente de mises à jour locales suivis par le contexte, cette opération d’extraction déclenchera automatiquement une diffusion de contexte tout d’abord.

    Dans le code fourni, tous les enregistrements dans la base distante `TodoItem` table sont interrogés, mais il est également possible de filtrer les enregistrements en passant un id de requête et la requête à `PushAsync`. Pour plus d’informations, reportez-vous à la section *Synchronisation incrémentielle* dans la [Synchronisation des données hors connexion dans les applications mobiles Azure].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }


## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation de données hors connexion dans les applications mobiles Azure]
* [FAIRE de Azure applications mobiles .NET SDK][8]

<!-- Images -->

<!-- URLs. -->
[Créer une application iOS de Xamarin]: app-service-mobile-xamarin-ios-get-started.md
[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md