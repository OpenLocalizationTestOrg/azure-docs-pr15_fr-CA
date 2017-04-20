<properties
    pageTitle="Concentrateur de IoT Azure pour C# mise en route | Microsoft Azure"
    description="Azure concentrateur IoT route C# démarré le didacticiel. Utilisez Azure IoT Hub et C# avec les kits de développement logiciel de Microsoft Azure IoT d’implémenter une solution de l’Internet des objets."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-net"></a>Mise en route avec concentrateur de IoT Azure pour .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous avez trois applications console de Windows :

* **CreateDeviceIdentity**, qui crée une identité de périphérique et d’une clé de sécurité associées pour connecter votre périphérique simulé.
* **ReadDeviceToCloudMessages**, qui affiche la télémétrie envoyé par votre périphérique simulé.
* **SimulatedDevice**, qui se connecte à votre concentrateur IoT avec l’identité du périphérique créée précédemment et envoie un message de télémétrie à chaque seconde à l’aide du protocole AMQP.

> [AZURE.NOTE] Pour plus d’informations sur les kits de développement différents que vous pouvez utiliser pour générer les deux applications à exécuter sur les périphériques et votre solution back-end, consultez le [SDK de concentrateur IoT][lnk-hub-sdks].

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Microsoft Visual Studio 2015.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Vous avez créé votre concentrateur IoT, et que la chaîne de nom d’hôte et de la connexion dont vous avez besoin pour compléter le reste de ce didacticiel.

## <a name="create-a-device-identity"></a>Création d’une identité de périphérique

