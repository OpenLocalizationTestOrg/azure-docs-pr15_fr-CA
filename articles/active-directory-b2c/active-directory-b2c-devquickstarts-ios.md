<properties
    pageTitle="Azure B2C de répertoire actif : Appeler un API web à partir d’une application iOS en utilisant les bibliothèques tierces | Microsoft Azure"
    description="Cet article vous explique comment créer une application de « liste de tâches » iOS qui appelle une API de web Node.js à l’aide des jetons de porteur de OAuth 2.0 à l’aide d’une bibliothèque tierce partie"
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

< balises ms.service= « active-directory-b2c » ms.workload="identity » ms.tgt_pltfrm="na » ms.devlang="objectivec » ms.topic= « héros-article »

    ms.date="07/26/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c--call-a-web-api-from-an-ios-application-using-a-third-party-library"></a>Azure AD B2C : Appeler un API web à partir d’une application iOS à l’aide d’une bibliothèque tierce partie

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

La plate-forme d’identité de Microsoft utilisant des normes ouvertes telles que OAuth2 et OpenID se connecter. Cela permet aux développeurs de tirer parti de toutes les bibliothèques qu’ils souhaitent intégrer grâce à nos services. Pour aider les développeurs à l’aide de notre plate-forme avec d’autres bibliothèques, nous avons écrit quelques procédures pas à pas comme celui-ci pour illustrer comment configurer des bibliothèques tierces pour se connecter à la plate-forme d’identité de Microsoft. La plupart des bibliothèques qui implémentent [la spécification RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) sera en mesure de se connecter à la plate-forme Microsoft Identity.


Si vous êtes nouveau sur OAuth2 ou OpenID de se connecter de cet exemple de configuration ne pensera pas beaucoup à vous. Nous vous recommandons de que vous regardez une brève [vue d’ensemble du protocole que nous avons détaillés ici](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Certaines fonctionnalités de notre plate-forme qui n’ont pas une expression dans ces normes, telles que la gestion de stratégie accès conditionnel et Intune, vous obligent à utiliser nos bibliothèques d’identité Microsoft Azure ouvrir la source. 
   
Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par la plate-forme B2C.  Pour déterminer si vous devez utiliser la plate-forme B2C, obtenir des informations sur les [limitations de B2C](active-directory-b2c-limitations.md).


## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire, ou client. Un répertoire est un conteneur pour tous vos utilisateurs, applications, groupes et bien plus encore. Si vous n’en avez pas déjà, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de continuer.

## <a name="create-an-application"></a>Création d’une application

Ensuite, vous devez créer une application dans votre répertoire B2C. Ainsi, les informations AD Azure dont il a besoin pour communiquer en toute sécurité avec votre application. L’application et l’API web sont représentées dans ce cas, par un seul **ID d’Application** parce qu’elles comprennent une application logique. Pour créer une application, suivez [les instructions](active-directory-b2c-app-registration.md). Veillez à :

- Inclure un **périphérique mobile** dans l’application.
- Copiez l' **ID de l’Application** qui est assigné à votre application. Vous devez également cela plus tard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure B2C d’Active Directory, chaque expérience de l’utilisateur est défini par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient une seule expérience d’identité : un signe combiné d’entrée et d’abonnement. Vous devez créer cette stratégie de chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lorsque vous créez la stratégie, veillez à :

- Choisissez le **nom** et les attributs d’abonnement dans votre stratégie.
- Choisissez les demandes d’application de **nom complet** et **l’ID d’objet** dans chaque stratégie. Vous pouvez choisir les autres déclarations.
- Copier le **nom** de chaque stratégie après que l’avoir créé. Il doit avoir le préfixe `b2c_1_`.  Vous devez ensuite le nom de la stratégie.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Après avoir créé vos stratégies, vous êtes prêt à créer votre application.


## <a name="download-the-code"></a>Télécharger le code

Le code de ce didacticiel est maintenue [sur GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c).  Pour suivre l’exemple, vous pouvez [Télécharger l’application sous la forme d’un fichier zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)de /archive/master.zip) ou le cloner :

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Vous pouvez également télécharger le code complété et commencer immédiatement : 

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## <a name="download-the-third-party-library-nxoauth2-and-launch-a-workspace"></a>Téléchargez le nxoauth2 de bibliothèque tierce et lancer un espace de travail

Pour cette procédure pas à pas, nous allons utiliser le OAuth2Client de GitHub, une bibliothèque de OAuth2 pour Mac OS X et iOS (cacao & cacao tactile). Cette bibliothèque est basée sur le brouillon 10 de la spécification OAuth2. Il implémente le profil de l’application d’origine et le point de terminaison de l’autorisation de l’utilisateur final. Voici tout ce que nous avons besoin dans l’ordre à développe avec la plate-forme d’identité de Microsoft.

### <a name="adding-the-library-to-your-project-using-cocoapods"></a>Ajout de la bibliothèque de votre projet à l’aide de CocoaPods

CocoaPods est un gestionnaire de dépendance pour les projets de Xcode. Il gère automatiquement les étapes de l’installation ci-dessus.

