<properties
    pageTitle="Azure AD NodeJS mise en route | Microsoft Azure"
    description="La création d’une API Web reste Node.js qui s’intègre avec Azure AD pour l’authentification."
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

# <a name="getting-started-with-web-api-for-node"></a>Mise en route avec les API de WEB de nœud

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

**Passport** est un middleware d’authentification pour Node.js. Extrêmement flexible et modulaire, Passport peut être discrètement lâché dans sur tout Express ou de l’application web de Resitify. Un ensemble complet de stratégies prennent en charge l’authentification à l’aide d’un nom d’utilisateur et mot de passe, Facebook, Twitter et bien plus encore. Nous avons développé une stratégie pour Active Directory de Microsoft Azure. Nous installer ce module et ensuite ajouter Microsoft Azure Active Directory `passport-azure-ad` plug-in.

Pour ce faire, vous devez :

1. Inscrire une application avec Azure AD
2. Configurez votre application pour utiliser azure-ad-passport de Passport plug-in.
3. Configurer une application cliente pour appeler l’à faire liste Web API

Le code de ce didacticiel est maintenue [sur GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [AZURE.NOTE] Cet article ne couvre pas la mise en œuvre d’ouverture de session, d’inscription et gestion des profils avec Azure AD B2C.  Il se concentre sur web appel API une fois que l’utilisateur est déjà authentifié.  Si vous ne l’avez pas déjà, vous devez commencer par [l’intégration à Active Directory de Azure document](./develop/active-directory-how-to-integrate.md) pour en savoir plus sur les bases d’Azure Active Directory.


Nous avez publié tout le code source pour cet exemple en cours d’exécution dans GitHub sous une licence MIT, donc n’hésitez pas à clone (ou mieux encore, fourche) et fournir des commentaires et tirez demandes.

## <a name="about-nodejs-modules"></a>À propos des Modules de Node.js

Dans cette procédure pas à pas, nous allons utiliser les modules Node.js. Les modules sont chargeables JavaScript qui fournissent des fonctionnalités spécifiques de votre application. Généralement, vous installez les modules à l’aide de l’outil de ligne de commande Node.js NPM dans le répertoire d’installation NPM, mais que certains modules, tels le HTTP, sont inclus le package Node.js de base.
Modules installés sont enregistrés dans le répertoire node_modules à la racine de votre répertoire d’installation Node.js. Chaque module dans le répertoire node_modules gère son propre répertoire node_modules qui contient tous les modules dont elle dépend, et chaque module requis a un répertoire node_modules. Cette structure de répertoire récursive représente la chaîne de dépendance.

Cette structure de chaîne de dépendance se traduit par un plus grand impact des applications, mais elle garantit que toutes les dépendances sont remplies et que la version des modules utilisés dans le développement sera également utilisée en production. Cela rend le comportement d’applications de production plus prévisible et évite les problèmes de versioning pouvant affecter les utilisateurs.

## <a name="1-register-a-azure-ad-tenant"></a>1. enregistrer un client AD Azure

Pour utiliser cet exemple, vous devrez un locataire de répertoire actif Azure. Si vous ne savez pas quel un locataire soit ou comment vous en obtiendrez un, voir [comment obtenir un Azure AD client](active-directory-howto-tenant.md).

## <a name="2-create-an-application"></a>2. création d’une application

Vous devez maintenant créer une application dans le répertoire, ce qui donne une publicité Azure certaines informations dont il a besoin pour communiquer en toute sécurité avec votre application.  L’application client et web API seront représentés par un seul **ID d’Application** dans ce cas, dans la mesure où ils constituent une application logique.  Pour créer une application, suivez [les instructions](active-directory-how-applications-are-added.md). Si vous créez une application de métier [ces instructions supplémentaires peuvent s’avérer utiles](active-directory-applications-guiding-developers-for-lob-applications.md).

Veillez à :

- Signe dans le portail de gestion Azure.
- Dans nav de la main gauche, cliquez sur **Active Directory**.
- Sélectionnez le client où vous souhaitez enregistrer l’application.
- Cliquez sur l’onglet **Applications** , puis cliquez sur Ajouter dans le bac d’alimentation inférieur.
- Suivez les invites et créer une nouvelle **Application Web et/ou WebAPI**.
    - Le **nom** de l’application décrit votre application aux utilisateurs finals
    - L' **Ouverture de session d’URL** est l’URL de base de votre application.  Valeur par défaut de l’exemple de code est `https://localhost:8080`.
    - L' **URI d’application ID** est un identificateur unique pour votre application.  La convention consiste à utiliser `https://<tenant-domain>/<app-name>`, par exemple :`https://contoso.onmicrosoft.com/my-first-aad-app`
- Une fois l’enregistrement terminé, DAS affecter à votre application un identificateur de client unique.  Vous aurez besoin de cette valeur dans les sections suivantes, donc copier à partir de l’onglet Configurer.

- RAPPEL : créer une **Application Secret** pour votre application et le copier vers le bas.  Vous en aurez besoin sous peu.
- RAPPEL : Copie l' **ID de l’Application** qui est assigné à votre application.  Vous devez également il peu de temps.


## <a name="3-download-nodejs-for-your-platform"></a>3. Téléchargez node.js pour votre plate-forme.
Pour utiliser cet exemple, vous devez disposer d’une installation active de Node.js.

Installer Node.js à partir de [http://nodejs.org](http://nodejs.org).

## <a name="4-install-mongodb-on-to-your-platform"></a>4. installation de MongoDB à votre plate-forme.

Pour utiliser cet exemple, vous devez disposer d’une installation active de MongoDB. MongoDB nous permet de rendre notre permanente de l’API REST entre les instances de serveur.

Installer MongoDB à partir de [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Cette procédure pas à pas suppose que vous utilisez des extrémités d’installation et de serveur par défaut de MongoDB, c'est-à-dire au moment de la rédaction de ce document : mongodb://localhost


## <a name="5-install-the-restify-modules-in-to-your-web-api"></a>5. installer les modules Restify à votre API Web

Nous allons utiliser Resitfy pour générer notre API REST. Restify est une infrastructure d’application minimale et flexible Node.js dérivée de Express qui a un jeu robuste de fonctions pour la création d’API reste sur se connecter.

### <a name="install-restify"></a>Restify d’installation

À partir de la ligne de commande, accédez au répertoire azuread. Si le répertoire **azuread** n’existe pas, créez-la.

`cd azuread - or- mkdir azuread; cd azuread`

Tapez la commande suivante :

`npm install restify`

Cette commande installe Restify.

#### <a name="did-you-get-an-error"></a>SI VOUS AVEZ REÇU UNE ERREUR ?

Lorsque vous utilisez npm sur certains systèmes d’exploitation, vous pouvez recevoir une erreur d’erreur : EPERM, chmod « / usr/local/bin /... » et une demande d’essayer d’exécuter le compte en tant qu’administrateur. Si cela se produit, utilisez la commande sudo pour exécuter npm à un niveau de privilège supérieur.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>SI VOUS AVEZ REÇU UN MESSAGE D’ERREUR CONCERNANT DTRACE ?

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
    └── bunyan@0.22.0 (mv@0.0.5)


## <a name="6-install-passportjs-in-to-your-web-api"></a>6. installation Passport.js dans sur votre site Web API

[Passport](http://passportjs.org/) est un middleware d’authentification pour Node.js. Extrêmement flexible et modulaire, Passport peut être discrètement lâché dans sur tout Express ou de l’application web de Resitify. Un ensemble complet de stratégies prennent en charge l’authentification à l’aide d’un nom d’utilisateur et mot de passe, Facebook, Twitter et bien plus encore. Nous avons développé une stratégie pour Azure Active Directory. Nous l’installation de ce module et ensuite ajouter la plug-in de stratégie Azure Active Directory.

À partir de la ligne de commande, accédez au répertoire azuread.

Entrez la commande suivante pour installer passport.js

`npm install passport`

La sortie de la commande doit ressembler à ce qui suit :

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="7-add-passport-azure-ad-to-your-web-api"></a>7. Ajoutez Passport-Azure-publicité sur votre site Web API

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



## <a name="8-add-mongodb-modules-to-your-web-api"></a>8. Ajoutez MongoDB modules à votre API Web

Nous allons utiliser MongoDB comme notre magasin de données pour cette raison, nous devons installer à la fois le largement utilisée du plug-in pour gérer les modèles et les schémas appelée Mongoose, ainsi que le pilote de base de données de MongoDB, également appelé MongoDB.


* `npm install mongoose`

## <a name="9--install-additional-modules"></a>9. installation des modules complémentaires

Ensuite, nous allons installer les autres modules requis.


À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`


Entrez les commandes suivantes pour installer les modules suivants dans le répertoire node_modules :

* `npm install assert-plus`
* `npm install bunyan`
* `npm update`


## <a name="10-create-a-serverjs-with-your-dependencies"></a>10. créer un server.js avec vos dépendances

Le fichier server.js vous fournira la plupart de nos fonctionnalités pour notre serveur Web API. Nous ajouterons la plupart du code à ce fichier. À des fins de production vous souhaitez refactoriser la fonctionnalité dans des fichiers plus petits, tels que les routes séparées et contrôleurs. Pour cette démonstration, nous allons utiliser server.js pour cette fonctionnalité.

À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Créer un `server.js` de fichiers dans notre éditeur favori et ajoutez les informations suivantes :

```Javascript
    'use strict';

    /**
    * Module dependencies.
    */

    var fs = require('fs');
    var path = require('path');
    var util = require('util');
    var assert = require('assert-plus');
    var bunyan = require('bunyan');
    var getopt = require('posix-getopt');
    var mongoose = require('mongoose/');
    var restify = require('restify');
    var passport = require('passport');
  var BearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Enregistrez le fichier. Nous reviendrons sur il peu de temps.

## <a name="11-create-a-config-file-to-store-your-azure-ad-settings"></a>11 :. Créer un fichier de configuration pour stocker vos paramètres AD Azure

Ce fichier de code transmet les paramètres de configuration à partir de votre portail de répertoire actif Azure pour Passport.js. Vous avez créé ces valeurs de configuration lorsque vous avez ajouté l’API Web au portail dans la première partie de la procédure pas à pas. Nous allons expliquer les éléments à ajouter dans les valeurs de ces paramètres après avoir copié le code.


À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Créer un `config.js` de fichiers dans notre éditeur favori et ajoutez les informations suivantes :

```Javascript
 exports.creds = {
     mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
     clientID: 'your client ID',
     audience: 'your application URL',
    // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
  // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
     identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
     validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
     passReqToCallback: false,
     loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

 };


```
Enregistrez le fichier.

## <a name="12-add-configuration-to-your-serverjs-file"></a>12. Ajoutez la configuration à votre fichier server.js

Nous avons besoin de lire ces valeurs dans le fichier de configuration que vous venez de créer dans notre application. Pour ce faire, nous avons simplement ajouter le fichier .config sous la forme d’une ressource requise dans notre application et définissez les variables globales à celles contenues dans le document config.js

À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Ouvrir votre `server.js` de fichiers dans notre éditeur favori et ajoutez les informations suivantes :

```Javascript
var config = require('./config');
```
Ensuite, ajoutez une nouvelle section à `server.js` par le code suivant :

```Javascript
var options = {
    // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback,
    loggingLevel: config.creds.loggingLevel

};

// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;

// Our logger
var log = bunyan.createLogger({
    name: 'Azure Active Directory Bearer Sample',
         streams: [
        {
            stream: process.stderr,
            level: "error",
            name: "error"
        },
        {
            stream: process.stdout,
            level: "warn",
            name: "console"
        }, ]
});

  // if logging level specified, switch to it.
  if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
```

Enregistrez le fichier.



## <a name="13-add-the-mongodb-model-and-schema-information-using-moongoose"></a>13. ajouter le modèle de MongoDB et les informations de schéma à l’aide de Moongoose

Toute cette préparation va maintenant démarrer à rembourser que nous vent ensemble ces trois fichiers dans un service de l’API REST.

Pour cette procédure pas à pas nous utiliserons MongoDB pour stocker nos tâches comme indiqué à ***l’étape 4***.

Si vous vous rappelez le `config.js` fichier que nous avons créé à ***l’étape 11*** nous avons appelé notre base de données `tasklist` que c’est ce que nous avons placé à la fin de notre URL de connexion mogoose_auth_local. Vous n’avez pas besoin de créer cette base de données à l’avance de MongoDB, il crée cela pour nous à la première exécution de notre application de serveur (en supposant qu’il n’en existe pas déjà).

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
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

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

## <a name="14-add-our-routes-for-our-task-rest-api-server"></a>14. ajouter nos gammes pour notre serveur de l’API REST de tâche

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

### <a name="1-add-default-routes-to-our-server"></a>1. Ajout d’itinéraires par défaut à notre serveur

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
        req.log.warn('createTodo: missing task');
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

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initalize the database as stated in the README?");
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

### <a name="2-next-lets-add-some-error-handling-in-our-apis"></a>2. ensuite, nous allons ajouter une gestion des erreurs dans nos API :

```

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


## <a name="15-create-your-server"></a>15. Créez votre serveur !

Nous disposons de notre base de données définie, nous avons nos gammes en place et la dernière chose à faire est d’ajouter notre instance de serveur qui va gérer nos appels.

Restify (et Express) ont beaucoup de haut niveau de personnalisation que vous pouvez faire d’un serveur de l’API REST, mais à nouveau, nous allons utiliser le programme d’installation de base pour nos besoins.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
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
```

## <a name="16-adding-the-routes-to-the-server-without-authentication-for-now"></a>16. Ajout d’itinéraires vers le serveur (sans authentification pour le moment)

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

## <a name="17-before-we-add-oauth-support-lets-run-the-server"></a>17. avant d’ajouter la prise en charge OAuth, exécutons le serveur.

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
HTTP/1.1 200 OK
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

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

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


## <a name="18-add-authentication-to-our-rest-api-server"></a>18. ajouter une authentification à notre serveur d’API reste

Maintenant que nous avons une API reste en cours d’exécution (Félicitations, btw !), nous allons apprendre à son utilité par rapport à une publicité Azure.

À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

### <a name="1-use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>1 : utiliser le OIDCBearerStrategy qui est inclus avec ad-azure passport

Jusqu'à présent, nous avons élaboré un serveur reste TODO standard sans aucune autorisation. Il s’agit d’où nous commençons à assembler qui.

Tout d’abord, nous devons indiquer que nous voulons utiliser Passport. Placez ce droit après la configuration du serveur :

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Lors de l’écriture d’API vous devez toujours lier les données à un élément unique à partir du jeton qui ne peut pas usurper l’identité de l’utilisateur. Lorsque ce serveur stocke les éléments TODO, il les stocke en fonction de l’ID d’objet de l’utilisateur dans le jeton (appelé par l’intermédiaire de token.oid) qui nous placés dans le champ « propriétaire ». Cela garantit que seul cet utilisateur peut accéder à son TODOs et personne d’autre ne peut accéder à l’entrée de TODOs. Aucun exposition n’est dans l’API de « propriétaire » pour un utilisateur externe peut demander d’autres TODOs même s’ils sont authentifiés.

Ensuite, nous allons utiliser la stratégie de support fourni avec le passport-azure-ad. Il suffit de considérer le code pour le moment, je vais vous l’expliquer brièvement. Placez ce après ce que vous pated ci-dessus :

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


var bearerStrategy = new BearerStrategy(options,
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

passport.use(bearerStrategy);
```

Passport utilise un modèle similaire pour toutes les stratégies (Twitter, Facebook, etc.) qui respectent tous les writers de stratégie. En examinant la stratégie vous voir que nous transmettre un function() qui a un jeton et un terminé comme paramètres. La stratégie loyalement reviendra à nous une fois tout de travail. Une fois que c’est le cas, nous devrons à stocker de l’utilisateur et placent le jeton ne souhaitons demandera à nouveau.

> [AZURE.IMPORTANT]
Le code ci-dessus prend tout utilisateur qui se trouve à s’authentifier à notre serveur. Il s’agit d’enregistrement automatique. Dans les serveurs de production vous ne voudriez pas que permettre à toute personne sans avoir d’abord les passer par une procédure d’enregistrement que vous décidez. C’est généralement le modèle que vous voir dans les applications consommateur qui vous permettent de vous inscrire sur Facebook mais puis vous demander d’entrer des informations supplémentaires. Si ce n’était pas un programme de ligne de commande, nous pourrions avoir venez d’extraire l’e-mail à partir de l’objet de jeton qui est retourné et alors invité à entrer des informations supplémentaires. Dans la mesure où il s’agit d’un serveur de test il suffit de les ajouter à la base de données en mémoire.

### <a name="2-finally-protect-some-endpoints"></a>2. Enfin, protéger certains points de terminaison

Vous protégez des points de terminaison en spécifiant le `passport.authenticate()` appeler avec le protocole que vous souhaitez utiliser.

Nous allons modifier notre gamme dans notre code de serveur pour faire quelque chose de plus intéressant :

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="19-run-your-server-application-again-and-ensure-it-rejects-you"></a>19. votre application de serveur à nouveau et vous assurer qu’il refuse de vous

Nous allons utiliser `curl` à nouveau pour voir si nous avons désormais OAuth2 protection contre nos points de terminaison. Nous ferons cela avant ouverte un de nos kits de développement logiciel du client par rapport à ce point de terminaison. Les en-têtes renvoyés doivent être suffisant pour nous dire que nous sommes le chemin d’accès de droite.

Tout d’abord, assurez-vous que votre instance de monogoDB est en cours d’exécution :

  $sudo mongod

Ensuite, placez-vous dans le répertoire et démarrer le roulage...

  server.js de nœud $ $ cd azuread

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

Si vous êtes intéressé par les étapes suivantes dans votre parcours ADAL, Voici certains clients ADAL pris en charge, nous vous recommandons de continuer à travailler :

Simplement cloner sur votre ordinateur de développeur et configurer comme indiqué dans la procédure pas à pas.

[ADAL pour iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL pour Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
