<properties
    pageTitle="Comment faire pour utiliser iOS SDK pour les applications mobiles Azure"
    description="Comment faire pour utiliser iOS SDK pour les applications mobiles Azure"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Comment faire pour utiliser iOS bibliothèque Client pour les applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide vous apprend à effectuer des scénarios courants utilisant des dernières [applications de Mobile Azure iOS SDK][1]. Si vous ne connaissez pas les applications Azure Mobile, première complète [Azure Mobile applications Quick Start] pour créer un serveur principal, créez une table et télécharger un projet de Xcode iOS prédéfinis. Dans ce guide, nous concentrer sur le côté client d’iOS SDK. Pour en savoir plus sur le Kit de développement côté serveur pour le serveur principal, voir la HOWTOs du Kit de développement logiciel serveur.

## <a name="reference-documentation"></a>Documentation de référence

La documentation de référence pour le client iOS SDK se trouve ici : [les applications Azure Mobile iOS référence Client][2].

## <a name="supported-platforms"></a>Plates-formes prises en charge

IOS SDK prend en charge les projets Objective-C, les projets Swift 2.2 ou Swift 2.3 pour iOS version 8.0 ou ultérieure.

L’authentification de « serveur-flux » utilise un mode d’affichage Web pour l’interface utilisateur présentée.  Si le périphérique n’est pas en mesure de présenter un WebView UI, puis une autre méthode d’authentification n’est nécessaire, qui est en dehors de la portée du produit.  
Ce kit de développement logiciel n’est donc pas approprié pour le type d’espion ou de la même façon sur des périphériques restreints.

##<a name="Setup"></a>Le programme d’installation et conditions requises

Ce guide suppose que vous avez créé un back-end avec une table. Ce guide suppose que la table a le même schéma que les tables dans ces didacticiels. Ce guide part également du principe que dans votre code, vous référencez `MicrosoftAzureMobile.framework` et importer `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Comment : créer des clients

Pour accéder à un back-end les applications Azure Mobile dans votre projet, vous devez créer un `MSClient`. Remplacer `AppUrl` par l’URL de l’application. Vous pouvez laisser `gatewayURLString` et `applicationKey` vide. Si vous configurez une passerelle pour l’authentification, remplir `gatewayURLString` par l’URL de la passerelle.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Comment : créer la référence de Table

À l’accès ou mise à jour des données, créer une référence à la table principale. Remplacer `TodoItem` avec le nom de votre table.

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Comment : interroger des données

Pour créer une requête de base de données, vous devez interroger la `MSTable` objet. La requête suivante obtient tous les éléments de `TodoItem` et enregistre le texte de chaque élément.

**Objective-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Comment : filtrer les données retournées

Pour filtrer les résultats, de nombreuses options sont disponibles.

Pour filtrer à l’aide d’un prédicat, utilisez un `NSPredicate` et `readWithPredicate`. Les filtres suivants a renvoyé des données pour rechercher uniquement les éléments Todo incomplètes.

**Objective-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Comment : utiliser MSQuery

Pour effectuer une requête complexe (y compris le tri et la pagination), créer un `MSQuery` objet, directement ou en utilisant un prédicat :

**Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`vous permet de contrôler plusieurs comportements de requête.

* Spécifier l’ordre des résultats
* Limiter les champs à renvoyer
* Limiter le nombre d’enregistrements pour retourner
* Spécifier le nombre total de réponse
* Spécifier les paramètres de chaîne de requête personnalisée de demande
* Appliquer des fonctions supplémentaires

Exécuter un `MSQuery` requête en appelant `readWithCompletion` sur l’objet.

## <a name="sorting"></a>Comment : trier des données avec MSQuery

Pour trier les résultats, examinons un exemple. Pour trier par 'texte' du champ dans l’ordre croissant, puis par ordre décroissant de « totale », vous devez appeler `MSQuery` comme suit :

