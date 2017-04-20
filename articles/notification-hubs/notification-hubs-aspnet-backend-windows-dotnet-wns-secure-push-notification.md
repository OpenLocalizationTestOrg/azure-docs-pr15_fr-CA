<properties
    pageTitle="Concentrateurs de Notification Azure sécurisés Push"
    description="Apprenez à envoyer des notifications de transmission sécurisée dans Azure. Exemples de code écrits en C# à l’aide de l’API .NET."
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs" 
    ms.workload="mobile"
    ms.tgt_pltfrm="windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>Concentrateurs de Notification Azure sécurisés Push

> [AZURE.SELECTOR]
- [Universel de Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [e/s](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>Vue d’ensemble

Prise en charge des notifications push dans Microsoft Azure vous permet d’accéder à une infrastructure push facile à utiliser, multi-plateformes, à grande échelle, ce qui simplifie considérablement l’implémentation des notifications de type Pousser pour les consommateurs et les entreprise des applications pour les plates-formes mobiles.

En raison des réglementations ou des contraintes de sécurité, une application peut parfois inclure quelque chose dans la notification ne peut pas être transmise par le biais de l’infrastructure de notification de transmission standard. Ce didacticiel explique comment réaliser la même expérience en envoyant des informations sensibles via une connexion sécurisée et authentifiée entre le périphérique client et le serveur principal chargé de l’application.

À un niveau élevé, le flux est la suivante :

1. Application back-end :
    - Magasins transport de données sécurisé dans la base de données back-end.
    - Renvoie l’ID de la notification au périphérique (aucune information sécurisée n’est envoyée).
2. L’application sur le périphérique, lors de la réception de la notification :
    - Le périphérique contacte back-end demandant le transport de données sécurisé.
    - L’application peut afficher la charge utile sous la forme d’une notification sur le périphérique.

Il est important de noter que dans le flux précédent (et, dans ce didacticiel), nous partons du principe que l’appareil stocke un jeton d’authentification dans le stockage local, une fois que l’utilisateur se connecte. Cela garantit une expérience complètement transparente, comme le périphérique peut récupérer de données sécurisé de la notification à l’aide de ce jeton. Si votre application ne stocke pas les jetons d’authentification sur le périphérique, ou si ces jetons peuvent être expirés, l’application de périphérique, lors de la réception de la notification doit afficher une notification générique, demander à l’utilisateur pour lancer l’application. Ensuite, l’application authentifie l’utilisateur et indique la charge utile de notification.

Ce didacticiel de Push de sécuriser montre comment envoyer une notification de transmission en toute sécurité. Le didacticiel s’appuie sur le didacticiel [d’Avertir les utilisateurs](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , afin que vous devez effectuer tout d’abord les étapes dans ce didacticiel.

> [AZURE.NOTE] Ce didacticiel suppose que vous avez créé et configuré votre concentrateur de notification, comme décrit dans [Mise en route avec les concentrateurs de Notification (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
Notez également que Windows Phone 8.1 requiert des informations d’identification Windows (pas Windows Phone), et que les tâches en arrière-plan ne fonctionnent pas sur Windows Phone 8.0 ou 8.1 de Silverlight. Pour les applications du Windows Store, vous pouvez recevoir des notifications via une tâche d’arrière-plan uniquement si l’application est le verrou plein écran est activé (cliquez sur la case à cocher dans le Appmanifest).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modifier le projet Windows Phone

1. Dans le projet **NotifyUserWindowsPhone** , ajoutez le code suivant à App.xaml.cs pour enregistrer la tâche d’arrière-plan de push. Ajoutez la ligne de code suivante à la fin de la `OnLaunched()` méthode :

        RegisterBackgroundTask();

2. Toujours dans App.xaml.cs, ajoutez la ligne suivante de code immédiatement après le `OnLaunched()` méthode :

        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier App.xaml.cs :

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4. Dans le menu **fichier** dans Visual Studio, cliquez sur **Enregistrer tout**.

## <a name="create-the-push-background-component"></a>Création du composant d’arrière-plan Push

L’étape suivante consiste à créer le composant d’arrière-plan push.

1. Dans l’Explorateur de solutions, cliquez sur le nœud de niveau supérieur de la solution (**Solution SecurePush** dans ce cas), puis cliquez sur **Ajouter**, puis cliquez sur **Nouveau projet**.

2. Développez des **Applications Store**, cliquez sur **Les applications Windows Phone**, puis cliquez sur **Composant Windows Runtime (Windows Phone)**. Nommez le projet **PushBackgroundComponent**, puis cliquez sur **OK** pour créer le projet.

    ![][12]

3. Dans l’Explorateur de solutions, cliquez sur le projet **PushBackgroundComponent (8.1 de Windows Phone)** , puis cliquez sur **Ajouter**, puis sur **une classe**. Nom de la nouvelle classe **PushBackgroundTask.cs**. Cliquez sur **Ajouter** pour générer la classe.

4. Remplacez le contenu entier de la définition d’espace de noms **PushBackgroundComponent** par le code suivant, en remplaçant l’espace réservé `{back-end endpoint}` avec le point de terminaison du back-end obtenue lors du déploiement de votre back-end :

        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }

            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";

                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;

                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                    ShowToast(notification);

                    deferral.Complete();
                }

                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }

5. Dans l’Explorateur de solutions, cliquez sur le projet **PushBackgroundComponent (8.1 de Windows Phone)** et puis cliquez sur **Gérer les Packages NuGet**.

6. Sur le côté gauche, cliquez sur **en ligne**.

7. Dans la zone **Rechercher** , tapez **Http Client**.

8. Dans la liste des résultats, cliquez sur **Bibliothèques de Client HTTP Microsoft**, puis cliquez sur **installer**. Terminer l’installation.

9. Dans la zone de NuGet **recherche** , tapez **Json.net**. Installez le package **Json.NET** , puis fermez la fenêtre du Gestionnaire de package NuGet.

10. Ajoutez le code suivant `using` instructions en haut du fichier **PushBackgroundTask.cs** :

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. Dans l’Explorateur de solutions, dans le projet **NotifyUserWindowsPhone (8.1 de Windows Phone)** , cliquez sur **références**, puis cliquez sur **Ajouter une référence**. Dans la boîte de dialogue Gestionnaire de référence, cochez la case en regard de la **PushBackgroundComponent**, puis cliquez sur **OK**.

12. Dans l’Explorateur de solutions, double-cliquez sur **Package.appxmanifest** dans le projet **NotifyUserWindowsPhone (8.1 de Windows Phone)** . Sous **Notifications**, définissez **Toast Capable** à **Oui**.

    ![][3]

13. Toujours dans le **Package.appxmanifest**, cliquez sur le menu de **déclarations** vers le haut. Dans la liste déroulante **Des déclarations disponibles** , cliquez sur **Tâches en arrière-plan**, puis cliquez sur **Ajouter**.

14. Dans **Package.appxmanifest**, sous **Propriétés**, vérifier la **notification d’émission**.

15. Dans **Package.appxmanifest**, sous **Paramètres de l’application**, tapez **PushBackgroundComponent.PushBackgroundTask** dans le champ de **Point d’entrée** .

    ![][13]

16. Dans le menu **fichier** , cliquez sur **Enregistrer tout**.

## <a name="run-the-application"></a>Exécution de l’Application

Pour exécuter l’application, effectuez les opérations suivantes :

1. Dans Visual Studio, exécutez l’application Web API **AppBackend** . Une page web d’ASP.NET s’affiche.

2. Dans Visual Studio, exécutez l’application Windows Phone **NotifyUserWindowsPhone (8.1 de Windows Phone)** . L’émulateur Windows Phone s’exécute et charge l’application automatiquement.

3. Dans l’interface utilisateur d’application **NotifyUserWindowsPhone** , entrez un nom d’utilisateur et le mot de passe. Ce peuvent être n’importe quelle chaîne, mais ils doivent être de la même valeur.

4. Dans l’interface utilisateur d’application **NotifyUserWindowsPhone** , cliquez sur **Ouvrir une session et inscrire**. Cliquez ensuite sur **Envoyer push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
