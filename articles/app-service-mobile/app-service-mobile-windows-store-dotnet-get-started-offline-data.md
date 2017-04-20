<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application de plate-forme de Windows universel (UWP) avec les applications Mobile | Service d’application Azure"
    description="Découvrez comment utiliser une application Azure Mobile du cache et la synchronisation des données hors connexion dans votre application de plate-forme de Windows universel (UWP)."
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-windows-app"></a>Activer la synchronisation hors connexion pour votre application Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel vous montre comment ajouter la prise en charge en mode hors connexion pour une application de plate-forme de Windows universel (UWP) à l’aide d’un back-end de l’application Mobile de Azure. Synchronisation hors connexion permet aux utilisateurs d’interagir avec une application mobile, affichage, ajout ou modification de données - même lorsqu’il n’y a pas de connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois que le périphérique est en ligne, ces modifications sont synchronisées avec le serveur principal à distance.

Dans ce didacticiel, vous mettez à jour le projet d’application UWP à partir du didacticiel [créer une application Windows] pour prendre en charge les fonctionnalités d’Azure Mobile applications hors connexion. Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour en savoir plus sur la fonctionnalité de synchronisation en mode hors connexion, reportez-vous à la rubrique [Synchronisation de données hors connexion dans les applications mobiles Azure].

## <a name="requirements"></a>Configuration requise

Ce didacticiel requiert les conditions préalables suivantes :

