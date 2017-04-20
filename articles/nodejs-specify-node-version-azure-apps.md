<properties
    pageTitle="Spécification d’une Version de Node.js"
    description="Découvrez comment spécifier la version du Node.js utilisés par les Sites Web Azure et les Services en nuage"
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Spécification d’une version de Node.js dans une application Azure

Lorsque vous hébergez une application Node.js, vous souhaiterez vous assurer que votre application utilise une version spécifique de Node.js. Il existe plusieurs manières d’effectuer cette opération pour les applications hébergées sur Azure.

##<a name="default-versions"></a>Versions par défaut

Les versions Node.js fournies par Azure sont constamment mis à jour. Sauf indication contraire, la version par défaut qui est spécifiée dans le `WEBSITE_NODE_DEFAULT_VERSION` variable d’environnement est utilisée. Pour substituer cette valeur par défaut, suivez les étapes décrites dans les sections suivantes de cet article

> [AZURE.NOTE] Si vous hébergez votre application dans un Service de Cloud Azure (rôle web ou de travail), et elle est la première fois que vous avez déployé l’application, Azure va tenter d’utiliser la même version de Node.js que vous avez installé sur votre environnement de développement s’il correspond à une des versions par défaut disponibles sur Azure.

##<a name="versioning-with-packagejson"></a>Versioning avec package.json

Vous pouvez spécifier la version de Node.js à utiliser en ajoutant le code suivant à votre fichier **package.json** :

    "engines":{"node":version}

Où *version* est le numéro de version spécifique à utiliser. Vous pouvez peut spécifier des conditions plus complexes de version, tel que :

    "engines":{"node": "0.6.22 || 0.8.x"}

Dans la mesure où 0.6.22 n’est pas une des versions disponibles dans l’environnement d’hébergement, la version la plus récente de la série qui est disponible est de 0,8 utilisé - 0.8.4.

##<a name="versioning-websites-with-app-settings"></a>Sites Web de versioning avec les paramètres de l’application
Si vous hébergez l’application d’un site Web, vous pouvez définir la variable d’environnement **WEBSITE_NODE_DEFAULT_VERSION** pour la version souhaitée. 

##<a name="versioning-cloud-services-with-powershell"></a>Services en nuage Versioning avec PowerShell

Si vous hébergez l’application dans un Service Cloud et déployez de l’application à l’aide de PowerShell d’Azure, vous pouvez substituer la version Node.js par défaut à l’aide de l’applet de commande PowerShell **Set-AzureServiceProjectRole** . Par exemple :

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Notez que les paramètres dans l’instruction ci-dessus sont sensibles à la casse.  Vous pouvez vérifier que la version correcte de Node.js a été sélectionnée en vérifiant la propriété **moteurs** dans **package.json de votre rôle**.

Vous pouvez également utiliser **AzureServiceProjectRoleRuntime-Get** pour extraire une liste des versions de Node.js disponibles pour les applications hébergées sous la forme d’un Service en nuage.  Vérifiez toujours la version de Node.js dépend de votre projet est dans cette liste.

##<a name="using-a-custom-version-with-azure-websites"></a>À l’aide d’une version personnalisée avec des sites Web Azure

Azure fournit plusieurs versions par défaut de Node.js, vous souhaiterez peut-être utiliser une version qui n’est pas fournie par défaut. Si votre application est hébergée comme un site Web d’Azure, vous pouvez le faire en utilisant le fichier **iisnode.yml** . Les étapes suivantes décrivent le processus de l’utilisation d’une version personnalisée de Node.Js avec un site Web d’Azure :

1. Créer un nouveau répertoire et puis créer un fichier **server.js** dans le répertoire. Le fichier **server.js** doit contenir les éléments suivants :

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    La version Node.js utilisée lorsque vous naviguez sur le site Web s’affiche.

2. Créer un nouveau site Web et notez le nom du site. Par exemple, le texte suivant utilise l' [Azure outils de ligne de commande] pour créer un site Web Azure nommé **monsiteweb**et puis activer un référentiel Git pour le site Web.

        azure site create mywebsite --git

3. Créez un nouveau répertoire nommé **bin** en tant qu’enfant du répertoire contenant le fichier **server.js** .

4. Télécharger la version spécifique de **node.exe** (version Windows) que vous souhaitez utiliser avec votre application. Par exemple, l’exemple suivant utilise **coin** pour télécharger la version 0.8.1 :

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Enregistrez le fichier **node.exe** dans le dossier **bin** créé précédemment.

5. Créer un fichier **iisnode.yml** dans le même répertoire que le fichier **server.js** , puis ajoutez le contenu suivant dans le fichier **iisnode.yml** :

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Ce chemin d’accès est où le fichier **node.exe** dans votre projet sera placé une fois que vous avez publié votre application pour le site Web d’Azure.

6. Publication de votre application. Par exemple, dans la mesure où j’ai créé un nouveau site Web avec le paramètre--git plus haut, les commandes suivantes ajouter les fichiers d’application vers mon référentiel Git local et puis les pousser vers le référentiel de site Web :

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Une fois que l’application a été publiée, ouvrez le site Web dans un navigateur. Vous devriez voir un message indiquant « Hello from Azure version de nœuds en cours d’exécution : v0.8.1 ».

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous comprenez comment spécifier la version du Node.js utilisé par votre application, de savoir comment [travailler avec les modules], [créer et déployer un Site Web de Node.js]et [comment utiliser les outils de ligne de commande Azure pour Mac et Linux].

Pour plus d’informations, consultez le [Centre pour développeurs Node.js](/develop/nodejs/).

[Comment utiliser les outils de ligne de commande de Azure pour Mac et Linux]: xplat-cli-install.md
[Outils de ligne de commande Azure]: xplat-cli-install.md
[travailler avec les modules]: nodejs-use-node-modules-azure-apps.md
[générer et déployer un Site Web de Node.js]: web-sites-nodejs-develop-deploy-mac.md
