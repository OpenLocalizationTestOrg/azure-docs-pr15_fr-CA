<properties
    pageTitle="Envoyer des notifications de multiplates-formes aux utilisateurs avec les concentrateurs Notification (ASP.NET)"
    description="Apprenez à utiliser les modèles de concentrateurs de Notification à envoyer, dans une demande unique, une notification indépendant de la plateforme cible de toutes les plates-formes."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Envoyer des notifications de multiplates-formes aux utilisateurs avec des concentrateurs de Notification


Dans le didacticiel précédent, [avertir les utilisateurs avec des concentrateurs de Notification], vous avez appris à envoyer des notifications à tous les périphériques sont enregistrés par un utilisateur authentifié spécifique. Dans ce didacticiel, plusieurs demandes étaient requis pour envoyer une notification à chaque plate-forme client pris en charge. Notification de concentrateurs prend en charge les modèles, qui vous permettent de spécifier comment un périphérique spécifique souhaite recevoir des notifications. Ceci simplifie l’envoi de notifications de multiplates-formes. Cette rubrique montre comment tirer parti des modèles à envoyer, dans une demande unique, une notification indépendant de la plateforme cible de toutes les plates-formes. Pour plus d’informations sur les modèles, consultez la [Vue d’ensemble des concentrateurs Azure Notification][Templates].

> [AZURE.NOTE] Concentrateurs de notification permet à un périphérique d’enregistrer plusieurs modèles avec la même balise. Dans ce cas, un message entrant ciblant cette balise entraîne plusieurs notifications sont remies à l’appareil, un pour chaque modèle. Cela vous permet d’afficher le même message dans plusieurs notifications visual, comme à la fois comme un badge et sous la forme d’une notification de toast dans une application du Windows Store.

Procédez comme suit pour envoyer des notifications de plusieurs plates-formes à l’aide de modèles :

1. Dans l’Explorateur de solutions dans Visual Studio, développez le dossier **Controllers** , puis ouvrez le fichier RegisterController.cs.

2. Localisez le bloc de code dans la méthode **Post** qui crée un nouveau remplacement de l’enregistrement du `switch` contenu par le code suivant :

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

    Ce code appelle la méthode propre à la plate-forme pour créer un enregistrement de modèle au lieu d’un enregistrement natif. Les enregistrements existants ne doivent pas être modifiées car des enregistrements de modèle dérivent à partir des enregistrements natifs.

3. Dans le contrôleur de **Notifications** , remplacez la méthode **sendNotification** avec le code suivant :

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

    Ce code envoie une notification à toutes les plates-formes en même temps et sans avoir à spécifier une charge utile native. Concentrateurs de notification conçoit et livre de la charge utile correcte à chaque périphérique avec la valeur fournie de _balise_ , comme indiqué dans les modèles enregistrés.

4. Publier à nouveau votre projet principal de WebApi.

5. Exécutez de nouveau l’application client et vérifiez que l’inscription a réussi.

6. (Facultatif) Déploiement de l’application cliente vers un second périphérique, puis exécutez l’application.

    Notez qu’une notification s’affiche sur chaque périphérique.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, trouver plus d’informations sur les concentrateurs de Notification et des modèles dans ces rubriques :

+ **[Concentrateurs de Notification permet d’envoyer des informations de dernière minute]** <br/>Montre un autre scénario illustrant l’utilisation de modèles

+  **[Vue d’ensemble des concentrateurs de Notification Azure][Templates]**<br/>Rubrique de présentation a des informations plus détaillées sur les modèles.


<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Concentrateurs de Notification permet d’envoyer des informations de dernière minute]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Avertir les utilisateurs avec des concentrateurs de Notification]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
