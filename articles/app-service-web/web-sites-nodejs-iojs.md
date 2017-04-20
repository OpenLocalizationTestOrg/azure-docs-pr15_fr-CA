<properties 
    pageTitle="L’utilisation de io.js avec Azure Application Service Web Apps" 
    description="Découvrez comment utiliser une application web dans le Service d’application Azure avec io.js." 
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
    ms.author="robmcm" />

# <a name="how-to-use-iojs-with-azure-app-service-web-apps"></a>L’utilisation de io.js avec Azure Application Service Web Apps

branche de populaires nœud [io.js] propose plusieurs différences au projet de Node.js de Joyent, y compris un modèle de gouvernance plus ouvert, un cycle de publication plus rapide et une adoption plus rapide de nouvelles fonctionnalités JavaScript expérimentales.

Alors que [Azure Application Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps a de nombreuses versions Node.js préinstallées, il permet également d’un binaire Node.js fourni par l’utilisateur. Cet article décrit deux méthodes permettant l’utilisation de la io.js sur l’application de Service Web Apps : l’utilisation d’un script de déploiement étendu, qui configure automatiquement Azure pour utiliser la dernière version disponible io.js, ainsi que le transfert manuel d’un io.js binaire. 

<a id="deploymentscript"></a>
## <a name="using-a-deployment-script"></a>À l’aide d’un Script de déploiement

Lors du déploiement d’une application de Node.js, application Service Web Apps s’exécute un certain nombre de petites commandes pour vous assurer que l’environnement est correctement configuré. À l’aide d’un script de déploiement, ce processus peut être personnalisé pour inclure le téléchargement et la configuration de io.js.

[Script de déploiement de io.js](https://github.com/felixrieseberg/iojs-azure) est disponible sur GitHub. Pour activer io.js sur votre application web, il suffit de copier **.deployment**, **deploy.cmd** et **IISNode.yml** à la racine de votre dossier d’application et déployer des applications Web.  

Le premier fichier, **.deployment**, commande à exécuter **deploy.cmd** lors du déploiement des applications Web. Ce script s’exécute toutes les étapes d’une application Node.js habituelles, mais également télécharge la dernière version de io.js. Enfin, le **IISNode.yml** configure les applications Web pour utiliser uniquement le io.js téléchargé binaire au lieu d’un binaire Node.js préinstallé.

> [AZURE.NOTE] Pour mettre à jour le binaire io.js utilisés juste redéployer votre application - le script sera télécharger une nouvelle version de io.js chaque fois que seule l’application est déployée.

<a id="manualinstallation"></a>
## <a name="using-manual-installation"></a>À l’aide d’une Installation manuelle

L’installation manuelle d’une version personnalisée de io.js comprend deux étapes. Tout d’abord, téléchargez le **fichier win-x64** binaire directement à partir de la [distribution de io.js]. Requis sont deux fichiers : **iojs.exe** et **iojs.lib**. Enregistrez les deux fichiers dans un dossier à l’intérieur de votre application web, par exemple dans **l’emplacement/iojs**.

Pour configurer des applications Web pour utiliser des **iojs.exe** au lieu d’une version pré-installée de nœud, créez un fichier **IISNode.yml** à la racine de votre application et ajoutez la ligne suivante.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, que vous avez appris comment utiliser io.js avec une application de Service Web Apps, à l’aide fournie les scripts de déploiement installation manuelle ainsi que. 

> [AZURE.NOTE] IO.js est en cours de développement importante et mis à jour plus fréquemment que Node.js. Un certain nombre de modules de Node.js peut ne pas fonctionne avec io.js - Veuillez consulter [io.js sur GitHub] pour le dépannage.

## <a name="whats-changed"></a>Ce qui a changé
* Pour obtenir un guide pour la modification de sites Web au Service de l’application voir : [Service d’application Azure et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

[IO.js]: https://iojs.org
[distribution de IO.js]: https://iojs.org/dist/
[IO.js sur GitHub]: https://github.com/iojs/io.js
[io.js Deployment Script]: https://github.com/felixrieseberg/iojs-azure
 