Dans cette section, vous créez une application console Windows qui crée une identité de périphérique dans le Registre de l’identité de votre concentrateur IoT. Un périphérique ne peut pas se connecter IoT concentrateur, à moins qu’une entrée dans le Registre d’identité de périphérique. Pour plus d’informations, consultez la section « Registre d’identité de périphérique » du [Guide de développeur de concentrateur IoT][lnk-devguide-identity]. Lorsque vous exécutez cette application de console, il génère un ID de périphérique unique et la clé de votre périphérique peut utiliser pour s’identifier lorsqu’il envoie des messages de périphérique-nuage à IoT concentrateur.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique de Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Assurez-vous que la version du.NET Framework est 4.5.1 ou ultérieur. Nommez le projet **CreateDeviceIdentity**.

    ![Nouveau projet Visual C# Bureau classique de Windows][10]

2. Dans l’Explorateur de solutions, cliquez sur le projet **CreateDeviceIdentity** , puis cliquez sur **Manage Nuget Packages**.

3. Dans la fenêtre **Du Gestionnaire de package Nuget** , sélectionnez **Parcourir**, recherchez **microsoft.azure.devices**, sélectionnez **installer** pour installer le package de la **Microsoft.Azure.Devices** et accepter les conditions d’utilisation. Cette procédure télécharge, installe et ajoute une référence dans le [Kit de développement logiciel de Microsoft Azure IoT Service] [ lnk-nuget-service-sdk] Nuget package et ses dépendances.

    ![Fenêtre de du Gestionnaire de package NuGet][11]

4. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier **Program.cs** :

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé par la chaîne de connexion pour le moyeu IoT que vous avez créé dans la section précédente.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Ajoutez la méthode suivante à la classe **Program** :

        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    Cette méthode crée une identité de périphérique avec l' ID **myFirstDevice**. (Si cet ID de périphérique existe déjà dans le Registre, le code récupère simplement les informations de périphérique existant.) Ensuite, l’application affiche la clé primaire de cette identité. Vous utilisez cette clé dans le périphérique simulé pour se connecter à votre concentrateur IoT.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Exécuter cette application et prenez note de la clé de périphérique.

    ![Clé de périphérique généré par l’application][12]

> [AZURE.NOTE] Le Registre d’identité IoT concentrateur ne stocke que les identités des appareils pour permettre un accès sécurisé au concentrateur. Il stocke les ID de périphérique et les clés à utiliser comme informations d’identification de sécurité et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un périphérique. Si votre application a besoin stocker d’autres métadonnées spécifiques au périphérique, il doit utiliser une banque spécifique à l’application. Pour plus d’informations, consultez le [Guide de développeur de concentrateur IoT][lnk-devguide-identity].

## <a name="receive-device-to-cloud-messages"></a>Recevoir des messages de périphérique-nuage

Dans cette section, vous créez une application console Windows qui lit les messages de périphérique-nuage IoT concentrateur. Un concentrateur IoT expose un [Azure événement concentrateurs][lnk-event-hubs-overview]-le point de terminaison compatible pour vous permettre de lire des messages de périphérique-nuage. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui n’est pas approprié pour un déploiement à un débit élevé. Pour savoir comment traiter les messages de périphérique-nuage à grande échelle, consultez [traitement des messages de périphérique-nuage] [ lnk-process-d2c-tutorial] didacticiel. Pour plus d’informations sur la façon de traiter les messages de concentrateurs d’événement, reportez-vous à la [Mise en route avec les concentrateurs d’événement] [ lnk-eventhubs-tutorial] didacticiel. (Ce didacticiel est applicable aux points de terminaison IoT concentrateur événement compatible avec le concentrateur.)

> [AZURE.NOTE] Le point de terminaison compatible avec concentrateur événement pour lire des messages de périphérique-nuage toujours utilise le protocole AMQP.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique de Windows à la solution en cours, en utilisant le modèle de projet **Application Console** . Assurez-vous que la version du.NET Framework est 4.5.1 ou ultérieur. Nommez le projet **ReadDeviceToCloudMessages**.

    ![Nouveau projet Visual C# Bureau classique de Windows][10]

2. Dans l’Explorateur de solutions, cliquez sur le projet **ReadDeviceToCloudMessages** , puis cliquez sur **Manage Nuget Packages**.

3. Dans la fenêtre **Du Gestionnaire de package Nuget** , recherchez **WindowsAzure.ServiceBus**, sélectionnez **installer**et accepter les conditions d’utilisation. Cette procédure télécharge, installe et ajoute une référence à un [Bus des services Azure][lnk-servicebus-nuget], avec toutes ses dépendances. Ce package permet de se connecter au point de terminaison compatible avec concentrateur événement sur votre concentrateur IoT.

4. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier **Program.cs** :

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé avec la chaîne de connexion pour le moyeu IoT que vous avez créé dans la section « Création d’un concentrateur IoT ».

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Ajoutez la méthode suivante à la classe **Program** :

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    Cette méthode utilise une instance de **EventHubReceiver** pour recevoir des messages provenant de tous le IoT concentrateur périphérique-nuage recevoir des partitions. Notez comment vous passer un `DateTime.Now` paramètre lorsque vous créez l’objet **EventHubReceiver** , pour qu’il reçoive uniquement les messages envoyés après son démarrage. Ce filtre est utile dans un environnement de test afin de voir l’ensemble actuel des messages. Dans un environnement de production votre code Assurez-vous qu’elle traite tous les messages. Pour plus d’informations, consultez [comment traiter les messages de périphérique-nuage IoT concentrateur] [ lnk-process-d2c-tutorial] didacticiel.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Windows qui simule un périphérique qui envoie des messages de périphérique-nuage à un concentrateur IoT.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique de Windows à la solution en cours, en utilisant le modèle de projet **Application Console** . Assurez-vous que la version du.NET Framework est 4.5.1 ou ultérieur. Nommez le projet **SimulatedDevice**.

    ![Nouveau projet Visual C# Bureau classique de Windows][10]

2. Dans l’Explorateur de solutions, cliquez sur le projet **SimulatedDevice** , puis cliquez sur **Manage Nuget Packages**.

3. Dans la fenêtre **Du Gestionnaire de package Nuget** , sélectionnez **Parcourir**, recherchez **Microsoft.Azure.Devices.Client**, sélectionnez **installer** pour installer le package de la **Microsoft.Azure.Devices.Client** et accepter les conditions d’utilisation. Cette procédure télécharge, installe et ajoute une référence à l' [Azure IoT - package Nuget de kit de développement logiciel de périphérique] [ lnk-device-nuget] et ses dépendances.

4. Ajoutez le code suivant `using` instruction au début du fichier **Program.cs** :

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Ajoutez les champs suivants à la classe **Program** . Remplacer par les valeurs d’espace réservé avec le nom d’hôte du concentrateur IoT que vous avez récupéré dans la section « Création d’un concentrateur IoT » et la clé de périphérique extraites dans la section « Création d’une identité de périphérique ».

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Ajoutez la méthode suivante à la classe **Program** :

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

    Cette méthode envoie un nouveau message de périphérique-nuage à chaque seconde. Le message contient un objet JSON sérialisé, avec l’ID de périphérique et un nombre généré de manière aléatoire pour simuler un capteur de vitesse du vent.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Par défaut, la méthode **Create** crée une instance de **DeviceClient** qui utilise le protocole AMQP pour communiquer avec IoT concentrateur. Pour utiliser le protocole HTTP, utilisez la substitution de la méthode **Create** qui vous permet de spécifier le protocole. Si vous utilisez le protocole HTTP, vous devez également ajouter le package Nuget **Microsoft.AspNet.WebApi.Client** à votre projet pour inclure l’espace de noms **System.Net.Http.Formatting** .

Ce didacticiel passe en revue les étapes de création d’un client d’appareil IoT concentrateur. Vous pouvez également utiliser le [Service de connecté pour Azure IoT concentrateur] [ lnk-connected-service] extension de Visual Studio pour ajouter le code nécessaire à votre application de client de périphérique.


> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes][lnk-transient-faults].

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Dans Visual Studio, dans l’Explorateur de solutions, avec le bouton droit de votre solution, puis cliquez sur **définir au démarrage de projets**. Sélectionnez **plusieurs projets de démarrage**, puis sélectionnez l’option **Démarrer** l’action pour les projets de la **ReadDeviceToCloudMessages** et le **SimulatedDevice** .

    ![Propriétés de projet de démarrage][41]

2.  Appuyez sur **F5** pour démarrer les applications en cours d’exécution. La sortie de console à partir de l’application **SimulatedDevice** affiche les messages sur que votre périphérique simulé envoie à votre concentrateur IoT. La sortie de console à partir de l’application **ReadDeviceToCloudMessages** affiche les messages que votre concentrateur IoT reçoit.

    ![Sortie de la console à partir d’applications][42]

3. La mosaïque de **l’utilisation** du [portail Azure] [ lnk-portal] indique le nombre de messages envoyés au hub :

    ![Mosaïque de l’utilisation de portail Azure][43]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous configuré un concentrateur IoT dans Azure portal et ensuite créé une identité de périphérique dans le Registre d’identité du concentrateur. Cette identité de périphérique vous permet d’activer l’application de périphérique simulé envoyer des messages de périphérique-nuage au concentrateur. Vous avez également créé une application qui affiche les messages reçus par le concentrateur. 

Pour continuer la mise en route avec IoT Hub et Explorer d’autres scénarios IoT, voir :

- [Connectez votre périphérique][lnk-connect-device]
- [Mise en route de la gestion des périphériques][lnk-device-management]
- [Mise en route avec le Kit de développement de passerelle IoT][lnk-gateway-SDK]

Pour savoir comment étendre votre solution IoT et de traiter des messages de périphérique-nuage à grande échelle, consultez [traitement des messages de périphérique-nuage] [ lnk-process-d2c-tutorial] didacticiel.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
