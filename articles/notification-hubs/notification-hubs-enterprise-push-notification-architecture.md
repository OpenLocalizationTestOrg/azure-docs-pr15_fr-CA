<properties
    pageTitle="Concentrateurs de notification - Architecture d’entreprise Push"
    description="Conseils sur l’utilisation de concentrateurs de Notification Azure dans un environnement d’entreprise"
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="enterprise-push-architectural-guidance"></a>Guide architecture d’entreprise push

Les entreprises se tournent aujourd'hui progressivement vers la création d’applications mobiles pour leurs utilisateurs finaux (externe) ou pour les employés (internes). Ils ont des systèmes back-end existant en place qu’il s’agisse d’ordinateurs centraux ou certaines applications cœur de métier qui doivent être intégrées dans l’architecture de l’application mobile. Ce guide traite de la meilleure façon de faire cette intégration recommandant des solutions possibles pour les scénarios courants.

Une exigence fréquente est pour l’envoi de notification de transmission aux utilisateurs par le biais de leur application mobile lorsqu’un événement d’intérêt se produit dans les systèmes back-end. Par exemple un client de la banque qui a application de banque de la banque sur ses iPhone souhaite être averti lorsqu’un débit est effectué au-dessus d’un certain montant à partir de son compte ou d’un scénario d’intranet dans lequel un employé du département Finances, qui a une application d’approbation budgétaire sur son Windows Phone souhaite être averti lorsqu’il reçoit une demande d’approbation.

Le compte bancaire ou le traitement d’approbation est susceptible de le faire dans un système back-end qui doit lancer une diffusion sur l’utilisateur. Il peut y avoir plusieurs ces systèmes back-end doivent tous générer le même type de logique pour mettre en œuvre la transmission lorsqu’un événement déclenche une notification. Ici la complexité réside dans l’intégration de plusieurs systèmes back-end avec un système de transmission unique où les utilisateurs finaux auraient pu souscrire pour différentes notifications et il peut même y avoir plusieurs applications mobiles, par exemple dans le cas d’applications mobiles d’intranet que d’une seule application mobile pour recevoir des notifications à partir de plusieurs systèmes back-end de ce type. Les systèmes back-end ne savent ou doivent savoir de sémantique/technologie push, donc une solution commune ici a toujours été d’introduire un composant qui interroge les systèmes back-end pour tous les événements d’intérêt et est chargé d’envoyer les messages envoyés au client.
Ici, nous parlerons une solution plus efficace à l’aide du Bus des services Azure - rubrique / d’abonnement qui permet de réduire la complexité tout en effectuant la solution évolutive.

Voici l’architecture générale de la solution (généralisée avec plusieurs applications mobiles mais également applicables lorsqu’il en existe qu’une seule application mobile)

## <a name="architecture"></a>Architecture

![][1]

L’élément clé dans ce diagramme architectural est de Bus des services Azure qui fournit un rubriques/abonnements (plus sur elle à la [programmation de Bus de Service Pub/Sub]) du modèle de programmation. Le récepteur, qui dans ce cas, est le principal Mobile (généralement [Le Service Mobile Azure], qui lance une campagne pour les applications mobiles) ne reçoit pas les messages directement à partir des systèmes back-end, mais au lieu de cela, nous avons une couche intermédiaire d’abstraction fournie par le [Bus des services Azure] qui permet le back-end mobile recevoir des messages à partir d’un ou plusieurs systèmes back-end. Une rubrique de Bus de Service doit être créé pour chacun des systèmes back-end, par exemple compte, RH, Finance, qui sont en fait des « rubriques » d’intérêt qui génèrent des messages à envoyer en tant que notification de transmission. Les systèmes back-end va envoyer des messages à ces rubriques. Un back-end Mobile peuvent s’abonner à un ou plusieurs de ces rubriques en créant un abonnement de Bus de Service. Cela donnera droit le back-end mobile pour recevoir des notifications du système back-end correspondants. Back-end mobile continue à écouter les messages sur leurs abonnements et dès qu’un message arrive, il reprend et l’envoie en tant que notification à son concentrateur de notification. Concentrateurs de notification remet ensuite finalement le message à l’application mobile. Pour résumer les principaux composants, nous avons :

1. Systèmes back-end (systèmes métier/hérité)
    - Crée la rubrique Bus de Service
    - Envoie le Message
2. Serveur principal mobile
    - Crée le Service abonnement
    - Reçoit le Message (à partir de système de back-end)
    - L’envoi de notifications aux clients (par l’intermédiaire de concentrateur de Notification Azure)
3. Application mobile
    - Reçoit et afficher la notification

###<a name="benefits"></a>Avantages :

1. Le découplage entre le récepteur (application/service mobile via un Hub de Notification) et l’expéditeur (systèmes back-end) permet à des systèmes back-end supplémentaires intégrées avec un changement minimal.
2. Cela rend également le scénario de plusieurs applications mobiles est en mesure de recevoir des événements à partir d’un ou plusieurs systèmes back-end.  

## <a name="sample"></a>Exemple :

