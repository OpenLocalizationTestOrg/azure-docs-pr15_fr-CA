<properties
    pageTitle="Traiter les messages de périphérique-nuage IoT Hub (.Net) | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre les modèles utiles pour traiter les messages de périphérique-nuage IoT concentrateur."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/05/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-net"></a>Didacticiel : Comment traiter les messages de périphérique-nuage IoT concentrateur à l’aide de .net

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introduction

Concentrateur de IoT Azure est un service entièrement géré qui permet fiable et sécurisée des communications bidirectionnelles entre millions d’appareils de IoT et une application back-end. Autres didacticiels ([mise en route de IoT Hub] et [d’Envoyer des messages de nuage-dispositif avec concentrateur de IoT][lnk-c2d]) montrent comment utiliser la fonctionnalité de messagerie périphérique-nuage et nuage-DISPOSITIF base de IoT concentrateur.

Ce didacticiel s’appuie sur le code indiqué dans le didacticiel [mise en route de IoT concentrateur] , et il montre deux modèles évolutifs que vous pouvez utiliser pour traiter les messages de périphérique-nuage :

- Le stockage fiable des messages de périphérique-nuage dans le [stockage blob Azure]. Un scénario courant est analytique de *chemin à froid* , dans lequel vous stockez les données de télémétrie de BLOB à utiliser comme entrée dans les processus analytique. Ces processus peuvent être pilotés par des outils tels que [l’Usine de données Azure] ou la pile [HDInsight (Hadoop)] .

- Le traitement des messages de périphérique-nuage *interactive* fiable. Messages de périphérique-nuage sont interactifs lorsqu’elles sont des déclencheurs immédiate pour un ensemble d’actions dans l’application principale. Par exemple, un périphérique peut envoyer un message d’alerte qui déclenche l’insertion d’un ticket dans un système CRM. En revanche, *du point de données* de messages alimentent simplement dans un moteur analytique. Par exemple, la télémétrie de température à partir d’un périphérique qui doit être stockées pour une analyse ultérieure est un message de point de données.

Étant donné que IoT concentrateur expose un [Concentrateur d’événements][lnk-event-hubs]-le point de terminaison compatible pour recevoir des messages de périphérique-nuage, ce didacticiel utilise une instance de [EventProcessorHost] . Cette instance :

* Stocke les messages de *point de données* fiable dans le stockage blob Azure.
* *Interactive* transfère des messages de périphérique-nuage à une [file d’attente de Bus de Service] d' Azure pour un traitement immédiat.

Bus de service permet d’assurer un traitement fiable des messages interactifs, car il fournit des points de contrôle par message et de la déduplication basée sur la fenêtre de temps.

> [AZURE.NOTE] Une instance de **EventProcessorHost** n'est qu’un seul moyen de traiter les messages interactifs. D’autres options incluent [Azure Fabric de Service] [ lnk-service-fabric] et [Azure flux Analytique][lnk-stream-analytics].

À la fin de ce didacticiel, vous exécutez trois applications console de Windows :

* **SimulatedDevice**, une version modifiée de l’application créé dans le didacticiel [mise en route de IoT concentrateur] , envoie des données point périphérique-nuage messages chaque seconde et interactive périphérique-nuage des messages toutes les 10 secondes. Cette application utilise le protocole de l’AMQP de communiquer avec IoT concentrateur.
* **ProcessDeviceToCloudMessages** utilise la classe [EventProcessorHost] pour récupérer des messages depuis le point de terminaison du concentrateur d’événements compatibles. Il fiable stocke les messages de point de données dans le stockage blob Azure, puis transfère les messages interactifs à une file d’attente de Bus de Service.
* **ProcessD2CInteractiveMessages** des files d’attente de messages interactifs à partir de la file d’attente de Bus de Service.

> [AZURE.NOTE] IoT concentrateur a la prise en charge du Kit de développement logiciel pour de nombreuses plates-formes de périphérique et les langages, notamment C, Java et JavaScript. Pour savoir comment remplacer le périphérique simulé dans ce didacticiel avec un périphérique physique et comment connecter des périphériques à un concentrateur IoT, consultez le [Centre pour développeurs IoT Azure].

