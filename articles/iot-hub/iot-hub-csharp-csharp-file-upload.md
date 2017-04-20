<properties
    pageTitle="Télécharger des fichiers à partir de périphériques à l’aide de concentrateur de IoT | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre à télécharger des fichiers à partir de périphériques à l’aide d’Azure IoT Hub avec C#."
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
     ms.date="06/21/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-upload-files-from-devices-to-the-cloud-with-iot-hub"></a>Didacticiel : Comment télécharger des fichiers à partir de périphériques vers le nuage avec IoT concentrateur

## <a name="introduction"></a>Introduction

Concentrateur de IoT Azure est un service entièrement géré qui permet fiable et sécurisée des communications bidirectionnelles entre millions d’appareils de IoT et une application back-end. Des didacticiels précédents ([mise en route de IoT Hub] et [Envoyer des messages de nuage-dispositif avec concentrateur de IoT]) illustrent la base périphérique-nuage et des fonctionnalités de messagerie de nuage-dispositif de IoT Hub et le didacticiel de [messages de processus périphérique-nuage] décrit un moyen fiable de stocker des messages de périphérique-nuage dans le stockage blob Azure. Toutefois, dans certains scénarios vous ne peut pas facilement mapper les données de que vos périphériques envoyer dans les messages de périphérique-nuage relativement petits qui IoT Hub accepte. Les exemples incluent des fichiers volumineux qui contiennent des images, des vidéos, des données de vibrations échantillonnées à une fréquence élevée, ou qui contiennent un type de données prétraitées. Ces fichiers sont généralement traitées dans le cloud à l’aide d’outils tels que [Azure Data Factory] ou la pile [Hadoop] par lots. Lors de transferts de fichiers à partir d’un périphérique sont préférables à l’envoi d’événements, vous pouvez toujours utiliser la fonctionnalité de sécurité et de fiabilité IoT concentrateur.

Ce didacticiel s’appuie sur le code dans le didacticiel [d’Envoyer des messages de nuage-dispositif avec IoT Hub] pour vous montrer comment utiliser les fonctions de téléchargement de fichier de IoT concentrateur. Il vous montre comment fournir de manière sécurisée un périphérique un Azure blob d’URI pour le téléchargement d’un fichier et comment utiliser les notifications de téléchargement de fichier IoT Hub pour déclencher le traitement du fichier dans votre principal de l’application.

À la fin de ce didacticiel, vous exécutez deux applications de console Windows :

* **SimulatedDevice**, une version modifiée de l’application créée dans le didacticiel [d’Envoyer des messages de nuage-dispositif avec IoT concentrateur] , qui télécharge un fichier de stockage à l’aide d’un SAS URI fourni par votre concentrateur IoT.
* **ReadFileUploadNotification**, qui reçoit les notifications de téléchargement de fichiers à partir de votre concentrateur IoT.

> [AZURE.NOTE] IoT Hub prend en charge plusieurs plates-formes de périphérique et les langues (y compris C, Java et Javascript) via le périphérique Azure IoT kits de développement logiciel. Reportez-vous au [Centre de développement IoT Azure] pour obtenir des instructions étape par étape sur la façon de connecter votre périphérique pour le code de ce didacticiel et généralement Azure IoT concentrateur.

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Microsoft Visual Studio 2015,

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associer un compte de stockage Azure IoT concentrateur

Parce que le périphérique simulé transfère un fichier vers un blob, vous devez disposer d’un compte de [Stockage Azure] associé à IoT concentrateur. Lorsque vous associez un compte de stockage Azure avec un concentrateur IoT, le concentrateur peut générer un URI SAS qu’un périphérique peut utiliser en toute sécurité de télécharger un fichier à un conteneur d’objet blob. Le service IoT concentrateur et les kits de développement de périphérique coordonnent le processus qui génère l’URI SAS et les rend accessibles à un périphérique à utiliser pour télécharger un fichier.

Suivez les instructions dans [les téléchargements de fichier de configuration via le portail Azure] [ lnk-configure-upload] pour associer un compte de stockage Azure à votre concentrateur IoT.

## <a name="upload-a-file-from-a-simulated-device"></a>Télécharger un fichier à partir d’un périphérique simulé

Dans cette section, vous modifiez l’application simulée périphérique créé à [Envoyer des messages de nuage-dispositif avec IoT concentrateur] pour recevoir des messages de nuage vers le périphérique du concentrateur IoT.

