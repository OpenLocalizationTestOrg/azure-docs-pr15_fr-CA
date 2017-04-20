<properties
    pageTitle="Envoyer des messages de nuage-dispositif avec IoT Hub | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre à envoyer des messages de nuage vers le périphérique à l’aide d’Azure IoT Hub avec C#."
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-net"></a>Didacticiel : Comment envoyer des messages de nuage-dispositif avec IoT Hub et .net

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduction

Concentrateur de IoT Azure est un service entièrement géré qui vous aide à activer fiables et sécurisées des communications bidirectionnelles entre millions d’appareils de IoT et mettre fin à une demande de retour. Le didacticiel [mise en route de concentrateur de IoT] montre comment créer un concentrateur IoT, configurer une identité de périphérique qu’il contient et un périphérique simulé qui envoie des messages de périphérique-nuage de code.

Ce didacticiel s’appuie sur la [mise en route de IoT concentrateur]. Il vous montre comment procéder pour :

- À partir de votre application cloud back-end, envoyer des messages de nuage-appareil à un seul périphérique par IoT concentrateur.
- Recevoir des messages du nuage vers le périphérique sur un périphérique.
- À partir de votre nuage application back-end, demander un accusé de réception (*commentaires*) pour les messages envoyés à un périphérique IoT concentrateur.

Vous trouverez plus d’informations sur les messages du nuage vers le périphérique dans le [Guide de développeur de concentrateur IoT][IoT Hub Developer Guide - C2D].

À la fin de ce didacticiel, vous allez exécuter deux applications de console Windows :

* **SimulatedDevice**, une version modifiée de l’application créée dans [mise en route de IoT concentrateur], qui se connecte à votre concentrateur IoT et reçoit les messages du nuage vers le périphérique.
* **SendCloudToDevice**, qui envoie un message de nuage vers le périphérique à périphérique simulé par IoT concentrateur, puis reçoit son accusé de réception.

> [AZURE.NOTE] IoT concentrateur a la prise en charge du Kit de développement logiciel pour de nombreuses plates-formes de périphérique et les langues (y compris C, Java et Javascript) par le biais de kits SDK de périphériques Azure IoT. Pour obtenir des instructions détaillées sur la façon de connecter votre périphérique pour les code de ce didacticiel et généralement Azure IoT Hub, consultez le [Centre pour développeurs IoT Azure].

Pour terminer ce didacticiel, vous allez besoin des éléments suivants :

+ Microsoft Visual Studio 2015

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

## <a name="receive-messages-on-the-simulated-device"></a>Recevoir des messages sur le périphérique simulé

Dans cette section, vous allez modifier l’application de périphérique simulé créé à la [mise en route de IoT concentrateur] pour recevoir des messages de nuage vers le périphérique du concentrateur IoT.

1. Dans Visual Studio, dans le projet **SimulatedDevice** , ajoutez la méthode suivante à la classe **Program** .

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    Le `ReceiveAsync` méthode retourne de manière asynchrone le message reçu au moment où il est reçu par le périphérique. Elle renvoie la valeur *null* après un délai d’expiration pouvant être précisés (dans ce cas, la valeur par défaut d’une minute est utilisé). Dans ce cas, le code doit continuer à attendre pour les nouveaux messages. C’est la raison pour laquelle le `if (receivedMessage == null) continue` ligne.

    L’appel à `CompleteAsync()` avertit IoT concentrateur que le message a été traité avec succès. Le message peut être supprimé en toute sécurité à partir de la file d’attente du périphérique. Si quelque chose s’est produite qui a empêché l’application de périphérique lors du traitement du message, IoT Hub le remet à nouveau. Il est alors important de logique de traitement de message dans l’application de périphérique *idempotent*, afin que la réception de plusieurs fois le même message produit le même résultat. Une application peut abandonner également temporairement d’un message, ce qui se traduit par un concentrateur IoT en conservant le message dans la file d’attente de consommation future. Ou bien, l’application peut rejeter un message, ce qui le supprime définitivement le message de la file d’attente. Pour plus d’informations sur le cycle de vie du message du nuage vers le périphérique, consultez le [Guide de développeur de concentrateur IoT][IoT Hub Developer Guide - C2D].

    > [AZURE.NOTE] Lorsque vous utilisez HTTP au lieu de MQTT ou AMQP comme transport, la `ReceiveAsync` méthode retourne immédiatement. Le modèle de prise en charge pour les messages du nuage vers le périphérique avec HTTP est connectées par intermittence des périphériques qui vérifient les messages rarement (moins de 25 minutes). Émission plus HTTP reçoit les résultats dans le concentrateur IoT la limitation de requêtes. Pour plus d’informations sur les différences entre la prise en charge MQTT, AMQP et HTTP et IoT Hub la limitation, voir le [Guide de développeur de concentrateur IoT][IoT Hub Developer Guide - C2D].

