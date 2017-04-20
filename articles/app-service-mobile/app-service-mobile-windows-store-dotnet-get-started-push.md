<properties
    pageTitle="Ajouter des notifications de type pousser à votre application de plate-forme de Windows universel (UWP) | Applications mobiles Azure"
    description="Apprenez à utiliser le Service Azure App Apps Mobile et concentrateurs de Notification Azure pour envoyer des notifications de type pousser à votre application de plate-forme de Windows universel (UWP)."
    services="app-service\mobile,notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-windows-app"></a>Ajouter des notifications de type pousser à votre application Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Vue d’ensemble

Dans ce didacticiel, vous ajoutez des notifications de type Pousser au projet [Démarrer Windows rapides](app-service-mobile-windows-store-dotnet-get-started.md) afin qu’une notification de transmission est envoyée au périphérique chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur téléchargé démarrage rapide, vous devez le progiciel d’extension de notification push. Pour plus d’informations, voir [travail avec le serveur principal de .NET SDK pour les applications mobiles Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="configure-hub"></a>Configurer un concentrateur de Notification

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="register-your-app-for-push-notifications"></a>Enregistrer votre application pour les notifications de type Pousser

Vous devez soumettre votre application du Windows Store, puis configurer votre projet de serveur pour s’intégrer avec Windows Notification Services (WNS) pour envoyer la commande.

1. Dans l’Explorateur de solutions de Visual Studio, le projet d’application UWP avec le bouton droit, cliquez sur **banque de** > **Associer une application à la banque...**. 

    ![Associer des application Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
    
2. Dans l’Assistant, cliquez sur **suivant**, connectez-vous à votre compte Microsoft, tapez un nom pour votre application en **réserve un nouveau nom de l’application**, puis cliquez sur **réserver**.

3. Après que l’inscription de l’application est créée avec succès, sélectionnez le nouveau nom de l’application et cliquez sur **suivant**, puis cliquez sur **associer**. Cette opération ajoute les informations d’inscription de Windows Store requises au manifeste d’application.  

7. Accédez au [Centre de développement Windows](https://dev.windows.com/en-us/overview), connectez-vous à l’aide de votre compte Microsoft, cliquez sur la nouvelle immatriculation app dans **Mes applications**, puis développez **Services** > **notifications de transmission**. 

8. Dans la page **notifications de transmission** , cliquez sur **services Microsoft Live de site** sous **Microsoft Azure Mobile Services**.

9. Dans la page d’inscription, prenez note de la valeur sous les **secrets de l’Application** et le **SID du Package**, que vous utiliserez ensuite pour configurer votre back-end de l’application mobile. 

    ![Associer des application Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

    > [AZURE.IMPORTANT] Le secret client et SID sont des informations d’identification de sécurité important. Ne pas partager ces valeurs avec n’importe qui ou les distribuer avec votre application. L' **Id d’Application** est utilisé avec le mot de passe pour configurer l’authentification de Microsoft Account.

##<a name="configure-the-backend-to-send-push-notifications"></a>Configurer le serveur principal pour envoyer des notifications de type Pousser

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

##<a id="update-service"></a>Mise à jour au serveur d’envoyer des notifications de type Pousser

Utilisez la procédure ci-dessous correspondant à votre type de projet principal&mdash; [back-end de .NET](#dotnet) ou de [back-end de Node.js](#nodejs).

### <a name="dotnet"></a>Projet de back-end de .NET

1. Dans Visual Studio, droit sur le projet serveur et cliquez sur **Gérer les Packages NuGet**, recherchez Microsoft.Azure.NotificationHubs, puis cliquez sur **installer**. Cette procédure installe la bibliothèque client de concentrateurs de Notification.

2. Développez les **contrôleurs**, ouvrez TodoItemController.cs et ajoutez le code suivant à l’aide des instructions :

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

3. Dans la méthode **PostTodoItem** , ajoutez le code suivant après l’appel à **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Ce code indique le concentrateur de notification pour envoyer une notification de transmission après qu’un nouvel élément est l’insertion.

4. Publiez de nouveau le projet serveur.

### <a name="nodejs"></a>Projet de back-end Node.js

1. Si vous n’avez pas déjà fait, [Téléchargez le projet de démarrage rapide](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou bien utilisez l' [éditeur en ligne dans le portail Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Remplacez le code existant dans le fichier todoitem.js par le texte suivant :

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;

    Envoie une notification de toast WNS contenant le item.text lors de l’insertion d’un nouvel élément todo.

2. Lorsque vous modifiez le fichier sur votre ordinateur local, publiez de nouveau le projet serveur.

##<a id="update-app"></a>Ajouter des notifications de type pousser à votre application

Ensuite, votre application doit enregistrer pour les notifications de type Pousser au démarrage. Lorsque vous avez déjà activé l’authentification, assurez-vous que le signe de l’utilisateur avant d’essayer de s’enregistrer pour les notifications de transmission.

1. Ouvrez le fichier de projet **App.xaml.cs** et ajoutez le code suivant `using` instructions :

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;

2. Dans le même fichier, ajoutez la définition suivante de la méthode **InitNotificationsAsync** à la classe **App** :

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Ce code récupère la ChannelURI pour l’application à partir de WNS et inscrit ce ChannelURI avec votre application Mobile du Service application.

3. En haut du Gestionnaire d’événements **OnLaunched** dans **App.xaml.cs**, ajoutez le modificateur **async** pour la définition de méthode et l’appel suivant à la nouvelle méthode **InitNotificationsAsync** , comme dans l’exemple suivant :

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    Cela garantit que le ChannelURI de courte durée de vie est enregistré chaque fois que l’application est lancée.

4. Régénérez votre projet d’application UWP. Votre application est prête à recevoir des notifications de toast.

##<a id="test"></a>Notifications de type Pousser de test dans votre application

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>Étapes suivantes

Pour en savoir plus sur les notifications de transmission :

* [Comment faire pour utiliser le client géré pour les applications mobiles Azure](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications)  
Les modèles permettent de vous envoyer multiplates-pousse et pousse localisée. Découvrez comment enregistrer les modèles.

* [Diagnostiquer les problèmes de notification de transmission](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Il existe diverses raisons pourquoi les notifications peuvent être perdues ou ne finissent pas sur les périphériques. Cette rubrique vous indique comment analyser et déterminer la cause des échecs de notification de transmission. 

Envisagez de passer à un des didacticiels suivants :

+ [Ajouter l’authentification à votre application](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Apprenez à authentifier les utilisateurs de votre application avec un fournisseur d’identité.

+ [Activer la synchronisation hors connexion pour votre application](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Découvrez comment ajouter la prise en charge en mode hors connexion de votre application à l’aide d’un back-end de l’application Mobile. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;affichage, ajout ou modification de données&mdash;même lorsqu’il n’y a pas de connexion réseau.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

