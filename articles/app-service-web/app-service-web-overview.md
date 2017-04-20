<properties
    pageTitle="Vue des applications Web | Microsoft Azure"
    description="Découvrez comment le Service d’application Azure vous permet de développer et d’héberger des applications web"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="cephalin"/>

# <a name="web-apps-overview"></a>Vue d’ensemble des applications Web

*Application Service Web Apps* est une plateforme de calcul entièrement géré qui est optimisée pour l’hébergement d’applications web et de sites Web. Cette offre de [plate-forme en tant que service](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) de Microsoft Azure permet que vous concentrer sur votre logique métier tout Azure prend en charge l’infrastructure d’exécuter et de faire évoluer vos applications.

La vidéo de 5 minutes suivante présente Azure Application Service Web Apps.

[AZURE.VIDEO azure-app-service-web-apps-with-yochay-kiriaty]

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="what-is-a-web-app-in-app-service"></a>Ce qui est une application web dans le Service d’application ?

Dans le Service d’application, une *application web* est les ressources de calcul Azure fournit pour l’hébergement d’une site Web ou une application web.  

Les ressources de calcul peuvent être sur partagé ou dédiés les machines virtuelles (VM), selon le niveau de prix que vous choisissez. Votre code d’application s’exécute dans un ordinateur virtuel géré qui est isolé des autres clients.

Votre code peut être dans n’importe quel langage ou le framework est pris en charge par le [Service d’application Azure](../app-service/app-service-value-prop-what-is.md), tels que ASP.NET, Node.js, Java, PHP ou Python. Vous pouvez également exécuter des scripts qui utilisent [PowerShell et autres langages de script](web-sites-create-web-jobs.md#acceptablefiles) dans une application web.

Pour obtenir des exemples de scénarios d’application typiques que vous pouvez utiliser des applications Web pour, consultez [scénarios d’applications Web](https://azure.microsoft.com/documentation/scenarios/web-app/) et des **scénarios et des recommandations** de [Azure Application Service, comparaison des Machines virtuelles, Service de Fabric et les Services en nuage](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>Pourquoi utiliser des applications Web ?

Voici les principales fonctionnalités de Service d’application qui s’appliquent aux applications Web :

- **Plusieurs langages et environnements** - Service de l’application a une prise en charge exhaustive pour ASP.NET, Node.js, Java, PHP et Python. Vous pouvez également exécuter [PowerShell et d’autres scripts ou des exécutables](../app-service-web/web-sites-create-web-jobs.md) sur VMs de Service d’application.

- **Optimisation de DevOps** - définir de [déploiement et intégration continue](../app-service-web/app-service-continuous-deployment.md) avec Visual Studio Team Services, GitHub ou BitBucket. Promouvoir les mises à jour via le [test et le test des environnements](../app-service-web/web-sites-staged-publishing.md). Exécuter [A / B test](../app-service-web/app-service-web-test-in-production-get-start.md). Gérer vos applications dans le Service d’application à l’aide de [PowerShell d’Azure](../powershell-install-configure.md) ou l' [interface de ligne de commande de multiplates-formes (CLI)](../xplat-cli-install.md).

- **Mettre à l’échelle globale avec haute disponibilité** - échelle [](../app-service-web/web-sites-scale.md) d’entrée ou de [sortie](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellement ou automatiquement. Héberger vos applications n’importe où dans l’infrastructure de centre de données global de Microsoft et le Service d’application [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promet à haute disponibilité.

- **Connexions aux plates-formes SaaS et les données sur site** - choisissez dans plus de 50 [connecteurs](../connectors/apis-list.md) pour les systèmes d’entreprise (par exemple, SAP, Siebel et Oracle), services de SaaS (par exemple, la force de vente et Office 365) et les services internet (tels que Facebook et Twitter). Accès en local les données à l’aide de [Connexions de hybride](../biztalk-services/integration-hybrid-connection-overview.md) et [Azure des réseaux virtuels](../app-service-web/web-sites-integrate-with-vnet.md).

- Service **sécurité et conformité** - App est [compatible PCI, SOC et ISO](https://www.microsoft.com/TrustCenter/).

- **Modèles d’application** - choisir parmi une liste complète des modèles d’application sur le [Marché d’Azure](https://azure.microsoft.com/marketplace/) qui vous permettent d’utiliser un Assistant pour installer les logiciels open source populaires comme WordPress, Joomla et Drupal.

- **Intégration de Visual Studio** - outils dédiés dans Visual Studio rationaliser le travail de création, le déploiement et le débogage.

En outre, une application web peut bénéficier des fonctionnalités offertes par les [Applications d’API](../app-service-api/app-service-api-apps-why-best-platform.md) (par exemple, prend en charge les CORS) et les [Applications Mobile](../app-service-mobile/app-service-mobile-value-prop.md) (par exemple, les notifications de type Pousser). Pour plus d’informations sur les types d’application dans le Service d’application, consultez [vue d’ensemble du Service d’application Azure](../app-service/app-service-value-prop-what-is.md).

Outre les applications Web dans le Service d’application, Azure offre des autres services qui peuvent être utilisés pour héberger des applications web et de sites Web. Pour la plupart des scénarios, les applications Web est le meilleur choix.  Architecture de microservice, compte [Service de Fabric](https://azure.microsoft.com/documentation/services/service-fabric)et si vous avez besoin de davantage de contrôle sur les ordinateurs virtuels que votre code s’exécute sur, envisagez [d’ordinateurs virtuels Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Pour plus d’informations sur la façon de choisir entre ces services Azure, consultez le [Service d’application Azure, Machines virtuelles, Service de Fabric et comparaison des Services en nuage](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Mise en route

Pour commencer par le déploiement des exemples de code pour une nouvelle application web dans le Service d’application, suivez le didacticiel de [déploiement de votre première application web vers Azure dans 5 minutes](app-service-web-get-started.md) . Vous aurez besoin d’un compte Azure gratuit.

Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.
