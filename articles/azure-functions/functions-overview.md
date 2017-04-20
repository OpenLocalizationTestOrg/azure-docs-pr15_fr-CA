<properties
   pageTitle="Vue d’ensemble des fonctions de Azure | Microsoft Azure"
   description="Comprendre comment utiliser les fonctions d’Azure afin d’optimiser les charges de travail asynchrones en quelques minutes."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# <a name="azure-functions-overview"></a>Vue d’ensemble des fonctions Azure

Fonctions Azure est une solution pour exécuter facilement les petits morceaux de code, ou « fonctions », dans le nuage. Vous pouvez écrire tout le code que nécessaire pour le problème à portée de main, sans se soucier de la totalité de l’application ou de l’infrastructure pour l’exécuter. Cela peut rendre le développement encore plus productif, et vous pouvez utiliser votre langage de développement de choix, tels que C#, F#, Node.js, Python ou PHP. Payez uniquement pour l’exécution de votre code et approbation Azure mise à l’échelle en fonction des besoins.

Cette rubrique fournit une vue d’ensemble des fonctions d’Azure. Si vous souhaitez lancer et commencer à utiliser les fonctions d’Azure, commencer par [créer votre première fonction Azure](functions-create-first-azure-function.md). Si vous cherchez des informations techniques supplémentaires sur les fonctions, consultez la [référence du développeur](functions-reference.md).

## <a name="features"></a>Fonctionnalités

Voici quelques fonctionnalités clées des fonctions d’Azure :
    
