<properties
    pageTitle="Mise en route avec AD Azure vous connecter et vous déconnecter à l’aide de node.js"
    description="Comment générer un node.js Express Web application MVC qui s’intègre avec Azure AD pour l’ouverture de session."
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
    ms.date="08/15/2016"
    ms.author="brandwe"/>

# <a name="nodejs-web-app-sign-in--sign-out-with-azure-ad"></a>NodeJS Web App de connexion & signer avec Azure AD


Ici, nous allons utiliser Passport pour :

- Connecter l’utilisateur à l’application à l’aide d’Active Directory Azure.
- Afficher des informations sur l’utilisateur.
- Connexion de l’utilisateur de l’application.

**Passport** est un middleware d’authentification pour Node.js. Extrêmement flexible et modulaire, Passport peut être discrètement lâché dans sur tout Express ou de l’application web de Resitify. Un ensemble complet de stratégies prennent en charge l’authentification à l’aide d’un nom d’utilisateur et mot de passe, Facebook, Twitter et bien plus encore. Nous avons développé une stratégie pour Active Directory de Microsoft Azure. Nous installer ce module et ensuite ajouter Microsoft Azure Active Directory `passport-azure-ad` plug-in.

Pour ce faire, vous devez :

1. Enregistrer une application.
2. Configurez votre application pour utiliser la stratégie de Passport-azure-ad.
3. Passport permet d’émettre des demandes de connexion et de déconnexion à AD Azure.
4. Imprimer les données relatives à l’utilisateur.

Le code de ce didacticiel est maintenue [sur GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Pour suivre l’exemple, vous pouvez [Télécharger le squelette de l’application sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) ou cloner le squelette :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

L’application terminée est fournie à la fin de ce didacticiel ainsi.

## <a name="1-register-an-app"></a>1. enregistrer une application
- Signe dans le portail de gestion Azure.
- Dans nav de la main gauche, cliquez sur **Active Directory**.
- Sélectionnez le client où vous souhaitez enregistrer l’application.
- Cliquez sur l’onglet **Applications** , puis cliquez sur Ajouter dans le bac d’alimentation inférieur.
- Suivez les invites et créer une nouvelle **Application Web et/ou WebAPI**.
    - Le **nom** de l’application décrit votre application aux utilisateurs finals
    -   L' **Ouverture de session d’URL** est l’URL de base de votre application.  Valeur par défaut du squelette est ' http://localhost:3000/authentification/openid/retour ''.
    - L' **URI d’application ID** est un identificateur unique pour votre application.  La convention consiste à utiliser `https://<tenant-domain>/<app-name>`, par exemple :`https://contoso.onmicrosoft.com/my-first-aad-app`
- Une fois l’enregistrement terminé, DAS affecter à votre application un identificateur de client unique.  Vous aurez besoin de cette valeur dans les sections suivantes, donc copier à partir de l’onglet Configurer.

## <a name="2-add-pre-requisities-to-your-directory"></a>2. Ajoutez pré-requisities à votre répertoire

À partir de la ligne de commande, accédez à votre dossier racine si ce n’est pas déjà il et exécutez les commandes suivantes :

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- De plus, vous aurez besoin notre `passport-azure-ad` ainsi :

- `npm install passport-azure-ad`

Cette opération installe les bibliothèques dépendent de ce passport-azure-ad.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. configurer votre application pour utiliser la stratégie de passport-nœud-js
Ici, nous allons configurer le middleware Express pour utiliser le protocole d’authentification OpenID de se connecter.  Passport servira à émettre des demandes de connexion et de déconnexion, la gestion de session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

-   Pour commencer, ouvrez le `config.js` de fichiers dans la racine du projet, puis entrez les valeurs de configuration de votre application dans le `exports.creds` section.
    -   Le `clientID:` est l' **Id de l’Application** affectée à votre application dans le portail de l’enregistrement.
    -   Le `returnURL` est l' **Uri de redirection** saisis dans le portail.
    - Le `clientSecret` est le mot de passe que vous avez généré dans le portail

- Ouvrez ensuite `app.js` à la racine du projet et ajoutez l’appel ci-dessous pour appeler la `OIDCStrategy` stratégie qui est fourni avec`passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// add a logger

var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- Ensuite, utilisez la stratégie nous référencé pour gérer les demandes de connexion

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    if (!profile.email) {
      return done(new Error("No email found"), null);
    }
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport utilise un modèle similaire pour toutes les stratégies (Twitter, Facebook, etc.) qui respectent tous les writers de stratégie. En examinant la stratégie vous voir que nous transmettre un function() qui a un jeton et un terminé comme paramètres. La stratégie loyalement reviendra à nous une fois tout de travail. Une fois que c’est le cas, nous devrons à stocker de l’utilisateur et placent le jeton ne souhaitons demandera à nouveau.


> [AZURE.IMPORTANT] 
Le code ci-dessus prend tout utilisateur qui se trouve à s’authentifier à notre serveur. Il s’agit d’enregistrement automatique. Dans les serveurs de production vous ne voudriez pas que permettre à toute personne sans avoir d’abord les passer par une procédure d’enregistrement que vous décidez. C’est généralement le modèle que vous voir dans les applications consommateur qui vous permettent de vous inscrire sur Facebook mais puis vous demander d’entrer des informations supplémentaires. Si ce n’était pas un exemple d’application, nous pourrions avoir venez d’extraire l’e-mail à partir de l’objet de jeton qui est retourné et alors invité à entrer des informations supplémentaires. Dans la mesure où il s’agit d’un serveur de test il suffit de les ajouter à la base de données en mémoire.

- Ensuite, nous allons ajouter les méthodes qui nous permettra de suivre le connecté dans les utilisateurs requis par Passport. Cela inclut la sérialisation et désérialisation d’informations de l’utilisateur :

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
   log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};
```

- Ensuite, nous allons ajouter le code pour charger le moteur express. Vous trouverez, nous utilisons la /views par défaut et le modèle de /routes Express fournit.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Enfin, ajoutez les itinéraires qui transmet les demandes d’ouverture de session réel pour le `passport-azure-ad` moteur :

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. Utilisation de Passport pour émettre des demandes de connexion et de déconnexion pour Azure AD

Votre application est désormais correctement configurée pour communiquer avec le point de terminaison v2.0 en utilisant le protocole d’authentification OpenID de se connecter.  `passport-azure-ad`a prise en charge de tous les détails insupportable d’élaborer des messages d’authentification, la validation de jetons à partir d’AD Azure et gestion de session de l’utilisateur.  Il ne reste qu’à permettre à vos utilisateurs de se connecter, se déconnecter et rassembler des informations supplémentaires sur l’utilisateur connecté.

- Tout d’abord, vous permet d’ajouter les méthodes par défaut, de connexion, de compte et de déconnexion à notre `app.js` fichier :

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-   Examinons en détail :
    -   Le `/` gamme vous redirigera vers la vue index.ejs en passant de l’utilisateur dans la demande (s’il en existe un)
    - Le `/account` itinéraire sera le premier ***vous assurer que nous sommes authentifiés*** (nous mettons en œuvre qui ci-dessous) puis passer l’utilisateur dans la demande de sorte que nous pouvons obtenir plus d’informations sur l’utilisateur.
    - Le `/login` gamme appellera notre authentificateur d’azuread-openidconnect de `passport-azuread` et si qui n’aboutit pas va rediriger l’utilisateur vers /login
    - Le `/logout` sera simplement appeler la logout.ejs (et router) qui efface les cookies et puis renvoyer l’utilisateur à index.ejs


- Pour la dernière partie de `app.js`, ajoutez la méthode EnsureAuthenticated qui est utilisée dans `/account` ci-dessus.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}
```

- Enfin, nous allons créer le serveur lui-même dans `app.js`:

```JavaScript