2. Ajoutez la méthode suivante dans la méthode **Main** , juste avant la `Console.ReadLine()` ligne :

        ReceiveC2dAsync();

> [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (comme intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes].

## <a name="send-a-cloud-to-device-message"></a>Envoyer un message de nuage-appareil

Dans cette section, vous allez écrire une application console Windows qui envoie des messages de nuage-dispositif pour l’application du périphérique simulé.

1. Dans la solution actuelle de Visual Studio, créez un nouveau projet d’application de bureau Visual C# à l’aide du modèle de projet **Application Console** . Nommez le projet **SendCloudToDevice**.

    ![Nouveau projet dans Visual Studio][20]

2. Dans l’Explorateur de solutions, avec le bouton droit de la solution, puis cliquez sur **Gérer les Packages NuGet pour Solution...**. 

    Cela ouvre la fenêtre **Gérer les Packages NuGet** .

3. Rechercher des `Microsoft Azure Devices`, cliquez sur **installer**et accepter les conditions d’utilisation. 

    Il télécharge installe et ajoute une référence à l' [Azure IoT - package NuGet de kit de développement logiciel de Service].

4. Ajoutez le code suivant `using` instruction au début du fichier **Program.cs** :

        using Microsoft.Azure.Devices;

5. Ajoutez les champs suivants à la classe **Program** . Remplacez par la valeur d’espace réservé avec la chaîne de connexion IoT hub à partir de la [mise en route de IoT Hub]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Ajoutez la méthode suivante à la classe **Program** :

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Cette méthode envoie un nouveau message de nuage vers le périphérique pour le périphérique avec l’ID, `myFirstDevice`. Modifiez ce paramètre en conséquence, au cas où vous modifié de celui utilisé dans la [mise en route de IoT concentrateur].

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Dans Visual Studio, avec le bouton droit de votre solution et sélectionnez **projets de définir au démarrage...**. Sélectionnez **plusieurs projets de démarrage**, puis sélectionnez l’action de **démarrage** **ProcessDeviceToCloudMessages**, **SimulatedDevice**et **SendCloudToDevice**.

9.  Appuyez sur **F5**. Les trois applications doivent démarrer. Sélectionnez les fenêtres **SendCloudToDevice** et appuyez sur **entrée**. Vous devriez voir le message qui est reçu par l’application simulée.

    ![Message de réception App][21]

## <a name="receive-delivery-feedback"></a>Recevoir des commentaires de livraison
Il est possible de demande livraison (ou expiration) des accusés de réception IoT concentrateur pour chaque message du nuage vers le périphérique. Cela permet le cloud back-end pour facilement informer logique de nouvelle tentative ou de compensation. Pour plus d’informations sur les évaluations du nuage vers le périphérique, consultez le [Guide de développeur de concentrateur IoT][IoT Hub Developer Guide - C2D].

Dans cette section, vous allez modifier l’application **SendCloudToDevice** pour obtenir des commentaires et de le recevoir IoT concentrateur.

1. Dans Visual Studio, dans le projet **SendCloudToDevice** , ajoutez la méthode suivante à la classe **Program** .
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Notez que le modèle de réception est le même que celui utilisé pour recevoir les messages du nuage vers le périphérique à partir de l’application de périphérique.

2. Ajoutez la méthode suivante dans la méthode **Main** , immédiatement après le `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` ligne :

        ReceiveFeedbackAsync();

3. Pour obtenir des commentaires pour la remise de votre message du nuage vers le périphérique, vous devez spécifier une propriété dans la méthode **SendCloudToDeviceMessageAsync** . Ajoutez la ligne suivante, juste après le `var commandMessage = new Message(...);` ligne :

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Exécutez les applications en appuyant sur **F5**. Vous devez voir les trois applications Démarrer. Sélectionnez les fenêtres **SendCloudToDevice** et appuyez sur **entrée**. Vous devriez voir le message en cours de réception par l’application simulée et après quelques secondes, le message de retour reçu par votre application **SendCloudToDevice** .

    ![Message de réception App][22]

> [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (comme intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes].

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment envoyer et recevoir des messages du nuage vers le périphérique. 

Pour voir des exemples de solutions de bout en bout complètes qui utilisent IoT Hub, consultez [Azure IoT Suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT concentrateur].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[IoT Azure - package NuGet SDK de Service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md

[Guide du développeur IoT concentrateur]: iot-hub-devguide.md
[Mise en route de concentrateur de IoT]: iot-hub-csharp-csharp-getstarted.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[IoT Azure Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/