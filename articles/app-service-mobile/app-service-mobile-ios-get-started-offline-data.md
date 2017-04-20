<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application Mobile de Azure (iOS)"
    description="Apprenez à utiliser les applications de Mobile Application Service de cache et la synchronisation des données hors connexion dans votre application iOS"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-ios-mobile-app"></a>Activer la synchronisation hors connexion pour votre application mobile iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel décrit la fonctionnalité de synchronisation hors connexion des applications de Mobile Azure pour iOS. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;affichage, ajout ou modification de données&mdash;même lorsqu’il n’y a pas de connexion réseau. Les modifications sont stockées dans une base de données locale ; une fois que le périphérique est en ligne, ces modifications sont synchronisées avec le serveur principal à distance.

S’il s’agit de votre première expérience avec les applications Azure Mobile, vous devez compléter le didacticiel [créer un iOS App]. Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour en savoir plus sur la fonctionnalité de synchronisation en mode hors connexion, reportez-vous à la rubrique [Synchronisation de données hors connexion dans les applications mobiles Azure].

## <a name="review-sync"></a>Examinez le code de synchronisation du client

Le projet de client que vous avez téléchargé pour le didacticiel [créer un iOS App] déjà contient un code prenant en charge la synchronisation hors connexion à l’aide d’une base de données locale basée sur les données de base. Cette section est un résumé de ce qui est déjà inclus dans le code du didacticiel. Pour une vue d’ensemble conceptuelle de la fonctionnalité, consultez [Synchronisation des données hors connexion dans les applications mobiles Azure].

La fonctionnalité de synchronisation des données en mode hors connexion synchronisation des applications de Mobile Azure permet aux utilisateurs finaux d’interagir avec une base de données local lorsque le réseau n’est pas accessible. Pour utiliser ces fonctionnalités dans votre application, vous initialisez le contexte de synchronisation de `MSClient` et faire référence à un magasin local. Puis faire référence à la table par le biais de la `MSSyncTable` interface.

1. Dans **QSTodoService.m** (Objective-C) ou **ToDoTableViewController.swift** (Swift), notez le type du membre `syncTable` est `MSSyncTable`. Synchronisation hors connexion utilise cette interface de table de synchronisation au lieu de `MSTable`. Lorsqu’une table de synchronisation est utilisée, toutes les opérations atteindre la banque locale et sont synchronisées uniquement avec le back-end à distance avec des opérations push et pull explicites.

    Pour obtenir une référence à une table de synchronisation, utilisez la méthode `syncTableWithName` sur `MSClient`. Pour supprimer la fonctionnalité de synchronisation en mode hors connexion, utilisez `tableWithName` à la place.

2. Avant de pouvoir effectuer des opérations de la table, la banque locale doit être initialisée. Voici le code approprié. 
    
    **Objective-C**:
    
    Dans le `QSTodoService.init` méthode :
    
    
            MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
            self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
    
    
    **Swift**:
    
    Dans le `ToDoTableViewController.viewDidLoad` méthode :
    
    
            let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
            let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
            self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
            client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
    

    Cette opération crée un magasin local à l’aide de l’interface `MSCoreDataStore`, qui est fourni dans le Kit de développement des applications mobiles. Vous pouvez à la place un fournir un autre magasin local en mettant en œuvre le `MSSyncContextDataSource` protocole. 
    
    En outre, le premier paramètre de `MSSyncContext` est utilisé pour spécifier un gestionnaire de conflit. Dans la mesure où nous avons passé `nil`, nous allons le Gestionnaire de conflit par défaut, ce qui ne fonctionne pas sur n’importe quel conflit.
    
