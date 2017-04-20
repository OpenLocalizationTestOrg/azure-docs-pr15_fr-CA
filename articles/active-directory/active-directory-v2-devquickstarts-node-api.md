<properties
    pageTitle="Annonce Azure v2.0 NodeJS Web API | Microsoft Azure"
    description="La génération d’une API de Web NodeJS accepte les jetons de deux Account personnel de Microsoft et les comptes de travail ou l’école."
    services="active-directory"
    documentationCenter="nodejs"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="secure-a-web-api-using-nodejs"></a>Sécuriser une API Web à l’aide de node.js

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

Azure Active Directory du point de terminaison version 2.0, vous pouvez protéger une API Web à l’aide des jetons d’accès [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) , qui permet aux utilisateurs avec des comptes de Microsoft et travail de personnel ou de l’établissement des comptes pour accéder en toute sécurité à votre API Web.

**Passport** est un middleware d’authentification pour Node.js. Extrêmement flexible et modulaire, Passport peut être discrètement lâché dans sur tout Express ou de l’application web de Resitify. Un ensemble complet de stratégies prennent en charge l’authentification à l’aide d’un nom d’utilisateur et mot de passe, Facebook, Twitter et bien plus encore. Nous avons développé une stratégie pour Active Directory de Microsoft Azure. Nous installer ce module et ensuite ajouter Microsoft Azure Active Directory `passport-azure-ad` plug-in.

## <a name="download"></a>Télécharger
Le code de ce didacticiel est maintenue [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs).  Pour suivre l’exemple, vous pouvez [Télécharger le squelette de l’application sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) ou cloner le squelette :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

L’application terminée est fournie à la fin de ce didacticiel ainsi.