* **Choix de langue** - fonctions d’écriture à l’aide de C#, F#, Node.js, Python, PHP, lot, bash, Java ou un fichier exécutable.
* **Modèle de tarification de paie-utilisation** - salaire uniquement pour la durée d’exécution de votre code. Consultez l’option de Plan de Service des applications dynamiques dans la [section de tarification](#pricing) ci-dessous.  
* **Mettre vos propres dépendances** - fonctions prend en charge NuGet et NPM, vous pouvez utiliser vos bibliothèques préférées.  
* **Sécurité intégrée** - déclenché par HTTP de protéger les fonctions avec les fournisseurs OAuth Azure Active Directory, Facebook, Google, Twitter, des Account de Microsoft.  
* **Intégration de simplifié** - facilement tirer parti des services Azure et les offres de logiciels-as-a-service (SaaS). Reportez-vous à la [section des intégrations](#integrations) pour obtenir des exemples.  
* **Développement flexible** - Code votre droit de fonctions dans le portail ou paramétrer l’intégration continue et déployer votre code via GitHub, Visual Studio Team Services et autres [Outils de développement de prise en charge](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Open source** - fonctions runtime est libre et [disponible sur GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Que puis-je faire avec les fonctions ?

Fonctions Azure est une excellente solution de traitement des données, intégration des systèmes, utilisation de l’internet-de-objets (IoT) et la création de microservices et les API simples. Envisagez de fonctions pour des tâches telles que le traitement de l’image ou de la commande, maintenance des fichiers, tâches à exécution longue que vous souhaitez exécuter dans un thread d’arrière-plan, ou pour toutes les tâches que vous souhaitez exécuter selon une planification. 

Fonctions fournit des modèles pour vous aider à démarrer avec des scénarios clés, notamment les suivantes :

* **BlobTrigger** - BLOB le stockage Azure processus lorsqu’ils sont ajoutés aux conteneurs. Vous pouvez utiliser ce pour le redimensionnement de l’image.
* **EventHubTrigger** - en réponse à des événements de rendu à un concentrateur d’événements Azure. Particulièrement utile dans les scénarios Internet des objets (IoT), traitement des flux de travail ou d’expérience utilisateur et instrumentation de l’application.
* **Webhook générique** - processus webhook HTTP demande à partir de n’importe quel service qui prend en charge les webhooks.
* **GitHub webhook** - répondre aux événements qui se produisent dans vos référentiels GitHub. Pour obtenir un exemple, voir [créer un webhook ou la fonction API](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** - déclencheur de l’exécution de votre code à l’aide d’une requête HTTP.
* **QueueTrigger** - répondre aux messages lorsqu’ils arrivent dans une file d’attente de stockage Azure. Pour obtenir un exemple, voir [Création d’une fonction d’Azure qui le lie à un service d’Azure](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** - connecter votre code à d’autres services Azure ou des services sur site en écoutant les files d’attente. 
* **ServiceBusTopicTrigger** - connecter votre code à d’autres services Azure ou des services sur site en vous abonnant aux rubriques. 
* **TimerTrigger** - exécuter le nettoyage ou autres tâches de traitement par lots selon un programme prédéfini. Pour obtenir un exemple, consultez [créer une fonction de traitement des événements](functions-create-an-event-processing-function.md).

Fonctions Azure prend en charge les *déclencheurs*, qui sont des moyens pour démarrer l’exécution de votre code et les *liaisons*, qui sont des moyens pour simplifier le codage de données d’entrée et de sortie. Pour obtenir une description détaillée des déclencheurs et des liaisons qui fournit des fonctions d’Azure, consultez [fonctions d’Azure déclencheurs et la référence du développeur de liaisons](functions-triggers-bindings.md).


## <a name="integrations"></a>Intégrations

Fonctions Azure s’intègre à une variété d’Azure et 3 rd-party services. Vous pouvez utiliser ces déclencher votre fonction et le démarrage de l’exécution ou à servir d’entrée et de sortie pour votre code. Les intégrations de service suivantes sont prises en charge par les fonctions d’Azure. 

* DocumentDB Azure
* Événement Azure concentrateurs 
* Applications Mobile Azure (tables)
* Concentrateurs de Notification Azure
* Bus des services Azure (files d’attente et rubriques)
* Stockage Azure (blob, files d’attente et les tables) 
* GitHub (webhooks)
* Sur site (à l’aide du Bus de Service)

## <a name="pricing"></a>Combien de fonctions coût ?

Fonctions Azure a deux sortes de plans, choisissez celui qui vous convient le mieux à vos besoins : 

* **Plan de l’hébergement dynamique** - lors de l’exécution de votre fonction, Azure fournit toutes les ressources de calculs nécessaires. Vous n’avez pas à vous soucier de la gestion des ressources et vous ne payez que pour le temps que votre code s’exécute. Les détails de tarification complètes sont disponibles sur la [page fonctions de prix](/pricing/details/functions). 

* **Plan de Service d’application** - de l’exécution de vos fonctions comme votre web, mobile et applications de l’API. Lorsque vous utilisez déjà le Service d’application pour les autres applications, vous pouvez exécuter les fonctions sur le même plan sans coût supplémentaire. Vous trouverez plus de détails sur la [page application des prix Service](/pricing/details/app-service/).

Pour plus d’informations sur les fonctions de mise à l’échelle, voir [Comment faire évoluer les fonctions Azure](functions-scale.md).

##<a name="next-steps"></a>Étapes suivantes

+ [Créer votre première fonction Azure](functions-create-first-azure-function.md)  
Lancer et créer votre première fonction à l’aide des fonctions d’Azure Démarrages rapides. 
+ [Référence du développeur de fonctions Azure](functions-reference.md)  
Fournit des informations techniques supplémentaires sur l’exécution de fonctions d’Azure et une référence pour les fonctions de codage et de définition des déclencheurs et des liaisons.
+ [Test des fonctions Azure](functions-test-a-function.md)  
Décrit les divers outils et techniques pour le test de vos fonctions.
+ [Comment faire évoluer les fonctions Azure](functions-scale.md)  
Décrit des plans de service disponibles avec fonctions d’Azure, y compris le plan de service dynamique et comment choisir la solution adaptée. 
+ [En savoir plus sur le Service d’application Azure](../app-service/app-service-value-prop-what-is.md)  
Fonctions Azure s’appuie sur la plate-forme de services d’application Azure pour les fonctionnalités principales telles que les déploiements, les variables d’environnement et les tests de diagnostic. 