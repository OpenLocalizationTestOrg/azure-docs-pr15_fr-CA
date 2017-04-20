<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application Mobile de Azure (Xamarin Android)"
    description="Apprenez à utiliser l’application Service Mobile App de cache et la synchronisation des données hors connexion dans votre application d’Android de Xamarin"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Activer la synchronisation hors connexion pour votre application mobile Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel présente la fonctionnalité de synchronisation hors ligne des applications de Mobile Azure pour Xamarin.Android. Synchronisation hors connexion permet aux utilisateurs d’interagir avec une application mobile, affichage, ajout ou modification de données, même lorsqu’il n’y a pas de connexion réseau. Les modifications sont stockées dans une base de données locale.
Une fois que le périphérique est en ligne, ces modifications sont synchronisées avec le service distant.

Dans ce didacticiel, vous mettez à jour le projet de client à partir du didacticiel [créer une application d’Android de Xamarin] en charge les fonctionnalités hors ligne des applications de Mobile Azure. Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour en savoir plus sur la fonctionnalité de synchronisation en mode hors connexion, reportez-vous à la rubrique [Synchronisation de données hors connexion dans les applications mobiles Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Mise à jour de l’application client pour prendre en charge les fonctionnalités hors connexion

Des fonctionnalités hors connexion Mobile application Azure vous autorisent à interagir avec une base de données locale lorsque vous êtes dans un scénario en mode hors connexion. Pour utiliser ces fonctionnalités dans votre application, vous initialisez un [SyncContext] dans un magasin local. Puis faire référence à la table par le biais de l’interface de [IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite est utilisé comme le magasin local du périphérique.

1. Dans Visual Studio, ouvrez le Gestionnaire de package NuGet dans le projet que vous avez effectuées dans le didacticiel de [Création d’une application d’Android de Xamarin] .  Rechercher et installer le package NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

2. Ouvrez le fichier ToDoActivity.cs et supprimez les commentaires de la `#define OFFLINE_SYNC_ENABLED` définition.

3. Dans Visual Studio, appuyez sur la touche **F5** pour régénérer et exécuter l’application cliente. L’application fonctionne de la même manière qu’avant l’activation de la synchronisation hors connexion. Toutefois, la base de données locale est maintenant rempli avec des données qui peuvent être utilisées dans un scénario en mode hors connexion.

## <a name="update-sync"></a>Mise à jour de l’application à se déconnecter du serveur principal

Dans cette section, vous rompez la connexion à votre back-end de l’application Mobile pour simuler une situation hors connexion. Lorsque vous ajoutez des éléments de données, votre gestionnaire d’exception indique que l’application est en mode hors connexion. Dans cet état, les nouveaux éléments ajoutés dans la banque locale et sont synchronisées avec le serveur principal d’une application mobile lors de l’exécution d’un push dans un état connecté.

1. Modifier ToDoActivity.cs dans le projet partagé. Modifier **ApplicationUrl ne** pour pointer vers une URL non valide :

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Vous pouvez également montrer le comportement hors connexion en désactivant le Wi-Fi et les réseaux cellulaires sur le périphérique ou en utilisant le mode avion.

2. Appuyez sur **F5** pour générer et exécuter l’application. Remarquez la synchronisation a échoué lors de l’actualisation lors du lancée de l’application.

3. Entrer de nouveaux articles et notez que push échoue avec l’état [CancelledByNetworkError] à chaque fois que vous cliquez sur **Enregistrer**. Toutefois, les nouveaux éléments todo existent dans le magasin local jusqu'à ce qu’ils peuvent être installés sur le serveur principal d’une application mobile.  Dans une application de production, si vous supprimez ces exceptions de l’application cliente se comporte comme s’il est toujours connecté sur le serveur principal d’une application mobile.

4. Fermez l’application et redémarrez pour vérifier que les nouveaux éléments que vous avez créés sont rendues persistantes dans le magasin local.

5. (Facultatif) Dans Visual Studio, ouvrez **l’Explorateur de serveurs**. Naviguez jusqu'à votre base de données dans **Azure**->**Les bases de données SQL**. Avec le bouton droit de votre base de données et sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Vous pouvez désormais parcourir à votre table de base de données SQL et de son contenu. Vérifiez que les données de la base de données back-end n’a pas changé.

6. (Facultatif) Employer un utilitaire reste Fiddler ou Postman pour interroger votre mobile back-end, à l’aide d’une requête GET sous la forme `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Mise à jour de l’application à se reconnecter votre back-end de l’application Mobile

Dans cette section, reconnectez l’application sur le serveur principal d’une application mobile. Lorsque vous exécutez l’application, le `OnCreate` appels du Gestionnaire d’événements `OnRefreshItemsSelected`. Cette méthode appelle `SyncAsync` de synchroniser votre magasin local avec la base de données back-end.

1. Ouvrez ToDoActivity.cs dans le projet partagé, et rétablir la modification de la propriété **ApplicationUrl ne** .

2. Appuyez sur la touche **F5** pour régénérer et exécuter l’application. L’application synchronise vos modifications locales avec le serveur Azure Mobile application principal à l’aide des opérations push et pull lors de la `OnRefreshItemsSelected` méthode s’exécute.

3. (Facultatif) Permet d’afficher les données mises à jour à l’aide de l’Explorateur d’objets SQL Server, ou un outil de reste comme Fiddler. Notez les données ont été synchronisée entre la base de données back-end de Azure Mobile application et le magasin local.

4. Dans l’application, cliquez sur la case à cocher en regard des quelques éléments à leur réalisation dans le magasin local.

  `CheckItem`appels `SyncAsync` à élément de synchronisation chaque terminée avec le back-end de l’application Mobile. `SyncAsync`appelle push et extraction. **Chaque fois que vous exécutez une extraction sur une table que le client a effectué des modifications, un « push » est toujours exécuté automatiquement**. Cela garantit la que cohérence de toutes les tables dans le magasin local ainsi que les relations. Ce comportement peut entraîner une commande inattendue. Pour plus d’informations sur ce comportement, consultez [Synchronisation des données hors connexion dans les applications mobiles Azure].

## <a name="review-the-client-sync-code"></a>Examinez le code de synchronisation du client

Le projet de client de Xamarin que vous avez téléchargé lorsque vous est déjà terminé le didacticiel de [créer une application d’Android de Xamarin] contient un code prenant en charge la synchronisation hors connexion à l’aide d’une base de données locale de SQLite. Voici un bref aperçu de ce qui est déjà inclus dans le didacticiel c ode. Pour une vue d’ensemble conceptuelle de la fonctionnalité, consultez [Synchronisation des données hors connexion dans les applications mobiles Azure].

* Avant de pouvoir effectuer des opérations de la table, la banque locale doit être initialisée. La base de données de la banque locale est initialisée lorsque `ToDoActivity.OnCreate()` exécute `ToDoActivity.InitLocalStoreAsync()`. Cette méthode crée une base de données de SQLite local à l’aide du `MobileServiceSQLiteStore` classe fournie par le client d’applications Mobile de Azure SDK.

    Le `DefineTable` méthode crée une table dans le magasin local qui correspond à des champs dans le type fourni, `ToDoItem` dans ce cas. Le type ne doit inclure toutes les colonnes qui se trouvent dans la base de données distante. Il est possible de stocker simplement un sous-ensemble de colonnes.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }


* Le `toDoTable` membre de `ToDoActivity` de la `IMobileServiceSyncTable` tapez à la place de `IMobileServiceTable`. La IMobileServiceSyncTable indique tous les créer, lire, mettre à jour et opérations de suppression (CRUD) table dans la base de données de la banque locale.

    Vous décidez lorsque les modifications sont transmises sur le serveur principal d’Azure Mobile application en appelant `IMobileServiceSyncContext.PushAsync()`. Le contexte de synchronisation permet de conserver des relations entre les tables en effectuant le suivi en exécutant un push de modifications dans une application cliente a modifié lorsque toutes les tables de `PushAsync` est appelée.

    Le code fourni appelle `ToDoActivity.SyncAsync()` la synchronisation à chaque actualisation de la liste todoitem ou un todoitem est ajouté ou terminé. Synchronise le code après chaque modification locale.

    Dans le code fourni, tous les enregistrements dans la base distante `TodoItem` table sont interrogés, mais il est également possible de filtrer les enregistrements en passant un id de requête et la requête à `PushAsync`. Pour plus d’informations, reportez-vous à la section *Synchronisation incrémentielle* dans la [Synchronisation des données hors connexion dans les applications mobiles Azure].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation de données hors connexion dans les applications mobiles Azure]
* [FAIRE de Azure applications mobiles .NET SDK][8]

<!-- URLs. -->
[Créer une application d’Android de Xamarin]: ../app-service-mobile-xamarin-android-get-started.md
[Synchronisation de données hors connexion dans les applications mobiles Azure]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Créer une application d’Android de Xamarin]: app-service-mobile-xamarin-android-get-started.md
[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
