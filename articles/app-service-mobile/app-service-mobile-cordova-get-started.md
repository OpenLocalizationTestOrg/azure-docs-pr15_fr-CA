<properties
    pageTitle="Créer une application de Cordova sur des applications mobiles de Service d’application Azure | Microsoft Azure"
    description="Suivez ce didacticiel pour se familiariser avec les serveurs principaux de l’application mobile Azure pour le développement d’Apache Cordova"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""
    tags=""
    keywords="cordova, javascript, les clients mobiles," />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-an-apache-cordova-app"></a>Créer un Apache Cordova app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel vous montre comment ajouter un service en nuage de back-end d’une application mobile Cordova de Apache à l’aide d’un back-end de l’application mobile Azure.  Vous allez créer un nouveau contexte d’application mobile et d’une simple _liste Todo_ , app Apache Cordova qui stocke les données d’application dans Azure.

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels Apache Cordova sur l’utilisation de la fonctionnalité d’applications Mobile dans le Service d’application Azure.

## <a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez les éléments suivants :

* Un ordinateur avec [Visual Studio 2015 de communauté] ou plus récente.
* [Visual Studio Tools pour Apache Cordova].
* Un [compte Azure actif](https://azure.microsoft.com/pricing/free-trial/).

Vous pouvez également contourner de Visual Studio, utilisez la ligne de commande Apache Cordova directement.  Cela est utile lors de l’exécution du didacticiel sur un ordinateur Mac.  Compiler des applications de client Apache Cordova à l’aide de la ligne de commande n’est pas couverte par ce didacticiel.

## <a name="create-a-new-azure-mobile-app-backend"></a>Créer un nouveau contexte d’application mobile Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Regardez une vidéo illustrant les étapes similaires](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Configurez le projet serveur

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Téléchargez et exécutez l’application Cordova d’Apache

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous terminé ce didacticiel de démarrage rapide, passez à un des didacticiels suivants :

* [Ajouter une authentification] à votre Apache Cordova app.
* [Ajouter des Notifications de type Pousser] votre Apache Cordova app.

Pour en savoir plus sur les concepts clés associés aux services d’application Azure.

* [Authentification]
* [Notifications de type Pousser]

Apprenez à utiliser les kits de développement logiciel.

* [Kit de développement logiciel Apache Cordova]
* [ASP.NET Server (SDK)]
* [Node.js Server (SDK)]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Communauté 2015]: http://www.visualstudio.com/
[Visual Studio Tools pour Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Ajouter l’authentification]: app-service-mobile-cordova-get-started-users.md
[Ajouter des Notifications de type Pousser]: app-service-mobile-cordova-get-started-push.md
[Authentification]: app-service-mobile-auth.md
[Notifications de type Pousser]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Kit de développement logiciel Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server (SDK)]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server (SDK)]: app-service-mobile-node-backend-how-to-use-server-sdk.md
