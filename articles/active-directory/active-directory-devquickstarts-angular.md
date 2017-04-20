<properties
    pageTitle="Azure AD AngularJS mise en route | Microsoft Azure"
    description="Comment générer une application angulaire JS seule Page qui intègre Azure AD pour l’ouverture de session et appelle AD Azure protégé d’API utilisant OAuth."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="securing-angularjs-single-page-apps-with-azure-ad"></a>Sécurisation des applications de Page AngularJS simple avec AD Azure

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Annonce Azure facilite la simple et directe pour ajouter connexion, déconnexion et de sécurisation des appels d’API de OAuth à vos applications de page unique.  Il permet à votre application authentifier les utilisateurs avec les comptes Active Directory et consommer n’importe quel web API protégé par AD Azure, telles que les API d’Office 365 ou l’API d’Azure.

Pour les applications de javascript s’exécutant dans un navigateur, AD Azure fournit la bibliothèque de l’authentification Active Directory, ou adal.js.  Seul but du Adal.js dans la vie est de faciliter votre application obtenir les jetons d’accès.  Pour illustrer simplement combien il est facile, ici que nous créerons une application AngularJS liste des tâches qui :

- Connecte l’utilisateur de l’application à l’aide d’Active Directory Azure comme le fournisseur d’identité.
- Affiche des informations sur l’utilisateur.
- En toute sécurité appelle à faire liste API l’application utilisant des jetons de porteur de DAS.
- Signe de l’utilisateur de l’application.

Pour générer l’application complète de travail, vous devez :

2. Inscrire l’application dans Azure AD.
3. Installation d’un ADAL et configurer le SPA.
5. ADAL permet de sécuriser les pages dans le SPA.