3. Maintenant, nous allons effectuer l’opération de synchronisation réel et obtenir des données à partir du serveur principal à distance.

    **Objective-C**:
    
    `syncData`tout d’abord exécute un push de nouvelles modifications, puis appelle `pullData` pour obtenir des données à partir du serveur principal à distance. À son tour, la méthode `pullData` Obtient de nouvelles données qui correspond à une requête :
    
    
            -(void)syncData:(QSCompletionBlock)completion
            {
                // push all changes in the sync context, then pull new data
                [self.client.syncContext pushWithCompletion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
                    [self pullData:completion];
                }];
            }
    
            -(void)pullData:(QSCompletionBlock)completion
            {
                MSQuery *query = [self.syncTable query];
    
                // Pulls data from the remote server into the local table.
                // We're pulling all items and filtering in the view
                // query ID is used for incremental sync
                [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
    
                    // Let the caller know that we have finished
                    if (completion != nil) {
                        dispatch_async(dispatch_get_main_queue(), completion);
                    }
                }];
            }
        
        
      **Swift**:
        
        
        func onRefresh(sender: UIRefreshControl!) {
            UIApplication.sharedApplication().networkActivityIndicatorVisible = true
            
            self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
                (error) -> Void in
                
                UIApplication.sharedApplication().networkActivityIndicatorVisible = false
                
                if error != nil {
                    // A real application would handle various errors like network conditions,
                    // server conflicts, etc via the MSSyncContextDelegate
                    print("Error: \(error!.description)")
                    
                    // We will just discard our changes and keep the servers copy for simplicity
                    if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                        for opError in opErrors {
                            print("Attempted operation to item \(opError.itemId)")
                            if (opError.operation == .Insert || opError.operation == .Delete) {
                                print("Insert/Delete, failed discarding changes")
                                opError.cancelOperationAndDiscardItemWithCompletion(nil)
                            } else {
                                print("Update failed, reverting to server's copy")
                                opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                            }
                        }
                    }
                }
                self.refreshControl?.endRefreshing()
            }
        } 
    
    
    Dans la version Objective-C, dans `syncData`, nous avons d’abord appeler `pushWithCompletion` sur le contexte de synchronisation. Cette méthode est un membre de `MSSyncContext` (au lieu de la synchronisation de table lui-même), car il va envoyer les modifications sur toutes les tables. Seuls les enregistrements qui ont été modifiées d’une façon localement (via les opérations CUD) seront envoyés au serveur. Puis le programme d’assistance `pullData` est appelée, les appels de `MSSyncTable.pullWithQuery` pour récupérer des données à distance et la stocker dans la base de données locale.
    
    Dans la version rapide, il n’y a aucun appel à `pushWithCompletion`. C’est parce que l’opération d’envoi n’était pas strictement nécessaire. S’il existe des modifications en attente dans le contexte de synchronisation pour la table qui effectue une opération de diffusion, extraction émet toujours un push tout d’abord. Toutefois, si vous avez plus d’une table de synchronisation, il est mieux appeler explicitement push pour s’assurer que tout est cohérent entre les tables connexes.
    
    Dans les versions à la fois le Objective-C et Swift, la méthode `pullWithQuery` vous permet de spécifier une requête pour filtrer les enregistrements que vous souhaitez récupérer. Dans cet exemple, la requête récupère simplement tous les enregistrements de la base distante `TodoItem` table.
    
    Le deuxième paramètre à `pullWithQuery` est un ID de requête qui est utilisé pour *la synchronisation incrémentielle*. Synchronisation incrémentielle copie uniquement les enregistrements modifiés depuis la dernière synchronisation, à l’aide de l’enregistrement `UpdatedAt` timestamp (appelé `updatedAt` dans le magasin local.) L’ID de la requête doit être une chaîne descriptive qui est unique pour chaque requête logique dans votre application. Pour refuser de recevoir de synchronisation incrémentielle, passer `nil` comme l’ID de la requête. Notez que cela peut potentiellement inefficace, dans la mesure où elle récupère tous les enregistrements de chaque opération d’extraction.

5. Les synchronisations app Objective-C lorsque nous modifier ou ajouter des données, un utilisateur effectue le mouvement de l’actualisation et de lancement. L’application rapide de synchroniser lorsqu’un utilisateur effectue le mouvement d’actualisation et de lancement. 

