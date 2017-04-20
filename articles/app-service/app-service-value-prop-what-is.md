<properties
    pageTitle="Service d’application Azure pour le web, mobile et applications d’API | Microsoft Azure"
    description="Découvrez comment Azure Application Service vous aide à développer, déployer et gérer des applications mobiles et le web."
    keywords="service d’application, service d’application azure, app coût service, échelle, évolutive, déploiement d’applications, déploiement d’application azure, paas, plate-forme-as-a-service, site Web, site web, web, azure mobile"
    services="app-service"
    documentationCenter=""
    authors="omarkmsft"
    manager="erikre"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/26/2016"
    ms.author="omark"/>

# <a name="what-is-azure-app-service"></a>Quel est le Service d’application Azure ?

*Service d’application* est une [plateforme en tant que service](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) offre de Microsoft Azure. Créer web et des applications mobiles pour chaque plate-forme ou périphérique. Intégrer vos applications avec les solutions SaaS, se connecter avec des applications sur site et automatisez vos processus d’entreprise. Azure s’exécute vos applications sur les machines virtuelles (VM), entièrement gérés avec un choix de ressources partagées de machine virtuelle ou ordinateurs virtuels dédiés.

Service d’application inclut le web et les fonctionnalités que nous avons précédemment fourni séparément en tant que sites Web d’Azure et d’Azure Mobile Services mobiles. Il inclut également de nouvelles capacités pour l’automatisation des processus métiers et l’hébergement cloud API. Sous la forme d’un service intégré, Service d’application vous permet de composer des divers composants : sites Web, application mobile back-ends, API RESTful et les processus métiers--en une solution unique.

La vidéo de 4 minutes suivante fournit une brève explication de comment le Service d’application est lié à des offres Azure antérieures et quelles sont les nouveautés dans il.

+[AZURE.VIDEO app-service-history-lesson]

## <a name="why-use-app-service"></a>Pourquoi utiliser le Service de l’application ?

Voici certaines fonctionnalités clés et les fonctionnalités du Service de l’application :

- **Plusieurs langages et environnements** - Service de l’application a une prise en charge exhaustive pour ASP.NET, Node.js, Java, PHP et Python. Vous pouvez également exécuter [Windows PowerShell et d’autres scripts ou des exécutables](../app-service-web/web-sites-create-web-jobs.md) sur VMs de Service d’application.

- **Optimisation de DevOps** - définir de [déploiement et intégration continue](../app-service-web/app-service-continuous-deployment.md) avec Visual Studio Team Services, GitHub ou BitBucket. Promouvoir les mises à jour via le [test et le test des environnements](../app-service-web/web-sites-staged-publishing.md). Exécuter [A / B test](../app-service-web/app-service-web-test-in-production-get-start.md). Gérer vos applications dans le Service d’application à l’aide de [PowerShell d’Azure](../powershell-install-configure.md) ou l' [interface de ligne de commande de multiplates-formes (CLI)](../xplat-cli-install.md).

- **Mettre à l’échelle globale avec haute disponibilité** - échelle [](../app-service-web/web-sites-scale.md) d’entrée ou de [sortie](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellement ou automatiquement. Héberger vos applications n’importe où dans l’infrastructure de centre de données global de Microsoft et le Service d’application [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promet à haute disponibilité.

- **Connexions aux plates-formes SaaS et les données sur site** - choisissez dans plus de 50 [connecteurs](../connectors/apis-list.md) pour les systèmes d’entreprise (par exemple, SAP, Siebel et Oracle), services de SaaS (par exemple, la force de vente et Office 365) et les services internet (tels que Facebook et Twitter). Accès en local les données à l’aide de [Connexions de hybride](../biztalk-services/integration-hybrid-connection-overview.md) et [Azure des réseaux virtuels](../app-service-web/web-sites-integrate-with-vnet.md).

- Service **sécurité et conformité** - App est [compatible PCI, SOC et ISO](https://www.microsoft.com/TrustCenter/).

- **Modèles d’application** - choisir parmi une liste complète des modèles d’application sur le [Marché d’Azure](https://azure.microsoft.com/marketplace/) qui vous permettent d’utiliser un Assistant pour installer les logiciels open source populaires comme WordPress, Joomla et Drupal.

- **Intégration de Visual Studio** - outils dédiés dans Visual Studio rationaliser le travail de création, le déploiement et le débogage.

## <a name="app-types-in-app-service"></a>Types d’application dans le Service d’application

Service de l’application offre plusieurs *types d’application*, chacun d’eux est destiné à héberger un type spécifique de la charge de travail :

- [**Applications web**](../app-service-web/app-service-web-overview.md) - pour l’hébergement d’applications web et de sites Web.

- [**Applications mobiles**](../app-service-mobile/app-service-mobile-value-prop.md) Pour l’hébergement d’application mobile back-end.

- [**Applications d’API**](../app-service-api/app-service-api-apps-why-best-platform.md) - pour l’hébergement des API RESTful.

- [**Applications de logique**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - pour l’automatisation des processus métiers et intégration des systèmes et des données sur les nuages sans écrire de code.

Word *application* ici fait référence aux ressources hébergement dédiés à l’exécution d’une charge de travail. Prise de « web app » par exemple, vous êtes probablement habitué à la pensée d’une application web en tant que les ressources de calcul et le code d’application qui ensemble offrent des fonctionnalités à un navigateur. Mais dans le Service d’application, une *application web* est les ressources de calcul Azure fournit pour l’hébergement de votre code d’application. Si votre application est composée d’un site web frontal et une API RESTful back-end, vous pouvez déployer à la fois pour une application web, ou vous pouvez déployer votre code frontal pour une application web et votre code de back-end pour une application API. Votre application peut être composée de plusieurs applications de Service de l’application de différents types.

## <a name="app-service-plans"></a>Les programmes de Service d’application

[Les programmes de Service application](azure-web-sites-web-hosting-plans-in-depth-overview.md) permet de spécifier le type de ressources de calcul que vos applications s’exécutent sur. Si vous pensez que les charges de trafic de lumière, vous pouvez utiliser partagé des machines virtuelles (**libre** et **partagées** , niveaux de prix). Pour les charges plus élevées, vous pouvez choisir à partir de plusieurs tailles de machines virtuelles dédiés (niveaux de**base**, **Standard**et **Premium** ). Plusieurs applications de Service de l’application peuvent partager le même plan, et leur mise à l’échelle vers les niveaux de prix entre eux dans le plan.

Si vous avez besoin de plus d’évolutivité et l’isolation du réseau, vous pouvez exécuter vos applications dans un [Environnement de Service d’application](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Tarification

Pour plus d’informations sur la quantité les frais de Service de l’application, consultez [Tarification des Service d’application](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="test-drive-app-service"></a>Testez l’application de Service

[Créer un exemple d’application web, une application mobile ou logique app](http://go.microsoft.com/fwlink/?LinkId=523751) - and -play avec elle pendant 1 heure, sans carte de crédit ne nécessaire, aucun engagement, pas de tracas.

Ouvrir un [compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/)ou essayez l’une de nos didacticiels mise en route :

* [Didacticiel : Créer une application web](../app-service-web/app-service-web-get-started.md)
* [Didacticiel : Créer une application mobile](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Didacticiel : Créer une application API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Didacticiel : Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)
