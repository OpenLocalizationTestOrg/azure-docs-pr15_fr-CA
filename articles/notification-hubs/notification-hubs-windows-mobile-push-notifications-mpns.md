<properties
    pageTitle="Envoi de notifications de type Pousser avec Azure Notification Hubs sur Windows Phone | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser des concentrateurs de Notification Azure pour les notifications de type pousser à une application Windows Phone 8 ou Windows Phone 8.1 Silverlight."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="notification d’émission, la notification, d’émission par émission de windows phone"
    authors="ysxu"
    manager="erikre"
    editor="erikre"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Envoi de notifications de type Pousser avec Azure Notification Hubs sur Windows Phone

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

> [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

Ce didacticiel vous montre comment utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type pousser à une application Windows Phone 8 ou Windows Phone 8.1 Silverlight. Si vous ciblez Windows Phone 8.1 (non-Silverlight), puis consultez la version [Windows universel](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
Dans ce didacticiel, vous créez une application Windows Phone 8 vide qui reçoit des notifications de type Pousser en utilisant le Service de Notification des émetteurs de Microsoft (MPNS). Lorsque vous avez terminé, vous serez en mesure d’utiliser votre concentrateur de notification à la diffusion des notifications de type pousser à tous les périphériques de l’exécution de votre application.

> [AZURE.NOTE] Le Kit de développement de Windows Phone concentrateurs Notification ne gère pas le Service de Notification Push Windows (WNS) avec les applications Windows Phone 8.1 Silverlight. Pour utiliser WNS (au lieu de MPNS) avec les applications Silverlight de Windows Phone 8.1, suivez les [Concentrateurs Notification - didacticiel de Silverlight de Windows Phone], qui utilise l’API de reste.

Le didacticiel illustre le scénario de diffusion simple à l’aide de concentrateurs de Notification.

##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ [Visual Studio 2012 Express pour Windows Phone], ou une version ultérieure.

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels de concentrateurs de Notification pour les applications Windows Phone 8.

##<a name="create-your-notification-hub"></a>Créer votre concentrateur de notification

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Cliquez sur la section <b>Notification Services</b> (dans les <i>paramètres</i>), cliquez sur <b>Windows Phone (MPNS)</b> et puis cliquez sur la case à cocher <b>Activer la transmission non authentifiée</b> .</p>
</li>
</ol>

&emsp;&emsp;![Azure Portal - activer les notifications de transmission unathenticated](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

Votre concentrateur est maintenant créé et configuré pour envoyer une notification non authentifiée pour Windows Phone.

> [AZURE.NOTE] Ce didacticiel utilise MPNS en mode non authentifié. Mode non authentifié de MPNS est fourni avec des restrictions sur les notifications que vous pouvez envoyer à chaque canal. Concentrateurs de notification prend en charge les [MPNS authentifié en mode](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) en vous permettant de télécharger votre certificat.

##<a name="connecting-your-app-to-the-notification-hub"></a>Connexion de votre application pour le concentrateur de notification

1. Dans Visual Studio, créez une nouvelle application Windows Phone 8.

    ![Visual Studio - projet - Windows Phone App][13]

    Dans Visual Studio 2013 2 ou version ultérieure, vous créez à la place d’une application Silverlight de Windows Phone.

    ![Visual Studio - projet - Blank App - Windows Phone Silverlight][11]

2. Dans Visual Studio, avec le bouton droit de la solution, puis cliquez sur **Gérer les Packages NuGet**.

    Cela affiche la boîte de dialogue **Gérer les Packages NuGet** .

3. Recherchez `WindowsAzure.Messaging.Managed` puis cliquez sur **installer**, puis acceptez les conditions d’utilisation.

    ![Visual Studio - du Gestionnaire de package NuGet][20]

    Il télécharge, installe et ajoute une référence à la bibliothèque de messagerie Azure pour Windows en utilisant le <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">package NuGet de WindowsAzure.Messaging.Managed</a>.

4. Ouvrez le fichier App.xaml.cs et ajoutez le code suivant `using` instructions :

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Ajoutez le code suivant en haut de la méthode **Application_Launching** dans App.xaml.cs :

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });

    >[AZURE.NOTE] La valeur **MyPushChannel** est un index qui est utilisé pour rechercher un canal existant dans la collection [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) . Si il n’est pas un il, créez une nouvelle entrée portant ce nom.
    
    Veillez à insérer le nom de votre concentrateur et la chaîne de connexion appelée **DefaultListenSharedAccessSignature** que vous avez obtenu dans la section précédente.
    Ce code extrait l’URI du canal pour l’application à partir de MPNS et inscrit ce canal URI avec votre concentrateur de notification. Il garantit également la chaîne QU'URI est enregistré dans votre centre de notification chaque fois que l’application est lancée.

    >[AZURE.NOTE]Ce didacticiel envoie une notification toast au périphérique. Lorsque vous envoyez une notification de mosaïque, vous devez appeler la méthode **BindToShellTile** sur le canal. Pour prendre en charge les deux toast et notifications de mosaïque, appeler à la fois **BindToShellTile** et **BindToShellToast**.

6. Dans l’Explorateur de solutions, développez **Propriétés**, ouvrez le `WMAppManifest.xml` de fichier et cliquez sur l’onglet **fonctions** , assurez-vous que la fonction **ID_CAP_PUSH_NOTIFICATION** est activée.

    ![Visual Studio - Windows Phone des fonctionnalités de l’application][14]

    Cela garantit que votre application peut recevoir des notifications de type Pousser. Sans lui, toute tentative d’envoi d’une notification de transmission à l’application échouera.

7. Appuyez sur le `F5` clé pour exécuter l’application.

    Un message d’enregistrement s’affiche dans l’application.

8. Fermez l’application.  

   >[AZURE.NOTE] Pour recevoir une notification de transmission toast, l’application ne doit pas s’exécuter au premier plan.

##<a name="send-push-notifications-from-your-backend"></a>Envoyer des notifications de type Pousser depuis votre back-end

Vous pouvez envoyer des notifications de type pousser à l’aide de concentrateurs de Notification à partir de n’importe quel back-end via <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">l’interface REST</a>public. Dans ce didacticiel, vous envoyez des notifications de type pousser à l’aide d’une application console .NET. 

Pour obtenir un exemple de l’envoi de notifications de type pousser à partir d’un back-end WebAPI d’ASP.NET qui est intégré avec les concentrateurs de Notification, reportez-vous à la section [Azure Notification concentrateurs avertir les utilisateurs avec le serveur principal .NET](./notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md).  

Pour obtenir un exemple de l’envoi de notifications de type pousser à l’aide de l' [API du reste](https://msdn.microsoft.com/library/azure/dn223264.aspx), Découvrez [comment utiliser des concentrateurs de Notification à partir de Java](./notification-hubs-java-push-notification-tutorial.md) et de [l’utilisation de concentrateurs de Notification à partir de PHP](./notification-hubs-php-push-notification-tutorial.md).

1. Avec le bouton droit de la solution, sélectionnez **Ajouter** , **Nouveau projet...**, sous **Visual C#**, cliquez sur **Windows** et **Application Console**, puis cliquez sur **OK**.

    ![Application de Console Visual Studio - projet-][6]

    Cette opération ajoute une nouvelle Visual C# application console à la solution. Vous pouvez également le faire dans une solution distincte.

4. Cliquez sur **Outils**et cliquez sur **Gestionnaire de package de bibliothèque**, puis cliquez sur **Console de Gestionnaire de package**.

    Cette option affiche la Console du Gestionnaire de package.

5.  La valeur **par défaut du projet de** votre projet d’application console dans la fenêtre de la **Console du Gestionnaire de package** et puis dans la fenêtre de la console, exécutez la commande suivante :

        Install-Package Microsoft.Azure.NotificationHubs

    Cette opération ajoute une référence à la Notification de concentrateurs Azure SDK à l’aide du <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">package Microsoft.Azure.Notification concentrateurs NuGet</a>.

6. Ouvrir le `Program.cs` de fichiers et ajoutez le code suivant `using` instruction :

        using Microsoft.Azure.NotificationHubs;

6. Dans le `Program` de classe, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    Veillez à remplacer le `<hub name>` espace réservé avec le nom du concentrateur de notification qui s’affiche dans le portail. De même, remplacez l’espace réservé chaîne de connexion avec la chaîne de connexion appelée **DefaultFullSharedAccessSignature** que vous avez obtenu dans la section « Configurer votre concentrateur de notification ».

    >[AZURE.NOTE]Assurez-vous que vous utilisez la chaîne de connexion avec un accès **complet** , ne pas **écouter** les accès. La chaîne d’écoute à l’accès n’a pas les autorisations pour envoyer des notifications de type Pousser.

4. Ajoutez la ligne suivante dans votre `Main` méthode :

         SendNotificationAsync();
         Console.ReadLine();

5. Avec votre émulateur de Windows Phone en cours d’exécution et votre application fermée, définissez le projet d’application console en tant que projet de démarrage par défaut, puis appuyez sur le `F5` clé pour exécuter l’application.

    Vous recevrez une notification de transmission toast. Cliquez sur la bannière toast charge l’application.

Vous pouvez trouver toutes les charges possibles dans les rubriques du [catalogue de toast] et [catalogue de mosaïque] sur MSDN.

##<a name="next-steps"></a>Étapes suivantes

Dans cet exemple simple, vous envoyé des notifications de type pousser à tous les périphériques Windows Phone 8. 

Pour cibler des utilisateurs spécifiques, reportez-vous au didacticiel [Utilisation des concentrateurs de Notification pour les notifications de type Pousser aux utilisateurs] . 

Si vous souhaitez que segmenter les utilisateurs en groupes d’intérêt, vous pouvez lire les [Concentrateurs de Notification utilisé pour envoyer des informations de dernière minute]. 

Pour en savoir plus sur l’utilisation de concentrateurs de Notification dans le [Guide de concentrateurs de Notification].



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio Express de 2012 pour Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Conseils de concentrateurs de notification]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Utiliser des concentrateurs de Notification pour les notifications de type Pousser aux utilisateurs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Concentrateurs de Notification permet d’envoyer des informations de dernière minute]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[catalogue de toast]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catalogue de mosaïque]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Concentrateurs de notification - didacticiel de Silverlight de Windows Phone]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