Étant donné que les synchronisations d’application chaque fois que les données sont modifiées (Objective-C) ou à chaque démarrage de l’application (Objective-C et Swift), l’application suppose que l’utilisateur est en ligne. Dans une autre section, nous mettrons à jour l’application afin que les utilisateurs puissent modifier même lorsqu’ils sont hors connexion.

## <a name="review-core-data"></a>Valider le modèle de données de base

Lorsque vous utilisez le magasin de données de base en mode hors connexion, vous devez définir des champs et des tables particulières dans votre modèle de données. L’exemple d’application comprend déjà un modèle de données avec le format approprié. Dans cette section, nous étudierons ces tables et comment ils sont utilisés.

- Ouvrez **QSDataModel.xcdatamodeld**. Il y a quatre tables définies : trois qui sont utilisés par le Kit de développement, et les éléments eux-mêmes une table pour le todo :     *MS_TableOperations : pour le suivi des éléments qui doivent être synchronisées avec le serveur    * MS_TableOperationErrors : pour le suivi de toutes les erreurs qui se produisent pendant la synchronisation hors connexion     *MS_TableConfig : pour le suivi de la dernière mise à jour de l’heure de la dernière opération de synchronisation pour toutes les opérations d’extraction    * TodoItem : Permet de stocker les éléments todo. Le système de colonnes **créédans**, **updatedAt**et la **version** sont des propriétés système facultatif.

>[AZURE.NOTE] Le Kit de développement logiciel applications Azure Mobile se réserve les noms de colonne qui est avec «**``**». Vous ne devez pas utiliser ce préfixe à autre chose que des colonnes système, sinon les noms de colonne seront modifiés lors de l’utilisation du back-end à distance.

- Lorsque vous utilisez la fonctionnalité de synchronisation en mode hors connexion, vous devez définir les tables système comme indiqué ci-dessous.

    ### <a name="system-tables"></a>Tables système

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

  	| Attribut  |    Type de     |
  	|----------- |   ------    |
  	| ID         | Entier 64  |
  	| ID d’élément     | Chaîne      |
  	| propriétés | Données binaires |
  	| table      | Chaîne      |
  	| tableKind  | Entier 16  |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

  	| Attribut  |    Type de     |
  	|----------- |   ------    |
  	| ID         | Chaîne      |
  	| operationId | Entier 64 |
  	| propriétés | Données binaires |
  	| tableKind  | Entier 16  |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

  	| Attribut  |    Type de     |
  	|----------- |   ------    |
  	| ID         | Chaîne      |
  	| clé        | Chaîne      |
  	| type de clé    | Entier 64  |
  	| table      | Chaîne      |
  	| valeur      | Chaîne      |

    ### <a name="data-table"></a>Table de données

    **TodoItem**

  	| Attribut    |  Type de   | Remarque                                                   |
  	|-----------   |  ------ | -------------------------------------------------------|
  	| ID           | Chaîne, requises  | clé primaire dans un magasin distant                            |
  	| Terminer     | Valeur booléenne | champ d’élément TODO                                        |
  	| texte         | Chaîne  | champ d’élément TODO                                        |
  	| créédans | Date    | (facultatif) correspond à la propriété du système créédans         |
  	| updatedAt | Date    | (facultatif) correspond à la propriété du système updatedAt         |
  	| Version   | Chaîne  | (facultatif) permet de détecter les conflits, correspond à la version |


## <a name="setup-sync"></a>Modifier le comportement de synchronisation de l’application

Dans cette section, vous allez modifier l’application afin qu’il n’est pas synchronisée sur le lancement de l’application, ou lors de l’insertion et de mise à jour d’éléments, mais uniquement lorsque le bouton de mouvement d’actualisation est effectué.

**Objective-C**:

