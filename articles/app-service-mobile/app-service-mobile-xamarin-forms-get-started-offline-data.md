<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application Mobile de Azure (Xamarin.Forms) | Microsoft Azure"
    description="Apprenez à utiliser l’application Service Mobile application de cache et la synchronisation des données hors connexion dans votre application de Xamarin.Forms"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Activer la synchronisation hors connexion pour votre application mobile Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d’ensemble
Ce didacticiel présente la fonctionnalité de synchronisation hors ligne des applications de Mobile Azure pour Xamarin.Forms. Synchronisation hors connexion permet aux utilisateurs d’interagir avec une application mobile, affichage, ajout ou modification de données, même lorsqu’il n’y a pas de connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois que le périphérique est en ligne, ces modifications sont synchronisées avec le service distant.

Ce didacticiel est basé sur la solution de démarrage rapide de Xamarin.Forms pour les applications mobiles que vous créez lorsque vous réalisez le didacticiel [créer une application iOS de Xamarin]. La solution de démarrage rapide pour les Xamarin.Forms contient le code pour prendre en charge la synchronisation hors connexion, qui doit simplement être activée. Dans ce didacticiel, vous mettez à jour la solution de démarrage rapide pour activer les fonctionnalités hors ligne des applications de Mobile Azure. Nous avons également mettre en surbrillance le code dans l’application hors connexion spécifiques. Si vous n’utilisez pas la solution de démarrage rapide téléchargé, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure][1].

Pour en savoir plus sur la fonctionnalité de synchronisation en mode hors connexion, reportez-vous à la rubrique [Synchronisation de données hors connexion dans les applications Azure Mobile][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Activer la fonctionnalité de synchronisation hors connexion de la solution de démarrage rapide

Le code de synchronisation hors connexion est inclus dans le projet à l’aide de directives de préprocesseur C#. Lors de la **hors connexion\_SYNC\_activé** symbole est défini, ces chemins de codes sont inclus dans la build. Pour les applications Windows, vous devez également installer la plate-forme SQLite.

1. Dans Visual Studio, la solution avec le bouton droit > **Manage NuGet Packages de Solution...**, puis de rechercher et d’installer le package NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** pour tous les projets dans la solution.

2. Dans l’Explorateur de solutions, ouvrez le fichier TodoItemManager.cs à partir du projet avec le **Portable** dans le nom, qui est un projet de bibliothèque de classes Portable, puis supprimez le commentaire la directive de préprocesseur suivante :

        #define OFFLINE_SYNC_ENABLED

4. (Facultatif) Pour prendre en charge les périphériques Windows, installez un des packages de runtime de SQLite suivants :

    * **Windows 8.1 Runtime :** Installer [SQLite pour Windows 8.1][3].
    * **Windows Phone 8.1 :** Installer [SQLite pour Windows Phone 8.1][4].
    * **Plate-forme Windows universel** Installer [SQLite l’universel universel de Windows][5].

    Bien que le démarrage rapide ne contient-elle pas un projet universel de Windows, la plate-forme Windows universel est pris en charge avec des formulaires de Xamarin.

5. (Facultatif) Dans chaque projet d’application Windows, cliquez sur **références** > **Ajouter une référence**, développez le dossier **Windows** > **Extensions**.
    Activer approprié **SQLite pour Windows** SDK ainsi que le Kit de développement **2013 Runtime pour Windows de Visual C++** .
    Les noms de SQLite SDK varient légèrement chaque plate-forme Windows.

## <a name="review-the-client-sync-code"></a>Examinez le code de synchronisation du client

Voici un bref aperçu de ce qui est déjà inclus dans le code du didacticiel à l’intérieur de la `#if OFFLINE_SYNC_ENABLED` directives. La fonctionnalité de synchronisation en mode hors connexion est dans le fichier de projet TodoItemManager.cs dans le projet de bibliothèque de classes Portable. Pour une vue d’ensemble conceptuelle de la fonctionnalité, consultez [Synchronisation des données hors connexion dans les applications Azure Mobile][2].

* Avant de pouvoir effectuer des opérations de la table, la banque locale doit être initialisée. La base de données de la banque locale est initialisé dans le constructeur de la classe **TodoItemManager** à l’aide du code suivant :

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Ce code crée une nouvelle base de SQLite locale à l’aide de la classe **MobileServiceSQLiteStore** .

    La méthode **DefineTable** crée une table dans le magasin local qui correspond à des champs dans le type fourni.  Le type ne doit inclure toutes les colonnes qui se trouvent dans la base de données distante. Il est possible de stocker un sous-ensemble de colonnes.

* Le champ **todoTable** dans **TodoItemManager** est un type de **IMobileServiceSyncTable** au lieu de **IMobileServiceTable**. Cette classe utilise la base de données locale pour tous les créer, lire, mettre à jour et supprimer des opérations de table (CRUD). Vous décidez quand ces modifications envoyées vers le serveur principal chargé de l’application Mobile en appelant **PushAsync** sur **IMobileServiceSyncContext**. Le contexte de synchronisation permet de conserver les relations entre les tables en effectuant le suivi en exécutant un push de modifications dans une application cliente a modifié lorsque **PushAsync** est appelé de toutes les tables.

    La méthode **SyncAsync** suivante est appelée pour synchroniser avec le serveur principal chargé de l’application Mobile :

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
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

            // Simple error/conflict handling.
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

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Cet exemple utilise la simple gestion des erreurs avec le Gestionnaire de synchronisation par défaut. Une application réelle traiterait les diverses erreurs comme des conditions réseau et des conflits de serveur à l’aide d’une implémentation personnalisée de la **IMobileServiceSyncHandler** .

## <a name="offline-sync-considerations"></a>Considérations relatives à la synchronisation hors connexion

Dans l’exemple, la méthode **SyncAsync** est uniquement appelée au démarrage et lorsqu’une synchronisation est demandée.  Pour lancer une synchronisation dans une application d’Android ou iOS, extraire vers le bas dans la liste d’éléments ; pour Windows, utilisez le bouton de **synchronisation** . Dans une application réelle, vous pouvez également apporter le déclencheur de la synchronisation, lorsque l’état du réseau change.

Lorsqu’une extraction est exécutée sur une table qui a en attente mises à jour locales suivi par le contexte, ce qui extraient des opération déclencheurs automatiquement un push de contexte précédent. Lors de l’actualisation, l’ajout et la fin des articles dans cet exemple, vous pouvez omettre l’appel explicite de **PushAsync** .

Dans le code fourni, tous les enregistrements dans la table TodoItem à distance sont interrogés, mais il est également possible de filtrer les enregistrements en passant un id de requête et la requête pour **PushAsync**. Pour plus d’informations, reportez-vous à la section *Synchronisation incrémentielle* dans la [Synchronisation des données hors connexion dans les applications Azure Mobile][2].

## <a name="run-the-client-app"></a>Exécutez l’application client

Avec la synchronisation hors connexion maintenant activée, exécutez l’application cliente au moins une fois sur chaque plate-forme pour remplir la base de données de la banque locale. Par la suite, simuler un scénario en mode hors connexion et modifier les données dans le magasin local lors de l’application est en mode hors connexion.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Le comportement de synchronisation de l’application cliente de mettre à jour

Dans cette section, modifiez le projet de client pour simuler un scénario en mode hors connexion à l’aide d’une URL d’application non valide pour votre serveur principal. Sinon, vous pouvez désactiver les connexions réseau en déplaçant votre périphérique sur « Mode avion. »  Lorsque vous ajoutez ou modifiez des éléments de données, ces modifications sont stockées dans le magasin local, mais non synchronisées au magasin de données back-end, jusqu'à ce que la connexion est rétablie.

1. Dans l’Explorateur de solutions, ouvrez le fichier Constants.cs du projet à partir du projet **Portable** et modifiez la valeur de `ApplicationURL` pour pointer vers une URL non valide :

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";

2. Ouvrez le fichier TodoItemManager.cs à partir du projet **Portable** , puis ajoutez un **catch** pour la classe de base **Exception** dans le bloc **try... catch dans **SyncAsync**** . Ce bloc **catch** écrit le message d’exception dans la console, comme suit :

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }

