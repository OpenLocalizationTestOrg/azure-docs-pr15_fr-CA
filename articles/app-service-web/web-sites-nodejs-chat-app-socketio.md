<properties
    pageTitle="Créer une application de conversation Node.js avec Socket.IO dans le Service d’application Azure"
    description="Didacticiel qui illustre l’utilisation de socket.io dans une application web de node.js hébergée sur Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-chat-application-with-socketio-in-azure-app-service"></a>Créer une application de conversation Node.js avec Socket.IO dans le Service d’application Azure

Socket.IO fournit des communications en temps réel entre votre node.js serveur et les clients à l’aide du protocole WebSocket. Il prend également en charge la procédure de secours autres transports (par exemple, l’interrogation de long) qui fonctionnent avec les navigateurs plus anciens. Ce didacticiel va vous guident dans une application de conversation Socket.IO basé sous la forme d’une application web Azure d’hébergement et vous indiquent comment adapter l’application à l’aide du [Cache Redis d’Azure]. Pour plus d’informations sur Socket.IO, consultez <http://socket.io/>.

> [AZURE.NOTE] Les procédures décrites dans cette tâche s’appliquent à l' [Application de Service Web Apps]; pour les Services en nuage, consultez [créer une Application de conversation Node.js avec Socket.IO sur un Service en nuage Azure].

## <a name="download-the-chat-example"></a>Télécharger l’exemple de conversation

Pour ce projet, nous allons utiliser l’exemple de conversation à partir du [référentiel de Socket.IO GitHub]. Effectuez les opérations suivantes pour télécharger l’exemple et l’ajouter au projet que vous avez précédemment créé.

1.  Télécharger un [ZIP ou GZ de version archivée] du projet Socket.IO (version 1.3.5 a été utilisée pour ce document.)

1.  Extraire l’archive et les copie les **exemples\\chat** répertoire vers un nouvel emplacement. Par exemple, ** \\nœud\\chat**.

## <a name="modify-appjs-and-install-modules"></a>Modifier app.js et installer des modules

1.  Renommez le fichier **index.js** en **app.js**. Cela permet d’Azure détecter qu’il s’agit d’une application Node.js.

1.  Ouvrez le fichier **app.js** dans un éditeur de texte. Modifier la ligne contenant `var io = require('../..')(server);` comme indiqué ci-dessous :

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        // New:
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

1. Ouvrez le fichier **package.json** et ajoutez une référence à socket.io sous `dependencies`, comme indiqué ci-dessous :

        "dependencies": {
          "express": "3.4.8",
          "socket.io": "1.3.5"
        }

1. À partir de la ligne de commande, remplacez par le ** \\nœud\\chat** npm directory et utiliser pour installer les modules requis par cette application :

        npm install

    Les modules seront installés dans un sous-dossier nommé **node_modules**.

## <a name="create-an-azure-web-app"></a>Créer une application Web Azure

Suivez ces étapes pour créer une application web d’Azure, activer la publication de Git et activer la prise en charge de WebSocket de l’application web.

> [AZURE.NOTE] Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">D’essai Azure</a>.

1. Installer l’Interface de ligne de commande (CLI Azure) Azure et se connecter à votre abonnement Azure. Reportez-vous à la section [installation et configuration de l’interface CLI Azure](../xplat-cli-install.md).

1. S’il s’agit de votre première configuration de temps un référentiel dans Azure, vous devez créer des informations d’identification. À partir de la CLI d’Azure, entrez la commande suivante :

        azure site deployment user set [username] [password]

1. Remplacez par le ** \\node\chat** directory et utilisez la commande suivante pour créer un nouveau Azure web app et un référentiel Git local. Cette commande crée également un nommé distant Git « azure ».

        azure site create mysitename --git

    Vous devez remplacer « mysitename » avec un nom unique pour votre application web.

1. Valider les fichiers existants dans le référentiel local en utilisant les commandes suivantes :

        git add .
        git commit -m "Initial commit"

1. Distribuer les fichiers sur le référentiel Azure Web Apps avec la commande suivante :

        git push azure master

    Lorsque vous y êtes invité, entrez vos informations d’identification de l’étape 2. Vous recevrez des messages d’état comme modules sont importés sur le serveur. Une fois ce processus terminé, l’application sera hébergée sur votre application web Azure.

    > [AZURE.NOTE] Lors de l’installation du module, vous remarquerez peut-être des erreurs qui ' le projet importé n’a pas été trouvé ». Elles peuvent être ignorées sans risque.