1. Dans Visual Studio, cliquez droit sur le projet **SimulatedDevice** , cliquez sur **Ajouter**, puis cliquez sur **Élément existant**. Naviguez jusqu'à un fichier image et l’inclure dans votre projet. Ce didacticiel suppose que l’image est nommée `image.jpg`.

2. Avec le bouton droit sur l’image, puis cliquez sur **Propriétés**. Assurez-vous que l’option **Copier dans le répertoire de sortie** est défini sur **toujours copier**.

    ![][1]

3. Dans le fichier **Program.cs** , ajoutez les instructions suivantes en haut du fichier :

        using System.IO;

4. Ajoutez la méthode suivante à la classe **Program** :
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    Le `UploadToBlobAsync` méthode utilise dans la source de flux et de nom de fichier du fichier à télécharger et gère le téléchargement vers le stockage. L’application console affiche le temps que nécessaire pour télécharger le fichier.

5. Ajoutez la méthode suivante dans la méthode **Main** , juste avant la `Console.ReadLine()` ligne :

        SendToBlobAsync();

> [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (comme intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes].

## <a name="receive-a-file-upload-notification"></a>Recevoir une notification de téléchargement de fichier

Dans cette section, vous écrivez une application console Windows qui reçoit les messages de notification de téléchargement de fichier IoT concentrateur.

1. Dans la solution actuelle de Visual Studio, créez un nouveau projet Visual C# Windows en utilisant le modèle de projet **Application Console** . Nommez le projet **ReadFileUploadNotification**.

    ![Nouveau projet dans Visual Studio][2]

2. Dans l’Explorateur de solutions, cliquez sur le projet **ReadFileUploadNotification** , puis cliquez sur **Manage NuGet Packages**.

    Cela affiche la fenêtre Gérer les Packages NuGet.

2. Rechercher des `Microsoft.Azure.Devices`, cliquez sur **installer**et accepter les conditions d’utilisation. 

    Ce télécharge installe et ajoute une référence à l' [Azure IoT - package NuGet SDK de Service] dans le projet **ReadFileUploadNotification** .

3. Dans le fichier **Program.cs** , ajoutez les instructions suivantes en haut du fichier :

        using Microsoft.Azure.Devices;

4. Ajoutez les champs suivants à la classe **Program** . Remplacez par la valeur d’espace réservé avec la chaîne de connexion IoT hub à partir de la [mise en route de IoT Hub]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Ajoutez la méthode suivante à la classe **Program** :
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();

            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();

                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }

    Notez que le modèle de réception est le même que celui utilisé pour recevoir les messages du nuage vers le périphérique à partir de l’application de périphérique.

6. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. Dans Visual Studio, avec le bouton droit de votre solution, puis sélectionnez **projets de définir au démarrage**. Sélectionnez **plusieurs projets de démarrage**, puis sélectionnez l’action de **démarrage** **ReadFileUploadNotification** et **SimulatedDevice**.

2. Appuyez sur **F5**. Les deux applications doivent démarrer. Vous devez voir le téléchargement effectué dans une application de console et le message de notification de téléchargement en cours de réception par l’autre application de console. [Azure portal] ou l’Explorateur de serveurs Visual Studio vous permet de vérifier la présence du fichier téléchargé dans votre compte de stockage Azure.

  ![][50]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment exploiter les fonctionnalités de téléchargement de fichier de IoT concentrateur afin de simplifier les transferts de fichiers à partir de périphériques. Vous pouvez continuer à Explorer les fonctionnalités de concentrateur IoT et scénarios avec les articles suivants :

- [Créer un concentrateur IoT par programme][lnk-create-hub]
- [Introduction au Kit de développement logiciel C][lnk-c-sdk]
- [Kits de développement logiciel IoT concentrateur][lnk-sdks]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Azure portal]: https://portal.azure.com/

[Usine de données Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Envoyer des messages de nuage-dispositif avec IoT concentrateur]: iot-hub-csharp-csharp-c2d.md
[Traitez les messages périphérique-nuage]: iot-hub-csharp-csharp-process-d2c.md
[Mise en route de concentrateur de IoT]: iot-hub-csharp-csharp-getstarted.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot

[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Stockage Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[IoT Azure - package NuGet SDK de Service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md


