<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application Mobile de Azure (Android)"
    description="Apprenez à utiliser les applications de Mobile Application Service de cache et la synchronisation des données hors connexion dans votre application d’Android"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Activer la synchronisation hors connexion pour votre application mobile Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel décrit la fonctionnalité de synchronisation hors connexion des applications de Mobile Azure pour Android. Synchronisation hors connexion permet aux utilisateurs d’interagir avec une application mobile&mdash;affichage, ajout ou modification de données&mdash;même lorsqu’il n’y a pas de connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois que le périphérique est en ligne, ces modifications sont synchronisées avec le serveur principal à distance.

S’il s’agit de votre première expérience avec les applications Azure Mobile, vous devez compléter le didacticiel, [créez une application d’Android]. Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour en savoir plus sur la fonctionnalité de synchronisation en mode hors connexion, reportez-vous à la rubrique [Synchronisation de données hors connexion dans les applications mobiles Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Mise à jour de l’application pour prendre en charge la synchronisation hors connexion

Avec la synchronisation en mode hors connexion, vous lire et d’écrivez dans une *table de synchronisation* (à l’aide de l’interface *IMobileServiceSyncTable* ), qui fait partie d’une base de données de **SQLite** sur votre périphérique.

Pour envoyer et extraire des modifications entre le périphérique et les Services de Mobile d’Azure, vous utilisez un *contexte de synchronisation* (*MobileServiceClient.SyncContext*), qui vous initialisez la base de données locale pour stocker des données localement.

1. Dans `TodoActivity.java`, commentez la définition existante de `mToDoTable` et ne commentez pas la version de table de synchronisation :

        private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. Dans le `onCreate` méthode, commentez l’initialisation existante de `mToDoTable` et supprimez les commentaires de cette définition :

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. Dans `refreshItemsFromTable` commenter la définition de `results` et supprimez les commentaires de cette définition :

        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Commentaire de la définition de `refreshItemsFromMobileServiceTable`.

5. Supprimez les commentaires de la définition de `refreshItemsFromMobileServiceTableSyncTable`:

        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }

6. Supprimez les commentaires de la définition de `sync`:

        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Tester l’application

Dans cette section, vous testez le comportement avec le Wi-Fi sur et puis désactivez Wi-Fi pour créer un scénario hors connexion.

Lorsque vous ajoutez des éléments de données, ils sont stockés dans le magasin local de SQLite, mais non synchronisés pour le service mobile jusqu'à ce que vous appuyez sur le bouton **Actualiser** . Autres applications peuvent avoir des besoins différents en matière de lorsque les données doivent être synchronisées, mais à des fins de démonstration, ce didacticiel a l’utilisateur demande explicitement.

Lorsque vous appuyez sur ce bouton, une nouvelle tâche d’arrière-plan démarre. Tout d’abord, il envoie toutes les modifications apportées dans le magasin local à l’aide du contexte de synchronisation, puis extrait tous les modifié les données d’Azure dans la table locale.

### <a name="offline-testing"></a>Tests hors ligne

1. Placez le périphérique ou le simulator dans *Le Mode avion*. Ceci crée un scénario hors connexion.

2. Ajoutez des éléments *ToDo* , ou marquer certains éléments comme étant terminée. Fermez le périphérique ou le simulator (ou forcer l’application) et redémarrez. Vérifiez que vos modifications ont été rendues persistantes sur le périphérique, car ils sont conservés dans le magasin local de SQLite.

3. Permet d’afficher le contenu de la table Azure *TodoItem* avec un SQL outil tel que *SQL Server Management Studio*, ou un client reste comme *Fiddler* ou *Postman*. Vérifiez que les nouveaux éléments n’ont _pas_ été synchronisées sur le serveur

    + Pour un back-end Node.js, accédez au [portail Azure](https://portal.azure.com/)et votre application Mobile back-end cliquez sur **Tables simple** > **TodoItem** pour afficher le contenu de la `TodoItem` table.
    + Pour un serveur principal de .NET, permet d’afficher le sommaire de l’aide d’un outil SQL telles que *SQL Server Management Studio*ou un client reste comme *Fiddler* ou *Postman*.

4. Activer Wi-Fi du périphérique ou le simulator. Ensuite, appuyez sur le bouton **Actualiser** .

5. Affichez de nouveau les données de TodoItem dans le portail Azure. TodoItems les nouvelles et modifiées doit maintenant apparaître.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation de données hors connexion dans les applications mobiles Azure]

* [Couverture nuageuse : synchronisation hors connexion dans Azure Services mobiles] \(Remarque : la vidéo est sur Services mobiles, mais la synchronisation hors connexion fonctionne de la même manière dans les applications Azure Mobile\)


<!-- URLs. -->

[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md

[Créer une application d’Android]: app-service-mobile-android-get-started.md

[Couverture de nuage : La synchronisation hors connexion dans Azure Services mobiles]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

