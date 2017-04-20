<properties
    pageTitle="Ajouter des Notifications de type pousser à Android application Azure applications mobiles"
    description="Apprenez à utiliser les applications Azure Mobile pour envoyer des notifications de type pousser à votre application d’Android."
    services="app-service\mobile"
    documentationCenter="android"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-android-app"></a>Ajouter des Notifications de type pousser à votre application d’Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d’ensemble
Dans ce didacticiel, vous ajoutez des notifications de type Pousser au projet [Android démarrage rapide] afin qu’une notification de transmission est envoyée au périphérique chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez le progiciel d’extension de notification push. Pour plus d’informations, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Conditions préalables

Vous devez :

* Une interface IDE en fonction du back-end de votre projet :

    * [Studio Android](https://developer.android.com/sdk/index.html) si cette application dispose d’un back-end Node.js.

    * [2013 de communauté de Visual Studio](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou une version ultérieure si cette application a un back-end de .net.

* Android 2.3 ou supérieur, la révision de Google référentiel 27 ou plue et Services de Google Play 9.0.2 ou version ultérieure pour la messagerie de nuage Firebase.

* Terminer la [main Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Créez un projet qui prend en charge la messagerie de nuage Firebase

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurer un concentrateur de Notification

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurer Azure pour envoyer des notifications de type Pousser

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Activer les notifications de type Pousser du projet serveur

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Ajouter des notifications de type pousser à votre application

Dans cette section, vous mettez à jour votre application d’Android client pour gérer les notifications de transmission.

### <a name="verify-android-sdk-version"></a>Vérifiez la Version du Kit de développement logiciel Android

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

L’étape suivante consiste à installer des services de lecture de Google. Messagerie de nuage Google a certaines API niveau exigences minimales pour le développement et le test, la propriété **minSdkVersion** dans le manifeste doit être conforme à.

Si vous testez avec un périphérique plus ancien, puis consultez [Définir des Google lire SDK Services] pour déterminer la vous pouvez définir cette valeur et la définir en conséquence.

### <a name="add-google-play-services-to-the-project"></a>Ajouter des Services de lecture de Google pour le projet

[AZURE.INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>Ajoutez le code

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testez l’application contre le service mobile publié

Vous pouvez tester l’application en directement en attachant un téléphone Android avec un câble USB, ou à l’aide d’un périphérique virtuel de l’émulateur.

## <a name="more"></a>Plus

<!-- URLs -->
[Android démarrage rapide]: app-service-mobile-android-get-started.md

[Configurer des Services de lecture de Google SDK]:https://developers.google.com/android/guides/setup
