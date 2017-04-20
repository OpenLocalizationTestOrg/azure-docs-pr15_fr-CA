<properties
    pageTitle="AngularJS de v2.0 AD Azure mise en route | Microsoft Azure"
    description="Comment générer une application angulaire JS seule Page qui se connecte à des comptes d’utilisateurs avec les deux comptes Microsoft personnels et centre ou l’école."
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


# <a name="add-sign-in-to-an-angularjs-single-page-app---net"></a>Ajoutez connexion à une application de page unique AngularJS - .NET

Dans cet article, nous allons ajouter signe avec des comptes de Microsoft sous tension à une application de AngularJS en utilisant le point de terminaison v2.0 Azure Active Directory.  Le point de terminaison version 2.0 vous permet d’effectuer une intégration simple dans votre application et d’authentifier les utilisateurs avec des comptes personnels et de travail ou aux études.

Cet exemple est une application de page unique liste de tâches simple qui stocke des tâches dans un back-end API REST, écrit à l’aide de l’infrastructure .NET 4.5 MVC et sécurisés à l’aide des jetons de support OAuth à partir d’AD Azure.  L’application AngularJS utilise notre open source de bibliothèque JavaScript d’authentification [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) pour gérer la connexion toute processus et acquière des jetons pour l’appel de l’API REST.  Le même modèle peut être appliqué pour s’authentifier auprès d’autres API reste, comme le [Microsoft Graph](https://graph.microsoft.com).

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="download"></a>Télécharger

Pour commencer, vous devez télécharger et installer Visual Studio.  Puis vous pouvez cloner ou [Télécharger](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) une application squelette :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet.git
```

L’application squelette inclut tout le code réutilisable pour une application simple de la AngularJS, mais il manque toutes les pièces liées à l’identité.  Si vous ne souhaitez pas suivre l’exemple, vous pouvez en revanche cloner ou [Télécharger](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/complete.zip) l’exemple terminé.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet.git
```

## <a name="register-an-app"></a>Enregistrer une application

Tout d’abord, créez une application dans le [Portail de l’enregistrement de App](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Ajouter la plate-forme **Web** pour votre application.
- Entrez l' **URI de redirection**. La valeur par défaut de cet exemple est `https://localhost:44326/`.
- Laissez la case à cocher **Autoriser le flux implicite** activé. 

Copie de l' **ID de l’Application** qui est assigné à votre application, vous en aurez besoin sous peu. 

## <a name="install-adaljs"></a>Installation de adal.js
Pour démarrer, naviguez pour le projet que vous avez téléchargé et installez adal.js.  Si vous avez installé le [bower](http://bower.io/) , vous pouvez exécuter cette commande.  Pour les incompatibilités de version de dépendance, choisissez la version plus récente.
```
bower install adal-angular#experimental
```

Sinon, vous pouvez télécharger manuellement [l’adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) et [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js).  Ajouter les deux fichiers à la `app/lib/adal-angular-experimental/dist` de la `TodoSPA` projet.

Maintenant ouvrir le projet dans Visual Studio et adal.js à la fin du corps de la page principale de charger :

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Configurer l’API REST

Alors que nous allons configurer les choses, nous allons apprendre le fonctionnement d’API REST de back-end.  Dans la racine du projet, ouvrez `web.config` et remplacer la `audience` valeur.  L’API REST utilise cette valeur pour valider les jetons qu’il reçoit de l’application angulaire sur demandes AJAX.

```xml
<!--web.config-->

...

    <appSettings>
        <add key="ida:Audience" value="[Your-application-id]" />
    </appSettings>
    
...
```

C’est tout le temps que nous allons dépenser décrivant le fonctionnement de l’API REST.  N’hésitez pas à examiner le code, mais si vous souhaitez en savoir plus sur la sécurisation du web API avec AD Azure, consultez [cet article](active-directory-v2-devquickstarts-dotnet-api.md). 

## <a name="sign-users-in"></a>Authentification des utilisateurs dans
Temps d’écrire du code d’identité.  Vous avez peut-être déjà remarqué que adal.js contient un fournisseur de AngularJS, qui joue parfaitement avec les mécanismes de routage angulaires.  Commencez par ajouter le module adal à l’application :

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Vous pouvez maintenant initialiser le `adalProvider` avec votre ID d’Application :

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

Excellent, adal.js a maintenant toutes les informations dont il a besoin pour sécuriser votre application et à connecter les utilisateurs.  Pour forcer l’abonnement pour une gamme donnée dans l’application, il est d’une ligne de code :

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Lorsqu’un utilisateur clique sur le `TodoList` lien, adal.js redirigera automatiquement vers Azure AD pour la connexion si nécessaire.  Vous pouvez également explicitement envoyer des demandes de connexion et de déconnexion en appelant adal.js sur vos contrôleurs de :

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## <a name="display-user-info"></a>Afficher les informations utilisateur
Maintenant que l’utilisateur est connecté, vous devrez probablement accéder aux données de l’authentification de la signature de l’utilisateur dans votre application.  Adal.js expose ces informations pour vous dans le `userInfo` objet.  Pour accéder à cet objet dans une vue, vous devez tout d’abord ajouter adal.js à la portée de la racine du contrôleur correspondant :

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Puis vous pouvez adresser directement le `userInfo` objet de votre affichage : 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Vous pouvez également utiliser le `userInfo` objet pour déterminer si l’utilisateur est connecté ou non.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Appeler l’API REST
Enfin, il est temps pour obtenir quelques jetons et appeler l’API REST pour créer, lire, mettre à jour et supprimer des tâches.  Et devinez un peu ?  Vous n’avez *rien*à faire.  Adal.js se charge automatiquement de la mise en route, la mise en cache et l’actualisation des jetons.  Il se charge également de joindre ces jetons aux demandes sortantes AJAX que vous envoyez à l’API REST.  

Comment exactement ceci fonctionne-t-il ? Il est tout grâce à la magie d' [intercepteurs de AngularJS](https://docs.angularjs.org/api/ng/service/$http), qui permet de adal.js transformer les messages http entrants et sortants.  En outre, adal.js suppose que toutes les demandes envoient au même domaine que la fenêtre doit utiliser des jetons destinés à l’ID d’Application même en tant que l’application AngularJS.  C’est pourquoi nous avons utilisé le même ID d’Application dans les deux l’application angulaire et l’API REST de NodeJS.  Bien sûr, vous pouvez substituer ce comportement et demander adal.js pour obtenir des jetons pour les autres API reste si nécessaire -, mais pour ce scénario simple effectuera les valeurs par défaut.

Voici un extrait qui montre combien il est facile d’envoyer des demandes avec des jetons de porteur de publicité Azure :

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Félicitations !  Votre application d’intégré seule page Azure AD est maintenant terminée.  Allez-y, prendre une courbure de l’arc.  Il peut authentifier les utilisateurs en toute sécurité appeler son back-end API REST à l’aide de OpenID de se connecter et obtenir des informations de base sur l’utilisateur.  Prêts à l’emploi, il prend en charge tout utilisateur disposant d’un personnel Account de Microsoft ou un travail ou aux études depuis Active Directory Azure.  Exécutez l’application et dans un navigateur, accédez à `https://localhost:44326/`.  Ouvrez une session en utilisant un compte personnel de Microsoft ou un compte de travail ou aux études.  Ajouter des tâches à la liste des actions de l’utilisateur et vous déconnecter.  Essayez de vous connecter avec l’autre type de compte. Si vous avez besoin d’un locataire AD Azure pour créer des utilisateurs de travail ou aux études, [savoir comment en obtenir un ici](active-directory-howto-tenant.md) (elle est gratuite).

Pour en savoir sur le point de terminaison v2.0, tête de nouveau notre [guide de développeur de version 2.0](active-directory-appmodel-v2-overview.md).  Pour obtenir des ressources supplémentaires, consultez :

- [Azure-exemples sur GitHub >>](https://github.com/Azure-Samples)
- [Azure AD de dépassement de capacité de pile >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Azure documentation AD [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous encourageons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et de s’abonner à l’alerte de sécurité.
