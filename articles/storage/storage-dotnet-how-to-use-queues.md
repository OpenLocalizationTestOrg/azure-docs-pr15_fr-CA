<properties
    pageTitle="Mise en route de stockage Azure file à l’aide de .NET | Microsoft Azure"
    description="Les files d’attente Azure fournissent une messagerie fiable et asynchrone entre les composants de l’application. Les composants de votre application à l’échelle indépendamment du nuage permet de messagerie."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="robinsh"/>

# <a name="get-started-with-azure-queue-storage-using-net"></a>Mise en route de stockage Azure file à l’aide de .NET

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Stockage de file d’attente Azure fournit nuage entre les composants de l’application de messagerie. Dans la conception d’applications à l’échelle, les composants d’application sont souvent découplées, afin qu’ils peuvent faire évoluer indépendamment. Stockage de la file d’attente offre une messagerie asynchrone pour des communications entre les composants de l’application, si elles sont en cours d’exécution dans le nuage, sur le bureau, sur un serveur local ou sur un périphérique mobile. Stockage de la file d’attente prend également en charge la gestion des tâches asynchrones et la création de flux de travail de processus.

### <a name="about-this-tutorial"></a>À propos de ce didacticiel

Ce didacticiel explique comment écrire du code .NET dans quelques scénarios courants utilisant le stockage de la file d’attente d’Azure. Scénarios couverts comprennent la création et la suppression de files d’attente Ajout, lecture et suppression des messages de la file d’attente.

**Estimée durée :** 45 minutes

**Prerequisities :**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Bibliothèque Client de stockage Azure pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestionnaire de Configuration Azure pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Un [compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Ajouter des déclarations d’espace de noms

Ajoutez le code suivant `using` instructions en haut de la `program.cs` fichier :

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types

### <a name="parse-the-connection-string"></a>Analyser la chaîne de connexion

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Créer le client de service de file d’attente

La classe **CloudQueueClient** vous permet de récupérer des files d’attente stockées dans le stockage de la file d’attente. Voici une méthode pour créer le client du service :

    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Vous êtes maintenant prêt à écrire du code qui lit et écrit des données dans le stockage de la file d’attente.

## <a name="create-a-queue"></a>Créer une file d’attente

Cet exemple montre comment créer une file d’attente s’il n’existe pas déjà :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a container.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-a-message-into-a-queue"></a>Insérer un message dans une file d’attente

Pour insérer un message dans une file d’attente existante, commencez par créer un nouveau **CloudQueueMessage**. Ensuite, appelez la méthode **AddMessage** . Un **CloudQueueMessage** peut être créée à partir d’une chaîne (au format de format UTF-8) ou un tableau **d’octets** . Voici le code qui crée une file d’attente (si elle n’existe pas) et insère le message « Hello, World » :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## <a name="peek-at-the-next-message"></a>Lire le message suivant

Vous pouvez lire le message avant une file d’attente sans le supprimer de la file d’attente en appelant la méthode **PeekMessage** .

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## <a name="change-the-contents-of-a-queued-message"></a>Modifier le contenu d’un message en file d’attente

Vous pouvez modifier le contenu d’un message sur place dans la file d’attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour de l’état de la tâche. Le code suivant met à jour le message de la file d’attente avec le nouveau contenu et définit le délai d’attente de visibilité pour étendre un autre 60 secondes. Cela enregistre l’état du travail associé au message et donne au client un autre minute pour continuer à travailler sur le message. Vous pouvez utiliser cette technique pour effectuer le suivi de plusieurs étapes de flux de travail dans la file d’attente messages, sans avoir à recommencer depuis le début en cas d’échec d’une étape de traitement en raison d’une défaillance matérielle ou logicielle. En général, vous conservez un nombre d’essais, et si le message est relancé plus de *n* fois, vous le supprimez. Cette option empêche un message qui déclenche une erreur d’application chaque fois qu’elle est traitée.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## <a name="de-queue-the-next-message"></a>File d’attente du message suivant

Votre code des files d’attente de message d’une file d’attente en deux étapes. Lorsque vous appelez **GetMessage**, vous obtenez le message suivant dans une file d’attente. Un message retourné à partir de **GetMessage** devienne invisible pour un autre code de lecture des messages de cette file d’attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour terminer la suppression du message de la file d’attente, vous devez également appeler **DeleteMessage**. Ce processus en deux étapes de suppression d’un message permet de garantir que si votre code ne parvient pas à traiter un message en raison d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et essayez à nouveau. Votre code appelle **DeleteMessage** droite une fois que le message a été traité.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Utiliser motif de Async Await avec les API de stockage file d’attente commun

Cet exemple montre comment utiliser le modèle de Async Await avec les API de stockage file d’attente commun. L’exemple appelle la version asynchrone de chacune des méthodes donnés, comme indiqué par le suffixe *Async* de chaque méthode. Lorsqu’une méthode asynchrone est utilisée, l’async-await modèle suspend l’exécution du locale jusqu'à ce que l’appel se termine. Ce comportement permet au thread en cours effectuer d’autres tâches, ce qui permet d’éviter les goulots d’étranglement et améliore la réactivité de votre application. Pour plus d’informations sur l’utilisation du modèle asynchrone-Await dans .NET, consultez [Async et Await (C# et Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Exploiter des options supplémentaires pour les files d’attente de messages

Il existe deux méthodes que vous pouvez personnaliser la récupération des messages à partir d’une file d’attente.
Tout d’abord, vous pouvez obtenir un lot de messages (32 maximum). Ensuite, vous pouvez définir un délai d’expiration de l’invisibilité plus longues ou plus courtes, permettant à votre code plus ou moins de temps à traiter entièrement chaque message. L’exemple de code suivant utilise la méthode **GetMessages** pour obtenir les 20 messages en un seul appel. Il traite chaque message à l’aide d’une boucle **foreach** . Il définit également le délai d’expiration de l’invisibilité à cinq minutes pour chaque message. Notez que le démarrage 5 minutes pour tous les messages en même temps, après 5 minutes ont passé que depuis l’appel à **GetMessages**, tous les messages qui n’ont pas été supprimés va devenir visible à nouveau.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Obtenir la longueur de la file d’attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La méthode **FetchAttributes** demande le service de file d’attente pour récupérer les attributs de la file d’attente, y compris le nombre de messages. La propriété **ApproximateMessageCount** renvoie la dernière valeur récupérée par la méthode **FetchAttributes** , sans appeler le service de file d’attente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="delete-a-queue"></a>Supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’il contient, appelez la méthode **Delete** de l’objet file d’attente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du stockage de la file d’attente, cliquez sur ces liens pour en savoir plus sur les tâches de stockage plus complexes.

- Consulter la documentation de référence de service de file d’attente pour plus d’informations sur l’API disponibles :
    - [Bibliothèque Client de stockage de référence .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Référence de l’API REST](http://msdn.microsoft.com/library/azure/dd179355)
- Découvrez comment simplifier le code que vous écrivez pour utiliser le stockage Azure à l’aide du [Kit de développement logiciel Azure WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md).
- Permet d’afficher plusieurs guides d’en savoir plus sur les options supplémentaires pour le stockage des données dans Azure.
    - [Mise en route de stockage Azure Table à l’aide de .NET](storage-dotnet-how-to-use-tables.md) pour stocker des données structurées.
    - [Mise en route de stockage Azure Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md) pour stocker des données non structurées.
    - [Se connecter à la base de données SQL à l’aide de .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) pour stocker des données relationnelles.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
