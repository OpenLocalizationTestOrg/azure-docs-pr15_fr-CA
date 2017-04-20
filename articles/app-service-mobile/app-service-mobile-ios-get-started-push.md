<properties
    pageTitle="Ajouter pousser des Notifications pour les e/s d’application avec les applications Azure Mobile"
    description="Apprenez à utiliser les applications Azure Mobile pour envoyer des notifications de type pousser à votre application d’e/s."
    services="app-service\mobile"
    documentationCenter="ios"
    manager="yochayk"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="yuaxu"/>


# <a name="add-push-notifications-to-your-ios-app"></a>Ajouter des Notifications de pousser à votre application d’iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d’ensemble
Dans ce didacticiel, vous ajoutez des notifications de type pousser [l’e/s rapides démarrer] le projet afin qu’une notification de transmission est envoyée au périphérique chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez le progiciel d’extension de notification push. Pour plus d’informations, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

Le [simulateur d’e/s ne gère pas les notifications de transmission](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Vous avez besoin d’un périphérique d’e/s physique et une [participation au programme de développement d’Apple](https://developer.apple.com/programs/ios/).

##<a name="configure-hub"></a>Configurer le concentrateur de Notification

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Enregistrer l’application de notifications de type Pousser

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurer Azure pour envoyer des notifications de type Pousser

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Mise à jour de back-end pour envoyer des notifications de type Pousser

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Ajouter des notifications de type pousser à l’application

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Notifications de type Pousser de test

[AZURE.INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>Plus

* Les modèles permettent de vous envoyer multiplates-pousse et pousse localisée. [Comment faire pour utiliser iOS bibliothèque Client pour les applications mobiles Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) vous explique comment enregistrer les modèles.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[démarrage rapide d’e/s]: app-service-mobile-ios-get-started.md
