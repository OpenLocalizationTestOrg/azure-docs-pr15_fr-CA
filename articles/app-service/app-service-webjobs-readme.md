<properties
    pageTitle="WebJobs dans le Service d’application Azure"
    description="Apprenez à créer des WebJobs pour exécuter les tests de l’arrière-plan et interagir avec des services tels que le Bus des services de stockage et de créer des tâches planifiées."
    services="app-service"
    documentationCenter=""
    authors="christopheranderson"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="chrande"/>

# <a name="using-webjobs-in-azure-app-service"></a>À l’aide de WebJobs dans le Service d’application Azure

Cet article liens vers des ressources de documentation sur l’utilisation d’Azure WebJobs et le WebJobs d’Azure SDK. WebJobs Azure fournissent un moyen simple pour exécuter des scripts ou des programmes en tant que processus d’arrière-plan sur [l’Application de Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Vous pouvez télécharger et exécuter un fichier exécutable comme comme cmd, bat, exe (.NET), ps1, sh, php, py, js et jar. Ces programmes s’exécutent en tant que WebJobs selon une planification (cron) ou en continu.

Le SDK WebJobs rend plus facile d’utiliser le stockage Azure. Le SDK WebJobs a une liaison et un système de déclencheur qui fonctionne avec Microsoft Azure stockage BLOB, files d’attente et Tables ainsi que files d’attente de Bus de Service.

Création, déploiement et gestion de WebJobs sont parfaitement compatible avec les outils intégrés dans Visual Studio. Vous pouvez créer WebJobs à partir de modèles, publier et gérer (run/stop/moniteur/debug) les.

WebJobs tableau de bord dans le portail Azure fournit de puissantes fonctions de gestion qui vous donnent un contrôle total sur l’exécution de WebJobs, y compris la possibilité d’appeler des fonctions dans WebJobs. Le tableau de bord affiche également les exécutions de la fonction et la sortie de journalisation.

[AZURE.INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]