```
$ vi Podfile
```
Ajoutez le code suivant pour cette podfile :

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Maintenant charger la podfile à l’aide de cocoapods. Cette opération crée un nouvel espace de travail XCode vous sera chargé.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## <a name="the-structure-of-the-project"></a>La structure du projet

Nous avons la structure suivante définie pour notre projet dans le squelette :

* Un **Mode Masque** un volet Office
* Un **Affichage des tâches ajouter** pour les données relatives à la tâche sélectionnée
* **Mode de connexion** qui permet à un utilisateur de se connecter à l’application.

Nous accédera dans différents fichiers projet pour ajouter l’authentification. Les autres parties du code tel que le code visual n’est pas pertinentes pour l’identité et sont fournis pour vous.

## <a name="create-the-settingsplist-file-for-your-application"></a>Créer le `settings.plist` fichier de votre application

Il est plus facile de configurer l’application, si nous avons un emplacement centralisé pour les valeurs de la configuration. Il vous permet également de comprendre la signification de chaque paramètre dans votre application. Nous mettons à profit la *Liste de propriétés* sous la forme d’un moyen de fournir ces valeurs à l’application.

* Créer/ouvrir la `settings.plist` de fichiers sous `Supporting Files` dans votre espace de travail

* Entrez les valeurs suivantes (nous allons étudier les détails bientôt)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Allez dans en détail.


Pour `authURL`, `loginURL`, `bhh`, `tokenURL` vous remarquerez que vous devez remplir le nom de votre client. C’est le nom du locataire de vos clients B2C qui vous a été affectée. Par exemple, `kidventusb2c.onmicrosoft.com`. Si vous utilisez notre open source Microsoft Azure identité bibliothèques nous d’extraction ces données vers le bas pour vous à l’aide de notre point de terminaison de métadonnées. Nous avons fait le travail d’extraction de ces valeurs pour vous.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Le `keychain` valeur est le conteneur qui utilise la bibliothèque NXOAuth2Client pour créer une chaîne de clé pour stocker vos jetons. Si vous souhaitez obtenir l’authentification unique dans les nombreuses applications vous pouvez spécifier le même trousseau dans chacune de vos applications ainsi que demander l’utilisation de ce trousseau dans votre entitements XCode. Ce sujet est traité dans la documentation d’Apple.

Le `<policy name>` à la fin de chaque URL sont les endroits où vous voulez placés la stratégie que vous avez créé ci-dessus. L’application appelle ces stratégies selon le flux.

Le `taskAPI` est le point de terminaison reste que nous appellerons avec votre jeton B2C, soit ajouter les tâches ou requête existante. Cela a été définie spécifiquement pour cet exemple. Vous n’avez pas besoin de le changer pour que l’exemple fonctionne.

Le reste de ces valeurs sont requises pour utiliser la bibliothèque et créez simplement les endroits pour réaliser les valeurs au contexte.

Maintenant que nous avons la `settings.plist` fichier créé, nous avons besoin de code pour le lire.

## <a name="set-up-a-appdata-class-to-read-our-settings"></a>Définir une classe AppData pour lire les paramètres

Créons un fichier simple qui analyse simplement notre `settngs.plist` fichier, nous avons créé ci-dessus et rendre ces disque de paramètres à l’avenir à n’importe quelle classe. Étant donné que nous ne souhaitez pas créer une nouvelle copie des données chaque fois qu’une classe de demande, nous utilisera un modèle Singleton et ne renvoyer que la même instance créée chaque fois qu’une demande est faite pour les paramètres

* Créer un `AppData.h` fichier :

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Créer un `AppData.m` fichier :

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Maintenant nous pouvons facilement obtenir nos données en appelant simplement `  AppData *data = [AppData getInstance];` dans un de nos classes comme vous le verrez ci-dessous.



## <a name="set-up-the-nxoauth2client-library-in-your-appdelegate"></a>Configurer la bibliothèque NXOAuth2Client dans votre AppDelegate

La bibliothèque NXOAuthClient requiert certaines valeurs pour être défini. Une fois cette opération terminée vous pouvez utiliser le jeton qui est entré pour appeler l’API REST. Car nous savons que la `AppDelegate` sera appelée chaque fois que nous charger l’application justifie que nous mettons nos valeurs de configuration de ce fichier.
* Ouvrir `AppDelegate.m` fichier

* Importer des fichiers d’en-tête, nous utiliserons plus tard.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Ajouter le `setupOAuth2AccountStore` méthode dans le AppDelegate

Nous devons créer un AccountStore et placez ensuite les données que nous viennent d’être lues à partir de la `settings.plist` fichier.

Il existe certaines choses que vous devez être conscient des relatives à ce stade au service de B2C rendre ce code plus compréhensibles :


1. Azure AD B2C utilise la *stratégie* tel que prévu par les paramètres de requête pour répondre à votre demande. Cela permet d’Azure Active Directory agir comme un service indépendant pour votre application. Pour fournir ces paramètres nous devons fournir de requête supplémentaire à la `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` méthode avec ses paramètres de stratégie personnalisée. 