**Objective-C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Comment : limiter les champs et développez les paramètres de chaîne de requête avec MSQuery

Pour limiter les champs soient renvoyés dans une requête, spécifiez les noms des champs dans la propriété **selectFields** . Cet exemple renvoie uniquement le texte et les champs terminés :

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Pour inclure des paramètres de chaîne de requête supplémentaires dans la demande au serveur (par exemple, dans la mesure où un script côté serveur personnalisé utilise les), remplir `query.parameters` comme suit :

**Objective-C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Comment : configurer la taille de la Page

Avec des applications Azure Mobile, la taille de la page détermine le nombre d’enregistrements qui sont extraits à la fois les tables de back-end. Un appel à `pull` données de lots puis les données, en fonction de cette taille de la page, jusqu'à ce qu’il n’y a pas plus d’enregistrements à extraire.

Il est possible de configurer une taille de page à l’aide de **MSPullSettings** comme indiqué ci-dessous. La taille de page par défaut est de 50, et l’exemple ci-dessous modifie à 3.

Vous pouvez configurer une autre taille de page pour des raisons de performances. Si vous avez un grand nombre d’enregistrements de données de petite taille, une taille de page élevée permet de réduire le nombre d’allers-retours avec le serveur. 

Ce paramètre contrôle uniquement la taille de page du côté client. Si le client demande une taille de page plus grande que le serveur principal d’applications Mobile prend en charge, la taille de page est limitée au maximum que le système principal est configuré pour prendre en charge. 

Ce paramètre est également le _nombre_ d’enregistrements de données, pas la _taille en octets_.

Si vous augmentez la taille de la page client, [vous devez également augmenter la taille de la page sur le serveur](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#_how-to-adjust-the-table-paging-size).

**Objective-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                } 
                           }];
```


**Swift**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    } 
}
```

##<a name="inserting"></a>Comment : insérer des données

Pour insérer une nouvelle ligne de table, créer un `NSDictionary` et appelez `table insert`. Si le [Schéma dynamique] est activée, le système principal de Service d’application Azure mobile génère automatiquement des nouvelles colonnes en fonction de la `NSDictionary`.

Si `id` n’est pas fourni, le back-end génère automatiquement un nouvel ID unique. Indiquez vos propres `id` par e-mail des adresses, noms d’utilisateur, ou les valeurs de votre propre personnalisé en tant que code. Fournir votre propre code peut faciliter les jointures et logique orientée métier de base de données.

Le `result` contient le nouvel élément a été inséré. En fonction de la logique du serveur, il peut avoir des données supplémentaires ou modifiées par rapport à ce qui a été transmis au serveur.

**Objective-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Comment : modifier des données

Pour mettre à jour une ligne existante, modifier un article et un appel `update`:

**Objective-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Vous pouvez également fournir le numéro de ligne et le champ mis à jour :

**Objective-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Au minimum, le `id` attribut doit être défini lorsque vous effectuez des mises à jour.

##<a name="deleting"></a>Comment : supprimer des données

Pour supprimer un élément, vous devez appeler `delete` avec l’élément :

**Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Vous pouvez également supprimer en fournissant un ID de ligne :

**Objective-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Au minimum, le `id` attribut doit être défini lors de la fabrication supprime.

##<a name="customapi"></a>Comment : appeler des API personnalisé

