<properties
    pageTitle="Azure AD B2C : Sécuriser une API web à l’aide de Node.js | Microsoft Azure"
    description="Comment créer un web Node.js API qui accepte des jetons à partir d’un fermier B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="08/30/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C : Sécuriser une API web à l’aide de Node.js

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Avec Azure Active Directory (AD Azure), B2C, vous pouvez sécuriser un site web API à l’aide des jetons d’accès OAuth 2.0. Ces jetons permettent à vos applications clientes qui utilisent Azure AD B2C pour s’authentifier auprès de l’API. Cet article vous montre comment créer une « liste de tâches » API qui permet aux utilisateurs d’ajouter et de liste des tâches. Le web API est sécurisé à l’aide d’Azure AD B2C et autorise uniquement les utilisateurs authentifiés à gérer leur liste de tâches.

> [AZURE.NOTE]  Cet exemple a été écrit pour être connecté par le biais de notre [iOS B2C, exemple d’application](active-directory-b2c-devquickstarts-ios.md). Effectuez la procédure en cours tout d’abord et puis suivre cet exemple.

**Passport** est un middleware d’authentification pour Node.js. Flexible et modulaire, Passport peut être installé de manière transparente dans n’importe quel rapides ou Restify l’application web. Un ensemble complet de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et mot de passe, Facebook, Twitter et bien plus encore. Nous avons développé une stratégie pour Azure Active Directory (AD Azure). Vous installez ce module et ajoutez la publicité Azure `passport-azure-ad` plug-in.

Pour faire de cet exemple, vous devez :

1. Inscrire une application avec AD Azure.
2. Configurer votre application pour utiliser de Passport `azure-ad-passport` plug-in.
3. Configurer une application cliente pour appeler l’API de web « liste de tâches ».


## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire, ou client.  Un répertoire est un conteneur pour tous les utilisateurs, applications, groupes et bien plus encore.  Si vous n’en avez pas déjà, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de continuer.

## <a name="create-an-application"></a>Création d’une application

Ensuite, vous devez créer une application dans votre répertoire B2C qui donne Azure AD certaines informations dont il a besoin pour communiquer en toute sécurité avec votre application. Dans ce cas, l’application client et web API sont représentés par un seul **ID de l’Application**, parce qu’elles comprennent une application logique. Pour créer une application, suivez [les instructions](active-directory-b2c-app-registration.md). Veillez à :

- Inclure un **web application/web api** dans l’application
- Entrez `http://localhost/TodoListService` sous la forme d’une **URL de la réponse**. Il s’agit de l’URL par défaut pour cet exemple de code.
- Créer un **secret de l’Application** pour votre application et copiez-le. Vous avez besoin de ces données ultérieurement. Notez que cette valeur doit être [l’objet d’un échappement XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) avant de l’utiliser.
- Copiez l' **ID de l’Application** qui est assigné à votre application. Vous avez besoin de ces données ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure B2C d’Active Directory, chaque expérience de l’utilisateur est défini par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient deux expériences d’identité : vous inscrire et vous connecter. Vous devez créer une stratégie de chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  Lorsque vous créez vos trois stratégies, veillez à :

- Cliquez sur le **nom d’affichage** et autres attributs d’abonnement dans votre stratégie d’inscription.
- Choisissez les demandes d’application de **nom complet** et **l’ID d’objet** dans chaque stratégie.  Vous pouvez choisir les autres déclarations.
- Copier le **nom** de chaque stratégie après que l’avoir créé. Il doit avoir le préfixe `b2c_1_`.  Vous avez besoin de ces noms de stratégies ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Après avoir créé vos stratégies de trois, vous êtes prêt à créer votre application.

