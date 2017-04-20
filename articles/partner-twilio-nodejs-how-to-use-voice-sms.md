<properties 
    pageTitle="L’utilisation de Twilio pour, VoIP, messagerie vocale et SMS dans Azure" 
    description="Apprenez à effectuer un appel téléphonique et d’envoyer un message SMS avec le service Twilio API sur Azure. Exemples de code écrits en Node.js." 
    services="" 
    documentationCenter="nodejs" 
    authors="devinrader" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="wpickett"/>


# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>L’utilisation de Twilio pour, VoIP, messagerie vocale et SMS dans Azure

Ce guide montre comment créer des applications qui communiquent avec Twilio et node.js sur Azure.

<a id="whatis"/>
## <a name="what-is-twilio"></a>Quel est Twilio ?

Twilio est une plate-forme d’API qui permet aux développeurs d’effectuer et recevoir des appels téléphoniques, envoyer et recevoir des messages texte et incorporer appel VoIP dans les applications mobiles basés sur navigateur et natives.  Nous allons Passez brièvement en revue avant de plonger comment tout cela fonctionne.

### <a name="receiving-calls-and-text-messages"></a>Réception d’appels et des Messages de texte

Twilio permet aux développeurs [d’acheter des numéros de téléphone programmables] [ purchase_phone] qui peut être utilisé pour envoyer et recevoir des appels et des messages de texte.  Lorsqu’un nombre Twilio reçoit un appel entrant ou du texte, Twilio envoie votre application web un HTTP POST ou GET, vous demandant de pour obtenir des instructions sur la façon de traiter l’appel ou le texte.  Votre serveur répond à la demande HTTP de Twilio [TwiML][twiml], un ensemble de balises XML qui contiennent des instructions sur la façon de gérer un appel ou du texte simple.  Nous allons voir des exemples de TwiML dans un instant.

### <a name="making-calls-and-sending-text-messages"></a>Émettre des appels et envoyer des Messages texte

En effectuant des demandes HTTP à l’API de service web Twilio, les développeurs peuvent envoyer des messages texte ou initier des appels téléphoniques sortants.  Pour les appels sortants, le développeur doit également spécifier une URL qui renvoie les instructions TwiML pour traiter les appels sortants une fois qu’il est connecté.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>L’incorporation de fonctionnalités VoIP dans le code de l’interface utilisateur (JavaScript, iOS ou Android)

Twilio fournit un kit de développement logiciel côté client qui peut transformer n’importe quel navigateur web de bureau, application iOS ou Android app un téléphone VoIP.  Dans cet article, nous allons étudier comment utiliser VoIP appel dans le navigateur.  En plus du SDK de JavaScript Twilio en cours d’exécution dans le navigateur, une application côté serveur (notre application node.js) doit servir à émettre un « jeton de capacité » au client JavaScript.  Vous pouvez en savoir plus sur l’utilisation de VoIP avec node.js [sur le blog de dev Twilio][voipnode].

<a id="signup"/>
## <a name="sign-up-for-twilio-microsoft-discount"></a>Inscrivez-vous Twilio (remise de Microsoft)

Avant d’utiliser les services de Twilio, vous devez première [inscription à un compte][signup].  Les clients Microsoft Azure recevoir une remise spéciale - [Veillez à Inscrivez-vous ici][signup]!

<a id="azuresite"/>
## <a name="create-and-deploy-a-nodejs-azure-website"></a>Créer et déployer un site de Web node.js Azure

Ensuite, vous devrez créer un site Web de node.js exécute sur Azure.  [La documentation officielle de cette procédure se trouve ici][azure_new_site].  À un niveau élevé, vous allez effectuer les opérations suivantes :

* Ouverture d’un compte Azure, si vous n’en avez pas déjà
* Pour créer un nouveau site Web à l’aide de la console d’administration Azure
* Ajout de prise en charge du contrôle source (nous supposerons que vous avez utilisé le git)
* Création d’un fichier `server.js` avec une application web de node.js simple
* Déploiement de cette application simple vers Azure

<a id="twiliomodule"/>
## <a name="configure-the-twilio-module"></a>Configurer le Module Twilio

Ensuite, nous commencerons à écrire une application node.js simple qui utilise l’API Twilio.  Avant de commencer, nous devons configurer nos informations d’identification du compte Twilio.  

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configuration des informations d’identification de Twilio dans les Variables d’environnement système

Afin que les requêtes authentifiées contre le Twilio back-end, nous devons notre SID de compte et un jeton auth, quelle fonction sous le nom d’utilisateur et le mot de passe défini pour notre compte Twilio. Il est le plus sûr moyen de configurer ces derniers pour une utilisation avec le module de nœud dans Azure via des variables d’environnement système, que vous pouvez définir directement dans la console d’administration Azure.