Ce didacticiel est directement applicable aux autres modes de consommation de messages concentrateur d’événements compatibles, tels que les projets de [HDInsight (Hadoop)] . Pour plus d’informations, voir le [guide du développeur d’Azure IoT Hub - périphérique vers le cloud].

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Microsoft Visual Studio 2015.

+ Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

Vous devez disposer d’une connaissance de base du [Stockage Azure] et [d’Azure Service Bus].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Envoyer des messages interactives à partir d’un périphérique simulé

Dans cette section, vous modifiez l’application de périphérique simulé que vous avez créé dans le didacticiel [mise en route de IoT Hub] pour envoyer des messages de périphérique-nuage interactifs au concentrateur IoT.

1. Dans Visual Studio, dans le projet **SimulatedDevice** , ajoutez la méthode suivante à la classe **Program** .

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    Cette méthode est similaire à la méthode **SendDeviceToCloudMessagesAsync** dans le projet **SimulatedDevice** . Les seules différences sont que vous maintenant de définir la propriété **MessageId** du système et une propriété d’utilisateur appelé **messageType**.
    Le code assigne un identificateur global unique (GUID) à la propriété **MessageId** . Le Bus de Service permet de dédupliquer les messages qu’il reçoit cet identificateur. L’exemple utilise la propriété **messageType** pour distinguer interactive à partir de messages de point de données. L’application transmet ces informations dans les propriétés du message, et non dans le corps du message, afin que le processeur d’événements n’a pas besoin désérialiser le message pour effectuer le routage des messages.

    > [AZURE.NOTE] Il est important de créer le **MessageId** permet de dédupliquer les messages interactifs dans le code de périphérique. Communications réseau intermittente ou autres défaillances, peuvent entraîner des retransmissions plusieurs du même message à partir de ce périphérique. Vous pouvez également utiliser un ID de message de sémantique, par exemple un hachage du message pertinentes des champs de données, à la place d’un GUID.

2. Ajoutez la méthode suivante dans la méthode **Main** , juste avant la `Console.ReadLine()` ligne :

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter une stratégie de nouvelles tentatives notamment backoff exponentielle, comme indiqué dans l’article MSDN [Transitoire de gestion des pannes].

## <a name="process-device-to-cloud-messages"></a>Traitez les messages périphérique-nuage

Dans cette section, vous créez une application console Windows qui traite les messages de périphérique-nuage IoT concentrateur. Concentrateur de IOT expose un [concentrateur d’événements]-point de terminaison compatible pour activer une application lire des messages de périphérique-nuage. Ce didacticiel utilise la classe [EventProcessorHost] pour traiter ces messages dans une application console. Pour plus d’informations sur la façon de traiter les messages de concentrateurs d’événement, consultez le didacticiel [Mise en route de concentrateurs de l’événement] .

Le défi lorsque vous implémentez un stockage fiable de messages de point de données ou de transfert des messages interactifs est ce traitement de l’événement s’appuie sur le consommateur de messages pour fournir des points de contrôle pour sa progression. En outre, pour atteindre un débit élevé, lorsque vous lisez à partir de l’événement concentrateurs vous devez fournir des points de contrôle par lots importants. Cette approche crée la possibilité de transformation en double pour un grand nombre de messages, s’il y a une erreur et vous revenez à un point de contrôle précédent. Dans ce didacticiel, vous allez apprendre à synchroniser les écritures sur le stockage Azure et windows de suppression des doublons de Bus de Service avec les points de contrôle **EventProcessorHost** .

Pour écrire des messages dans le stockage Azure, fiable, l’exemple utilise la fonctionnalité de validation de chaque bloc de [BLOB de bloc][Azure Block Blobs]. Le processeur d’événements accumule les messages en mémoire jusqu'à ce qu’il est temps de fournir un point de contrôle. Par exemple, après que le tampon cumulé de messages atteint la taille de bloc maximale de 4 Mo, ou le Bus des services de déduplication laps de temps s’écoule. Ensuite, avant le point de contrôle, le code valide un nouveau bloc au blob.