* Visual Studio 2013 sur Windows 8.1 ou version ultérieure.
* Fin de [créer une application Windows]de[créer une application windows].
* [Banque de SQLite Services Azure Mobile][sqlite store nuget]
* [SQLite pour le développement de la plate-forme de Windows universel](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Mise à jour de l’application client pour prendre en charge les fonctionnalités hors connexion

Des fonctionnalités hors connexion Mobile application Azure vous autorisent à interagir avec une base de données locale lorsque vous êtes dans un scénario en mode hors connexion. Pour utiliser ces fonctionnalités dans votre application, vous initialisez un [SyncContext] [ synccontext] dans un magasin local. Puis référencer votre table par l’intermédiaire de l’interface de[IMobileServiceSyncTable] de [IMobileServiceSyncTable]. SQLite est utilisé comme le magasin local du périphérique.

1. Installez le [runtime SQLite pour la plate-forme Windows universel](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).

2. Dans Visual Studio, ouvrez le Gestionnaire de package NuGet pour le projet d’application UWP que vous avez effectuées dans le didacticiel de [créer une application Windows] .
    Rechercher et installer le package NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

4. Dans l’Explorateur de solutions, cliquez sur **références** > **Ajouter une référence...**  >  **Universel de Windows** > **Extensions**, puis activer **SQLite pour plate-forme universel de Windows** et **Visual C++ 2015 Runtime pour les applications de la plate-forme universel de Windows**.

    ![Ajouter une référence de SQLite UWP][1]

5. Ouvrez le fichier MainPage.xaml.cs et ne commentez pas la `#define OFFLINE_SYNC_ENABLED` définition.

6. Dans Visual Studio, appuyez sur la touche **F5** pour régénérer et exécuter l’application cliente. L’application fonctionne de la même manière qu’avant l’activation de la synchronisation hors connexion. Toutefois, la base de données locale est maintenant rempli avec des données qui peuvent être utilisées dans un scénario en mode hors connexion.

## <a name="update-sync"></a>Mise à jour de l’application à se déconnecter du serveur principal

Dans cette section, vous rompez la connexion à votre back-end de l’application Mobile pour simuler une situation hors connexion. Lorsque vous ajoutez des éléments de données, votre gestionnaire d’exception indique que l’application est en mode hors connexion. Dans cet état, les nouveaux éléments ajoutés dans le magasin local et seront synchronisés avec le serveur principal d’une application mobile lors de la prochaine exécution push dans un état connecté.

1. Modifier App.xaml.cs dans le projet partagé. Commentez l’initialisation de la **MobileServiceClient** et ajoutez la ligne suivante, qui utilise une URL non valide application mobile :

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Vous pouvez également montrer le comportement hors connexion en désactivant le Wi-Fi et les réseaux cellulaires sur le périphérique, ou utiliser le mode avion.

2. Appuyez sur **F5** pour générer et exécuter l’application. Remarquez la synchronisation a échoué lors de l’actualisation lors du lancée de l’application.

3. Entrer de nouveaux articles et notez que push échoue avec l’état [CancelledByNetworkError] chaque fois que vous cliquez sur **Enregistrer**. Toutefois, les nouveaux éléments todo existent dans le magasin local jusqu'à ce qu’ils peuvent être installés sur le serveur principal d’une application mobile.  Dans une application de production, si vous supprimez ces exceptions de l’application cliente se comporte comme s’il est toujours connecté sur le serveur principal d’une application mobile.

4. Fermez l’application et redémarrez pour vérifier que les nouveaux éléments que vous avez créés sont rendues persistantes dans le magasin local.

5. (Facultatif) Dans Visual Studio, ouvrez **l’Explorateur de serveurs**. Naviguez jusqu'à votre base de données dans **Azure**->**Les bases de données SQL**. Avec le bouton droit de votre base de données et sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Vous pouvez désormais parcourir à votre table de base de données SQL et de son contenu. Vérifiez que les données de la base de données back-end n’a pas changé.

6. (Facultatif) Employer un utilitaire reste Fiddler ou Postman pour interroger votre mobile back-end, à l’aide d’une requête GET sous la forme `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Mise à jour de l’application à se reconnecter votre back-end de l’application Mobile

Dans cette section, vous reconnectez l’application sur le serveur principal d’une application mobile. Ces modifications de simuler une reconnexion de réseau dans l’application.

Lorsque vous exécutez l’application, le `OnNavigatedTo` appels du Gestionnaire d’événements `InitLocalStoreAsync`. Cette méthode appelle à son tour `SyncAsync` de synchroniser votre magasin local avec la base de données back-end. L’application tente de synchroniser au démarrage.

1. Ouvrez App.xaml.cs dans le projet partagé, et supprimez les commentaires de votre précédente initialisation de `MobileServiceClient` à utiliser le bon l’URL de l’application mobile.

2. Appuyez sur la touche **F5** pour régénérer et exécuter l’application. L’application synchronise vos modifications locales avec le serveur Azure Mobile application principal à l’aide des opérations push et pull lors de la `OnNavigatedTo` Gestionnaire d’événements s’exécute.

3. (Facultatif) Permet d’afficher les données mises à jour à l’aide de l’Explorateur d’objets SQL Server, ou un outil de reste comme Fiddler. Notez les données ont été synchronisée entre la base de données back-end de Azure Mobile application et le magasin local.

4. Dans l’application, cliquez sur la case à cocher en regard des quelques éléments à leur réalisation dans le magasin local.

  `UpdateCheckedTodoItem`appels `SyncAsync` à élément de synchronisation chaque terminée avec le back-end de l’application Mobile. `SyncAsync`appelle push et extraction. Toutefois, **chaque fois que vous exécutez une extraction sur une table que le client a effectué des modifications, un « push » est toujours exécuté automatiquement**. Ce comportement garantit la que cohérence de toutes les tables dans la banque locale ainsi que des relations. Ce comportement peut entraîner une commande inattendue.  Pour plus d’informations sur ce comportement, consultez [Synchronisation des données hors connexion dans les applications mobiles Azure].


##<a name="api-summary"></a>Résumé de l’API

Pour prendre en charge les fonctionnalités hors connexion des services mobiles, nous avons utilisé l’interface [IMobileServiceSyncTable] et initialisé [MobileServiceClient.SyncContext] [ synccontext] avec une base de données locale de SQLite. En mode hors connexion, les opérations normales pour applications Mobile fonctionnent comme si l’application est toujours connectée alors que les opérations se déroulent sur le magasin local. Les méthodes suivantes sont utilisées pour synchroniser le magasin local avec le serveur :

*  **[PushAsync]** Dans la mesure où cette méthode est un membre de [IMobileServicesSyncContext], modifications dans toutes les tables envoyées vers le serveur principal. Seuls les enregistrements des modifications locales sont envoyés au serveur.

* **[PullAsync] ** 
   une extraction est démarrée à partir d’un [IMobileServiceSyncTable]. Lorsqu’il y a des modifications dans la table, un push implicite est exécuté pour s’assurer que toutes les tables dans le magasin local ainsi que les relations restent cohérentes. Le paramètre *pushOtherTables* contrôle si les autres tables dans le contexte sont transmis dans un push implicite. Le paramètre de la *requête* prend une [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] 
   ou la chaîne de requête OData pour filtrer les données retournées. Le paramètre de *l’élément queryId* est utilisé pour définir la synchronisation incrémentielle. Pour plus d’informations, consultez  [Synchronisation des données hors connexion dans les applications mobiles Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

* **[PurgeAsync]** Votre application doit appeler régulièrement cette méthode pour purger les données périmées du magasin local. Utilisez le paramètre *force* lorsque vous avez besoin de purger les modifications qui n’ont pas encore été synchronisées.

Pour plus d’informations sur ces concepts, consultez [Synchronisation des données hors connexion dans les applications mobiles Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Plus d’infos

Les rubriques suivantes fournissent des informations supplémentaires sur la fonctionnalité de synchronisation hors ligne des applications Mobile :

* [Synchronisation de données hors connexion dans les applications mobiles Azure]
* [FAIRE de Azure applications mobiles .NET SDK][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md
[créer une application windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