Pour en savoir plus sur le fonctionnement des stratégies dans Azure AD B2C, démarrer avec le [.NET web app en route](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Télécharger le code

Le code de ce didacticiel [est conservée sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Pour générer l’exemple à mesure que vous progressez, vous pouvez [télécharger un squelette de projet sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). Vous pouvez également cloner le squelette :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

L’application terminée est également [disponible sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) ou sur le `complete` branche du même référentiel.

## <a name="download-nodejs-for-your-platform"></a>Télécharger Node.js pour votre plate-forme.

Pour utiliser cet exemple, vous avez besoin d’une installation active de Node.js. 

Installer Node.js à partir de [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Installer MongoDB pour votre plate-forme.

Pour utiliser cet exemple, vous avez besoin d’une installation active de MongoDB. MongoDB nous permet de rendre votre API REST persistantes entre les instances de serveur.

Installer MongoDB à partir de [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Cette procédure suppose que vous utilisez des extrémités d’installation et de serveur par défaut de MongoDB, qui, au moment de la rédaction de ce document est `mongodb://localhost`.

## <a name="install-the-restify-modules-in-your-web-api"></a>Installez les modules de Restify de votre site web API

Restify nous permet de construire votre API REST. Restify est une infrastructure d’application minimale et flexible Node.js dérivée d’Express. Il a un jeu robuste de fonctions pour la création d’API reste sur se connecter.

### <a name="install-restify"></a>Restify d’installation

À partir de la ligne de commande, modifiez à votre annuaire `azuread`. Si le `azuread` répertoire n’existe pas, créez-la.

`cd azuread`ou`mkdir azuread;`

Entrez la commande suivante :

`npm install restify`

Cette commande installe Restify.

#### <a name="did-you-get-an-error"></a>Si vous avez reçu une erreur ?

Dans certains systèmes d’exploitation, lorsque vous utilisez `npm`, vous pouvez recevoir l’erreur `Error: EPERM, chmod '/usr/local/bin/..'` et une demande que vous exécutez le compte en tant qu’administrateur. Si ce problème se produit, utilisez le `sudo` commande à exécuter `npm` à un niveau de privilège supérieur.

#### <a name="did-you-get-a-dtrace-error"></a>Si vous avez reçu une erreur DTrace ?

Vous pouvez voir quelque chose comme ce texte lors de l’installation de Restify :

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify fournit un mécanisme puissant pour le suivi d’autres appels à l’aide de DTrace. Toutefois, de nombreux systèmes d’exploitation n’ont pas de DTrace disponible. Vous pouvez ignorer ces erreurs.

La sortie de la commande doit ressembler à ce texte :

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Installer un compte Passport de votre site web API

À partir de la ligne de commande, modifiez à votre annuaire `azuread`, si ce n’est déjà fait.

Installer un compte Passport à l’aide de la commande suivante :

`npm install passport`

La sortie de la commande doit être semblable à ce texte :

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Ajouter un passport-azuread à votre site web API

Ensuite, ajoutez la stratégie OAuth à l’aide de `passport-azuread`, un ensemble de stratégies qui se connectent AD Azure avec Passport. Utilisez cette stratégie pour les jetons de support dans l’échantillon de l’API REST.

> [AZURE.NOTE] Bien que OAuth2 fournit un cadre dans lequel n’importe quel type de jeton connu peut être émis, seuls certains types de jetons ont acquis utilisation généralisée. Les jetons pour protéger les points de terminaison sont des jetons de porteur. Ces types de jetons sont le plus couramment émise dans OAuth2. De nombreuses implémentations de supposent que les jetons de support sont le seul type de jeton émis.

À partir de la ligne de commande, modifiez à votre annuaire `azuread`, si ce n’est déjà fait.

Installer le passeport `passport-azure-ad` module à l’aide de la commande suivante :

`npm install passport-azure-ad`

La sortie de la commande doit être semblable à ce texte :

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Ajouter des modules de MongoDB dans votre site web API

Cet exemple utilise MongoDB comme magasin de données. Pour que l’installation Mongoose, largement utilisée du plug-in de gestion de modèles et les schémas.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Installation des modules complémentaires

Ensuite, installez les modules requis restants.

À partir de la ligne de commande, modifiez à votre annuaire `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Installer les modules dans votre `node_modules` répertoire :

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`


## <a name="create-a-serverjs-file-with-your-dependencies"></a>Créer un fichier server.js avec vos dépendances

Le `server.js` fichier fournit la majeure partie de la fonctionnalité de votre serveur Web API. 

À partir de la ligne de commande, modifiez à votre annuaire `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Créer un `server.js` le fichier dans un éditeur. Ajoutez les informations suivantes :

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Enregistrez le fichier. Vous revenez ultérieurement.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Créer un fichier config.js pour stocker vos paramètres AD Azure

Ce fichier de code transmet les paramètres de configuration à partir de votre portail de publicité Azure à la `Passport.js` fichier. Vous avez créé ces valeurs de configuration lorsque vous avez ajouté le web API au portail dans la première partie de la procédure. Nous expliquer les éléments à ajouter dans les valeurs de ces paramètres après avoir copié le code.

À partir de la ligne de commande, modifiez à votre annuaire `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Créer un `config.js` le fichier dans un éditeur. Ajoutez les informations suivantes :

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>', 
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Valeurs requises

`clientID`: L’ID de client de votre application Web API.

`IdentityMetadata`: C’est là `passport-azure-ad` recherche vos données de configuration pour le fournisseur d’identité. Il recherche également les clés valider les jetons de web JSON. 

`audience`: L’identificateur de ressource uniforme (URI) à partir du portail qui identifie l’application appelante. 

`tenantName`: Votre nom de client (par exemple, **contoso.onmicrosoft.com**).

`policyName`: La stratégie que vous souhaitez valider les jetons entrants sur votre serveur. Cette stratégie doit être la même stratégie que vous utilisez dans l’application cliente pour la connexion.

> [AZURE.NOTE] Pour notre aperçu B2C, utiliser les mêmes stratégies sur le programme d’installation de client et le serveur. Si vous avez déjà effectué une étape par étape et créé ces stratégies, vous n’avez pas besoin de recommencer l’opération. Parce qu’à la fin de la procédure, vous devez ne configurer des stratégies pour les procédures détaillées du client sur le site.

## <a name="add-configuration-to-your-serverjs-file"></a>Ajouter la configuration à votre fichier de server.js

Pour lire les valeurs de la `config.js` vous avez créé, ajoutez le `.config` de fichiers sous la forme d’une ressource requise dans votre application et ensuite définir les variables globales à celles de la `config.js` document.

À partir de la ligne de commande, modifiez à votre annuaire `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Ouvrir le `server.js` le fichier dans un éditeur. Ajoutez les informations suivantes :

```Javascript
var config = require('./config');
```
Ajouter une nouvelle section à `server.js` qui inclut le code suivant :

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

Ensuite, nous allons ajouter des espaces réservés pour les utilisateurs que nous recevons de nos applications appelante.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Commençons et créer notre journal trop.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Ajouter les informations de modèle et schéma de MongoDB à l’aide de Mongoose

La préparation antérieure s’acquitte comme vous mettez ces trois fichiers dans un service de l’API REST.

Pour cette procédure, utilisez MongoDB pour stocker vos tâches, comme indiqué plus haut.

Dans le `config.js` fichier, que vous avez appelé votre **liste des tâches**de base de données. Ce nom a été également ce que vous mettez à la fin de la `mongoose_auth_local` URL de connexion. Vous n’avez pas besoin de créer cette base de données à l’avance de MongoDB. Il crée la base de données de la première exécution de votre application serveur.

Après que vous informer le serveur de base de données MongoDB à utiliser, vous devez écrire du code supplémentaire pour créer le modèle et le schéma pour vos tâches de serveur.

### <a name="expand-the-model"></a>Développez le modèle

Ce modèle de schéma est simple. Vous pouvez le développer si nécessaire.

`owner`: Qui est affectée à la tâche. Cet objet est une **chaîne**.  

`Text`: La tâche elle-même. Cet objet est une **chaîne**.

`date`: Date d’échéance de la tâche. Cet objet est **datetime**.

`completed`: Si la tâche est terminée. Cet objet est un **type Boolean**.

### <a name="create-the-schema-in-the-code"></a>Créez le schéma dans le code

À partir de la ligne de commande, modifiez à votre annuaire `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Ouvrir le `server.js` le fichier dans un éditeur. Ajoutez les informations suivantes sous l’entrée de configuration :

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Vous créez tout d’abord le schéma, et puis vous créez un objet de modèle que vous utilisez pour stocker vos données dans le code lorsque vous définissez vos **itinéraires**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Ajoutez des itinéraires pour votre serveur de tâche API REST

Maintenant que vous avez un modèle de base de données à utiliser, ajouter les itinéraires que vous utilisez pour votre serveur de l’API REST.

### <a name="about-routes-in-restify"></a>Sur les gammes de Restify

Fonctionnement des gammes Restify de la même façon qu’ils utilisent lorsqu’ils utilisent la pile Express. Vous définissez des itinéraires à l’aide de l’URI que vous comptez les applications client à appeler. 

Un modèle typique pour une gamme de Restify est :

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Restify et Express peuvent fournir des fonctionnalités beaucoup plus profonde, telles que la définition des types d’application et la réalisation de routage complexe entre les points de terminaison différents. Pour les besoins de ce didacticiel, nous simplifier ces itinéraires.

#### <a name="add-default-routes-to-your-server"></a>Ajouter des itinéraires par défaut sur votre serveur

Maintenant, vous ajoutez les itinéraires CRUD de base de **Création** et de la **liste** de nos API REST. Autres itinéraires sont accessibles dans le `complete` branche de l’échantillon.

À partir de la ligne de commande, modifiez à votre annuaire `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Ouvrir le `server.js` le fichier dans un éditeur. Ci-dessous les entrées de base de données que vous avez faites ci-dessus ajoutez les informations suivantes :

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>Ajouter la gestion des erreurs pour les itinéraires

Ajouter une gestion des erreurs afin que vous pouvez communiquer les problèmes que vous rencontrez au client de façon qu’il puisse comprendre.

Ajoutez le code suivant :

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>Créez votre serveur

Vous avez maintenant défini par votre base de données et mise en place de votre parcours. La dernière chose à faire est d’ajouter l’instance de serveur qui gère les appels.

Restify et Express fournir une personnalisation complète d’un serveur de l’API REST, mais nous utilisons la configuration la plus basique ici. 

```Javascript

**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Ajouter les itinéraires vers le serveur (sans authentification)

```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

``` 

## <a name="add-authentication-to-your-rest-api-server"></a>Ajouter l’authentification sur votre serveur de l’API REST

Maintenant que vous avez un serveur API REST en cours d’exécution, vous pouvez le rendre utile contre AD Azure.

À partir de la ligne de commande, modifiez à votre annuaire `azuread`, si ce n’est pas déjà fait :

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Utilisez le OIDCBearerStrategy qui est inclus avec ad-azure passport


> [AZURE.TIP]
Lorsque vous écrivez des API, vous devez toujours lier les données à un élément unique à partir du jeton qui ne peut pas usurper l’identité de l’utilisateur. Lorsque le serveur stocke les éléments ToDo, il se base sur l' **oid** de l’utilisateur dans le jeton (appelé par l’intermédiaire de token.oid), qui est inséré dans le champ « propriétaire ». Cette valeur permet de s’assurer que seul cet utilisateur peut accéder à leurs propres éléments ToDo. N’est aucune exposition dans l’API de « propriétaire », un utilisateur externe peut demander les éléments ToDo autres même s’ils sont authentifiés.

Ensuite, utilisez la stratégie de support fourni avec `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport utilise le même modèle pour tous ses stratégies. Vous lui passez un `function()` qui a `token` et `done` en tant que paramètres. La stratégie qui se présente à vous après que qu’il a effectué son travail. Vous devez stocker l’utilisateur, puis enregistrer le jeton de sorte que vous n’avez pas besoin de demander à nouveau.

> [AZURE.IMPORTANT]
Le code ci-dessus prend tout utilisateur qui se trouve à authentifier votre serveur. Ce processus est appelé autoregistration. Dans les serveurs de production, ne laissez pas de tout accès aux utilisateurs l’API sans avoir d’abord les passer par un processus d’enregistrement. Ce processus est généralement le modèle que vous voir dans les applications consommateur qui vous permettent d’enregistrer à l’aide de Facebook mais puis vous demander d’entrer des informations supplémentaires. Si ce programme n’est pas un programme de ligne de commande, nous avons extrait le courrier électronique à partir de l’objet de jeton qui est retourné et un message demande ensuite aux utilisateurs de remplir des informations supplémentaires. Dans la mesure où il s’agit d’un échantillon, nous les ajouter à une base de données en mémoire.



## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Exécutez votre application serveur pour vérifier qu’il rejette vous

Vous pouvez utiliser `curl` pour voir si vous avez OAuth2 protection contre vos points de terminaison. Les en-têtes renvoyés doivent être suffisant pour vous dire que vous êtes sur la bonne voie.

Assurez-vous que votre instance de MongoDB est en cours d’exécution :

    $sudo mongodb

Accédez au répertoire et exécuter le serveur :

    $ cd azuread
    $ node server.js

Dans une nouvelle fenêtre de terminal, exécutez`curl`

Essayez un billet de base :

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Une erreur 401 correspond à la réponse souhaitée. Il indique que la couche de Passport tente de rediriger vers le point de terminaison autoriser.


## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Vous disposez maintenant d’un service de l’API REST qui utilise OAuth2

Vous avez implémenté une API REST à l’aide de Restify et OAuth ! Vous avez maintenant code suffisante afin que vous puissiez continuer à développer votre service et générer cet exemple. Vous avez vérifié qu’autant que possible sans l’aide d’un client compatible OAuth2 avec ce serveur. Pour que l’étape suivante, utilisez une procédure supplémentaire à notre procédure pas à pas [se connecter à un site web API à l’aide d’e/s avec B2C](active-directory-b2c-devquickstarts-ios.md) .


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais déplacer vers des rubriques plus avancées, telles que :

[Se connecter à un site web API à l’aide d’e/s avec B2C](active-directory-b2c-devquickstarts-ios.md)