Sélectionnez votre site node.js et cliquez sur le lien « Configurer ».  Si vous faites défiler vers le bas, vous verrez une zone dans laquelle vous pouvez définir des propriétés de configuration de votre application.  Entrez vos informations d’identification du compte Twilio ([trouvé sur votre tableau de bord de Twilio][twilio_dashboard]) comme indiqué - veillez à les nommer « TWILIO_ACCOUNT_SID » et « TWILIO_AUTH_TOKEN », respectivement :

![Console d’administration Azure][azure-admin-console]

Une fois que vous avez configuré ces variables, redémarrez votre application dans la console d’Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Déclaration du module Twilio dans package.json

Ensuite, nous devons créer un package.json pour gérer les dépendances de nos modules nœud via [npm].  Au même niveau que le fichier « server.js » que vous avez créé dans le didacticiel Azure/node.js, créez un fichier nommé « package.json ».  À l’intérieur de ce fichier, placez le texte suivant :

  {« nom » : «-nom de l’application », « version » : « 0.0.1 », « privé » : true, « scripts » : {« Démarrer » : « serveur de nœud »}, « dépendances » : {« express » : « 3.1.0 », « ejs » : "*«, « twilio » : »*"}}

Il déclare le module twilio sous la forme d’une dépendance, ainsi que le populaire [infrastructure web express] [ express] et le moteur du modèle EJS.  OK, maintenant nous sommes tous paramétrés, nous allons écrire du code !

<a id="makecall"/>
## <a name="make-an-outbound-call"></a>Effectuer un appel sortant

Nous allons créer un formulaire simple qui place un appel à un nombre, que nous avons choisi.  Ouvrez server.js et entrez le code suivant.  Notez que le texte « CHANGE_ME » - y placez le nom de votre site Web azure :

    // Module dependencies
    var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

    // Create Express web application
    var app = express();

    // Express configuration
    app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
    });
    app.configure('development', function(){
      app.use(express.errorHandler());
    });

    // Render an HTML user interface for the application's home page
    app.get('/', function(request, response) {
      response.render('index');
    });

    // Handle the form POST to place a call
    app.post('/call', function(request, response) {
      var client = twilio();
      client.makeCall({
          // make a call to this number
          to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

    // Generate TwiML to handle an outbound call
    app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
    });

    // Start server
    http.createServer(app).listen(app.get('port'), function(){
      console.log("Express server listening on port " + app.get('port'));
    });

Ensuite, créez un répertoire nommé « vues » - à l’intérieur de ce répertoire, créez un fichier nommé « index.ejs » avec le contenu suivant :

    <!DOCTYPE html>
    <html>
    <head>
      <title>Twilio Test</title>
      <style>
        input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      </style>
    </head>
    <body>
      <h1>Twilio Test</h1>
      <form action="/call" method="POST">
          <input placeholder="Enter a phone number" name="number"/>
          <br/>
          <input type="submit" value="Call the number above"/>
      </form>
    </body>
    </html>

Maintenant déployer votre site Web sur Azure et ouvrez votre maison.  Vous pourrez entrer votre numéro de téléphone dans le champ de texte et recevoir un appel à partir de votre numéro de Twilio !

<a id="sendmessage"/>
## <a name="send-an-sms-message"></a>Envoyer un Message SMS

Maintenant, nous allons définir une interface utilisateur et la logique de traitement de formulaire d’envoyer un message texte.  Ouvrez « server.js » et ajoutez le code suivant après le dernier appel à « app.post » :

    app.post('/sms', function(request, response) {
      var client = twilio();
      client.sendSms({
          // send a text to this number
          to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

Dans « views/index.ejs », ajoutez une autre forme sous la première personne à soumettre un numéro et un message texte :

    <form action="/sms" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input placeholder="Enter a message to send" name="message"/>
      <br/>
      <input type="submit" value="Send text to the number above"/>
    </form>

Redéployer votre application Azure, et vous devez maintenant être capable de soumettre le formulaire et envoyer vous-même (ou l’un de vos amis les plus proches) un message texte !

<a id="nextsteps"/>
## <a name="next-steps"></a>Étapes suivantes

Vous avez désormais appris les notions de base de l’utilisation de node.js et Twilio pour créer des applications qui communiquent.  Mais ces exemples de travail à peine la surface de ce qui est possible avec Twilio et node.js.  Pour plus d’informations à l’aide de Twilio avec node.js, consultez les ressources suivantes :

* [Documents de module officiel][docs]
* [Didacticiel sur les systèmes VoIP avec les applications de node.js][voipnode]
* [Votr - un vote application avec node.js et CouchDB (trois parties) de SMS en temps réel][votr]
* [Programmation de paire dans le navigateur avec node.js][pair]

Nous espérons que vous appréciez node.js et Twilio de piratage sur Azure !

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: /app-service-web/web-sites-nodejs-develop-deploy-mac.md
[twilio_dashboard]: https://www.twilio.com/user/account
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png



