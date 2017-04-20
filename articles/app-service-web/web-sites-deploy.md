<properties
    pageTitle="Déployer votre application au Service d’application Azure"
    description="Apprenez à déployer votre application au Service d’application Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cephalin;dariac"/>
    
# <a name="deploy-your-app-to-azure-app-service"></a>Déployer votre application au Service d’application Azure

Cet article vous aide à déterminer la meilleure option pour déployer les fichiers de votre application web, back-end de l’application mobile ou API app au [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714), puis vous guide vers les ressources appropriées avec des instructions spécifiques à votre option préférée.

## <a name="overview"></a>Vue d’ensemble du déploiement Application Service Azure

Service d’application Azure gère l’infrastructure d’application pour vous (ASP.NET, PHP, Node.js, etc.). Certaines infrastructures sont activées par défaut tandis que d’autres, tels que Java et les Python, ayez une configuration simple coche pour l’activer. En outre, vous pouvez personnaliser votre infrastructure d’application, telles que le nombre de bits de votre exécution ou de la version PHP. Pour plus d’informations, voir [configurer votre application dans le Service d’application Azure](web-sites-configure.md).

Puisque vous n’avez pas à vous préoccuper de l’infrastructure de serveur ou une application web, le déploiement de votre application au Service d’application est une question de déploiement de votre code, des fichiers binaires, des fichiers de contenu et leur structure de répertoire correspondant, dans le [répertoire de **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) dans Azure (ou de la **/travaux/App_Data/wwwroot/sites/** répertoire de WebJobs). Service d’application prend en charge les options de déploiement suivantes : 

- [FTP ou FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): utilisez vos favoris FTP ou FTPS activé pour déplacer vos fichiers vers Azure, à partir de [FileZilla](https://filezilla-project.org) à complète l’IDE comme [NetBeans](https://netbeans.org). Il s’agit strictement d’un processus de téléchargement du fichier. Aucun des services supplémentaires ne sont fournis par le Service de l’application, tels que le contrôle de version, gestion de structure de fichiers, etc.. 

- [Kudu (Git/mercurienne ou OneDrive/boîte de dépôt)](https://github.com/projectkudu/kudu/wiki/Deployment): utilisez le [moteur de déploiement](https://github.com/projectkudu/kudu/wiki) dans le Service d’application. Poussez votre code à Kudu directement à partir de n’importe quel référentiel. Kudu fournit également des services ajoutés chaque fois que le code est poussé, y compris le contrôle de version, la restauration du package, MSBuild et [les raccordements de web](https://github.com/projectkudu/kudu/wiki/Web-hooks) pour un déploiement en continu et autres tâches d’automatisation. Le moteur de déploiement Kudu prend en charge 3 types différents de sources de déploiement :   
    * Synchronisation de contenu à partir de la boîte de dépôt et de OneDrive   
    * Référentiel de DEPLOIEMENT continu avec synchronisation automatique à partir de Visual Studio Team Services, Bitbucket et GitHub  
    * Déploiement basé sur un référentiel avec une synchronisation manuelle à partir de Git local  

- [Déploiement Web](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): déployer le code de Service de l’application directement à partir de vos favori Microsoft des outils tels que Visual Studio l’utilisant des outils qui automatise le déploiement pour les serveurs IIS. Cet outil prend en charge le déploiement diff uniquement, création de base de données, les transformations de chaînes de connexion, etc.. Déploiement de Web est différente de Kudu fichiers binaires d’application sont créés avant de les déployer vers Azure. Similaire à FTP, aucun des services supplémentaires ne sont fournis par le Service de l’application.

Outils de développement web courants prennent en charge une ou plusieurs de ces processus de déploiement. Tandis que l’outil que vous choisissez détermine les processus de déploiement que vous pouvez exploiter, la fonctionnalité DevOps réelle à votre disposition dépend de la combinaison du processus de déploiement et les outils spécifiques que vous choisissez. Par exemple, si vous effectuez le déploiement de Web à partir de [Visual Studio avec Azure SDK](#vspros), même si vous n’obtenez pas automation à partir de Kudu, vous obtenez restauration du package et l’automatisation de MSBuild dans Visual Studio. 

>[AZURE.NOTE] Ces processus de déploiement ne pas réellement [mettre en service les ressources Azure](../resource-group-template-deploy-portal.md) que votre application peut nécessiter. Toutefois, la plupart des articles sur les procédures liées vous montrer comment mettre en service l’application et déployer votre code au fin bout à bout. Vous trouverez également des options supplémentaires pour le provisionnement des ressources Azure dans la section [automatisez le déploiement à l’aide des outils de ligne de commande](#automate) .
     
## <a name="ftp"></a>Déployer via FTP en copiant les fichiers manuellement vers Azure
Si vous permettent de copier manuellement votre contenu web sur un serveur web, vous pouvez utiliser un utilitaire [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) pour copier les fichiers, tels que l’Explorateur Windows ou [FileZilla](https://filezilla-project.org/).

Les professionnels de l’informatique de copier manuellement les fichiers sont les suivants :

- Une connaissance et une complexité minimale pour les outils de FTP. 
- Permet de savoir exactement où vos fichiers vont.
- Sécurité avec FTPS.

Les inconvénients de la copie manuelle de fichiers sont les suivants :

- Avoir à connaître déployer des fichiers dans les répertoires corrects dans le Service d’application. 
- Aucun contrôle de version pour la restauration en cas de défaillance.
- Aucun historique de déploiement intégrées pour la résolution des problèmes de déploiement.
- Déploiement de long potentiel fois parce que de nombreux outils FTP ne fournir diff seule copie et il suffit de copier tous les fichiers.  

### <a name="howtoftp"></a>Comment déployer en copiant les fichiers manuellement vers Azure
Copie des fichiers vers Azure implique quelques étapes simples :

1. Supposant que vous déjà établi des informations d’identification de déploiement, d’obtenir les informations de connexion FTP par les **paramètres** > **Propriétés**et ensuite à copier les valeurs **d’Utilisateur FTP/déploiement**, **Nom d’hôte FTP**et **FTPS nom d’hôte**. Copiez la valeur utilisateur **FTP/déploiement utilisateur** affichée par le portail d’Azure, y compris le nom de l’application afin de fournir le contexte approprié pour le serveur FTP.
2. À partir de votre client FTP, utilisez les informations de connexion que vous avez recueillies pour vous connecter à votre application.
3. Copier vos fichiers et leur structure de répertoire correspondant dans le [répertoire de **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) dans Azure (ou de la **/travaux/App_Data/wwwroot/sites/** répertoire de WebJobs).
4. Accédez à l’URL de votre application pour vérifier que l’application s’exécute correctement. 

Pour plus d’informations, consultez la ressource suivante :

* [Créer une application web PHP-MySQL et de déployer à l’aide de FTP](web-sites-php-mysql-deploy-use-ftp.md).

## <a name="dropbox"></a>Déployer par la synchronisation avec un dossier de nuage
Une bonne alternative à [Copier les fichiers manuellement](#ftp) est la synchronisation de fichiers et des dossiers au Service de l’application à partir d’un service de stockage cloud tels que OneDrive et d’échange. La synchronisation avec un dossier de nuage utilise le processus de Kudu pour le déploiement (voir [vue d’ensemble du processus de déploiement](#overview)).

Les professionnels de la synchronisation avec un dossier de nuage sont les suivantes :

- Simplicité de déploiement. Services tels que OneDrive et échange fournissent des clients de synchronisation de bureau, votre répertoire de travail local est également votre répertoire de déploiement.
- Déploiement d’un seul clic.
- Toutes les fonctionnalités dans le moteur de déploiement de Kudu seront disponible (par exemple, restauration de package, automation).

Les inconvénients de la synchronisation avec un dossier de nuage sont les suivantes :

- Aucun contrôle de version pour la restauration en cas de défaillance.
- Aucun déploiement automatisé, synchronisation manuelle est requise.

### <a name="howtodropbox"></a>Le déploiement par la synchronisation avec un dossier de nuage
Dans le [Portail Azure](https://portal.azure.com), vous pouvez désigner un dossier pour la synchronisation de contenu dans votre stockage en nuage OneDrive ou d’échange, travailler avec votre code d’application et le contenu de ce dossier et au Service de l’application de synchronisation en cliquant sur un bouton.

* [Contenu de la synchronisation d’un dossier de nuage au Service d’application Azure](app-service-deploy-content-sync.md). 

## <a name="continuousdeployment"></a>Déployer en continu à partir d’un service de contrôle de source de nuage
Si votre équipe de développement utilise un service de gestion (des services SCM) de code source basée sur le cloud tels que [Visual Studio Team Services](http://www.visualstudio.com/), [GitHub](https://www.github.com)ou [BitBucket](https://bitbucket.org/), vous pouvez configurer le Service d’application pour l’intégrer à votre référentiel et déployer en continu. 

Avantages du déploiement d’un service de contrôle de source de nuage sont les suivantes :

- Contrôle de version pour activer la restauration.
- Possibilité de configurer le déploiement en continu pour Git (et mercurienne le cas échéant) les référentiels. 
- Déploiement de branche spécifique, peuvent déployer différentes branches à différents [emplacements](web-sites-staged-publishing.md).
- Toutes les fonctionnalités dans le moteur de déploiement de Kudu seront disponible (par exemple, version de déploiement, restauration, restauration du package, automation).

CON du déploiement d’un service de contrôle de source de nuage est :

- Des connaissances du service SCM respectif nécessaire.

###<a name="vsts"></a>Comment déployer en continu à partir d’un service de contrôle de source de nuage
Dans le [Portail Azure](https://portal.azure.com), vous pouvez configurer le déploiement continu à partir de Visual Studio Team Services, Bitbucket et GitHub.

* [Déploiement continue à un Service d’application Azure](app-service-continuous-deployment.md). 

## <a name="localgitdeployment"></a>Déployer à partir de Git local
Si votre équipe de développement utilise un service de gestion (des services SCM) sur site local source code basé sur Git, vous pouvez configurer cette opération comme une source de déploiement au Service de l’application. 

Professionnels de l’informatique de déployer à partir de Git local sont les suivantes :

- Contrôle de version pour activer la restauration.
- Déploiement de branche spécifique, peuvent déployer différentes branches à différents [emplacements](web-sites-staged-publishing.md).
- Toutes les fonctionnalités dans le moteur de déploiement de Kudu seront disponible (par exemple, version de déploiement, restauration, restauration du package, automation).

CON de déploiement à partir de Git local est la suivante :

- Des connaissances du système SCM respectif requis.
- Aucune solution clé en main pour un déploiement en continu. 

###<a name="vsts"></a>Comment faire pour déployer à partir de Git local
Dans le [Portail Azure](https://portal.azure.com), vous pouvez configurer le déploiement de Git local.

* [Déploiement local Git au Service d’application Azure](app-service-deploy-local-git.md). 
* [Publication d’applications Web à partir de n’importe quel mis en pension git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html).  

## <a name="deploy-using-an-ide"></a>Déployer à l’aide d’un IDE
Si vous utilisez déjà [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) avec un [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/), ou autres suites IDE [Xcode](https://developer.apple.com/xcode/), [Eclipse](https://www.eclipse.org)et [IntelliJ idée](https://www.jetbrains.com/idea/), vous pouvez déployer vers Azure directement à partir de votre IDE. Cette option est idéale pour un développeur individuel.

Visual Studio prend en charge tous les processus de déploiement de trois (FTP, Git et Web Deploy), selon vos préférences, tandis que les autres IDE peut déployer au Service de l’application, si elles ont intégration FTP ou Git (voir [vue d’ensemble du processus de déploiement](#overview)).

Les professionnels du déploiement à l’aide d’un IDE sont les suivantes :

- Potentiellement réduire l’outillage votre application de bout en bout de cycle de vie. Développer, déboguer, suivre et déployer votre application à tout Azure sans déplacer en dehors de votre IDE. 

Les inconvénients du déploiement à l’aide d’un IDE sont les suivantes :

- Complexité de l’outillage.
- Nécessite toujours un système de contrôle de code source pour un projet d’équipe.

<a name="vspros"></a>Autres professionnels de l’informatique de déploiement à l’aide de Visual Studio avec Azure SDK sont les suivantes :

- Azure SDK permet des citoyens de première classe de ressources Azure dans Visual Studio. Créer, supprimer, modifier, démarrer et arrêter les applications, la base de données SQL de back-end, live-déboguer l’application Azure et bien plus encore. 
- Modification des fichiers de code sur Azure en direct.
- Débogage des applications en direct sur Azure.
- Explorateur d’Azure intégré.
- Déploiement de comparaison uniquement. 

###<a name="vs"></a>Comment déployer directement à partir de Visual Studio

* [Mise en route avec Azure et ASP.NET](web-sites-dotnet-get-started.md). Comment créer et déployer un projet de web ASP.NET MVC simple à l’aide de Visual Studio et Web Deploy.
* [Comment faire pour déployer Azure WebJobs à l’aide de Visual Studio](websites-dotnet-deploy-webjobs.md). Comment configurer des projets d’Application de la Console afin qu’ils déploient en tant que WebJobs.  
* [Déployer une application sécurisée ASP.NET MVC 5 avec l’appartenance, OAuth et de la base de données SQL pour les applications Web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Comment créer et déployer un projet web d’ASP.NET MVC avec une base de données SQL, à l’aide de Visual Studio, le déploiement de Web et Migrations premier du Code Entity Framework.
* [Déploiement de Web ASP.NET à l’aide de Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Une série de didacticiels 12-partie qui couvre une gamme plus complète des tâches de déploiement que les autres dans cette liste. Certaines fonctionnalités de déploiement d’Azure ont été ajoutées depuis le didacticiel a été écrit, mais les notes ajoutées ultérieurement expliquent ce qu’est manquant.
* [Déploiement d’un site Web ASP.NET Azure dans Visual Studio 2012, à partir d’un référentiel Git directement](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explique comment déployer un projet web d’ASP.NET dans Visual Studio, en utilisant le plug-in de Git de valider le code Git et Azure qui se connecte au référentiel Git. À partir de Visual Studio 2013, prise en charge de Git est intégrée et ne nécessite pas l’installation d’un plug-in.

###<a name="aztk"></a>Comment faire pour déployer à l’aide des boîtes à outils Azure pour Eclipse et idée de IntelliJ

Microsoft rend possible de déployer des applications Web vers Azure directement à partir d’Eclipse et IntelliJ de via le [Shared Computer Toolkit Azure pour Eclipse](../azure-toolkit-for-eclipse.md) et [Shared Computer Toolkit Azure pour IntelliJ](../azure-toolkit-for-intellij.md). Les didacticiels suivants illustrent les étapes impliquées dans le déploiement mondial de simple un « Hello » Web App sur Azure à l’aide de l’IDE :

*  [Créer une application de Web Hello World pour Azure dans Eclipse](./app-service-web-eclipse-create-hello-world-web-app.md). Ce didacticiel vous montre comment le Shared Computer Toolkit Azure pour Eclipse permet de créer et de déployer une application Web Hello World pour Azure.
*  [Créer une application de Web Hello World pour Azure dans IntelliJ](./app-service-web-intellij-create-hello-world-web-app.md). Ce didacticiel vous montre comment le Shared Computer Toolkit Azure pour IntelliJ permet de créer et de déployer une application Web Hello World pour Azure.


## <a name="automate"></a>Automatiser le déploiement à l’aide des outils de ligne de commande

* [Automatiser le déploiement avec MSBuild](#msbuild)
* [Copier des fichiers avec des scripts et des outils FTP](#ftp)
* [Automatiser le déploiement avec Windows PowerShell](#powershell)
* [Automatisation du déploiement avec l’API de gestion de .NET](#api)
* [Déployer à partir d’Azure l’Interface de ligne de commande (CLI Azure)](#cli)
* [Déployer à partir de la ligne de commande de déploiement Web](#webdeploy)
* [À l’aide de Scripts de commandes FTP](http://support.microsoft.com/kb/96269).
 
Une autre option de déploiement consiste à utiliser un service en nuage comme [Octopus déployer](http://en.wikipedia.org/wiki/Octopus_Deploy). Pour plus d’informations, consultez [ASP.NET de déployer des applications pour les Sites Web Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Automatiser le déploiement avec MSBuild

Si vous utilisez l' [IDE de Visual Studio](#vs) pour le développement, vous pouvez utiliser [MSBuild](http://msbuildbook.com/) pour automatiser tout ce que vous pouvez faire dans votre IDE. Vous pouvez configurer MSBuild pour permet de copier des fichiers de [Déploiement Web](#webdeploy) ou [FTP/FTPS](#ftp) . Web Deploy peut également automatiser de nombreuses autres relatifs au déploiement tâches, telles que le déploiement des bases de données.

Pour plus d’informations sur le déploiement de ligne de commande à l’aide de MSBuild, consultez les ressources suivantes :

* [Le déploiement Web ASP.NET à l’aide de Visual Studio : déploiement de la ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Dixième d’une série de didacticiels sur le déploiement d’Azure à l’aide de Visual Studio. Montre comment utiliser la ligne de commande pour déployer une fois le paramétrage des profils de publication dans Visual Studio.
* [Dans le moteur de génération de Microsoft : à l’aide de MSBuild et Team Foundation Build](http://msbuildbook.com/). Livre de papier qui inclut des chapitres sur l’utilisation de MSBuild pour le déploiement.

###<a name="powershell"></a>Automatiser le déploiement avec Windows PowerShell

Vous pouvez exécuter des fonctions de déploiement de MSBuild ou FTP à partir de [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Si vous procédez ainsi, vous pouvez également utiliser une collection d’applets de commande Windows PowerShell qui facilitent l’appel de l’API de gestion Azure reste.

Pour plus d’informations, consultez les ressources suivantes :

* [Déployer une application web liée à un référentiel de GitHub](app-service-web-arm-from-github-provision.md)
* [Mettre en service une application web avec une base de données SQL](app-service-web-arm-with-sql-database-provision.md)
* [Configurer et déployer microservices prévisible dans Azure](app-service-deploy-complex-application-predictably.md)
* [Création d’applications réelles nuage avec Azure – automatisez tout](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Chapitre E-book qui explique comment l’exemple d’application indiqué dans le livre électronique utilise des scripts de Windows PowerShell pour créer un environnement de test Azure et la déployer sur elle. Reportez-vous à la section de [ressources](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) pour obtenir des liens vers la documentation supplémentaire de PowerShell d’Azure.
* [À l’aide de Scripts de Windows PowerShell pour publier dans des environnements de Test et de développement](../vs-azure-tools-publishing-using-powershell-scripts.md). Comment utiliser Windows PowerShell déploiement de scripts que Visual Studio génère.

###<a name="api"></a>Automatisation du déploiement avec l’API de gestion de .NET

Vous pouvez écrire du code C# pour exécuter les fonctions de MSBuild ou FTP pour le déploiement. Si vous procédez ainsi, vous pouvez accéder à la gestion Azure API REST pour exécuter des fonctions de gestion de site.

Pour plus d’informations, consultez la ressource suivante :

* [Automatisation de tout avec les bibliothèques de gestion Azure .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introduction à l’API de gestion de .NET et des liens vers une documentation plus.

###<a name="cli"></a>Déployer à partir d’Azure l’Interface de ligne de commande (CLI Azure)

Vous pouvez utiliser la ligne de commande dans des machines Windows, Mac ou Linux pour le déploiement à l’aide de FTP. Si vous procédez ainsi, vous pouvez également accéder la gestion Azure reste API à l’aide de la CLI d’Azure.

Pour plus d’informations, consultez la ressource suivante :

* [Les outils de ligne de commande d’azure](/downloads/#cmd-line-tools). Page de portail dans Azure.com pour plus d’informations d’outil de ligne de commande.

###<a name="webdeploy"></a>Déployer à partir de la ligne de commande de déploiement Web

[Déploiement Web](http://www.iis.net/downloads/microsoft/web-deploy) est un logiciel Microsoft pour le déploiement vers IIS qui non seulement fournit des fonctionnalités de synchronisation de fichiers intelligent mais également effectuer ou coordonner de nombreuses autres tâches liées au déploiement qui ne peuvent pas être automatisées lorsque vous utilisez FTP. Par exemple, le déploiement Web peuvent déployer une nouvelle base de données ou des mises à jour de la base de données avec votre application web. Web Deploy peut également réduire le temps nécessaire pour mettre à jour un site existant, dans la mesure où il peut copier intelligemment les uniquement les fichiers modifiés. Visual Studio de Microsoft et de Team Foundation Server sont prise en charge intégrée de déploiement Web, mais vous pouvez également utiliser la déploiement Web directement à partir de la ligne de commande pour automatiser le déploiement. Commandes de déploiement Web sont très puissants, mais possible en forte pente de la courbe d’apprentissage.

Pour plus d’informations, consultez la ressource suivante :

* [Les applications Web simple : déploiement](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo sur un outil, qu'il a écrit pour le rendre plus facile à utiliser de déploiement Web.
* [Outil de déploiement web](http://technet.microsoft.com/library/dd568996). Documentation officielle sur le site TechNet de Microsoft. Date du mais toujours un bon point de départ.
* [À l’aide de Web de déploiement](http://www.iis.net/learn/publish/using-web-deploy). Documentation officielle sur le site Microsoft IIS.NET. Également datés mais un bon point de départ.
* [Le déploiement Web ASP.NET à l’aide de Visual Studio : déploiement de la ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild est le moteur de génération utilisé par Visual Studio, et il peut également être utilisé à partir de la ligne de commande pour déployer des applications web à des applications Web. Ce didacticiel fait partie d’une série qui est principalement sur le déploiement de Visual Studio.

##<a name="nextsteps"></a>Étapes suivantes

Dans certains scénarios, vous pouvez souhaiter pouvoir facilement basculer entre un test et une version de production de votre application. Pour plus d’informations, consultez [Déploiement de mis en place sur les applications Web](web-sites-staged-publishing.md).

Un plan de sauvegarde et de restauration en place est une partie importante de tout processus de déploiement. Pour plus d’informations sur le Service de l’application de sauvegarde et restaurer la fonctionnalité, voir [Sauvegardes des applications Web](web-sites-backup.md).  

Pour plus d’informations sur l’utilisation du contrôle d’accès basée sur les rôles d’Azure pour gérer l’accès à un déploiement de Service d’application, consultez [RBAC et publication de sites Web App](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/).


 
