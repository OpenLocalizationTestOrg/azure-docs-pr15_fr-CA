<properties
    pageTitle="Ajouter des notifications de type pousser à votre application Xamarin.Android | Service d’application Azure"
    description="Apprenez à utiliser le Service d’application Azure et d’Azure Notification concentrateurs pour envoyer des notifications de type pousser à votre application Xamarin.Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Ajouter des notifications de type pousser à votre application Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Vue d’ensemble


Dans ce didacticiel, vous ajoutez les notifications de transmission au projet [Xamarin.Android rapide démarrer](app-service-mobile-windows-store-dotnet-get-started.md) afin qu’une notification de transmission est envoyée au périphérique chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez le progiciel d’extension de notification push. Pour plus d’informations, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .


##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ Un compte Google actif. Vous pouvez vous inscrire pour un compte Google à [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
+ Le [composant Client de messagerie de nuage de Google](http://components.xamarin.com/view/GCMClient/).

##<a name="configure-hub"></a>Configurer un concentrateur de Notification

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a id="register"></a>Activer la Firebase de messagerie en nuage

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

##<a name="configure-azure-to-send-push-requests"></a>Configurer Azure pour envoyer des demandes de transmission

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

##<a id="update-server"></a>Mettre à jour le projet serveur pour envoyer des notifications de type Pousser

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Configurez le projet de client pour les notifications de type Pousser

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Ajouter code de notifications push pour votre application

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Notifications de type Pousser de test dans votre application

Vous pouvez tester l’application à l’aide d’un périphérique virtuel de l’émulateur. Il existe des étapes de configuration supplémentaires requises lors de l’exécution sur un émulateur.

1. Assurez-vous que vous déployez à ou le débogage sur un périphérique virtuel qui a des APIs de Google défini comme cible, comme indiqué dans le Gestionnaire de périphérique virtuel Android (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Ajouter un compte Google Android périphérique en cliquant sur **applications** > **paramètres** > **Ajouter un compte**, puis suivez les invites.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Exécutez l’application de la liste des tâches comme avant et insérer un nouvel élément todo. Cette fois-ci, une icône de notification s’affiche dans la zone de notification. Vous pouvez ouvrir le tiroir de notification pour afficher le texte intégral de la notification.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