Le processeur d’événements utilise les concentrateurs d’événement offsets de message comme ID de bloc. Ce mécanisme permet d’effectuer une vérification de la déduplication avant elle valide le nouveau bloc de stockage, en prenant soin de blocage possible entre la validation d’un bloc et le point de contrôle, le processeur d’événements.

> [AZURE.NOTE] Ce didacticiel utilise un seul compte de stockage Azure pour écrire tous les messages récupérés IoT concentrateur. Pour décider si vous devez utiliser plusieurs comptes de stockage Azure dans votre solution, consultez [instructions une évolutivité du stockage Azure].

L’application utilise la fonction de déduplication de Bus des services afin d’éviter les doublons lorsqu’il traite les messages interactifs. Le périphérique simulé l’estampille interactive avec un unique **MessageId**. Ces ID permettre de Bus des services afin de garantir que, dans la fenêtre de temps spécifiée de déduplication, aucun deux messages avec le même **MessageId** ne sont remis aux récepteurs. Cette déduplication, ainsi que la sémantique d’achèvement par message fournie par les files d’attente de Bus de Service, rend facile à implémenter le traitement fiable des messages interactifs.

Pour vous assurer qu’aucun message n’est renvoyé à l’extérieur de la fenêtre de la déduplication, le code synchronise le mécanisme de point de contrôle de **EventProcessorHost** avec la fenêtre de la déduplication de file d’attente de Bus de Service. Cette synchronisation est effectuée en forçant un point de contrôle au moins une fois, chaque fois que la fenêtre de la déduplication s’écoule (dans ce didacticiel, la fenêtre est d’une heure).

> [AZURE.NOTE] Ce didacticiel utilise une file d’attente de Bus de Service partitionnée unique pour traiter tous les messages interactifs extraite IoT concentrateur. Pour plus d’informations sur l’utilisation des files d’attente de Bus de Service pour satisfaire les exigences d’évolutivité de votre solution, consultez la documentation de [Bus des services Azure] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Configurer un compte de stockage Azure et une file d’attente de Bus de Service
Pour utiliser la classe [EventProcessorHost] , vous devez disposer d’un compte de stockage Azure pour activer **EventProcessorHost** enregistrer les informations de point de contrôle. Vous pouvez utiliser un compte de stockage Azure existant, ou suivez les instructions [Sur le stockage Azure] pour créer un nouveau. Prenez note de la chaîne de connexion de compte de stockage Azure.

> [AZURE.NOTE] Lorsque vous copiez et collez la chaîne de connexion de compte de stockage Azure, assurez-vous qu’il n’y pas d’espaces inclus.

Vous devez également une file d’attente de Bus de Service pour permettre un traitement fiable des messages interactifs. Vous pouvez créer une file d’attente par programme, avec une fenêtre de déduplication d’une heure, comme expliqué dans [l’utilisation de files d’attente de Bus de Service de][file d’attente de Bus de Service]. Vous pouvez également utiliser le [Azure portal classique][lnk-classic-portal], procédez comme suit :

1. Cliquez sur **Nouveau** dans le coin inférieur gauche. Puis cliquez sur **Services d’App** > **Service Bus** > **file d’attente** > **Personnalisé créer**. Entrez le nom **d2ctutorial**, sélectionnez une zone et utilisez un espace de noms ou créer un nouveau. Sur la page suivante, sélectionnez **Activer la détection des doublons**et définir la **fenêtre de temps de l’historique de détection de doublons** pour une heure. Cliquez sur la case à cocher dans le coin inférieur droit pour enregistrer la configuration de votre file d’attente.

    ![Créer une file d’attente dans Azure portal][30]

2. Dans la liste des files d’attente de Bus de Service, cliquez sur **d2ctutorial**, puis cliquez sur **configurer**. Créer deux stratégies d’accès partagé, un appelé **Envoyer** avec autorisations **Envoyer** et un appelé **écouter** avec des autorisations **d’écouter** . Lorsque vous avez terminé, cliquez sur **Enregistrer** dans la partie inférieure.

    ![Configurer une file d’attente dans Azure portal][31]