1. Socket.IO utilise le protocole WebSocket, qui n’est pas activés par défaut sur Azure. Pour activer les sockets web, utilisez la commande suivante :

        azure site set -w

    Si vous y êtes invité, entrez le nom de l’application web.

    >[AZURE.NOTE]
    >Le 'site azure set -w' commande est fonctionnent uniquement avec la version 0.7.4 ou supérieur de l’Interface de ligne de commande Azure. Vous pouvez également activer la prise en charge de WebSocket via le [Portail Azure](https://portal.azure.com).
    >
    >Pour activer le protocole WebSocket via le portail d’Azure, cliquez sur l’application web à partir de la blade d’applications Web, cliquez sur **tous les paramètres** > **paramètres de l’Application**. Sous **Web Sockets**, cliquez **sur**. Puis cliquez sur **Enregistrer**.

1. Pour afficher l’application web sur Azure, utilisez la commande suivante pour lancer votre navigateur web et accédez à l’application web hébergé :

        azure site browse

Votre application est en cours d’exécution sur Azure et peut relayer des messages de conversation entre les différents clients à l’aide de Socket.IO.

## <a name="scale-out"></a>Évoluer

Les applications Socket.IO peuvent faire évoluer à l’aide d’un __adaptateur__ pour distribuer des messages et événements entre plusieurs instances de l’application. Alors que plusieurs cartes sont disponibles, la carte de [redis-socket.io] facilement utilisable avec la fonctionnalité de Cache Redis d’Azure.

> [AZURE.NOTE] Une exigence supplémentaire pour faire évoluer une solution Socket.IO est prise en charge de sessions persistantes. Sessions persistantes sont activées par défaut pour les applications Web de Azure Azure demande de routage. Pour plus d’informations, voir les [Instance affinité dans des Sites Web Azure].

### <a name="create-a-redis-cache"></a>Créer un cache de Redis

Effectuez les étapes dans [créer un cache dans le Cache de Redis Azure] pour créer un cache.

> [AZURE.NOTE] Enregistrer le __nom d’hôte__ et de la __clé primaire__ de votre cache, il seront nécessaire dans les étapes suivantes.

### <a name="add-the-redis-and-socketio-redis-modules"></a>Ajouter les modules de socket.io-redis et le redis

1. À partir d’une ligne de commande, remplacez par le __ \\nœud\\chat__ directory et utilisez la commande suivante.

        npm install socket.io-redis@0.1.4 redis@0.12.1 --save

    > [AZURE.NOTE] Les versions spécifiées dans cette commande sont les versions utilisées lors du test de cet article.

1. Modifiez le fichier __app.js__ pour ajouter ce qui suit juste après les lignes`var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Remplacer __redishostname__ et __rediskey__ avec le nom d’hôte et de la clé pour votre cache Redis.

    Vous créez une publication et abonnement client au cache Redis créé précédemment. Les clients sont ensuite utilisés avec la carte pour configurer Socket.IO pour utiliser le cache Redis pour la transmission des messages et les événements entre les instances de votre application

    > [AZURE.NOTE] Alors que la carte __redis-socket.io__ peut communiquer directement avec Redis, la version actuelle ne gère pas l’authentification requise par le cache d’Azure Redis. Ainsi, la connexion initiale est créée à l’aide du module __redis__ , puis le client est passé à la carte __socket.io-redis__ .
    >
    > Cache de Redis Azure prend en charge les connexions sécurisées à l’aide du port 6380, les modules utilisés dans cet exemple ne prend pas en charge les connexions sécurisées à 7/14/2014. Le code ci-dessus utilise la par défaut, le port non sécurisé de 6379.

1. Enregistrer modification __app.js__

### <a name="commit-changes-and-redeploy"></a>Valider les modifications et redéployer

À partir de la ligne de commande dans le __ \\nœud\\chat__ directory, utilisez les commandes suivantes pour valider les modifications et redéployer l’application.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Une fois que les modifications ont été envoyées vers le serveur, vous pouvez mettre à l’échelle votre site sur plusieurs instances à l’aide de la commande suivante.

    azure site scale instances --instances #

Où __#__ est le nombre d’instances à créer.

Vous pouvez vous connecter à votre application web à partir de plusieurs navigateurs ou ordinateurs pour vérifier que les messages sont correctement envoyés à tous les clients.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="connection-limits"></a>Limites de connexion

Les applications Web Azure est disponible dans plusieurs UGS, qui déterminent les ressources disponibles pour votre site. Cela inclut le nombre de connexions autorisées de WebSocket. Pour plus d’informations, consultez la [page Web des applications de prix].

### <a name="messages-arent-being-sent-using-websockets"></a>Les messages ne sont pas envoyées à l’aide du protocole WebSocket

Si les navigateurs client conserver retomber à durée d’interrogation au lieu d’utiliser le protocole WebSocket, il peut être dû à l’une des opérations suivantes.

* **Essayez de limiter le transport de tout protocole WebSocket**

    Pour Socket.IO à utiliser les WebSockets comme le transport des messages, le serveur et le client doivent prendre en charge WebSocket. Si une ou l’autre ne le fait pas, Socket.IO négociera un autre transport, telles que l’interrogation longue. La liste par défaut de transports utilisés par Socket.IO est ` websocket, htmlfile, xhr-polling, jsonp-polling`. Vous pouvez le forcer à utiliser uniquement le protocole WebSocket en ajoutant le code suivant au fichier **app.js** , après la ligne contenant `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [AZURE.NOTE] Notez que les anciens navigateurs qui ne prennent pas en charge le protocole WebSocket ne sera pas en mesure de se connecter au site, tandis que le code ci-dessus est actif, il restreint les communications au protocole WebSocket uniquement.

* **Utiliser le protocole SSL**

    Protocole WebSocket s’appuie sur certains en-têtes HTTP utilisés plus petite, telle que l’en-tête de **mise à niveau** . Certains périphériques réseau intermédiaires, tels que des serveurs proxy web, peuvent supprimer ces en-têtes. Pour éviter ce problème, vous pouvez établir la connexion WebSocket via le protocole SSL.

    Un moyen simple d’y parvenir consiste à configurer les Socket.IO pour `match origin protocol`. Cela ordonne Socket.IO pour sécuriser les communications de protocole WebSocket identique à celui de la demande HTTP/HTTPS d’origine pour la page web. Si un navigateur utilise une URL HTTPS pour accéder à votre site Web, les communications de WebSocket via Socket.IO sera sécurisées via le protocole SSL.

    Pour modifier cet exemple pour activer cette configuration de, ajoutez le code suivant dans le fichier **app.js** après la ligne contenant `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

* **Vérifiez les paramètres du fichier web.config**

    Les applications web Azure qui hébergent des applications de Node.js utilisent le fichier **web.config** pour router les demandes entrantes vers l’application Node.js. Pour le protocole WebSocket pour fonctionner correctement avec les applications Node.js, **web.config** doit contenir l’entrée suivante.

        <webSocket enabled="false"/>

    Cette opération désactive le module WebSocket de IIS, ce qui inclut sa propre implémentation de WebSocket et entre en conflit avec les modules WebSocket spécifiques Node.js comme Socket.IO. Si cette ligne n’est pas présente ou est définie sur `true`, c’est peut-être la raison pour laquelle le transport WebSocket ne fonctionne pas pour votre application.

    Normalement, les applications de Node.js n’incluent pas de fichier **web.config** , afin que les sites Web d’Azure génère un automatiquement pour les applications de Node.js lorsqu’ils sont déployés. Dans la mesure où ce fichier est automatiquement généré sur le serveur, vous devez utiliser le FTP ou FTPS l’URL de votre site Web pour afficher ce fichier. Vous pouvez trouver le FTP et FTPS l’URL de votre site dans le portail classique en sélectionnant votre application web, puis sur le lien de **tableau de bord** . Les URL sont affichées dans la section **Aperçu rapide** .

    > [AZURE.NOTE] Le fichier **web.config** est généré uniquement par des sites Web d’Azure, si votre application n’en fournit pas. Si vous fournissez un fichier **web.config** dans la racine de votre projet d’application, il sera utilisé par les applications Web Azure.

    Si l’entrée n’est pas présente ou est définie sur une valeur de `true`, puis créez un **web.config** dans la racine de votre application Node.js et spécifiez une valeur de `false`.  Pour référence, la Voici un défaut **web.config** pour une application qui utilise **app.js** comme point d’entrée.

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js web app entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    Si votre application utilise un point d’entrée autre que **app.js**, vous devez remplacer toutes les occurrences de **app.js** par le point d’entrée correct. Par exemple, remplacez **app.js** par **server.js**.

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application], où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment créer une application de conversation hébergée dans une application web Azure. Vous pouvez également héberger cette application comme un Service en nuage Azure. Pour savoir comment procéder, consultez [créer une Application de conversation Node.js avec Socket.IO sur un Service en nuage Azure].

Pour plus d’informations, consultez également le [Centre pour développeurs Node.js].

## <a name="whats-changed"></a>Ce qui a changé

* Pour obtenir un guide pour la modification de sites Web au Service de l’application voir : [Service d’application Azure et son Impact sur les Services Azure existants].

<!-- URL List -->

[Cache d’Azure Redis]: /documentation/services/redis-cache/
[Applications de Service d’application Web]: http://go.microsoft.com/fwlink/?LinkId=529714
[Page de tarification des applications Web]: http://go.microsoft.com/fwlink/?LinkId=511643
[Créer une Application de conversation de Node.js avec Socket.IO sur un Service Cloud Azure]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../xplat-cli-install.md
[Service d’application Azure et son Impact sur les Services Azure existants]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centre de développement Node.js]: /develop/nodejs/
[Essayez le Service d’application]: http://go.microsoft.com/fwlink/?LinkId=523751
[Affinité d’instance dans les Sites Web Azure]: https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Créer un cache dans le Cache de Redis Azure]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[Socket.IO-redis]: https://github.com/socketio/socket.io-redis
[Référentiel de Socket.IO GitHub]: https://github.com/socketio/socket.io
[ZIP ou GZ archivés version]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
