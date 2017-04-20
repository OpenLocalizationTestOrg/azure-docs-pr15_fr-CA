<properties
    pageTitle="Azure AD iOS route | Microsoft Azure"
    description="Comment générer une application iOS qui s’intègre avec Azure AD pour l’ouverture de session et appelle AD Azure protégé d’API utilisant OAuth."
    services="active-directory"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-ios-app"></a>Intégrer AD Azure dans un application d’iOS

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Annonce Azure fournit la bibliothèque de l’authentification Active Directory, ou ADAL, pour les clients d’e/s qui ont besoin d’accéder aux ressources protégées.  Seul but de ADAL dans la vie est de faciliter votre application obtenir les jetons d’accès.  Pour illustrer simplement combien il est facile, ici, nous créerons une application de liste de tâches objectif C qui :

-   Obtient accès jetons pour l’appel de l’API d’Azure AD graphique utilisant le [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Recherche dans un répertoire pour les utilisateurs disposant d’un alias donné.

Pour générer l’application complète de travail, vous devez :

2. Inscrire l’application dans Azure AD.
3. Installer et configurer les ADAL.
5. Utilisez ADAL pour obtenir des jetons à partir d’AD Azure.

Pour démarrer, [Téléchargez le squelette d’application](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  Vous aurez également besoin d’un locataire AD Azure dans lequel vous pouvez créer des utilisateurs et enregistrer une application.  Si vous ne disposez pas d’un client, à [savoir comment en obtenir un](active-directory-howto-tenant.md).

> [AZURE.TIP] Essayez de l’aperçu de notre nouveau [portail destiné aux développeurs](https://identity.microsoft.com/Docs/iOS) qui vous permettra de devenir opérationnel avec Azure Active Directory en quelques minutes !  Le portail des développeurs vous guidera tout au long du processus d’inscription d’une application et d’intégration AD Azure dans votre code.  Lorsque vous avez terminé, vous aurez une application simple qui peut authentifier les utilisateurs de vos clients et un back-end qui peuvent accepter des jetons et effectuer la validation. 

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>*1. Déterminez quel sera votre URI de redirection pour iOS*

Afin de pouvoir lancer en toute sécurité de vos applications dans certains scénarios SSO nous requièrent la création d’un **URI de redirection** dans un format particulier. Un URI de redirection est utilisé pour s’assurer que les jetons de retournent à l’application correcte qui a demandé les.

Le format de l’e/s pour un URI de redirection est :

```
<app-scheme>://<bundle-id>
```

-   **jeu d’aap** - cela est enregistré dans votre projet XCode. Il est comment les autres applications peuvent vous appeler. Vous pouvez trouver sous Info.plist -> types d’URL -> identificateur d’URL. Vous devez créer une si vous ne disposez pas d’une ou plusieurs configuré.
-   **id de lot** - Ceci correspond à l’identificateur d’offre groupée sous « identity » annuler les paramètres de votre projet dans XCode.

Voici un exemple de ce code de démarrage rapide : ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>*2. Enregistrez l’Application DirectorySearcher*
Pour permettre à votre application obtenir des jetons, vous devrez tout d’abord l’enregistrer dans votre client AD Azure et de lui accorder l’autorisation d’accéder à l’API Azure AD graphique :

-   Signe dans le portail de gestion Azure
-   Dans nav de la main gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lequel l’application.
-   Cliquez sur l’onglet **Applications** , cliquez sur **Ajouter** dans le bac d’alimentation inférieur.
-   Suivez les invites et créez une nouvelle **Application Client d’origine**.
    -   Le **nom** de l’application décrit votre application aux utilisateurs finals
    -   L' **Uri de redirection** est une combinaison de schéma et de chaîne AD Azure permet de renvoyer les réponses de jetons.  Permet d’entrer une valeur spécifique à votre application basée sur les informations ci-dessus.
-   Une fois l’enregistrement terminé, DAS affecter à votre application un identificateur de client unique.  Vous aurez besoin de cette valeur dans les sections suivantes, donc copier à partir de l’onglet **configurer** .
- Également dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ».  Pour l’application « Azure Active Directory », ajoutez l’autorisation de **l’annuaire de l’organisation de votre accès** sous **Autorisations d’accès délégué**.  Cela permettra à votre application à l’API de graphique pour les utilisateurs de la requête.

## <a name="3-install--configure-adal"></a>*3. installation et configuration ADAL*
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer ADAL et écrire votre code liées à l’identité.  Pour ADAL être en mesure de communiquer avec Active Directory Azure, vous devez lui fournir des informations sur l’inscription de l’application.
-   Commencez par ajouter ADAL au projet DirectorySearcher à l’aide de Cocapods.

```
$ vi Podfile
```
Ajoutez le code suivant pour cette podfile :

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

Maintenant charger la podfile à l’aide de cocoapods. Cette opération crée un nouvel espace de travail XCode vous sera chargé.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-   Dans le projet de démarrage rapide, ouvrez le fichier plist `settings.plist`.  Remplacez les valeurs des éléments dans la section selon les valeurs que vous entrez dans le portail Azure.  Votre code font référence à ces valeurs lorsqu’elle utilise ADAL.
    -   Le `tenant` est le domaine de vos clients AD Azure, par exemple : contoso.onmicrosoft.com
    -   Le `clientId` est l’identifiant du client de votre application, vous avez copié à partir du portail.
    -   Le `redirectUri` est la redirection url que vous avez enregistré dans le portail.

## <a name="4--use-adal-to-get-tokens-from-aad"></a>*4. Utilisation des ADAL pour obtenir les jetons de DAS*
Le principe de base derrière l’ADAL est que chaque fois que votre application a besoin d’un jeton d’accès, il appelle simplement une completionBlock `+(void) getToken : `, et ADAL s’occupe du reste.  

-   Dans le `QuickStart` ouvert le projet, `GraphAPICaller.m` et recherchez la `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` commentaire vers le haut.  Il s’agit d’où vous passez ADAL les coordonnées via un CompletionBlock pour communiquer avec Active Directory Azure et lui indiquer comment mettre en cache des jetons.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- Nous devons maintenant utiliser ce jeton pour rechercher des utilisateurs dans le graphique. Trouver la `// TODO: implement SearchUsersList` commentThis méthode fait une demande GET à l’API de Azure AD graphique pour la requête pour les utilisateurs dont UPN commence par le terme de recherche donné.  Mais pour l’API de graphique d’une requête, vous devez inclure un access_token dans les `Authorization` en-tête de la demande - c’est là qu’intervient ADAL.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {

             NSOperationQueue *queue = [[NSOperationQueue alloc]init];

             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                 if (error == nil && data != nil){

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

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
                         s.name =[keyValuePairs valueForKey:@"givenName"];

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
     }];

}

```
- Lorsque votre application demande un jeton en appelant `getToken(...)`, ADAL tente de retourner un jeton sans demander à l’utilisateur des informations d’identification.  Si ADAL détermine que l’utilisateur doit se connecter afin d’obtenir un jeton, s’afficher une boîte de dialogue d’ouverture de session, collecter les informations d’identification de l’utilisateur et retourne un jeton que si l’authentification aboutit.  Si ADAL ne peut pas retourner un jeton pour une raison quelconque, elle génère une `AdalException`.
- Notez que la `AuthenticationResult` objet contient un `tokenCacheStoreItem` objet qui peut être utilisé pour collecter des informations de votre application peut nécessiter.  Dans le démarrage rapide, `tokenCacheStoreItem` est utilisée pour déterminer si authenitcation s’est déjà produit.


## <a name="step-5-build-and-run-the-application"></a>Étape 5 : Générer et exécuter l’application



Félicitations ! Vous avez une application iOS de travail qui a la capacité d’authentifier les utilisateurs, d’appeler en toute sécurité les API Web à l’aide de OAuth 2.0 et obtenez des informations de base sur l’utilisateur.  Si vous ne l’avez pas déjà, il est temps de remplir vos clients avec certains utilisateurs.  Exécutez votre application QuickStart et vous connecter avec un de ces utilisateurs.  Rechercher d’autres utilisateurs en fonction de leur UPN.  Fermez l’application et exécutez de nouveau.  Notez comment la session de l’utilisateur reste intacte.

ADAL facilite l’intégration de toutes ces fonctionnalités d’identité commune dans votre application.  Il s’occupe de tout le travail pour vous - gestion du cache, la prise en charge de protocoles d’OAuth, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation de jetons a expiré et bien plus encore.  Tout ce que vous devez vraiment savoir est un seul appel d’API, `getToken`.

Pour référence, l’exemple complet (sans les valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="additional-scenarios"></a>Scénarios supplémentaires
Vous pouvez désormais déplacer d’autres scénarios.  Vous souhaiterez peut-être essayer :

- [Sécuriser un Web Node.JS API avec Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
- Découvrez [comment activer SSO entre-app sur iOS à l’aide de ADAL](active-directory-sso-ios.md)  

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
