<properties
    pageTitle="Ajouter une authentification sur iOS avec les applications Azure Mobile"
    description="Apprenez à utiliser les applications Azure Mobile pour authentifier les utilisateurs de votre application iOS via un grand nombre de fournisseurs d’identité, y compris les DAS, Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-ios-app"></a>Ajouter l’authentification à votre application d’iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Dans ce didacticiel, vous ajoutez l’authentification pour le projet [d’e/s rapides démarrer] à l’aide d’un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [démarrage d’e/s rapides] , vous devez effectuer tout d’abord.

##<a name="register"></a>Enregistrer votre application pour l’authentification et de configurer le Service de l’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Dans Xcode, appuyez sur **exécuter** pour démarrer l’application. Une exception est levée parce que l’application tente d’accéder le serveur principal en tant qu’un utilisateur non authentifié, mais _TodoItem_ table requiert l’authentification.

##<a name="add-authentication"></a>Ajouter une authentification à l’application

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[démarrage rapide d’e/s]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