app.listen(3000);

```


## <a name="5-create-the-views-and-routes-in-express-to-display-our-user-in-the-website"></a>5. créer les vues et les itinéraires dans express pour afficher notre utilisateur dans le site Web

Nous avons notre `app.js` complète. Maintenant il nous faut simplement ajouter les itinéraires et les vues qui affichent les informations que nous obtenons à l’utilisateur ainsi que la poignée du `/logout` et `/login` que nous avons créé des itinéraires.

- Créer le `/routes/index.js` gamme sous le répertoire racine.

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Créer le `/routes/user.js` gamme sous le répertoire racine

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Ces itinéraires simples simplement passe le long de la demande pour nos vues, y compris l’utilisateur s’il est présent.

- Créer le `/views/index.ejs` sous le répertoire racine. Il s’agit d’une page simple qui va appeler nos méthodes de connexion et de déconnexion et nous permettent de récupérer les informations de compte. Notez que nous pouvons utiliser la conditionnelle `if (!user)` en tant que l’utilisateur transmises par l’intermédiaire de la demande est preuve que nous avons connecté de l’utilisateur.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

- Créer le `/views/account.ejs` afficher sous le répertoire racine, afin que nous pouvons afficher des informations supplémentaires qui `passport-azuread` a mis dans la demande de l’utilisateur.

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Enfin, nous allons effectuer cette recherche assez en ajoutant une mise en page. Créer la ' / affichage des views/layout.ejs sous le répertoire racine

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>Passport-OpenID Example</title>
    </head>
    <body>
        <% if (!user) { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/login">Log In</a>
            </p>
        <% } else { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/account">Account</a> | 
            <a href="/logout">Log Out</a>
            </p>
        <% } %>
        <%- body %>
    </body>
</html>
```

Enfin, générez et exécutez votre application ! 

Exécutez `node app.js` et accédez à`http://localhost:3000`


Connectez-vous à l’aide d’un Account de Microsoft personnel ou un compte de travail ou de l’école et remarquez comment l’identité de l’utilisateur est répercutée dans la liste Account.  Vous disposez maintenant d’une application web sécurisée à l’aide de protocoles standard qui peuvent authentifier les utilisateurs avec leurs comptes personnels et de travail ou aux études.

Pour référence, l’exemple complet (sans les valeurs de configuration) [est fourni sous la forme d’un fichier zip ici](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip), ou vous pouvez le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


Vous pouvez désormais déplacer sur des sujets plus avancés.  Vous souhaiterez peut-être essayer :

[Sécuriser un site Web API avec Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