Pour démarrer, [Téléchargez le squelette d’application](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Vous aurez également besoin d’un locataire AD Azure dans lequel vous pouvez créer des utilisateurs et enregistrer une application.  Si vous ne disposez pas d’un client, à [savoir comment en obtenir un](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. enregistrer l’Application DirectorySearcher*
Pour permettre à votre application authentifier les utilisateurs et d’obtenir des jetons, vous devrez tout d’abord l’enregistrer dans votre client AD Azure :

-   Connexion au [portail de gestion Azure](https://manage.windowsazure.com)
-   Dans nav de la main gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lequel l’application.
-   Cliquez sur l’onglet **Applications** , cliquez sur **Ajouter** dans le bac d’alimentation inférieur.
-   Suivez les invites et créer une nouvelle **Application Web et/ou WebAPI**.
    -   Le **nom** de l’application décrit votre application aux utilisateurs finals.
    -   L' **Uri de redirection** est l’emplacement auquel DAS retournera les jetons.  L’emplacement par défaut de cet exemple est`https://localhost:44326/`
-   Une fois l’enregistrement terminé, DAS affecter votre application unique **ID Client**.  Vous aurez besoin de cette valeur dans les sections suivantes, donc copier à partir de l’onglet **configurer** .
- Adal.js utilise le flux implicite OAuth pour communiquer avec Active Directory Azure.  Vous devez activer le flux implicite pour votre application par :
    - Télécharger le manifeste de l’application en cliquant sur **Gérer le manifeste**.
    - Ouvrez le manifeste et recherchez le `oauth2AllowImplicitFlow` propriété. Définissez sa valeur sur `true`.
    - Enregistrer et télécharger le manifeste de l’application en cliquant sur **Gérer manifeste** une fois de plus.

## <a name="2-install-adal--configure-the-spa"></a>*2. Installez ADAL & configurer le SPA*
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer adal.js et écrire votre code liées à l’identité.

-   Commencez par ajouter des adal.js au projet TodoSPA à l’aide de la Console du Gestionnaire de package :
  - Téléchargez le [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) et l’ajouter à la `App/Scripts/` répertoire du projet.
  - Téléchargez [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) et l’ajouter à la `App/Scripts/` répertoire du projet.
  - Charger chaque script avant la fin de la `</body>` de `index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-   Pour le back-end du SPA à faire liste API d’accepter des jetons à partir du navigateur, le serveur principal doit les informations de configuration sur l’enregistrement de l’application. Dans le projet TodoSPA, ouvrez `web.config`.  Remplacez les valeurs des éléments dans les `<appSettings>` section selon les valeurs que vous entrez dans le portail Azure.  Votre code font référence à ces valeurs lorsqu’elle utilise ADAL.
    -   Le `ida:Tenant` est le domaine de vos clients AD Azure, par exemple : contoso.onmicrosoft.com
    -   Le `ida:Audience` doit être l' **ID Client** de votre application, vous avez copié à partir du portail.

## <a name="3--use-adal-to-secure-pages-in-the-spa"></a>*3. Utilisez ADAL afin de sécuriser les pages dans le SPA*
Adal.js a été conçu pour être intégré aux fournisseurs de gammes et http AngularJS, ce qui vous permet de sécuriser des vues individuelles dans votre SPA.

- Dans `App/Scripts/app.js`, mettre dans le module adal.js :

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- Vous pouvez maintenant initialiser le `adalProvider` avec les valeurs de configuration de l’enregistrement de l’application, également dans `App/Scripts/app.js`:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- Pour sécuriser la `TodoList` afficher dans l’application, uniquement une seule ligne de code est nécessaire - `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

Vous disposez maintenant d’une application de page unique sécurisée avec la possibilité de connecter les utilisateurs et d’émettre des demandes protégés jetons de support à son API de back-end.  Lorsqu’un utilisateur clique sur le `TodoList` lien, adal.js redirigera automatiquement vers Azure AD pour ouvrir une session si nécessaire.  En outre, adal.js s’attache automatiquement un access_token pour toutes les requêtes ajax qui sont envoyés au serveur principal de l’application.  Ce qui précède est le minimum vital requis pour construire un SPA avec adal.js -, mais il existe un certain nombre d’autres fonctionnalités qui sont utiles dans les établissements thermaux :

- Explicitement problème de connexion et déconnexion des requêtes, vous pouvez définir des fonctions dans vos contrôleurs qui appellent adal.js.  In `App/Scripts/homeCtrl.js`:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- Vous pourriez également présente des informations utilisateur dans l’interface utilisateur de l’application.  Le service adal a déjà été ajouté à la `userDataCtrl` contrôleur, pour pouvoir accéder à la `userInfo` l’objet dans la vue associée, `App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- Il existe également un grand nombre de scénarios dans lesquels vous souhaiterez savoir si l’utilisateur est connecté ou non.  Vous pouvez également utiliser le `userInfo` objet de rassembler ces informations.  Par exemple, dans `index.html` vous pouvez afficher le bouton « Déconnexion » ou « Login » en fonction de l’état d’authentification :

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Félicitations ! Votre annonce Azure intégré App de Page unique est à présent terminée.  Il peut authentifier les utilisateurs, en toute sécurité appeler son serveur principal à l’aide de OAuth 2.0 et obtenir des informations de base sur l’utilisateur.  Si vous ne l’avez pas déjà, il est temps de remplir vos clients avec certains utilisateurs.  Exécutez votre à faire de liste SPA et connectez-vous à l’aide d’un de ces utilisateurs.  Ajouter des tâches pour les utilisateurs à répertorier, vous déconnecter et vous reconnecter.

Adal.js facilite l’intégration de toutes ces fonctionnalités d’identité commune dans votre application.  Il s’occupe de tout le travail pour vous - gestion du cache, la prise en charge de protocoles d’OAuth, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation de jetons a expiré et bien plus encore.

Pour référence, l’exemple complet (sans les valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Vous pouvez désormais déplacer d’autres scénarios.  Vous souhaiterez peut-être essayer :

[Appeler une API de Web CORS de SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
