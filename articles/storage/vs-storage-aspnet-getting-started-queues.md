<properties
    pageTitle="Mise en route de Visual Studio et de stockage de la file d’attente connectée services (ASP.NET) | Microsoft Azure"
    description="Comment démarrer l’utilisation du stockage de la file d’attente d’Azure dans un projet ASP.NET dans Visual Studio après la connexion à un compte de stockage à l’aide de Visual Studio de services connectés"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services"></a>Mise en route de la file d’attente d’Azure Visual Studio et stockage des services connectés

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment démarrer l’utilisation du stockage de la file d’attente d’Azure dans Visual Studio après avoir créé ou référencé d’un compte de stockage Azure dans un projet ASP.NET à l’aide de la boîte de dialogue de Visual Studio, **Ajouter des Services connectés** .

Nous allons vous montrer comment créer et accéder à une file d’attente d’Azure dans votre compte de stockage. Nous vous montrerons également comment effectuer des opérations de file d’attente de base, telles que l’ajout, modification, lecture et suppression des messages de la file d’attente. Les exemples sont écrits en code C# et utilisent la [Bibliothèque de Client Microsoft Azure stockage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Pour plus d’informations sur ASP.NET, consultez [ASP.NET](http://www.asp.net).

Stockage Azure de file d’attente est un service permettant de stocker de grandes quantités de messages qui sont accessibles à partir de n’importe où dans le monde via des appels authentifiés à l’aide de HTTP ou HTTPS. Un message de la file d’attente unique peut être jusqu'à 64 Ko, et une file d’attente peut contenir des millions de messages, dans la limite de la capacité totale d’un compte de stockage.

## <a name="access-queues-in-code"></a>Files d’attente de l’accès dans le code

Pour accéder aux files d’attente dans les projets ASP.NET, vous devez inclure les éléments suivants à n’importe quel fichier de code source C# qui a accès au stockage de la file d’attente d’Azure.

1. Assurez-vous que les déclarations d’espace de noms en haut du fichier C# incluent ces instructions **using** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Récupérez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d’obtenir le votre chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Récupérez un objet **CloudQueueClient** pour référencer les objets file d’attente dans votre compte de stockage.  

        // Create the CloudQueueClient object for this storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Récupérez un objet **CloudQueue** pour faire référence à une file d’attente spécifique.

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Remarque** Utiliser le code ci-dessus gauche du code dans les exemples suivants.

## <a name="create-a-queue-in-code"></a>Créer une file d’attente dans le code

Pour créer une file d’attente Azure dans le code, il suffit d’ajouter un appel à **CreateIfNotExists** pour le code ci-dessus.

    // Create the messageQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Ajouter un message à une file d’attente

Pour insérer un message dans une file d’attente existante, créez un nouvel objet **CloudQueueMessage** , puis appelez la méthode **AddMessage** .

Un objet **CloudQueueMessage** peut être créé à partir d’une chaîne (au format de format UTF-8) ou un tableau d’octets.

Voici un exemple qui insère le message « Hello, World ».

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Lire un message dans une file d’attente

Vous pouvez lire le message avant une file d’attente sans le supprimer de la file d’attente en appelant la méthode PeekMessage ().

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Lire et supprimer un message dans une file d’attente

Votre code peut supprimer (file d’attente) un message à partir d’une file d’attente en deux étapes.
1. Appelez GetMessage() pour obtenir le message suivant dans une file d’attente. Un message retourné à partir de GetMessage() devienne invisible pour un autre code de lecture des messages de cette file d’attente. Par défaut, ce message reste invisible pendant 30 secondes.
2.  Pour terminer la suppression du message de la file d’attente, appelez **DeleteMessage**.

Ce processus en deux étapes de suppression d’un message permet de garantir que si votre code ne parvient pas à traiter un message en raison d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et essayez à nouveau. Le code suivant appelle **DeleteMessage** droite une fois que le message a été traité.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-for-de-queuing-messages"></a>Utiliser des options supplémentaires pour les files d’attente de messages

Il existe deux méthodes que vous pouvez personnaliser la récupération des messages à partir d’une file d’attente.
Tout d’abord, vous pouvez obtenir un lot de messages (32 maximum). Ensuite, vous pouvez définir un délai d’expiration de l’invisibilité plus longues ou plus courtes, permettant à votre code plus ou moins de temps à traiter entièrement chaque message. L’exemple de code suivant utilise la méthode **GetMessages** pour obtenir les 20 messages en un seul appel. Il traite chaque message à l’aide d’une boucle **foreach** . Il définit également le délai d’expiration de l’invisibilité à cinq minutes pour chaque message. Notez que le démarrage 5 minutes pour tous les messages en même temps, après 5 minutes ont passé que depuis l’appel à **GetMessages**, tous les messages qui n’ont pas été supprimés va devenir visible à nouveau.

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

Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La méthode **FetchAttributes** demande la queueservice pour récupérer les attributs de la file d’attente, y compris le nombre de messages. La propriété **ApproximateMethodCount** renvoie la dernière valeur récupérée par la méthode **FetchAttributes** , sans appeler le queueservice.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-async-await-pattern-with-common-queueapis"></a>Utiliser Async Await un motif de queueAPIs courantes

Cet exemple montre comment utiliser le modèle de Async Await avec queueAPIs commune. L’exemple appelle la version asynchrone de chacune des méthodes donnés, ceci peut être observé par le post-correctif de Async de chaque méthode. Lorsqu’une méthode asynchrone est utilisée l’async-await modèle suspend l’exécution du locale jusqu'à ce que l’appel se termine. Ce comportement permet au thread en cours effectuer d’autres tâches qui permet d’éviter les goulots d’étranglement et améliore la réactivité de votre application. Pour plus d’informations sur l’utilisation du modèle de Async-Await dans .NET, consultez [Async et Await (C# et Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’il contient, appelez la méthode **Delete** de l’objet file d’attente.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]