3. Cliquez sur le **tableau de bord** en haut et **les informations de connexion** puis en bas. Prenez note des deux chaînes de connexion.

    ![Tableau de bord de file d’attente dans Azure portal][32]

### <a name="create-the-event-processor"></a>Créer le processeur d’événements

1. Dans la solution actuelle de Visual Studio, pour créer un projet Visual C# Windows en utilisant le modèle de projet **Application Console** , cliquez sur le **fichier** > **Ajouter** > **Nouveau projet**. Assurez-vous que la version du.NET Framework est 4.5.1 ou ultérieur. Nommez le projet **ProcessDeviceToCloudMessages**, puis cliquez sur **OK**.

    ![Nouveau projet dans Visual Studio][10]

2. Dans l’Explorateur de solutions, cliquez sur le projet **ProcessDeviceToCloudMessages** , puis cliquez sur **Manage NuGet Packages**. La boîte de dialogue **Du Gestionnaire de package NuGet** s’affiche.

3. Recherchez **WindowsAzure.ServiceBus**, cliquez sur **installer**et accepter les conditions d’utilisation. Cette opération télécharge, installe et ajoute une référence au [package NuGet de Bus de Service Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), avec toutes ses dépendances.

4. Recherchez **Microsoft.Azure.ServiceBus.EventProcessorHost**, cliquez sur **installer**et accepter les conditions d’utilisation. Cette opération télécharge, installe et ajoute une référence à l' [Azure Service événement concentrateur - package NuGet de EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), avec toutes ses dépendances.

5. Droit sur le projet **ProcessDeviceToCloudMessages** et cliquez sur **Ajouter**, puis cliquez sur **une classe**. Nommez la nouvelle classe **StoreEventProcessor**, puis cliquez sur **OK** pour créer la classe.

6. Ajoutez les instructions suivantes en haut du fichier StoreEventProcessor.cs :

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Remplacez par le code suivant pour le corps de la classe :

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    La classe **EventProcessorHost** appelle cette classe pour traiter les messages de périphérique-nuage reçus IoT concentrateur. Le code dans cette classe implémente la logique pour stocker des messages de manière fiable dans un conteneur de blob et transférer les messages à la file d’attente de Bus de Service interactifs.

    La méthode **OpenAsync** initialise la variable **currentBlockInitOffset** , qui effectue le suivi de l’offset courant du premier message lu par ce processeur d’événements. N’oubliez pas que chaque processeur est responsable d’une partition unique.

    La méthode **ProcessEventsAsync** IoT concentrateur reçoit un lot de messages et les traite comme suit : il envoie des messages interactifs à la file d’attente de Bus de Service et ajoute les messages de point de données dans la mémoire tampon appelée **toAppend**. Si la mémoire tampon atteint la limite de 4 Mo, ou les périodes de déduplication s’écoule (une heure après un point de contrôle dans ce didacticiel), l’application déclenche un point de contrôle.

    La méthode **AppendAndCheckpoint** génère d’abord une ID de bloc pour le bloc à ajouter. Le stockage Azure requiert tous les bloquent ID pour avoir la même longueur, afin que la méthode remplit le décalage avec des zéros non significatifs - `currentBlockInitOffset.ToString("0000000000000000000000000")`. Ensuite, si un bloc avec cet ID existe déjà dans l’objet blob, la méthode remplace par le contenu de la mémoire tampon.

    > [AZURE.NOTE] Pour simplifier le code, ce didacticiel utilise un seul blob par partition pour stocker les messages. Une véritable solution implémenterait propagées en créant des fichiers supplémentaires après un certain temps ou lorsqu’ils atteignent une certaine taille de fichier. N’oubliez pas qu’un blob Azure bloc peut contenir au maximum 195 Go de données.