## <a name="1-register-an-app"></a>1. enregistrer une application
Créer une nouvelle application à [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Copie de l' **Id de l’Application** affectée à votre application, vous en aurez besoin plus rapidement.
- Ajouter la plate-forme **Mobile** pour votre application.
- Copier vers le bas de l' **URI de redirection** à partir du portail. Vous devez utiliser la valeur par défaut de `urn:ietf:wg:oauth:2.0:oob`.


## <a name="2-download-nodejs-for-your-platform"></a>2 : télécharger node.js pour votre plate-forme.
Pour utiliser cet exemple, vous devez disposer d’une installation active de Node.js.

Installer Node.js à partir de [http://nodejs.org](http://nodejs.org).

## <a name="3-install-mongodb-on-to-your-platform"></a>3 : MongoDB installer à votre plate-forme.

Pour utiliser cet exemple, vous devez disposer d’une installation active de MongoDB. MongoDB nous permet de rendre notre permanente de l’API REST entre les instances de serveur.

Installer MongoDB à partir de [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Cette procédure pas à pas suppose que vous utilisez des extrémités d’installation et de serveur par défaut de MongoDB, c'est-à-dire au moment de la rédaction de ce document : mongodb://localhost

## <a name="4-install-the-restify-modules-in-to-your-web-api"></a>4 : installez les modules Restify à votre API Web

Nous allons utiliser Resitfy pour générer notre API REST. Restify est une infrastructure d’application minimale et flexible Node.js dérivée de Express qui a un jeu robuste de fonctions pour la création d’API reste sur se connecter.

### <a name="install-restify"></a>Restify d’installation

À partir de la ligne de commande, accédez au répertoire azuread. Si le répertoire **azuread** n’existe pas, créez-la.

`cd azuread`- ou -`mkdir azuread;`

Tapez la commande suivante :

`npm install restify`

Cette commande installe Restify.

#### <a name="did-you-get-an-error"></a>Si vous avez reçu une erreur ?

Lorsque vous utilisez npm sur certains systèmes d’exploitation, vous pouvez recevoir une erreur d’erreur : EPERM, chmod « / usr/local/bin /... » et une demande d’essayer d’exécuter le compte en tant qu’administrateur. Si cela se produit, utilisez la commande sudo pour exécuter npm à un niveau de privilège supérieur.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>Si vous avez reçu un message d’erreur concernant DTrace ?

Vous pouvez voir quelque chose comme ceci lors de l’installation de Restify :

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


Restify fournit un mécanisme puissant pour effectuer le suivi des appels reste à l’aide de DTrace. Toutefois, de nombreux systèmes d’exploitation n’ont pas de DTrace disponible. Vous pouvez ignorer ces erreurs.


La sortie de cette commande doit ressembler à ce qui suit :


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
    └── bunyan@0.22.0(mv@0.0.5)


## <a name="5-install-passportjs-into-your-web-api"></a>5 : installer Passport.js dans votre site Web API

[Passport](http://passportjs.org/) est un middleware d’authentification pour Node.js. Extrêmement flexible et modulaire, Passport peut être discrètement lâché dans sur tout Express ou de l’application web de Resitify. Un ensemble complet de stratégies prennent en charge l’authentification à l’aide d’un nom d’utilisateur et mot de passe, Facebook, Twitter et bien plus encore. Nous avons développé une stratégie pour Azure Active Directory. Nous l’installation de ce module et ensuite ajouter la plug-in de stratégie Azure Active Directory.

À partir de la ligne de commande, accédez au répertoire azuread.

Entrez la commande suivante pour installer passport.js

`npm install passport`

La sortie de la commande doit ressembler à ce qui suit :

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6 : ajouter un Passport-Azure-annonce sur votre site Web API

Ensuite, nous allons ajouter la stratégie OAuth, à l’aide de passport-azuread, un ensemble de stratégies qui se connectent Azure Active Directory avec Passport. Nous allons utiliser cette stratégie pour les jetons de porteur dans cet exemple de l’API Rest.

> [AZURE.NOTE] Bien que OAuth2 fournit un cadre dans lequel n’importe quel type de jeton connu peut être émis, uniquement certains types de jetons ont acquise utilisation de poussée. Pour protéger les points de terminaison, qui a pour être porteur jetons. Jetons de support sont les plus émis largement le type de jeton dans OAuth2, et de nombreuses implémentations de supposent que les jetons de support sont le seul type de jeton émis.

À partir de la ligne de commande, changez de répertoire vers le répertoire azuread

Tapez la commande suivante pour installer le module de passport-azure-annonce de Passport.js :

`npm install passport-azure-ad`

La sortie de la commande doit ressembler à ce qui suit :

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

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7 : ajouter des modules de MongoDB à votre API Web

Nous allons utiliser MongoDB comme notre magasin de données pour cette raison, nous devons installer à la fois le largement utilisée du plug-in pour gérer les modèles et les schémas appelée Mongoose, ainsi que le pilote de base de données de MongoDB, également appelé MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## <a name="8-install-additional-modules"></a>8 : installation des modules complémentaires

Ensuite, nous allons installer les autres modules requis.


À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`


Entrez les commandes suivantes pour installer les modules suivants dans le répertoire node_modules :

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## <a name="9-create-a-serverjs-with-your-dependencies"></a>9 : créer un server.js avec vos dépendances

Le fichier server.js vous fournira la plupart de nos fonctionnalités pour notre serveur Web API. Nous ajouterons la plupart du code à ce fichier. À des fins de production vous souhaitez refactoriser la fonctionnalité dans des fichiers plus petits, tels que les routes séparées et contrôleurs. Pour cette démonstration, nous allons utiliser server.js pour cette fonctionnalité.

À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Créer un `server.js` de fichiers dans notre éditeur favori et ajoutez les informations suivantes :

```Javascript
'use strict';
/**
* Module dependencies.
*/
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
```

Enregistrez le fichier. Nous reviendrons sur il peu de temps.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10 : créer un fichier de configuration pour stocker vos paramètres AD Azure

Ce fichier de code transmet les paramètres de configuration à partir de votre portail de répertoire actif Azure pour Passport.js. Vous avez créé ces valeurs de configuration lorsque vous avez ajouté l’API Web au portail dans la première partie de la procédure pas à pas. Nous allons expliquer les éléments à ajouter dans les valeurs de ces paramètres après avoir copié le code.


À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Créer un `config.js` de fichiers dans notre éditeur favori et ajoutez les informations suivantes :

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
};

```



### <a name="required-values"></a>Valeurs requises

*IdentityMetadata*: il s’agit dans laquelle vos données de configuration pour le IdP ainsi que les clés valider les jetons JWT recherche passport-azure-ad. Vous ne souhaitez probablement pas modifier cette option si vous utilisez Azure Active Directory.

*public*: votre URI de redirection à partir du portail.

> [AZURE.NOTE]
Nous restaurer nos clés à des intervalles fréquents. Veuillez vous assurer que vous extrayez toujours à partir de l’URL « openid_keys » et que l’application peut accéder à internet.


## <a name="11-add-configuration-to-your-serverjs-file"></a>11 : ajouter la configuration à votre fichier de server.js

Nous avons besoin de lire ces valeurs dans le fichier de configuration que vous venez de créer dans notre application. Pour ce faire, nous avons simplement ajouter le fichier .config sous la forme d’une ressource requise dans notre application et définissez les variables globales à celles contenues dans le document config.js

À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Ouvrir votre `server.js` de fichiers dans notre éditeur favori et ajoutez les informations suivantes :

```Javascript
var config = require('./config');
```
Ensuite, ajoutez une nouvelle section à `server.js` par le code suivant :

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
identityMetadata: config.creds.identityMetadata,
issuer: config.creds.issuer,
audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="step-12-add-the-mongodb-model-and-schema-information-using-moongoose"></a>Étape 12 : Ajouter le modèle de MongoDB et les informations de schéma à l’aide de Moongoose

Toute cette préparation va maintenant démarrer à rembourser que nous vent ensemble ces trois fichiers dans un service de l’API REST.

Pour cette procédure pas à pas nous utiliserons MongoDB pour stocker nos tâches comme indiqué à ***l’étape 4***.

Si vous vous rappelez à partir du fichier config.js que nous avons créé à l’étape 11, nous avons appelé notre base de données de *liste des tâches*, comme c’est ce que nous avons placé à la fin de notre URL de connexion mogoose_auth_local. Vous n’avez pas besoin de créer cette base de données à l’avance de MongoDB, il crée cela pour nous à la première exécution de notre application de serveur (en supposant qu’il n’en existe pas déjà).

Maintenant que nous avons dit le serveur quelle base de données MongoDB nous souhaitons utiliser, nous devons écrire du code supplémentaire pour créer le modèle et le schéma pour les tâches de notre serveur.

#### <a name="discussion-of-the-model"></a>Discussion du modèle

Notre modèle de schéma est très simple, et que vous le développez si nécessaire.

NOM - nom de qui est affecté à la tâche. Une ***chaîne***

TÂCHES - la tâche elle-même. Une ***chaîne***

DATE - la date d’échéance de la tâche. Un ***DATETIME***

TERMINÉ - si la tâche est terminée ou non. ***BOOLEAN***

#### <a name="creating-the-schema-in-the-code"></a>Création du schéma dans le code


À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Ouvrir votre `server.js` de fichiers dans notre éditeur favori et ajoutez les informations suivantes sous l’entrée de configuration :

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Vous connecter au serveur de MongoDB et restituent un objet de schéma pour nous.

#### <a name="using-the-schema-create-our-model-in-the-code"></a>À l’aide du schéma, de créer notre modèle dans le code

Sous le code que vous avez écrit ci-dessus, ajoutez le code suivant :

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Comme vous pouvez le voir à partir du code, nous créer notre schéma et puis créer un objet de modèle que nous utiliserons pour stocker ses données dans tout le code lorsque nous définir nos ***gammes***.

## <a name="step-13-add-our-routes-for-our-task-rest-api-server"></a>Étape 13 : Ajout de nos gammes de notre serveur de l’API REST de tâche

Maintenant que nous disposons d’un modèle de base de données à utiliser, ajoutons les itinéraires que nous utiliserons pour notre serveur de l’API REST.

### <a name="about-routes-in-restify"></a>Sur les gammes de Restify

Fonctionnement des gammes Restify de la même façon qu'ils le font à l’aide de la pile Express. Vous définissez des itinéraires à l’aide de l’URI que vous comptez les applications client à appeler. En règle générale, vous définissez votre parcours dans un fichier distinct. Dans notre cas, nous pouvons fournir nos gammes dans le fichier server.js. Nous vous recommandons de que vous prendre leur propre fichier pour utilisation en production.

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


C’est le modèle au niveau de la base. Resitfy (et Express) fournissent beaucoup plus profond functionaltiy telles que la définition des types d’application et la réalisation de routage complexe entre les points de terminaison différents. Dans notre cas, nous allons conserver ces itinéraires très simplement.

#### <a name="add-default-routes-to-our-server"></a>Ajouter des itinéraires par défaut à notre serveur

Nous maintenant ajouter les itinéraires CRUD de base de la créer, récupérer, mettre à jour et supprimer.

À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Ouvrir votre `server.js` de fichiers dans notre éditeur favori et ajoutez les informations suivantes sous les entrées de base de données que vous avez faites ci-dessus :

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
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
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
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
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

### <a name="add-some-error-handling-for-the-routes"></a>Ajouter une gestion des erreurs pour les itinéraires

Il est judicieux d’ajouter une gestion des erreurs afin que nous puissions communiquer au client le problème que nous avons rencontré de façon il peut comprendre.

Ajoutez le code suivant sous le code que vous avez écrit ci-dessus :

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


## <a name="step-14-create-your-server"></a>Étape 14 : Créer votre serveur !

Nous disposons de notre base de données définie, nous avons nos gammes en place et la dernière chose à faire est d’ajouter notre instance de serveur qui va gérer nos appels.

Restify (et Express) ont beaucoup de haut niveau de personnalisation que vous pouvez faire d’un serveur de l’API REST, mais à nouveau, nous allons utiliser le programme d’installation de base pour nos besoins.

```Javascript
/**
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
}));
```
## <a name="15-adding-the-routes-without-authentication-for-now"></a>15 : ajouter les itinéraires (sans authentification pour le moment)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-before-we-add-oauth-support-lets-run-the-server"></a>16 : avant d’ajouter la prise en charge OAuth, nous allons exécuter le serveur.

Tester votre serveur avant d’ajouter l’authentification

La façon la plus simple pour cela est à l’aide de roulage dans une ligne de commande. Avant cela, nous avons besoin d’un utilitaire simple qui nous permet d’analyser la sortie au format JSON. Pour ce faire, installez l’outil de json comme utilisent tous les exemples ci-dessous.

`$npm install -g jsontool`

Cette opération installe l’outil JSON globalement. Maintenant que nous avons accompli-vous de jouer avec le serveur :

Tout d’abord, assurez-vous que votre instance de monogoDB est en cours d’exécution...

`$sudo mongod`

Ensuite, placez-vous dans le répertoire et démarrer le roulage...

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 2.0OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

Ensuite, nous pouvons ajouter une tâche de cette façon :

`$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

La réponse doit être :

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
Et nous pouvons de cette manière liste de tâches pour Brandon :

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Si tout cela fonctionne, nous sommes prêts à ajouter OAuth pour le serveur de l’API REST.

**Vous disposez d’un serveur de l’API REST avec MongoDB !**

## <a name="17-add-authentication-to-our-rest-api-server"></a>17 : ajouter une authentification à notre serveur d’API reste

Maintenant que nous avons une API reste en cours d’exécution (Félicitations, btw !), nous allons apprendre à son utilité par rapport à une publicité Azure.

À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

### <a name="1-use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>1 : utiliser l’oidcbearerstrategy qui est inclus avec ad-azure passport

Jusqu'à présent, nous avons élaboré un serveur reste TODO standard sans aucune autorisation. Il s’agit d’où nous commençons à assembler qui.

Tout d’abord, nous devons indiquer que nous voulons utiliser Passport. Placez ce droit après la configuration du serveur :

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Lors de l’écriture d’API vous devez toujours lier les données à un élément unique à partir du jeton qui ne peut pas usurper l’identité de l’utilisateur. Lorsque ce serveur stocke les éléments TODO, il les stocke en fonction de l’ID d’abonnement de l’utilisateur dans le jeton (appelé par l’intermédiaire de token.sub) qui nous placés dans le champ « propriétaire ». Cela garantit que seul cet utilisateur peut accéder à son TODOs et personne d’autre ne peut accéder à l’entrée de TODOs. Aucun exposition n’est dans l’API de « propriétaire » pour un utilisateur externe peut demander d’autres TODOs même s’ils sont authentifiés.

Ensuite, nous allons utiliser la stratégie ouverte porteur de connexion ID fourni avec passport-azure-ad. Il suffit de considérer le code pour le moment, je vais vous l’expliquer brièvement. Placez ce après ce que vous pated ci-dessus :

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
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
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport utilise un modèle similaire pour toutes les stratégies (Twitter, Facebook, etc.) qui respectent tous les writers de stratégie. En examinant la stratégie vous voir que nous transmettre un function() qui a un jeton et un terminé comme paramètres. La stratégie loyalement reviendra à nous une fois tout de travail. Une fois que c’est le cas, nous devrons à stocker de l’utilisateur et placent le jeton ne souhaitons demandera à nouveau.

> [AZURE.IMPORTANT]
Le code ci-dessus prend tout utilisateur qui se trouve à s’authentifier à notre serveur. Il s’agit d’enregistrement automatique. Dans les serveurs de production vous ne voudriez pas que permettre à toute personne sans avoir d’abord les passer par une procédure d’enregistrement que vous décidez. C’est généralement le modèle que vous voir dans les applications consommateur qui vous permettent de vous inscrire sur Facebook mais puis vous demander d’entrer des informations supplémentaires. Si ce n’était pas un programme de ligne de commande, nous pourrions avoir venez d’extraire l’e-mail à partir de l’objet de jeton qui est retourné et alors invité à entrer des informations supplémentaires. Dans la mesure où il s’agit d’un serveur de test il suffit de les ajouter à la base de données en mémoire.

### <a name="2-finally-protect-some-endpoints"></a>2. Enfin, protéger certains points de terminaison

Vous protégez des points de terminaison en spécifiant l’appel de passport.authenticate() avec le protocole que vous souhaitez utiliser.

Nous allons modifier notre gamme dans notre code de serveur pour faire quelque chose de plus intéressant :

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again-and-ensure-it-rejects-you"></a>18 : votre application server à nouveau et vous assurer qu’il refuse de vous

Nous allons utiliser `curl` à nouveau pour voir si nous avons désormais OAuth2 protection contre nos points de terminaison. Nous ferons cela avant ouverte un de nos kits de développement logiciel du client par rapport à ce point de terminaison. Les en-têtes renvoyés doivent être suffisant pour nous dire que nous sommes le chemin d’accès de droite.

Tout d’abord, assurez-vous que votre instance de monogoDB est en cours d’exécution...

    $sudo mongod

Ensuite, placez-vous dans le répertoire et démarrer le roulage...

    $ cd azuread
    $ node server.js

Essayez un billet de base :

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Un 401 est la réponse que vous cherchez ici, car cela indique que la couche de Passport tente de rediriger vers le point de terminaison autoriser, qui est exactement ce que vous voulez.


## <a name="congratulations-you-have-a-rest-api-service-using-oauth2"></a>Félicitations ! Vous avez un Service d’API reste à l’aide de OAuth2 !

Vous avez a autant que vous le pouvez avec ce serveur sans l’aide d’un client compatible OAuth2. Vous devez passer par une procédure pas à pas supplémentaires.

Si vous recherchez seulement des informations sur l’implémentation d’une API REST à l’aide de Restify et OAuth2, vous avez plus de suffisamment de code pour apprendre comment générer cet exemple et développez votre service.

## <a name="next-steps"></a>Étapes suivantes

Pour référence, l’exemple complet (sans les valeurs de configuration) [est fourni sous la forme d’un fichier zip ici](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip), ou vous pouvez le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Vous pouvez désormais déplacer sur des sujets plus avancés.  Vous souhaiterez peut-être essayer :

[Sécuriser une application web de Node.js en utilisant le point de terminaison v2.0 >>](active-directory-v2-devquickstarts-node-web.md)

Pour obtenir des ressources supplémentaires, consultez :
- [Le guide du développeur v2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow « azure-active directory » balise >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous encourageons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et de s’abonner à l’alerte de sécurité.
