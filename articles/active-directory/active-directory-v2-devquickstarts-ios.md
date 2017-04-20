<properties
    pageTitle="IOS de v2.0 Azure AD App | Microsoft Azure"
    description="Comment générer une application iOS qui signe dans les comptes d’utilisateurs avec les deux compte Microsoft personnel et Professionnel ou à l’école à l’aide de bibliothèques tierces."
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="brandwe"/>

# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Ajoutez connexion à une application iOS à l’aide d’une bibliothèque tierce avec les API de graphique à l’aide de l’extrémité de la version 2.0

La plate-forme d’identité de Microsoft utilisant des normes ouvertes telles que OAuth2 et OpenID se connecter. Les développeurs peuvent utiliser n’importe quelle bibliothèque qu’ils souhaitent intégrer grâce à nos services. Pour aider les développeurs à utiliser notre plate-forme avec d’autres bibliothèques, nous avons écrit quelques procédures pas à pas comme celui-ci pour montrer comment configurer des bibliothèques tierces pour se connecter à la plate-forme d’identité de Microsoft. La plupart des bibliothèques qui implémentent [la spécification RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) pouvez vous connecter à la plate-forme d’identité de Microsoft.

Avec l’application qui crée de cette procédure pas à pas, les utilisateurs peuvent se connecter à leur organisation et puis rechercher d’autres utilisateurs de leur entreprise à l’aide de l’API du graphique.

Si vous êtes novice dans OAuth2 ou OpenID de se connecter, de cet exemple de configuration ne peut pas de sens pour vous. Nous vous conseillons de lire [les protocoles v2.0 - flux de Code OAuth 2.0 d’autorisation](active-directory-v2-protocols-oauth-code.md) pour l’arrière-plan.


> [AZURE.NOTE]
    Certaines fonctionnalités de notre plate-forme qui n’ont pas une expression dans les normes OAuth2 ou OpenID de se connecter, par exemple l’accès conditionnel et de gestion des stratégies de Intune vous obligent à utiliser nos bibliothèques d’identité Microsoft Azure ouvrir la source.

Le point de terminaison v2.0 ne supporte pas toutes les fonctionnalités et scénarios d’Azure Active Directory.

> [AZURE.NOTE]
    Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="download-code-from-github"></a>Télécharger le code à partir de GitHub
