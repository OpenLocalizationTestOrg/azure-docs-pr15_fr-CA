<properties 
    pageTitle="Ressources de documentation WebJobs Azure" 
    description="Ressources recommandées pour apprendre comment utiliser Azure WebJobs et le WebJobs d’Azure SDK." 
    services="app-service" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="tdykstra"/>

# <a name="azure-webjobs-documentation-resources"></a>Ressources de documentation WebJobs Azure

## <a name="overview"></a>Vue d’ensemble

Cette rubrique fournit des ressources de documentation sur l’utilisation d’Azure WebJobs et le WebJobs d’Azure SDK. WebJobs Azure fournissent un moyen simple pour exécuter des scripts ou des programmes en tant que processus d’arrière-plan dans le contexte d’une [application de Service de l’application web, API, application ou application mobile](../app-service/app-service-value-prop-what-is.md). Vous pouvez télécharger et exécuter un fichier exécutable comme comme cmd, bat, exe (.NET), ps1, sh, php, py, js et jar. Ces programmes s’exécutent en tant que WebJobs selon une planification (cron) ou en continu.

Le [Kit de développement logiciel WebJobs](websites-webjobs-resources.md) vise à simplifier le code que vous écrivez pour les tâches courantes qu’un WebJob peut effectuer, telles que le traitement d’image, de traitement de la file d’attente, RSS agrégation, gestion des fichiers et l’envoi d’e-mails. Le SDK WebJobs possède des fonctionnalités intégrées pour travailler avec le stockage Azure et Bus de Service, tâches de planification et de gestion des erreurs et de nombreux autres scénarios courants. En outre, il est conçu pour être extensible, et il est un [référentiel de code source pour les extensions d’ouvrir](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). [Fonctions d’Azure](../azure-functions/functions-overview.md) (en aperçu) est basé sur une version du SDK WebJobs qui fonctionne avec C# script, Node.js et d’autres langages. 

Création, déploiement et gestion de WebJobs sont parfaitement compatible avec les outils intégrés dans Visual Studio. Vous pouvez créer WebJobs à partir de modèles, publier et gérer (run/stop/moniteur/debug) les. 

WebJobs tableau de bord dans le portail Azure fournit de puissantes fonctions de gestion qui vous donnent un contrôle total sur l’exécution de WebJobs, y compris la possibilité d’appeler des fonctions dans WebJobs. Le tableau de bord affiche également les exécutions de la fonction et la sortie de journalisation. 

##<a name="getstarted"></a>Mise en route avec le SDK de WebJobs et de WebJobs

