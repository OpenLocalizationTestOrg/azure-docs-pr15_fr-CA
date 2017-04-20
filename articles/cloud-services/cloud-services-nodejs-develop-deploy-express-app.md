<properties 
    pageTitle="Web application avec Express (Node.js) | Microsoft Azure" 
    description="Didacticiel qui s’appuie sur le didacticiel service de cloud et montre comment utiliser le module Express." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>






# <a name="build-a-nodejs-web-application-using-express-on-an-azure-cloud-service"></a>Créer une application web de Node.js à l’aide de Express sur un Service en nuage Azure

Node.js inclut un ensemble minimal de fonctionnalités dans le runtime de base.
Les développeurs utilisent souvent les 3 modules de tiers pour fournir des fonctionnalités supplémentaires lorsque vous développez une application Node.js. Dans ce didacticiel, vous allez créer une nouvelle application à l’aide du module [Express][] , qui fournit une infrastructure MVC pour créer des applications web Node.js.

Une capture d’écran de l’application terminée est inférieure à :

![Un navigateur web affichant la bienvenue à Express dans Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##<a name="create-a-cloud-service-project"></a>Créez un projet de Service Cloud

Effectuez les étapes suivantes pour créer un nouveau projet de service cloud nommé 'expressapp' :

1. À partir du **Menu Démarrer** ou **l’Écran de démarrage**, recherchez **Windows PowerShell**. Enfin, **Windows PowerShell** d’avec le bouton droit et sélectionnez **Exécuter en tant qu’administrateur**.

    ![Icône de PowerShell Azure](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

    [AZURE.INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

2. Changez les répertoires pour le **c:\\nœud** directory et entrez les commandes suivantes pour créer une nouvelle solution nommé **expressapp** et un rôle web nommé **WebRole1**:

        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21

    > [AZURE.NOTE] Par défaut, **Ajouter-AzureNodeWebRole** utilise une ancienne version de Node.js. L’instruction **Set-AzureServiceProjectRole** ci-dessus indique à Azure à utiliser v0.10.21 de nœud.  Notez que les paramètres respectent la casse.  Vous pouvez vérifier que la version correcte de Node.js a été sélectionnée en vérifiant la propriété **moteurs** dans **WebRole1\package.json**.

##<a name="install-express"></a>Installation Express

1. Installer le générateur Express en émettant la commande suivante :

        PS C:\node\expressapp> npm install express-generator -g

    La sortie de la commande npm doit ressembler le résultat ci-dessous. 

    ![Commande express d’installation de Windows PowerShell affichant la sortie de la npm.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Changez les répertoires pour le répertoire **WebRole1** et utilisez la commande expresse pour générer une nouvelle application :

        PS C:\node\expressapp\WebRole1> express

    Vous devrez remplacer votre application antérieure. Saisissez **y** ou sur **Oui** pour continuer. Express génère le fichier app.js et une structure de dossiers pour la création de votre application.

    ![La sortie de la commande expresse](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  Pour installer des dépendances supplémentaires définis dans le fichier package.json, tapez la commande suivante :

        PS C:\node\expressapp\WebRole1> npm install

    ![Commande d’installation de la sortie de la npm](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  Utilisez la commande suivante pour copier le fichier **bin/www** à **server.js**. C’est pour le service en nuage puisse trouver le point d’entrée pour cette application.

        PS C:\node\expressapp\WebRole1> copy bin/www server.js

    Une fois cette commande exécutée, vous devez avoir un fichier **server.js** dans le répertoire WebRole1.

7.  Modifier la **server.js** pour supprimer une du '.' caractères à partir de la ligne suivante.

        var app = require('../app');

    Après avoir apporté cette modification, la ligne doit apparaître comme suit.

        var app = require('./app');

    Cette modification est nécessaire dans la mesure où nous avons déplacé le fichier (anciennement **bin/www**) dans le même répertoire que le fichier d’application soit nécessaire. Après avoir apporté cette modification, enregistrez le fichier **server.js** .

8.  Utilisez la commande suivante pour exécuter l’application dans l’émulateur Azure :

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

    ![Une page web contenant la bienvenue express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modification de l’affichage

Maintenant modifier l’affichage pour afficher le message « Bienvenue sur Express dans Azure ».

1.  Entrez la commande suivante pour ouvrir le fichier index.jade :

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![Le contenu du fichier index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade est le moteur d’affichage par défaut utilisé par les applications d’Express. Pour plus d’informations sur le moteur d’affichage Jade, consultez [http://jade-lang.com][].

2.  Modifier la dernière ligne de texte en ajoutant **dans Azure**.

    ![Le fichier index.jade, lit de la dernière ligne : Bienvenue dans p \#{titre} dans Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Enregistrez le fichier et quittez le bloc-notes.

4.  Actualisez votre navigateur et vous verrez vos modifications.

    ![Une fenêtre de navigateur, la page contient Bienvenue Express dans Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Après le test de l’application, utilisez l’applet de commande **Stop-AzureEmulator** pour arrêter l’émulateur.

##<a name="publishing-the-application-to-azure"></a>Publication de l’Application vers Azure

Dans la fenêtre PowerShell d’Azure, utilisez l’applet de commande **Publier-AzureServiceProject** pour déployer l’application à un service cloud

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Une fois l’opération de déploiement terminée, votre navigateur va ouvrir et afficher la page web.

![Un navigateur web affichant la page Express. L’URL indique que celle-ci est maintenant hébergée sur Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs Node.js](/develop/nodejs/).

  [Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://Jade-lang.com]: http://jade-lang.com

 
