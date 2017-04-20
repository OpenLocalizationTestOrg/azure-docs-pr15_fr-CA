<properties
    pageTitle="Créer une application d’Android sur des applications mobiles de Service d’application Azure | Microsoft Azure"
    description="Suivez ce didacticiel pour se familiariser avec les serveurs principaux de l’application mobile Azure pour le développement Android"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

#<a name="create-an-android-app"></a>Créer une application d’Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel vous montre comment ajouter un service en nuage de back-end d’une application mobile Android à l’aide d’un back-end de l’application mobile Azure.  Vous allez créer un nouveau contexte d’application mobile et d’une simple _liste Todo_ , Android application qui stocke les données d’application dans Azure.

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels Android sur l’utilisation de la fonctionnalité d’applications Mobile dans le Service d’application Azure.

## <a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez les éléments suivants :

* [Les outils de développement android](https://developer.android.com/sdk/index.html), qui inclut l’environnement de développement intégré Android et la dernière plate-forme Android.
* Azure Mobile Android SDK, qui est référencé automatiquement dans le cadre du projet de démarrage rapide que vous téléchargez.
* Un [compte Azure actif](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Créer un nouveau contexte d’application mobile Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurez le projet serveur

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-android-app"></a>Téléchargez et exécutez l’application Android

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