Avec une API personnalisée, vous pouvez exposer toutes les fonctionnalités back-end. Il n’est pas mapper à une opération de la table. Non seulement avoir davantage de contrôle sur la messagerie, vous pouvez même en lecture/jeu d’en-têtes et de modifier le format de corps de réponse. Pour savoir comment créer une API personnalisée sur le serveur principal, lire [Des API personnalisé](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Pour appeler une API personnalisée, appelez `MSClient.invokeAPI`. La demande et la réponse de contenu sont traités en tant que JSON. Pour utiliser d’autres types de médias, [Utilisez l’autre surcharge de `invokeAPI` ] [ 5].  Pour rendre une `GET` demande au lieu d’un `POST` demande, paramètre de jeu de `HTTPMethod` à `"GET"` et le paramètre `body` à `nil` (dans la mesure où les requêtes GET n’ont pas les corps de message.) Si votre API personnalisé prend en charge les autres verbes HTTP, modifier `HTTPMethod` correctement.

**Objective-C**:

```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Comment : modèles de push de Registre pour envoyer des notifications de multiplates-formes

Pour enregistrer les modèles, passer des modèles avec votre méthode de **registerDeviceToken de client.push** dans votre application cliente.

**Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Vos modèles sont de type NSDictionary et peuvent contenir plusieurs modèles dans le format suivant :

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Toutes les balises sont supprimés de la demande de sécurité.  Pour ajouter des balises aux installations ou aux modèles dans les installations, reportez-vous à [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure][4].  Pour envoyer des notifications à l’aide de ces modèles enregistrés, travailler avec les [API de concentrateurs de Notification][3].

##<a name="errors"></a>Comment : gérer les erreurs

Lorsque vous appelez un back-end mobile de Service d’application Azure, le bloc de fin contient un `NSError` paramètre. Lorsqu’une erreur se produit, ce paramètre est n’est pas nil. Dans votre code, vérifiez ce paramètre et gérer l’erreur en fonction des besoins, comme cela est indiqué dans les extraits de code précédents.

Le fichier [`<WindowsAzureMobileServices/MSError.h>`] [6] définit les constantes de `MSErrorResponseKey`, `MSErrorRequestKey`, et `MSErrorServerItemKey`. Pour obtenir des données supplémentaires relatives à l’erreur :

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

En outre, le fichier définit des constantes pour chaque code d’erreur :

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Comment : authentifier les utilisateurs avec la bibliothèque d’authentification Active Directory

Vous pouvez utiliser la bibliothèque de l’authentification Active Directory (ADAL) pour connecter les utilisateurs dans votre application à l’aide d’Azure Active Directory. Authentification de flux du client à l’aide d’un fournisseur d’identité SDK est préférable à l’utilisation de la `loginWithProvider:completion:` méthode.  L’authentification du client flux fournit un aspect UX plus natif et permet une personnalisation supplémentaire.

1. Configurer votre back-end de l’application mobile pour DAS connexion en suivant [la configuration du Service d’application pour la connexion Active Directory] [ 7] didacticiel. Veillez à terminer l’étape facultative de l’inscription d’une application client natif. Pour iOS, il est recommandé que la redirection URI est de la forme `<app-scheme>://<bundle-id>`. Pour plus d’informations, consultez le [démarrage rapide d’e/s ADAL][8].

2. Installez ADAL à l’aide de Cocoapods. Modifier vos Podfile pour inclure la définition suivante, en remplaçant **Votre projet** avec le nom de votre projet Xcode :

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   et le bloc :

        pod 'ADALiOS'

3. À l’aide du Terminal, exécutez `pod install` depuis le répertoire contenant votre projet et ouvrez l’espace de travail Xcode généré (et non sur le projet).

4. Ajoutez le code suivant à votre application, en fonction du langage que vous utilisez. Dans chaque cas, effectuer ces replacements :

    * Remplacer **l’Insertion-autorité-ici** avec le nom du locataire dans lequel vous déployé votre application. Le format doit être https://login.windows.net/contoso.onmicrosoft.com. Cette valeur peut être copiée à partir de l’onglet domaine dans Azure Active Directory [portail classique Azure].
    * Remplacez **Insertion-RESOURCE-ID-ici** l’ID client pour votre back-end de l’application mobile. Vous pouvez obtenir l’ID de client à partir de l’onglet **Avancé** , sous **Les paramètres Active Directory Azure** dans le portail.
    * Remplacer **l’Insertion-CLIENT-ID-ici** avec l’ID de client que vous avez copié à partir de l’application client natif.
    * Remplacer **l’Insertion-redirection-URI-ici** avec le point de terminaison de votre site _/.auth/login/done_ , à l’aide du schéma HTTPS. Cette valeur doit être similaire à _https://contoso.azurewebsites.net/.auth/login/done_.

**Objective-C**:

    #import <ADALiOS/ADAuthenticationContext.h>
    #import <ADALiOS/ADAuthenticationSettings.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*))completionBlock;
    {
        NSString *authority = @"INSERT-AUTHORITY-HERE";
        NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
        NSString *clientId = @"INSERT-CLIENT-ID-HERE";
        NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
        ADAuthenticationError *error;
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
        authContext.parentController = parent;
        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:resourceId
                                     clientId:clientId
                                  redirectUri:redirectUri
                              completionBlock:^(ADAuthenticationResult *result) {
                                  if (result.status != AD_SUCCEEDED)
                                  {
                                      completionBlock(nil, result.error);;
                                  }
                                  else
                                  {
                                      NSDictionary *payload = @{
                                                                @"access_token" : result.tokenCacheStoreItem.accessToken
                                                                };
                                      [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                  }
                              }];
    }


**Swift**:

    // add the following imports to your bridging header:
    //      #import <ADALiOS/ADAuthenticationContext.h>
    //      #import <ADALiOS/ADAuthenticationSettings.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let authority = "INSERT-AUTHORITY-HERE"
        let resourceId = "INSERT-RESOURCE-ID-HERE"
        let clientId = "INSERT-CLIENT-ID-HERE"
        let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
        var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
        let authContext = ADAuthenticationContext(authority: authority, error: error)
        authContext.parentController = parent
        ADAuthenticationSettings.sharedInstance().enableFullScreen = true
        authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
                if result.status != AD_SUCCEEDED {
                    completion(nil, result.error)
                }
                else {
                    let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                    client.loginWithProvider("aad", token: payload, completion: completion)
                }
            }
    }

