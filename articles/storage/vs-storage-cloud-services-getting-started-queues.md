<properties
    pageTitle="Mise en route de Visual Studio et de stockage de la file d’attente connectée services (services en nuage) | Microsoft Azure"
    description="Comment démarrer l’utilisation du stockage de la file d’attente d’Azure dans un projet de service cloud dans Visual Studio après la connexion à un compte de stockage à l’aide de Visual Studio de services connectés"
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
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Mise en route avec Visual Studio et de stockage de la file d’attente d’Azure connecté services (services en nuage de projets)

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment démarrer l’utilisation du stockage de la file d’attente d’Azure dans Visual Studio après avoir créé ou référencé d’un compte de stockage Azure dans un projet de services cloud à l’aide de la boîte de dialogue de Visual Studio, **Ajouter des Services connectés** .

Nous allons vous montrer comment créer une file d’attente dans le code. Nous vous montrerons également comment effectuer des opérations de file d’attente de base, telles que l’ajout, modification, lecture et suppression des messages de la file d’attente. Les exemples sont écrits en code C# et utilisent la [Bibliothèque de Client Microsoft Azure stockage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

L’opération **Ajouter Connected Services** installe les packages NuGet appropriés pour accéder au stockage Azure dans votre projet et ajoute la chaîne de connexion pour le compte de stockage pour vos fichiers de configuration de projet.

 - Pour plus d’informations sur les files d’attente de manipulation dans le code, consultez [mise en route de stockage de la file d’attente de Azure à l’aide de .NET](storage-dotnet-how-to-use-queues.md) .
 - Consultez la [documentation du stockage](https://azure.microsoft.com/documentation/services/storage/) pour des informations générales sur le stockage Azure.
 - Pour des informations générales sur les services en nuage Azure, consultez [la documentation des Services de nuage](https://azure.microsoft.com/documentation/services/cloud-services/) .
 - Pour plus d’informations sur la programmation d’applications d’ASP.NET, consultez [ASP.NET](http://www.asp.net) .


Stockage Azure de file d’attente est un service permettant de stocker de grandes quantités de messages qui sont accessibles à partir de n’importe où dans le monde via des appels authentifiés à l’aide de HTTP ou HTTPS. Un message de la file d’attente unique peut être jusqu'à 64 Ko, et une file d’attente peut contenir des millions de messages, dans la limite de la capacité totale d’un compte de stockage.


## <a name="access-queues-in-code"></a>Files d’attente de l’accès dans le code

Pour accéder aux files d’attente dans les projets de Services de Cloud de Visual Studio, vous devez inclure les éléments suivants à n’importe quel fichier de code source C# qui a accès au stockage de la file d’attente d’Azure.

1. Assurez-vous que les déclarations d’espace de noms en haut du fichier C# incluent ces instructions **using** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Récupérez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d’obtenir le votre chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Récupérez un objet **CloudQueueClient** pour référencer les objets file d’attente dans votre compte de stockage.  

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Récupérez un objet **CloudQueue** pour faire référence à une file d’attente spécifique.

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Remarque :** Utiliser le code ci-dessus gauche du code dans les exemples suivants.

## <a name="create-a-queue-in-code"></a>Créer une file d’attente dans le code

Pour créer la file d’attente dans le code, il suffit d’ajouter un appel à **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Ajouter un message à une file d’attente

Pour insérer un message dans une file d’attente existante, créez un nouvel objet **CloudQueueMessage** , puis appelez la méthode **AddMessage** .

Un objet **CloudQueueMessage** peut être créé à partir d’une chaîne (au format de format UTF-8) ou un tableau d’octets.

Voici un exemple qui insère le message « Hello, World ».

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Lire un message dans une file d’attente

Vous pouvez lire le message avant une file d’attente sans le supprimer de la file d’attente en appelant la méthode **PeekMessage** .

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Lire et supprimer un message dans une file d’attente

Votre code peut supprimer (file d’attente) un message à partir d’une file d’attente en deux étapes.

1. Appelez **GetMessage** pour obtenir le message suivant dans une file d’attente. Un message retourné à partir de **GetMessage** devienne invisible pour un autre code de lecture des messages de cette file d’attente. Par défaut, ce message reste invisible pendant 30 secondes.
2.  Pour terminer la suppression du message de la file d’attente, appelez **DeleteMessage**.

Ce processus en deux étapes de suppression d’un message permet de garantir que si votre code ne parvient pas à traiter un message en raison d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et essayez à nouveau. Le code suivant appelle **DeleteMessage** droite une fois que le message a été traité.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Utiliser des options supplémentaires pour traiter et supprimer des messages de la file d’attente

Il existe deux méthodes que vous pouvez personnaliser la récupération des messages à partir d’une file d’attente.

 - Vous pouvez obtenir un lot de messages (32 maximum).
 - Vous pouvez définir un délai d’expiration de l’invisibilité plus longues ou plus courtes, permettant à votre code plus ou moins de temps à traiter entièrement chaque message. L’exemple de code suivant utilise la méthode **GetMessages** pour obtenir les 20 messages en un seul appel. Il traite chaque message à l’aide d’une boucle **foreach** . Il définit également le délai d’expiration de l’invisibilité à cinq minutes pour chaque message. Notez que le démarrage 5 minutes pour tous les messages en même temps, après 5 minutes ont passé que depuis l’appel à **GetMessages**, tous les messages qui n’ont pas été supprimés va devenir visible à nouveau.

Voici un exemple :

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Obtenir la longueur de la file d’attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La méthode **FetchAttributes** demande le service de file d’attente pour récupérer les attributs de la file d’attente, y compris le nombre de messages. La propriété **ApproximateMethodCount** renvoie la dernière valeur récupérée par la méthode **FetchAttributes** , sans appeler le service de file d’attente.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Utiliser le modèle de Async Await avec les API de file d’attente Azure courantes

Cet exemple montre comment utiliser le modèle Async Await avec les API de file d’attente commune Azure. L’exemple appelle la version asynchrone de chacune des méthodes donnés, ceci peut être observé par le post-correctif de **Async** de chaque méthode. Lorsqu’une méthode asynchrone est utilisée l’async-await modèle suspend l’exécution du locale jusqu'à ce que l’appel se termine. Ce comportement permet au thread en cours effectuer d’autres tâches qui permet d’éviter les goulots d’étranglement et améliore la réactivité de votre application. Pour plus d’informations sur l’utilisation du modèle de Async-Await dans .NET, consultez [Async et Await (C# et Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’il contient, appelez la méthode **Delete** de l’objet file d’attente.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