* [Introduction à WebJobs Azure](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [WebJobs Azure sont Isard et doit pouvoir les utiliser dès maintenant !](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Publication de Blog par recherche de Troy.)
* [Fonctionnalités d’Azure WebJobs](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Quel est le SDK WebJobs](websites-dotnet-webjobs-sdk.md)
* [Guide des tâches en arrière-plan par Microsoft Patterns and Practices](/documentation/articles/best-practices-background-jobs/)
* [Annonce de la 1.1.0 RTM du Kit de développement Microsoft Azure WebJobs](/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Mise en route avec le Kit de développement WebJobs Azure](websites-dotnet-webjobs-sdk-get-started.md)
* [Comment faire pour utiliser le stockage de la file d’attente Azure avec le SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [L’utilisation du stockage blob Azure avec le SDK WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Comment faire pour utiliser le stockage par table Azure avec le SDK WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [L’utilisation du Bus des services Azure avec le SDK WebJobs](websites-dotnet-webjobs-sdk-service-bus.md)
* [L’utilisation de WebHooks avec le SDK WebJobs, avec des exemples de GitHub, IFTTT et HTTP](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/WebHooks-Walkthrough)
* [Azure WebJobs SDK Guide de référence rapide (téléchargement PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [Documentation des paramètres WebJobs dans GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* Vidéos
    * [Le Kit de développement de WebJobs et de WebJobs](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
    * [Série de vidéos WebJobs Azure sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
    * [Introduction WebJobs outillage pour Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Outils de WebJobs et le débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
    * [Azure mise à jour de WebJobs avec Pranav Rastogi - extensibilité dans la version 1.1](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

Consultez également les sections suivantes sur [WebJobs de déploiement](#deploy) et [de test et de débogage WebJobs](#debug).

##<a name="deploy"></a>Déploiement WebJobs

* [Comment faire pour déployer Azure WebJobs à l’aide de Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Comment déployer des WebJobs de via le portail Azure](web-sites-create-web-jobs.md)
* [L’activation de livraison de ligne de commande ou en continu des WebJobs Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Déploiement d’une application de console .NET vers Azure à l’aide de WebJobs de GIT](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Déploiement d’une WebJob F# vers Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Déploiement des services personnalisés en tant que Webjobs d’Azure](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* Vidéos
    * [Introduction WebJobs outillage pour Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Outils de WebJobs et le débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Planification WebJobs

* [La boîte de dialogue WebJob Azure ajouter](websites-dotnet-deploy-webjobs.md#configure)
* [Créer un WebJob programmé dans le portail Azure](web-sites-create-web-jobs.md#CreateScheduled)
* [Raccorder une tâche du planificateur à un WebJob](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Planification WebJobs Azure avec des expressions cron](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [Les WebJob des fonctions de planification à l’aide de la TimerTrigger du Kit de développement logiciel WebJobs](websites-dotnet-webjobs-sdk.md#schedule)

##<a name="debug"></a>Test et débogage WebJobs

* [Nouveau développeur et les fonctionnalités de débogage pour WebJobs Azure dans Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Afficher le tableau de bord de WebJobs](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Comment écrire des journaux à l’aide du SDK de WebJobs et de les afficher dans le tableau de bord](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [WebJobs de débogage à distance](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Qui a écrit ce blob ?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Code d’hébergement interactif dans le nuage](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Ajout de Trace pour Azure WebJobs](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Contrôler, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* Vidéos
    * [Outils de WebJobs et le débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>WebJobs de mise à l’échelle

* [Mise à l’échelle de votre Application Web avec des sites Web Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Service d’application azure : architecture d’applications à l’échelle Massive prêt-pour-Business Web](https://channel9.msdn.com/Events/Build/2014/3-626). Couvertures mise à l’échelle des applications web avec WebJobs, y compris le SDK WebJobs.
* Vidéos
    * [Mise à l’échelle WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Autres ressources WebJobs

* [Billet de blog WebJobs GA Azure par Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Traitements de Powershell Web sur le Service d’application Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Recevoir une notification lorsque votre Azure déclenchée WebJobs se termine](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Stratégie de rétention de sauvegarde d’application Web simple avec WebJobs](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Applications Web azure et les Services en nuage lent à la première demande](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Montre comment utiliser WebJobs pour simuler la fonctionnalité AlwaysOn qui est uniquement disponible pour la couche de la tarification Standard.
* [Arrêt WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Arrêt de WebJobs Kit de développement logiciel, voir [arrêt progressif](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Automatisation des sauvegardes avec Azure WebJobs & AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Vidéos
    * [Azure vidéos WebJobs par Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
    * [Série de vidéos WebJobs Azure sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Autres ressources WebJobs SDK

* [Notes de mise à jour du Kit de développement logiciel WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [WebJobs Kit de développement de code source](https://github.com/Azure/azure-webjobs-sdk)
* [Extensions de WebJobs le Kit de développement de code source](https://github.com/Azure/azure-webjobs-sdk-extensions), avec un [guide détaillé pour le modèle d’extensibilité](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).  
* [Code source du Script du Kit de développement logiciel WebJobs](https://github.com/Azure/azure-webjobs-sdk-script/) (utilisé pour les [Fonctions d’Azure](../azure-functions/functions-overview.md))
* [Pour télécharger des fichiers FREB au stockage Azure à l’aide du SDK de WebJobs WebJob](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hébergement webjobs Azure en dehors d’Azure, avec les avantages de l’enregistrement d’un Azure hébergé webjob](http://bypassion.dk/?p=510)
* [Création d’un outil d’importation de données avec Azure WebJobs](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Vue d’ensemble des fonctions Azure](../azure-functions/functions-overview.md)
* Vidéos
    * [Série de vidéos WebJobs Azure sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Exemples d’applications WebJob

* [Exemples d’applications fournis par l’équipe WebJobs sur GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Simple application Web Azure WebJobs Backend à l’aide du SDK WebJobs](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Illustre l’utilisation de WebJobs planifiées et événementielles. Consultez le blog [la reconstruction de le SiteMonitR à l’aide du Kit de développement logiciel Azure WebJobs](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blogs

* [Blog Azure](/blog)
* [Blog de Amit Apple](http://blog.amitapple.com/). Se concentre sur WebJobs (pas le Kit de développement).
* [Blog de Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Obtention d’aide WebJobs

* [StackOverflow pour WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow pour le Kit de développement WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [StackOverflow pour les fonctions Azure](http://stackoverflow.com/questions/tagged/azure-functions)
* [Forum sur Azure et ASP.NET](http://forums.asp.net/1247.aspx)
* [Forum d’application Service Web applications Azure](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Site de voix d’utilisateur Web applications Azure](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/). Utilisez le hashtag #AzureWebJobs.
* [Signaler un problème ou une bogue de WebJobs](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

