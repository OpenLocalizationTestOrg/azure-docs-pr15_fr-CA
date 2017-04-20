<properties
    pageTitle="Node.js mise en route | Microsoft Azure"
    description="Découvrez comment créer une simple application de web Node.js et le déployer vers un service cloud Azure."
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
    ms.topic="hero-article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Générer et déployer une application Node.js à un Service de Cloud Azure

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

Ce didacticiel explique comment créer une simple application de Node.js s’exécutant dans un Service de Cloud Azure. Les Services en nuage sont les blocs de construction des applications cloud évolutives dans Azure. Ils permettent la séparation et gestion indépendante et échelle de front-end et back-end des composants de votre application.  Les Services en nuage fournissent une machine virtuelle de dédié robuste pour l’hébergement de chaque rôle de façon fiable.

Pour plus d’informations sur les Services en nuage, et comment ils comparent aux ordinateurs virtuels et de sites Web d’Azure, voir les [sites Web d’Azure, de Services en nuage et de comparaison des Machines virtuelles].

>[AZURE.TIP] Vous souhaitez pour créer un site Web simple ? Si votre scénario implique qu’un simple site Web frontal, envisagez [d’utiliser une application web léger]. Vous pouvez facilement mettre à niveau à un Service en nuage que votre application web s’agrandit et l’évolution de vos besoins.

En suivant ce didacticiel, vous allez créer une application web simple hébergée dans un rôle web. Vous allez utiliser l’émulateur de calcul pour tester votre application localement, puis le déployer à l’aide des outils de ligne de commande PowerShell.

L’application est une application simple « hello world » :

