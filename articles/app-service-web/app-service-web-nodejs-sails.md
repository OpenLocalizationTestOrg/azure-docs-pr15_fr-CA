<properties
    pageTitle="Déployer une application web de Sails.js service d’application Azure"
    description="Découvrez comment déployer une application Node.js Azure Application Service. Ce didacticiel vous montre comment déployer une application web de Sails.js."
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
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="cephalin"/>

# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Déployer une application web de Sails.js service d’application Azure

Ce didacticiel vous montre comment déployer une application de Sails.js au Service d’application Azure. Dans le processus, vous pouvez en déduire des connaissances générales sur la configuration de votre application Node.js à exécuter dans le Service d’application. 

Vous devez disposer des connaissances pratiques de Sails.js. Ce didacticiel n’est pas destiné à résoudre les problèmes liés à l’exécution de Sail.js en général.


## <a name="prerequisites"></a>Conditions préalables

- [Node.js](https://nodejs.org/)
- [Sails.js](http://sailsjs.org/get-started)
- [GIT](http://www.git-scm.com/downloads)
- [CLI Azure](../xplat-cli-install.md)
- Un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [vous inscrire à une évaluation gratuite](/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer vos avantages d’abonné de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Pour voir les services d’application Azure en action avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pouvez créer une application de courte durée starter immédiatement dans le Service d’application : aucune carte de crédit requise, aucun engagement.

## <a name="step-1-create-a-sailsjs-app-locally"></a>Étape 1 : Créer une application de Sails.js localement

Tout d’abord, créer rapidement une application de Sails.js par défaut dans votre environnement de développement en suivant ces étapes :

1. Ouvrez le terminal de ligne de commande de votre choix et `CD` dans un répertoire de travail.

2. Créer une application de Sails.js et exécutez-le :

        sails new <appname>
        cd <appname>
        sails lift

    Vérifiez que vous pouvez accéder à la page d’accueil par défaut dans http://localhost:1377.

## <a name="step-2-create-the-azure-app-resource"></a>Étape 2 : Créer la ressource d’application Azure

Ensuite, créez la ressource de Service de l’application dans Azure. Vous allez déployer votre application Sails.js lui, ultérieurement.

1. Ouvrez une session Azure similaires ainsi :
1. Sur le même terminal, modifier en mode d’ASM et connectez-vous pour Azure :

        azure config mode asm
        azure login

    Suivez l’invite pour continuer la connexion dans un navigateur avec un compte Microsoft qui comporte votre abonnement Azure.

2. Vérifiez que vous êtes toujours dans le répertoire racine de votre projet de Sails.js. Créez la ressource de l’application de Service de l’application dans Azure avec un nom d’application unique avec la commande suivante. L’URL de votre application web est http://&lt;appname >. azurewebsites.net.

        azure site create --git <appname>

    Suivez l’invite de sélection d’une région Azure pour déployer sur. Si vous avez jamais configuré les informations d’identification de déploiement Git/FTP pour votre abonnement Azure, vous allez également invité à les créer.

    Une fois que la ressource de l’application de Service d’application est créée :

    - Application de Sails.js est initialisée Git,
    - Votre référentiel initialisée Git local est connecté à la nouvelle application de Service de l’application comme un Git distant, nommés « azure », et
    - Et iisnode.yml fichier est créé dans le répertoire racine. Vous pouvez utiliser ce fichier pour configurer les [iisnode](https://github.com/tjanczuk/iisnode), qui utilise des services d’application pour exécuter les applications de Node.js.

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Étape 3 : Configurer et déployer votre application Sails.js

 Utilisation d’une application Sails.js dans le Service d’application se compose de trois étapes principales :

 - Configurer votre application pour qu’elle s’exécuter dans le Service d’application
 - Déployer sur l’application de Service
 - Lecture des journaux stderr et stdout pour résoudre les problèmes de déploiement

Procédez comme suit :

1. Ouvrez le nouveau fichier iisnode.yml de votre répertoire racine et ajouter les deux lignes suivantes :

        loggingEnabled: true
        logDirectory: iisnode

    L’enregistrement est désormais activé pour iisnode. Pour plus d’informations sur la façon dont cela fonctionne, consultez  [obtenir les journaux stdout et stderr à partir d’iisnode](app-service-web-nodejs-get-started.md#iisnodelog).

2. Ouvrez config/env/production.js pour configurer votre environnement de production et la valeur `port` et `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Vous pouvez trouver la documentation de ces paramètres de configuration dans la  [Documentation de Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

    Ensuite, vous devez vous assurer que [Grunt](https://www.npmjs.com/package/grunt) est compatible avec les lecteurs de réseau d’Azure. Grunt versions inférieures à 1.0.0 utilise un package obsolète [glob](https://www.npmjs.com/package/glob) (moins de 5.0.14), qui ne prend pas en charge les lecteurs réseau. 

3. Package.json d’ouvrir et de modifier le `grunt` version `1.0.0` et supprimer toutes les `grunt-*` packages. Votre `dependencies` propriété devrait ressembler à ceci :

        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },

3. Dans package.json, ajoutez le code suivant `engines` propriété pour définir la version de Node.js que nous voulons.

        "engines": {
            "node": "6.6.0"
        },

6. Enregistrez vos modifications et tester vos modifications afin de vous assurer que votre application continue à s’exécuter localement. Pour ce faire, supprimez le `node_modules` dossier et ensuite l’exécuter :

        npm install
        sails lift

4. Maintenant, utilisez git pour déployer votre application sur Azure :

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. Enfin, lancez votre application Azure live dans le navigateur :

        azure site browse

    Vous devez maintenant voir la même page d’accueil de Sails.js.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Résoudre les problèmes de votre déploiement

Si votre application Sails.js échoue pour une raison quelconque dans le Service d’application, trouver les journaux stderr pour aider à le résoudre.
Pour plus d’informations, consultez [obtenir les journaux stdout et stderr à partir d’iisnode](app-service-web-nodejs-sails.md#iisnodelog).
S’il a démarré avec succès, le journal de stdout doit afficher le message bien connu :

                .-..-.

    Sails              <|    .-..-.
    v0.12.4             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

Vous pouvez contrôler la granularité des journaux stdout dans le fichier [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) . 

## <a name="connect-to-a-database-in-azure"></a>Se connecter à une base de données Azure

Pour vous connecter à une base de données Azure, vous créez la base de données de votre choix dans Azure, par exemple la base de données de SQL Azure, MySQL, MongoDB, Cache d’Azure (Redis), etc. et utilisez [l’adaptateur de magasin de données](https://github.com/balderdashy/sails#compatibility) correspondant à s’y connecter. Les étapes décrites dans cette section vous montrent comment se connecter à une base de données MySQL dans Azure.

1. Suivez le didacticiel [ici](../store-php-create-mysql-database.md) pour créer une base de données MySQL dans Azure.

2. À partir de votre terminal de ligne de commande, installez la carte de MySQL :

        npm install sails-mysql --save

3. Ouvrez config/connections.js et ajoutez l’objet de connexion suivantes à la liste : 

        mySql: {
            adapter: 'sails-mysql',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost, 
            database: process.env.dbname,
            options: {
                encrypt: true
            }
        },

4. Pour chaque variable d’environnement (`process.env.*`), vous devez définir dans le Service d’application. Pour ce faire, exécutez les commandes suivantes à partir de votre terminal. Toutes les informations de connexion dont vous avez besoin se trouvent dans le portail Azure (voir [se connecter à votre base de données MySQL](../store-php-create-mysql-database.md#connect)).

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbname="<database name>"
        
    Placer les paramètres dans les paramètres de l’application Azure conserve les données sensibles de votre contrôle de code source (Git). Ensuite, vous allez configurer votre environnement de développement pour utiliser les mêmes informations de connexion.

4. Ouvrez config/local.js et ajoutez l’objet connexions suivantes :

        connections: {
            mySql: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>", 
                database: "<database name>",
            },
        },
    
    Cette configuration remplace les paramètres dans votre fichier config/connections.js pour l’environnement local. Ce fichier est exclu par le .gitignore par défaut dans votre projet, donc il ne doit pas être stocké dans le Git. Vous êtes maintenant en mesure de se connecter à votre base de données MySQL, à la fois à partir de votre application web Azure et votre environnement de développement local.

4. Ouvrez config/env/production.js pour configurer votre environnement de production et ajoutez le code suivant `models` objet :

        models: {
            connection: 'mySql',
            migrate: 'safe'
        },

4. Ouvrez config/env/development.js pour configurer votre environnement de développement et ajoutez le code suivant `models` objet :

        models: {
            connection: 'mySql',
            migrate: 'alter'
        },

    `migrate: 'alter'`vous permet d’utiliser les fonctionnalités de migration de base de données pour créer et mettre à jour facilement de vos tables de base de données dans votre MySQL. Toutefois, `migrate: 'safe'` est utilisé pour votre environnement Azure (production), car Sails.js ne vous autorise pas à utiliser `migrate: 'alter'` dans un environnement de production (voir la  [Documentation de Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).

4. À partir du terminal, [Générer](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) une [API de plan](http://sailsjs.org/documentation/concepts/blueprints) Sails.js comme vous, puis exécuterait normalement `sails lift` pour créer la base de données de migration de base de données Sails.js. Par exemple :

         sails generate api mywidget
         sails lift

    Le `mywidget` modèle généré par cette commande est vide, mais nous pouvons l’utiliser pour montrer que nous possèdent une connectivité de base de données.
    Lorsque vous exécutez `sails lift`, il crée les tables manquantes pour les modèles de votre application utilise.

6. Accéder à l’API que vous venez de créer dans le navigateur de modèle. Par exemple :

        http://localhost:1337/mywidget/create
    
    L’API doit retourner l’entrée créée avec vous dans la fenêtre de navigateur, ce qui signifie que votre base de données est créé avec succès.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}

5. Maintenant, appuyez sur vos modifications vers Azure, et accédez à votre application pour vérifier qu’il fonctionne toujours.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Accéder à l’API de plan de votre application web Azure. Par exemple :

        http://<appname>.azurewebsites.net/mywidget/create

    Si l’API retourne une autre nouvelle entrée, puis votre application Azure web communique avec votre base de données MySQL.

## <a name="more-resources"></a>Plus de ressources

- [Mise en route avec les applications web Node.js dans le Service d’application Azure](app-service-web-nodejs-get-started.md)
- [À l’aide de Modules de Node.js avec des applications Azure](../nodejs-use-node-modules-azure-apps.md)
