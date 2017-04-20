<properties 
    pageTitle="Générer et déployer une application web de Node.js vers Azure à l’aide de WebMatrix" 
    description="Un didacticiel qui vous explique comment utiliser des WebMatrix pour développer une application Node.js et le déployer vers Azure Application Service Web Apps." 
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


# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>Générer et déployer une application web de Node.js vers Azure à l’aide de WebMatrix

Ce didacticiel vous montre comment utiliser de WebMatrix pour développer une application Node.js et le déployer vers [Azure Application Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. WebMatrix est un outil de développement web gratuit de Microsoft qui inclut tout ce dont vous avez besoin pour le développement d’applications web ou site Web. WebMatrix inclut plusieurs fonctionnalités qui le rendent Node.js facile à utiliser, y compris de la complétion de code, des modèles prédéfinis et prise en charge de l’éditeur de Jade, moins et CoffeeScript. Pour en savoir plus à propos de [WebMatrix](https://www.microsoft.com/web/webmatrix/).

À la fin de ce guide, vous disposerez d’une application web de Node.js en cours d’exécution dans le Service d’application Azure.
 
Une capture d’écran de l’application terminée est inférieure à :

![Site Web de nœud Azure][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="sign-into-azure"></a>Signe dans Azure

Suivez ces étapes pour créer une application web dans le Service d’application Azure.

1. Lancement de WebMatrix
2. Si c’est la première fois que vous avez utilisé WebMatrix, vous serez invité à signer dans Azure.  Vous pouvez dans le cas contraire, cliquez sur le bouton **Connexion** et cliquez sur **Ajouter un compte**.  Sélectionnez cette option pour **vous connecter** à l’aide de votre Account de Microsoft.

    ![Ajouter compte][addaccount]

3. Si vous êtes inscrit à un compte Azure, vous pourrez vous connecter à l’aide de votre Account de Microsoft :

    ![Signe dans Azure][signin]  


## <a name="create-a-site-using-a-built-in-template-for-azure"></a>Créer un site à l’aide d’un modèle intégré pour Azure

1. Sur l’écran de démarrage, cliquez sur le bouton **Nouveau** et cliquez sur pour créer un nouveau site à partir de la galerie de modèles de la **Galerie de modèles** :

    ![Nouveau site à partir de la galerie de modèles][sitefromtemplate]

2. Dans la boîte de dialogue de **Site à partir du modèle** , sélectionnez le **nœud** , puis **Site Express**. Enfin, cliquez sur **suivant**. S’il vous manque des conditions requises pour le modèle de **Site Express** , vous êtes invité à les installer.

    ![Sélectionnez le modèle express][webmatrix-templates]

3. Si vous êtes connecté dans Azure, vous avez maintenant la possibilité de créer une application web de Service d’application de votre site local.  Choisissez un nom unique et sélectionnez le centre de données où vous souhaitez que votre application de web Service de l’application à créer : 

    ![Créer un site sur Azure][nodesitefromtemplateazure]
    
4. WebMatrix après création du site local et la création de l’application web de Service de l’application, l’IDE WebMatrix est affiché.

    ![WebMatrix ide][webmatrix-ide]

##<a name="publish-your-application-to-azure"></a>Publiez votre application vers Azure

1. Dans WebMatrix, cliquez sur **Publier** à partir du ruban **accueil** pour afficher la boîte de dialogue **Aperçu avant publication** pour le site.

    ![Aperçu de la publication][webmatrix-node-publishpreview]

2. Cliquez sur **Continuer**. Lorsque la publication est terminée, l’URL de l’application web de Service de l’application est affichée en bas de l’IDE de WebMatrix

    ![publication terminée][webmatrix-publish-complete]

3. Cliquez sur le lien pour ouvrir l’application web de Service de l’application dans votre navigateur.

    ![Application web Express][webmatrix-node-express-site]

##<a name="modify-and-republish-your-application"></a>Modifier et republier votre application

Vous pouvez facilement modifier et republier votre application. Ici, vous pouvez apporter une simple Remplacez le titre dans le fichier **index.jade** , puis republier l’application.

1. Dans WebMatrix, sélectionnez **fichiers**, puis développez le dossier **views** . Ouvrez le fichier **index.jade** en double-cliquant dessus.

    ![WebMatrix affichage index.jade][webmatrix-modify-index]

2. Modifiez la ligne de paragraphe à la suivante :

        p Welcome to #{title} with WebMatrix on Azure!

3. Enregistrez vos modifications, puis cliquez sur l’icône publier. Enfin, cliquez sur **Continuer** dans la boîte de dialogue **Aperçu avant publication** et attendez que la mise à jour à publier.

    ![Aperçu de la publication][webmatrix-republish]

4. Lorsque la publication est terminée, cliquez sur le lien renvoyé lorsque le processus de publication est terminé pour voir l’application web de Service d’application mis à jour.

    ![Nœud Azure web app][webmatrix-node-completed]

##<a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les versions de Node.js fournis avec Azure et comment spécifier la version à utiliser avec votre application, consultez [spécification d’une version Node.js dans une application Azure](../nodejs-specify-node-version-azure-apps.md).

Si vous rencontrez des problèmes avec votre application après que qu’il a été déployé sur Azure, consultez [comment déboguer une application web de Node.js dans le Service d’application Azure](web-sites-nodejs-debug.md) pour plus d’informations sur le diagnostic du problème.

## <a name="whats-changed"></a>Ce qui a changé
* Pour obtenir un guide pour la modification de sites Web au Service de l’application voir : [Service d’application Azure et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
 