###<a name="prerequisites"></a>Conditions préalables
Vous devez effectuer les didacticiels suivants pour vous familiariser avec les étapes de configuration ainsi que la création commune & concepts :

1. [Programmation de Bus de Service Pub/Sub] - Ceci explique comment travailler avec les abonnements/Service Bus rubriques, comment créer un espace de noms contenant les rubriques/abonnements, comment envoyer et recevoir des messages à partir de ceux-ci.
2. [Concentrateurs de notification - didacticiel Windows universel] - cette rubrique explique comment configurer une application du Windows Store et les concentrateurs de Notification permet d’inscrire et de recevoir des notifications.

###<a name="sample-code"></a>Exemples de code

L’exemple de code complet est disponible à [Échantillons de concentrateur de Notification]. Elle est divisée en trois composants :

1. **EnterprisePushBackendSystem**

    une barre d’outils. Ce projet utilise le package Nuget *WindowsAzure.ServiceBus* et est basé sur la [programmation du Bus de Service Pub/Sub].

    b. Il s’agit d’une C# console application simple pour simuler un système métier qui lance le message soit remis à l’application mobile.

        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }

    c. `CreateTopic`permet de créer la rubrique Service Bus où nous envoie des messages.

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

    d. `SendMessage`est utilisé pour envoyer les messages à cette rubrique de Bus de Service. Ici nous sommes simplement envoyer un ensemble de messages aléatoires à la rubrique périodiquement dans le cadre de l’exemple. Il est un système back-end qui enverra des messages lorsqu’un événement se produit.

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

    une barre d’outils. Ce projet utilise les packages *WindowsAzure.ServiceBus* et *Microsoft.Web.WebJobs.Publish* Nuget et est basé sur la [programmation du Bus de Service Pub/Sub].

    b. Il s’agit d’une autre application de console C# qui nous sera exécuté comme un [WebJob d’Azure] dans la mesure où elle doit s’exécuter en permanence pour recevoir des messages à partir des systèmes métier/back-end. Il s’agit de la partie de votre back-end Mobile.

        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the subscription which will receive messages
            CreateSubscription(connectionString);

            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }

    c. `CreateSubscription`permet de créer un abonnement de Bus de Service pour la rubrique où le système back-end enverra des messages. En fonction du scénario, ce composant crée un ou plusieurs abonnements à des rubriques correspondantes (par exemple, certains peuvent recevoir les messages à partir du système de ressources humaines, certains membres du système financier et ainsi de suite)

        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

    d. ReceiveMessageAndSendNotification est utilisée pour lire le message à partir de la rubrique à l’aide de son abonnement et que si la lecture réussit ensuite une notification (dans l’exemple de scénario une notification de toast natif Windows) pour être transmises à l’application mobile à l’aide de concentrateurs de Notification Azure.

        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");

            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

    e. Pour la publication de ce sous la forme d’un **WebJob**, cliquez avec le bouton droit sur la solution dans Visual Studio et sélectionnez **Publier en tant que WebJob**

    ![][2]

    f. Sélectionnez votre profil de publication et créer un nouveau site Web de Azure s’il n’existe déjà qui hébergera ce WebJob et une fois que le site Web puis le **Publier**.

    ![][3]

    g. Configurer la tâche pour être « Exécuter en continu » afin que lors de la connexion au [Portail classique d’Azure] , vous devriez voir quelque chose comme suit :

    ![][4]


3. **EnterprisePushMobileApp**

    une barre d’outils. Il s’agit d’une application Windows Store qui recevra les notifications toast à partir de le WebJob en cours d’exécution en tant que partie de votre back-end Mobile et afficher. Il repose sur les [Concentrateurs de Notification - didacticiel universel de Windows].  

    b. Assurez-vous que votre application est activée pour recevoir des notifications de toast.

    c. S’assurer que les concentrateurs de Notification suivant code d’enregistrement est appelé au niveau de l’application de démarrage (après le remplacement de la *HubName* et le *DefaultListenSharedAccessSignature*:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>Exemple :

1. Assurez-vous que votre WebJob est exécuté avec succès et planifiées « Exécuter en continu ».
2. Exécuter l' **EnterprisePushMobileApp** qui permet de démarrer l’application du Windows Store.
3. Exécutez l’application console **EnterprisePushBackendSystem** qui simule la principale de LoB et démarrera l’envoi de messages, et vous devriez voir notifications toast apparaissant comme suit :

    ![][5]

4. Les messages ont été envoyés à l’origine aux rubriques du Bus de Service qui a été analysé par les abonnements de Bus de Service dans votre projet Web. Une fois qu’un message a été reçu, une notification a été créée et envoyée à l’application mobile. Vous pouvez consulter les journaux de WebJob pour confirmer le traitement lorsque vous accédez au lien de journaux dans le [Portail classique d’Azure] pour votre projet Web :

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Exemples de concentrateur de notification]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Service Mobile]: http://azure.microsoft.com/documentation/services/mobile-services/
[Bus des services Azure]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programmation de Bus de service Pub/Sub]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[WebJob Azure]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Concentrateurs de notification - didacticiel Windows universel]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure Portal classique]: https://manage.windowsazure.com/
