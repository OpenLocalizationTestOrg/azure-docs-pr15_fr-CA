<properties
    pageTitle="App Node.js API dans le Service d’application Azure | Microsoft Azure"
    description="Apprenez à créer une API RESTful Node.js et le déployer sur une application API dans le Service d’application Azure."
    services="app-service\api"
    documentationCenter="node"
    authors="bradygaster"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="node"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="rachelap"/>

# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Générer une API RESTful Node.js et le déployer pour une application API dans Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Ce didacticiel explique comment créer un simple [Node.js](http://nodejs.org) API et le déployer à l’aide de [Git](http://git-scm.com)à une [application d’API](app-service-api-apps-why-best-platform.md) dans le [Service d’application Azure](../app-service/app-service-value-prop-what-is.md) . Vous pouvez utiliser n’importe quel système d’exploitation pouvant exécuter Node.js, et vous devez faire tout votre travail à l’aide des outils de ligne de commande tels que cmd.exe ou bash.

## <a name="prerequisites"></a>Conditions préalables

1. Compte Microsoft Azure ([Ouvrir un compte gratuitement ici](https://azure.microsoft.com/pricing/free-trial/))
1. [Node.js](http://nodejs.org) installé (cet exemple suppose que vous avez Node.js version 4.2.2)
2. [Git](https://git-scm.com/) installé
1. Compte de [GitHub](https://github.com/)

Service d’application prend en charge les nombreuses façons de déployer votre code pour une application API, ce didacticiel illustre la méthode Git et suppose que vous disposez des connaissances de base de l’utilisation de Git. Pour plus d’informations sur les autres méthodes de déploiement, consultez [déploiement de votre application au Service d’application Azure](../app-service-web/web-sites-deploy.md).

## <a name="get-the-sample-code"></a>Obtenir le code d’exemple

1. Ouvrez une interface de ligne de commande qui peut exécuter les commandes Node.js et Git.

1. Accédez à un dossier que vous pouvez utiliser un Git référentiel local, et un clone du [référentiel GitHub contenant l’exemple de code](https://github.com/Azure-Samples/app-service-api-node-contact-list).

        git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git

    L’API de l’exemple fournit deux points de terminaison : une demande Get pour `/contacts` renvoie une liste de noms et d’adresses électroniques au format JSON, tandis que `/contacts/{id}` renvoie uniquement le contact sélectionné.

## <a name="scaffold-auto-generate-nodejs-code-based-on-swagger-metadata"></a>ECHAFAUDAGE (générer automatiquement) Node.js code basés sur les métadonnées de Swagger

[Swagger](http://swagger.io/) est un format de fichier de métadonnées qui décrivent une API RESTful. Service d’application Azure a [prise en charge intégrée pour les métadonnées de Swagger](app-service-api-metadata.md). Cette section du didacticiel Modélise un processus de développement d’API dans lequel vous créez d’abord les métadonnées Swagger et l’utilisez pour scaffold (générer automatiquement) code de serveur pour l’API. 

>[AZURE.NOTE] Vous pouvez ignorer cette section si vous ne souhaitez pas savoir comment ECHAFAUDAGE code Node.js à partir d’un fichier de métadonnées de Swagger. Si vous souhaitez simplement déployer les exemples de code pour une nouvelle application API, passez directement à la section [Création d’une application API dans Azure](#createapiapp) .

### <a name="install-and-execute-swaggerize"></a>Installer et exécuter Swaggerize

1. Exécutez les commandes suivantes pour installer les modules NPM **yo** et **swaggerize-générateur** globalement.

        npm install -g yo
        npm install -g generator-swaggerize

    Swaggerize est un outil qui génère le code de serveur pour une API décrite par un fichier de métadonnées de Swagger. Le fichier Swagger que vous utiliserez est nommé *api.json* et se trouve dans le dossier *démarrage* du référentiel que vous le cloné.

2. Naviguez vers le dossier de *démarrage* , puis exécutez le `yo swaggerize` commande. Swaggerize de se poser une série de questions.  Pour **nom de ce projet**, entrez « ContactList », pour le **chemin d’accès au document de swagger**, « api.json » et **Express, Joyeux, ou Restify**, entrez « express ».

        yo swaggerize

    ![Swaggerize de ligne de commande](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
    **Remarque**: Si vous rencontrez une erreur dans cette étape, l’étape suivante explique comment résoudre le problème.

    Swaggerize crée un dossier de l’application, les gestionnaires d’échafaudages et les fichiers de configuration et génère un fichier **package.json** . Le moteur d’affichage express est utilisé pour générer la page d’aide Swagger.  

3. Si le `swaggerize` Échec de la commande avec un « jeton inattendu » ou une « séquence d’échappement non valide », corrigez la cause de l’erreur en modifiant le fichier généré *package.json* . Dans le `regenerate` sous `scripts`, modifier la barre oblique inverse qui précède *api.json* pour une barre oblique, de sorte que la ligne ressemble à l’exemple suivant :

        "regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"

1. Accédez au dossier qui contient le code avec génération de modèles (dans ce cas, le sous-dossier */start/ContactList* ).

1. Exécutez `npm install`.
    
        npm install
        
2. Installez le module NPM **jsonpath** . 

        npm install --save jsonpath
        
    ![Installation de Jsonpath](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Installez le module NPM **swaggerize-ui** . 

        npm install --save swaggerize-ui
        
    ![Installation de l’interface utilisateur de swaggerize](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

### <a name="customize-the-scaffolded-code"></a>Personnaliser le code avec génération de modèles

1. Copier le dossier des **lib** à partir du dossier **démarrage** dans le dossier **ContactList** créé par le scaffolder. 

1. Remplacez le code dans le fichier **handlers/contacts.js** par le code suivant. 

    Ce code utilise les données JSON stockées dans le fichier **lib/contacts.json** qui est pris en charge par **lib/contactRepository.js**. Le nouveau code contacts.js répond aux requêtes HTTP pour obtenir tous les contacts et les renvoyer en tant que charge utile JSON. 

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Remplacez le code dans le fichier **handlers/contacts/{id}.js** avec le code fofllowing. 

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Remplacez le code dans **server.js** par le code suivant. 

    Les modifications apportées au fichier server.js sont appelées à l’aide des commentaires afin de voir les modifications apportées. 

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth and final change
        });

### <a name="test-with-the-api-running-locally"></a>Effectuez un test avec l’API s’exécutant localement

1. Activer le serveur à l’aide de l’exécutable de ligne de commande Node.js. 

        node server.js

1. Lorsque vous naviguez vers **http://localhost:8000/contacts**, vous consultez le résultat JSON de la liste de contacts (ou si vous êtes invité à le télécharger, en fonction de votre navigateur). 

    ![Tous les appels d’Api de Contacts](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Lorsque vous naviguez vers **http://localhost:8000/contacts/2**, vous verrez le représenté par la valeur de l’id de contact.

    ![Appel de l’Api de Contact spécifiques](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Les données JSON de Swagger sont pris en charge par le point de **terminaison/swagger** :

    ![Contacts Swagger Json](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. L’interface utilisateur de Swagger est pris en charge par le point de terminaison **/docs** . Dans l’interface utilisateur de Swagger, vous pouvez utiliser les fonctionnalités de client HTML enrichies pour tester votre API.

    ![Interface utilisateur de swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a>Créer une nouvelle application API

Dans cette section, vous utilisez le portail Azure pour créer une nouvelle application API dans Azure. Cette application API représente les ressources de calcul Azure fournit pour exécuter votre code. Dans les sections suivantes, vous allez déployer votre code à la nouvelle application API.

1. Accédez au [portail Azure](https://portal.azure.com/). 

1. Cliquez sur **Nouveau > Web + Mobile > App de l’API**. 

    ![Nouvelle API application de portail](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

4. Entrez un **nom** qui est unique dans le domaine *azurewebsites.net* , tels que des NodejsAPIApp plus un numéro pour le rendre unique. 

    Par exemple, si le nom est `NodejsAPIApp`, l’URL sera `nodejsapiapp.azurewebsites.net`.

    Si vous entrez un nom de quelqu'un d’autre a déjà utilisé, vous voyez un point d’exclamation rouge à droite.

6. Dans la liste déroulante **Groupe de ressources** , cliquez sur **Nouveau**et dans le **nom du nouveau groupe de ressource** Entrez « NodejsAPIAppGroup » ou un autre nom si vous le souhaitez. 

    Un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) est un ensemble de ressources Azure API applications, bases de données, des ordinateurs virtuels. Pour ce didacticiel, il est préférable de créer un nouveau groupe de ressources, car cela facilite à supprimer en une seule étape toutes les ressources Azure que vous créez pour le didacticiel.

4. Cliquez sur **Plan/emplacement du Service de l’application**, puis cliquez sur **Créer un nouveau**.

    ![Créer le plan de Service d’application](./media/app-service-api-nodejs-api-app/newappserviceplan.png)

    Dans les étapes suivantes, vous créez un plan de Service d’application pour le nouveau groupe de ressources. Un plan de Service de l’application spécifie les ressources de calcul fonctionnant sous votre application API. Par exemple, si vous choisissez la couche libre, votre application API s’exécute sur VMs partagés, alors que pour certains niveaux payées Il s’exécute sur les ordinateurs virtuels dédiés. Pour plus d’informations sur les plans de Service d’application, consultez [vue d’ensemble des plans de Service d’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. De la lame de **Plan de Service d’application** , entrez « NodejsAPIAppPlan » ou un autre nom si vous le souhaitez.

5. Dans la liste déroulante **emplacement** , sélectionnez l’emplacement le plus proche de vous.

    Ce paramètre spécifie le centre de données Azure votre application s’exécutera dans. Pour ce didacticiel, vous pouvez sélectionner n’importe quelle région et il ne faire la différence notable. Mais, pour une application de production, vous souhaitez que votre serveur soit aussi proche que possible pour les clients qui sont accèdent pour réduire la [latence](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Cliquez sur **les niveaux de tarification > Afficher tout > F1 libre**.

    Pour ce didacticiel, le niveau de tarification libre offre des performances suffisantes.

    ![Sélectionnez libre, niveau de tarification](./media/app-service-api-nodejs-api-app/selectfreetier.png)

6. Dans la lame **Plan de Service d’application** , cliquez sur **OK**.

7. Dans la lame **API App** , cliquez sur **créer**.

## <a name="set-up-your-new-api-app-for-git-deployment"></a>Configurez votre nouvelle application API pour le déploiement de Git

Vous allez déployer votre code à l’application de l’API en poussant les validations vers un référentiel Git dans le Service d’application Azure. Dans cette section du didacticiel, vous créez les informations d’identification et le référentiel Git dans Azure que vous utiliserez pour le déploiement.  

1. Une fois votre application API a été créée, cliquez sur **les Services d’application > {votre application API}** à partir de la page d’accueil de portail. 

    Le portail affiche des pales de **l’API application** et les **paramètres** .

    ![Portail API app et lame de paramètres](media/app-service-api-nodejs-api-app/portalapiappblade.png)

1. De la lame de **paramètres** , faites défiler jusqu'à la section **publication** , puis cliquez sur **informations d’identification de déploiement**.
 
3. De la lame de **définir les informations d’identification de déploiement** , permet d’entrer un nom d’utilisateur et le mot de passe, puis cliquez sur **Enregistrer**.

    Vous utiliserez ces informations d’identification pour la publication de votre code Node.js à votre application d’API. 

    ![Informations d’identification de déploiement](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. Dans la lame de **paramètres** , cliquez sur **source de déploiement > choisir la Source > Local Git référentiel**, puis cliquez sur **OK**.

    ![Créer mis en pension de Git](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Une fois les modifications de la lame pour vous montrer vos déploiements actives a été créé à votre référentiel Git. Le référentiel étant nouveau, vous n’avez aucun déploiement actif dans la liste. 

    ![Aucun déploiement actif](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. Copiez l’URL de référentiel Git. Pour ce faire, accédez à la lame pour votre nouvelle application API et examinez la section **Essentials** de la lame. Notez l' **URL du clone Git** dans la section **Essentials** . Lorsque vous placez le curseur sur cette URL, vous voyez une icône sur la droite qui copie l’URL dans le Presse-papiers. Cliquez sur cette icône pour copier l’URL.

    ![Obtenir l’Url Git à partir du portail](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Remarque**: vous devez le clone Git URL dans la section suivante Assurez-vous donc enregistrer quelque part pour le moment.

Maintenant que vous avez une application API grâce à un référentiel Git sa sauvegarde, vous pouvez distribuer le code dans le référentiel pour déployer le code à l’application de l’API. 

## <a name="deploy-your-api-code-to-azure"></a>Déployer votre code API vers Azure

Dans cette section, vous créez un référentiel Git local qui contient votre code de serveur pour l’API, et puis vous poussez votre code à partir de ce référentiel dans le référentiel dans Azure que vous avez créé précédemment.

1. Copier le `ContactList` dossier dans un emplacement que vous pouvez utiliser pour un nouveau référentiel Git local. Si vous avez effectué la première partie du didacticiel, copiez `ContactList` de la `start` dossier ; Sinon, copiez `ContactList` de la `end` dossier.

1. Dans votre outil de ligne de commande, accédez au nouveau dossier, puis exécutez la commande suivante pour créer un nouveau référentiel Git local. 

        git init

     ![Nouveau Local mis en pension de Git](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Exécutez la commande suivante pour ajouter un Git distant pour le référentiel de l’application de votre API. 

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Remarque**: Remplacez la chaîne « YOUR_GIT_CLONE_URL_HERE » avec votre propre URL Git clone que vous avez copié précédemment. 

1. Exécutez les commandes suivantes pour créer une validation qui contient l’ensemble de votre code. 

        git add .
        git commit -m "initial revision"

    ![Résultat de la validation de GIT](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Exécuter la commande pour pousser votre code vers Azure. Lorsque vous êtes invité à taper un mot de passe, entrez celui que vous avez créé précédemment dans le portail Azure.

        git push azure master

    Cela déclenche un déploiement pour votre application d’API.  

1. Dans votre navigateur, revenir à la lame de **déploiement** pour votre application d’API, et vous voyez que le déploiement est en cours. 

    ![Déploiement qui passe](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Simultanément, l’interface de ligne de commande reflète l’état de votre déploiement durant ce temps. 

    ![Problème de déploiement de nœud Js](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

    Une fois le déploiement terminé, la lame de **déploiements** reflète le déploiement réussi de vos modifications du code à votre application d’API. 

## <a name="test-with-the-api-running-in-azure"></a>Effectuez un test avec l’API exécute dans Azure
 
3. Copiez l' **URL** dans la section de **l’éducation** de la blade d’API App. 

    ![Déploiement terminé](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. À l’aide d’un client de l’API REST Postman ou Fiddler (ou votre navigateur web), de fournir l’URL de vos contacts appel d’API, qui est la `/contacts` point de terminaison de votre application d’API. L’URL sera`https://{your API app name}.azurewebsites.net/contacts`

    Lorsque vous émettez une requête GET à ce point de terminaison, vous obtenez la sortie JSON de votre application d’API.

    ![Postman en appuyant sur les Api](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

2. Dans un navigateur, accédez à la `/docs` point de terminaison pour tester l’interface utilisateur de Swagger qu’il s’exécute dans Azure.

Maintenant que vous avez associé de livraison continue, vous pouvez apporter des modifications de code et les déployer sur Azure simplement en poussant les validations vers votre référentiel Azure Git.

## <a name="next-steps"></a>Étapes suivantes

À ce stade, vous avez correctement créé une application API et déployé code Node.js API. L’affiche de didacticiel suivant comment [utiliser les API des applications à partir de clients de JavaScript, à l’aide de CORS](app-service-api-cors-consume-javascript.md).