## <a name="facebook-sdk"></a>Comment : authentifier les utilisateurs avec le Facebook SDK pour iOS

Le Facebook SDK pour iOS vous permet de connecter des utilisateurs à votre application à l’aide de Facebook.  À l’aide d’une authentification de flux du client est préférable à l’utilisation de la `loginWithProvider:completion:` méthode.  L’authentification de flux client fournit un aspect UX plus natif et permet une personnalisation supplémentaire.

1. Configurer votre back-end de l’application mobile pour la connexion à Facebook en suivant [la configuration du Service d’application pour la connexion Facebook] [ 9] didacticiel.

2. Installer le Facebook SDK pour iOS en suivant le [Kit de développement Facebook pour iOS - mise en route] [ 10] documentation. Au lieu de créer une application, vous pouvez ajouter la plateforme iOS à votre enregistrement. 

3. Documentation de Facebook inclut du code Objective-C dans le délégué de l’application. Si vous utilisez **Swift**, vous pouvez utiliser les conversions suivantes pour AppDelegate.swift :
  
        // Add the following import to your bridging header:
        //      #import <FBSDKCoreKit/FBSDKCoreKit.h>
        
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }

4. Outre l’ajout de `FBSDKCoreKit.framework` à votre projet, également ajouter une référence à `FBSDKLoginKit.framework` de la même manière. 

4. Ajoutez le code suivant à votre application, en fonction du langage que vous utilisez. 