8. Dans la classe du **programme** , ajoutez l’instruction **using** suivante en haut :

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modifiez la méthode **Main** dans la classe de **programme** comme suit. Remplacez **{chaîne de connexion d’un concentrateur d’iot}** par la chaîne de connexion **iothubowner** à partir du didacticiel [mise en route de IoT concentrateur] . Remplacez la chaîne de connexion de stockage de la chaîne de connexion que vous avez noté au début de cette section. Remplacez la chaîne de connexion du Bus de Service avec les autorisations **Envoyer** de la file d’attente nommée **d2ctutorial** , que vous avez noté au début de cette section :

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

    > [AZURE.NOTE] Par souci de simplicité, ce didacticiel utilise une seule instance de la classe [EventProcessorHost] . Pour plus d’informations, consultez le [Guide de programmation des concentrateurs d’événement].

## <a name="receive-interactive-messages"></a>Recevoir des messages interactifs
Dans cette section, vous écrivez une application console Windows qui reçoit les messages interactifs à partir de la file d’attente de Bus de Service. Pour plus d’informations sur l’architecture d’une solution à l’aide du Bus de Service, voir [créer des applications évolutives avec un Bus de Service][].

1. Dans la solution actuelle de Visual Studio, créez un projet Windows Visual C# à l’aide du modèle de projet **Application Console** . Nommez le projet **ProcessD2CInteractiveMessages**.

2. Dans l’Explorateur de solutions, cliquez sur le projet **ProcessD2CInteractiveMessages** , puis cliquez sur **Manage NuGet Packages**. Cette opération affiche la fenêtre **Du Gestionnaire de package NuGet** .

3. Recherchez **WindowsAzure.ServiceBus**, cliquez sur **installer**et accepter les conditions d’utilisation. Cette opération télécharge, installe et ajoute une référence pour le [Bus de Service d’Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), avec toutes ses dépendances.

4. Ajoutez les instructions **using** suivantes en haut du fichier **Program.cs** :

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Enfin, ajoutez les lignes suivantes à la méthode **Main** . Remplacez par la chaîne de connexion avec des autorisations **d’écoute** pour la file d’attente nommée **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Dans Visual Studio, dans l’Explorateur de solutions, avec le bouton droit de votre solution, puis sélectionnez **Définir les projets de démarrage**. Sélectionnez **plusieurs projets de démarrage**, puis sélectionnez **Démarrer** l’action pour les projets **ProcessDeviceToCloudMessages**, **SimulatedDevice**et **ProcessD2CInteractiveMessages** .

2.  Appuyez sur **F5** pour démarrer les trois applications console. L’application **ProcessD2CInteractiveMessages** doit traiter tous les messages envoyés à partir de l’application **SimulatedDevice** interactif.

  ![Trois applications console][50]

> [AZURE.NOTE] Pour voir les mises à jour dans le blob, vous devrez peut-être réduire la constante **MAX_BLOCK_SIZE** dans la classe **StoreEventProcessor** à une valeur inférieure, par exemple **1024**. Cette modification est utile dans la mesure où elle prend un certain temps pour atteindre la limite de taille de bloc avec les données envoyées par le périphérique simulé. Avec une plus petite taille de bloc, vous n’avez pas besoin d’attendre si longtemps pour voir l’objet blob est créé et mis à jour. Toutefois, à l’aide d’une taille de bloc supérieure rend l’application plus évolutive.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment traiter fiable du point de données et des messages de périphérique-nuage interactives à l’aide de la classe [EventProcessorHost] .

[Comment envoyer des messages de nuage-dispositif avec concentrateur de IoT] [ lnk-c2d] vous montre comment envoyer des messages à vos équipements depuis votre back-end.

Pour voir des exemples de solutions de bout en bout complètes qui utilisent IoT Hub, consultez [Azure IoT Suite][lnk-suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT concentrateur].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Stockage des objets blob Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Usine de données Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[File d’attente de Bus de service]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guide du développeur Azure IoT Hub - périphérique vers le cloud]: iot-hub-devguide-messaging.md

[Stockage Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus des services Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guide du développeur IoT concentrateur]: iot-hub-devguide.md
[Mise en route de concentrateur de IoT]: iot-hub-csharp-csharp-getstarted.md
[Centre de développement Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[À propos du stockage Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Mise en route avec les concentrateurs d’événement]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Évolutivité de stockage Azure instructions]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Concentrateurs d’événement Guide de programmation]: ../event-hubs/event-hubs-programming-guide.md
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Créer des applications évolutives avec un Bus de Service]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