2. Azure AD B2C utilise étendues pratiquement identique en tant que d’autres serveurs OAuth2. Cependant dans la mesure où l’utilisation de B2C est tout autant une question d’authentification d’un utilisateur en tant que de l’accès aux ressources des étendues sont absolument nécessaire pour le flux fonctionne correctement. Il s’agit de la `openid` étendue. Notre identité Microsoft SDK fournissent automatiquement le `openid` étendue pour vous afin que vous ne verrez que dans notre configuration du Kit de développement logiciel. Toutefois, dans la mesure où nous utilisons une bibliothèque tierce, nous devons spécifier cette étendue.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Ensuite, assurez-vous que vous l’appelez dans le AppDelegate sous `didFinishLaunchingWithOptions:` méthode. 

```
[self setupOAuth2AccountStore];
```


## <a name="create-a-loginviewcontroller-class-that-we-will-use-to-handle-authentication-requests"></a>Créer un `LoginViewController` classe que nous utiliserons pour gérer les demandes d’authentification

Nous utilisons un affichage en mode Web pour connecter à compte. Cela nous permet à l’utilisateur d’autres facteurs comme le message texte SMS (si configuré) ou, rendez les messages d’erreur à l’utilisateur. Ici, nous allons définir jusqu'à l’affichage Web et ensuite écrire ultérieurement le code pour gérer les rappels qui se produit dans l’affichage Web à partir du Service d’identité de Microsoft.

* Créer un `LoginViewController.h` classe

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Nous allons créer chacune de ces méthodes ci-dessous.

> [AZURE.NOTE] 
    Assurez-vous que vous liez le `loginView` à webview réelle qui est à l’intérieur de votre table de montage séquentiel. Dans le cas contraire, vous n’avez un affichage Web qui peut apparaître lorsqu’il est temps de s’authentifier.

* Créer un `LoginViewController.m` classe

* Ajouter des variables pour exécuter l’état comme nous authentifier

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Substituer les méthodes d’affichage Web pour gérer l’authentification

Nous devons indiquer le mode d’affichage Web le comportement souhaité lorsque l’utilisateur doit se connecter comme indiqué ci-dessus. Vous pouvez simplement coupez et collez le code ci-dessous.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Écrire du code pour gérer le résultat de la demande de OAuth2

Nous avons besoin de code qui gèrera le redirectURL revient à partir de l’affichage en mode Web. Si elle n’a pas réussi, nous allons essayer à nouveau. La bibliothèque fournit quant à lui l’erreur que vous pouvez voir dans la console ou gérer de façon asynchrone. 

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Permet de paramétrer les fabriques de Notification.

Nous créons la même méthode que nous l’avons fait dans le `AppDelegate` ci-dessus, mais cette fois-ci, nous allons ajouter quelques `NSNotification`s pour nous dire ce qui se passe dans notre service. Nous définissons un observateur qui nous l’indiquera lorsque quelque chose change avec le jeton. Une fois le jeton nous renvoyer l’utilisateur vers le `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Ajoutez le code qui gère l’utilisateur chaque fois qu’une demande est lancée pour signe natif

Nous allons créer une méthode qui sera appelée à chaque fois que nous avons une demande d’authentification. Il s’agit de la méthode qui crée un affichage en mode Web

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Enfin, appelons toutes ces méthodes, nous avons écrit ci-dessus chaque fois le `LoginViewController` charge. Pour ce faire, nous avons Ajout ces méthodes à notre `viewDidLoad` méthode Apple nous donne

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Vous avez maintenant terminé avec la création de la méthode principale, que nous allons interagir avec notre application pour se connecter. Une fois que nous avons connecté, nous devez utiliser nos jetons que nous avons reçu. Pour cela, nous allons créer du code d’application d’assistance qui appelle les API reste pour nous à l’aide de cette bibliothèque.


## <a name="create-a-graphapicaller-class-to-handle-our-requests-to-a-rest-api"></a>Créer un `GraphAPICaller` classe pour gérer les demandes à une API REST

Nous avons une configuration chargée chaque fois que nous charger de notre application. Maintenant, nous devons faire quelque chose avec elle, une fois que nous disposons d’un jeton. 

* Créer un `GraphAPICaller.h` fichier

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

À partir de ce code, vous voyez que nous allons créer deux méthodes : une pour obtenir les tâches à partir d’une API et une autre pour ajouter des tâches à l’API.

Maintenant que nous avons défini notre interface, ajoutons l’implémentation réelle :

* Créer un`GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## <a name="run-the-sample-app"></a>Exécutez l’exemple d’application

Enfin, générez et exécutez l’application dans Xcode. S’inscrire ou se connecter à l’application et créer des tâches pour un utilisateur connecté. Vous déconnecter et vous reconnecter en tant qu’autre utilisateur et créer des tâches pour cet utilisateur.

Notez que les tâches sont stockées par utilisateur sur le API, car l’API extrait l’identité de l’utilisateur à partir du jeton d’accès qu’il reçoit.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais déplacer sur des rubriques plus avancées B2C. Vous pouvez essayer :

[Appeler une API de web Node.js à partir d’une application web de Node.js]()

[Personnaliser l’expérience utilisateur pour une application B2C]()
