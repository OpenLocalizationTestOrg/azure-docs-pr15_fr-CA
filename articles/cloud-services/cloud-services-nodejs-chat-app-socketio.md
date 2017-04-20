<properties 
    pageTitle="Application de Node.js à l’aide de Socket.io | Microsoft Azure" 
    description="Découvrez comment utiliser socket.io dans une application node.js hébergée sur Azure." 
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

# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Créer une Application de conversation de Node.js avec Socket.IO sur un Service Cloud Azure

Socket.IO assure une communication en temps réel entre les clients et votre serveur de node.js. Ce didacticiel vous aidera à l’hébergement d’un socket. E/s en fonction d’application de conversation sur Azure. Pour plus d’informations sur Socket.IO, consultez <http://socket.io/>.

Une capture d’écran de l’application terminée est inférieure à :

![Une fenêtre de navigateur affichant le service hébergé sur Azure][completed-app]  

## <a name="prerequisites"></a>Conditions préalables

S’assurer que les produits et versions suivants sont installées pour réussir l’exemple dans cet article :

* Installer [Visual Studio 2013](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Installer [Node.js](https://nodejs.org/download/)
* Installer les [Python version 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Créez un projet de Service Cloud

Les étapes suivantes créent le projet de service cloud qui hébergera l’application Socket.IO.

1. À partir du **Menu Démarrer** ou **l’Écran de démarrage**, recherchez **Windows PowerShell**. Enfin, **Windows PowerShell** d’avec le bouton droit et sélectionnez **Exécuter en tant qu’administrateur**.

    ![Icône de PowerShell Azure][powershell-menu]

2. Créez un répertoire appelé **c:\\nœud**. 
 
        PS C:\> md node

3. Changez les répertoires pour le **c:\\nœud** directory
 
        PS C:\> cd node

4. Entrez les commandes suivantes pour créer une nouvelle solution nommé **chatapp** et un rôle de collaborateur nommé **WorkerRole1**:

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    Vous verrez la réponse suivante :

    ![La sortie de la nouvelle-azureservice et d’azurenodeworkerrolecmdlets-ajouter](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Télécharger l’exemple de conversation

Pour ce projet, nous allons utiliser l’exemple de conversation à partir du [référentiel de Socket.IO GitHub]. Effectuez les opérations suivantes pour télécharger l’exemple et l’ajouter au projet que vous avez précédemment créé.

1.  Créer une copie locale du référentiel à l’aide du bouton de **Clone** . Vous pouvez également utiliser le bouton **ZIP** pour télécharger le projet.

    ![Une fenêtre de navigateur affichant des https://github.com/LearnBoost/socket.io/tree/master/examples/chat, avec l’icône de téléchargement ZIP mis en surbrillance][chat-example-view]

3.  Naviguer dans la structure de répertoires du référentiel local jusqu'à ce que vous arriviez à la **exemples\\chat** directory. Copiez le contenu de ce répertoire vers le **C:\\nœud\\chatapp\\WorkerRole1** répertoire créé précédemment.

    ![Explorateur de solutions, affichage du contenu des exemples\\répertoire extrait de l’archive][chat-contents]

    Les éléments en surbrillance dans la capture d’écran ci-dessus sont des fichiers copiés à partir de la **exemples\\chat** directory

4.  Dans la **C:\\nœud\\chatapp\\WorkerRole1** répertoire, supprimez le fichier **server.js** , puis renommez le fichier **app.js** en **server.js**. Cela supprime le fichier **server.js** par défaut créé précédemment par l’applet de commande **Add-AzureNodeWorkerRole** et le remplace par le fichier d’application à partir de l’exemple de conversation.

### <a name="modify-serverjs-and-install-modules"></a>Modifier Server.js et installer des Modules

Avant de tester l’application dans l’émulateur Azure, nous devons apporter de légères modifications. Effectuez les opérations suivantes dans le fichier server.js :

1.  Dans Visual Studio ou un éditeur de texte, ouvrez le fichier **server.js** .

2.  Recherchez la section de **dépendances de Module** au début de server.js et modifiez la ligne qui contient **sio = require('.. //.. lib//Socket.IO')** pour **sio = require('socket.io')** comme indiqué ci-dessous :

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  Pour vous assurer que l’application est à l’écoute sur le port approprié, ouvrez server.js dans le bloc-notes ou votre éditeur favori et puis modifiez la ligne suivante en remplaçant **3000** avec **process.env.port** comme indiqué ci-dessous :

        //app.listen(3000, function () {            //Original
        app.listen(process.env.port, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

Après avoir enregistré les modifications apportées à **server.js**, procédez comme suit pour installer les modules requis et puis de tester l’application dans l’émulateur Azure :

1.  À l’aide de **PowerShell d’Azure**, modifiez les répertoires pour le **C:\\nœud\\chatapp\\WorkerRole1** directory et utilisez la commande suivante pour installer les modules requis par cette application :

        PS C:\node\chatapp\WorkerRole1> npm install

    Ceci installera les modules répertoriés dans le fichier package.json. Une fois la commande terminée, vous devriez voir une sortie similaire à ce qui suit :

    ![Commande d’installation de la sortie de la npm][The-output-of-the-npm-install-command]

4.  Dans la mesure où cet exemple était à l’origine une partie du référentiel Socket.IO GitHub et la bibliothèque Socket.IO fait directement référence dans le chemin d’accès relatif, Socket.IO n’était pas référencé dans le fichier package.json, nous devons l’installer en exécutant la commande suivante :

        PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Test et déploiement

1.  Lancez l’émulateur en émettant la commande suivante :

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  Ouvrez un navigateur et accédez à **http://127.0.0.1**.

3.  Lorsque la fenêtre du navigateur s’ouvre, saisissez un surnom et puis appuyez sur ENTRÉE.
    Cela va vous pour publier des messages sous la forme d’un surnom spécifique. Pour tester la fonctionnalité multi-utilisateur, ouvrir d’autres fenêtres de navigateur à l’aide de la même URL et entrez plusieurs surnoms.

    ![Affichage des messages de conversation de User1 et User2 deux fenêtres de navigateur](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  Après le test de l’application, arrêter l’émulateur en émettant la commande suivante :

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Pour déployer l’application vers Azure, utilisez l’applet de commande **Publier-AzureServiceProject** . Par exemple :

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    > [AZURE.IMPORTANT] Veillez à utiliser un nom unique, sinon le processus de publication échoue. Une fois le déploiement terminé, le navigateur ouvrir et naviguez jusqu’au service déployé.
    > 
    > Si vous recevez un message d’erreur indiquant que le nom d’abonnement fourni n’existe pas dans le profil de publication importés, vous devez télécharger et importer le profil de publication pour votre abonnement avant de le déployer sur Azure. Reportez-vous à la section **déploiement de l’Application dans Azure** , de [créer et de déployer une application Node.js à un Service de Cloud Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

    ![Une fenêtre de navigateur affichant le service hébergé sur Azure][completed-app]

    > [AZURE.NOTE] Si vous recevez un message d’erreur indiquant que le nom d’abonnement fourni n’existe pas dans le profil de publication importés, vous devez télécharger et importer le profil de publication pour votre abonnement avant de le déployer sur Azure. Reportez-vous à la section **déploiement de l’Application dans Azure** , de [créer et de déployer une application Node.js à un Service de Cloud Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

Votre application est en cours d’exécution sur Azure et peut relayer des messages de conversation entre les différents clients à l’aide de Socket.IO.

> [AZURE.NOTE] Pour plus de simplicité, cet exemple est limité à la conversation entre les utilisateurs connectés à la même instance. Cela signifie que si le service en nuage crée deux instances de rôle de travail, les utilisateurs seulement pourront discuter avec d’autres utilisateurs connectés à la même instance de rôle de travail. Pour faire évoluer l’application de fonctionner avec plusieurs instances de rôle, vous pouvez utiliser une technologie de Bus de Service de partager l’état du magasin Socket.IO entre les instances. Pour obtenir des exemples, consultez les exemples de l’utilisation de files d’attente de Bus de Service et les rubriques dans [Azure SDK pour Node.js GitHub référentiel](https://github.com/WindowsAzure/azure-sdk-for-node).

##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment créer une application de base de conversation hébergée dans un Service de Cloud Azure. Pour savoir comment héberger cette application dans un site Web d’Azure, consultez [créer une Application de conversation Node.js avec Socket.IO sur un Site Web de Azure][chatwebsite].

Pour plus d’informations, consultez également le [Centre pour développeurs Node.js](/develop/nodejs/).

  [chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://www.windowsazure.com/support/sla/
  [Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
  [Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Référentiel de Socket.IO GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure Considerations]: #windowsazureconsiderations
  [Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
  [Summary and Next Steps]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  
 