![Un navigateur affiche la page web de Hello World][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Conditions préalables

> [AZURE.NOTE] Ce didacticiel utilise PowerShell Azure, qui nécessite Windows.

- Installez et configurez [Azure Powershell].
- Téléchargez et installez le [SDK Azure pour .NET 2.7]. Dans les paramètres d’installation, sélectionnez :
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## <a name="create-an-azure-cloud-service-project"></a>Créer un projet de Service Cloud de Azure

Effectuer les tâches suivantes pour créer un nouveau projet Azure Cloud Service, ainsi que de la génération de modèles automatique base Node.js :

1. Exécution **de Windows PowerShell** en tant qu’administrateur ; à partir du **Menu Démarrer** ou **l’Écran de démarrage**, recherchez **Windows PowerShell**.

2. [PowerShell de se connecter] à votre abonnement.

3. Entrez l’applet de commande PowerShell suivante pour créer pour créer le projet :

        New-AzureServiceProject helloworld

    ![Le résultat de la commande helloworld de New-AzureService][The result of the New-AzureService helloworld command]

    L’applet de commande **New-AzureServiceProject** génère une structure de base pour la publication d’une application Node.js à un Service Cloud. Il contient des fichiers de configuration nécessaires pour la publication sur Azure. L’applet de commande modifie également le répertoire de travail dans le répertoire pour le service.

    L’applet de commande crée les fichiers suivants :

    -   **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** et **ServiceDefinition.csdef**: Azure spécifiques des fichiers nécessaires à la publication de votre application. Pour plus d’informations, consultez [vue d’ensemble de la création d’un Service hébergé pour Azure].

    -   **deploymentSettings.json**: stocke les paramètres locaux qui sont utilisées par les applets de commande PowerShell de Azure déploiement.

4.  Entrez la commande suivante pour ajouter un nouveau rôle web :

        Add-AzureNodeWebRole

    ![La sortie de la commande Add-AzureNodeWebRole][The output of the Add-AzureNodeWebRole command]

    L’applet de commande **Add-AzureNodeWebRole** crée une application de base Node.js. Elle modifie également les fichiers **.csfg** et **.csdef** pour ajouter des entrées de configuration pour le nouveau rôle.

    > [AZURE.NOTE] Si vous ne spécifiez pas un nom de rôle, un nom par défaut est utilisé. Vous pouvez fournir un nom comme premier paramètre de l’applet de commande :`Add-AzureNodeWebRole MyRole`

L’application Node.js est définie dans le fichier **server.js**, situé dans le répertoire pour le rôle web (**WebRole1** par défaut). Voici le code :

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Ce code est essentiellement identique à l’exemple « Hello World » sur le site Web [nodejs.org] , sauf qu’elle utilise le numéro de port affecté par l’environnement en nuage.

## <a name="deploy-the-application-to-azure"></a>Déployer l’application vers Azure

    [AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

### <a name="download-the-azure-publishing-settings"></a>Télécharger les paramètres de publication Azure

Pour déployer votre application dans Azure, vous devez d’abord télécharger les paramètres de publication pour votre abonnement Azure.

1.  Exécutez l’applet de commande PowerShell de Azure suivante :

        Get-AzurePublishSettingsFile

    Utiliser votre navigateur pour accéder à la page de téléchargement des paramètres de publication. Vous pouvez être invité à vous connecter avec un Account de Microsoft. Dans ce cas, utilisez le compte associé à votre abonnement Azure.

    Enregistrer le profil téléchargé à un emplacement de fichier que vous pouvez accéder facilement.

2.  Exécuter après l’applet de commande pour importer le profil de publication que vous avez téléchargé :

        Import-AzurePublishSettingsFile [path to file]


    > [AZURE.NOTE] Après avoir importé les paramètres de publication, envisagez de supprimer le fichier .publishSettings téléchargé, car il contient des informations qui pourraient permettre à un utilisateur pour accéder à votre compte.

### <a name="publish-the-application"></a>Publication de l’application

Pour publier, exécutez les commandes suivantes :

    $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

- **-ServiceName** Spécifie le nom pour le déploiement. Ce doit être un nom unique, sinon le processus de publication échoue. La commande **Get-Date** s’attache à une chaîne de date/heure qui doit rendre le nom unique.

- **-Emplacement** Spécifie que l’application sera hébergée dans le datacenter. Pour afficher une liste des centres de données disponible, utilisez l’applet de commande **Get-AzureLocation** .

- **-Lancement** ouvre une fenêtre de navigateur et navigue vers le service hébergé une fois le déploiement terminé.

Après le succès de la publication, vous verrez une réponse semblable à la suivante :

![La sortie de la commande Publier-AzureService][The output of the Publish-AzureService command]

> [AZURE.NOTE]
> Elle peut prendre plusieurs minutes pour que l’application à déployer et devient disponible lors de la première publication.

Une fois le déploiement terminé, une fenêtre de navigateur va ouvrir et accédez au service de cloud.

![Une fenêtre de navigateur affichant la page de monde hello ; l’URL indique que la page est hébergée sur Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Votre application est en cours d’exécution sur Azure.

L’applet de commande **Publier-AzureServiceProject** exécute les étapes suivantes :

1.  Crée un package de déploiement. Le package contient tous les fichiers dans votre dossier d’application.

2.  Crée un nouveau **compte de stockage** , s’il n’existe pas. Le compte de stockage Azure est utilisé pour stocker le package d’application au cours du déploiement. Vous pouvez supprimer en toute sécurité du compte de stockage après le déploiement.

3.  Crée un nouveau **service en nuage** , s’il n’existe pas déjà. Un **service en nuage** est le conteneur dans lequel votre application est hébergée, lorsqu’elle est déployée vers Azure. Pour plus d’informations, consultez [vue d’ensemble de la création d’un Service hébergé pour Azure].

4.  Publie le package de déploiement sur Azure.


## <a name="stopping-and-deleting-your-application"></a>Arrêt et suppression de votre application.

Après avoir déployé votre application, vous souhaiterez sans doute désactiver afin d’éviter des coûts supplémentaires. Échéances Azure web par heure de temps serveur consommé, les instances de rôles. Heure du serveur est consommée une fois que votre application est déployée, même si les instances ne sont pas en cours d’exécution et à l’état arrêté.

1.  Dans la fenêtre Windows PowerShell, arrêter le déploiement du service créé dans la section précédente avec l’applet de commande suivante :

        Stop-AzureService

    Arrêt du service peut prendre plusieurs minutes. Lorsque le service est arrêté, vous recevez un message indiquant qu’il s’est arrêté.

    ![Le statut de la commande Stop-AzureService][The status of the Stop-AzureService command]

2.  Pour supprimer le service, appeler l’applet de commande suivante :

        Remove-AzureService

    Lorsque vous y êtes invité, entrez **Y** pour supprimer le service.

    Suppression du service peut prendre plusieurs minutes. Une fois que le service a été supprimé, vous recevez un message indiquant que le service a été supprimé.

    ![Le statut de la commande Remove-AzureService][The status of the Remove-AzureService command]

    > [AZURE.NOTE] Suppression du service ne supprime pas le compte de stockage qui a été créé lorsque le service a été publié à l’origine, et vous continuerez à être facturé pour l’espace de stockage utilisé. Si rien d’autre n’utilise le stockage, vous souhaiterez probablement supprimer.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs Node.js].

<!-- URL List -->

[Comparaison de sites Web, les Services en nuage et les Machines virtuelles Azure]: ../app-service-web/choose-web-site-cloud-service-vm.md
[à l’aide d’une application web léger]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Powershell Azure]: ../powershell-install-configure.md
[Azure SDK pour .NET 2.7]: http://www.microsoft.com/en-us/download/details.aspx?id=48178
[Se connecter à PowerShell]: ../powershell-install-configure.md#how-to-connect-to-your-subscription
[nodejs.org]: http://nodejs.org/
[Vue d’ensemble de la création d’un Service hébergé pour Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Centre de développement Node.js]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