3. Générez et exécutez l’application cliente.  Ajouter des articles. Notez qu’une exception est enregistrée dans la console à chaque tentative d’effectuer une synchronisation avec le serveur principal. Ces nouveaux éléments n’existent que dans le magasin local jusqu'à ce qu’ils peuvent être installés sur le serveur principal mobile. L’application cliente se comporte comme si elle est connectée au back-end, prenant en charge que tous les créent, lire, mettre à jour, les opérations de suppression (CRUD).

4. Fermez l’application et redémarrez pour vérifier que les nouveaux éléments que vous avez créés sont rendues persistantes dans le magasin local.

5. (Facultatif) Utilisez Visual Studio pour afficher votre table de base de données de SQL Azure pour voir que les données de la base de données back-end n’a pas changé.

    Dans Visual Studio, ouvrez **l’Explorateur de serveurs**. Naviguez jusqu'à votre base de données dans **Azure**->**Les bases de données SQL**. Avec le bouton droit de votre base de données et sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Vous pouvez désormais parcourir à votre table de base de données SQL et de son contenu.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Mise à jour de l’application client à se reconnecter votre back-end mobile

Dans cette section, reconnectez l’application sur le serveur principal mobile, ce qui simule l’application revenant à un état en ligne. Lorsque vous effectuez le mouvement de l’actualisation, données sont synchronisées à la principale mobile.

1. Rouvrez le Constants.cs. Corriger le `applicationURL` pour pointer vers l’URL correcte.

2. Regénérez et exécutez l’application cliente. L’application tente d’effectuer une synchronisation avec le serveur principal d’une application mobile après le lancement. Vérifiez que l’option aucune exception n’est consignées dans la console de débogage.

3. (Facultatif) Permet d’afficher les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou d’un outil reste comme Fiddler ou [Postman][6]. Notez que les données ont été synchronisées entre la base de données back-end et le magasin local.

    Notez les données a été synchronisées entre la base de données et le magasin local et contient les éléments que vous avez ajouté pendant que votre application a été déconnectée.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation de données hors connexion dans les applications mobiles Azure][2]
* [FAIRE de Azure applications mobiles .NET SDK][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md