Le code de ce didacticiel est maintenue [sur GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Pour suivre l’exemple, vous pouvez [Télécharger le squelette de l’application sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou cloner le squelette :

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Vous pouvez également téléchargez l’exemple et commencez dès maintenant :

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Enregistrer une application
Créer une nouvelle application sur le [portail de l’enregistrement d’Application](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez les étapes détaillées à [l’enregistrement d’une application avec le point de terminaison v2.0](active-directory-v2-app-registration.md).  Veillez à :

- Copiez l' **Id de l’Application** qui est assigné à votre application, car vous en aurez besoin plus rapidement.
- Ajouter la plate-forme **Mobile** pour votre application.
- Copier l' **URI de redirection** à partir du portail. Vous devez utiliser la valeur par défaut de `urn:ietf:wg:oauth:2.0:oob`.


## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Télécharger la bibliothèque NXOAuth2 de tiers et créer un espace de travail

Pour cette procédure pas à pas, vous allez utiliser le OAuth2Client de GitHub, qui est une bibliothèque de OAuth2 pour Mac OS X et iOS (cacao et ses toucher). Cette bibliothèque est basée sur le brouillon 10 de la spécification OAuth2. Il implémente le profil de l’application d’origine et le point de terminaison de l’autorisation de l’utilisateur. Ce sont toutes les choses que vous devez intégrer à la plate-forme d’identité de Microsoft.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Ajouter la bibliothèque à votre projet à l’aide de CocoaPods

CocoaPods est un gestionnaire de dépendance pour les projets de Xcode. Il gère automatiquement les étapes de l’installation précédente.

```
$ vi Podfile
```
1. Ajoutez le code suivant pour cette podfile :

    ```
     platform :ios, '8.0'

     target 'QuickStart' do

     pod 'NXOAuth2Client'

     end
    ```

2. Charger la podfile à l’aide de CocoaPods. Cette opération crée un nouvel espace de travail Xcode que vous allez charger.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Explorer la structure du projet

La structure suivante est définie pour notre projet dans le squelette :

- Un mode de masque avec une recherche de nom UPN
- Une vue détaillée, pour les données concernant l’utilisateur sélectionné
- Une vue de connexion où un utilisateur peut se connecter à l’application pour interroger le graphique

Nous déplacera vers plusieurs fichiers dans le squelette pour ajouter l’authentification. Autres parties du code, tel que le code visual, n’appartiennent pas à l’identité, mais elles sont fournies pour vous.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Configurer le fichier settings.plst dans la bibliothèque

-   Dans le projet de démarrage rapide, ouvrez le `settings.plist` fichier. Remplacez les valeurs des éléments dans la section selon les valeurs que vous avez utilisé dans le portail Azure. Votre code font référence à ces valeurs chaque fois qu’il utilise la bibliothèque d’authentification Active Directory.
    -   Le `clientId` est l’ID de client de l’application que vous avez copié à partir du portail.
    -   Le `redirectUri` est l’URL de redirection que le portail fourni.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Configurer la bibliothèque NXOAuth2Client dans votre LoginViewController

La bibliothèque NXOAuth2Client requiert certaines valeurs pour être défini. Une fois cette tâche terminée, vous pouvez utiliser le jeton rachetée d’appeler l’API graphique. Car `LoginView` sera appelé chaque fois que nous avons d’abord s’authentifier, il est judicieux de placer les valeurs de configuration de ce fichier.

- Nous allons ajouter des valeurs à la `LoginViewController.m` fichier pour définir le contexte pour l’authentification et l’autorisation. Plus d’informations sur les valeurs de suivent le code.

    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Nous allons voir plus d’informations sur le code.

La première chaîne est de `scopes`.  Le `User.Read` valeur vous permet de lire le profil de base de l’utilisateur connecté.

Pour plus d’informations sur toutes les étendues disponibles dans des [portées d’autorisation de Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Pour `authURL`, `loginURL`, `bhh`, et `tokenURL`, vous devez utiliser les valeurs fournies précédemment. Si vous utilisez Microsoft Azure identité bibliothèques open source, nous extraire ces données vers le bas pour vous à l’aide de notre point de terminaison de métadonnées. Nous avons fait le travail d’extraction de ces valeurs pour vous.

Le `keychain` valeur est le conteneur qui utilise la bibliothèque NXOAuth2Client pour créer une chaîne de clé pour stocker vos jetons. Si vous souhaitez obtenir de session unique (SSO) entre plusieurs applications, vous pouvez spécifier le même trousseau dans chacune de vos applications et demander l’utilisation de ce trousseau dans vos droits Xcode. Cela est expliqué dans la documentation d’Apple.

Le reste de ces valeurs sont requises pour utiliser la bibliothèque et créer des emplacements pour réaliser les valeurs au contexte.

### <a name="create-a-url-cache"></a>Créer un cache d’URL

À l’intérieur de `(void)viewDidLoad()`, qui est toujours appelée après le chargement de l’affichage, le code suivant attribue à un cache pour notre usage.

Ajoutez le code suivant :

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Créer un affichage en mode Web pour la connexion

Un affichage en mode Web peut demander à l’utilisateur pour les autres facteurs comme le message texte SMS (si configuré) ou renvoyer des messages d’erreur à l’utilisateur. Ici, vous pouvez définir jusqu'à l’affichage Web et ensuite écrire ultérieurement le code pour gérer les rappels qui se produit dans l’affichage Web dans les services d’identité.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Substituer les méthodes d’affichage Web pour gérer l’authentification

Pour indiquer le mode d’affichage Web ce qui se produit lorsqu’un utilisateur doit se connecter comme indiqué précédemment, vous pouvez coller le code suivant.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Écrire du code pour gérer le résultat de la demande de OAuth2

Le code suivant traite le redirectURL qui renvoie à partir de l’affichage en mode Web. Si l’authentification n’a pas réussie, le code va essayer à nouveau. Pendant ce temps, la bibliothèque fournit l’erreur que vous pouvez voir dans la console ou gérer de façon asynchrone.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Définir le contexte OAuth (appelée magasin de comptes)

Ici, vous pouvez appeler `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` sur la banque de comptes partagés pour chaque service que vous souhaitez être en mesure d’accéder à l’application. Le type de compte est une chaîne qui est utilisée comme identificateur pour un service déterminé. Étant donné que vous accédez à l’API du graphique, le code fait référence à en tant que `"myGraphService"`. Puis, vous configurez un observateur qui vous avertira lorsque quelque chose change avec le jeton. Après avoir obtenu le jeton, vous revenez l’utilisateur précédent pour le `masterView`.



```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Configurer le mode Masque pour rechercher et afficher les utilisateurs de l’API de graphique

Une application maître-View-Controller (MVC) qui affiche les données retournées dans la grille est dépasse le cadre de cette procédure pas à pas et de nombreux didacticiels en ligne expliquent comment créer un. Ce code se trouve dans le fichier squelette. Toutefois, vous devez faire des choses dans cette application MVC :

* Intercepter lorsqu’un utilisateur tape quelque chose dans le champ de recherche
* Fournir un objet de données vers le MasterView afin d’afficher les résultats dans la grille

Nous nous chargeons ceux ci-dessous.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Ajouter un contrôle pour vérifier si vous êtes connecté

L’application effectue peu si l’utilisateur n’est pas signé, il est judicieux de vérifier s’il existe déjà un jeton dans le cache. Si ce n’est pas le cas, vous redirigez vers le LoginView pour ouvrir une session pour l’utilisateur. Si vous vous rappelez, la meilleure façon pour effectuer des actions lors du charge d’une vue consiste à utiliser le `viewDidLoad()` méthode qui nous fournit par Apple.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Mettre à jour la Table lors de la réception de données

Lorsque l’API graphique renvoie les données, vous devez afficher les données. Pour plus de simplicité, ce qui est tout le code pour mettre à jour la table. Vous pouvez simplement Coller les valeurs dans votre code réutilisable MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Fournir un moyen d’appeler l’API graphique lorsqu’un utilisateur tape dans le champ de recherche

Lorsqu’un utilisateur tape une recherche dans la zone, vous devez venu que l’API graphique. Le `GraphAPICaller` (classe), que vous allez générer dans le code suivant, sépare la fonctionnalité de recherche de la présentation. Pour l’instant, nous allons écrire le code qui alimente tous les caractères à l’API de graphique. Pour ce faire, nous fournissant une méthode appelée `lookupInGraph`, qui accepte la chaîne à rechercher.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Écrire une classe d’assistance pour accéder à l’API de graphique

C’est au cœur de notre application. Considérant que le reste a été insérer du code dans le modèle MVC par défaut à partir de Apple, ici vous écrivez du code pour interroger le graphique en tant que l’utilisateur tape et puis de renvoyer ces données. Voici le code et suit une explication détaillée.

### <a name="create-a-new-objective-c-header-file"></a>Créer un nouveau fichier d’en-tête C d’objectif

Nommez le fichier `GraphAPICaller.h`et ajoutez le code suivant.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Ici, vous voyez qu’une méthode spécifiée prend une chaîne et renvoie un completionBlock. Cette completionBlock, comme vous l’avez peut-être deviné, met à jour la table en fournissant un objet alimentés en données en temps réel comme la recherche de l’utilisateur.


### <a name="create-a-new-objective-c-file"></a>Créer un nouveau fichier d’objectif C

Nommez le fichier `GraphAPICaller.m`et ajoutez la méthode suivante.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Examinons cette méthode en détail.

Le cœur de ce code se trouve dans le `NXOAuth2Request`, la méthode qui prend les paramètres que vous avez déjà définie dans le fichier settings.plist.

La première étape consiste à construire l’appel API de graphique de droite. Comme vous appelez `/users`, vous spécifiez qu’en l’ajoutant à la ressource de l’API du graphique avec la version. Il est logique de placer ces paramètres dans un fichier de paramètres externe car il peuvent changer l’évolution de l’API.


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Ensuite, vous devez spécifier les paramètres qui vous fournira également à l’appel de l’API du graphique. Il est *très important* que vous ne placez pas les paramètres dans le point de terminaison de ressources car qui est modifié pour tous les caractères conforme non-URI lors de l’exécution. Tout code de requête doit être fourni dans les paramètres.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Vous remarquerez que cela appelle une `convertParamsToDictionary` méthode que vous n’avez pas encore écrit. Procédons maintenant à la fin du fichier :

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Ensuite, nous allons utiliser le `NXOAuth2Request` méthode d’obtenir des données à partir de l’API dans le format JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Enfin, nous allons voir comment vous renvoyez les données vers le MasterViewController. Les données renvoie tel qu’il est sérialisé et doivent être désérialisé et chargé dans un objet pouvant être consommé par le MainViewController. À cette fin, le squelette a une `User.m/h` fichier qui crée un objet utilisateur. Vous remplissez cet objet utilisateur avec les informations à partir du graphique.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Exécuter l’exemple

Si vous avez utilisé le squelette ou suivi ainsi que de la procédure pas à pas, que votre application doit s’exécuter maintenant. Lancer le simulateur et cliquez sur **Ouvrir une session** pour utiliser l’application.

## <a name="get-security-updates-for-our-product"></a>Obtenir des mises à jour de sécurité pour notre produit

Nous vous encourageons à obtenir des notifications d’incidents de sécurité se produire en visitant le [Centre de sécurité TechNet](https://technet.microsoft.com/security/dd252948) et s’abonner à l’alerte de sécurité.