1. Dans **QSTodoListViewController.m**, remplacez la méthode **viewDidLoad** pour supprimer l’appel à `[self refresh]` à la fin de la méthode. Désormais, les données ne seront pas synchronisées avec le serveur au démarrage de l’application, mais il seront à la place le contenu du magasin local.

2. Dans **QSTodoService.m**, modifiez la définition de `addItem` afin qu’il ne synchroniser une fois que l’élément est inséré. Supprimer le `self syncData` bloquer et le remplacer par le texte suivant :

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modifiez la définition de `completeItem` comme indiqué ci-dessus ; supprimer le bloc de `self syncData` et les remplacer par les éléments suivants :

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Swift**:

1. Dans `viewDidLoad` dans **ToDoTableViewController.swift**, commenter ces deux lignes, pour arrêter la synchronisation au démarrage de l’application. Au moment de la rédaction de cet article, l’application Swift Todo ne met pas à jour le service lorsque quelqu'un ajoute ou fin d’un élément, uniquement sur le lancement de l’application.

        self.refreshControl?.beginRefreshing()
        self.onRefresh(self.refreshControl)


## <a name="test-app"></a>Tester l’application

Dans cette section, vous vous connectez à une URL non valide pour simuler un scénario hors connexion. Lorsque vous ajoutez des éléments de données, ils seront stockés dans le magasin de données de base local, mais pas synchronisées avec le serveur principal mobile.

1. Modifier l’URL d’application Mobile dans **QSTodoService.m** à une URL non valide et exécutez de nouveau l’application :

    **Objective-C** dans QSTodoService.m :
    
            self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
    
    **Swift** dans ToDoTableViewController.swift :

        let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. Ajoutez des éléments todo. Fermez le simulateur (ou forcer l’application) et redémarrez. Vérifiez que vos modifications ont été rendues persistantes.

3. Afficher le contenu de la table distante de TodoItem :

    + Pour un back-end Node.js, accédez au [portail Azure](https://portal.azure.com/)et votre application Mobile back-end cliquez sur **Tables simple** > **TodoItem** pour afficher le contenu de la `TodoItem` table.
    + Pour un serveur principal de .NET, permet d’afficher le sommaire de l’aide d’un outil SQL telles que SQL Server Management Studio ou un client reste comme Fiddler ou Postman.

    Vérifiez que les nouveaux éléments n’ont *pas* été synchronisées sur le serveur :

4. Changer l’URL vers la bonne sur **QSTodoService.m** et exécutez à nouveau l’application. Effectuez le mouvement d’actualisation en tirant vers le bas de la liste des éléments. Vous verrez un compteur de progression.

5. Affichez de nouveau les données de TodoItem. TodoItems les nouvelles et modifiées doit maintenant apparaître.

## <a name="summary"></a>Résumé

Pour prendre en charge la fonctionnalité de synchronisation en mode hors connexion, nous avons utilisé le `MSSyncTable` de l’interface et l’initialisation `MSClient.syncContext` avec un magasin local. Dans ce cas, la banque locale est une base de données basée sur des données de base.

Lorsque vous utilisez un magasin de données de base locaux, vous devez définir plusieurs tables avec les [Propriétés système correctes](#review-core-data).

Les opérations CRUD normales pour les applications Azure Mobile fonctionnent comme si l’application est toujours connectée, mais toutes les opérations de se produisent contre le magasin local.

Lorsque nous voulions synchroniser le magasin local avec le serveur, nous avons utilisé le `MSSyncTable.pullWithQuery`méthode.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation de données hors connexion dans les applications mobiles Azure]

* [Couverture nuageuse : synchronisation hors connexion dans Azure Services mobiles] \(Remarque : la vidéo est sur Services mobiles, mais la synchronisation hors connexion fonctionne de la même manière dans les applications Azure Mobile\)

<!-- URLs. -->


[Créer un application d’iOS]: app-service-mobile-ios-get-started.md
[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Couverture de nuage : La synchronisation hors connexion dans Azure Services mobiles]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