**Objective-C**:

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {       
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**Swift**:

    // Add the following imports to your bridging header:
    //      #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //      #import <FBSDKCoreKit/FBSDKAccessToken.h>
    
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>Comment : authentifier les utilisateurs avec Twitter de tissu pour iOS

Fabric d’e/s vous permet de déconnecter des utilisateurs dans votre application à l’aide de Twitter. Flux de l’authentification du client est préférable à l’utilisation de la `loginWithProvider:completion:` de la méthode, il fournit un aspect UX plus natif et permet la personnalisation supplémentaire.

1. Configurer votre back-end de l’application mobile pour la connexion à Twitter en suivant le didacticiel [comment configurer le Service d’application pour connexion à Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) .

2. Ajouter le Fabric à votre projet en [tissu pour iOS - mise en route] de la documentation et de configuration de TwitterKit.

    > [AZURE.NOTE] Par défaut, Fabric crée une application Twitter. Vous pouvez éviter la création d’une application par enregistrement de la clé de consommateur et le Secret de consommateur que vous avez créé précédemment à l’aide d’extraits de code suivants.  Sinon, vous pouvez remplacer les valeurs de clé de consommateur et Secret de consommateur que vous fournissez au Service de l’application avec les valeurs affichées dans le [Tableau de bord de Fabric]. Si vous choisissez cette option, veillez à définir l’URL de rappel à une valeur d’espace réservé, tel que `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Si vous choisissez d’utiliser les secrets que vous avez créé précédemment, ajoutez le code suivant à votre délégué de l’application :
    
    **Objective-C**:

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }
        
    **Swift**:
    
        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
    
3. Ajoutez le code suivant à votre application, en fonction du langage que vous utilisez. 

**Objective-C**:

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**Swift**:

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>Comment : authentifier les utilisateurs avec Google connexion SDK pour iOS

Google connexion SDK pour iOS permet de connecter des utilisateurs à votre application à l’aide d’un compte Google.  Google a récemment annoncé que les modifications apportées à leurs stratégies de sécurité OAuth.  Ces modifications de stratégie exige l’utilisation du SDK de Google à l’avenir.

1. Configurer votre back-end de l’application mobile pour Google connexion en suivant le didacticiel [comment configurer le Service d’application pour la connexion de Google](app-service-mobile-how-to-configure-google-authentication.md) .

2. Installez le SDK de Google pour iOS par [Google reconnectez-vous pour iOS - démarrer l’intégration](https://developers.google.com/identity/sign-in/ios/start-integrating) de la documentation. Vous pouvez ignorer la section « Authentification avec un serveur principal ».

3. Ajoutez le code suivant à votre délégué `signIn:didSignInForUser:withError:` méthode, selon le langage que vous utilisez.

**Objective-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };
        
        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Swift**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

4. Assurez-vous que vous ajoutez également ce qui suit à `application:didFinishLaunchingWithOptions:` dans votre délégué de l’application, remplacer « SERVER_CLIENT_ID » avec le même ID que vous avez utilisé pour configurer l’application de Service à l’étape 1.

**Objective-C**:

        [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
 
 **Swift**:
 
        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"

 
 5. Ajoutez le code suivant à votre application dans un UIViewController qui implémente la `GIDSignInUIDelegate` protocole, selon le langage que vous utilisez.  Vous êtes déconnecté avant d’être signé à nouveau, et même si vous n’avez pas besoin d’entrer de nouveau vos informations d’identification, vous voyez une boîte de dialogue de consentement.  Appelez cette méthode uniquement lorsque le jeton de session a expiré.
 
 **Objective-C**:

        #import <Google/SignIn.h>
        // ...
        - (void)authenticate
        {
                [GIDSignIn sharedInstance].uiDelegate = self;
                [[GIDSignIn sharedInstance] signOut];
                [[GIDSignIn sharedInstance] signIn];
        }
 
 **Swift**:
    
        // ...
        func authenticate() {
            GIDSignIn.sharedInstance().uiDelegate = self
            GIDSignIn.sharedInstance().signOut()
            GIDSignIn.sharedInstance().signIn()
        }
        
<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Guide de démarrage rapide de Apps Mobile Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Schéma dynamique]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Tableau de bord de tissu]: https://www.fabric.io/home
[TISSU pour iOS - mise en route]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem
[9]: app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
