<properties
    pageTitle="Mise en route avec les applications web Node.js dans le Service d’application Azure"
    description="Découvrez comment déployer une application Node.js à une application web dans le Service d’application Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="get-started-article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Mise en route avec les applications web Node.js dans le Service d’application Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Ce didacticiel explique comment créer une simple application de [Node.js] et déployez-le au [Service d’application Azure] à partir d’un environnement de ligne de commande, tels que cmd.exe ou bash. Vous pouvez suivre les instructions de ce didacticiel sur n’importe quel système d’exploitation pouvant exécuter Node.js.

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)] 

<a name="prereq"></a>
## <a name="prerequisites"></a>Conditions préalables

- [Node.js]
- [Bower]
- [Yeoman]
- [GIT]
- [CLI Azure]
- Un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [vous inscrire à une évaluation gratuite] ou [activer vos avantages d’abonné de Visual Studio].

## <a name="create-and-deploy-a-simple-nodejs-web-app"></a>Créer et déployer une application web de Node.js simple

1. Ouvrez le terminal de ligne de commande de votre choix et installer l' [Express générateur pour Yeoman].

        npm install -g generator-express

2. `CD`à un répertoire de travail et générer une application expresse à l’aide de la syntaxe suivante :

        yo express
        
    Choisissez les options suivantes lorsque vous y êtes invité :  

    `? Would you like to create a new directory for your project?`**Oui**  
    `? Enter directory name`**{appname}**  
    `? Select a version to install:`**MVC**  
    `? Select a view engine to use:`**Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):`**Aucun**  
    `? Select a database to use:`**Aucun**  
    `? Select a build tool to use:`**Grunt**

3. `CD`dans le répertoire racine de votre application de nouveau et le démarrer pour vous assurer qu’il s’exécute dans votre environnement de développement :

        npm start

    Dans votre navigateur, accédez à <http://localhost:3000> pour vous assurer que vous pouvez voir la page d’accueil Express. Une fois que vous avez vérifié l’exécution de votre application correctement, utilisez `Ctrl-C` pour l’arrêter.
    
1. Modifier en mode d’ASM et connectez-vous à Azure (vous devez [Azure CLI](#prereq)) :

        azure config mode asm
        azure login

    Suivez l’invite pour continuer la connexion dans un navigateur avec un compte Microsoft qui comporte votre abonnement Azure.

2. Assurez-vous que vous êtes toujours dans le répertoire racine de votre application, puis créez la ressource de l’application de Service de l’application dans Azure avec un nom d’application unique avec la commande suivante. Par exemple : http://{appname}.azurewebsites.net

        azure site create --git {appname}

    Suivez l’invite de sélection d’une région Azure pour déployer sur. Si vous avez jamais configuré les informations d’identification de déploiement Git/FTP pour votre abonnement Azure, vous allez également invité à les créer.

3. Ouvrez le fichier./config/config.js à partir de la racine de votre application et de modifier le port de production de `process.env.port`; votre `production` propriété dans la `config` objet doit ressembler à l’exemple suivant :

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Cela permet à votre application Node.js répondre pour web demandes sur le port par défaut qu’iisnode est à l’écoute.
    
4. Ouvrez./package.json et ajoutez le `engines` propriété pour [spécifier la version Node.js souhaitée](#version).

        "engines": {
            "node": "6.6.0"
        }, 

4. Enregistrez vos modifications, puis git permet de déployer votre application sur Azure :

        git add .
        git commit -m "{your commit message}"
        git push azure master

    Le générateur Express fournit déjà un fichier .gitignore, de sorte que votre `git push` ne consomme la bande passante de la tentative de transfert de la node_modules / répertoire.

5. Enfin, lancez votre application Azure live dans le navigateur :

        azure site browse

    Vous devez maintenant voir votre application de web Node.js en direct dans le Service d’application Azure.
    
    ![Exemple de navigation de l’application déployée.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Mise à jour de votre application web de Node.js

Pour mettre à jour votre application de web Node.js en cours d’exécution dans le Service d’application, il suffit d’exécuter `git add`, `git commit`, et `git push` comme vous l’avez fait lorsque vous avez déployé votre application web pour la première.
     
## <a name="how-app-service-deploys-your-nodejs-app"></a>Comment le Service de l’application déploie votre application Node.js

Service d’application Azure utilise [iisnode] pour exécuter les applications de Node.js. L’infrastructure du langage commun Azure et le moteur Kudu (déploiement Git) fonctionnent ensemble pour vous offrir une expérience agréable lors de développer et de déployer des applications Node.js à partir de la ligne de commande. 

- `azure site create --git`reconnaît le modèle Node.js commun de server.js ou de app.js et crée un iisnode.yml de votre répertoire racine. Vous pouvez utiliser ce fichier pour personnaliser les iisnode.
- À `git push azure master`, Kudu automatise les tâches de déploiement suivantes :

    - Si package.json se trouve dans la racine du référentiel, exécutez `npm install --production`.
    - Générer un fichier Web.config pour iisnode qui pointe vers votre script de démarrage de package.json (par exemple, server.js ou app.js).
    - Personnaliser le Web.config pour préparer votre application pour le débogage avec l’inspecteur-nœud.
    
## <a name="use-a-nodejs-framework"></a>Utilisation d’une infrastructure Node.js

Si vous utilisez un cadre Node.js populaires, tels que les [Sails.js] [ SAILSJS] ou [MEAN.js] [ MEANJS] pour développer des applications, vous pouvez déployer ceux pour le Service de l’application. Des structures populaires Node.js ont leurs particularités spécifiques et leurs dépendances de package Conserver mise à jour. Toutefois, Service d’application rend les journaux stdout et stderr disponibles, afin de savoir exactement ce qui se passe avec votre application et de modifier en conséquence. Pour plus d’informations, consultez [obtenir les journaux stdout et stderr à partir d’iisnode](#iisnodelog).

Les didacticiels suivants vous expliquera comment travailler avec un cadre spécifique dans le Service d’application :

- [Déployer une application web de Sails.js service d’application Azure]
- [Créer une application de conversation Node.js avec Socket.IO dans le Service d’application Azure]
- [L’utilisation de io.js avec Azure Application Service Web Apps]

<a name="version"></a>
## <a name="use-a-specific-nodejs-engine"></a>Utilisez un moteur de Node.js spécifique

Dans votre flux de travail typique, vous indiquez Service d’application d’utiliser un moteur de Node.js spécifique comme vous le feriez normalement dans package.json.
Par exemple :

    "engines": {
        "node": "6.6.0"
    }, 

Le moteur de déploiement Kudu détermine le moteur de Node.js à utiliser dans l’ordre suivant :

- Examinons tout d’abord iisnode.yml pour voir si `nodeProcessCommandLine` est spécifié. Si Oui, puis l’utiliser.
- Ensuite, examinez package.json pour voir si `"node": "..."` est spécifié dans le `engines` objet. Si Oui, puis l’utiliser.
- Par défaut, choisissez une version de Node.js par défaut.

>[AZURE.NOTE] Il est recommandé de définir explicitement le moteur Node.js souhaité. La version de Node.js par défaut peut modifier, et vous obtiendrez des erreurs dans votre application web d’Azure, car la version de Node.js par défaut n’est pas appropriée pour votre application.

<a name="iisnodelog"></a>
## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Accédez aux journaux stdout et stderr à partir d’iisnode

Pour lire les journaux iisnode, procédez comme suit.

> [AZURE.NOTE] Une fois ces étapes effectuées, les fichiers journaux n’existe ne peut-être pas jusqu'à ce qu’une erreur se produit.

1. Ouvrez le fichier iisnode.yml qui fournit de l’infrastructure du langage commun Azure.

2. Définissez les deux paramètres suivants : 

        loggingEnabled: true
        logDirectory: iisnode
    
    Ensemble, ils indiquent les iisnode dans le Service d’application pour mettre la sortie de stdout et stderror dans les D:\home\site\wwwroot\**iisnode** répertoire.

3. Enregistrez vos modifications, puis distribuer vos modifications vers Azure avec les commandes Git suivantes :

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode est désormais configuré. Les étapes suivantes vous montrent comment accéder à ces fichiers journaux.
     
4. Dans votre navigateur, accéder à la console de débogage pour votre application, qui se trouve à Kudu :

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Cette URL est différente de l’URL d’application web par l’ajout de "*.scm.*" le nom DNS. Si vous omettez cet ajout à l’URL, vous obtiendrez une erreur 404.

5. Accédez à D:\home\site\wwwroot\iisnode

    ![Navigation à l’emplacement des fichiers journaux iisnode.][iislog-kudu-console-find]

6. Cliquez sur l’icône **Modifier** pour le journal que vous souhaitez lire. Vous pouvez également cliquer sur **Télécharger** ou **Supprimer** si vous le souhaitez.

    ![Ouverture d’un fichier de journal d’iisnode.][iislog-kudu-console-open]

    Vous pouvez maintenant voir le journal pour vous aider à déboguer votre déploiement du Service de l’application.
    
    ![Examen d’un fichier journal d’iisnode.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Déboguer votre application avec nœud-inspecteur

Si vous utilisez un nœud-inspecteur pour déboguer vos applications Node.js, vous pouvez l’utiliser pour votre application de Service de l’application direct. Nœud-inspecteur est préinstallé dans l’installation d’iisnode pour le Service de l’application. Et si vous déployez via Git, Web.config généré automatiquement à partir de Kudu contient déjà tous les la configuration que vous devez activer le nœud-inspecteur.

Pour activer l’inspecteur-nœud, procédez comme suit :

1. Ouvrez iisnode.yml à la racine de votre référentiel et spécifiez les paramètres suivants : 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Enregistrez vos modifications, puis distribuer vos modifications vers Azure avec les commandes Git suivantes :

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. Maintenant, seulement accéder au fichier de démarrage de votre application comme spécifié par le script de démarrage dans votre package.json, avec /debug ajouté à l’URL. Par exemple,

        http://{appname}.azurewebsites.net/server.js/debug
    
    Ou,
    
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Plus de ressources

- [Spécification d’une version de Node.js dans une application Azure](../nodejs-specify-node-version-azure-apps.md)
- [Meilleures pratiques et guide de dépannage pour les applications de Node.js sur Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Comment faire pour déboguer une application web de Node.js dans le Service d’application Azure](web-sites-nodejs-debug.md)
- [À l’aide de Modules de Node.js avec des applications Azure](../nodejs-use-node-modules-azure-apps.md)
- [Azure Application Service Web applications : Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Centre de développement Node.js](/develop/nodejs/)
- [Mise en route avec des applications web dans le Service d’application Azure](app-service-web-get-started.md)
- [Exploration de la Console de débogage Ultrasecret Kudu]

<!-- URL List -->

[CLI Azure]: ../xplat-cli-install.md
[Service d’application Azure]: ../app-service/app-service-value-prop-what-is.md
[activer vos avantages d’abonné de Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Créer une application de conversation Node.js avec Socket.IO dans le Service d’application Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Déployer une application web de Sails.js service d’application Azure]: ./app-service-web-nodejs-sails.md
[Exploration de la Console de débogage Ultrasecret Kudu]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Express Générateur de Yeoman]: https://github.com/petecoop/generator-express
[GIT]: http://www.git-scm.com/downloads
[L’utilisation de io.js avec Azure Application Service Web Apps]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[Inscrivez-vous pour une version d’évaluation gratuite]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png
