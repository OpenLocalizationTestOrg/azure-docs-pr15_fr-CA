<properties
    pageTitle="Service d’application Azure : Les Applications de Service d’application mise à l’échelle"
    description="Découvrez les coulisses de la mise à l’échelle d’Application dans le Service d’application."
    keywords="application service, azure application service, plan de service application évolutive, échelle, coût de service d’application"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-scaling-app-service-applications"></a>Service d’application Azure : Les Applications de Service d’application mise à l’échelle

Applications hébergées dans le Service d’application Azure peuvent [parvenir à grande échelle](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/).
Toutefois, la mise à l’échelle d’une application est un problème complexe qui ne dispose pas d’une solution « passe-partout ». À correctement à l’échelle votre application il sont 3 domaines clés qui contribueront à la réussite de vos applications :

1. Comprendre l’architecture de votre application et ses faiblesses.
    * Est l’état de votre Application ? Sans état ?
    * Ce sont tous les composants de votre application ?
        * Où sont les goulots d’étranglement de l’application ?
    * Lorsque la charge est appliquée à votre application, ce qui se brisera en premier ?
2. Comprendre les exigences de performances et de la charge attendues.
    * L’application a besoin pour servir de mille utilisateurs ? ou un million ?
    * Le trafic arriveront à partir d’un emplacement géographique unique ou global ?
    * Y a-t-il des variations saisonnières ? pics de trafic ?
    * La vitesse à laquelle l’application doit y répondre ? 1 seconde ? 1 milliseconde ?
3. Comprendre et exploiter correctement de la plate-forme hébergeant votre application.
    * Quelles sont les fonctionnalités dois-je utiliser pour atteindre mes objectifs d’échelle ?

Cette section vous aidera à comprendre les facteurs et les aide à vous concevoir une stratégie qui tire parti des fonctionnalités de Service d’application nécessaires pour atteindre vos objectifs d’évolutivité.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]
