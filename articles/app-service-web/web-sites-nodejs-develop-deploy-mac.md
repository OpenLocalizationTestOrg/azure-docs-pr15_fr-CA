<properties
    pageTitle="Créer une application web de Node.js dans le Service d’application Azure | Microsoft Azure"
    description="Découvrez comment déployer une application Node.js à une application web dans le Service d’application Azure."
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
    ms.topic="hero-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-web-app-in-azure-app-service"></a>Créer une application web de Node.js dans le Service d’application Azure

> [AZURE.SELECTOR]
- [.NET](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

Ce didacticiel explique comment créer une simple application de [Node.js](http://nodejs.org) et le déployer à l’aide de [Git](http://git-scm.com)à une [application web](app-service-web-overview.md) dans le [Service d’application Azure](../app-service/app-service-value-prop-what-is.md) . Vous pouvez suivre les instructions de ce didacticiel sur n’importe quel système d’exploitation qui est capable d’exécuter Node.js.

Vous apprendrez à :

* Comment créer une application web dans le Service d’application Azure via le portail d’Azure.
* Comment déployer une application Node.js à l’application web en poussant vers le référentiel Git de l’application web.

L’application terminée écrit une courte chaîne « hello world » dans le navigateur.

![Un navigateur affiche le message « Hello World ».][helloworld-completed]

Pour des didacticiels et des exemples de code avec des applications plus complexes Node.js, ou les autres rubriques relatives à l’utilisation de Node.js dans Azure, consultez le [Centre pour développeurs Node.js](/develop/nodejs/).

> [AZURE.NOTE]
> Pour terminer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [activer vos avantages d’abonné de Visual Studio](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) , ou [Inscrivez-vous pour un essai gratuit](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).
>
> Si vous souhaitez commencer avec le Service d’application Azure avant de vous inscrivez pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application : aucune carte de crédit requise et aucun des engagements.

## <a name="create-a-web-app-and-enable-git-publishing"></a>Créer une application web et activer la publication de Git

Suivez ces étapes pour créer une application web dans le Service d’application Azure et activer la publication de Git. 

[GIT](http://git-scm.com/) est un système de contrôle de version distribuée que vous pouvez utiliser pour déployer votre site Web d’Azure. Vous allez stocker le code que vous écrivez pour votre application web dans un référentiel Git local, et vous allez déployer votre code sur Azure en poussant vers un référentiel distant. Cette méthode de déploiement est une fonctionnalité de Service d’application web applications.  

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur l’icône **+ nouvelle** sur la partie supérieure gauche du portail Azure.

3. Cliquez sur **Web + Mobile**, puis cliquez sur **application Web**.

    ![][portal-quick-create]

4. Entrez un nom pour l’application web dans la zone de **l’application Web** .

    Ce nom doit être unique dans le domaine azurewebsites.net, car l’URL de l’application web sera {nom}. azurewebsites.net. Si le nom que vous entrez n’est pas unique, un point d’exclamation rouge s’affiche dans la zone de texte.

5. Sélectionnez un **abonnement**.

6. Sélectionnez un **Groupe de ressources** ou créez-en un nouveau.

    Pour plus d’informations sur les groupes de ressources, consultez [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

7. Sélectionnez un **Emplacement de plan de Service** d’application/ou créez-en un nouveau.

    Pour plus d’informations sur les plans de Service d’application, consultez [vue d’ensemble des plans de Service d’application Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

8. Cliquez sur **créer**.
   
    ![][portal-quick-create2]

    En un temps très court, généralement moins d’une minute, Azure termine la création de la nouvelle application web.

9. Cliquez sur **Web apps > {votre nouvelle application web}**.

    ![](./media/web-sites-nodejs-develop-deploy-mac/gotowebapp.png)

10. De la lame **d’application Web** , cliquez sur la partie du **déploiement** .

    ![][deployment-part]

11. De la lame de **DEPLOIEMENT continu** , cliquez sur **Choisir la Source**

12. Cliquez sur **Un référentiel Git Local**, puis cliquez sur **OK**.

    ![][setup-git-publishing]

13. Permet de paramétrer les informations d’identification du déploiement si vous ne l’avez pas déjà fait.

    une barre d’outils. De la lame d’application Web, cliquez sur **les paramètres > informations d’identification de déploiement**.

    ![][deployment-credentials]
 
    b. Créer un nom d’utilisateur et le mot de passe. 
    
    ![](./media/web-sites-nodejs-develop-deploy-mac/setdeploycreds.png)

14. De la lame d’application Web, cliquez sur **paramètres**, puis cliquez sur **Propriétés**.
 
    Pour publier, vous devez pousser vers un référentiel Git distant. L’URL pour le référentiel est répertorié sous **GIT URL**. Vous utiliserez cette URL plus loin dans le didacticiel.

    ![][git-url]

## <a name="build-and-test-your-application-locally"></a>Générer et tester votre application localement

Dans cette section, vous allez créer un fichier **server.js** qui contient une version légèrement modifiée de l’exemple « Hello World » à partir de [nodejs.org]. Le code ajoute process.env.PORT comme le port à l’écoute lors de l’exécution dans une application web Azure.

1. Créez un répertoire nommé *helloworld*.

2. Utilisez un éditeur de texte pour créer un nouveau fichier nommé **server.js** dans le répertoire *helloworld* .

2. Copiez le code suivant dans le fichier **server.js** , puis enregistrez le fichier :

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Ouvrez la ligne de commande et utilisez la commande suivante pour démarrer l’application web localement.

        node server.js

4. Ouvrez votre navigateur web et accédez à http://localhost:1337. 

    Une page Web qui affiche « Hello World » s’affiche, comme illustré dans la capture d’écran suivante.

    ![Un navigateur affiche le message « Hello World ».][helloworld-localhost]

## <a name="publish-your-application"></a>Publication de votre application.

1. Installez le Git si vous ne l’avez pas déjà fait.

    Pour obtenir des instructions d’installation pour votre plate-forme, consultez la [page de téléchargement de Git](http://git-scm.com/download).

1. À partir de la ligne de commande, accédez au répertoire **helloworld** et entrez la commande suivante pour initialiser un référentiel Git local.

        git init


2. Pour ajouter des fichiers dans le référentiel, utilisez les commandes suivantes :

        git add .
        git commit -m "initial commit"

3. Ajoutez un Git distant pour pousser des mises à jour pour l’application web que vous avez créée précédemment, à l’aide de la commande suivante :

        git remote add azure [URL for remote repository]

4. Envoi de vos modifications vers Azure à l’aide de la commande suivante :

        git push azure master

    Vous y êtes invité pour le mot de passe que vous avez créé précédemment. La sortie est similaire à l’exemple suivant.

        Counting objects: 3, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 374 bytes, done.
        Total 3 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id '5ebbe250c9'.
        remote: Preparing files for deployment.
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
         * [new branch]      master -> master

5. Pour afficher votre application, cliquez sur le bouton **Parcourir** sur la partie de **l’Application Web** du portail Azure.

    ![Bouton Parcourir](./media/web-sites-nodejs-develop-deploy-mac/browsebutton.png)

    ![Hello world dans Azure](./media/web-sites-nodejs-develop-deploy-mac/helloworldazure.png)

## <a name="publish-changes-to-your-application"></a>Publier les modifications apportées à votre application

1. Ouvrez le fichier **server.js** dans un éditeur de texte et modifier « Bonjour World\n » à « Bonjour Azure\n ». 

2. Enregistrez le fichier.

2. À partir de la ligne de commande, accédez au répertoire **helloworld** et exécutez les commandes suivantes :

        git add .
        git commit -m "changing to hello azure"
        git push azure master

    Vous êtes invité à nouveau votre mot de passe.

3. Actualiser la fenêtre du navigateur cible de la navigation vers les URL de l’application web.

    ![Une page web affichant « Hello Azure »][helloworld-completed]

## <a name="roll-back-a-deployment"></a>Restaurer un déploiement

À partir de la blade **d’application Web** vous pouvez cliquer sur **Paramètres > déploiement continue** pour consulter l’historique de déploiement dans la lame de **déploiements** . Si vous devez restaurer un précédent déploiement, vous pouvez sélectionner, puis **redéployer** dans la lame de **Détails du déploiement** .

## <a name="next-steps"></a>Étapes suivantes

Vous avez déployé une application Node.js à une application web dans le Service d’application Azure. Pour plus d’informations sur comment exécutent des applications de Node.js par les applications de Service d’application web, consultez [Azure Application Service Web Apps : Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx) et en [spécifiant une version Node.js dans une application Azure](../nodejs-specify-node-version-azure-apps.md).

Node.js fournit un riche écosystème de modules qui peuvent être utilisées par vos applications. Pour savoir comment les applications Web fonctionne avec les modules, consultez [Node.js d’à l’aide des modules avec les applications Azure](../nodejs-use-node-modules-azure-apps.md).

Si vous rencontrez des problèmes avec votre application après que qu’il a été déployé sur Azure, consultez [comment déboguer une application Node.js dans le Service d’application Azure](web-sites-nodejs-debug.md) pour plus d’informations sur le diagnostic du problème.

Cet article utilise le portail Azure pour créer une application web. Vous pouvez également utiliser [l’Interface de ligne de commande de Azure](../xplat-cli-install.md) ou [Azure PowerShell](../powershell-install-configure.md) pour effectuer les mêmes opérations.

Pour plus d’informations sur la façon de développer des applications Node.js sur Azure, consultez le [Centre pour développeurs Node.js](/develop/nodejs/).

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png
[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png
[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png
[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png
