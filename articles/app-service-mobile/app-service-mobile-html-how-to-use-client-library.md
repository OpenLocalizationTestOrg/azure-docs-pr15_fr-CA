<properties
    pageTitle="Comment utiliser le Kit de développement de JavaScript pour les applications mobiles Azure"
    description="Comment faire pour utiliser v pour les applications mobiles Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>L’utilisation de la bibliothèque Client JavaScript pour les applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide vous apprend à effectuer des scénarios courants utilisant le dernier [SDK JavaScript pour les applications mobiles Azure]. Si vous ne connaissez pas les applications Azure Mobile, d’abord effectuer le [Démarrage rapide d’Azure Mobile Apps] pour créer un serveur principal et de créer une table. Dans ce guide, nous concentrer sur l’utilisation du back-end mobile dans les applications Web HTML/JavaScript.

## <a name="supported-platforms"></a>Plates-formes prises en charge

Nous limiter la prise en charge du navigateur pour les versions en cours et le derniers des principaux navigateurs : Google Chrome, Microsoft Edge, Microsoft Internet Explorer et Mozilla Firefox.  Nous pensons que le Kit de développement logiciel pour fonctionner avec un navigateur relativement récent.

Le package est distribué comme un Module universel de JavaScript, afin qu’il prend en charge les globals, AMD, et met en forme CommonJS.

##<a name="Setup"></a>Le programme d’installation et conditions requises

Ce guide suppose que vous avez créé un back-end avec une table. Ce guide suppose que la table a le même schéma que les tables dans ces didacticiels.

Installer le kit SDK Azure Mobile applications JavaScript peut être effectuée la `npm` commande :

```
npm install azure-mobile-apps-client --save
```

Une fois installée, la bibliothèque se trouve dans `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`.  Copiez ce fichier vers votre espace web.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

La bibliothèque peut également être utilisée comme un module ES2015, au sein d’environnements de CommonJS des Browserify Webpack et sous la forme d’une bibliothèque d’AMD.  Par exemple :

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Comment : authentifier les utilisateurs

Service d’application Azure prend en charge l’authentification et l’autorisation des utilisateurs de l’application à l’aide de différents fournisseurs d’identité externes : Twitter, Microsoft Account, Facebook et Google. Vous pouvez définir des autorisations sur les tables pour restreindre l’accès à des opérations spécifiques aux utilisateurs authentifiés uniquement. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour mettre en œuvre des règles d’autorisation dans les scripts de serveur. Pour plus d’informations, consultez le didacticiel [mise en route de l’authentification] .

Deux flux d’authentification sont pris en charge : un flux du serveur et un flux de client.  Le flux de serveur fournit l’expérience d’authentification la plus simple, car il repose sur l’interface du fournisseur web d’authentification. Le flux client permet une intégration plus poussée avec des fonctionnalités spécifiques au périphérique tels que single-sign-on car il repose sur les kits de développement logiciel spécifique au fournisseur.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Comment : configurer votre Service d’application Mobile pour les URL de redirection externe.

Plusieurs types d’applications de JavaScript permet de gérer les flux OAuth UI une capacité de bouclage.  Ces fonctionnalités sont les suivantes :

* Votre service s’exécute
* À l’aide de rechargement direct avec le Framework IONIQUE
* Redirection vers le Service d’application pour l’authentification. 

Exécution locale peut entraîner des problèmes car, par défaut, l’authentification du Service de l’application est uniquement configurée pour autoriser l’accès depuis votre back-end de l’application Mobile. Utilisez les étapes suivantes pour modifier les paramètres de Service de l’application pour activer l’authentification lorsque le serveur s’exécute :

1. Connectez-vous au [portail Azure]
2. Naviguez jusqu'à votre back-end de l’application Mobile.
3. Dans le menu **Outils de développement** , sélectionnez **Explorateur de ressources** .
4. Cliquez sur **Aller** pour ouvrir l’Explorateur de ressources pour la principale application Mobile dans une fenêtre ou un nouvel onglet.
5. Développez la **configuration** > nœud**authsettings** pour votre application.
6. Cliquez sur le bouton **Modifier** pour activer la modification de la ressource.
7. Recherchez l’élément **allowedExternalRedirectUrls** , qui doit être null. Ajoutez votre URL dans un tableau :

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Remplacer les URL dans le tableau avec l’URL de votre service, ce qui, dans cet exemple, est `http://localhost:3000` pour le service d’exemple Node.js local. Vous pouvez également utiliser `http://localhost:4400` pour le service de propagation ou de certains autre URL, selon la configuration de votre application.

8. En haut de la page, cliquez sur **Lecture**, puis cliquez sur **Insérer** pour enregistrer vos mises à jour.

Vous devez également ajouter les mêmes URL loopback pour les paramètres de liste d’autorisation CORS :

1. Naviguer en arrière vers le [portail Azure].
2. Naviguez jusqu'à votre back-end de l’application Mobile.
3. Cliquez sur **CORS** dans le menu de **l’API** .
4. Saisissez chaque URL dans la zone de texte vide **Autorisée des origines** .  Une nouvelle zone de texte est créée.
5. Cliquez sur **Enregistrer**
    
Une fois le serveur principal met à jour, vous serez en mesure d’utiliser les nouvelles URL loopback dans votre application.

<!-- URLs. -->
[Guide de démarrage rapide de Apps Mobile Azure]: app-service-mobile-cordova-get-started.md
[Mise en route de l’authentification]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure portal]: https://portal.azure.com/
[Kit de développement de JavaScript pour les applications mobiles Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx

