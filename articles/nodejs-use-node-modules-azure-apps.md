<properties
    pageTitle="Utilisation des Modules de Node.js"
    description="Apprenez à travailler avec les modules Node.js lors de l’utilisation de Service d’application Azure ou de Services en nuage."
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


# <a name="using-nodejs-modules-with-azure-applications"></a>À l’aide de Modules de Node.js avec des applications Azure

Ce document fournit des conseils sur l’utilisation de modules de Node.js avec les applications hébergées sur Azure. Il explique comment s’assurer que votre application utilise une version spécifique du module, ainsi que de l’utilisation de modules natifs avec Azure.

Si vous êtes déjà familiarisé avec un résumé de ce qui est décrit dans cet article est d’utiliser Node.js modules, les fichiers **package.json** et **shrinkwrap.json-npm** , les éléments suivants :

* Service d’application Azure comprend les fichiers **package.json** et **shrinkwrap.json-npm** et peuvent installer des modules basés sur les entrées de ces fichiers.
* Services en nuage Azure attendent tous les modules à installer sur l’environnement de développement et la **nœud\_modules** répertoire à inclure dans le cadre du package de déploiement. Il est possible d’activer la prise en charge pour l’installation de modules à l’aide des fichiers **package.json** ou **npm-shrinkwrap.json** sur les Services en nuage, mais cela nécessite une personnalisation des scripts par défaut utilisés par les projets de Service Cloud. Pour obtenir un exemple de cette procédure, reportez-vous à [tâche de démarrage d’Azure pour exécuter installation npm afin d’éviter le déploiement de modules de nœud](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [AZURE.NOTE] Les Machines virtuelles Azure ne sont pas abordées dans cet article, comme l’expérience de déploiement sur un ordinateur virtuel dépend du système d’exploitation hébergé par la Machine virtuelle.

##<a name="nodejs-modules"></a>Modules de Node.js

Les modules sont chargeables JavaScript qui fournissent des fonctionnalités spécifiques de votre application. Un module est généralement installé à l’aide de l’outil de ligne de commande **npm** , cependant d’autres (tels que le module http) sont fournis dans le cadre du package principal Node.js.

Lorsque les modules sont installés, ils sont stockés dans le **nœud\_modules** répertoire à la racine de votre structure de répertoire d’application. Chaque module dans le **nœud\_modules** directory maintient son propre **nœud\_modules** répertoire contenant tous les modules qu’elle dépend, et ce processus se répète à nouveau pour chaque module tout en bas de la chaîne de dépendance. Ainsi, chaque module installé afin que ses propres exigences de version pour les modules qu'il dépend, toutefois il peut entraîner une arborescence de répertoires volumineuse.

Lors du déploiement de la **nœud\_modules** répertoire dans le cadre de votre application, il augmente la taille du déploiement par rapport à l’utilisation d’un fichier **package.json** ou **shrinkwrap.json-npm** ; Toutefois, il ne garantit que la version des modules utilisés dans la production sont les mêmes que celles utilisées dans le développement.

###<a name="native-modules"></a>Modules natifs

Alors que la plupart des modules sont des fichiers de JavaScript simplement en texte brut, certains modules sont des images binaires de propres à la plate-forme. Ces modules sont compilés au moment de l’installation, généralement en utilisant les Python et nœud-gyp. Dans la mesure où les Services en nuage Azure s’appuient sur la **nœud\_modules** dossier déployée dans le cadre du module application, tout état natif inclus dans le cadre des modules installés doit fonctionner dans un service en nuage comme il a été installé et compilé sur un système de développement de Windows.

Service d’application Azure ne prend pas en charge tous les modules natifs et peut échouer au compilation ceux avec des conditions très spécifiques. Tandis que certains modules populaires comme MongoDB facultatifs dépendances natives et travail très bien sans eux, deux solutions de contournement fait leurs preuves avec presque tous les modules natifs disponibles aujourd'hui :

* Sur un ordinateur Windows qui possède les conditions préalables de tous les natif du module installés, exécutez **l’installation de npm** . Ensuite, déployer le **nœud\_modules** dossier dans le cadre de l’application de Service d’application Azure.
* Service d’application Azure peut être configuré pour exécuter bash personnalisé ou des scripts de shell au cours du déploiement, ce qui vous donne la possibilité d’exécuter des commandes personnalisées et configurer avec précision la manière **npm installer** est en cours d’exécution. Pour une vidéo montrant comment effectuer cette opération, consultez [Scripts de déploiement de site Web personnalisés avec Kudu].

###<a name="using-a-packagejson-file"></a>À l’aide d’un fichier package.json

Le fichier **package.json** est un moyen de spécifier les dépendances de niveau supérieur que votre application requiert la plateforme d’hébergement permettant d’installer les dépendances, plutôt que de vous obliger à inclure la **nœud\_packages** dossier dans le cadre du déploiement. Une fois que l’application a été déployée, la commande **install de npm** est utilisée pour analyser le fichier **package.json** et installer toutes les dépendances répertoriées.

Pendant le développement, vous pouvez utiliser la **--Enregistrer**, **--Enregistrer-dev**, ou **--Enregistrer-facultatif** paramètres lors de l’installation de modules pour ajouter une entrée pour le module à votre fichier de **package.json** , automatiquement. Pour plus d’informations, voir la rubrique [npm-install](https://docs.npmjs.com/cli/install).

Un problème potentiel avec le fichier **package.json** est qu’il indique uniquement la version pour les dépendances de niveau supérieur. Chaque module installé peut-être ou ne peut-être pas spécifier la version des modules qu'il dépend, et il est donc possible que vous risquez avec une chaîne de dépendance différente que celle utilisée dans le développement.

> [AZURE.NOTE]
> Lors du déploiement de Service d’application Azure, si votre fichier <b>package.json</b> fait référence à un module natif une erreur semblable au suivant s’affiche lors de la publication de l’application à l’aide de Git :

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###<a name="using-a-npm-shrinkwrapjson-file"></a>À l’aide d’un fichier npm-shrinkwrap.json

Le fichier **shrinkwrap.json-npm** est une tentative pour résoudre les limites de gestion des versions de module du fichier **package.json** . Alors que le fichier **package.json** comprend uniquement des versions pour les modules de niveau supérieur, le fichier **npm-shrinkwrap.json** contient la version requise pour la chaîne de dépendance complet du module.

Lorsque votre application est prête pour la production, vous pouvez verrouiller-vers le bas de la version requise et créer un fichier **npm-shrinkwrap.json** à l’aide de la commande **npm déchiré** . Utiliser les versions actuellement installées dans le **nœud\_modules** dossier et les enregistrer dans le fichier **shrinkwrap.json-npm** . Une fois que l’application a été déployée à l’environnement d’hébergement, la commande **install de npm** est utilisée pour analyser le fichier **npm-shrinkwrap.json** et installer toutes les dépendances répertoriées. Pour plus d’informations, consultez [npm-déchiré](https://docs.npmjs.com/cli/shrinkwrap).

> [AZURE.NOTE]
>Lors du déploiement de Service d’application Azure, si votre fichier <b>npm-shrinkwrap.json</b> fait référence à un module natif une erreur semblable au suivant s’affiche lors de la publication de l’application à l’aide de Git :

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous comprenez comment utiliser des modules de Node.js avec Azure, découvrez comment [spécifier la version Node.js], [Générer et déployer une application web de Node.js]et [comment utiliser l’Interface de ligne de commande Azure pour Mac et Linux].

Pour plus d’informations, consultez le [Centre pour développeurs Node.js](/develop/nodejs/).

[spécifier la version Node.js]: nodejs-specify-node-version-azure-apps.md
[Comment faire pour utiliser l’Interface de ligne de commande Azure pour Mac et Linux]: xplat-cli-install.md
[générer et déployer une application web de Node.js]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Site Web de personnaliser les Scripts de déploiement avec Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/
