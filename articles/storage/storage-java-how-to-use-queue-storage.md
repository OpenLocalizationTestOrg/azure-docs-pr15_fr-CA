<properties
    pageTitle="Comment faire pour utiliser le stockage de file d’attente à partir de Java | Microsoft Azure"
    description="Apprenez à utiliser le service de file d’attente d’Azure pour créer et supprimer des files d’attente, insérer, obtenir et supprimer les messages. Exemples écrits en Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-java"></a>Comment faire pour utiliser le stockage de file d’attente à partir de Java

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous explique comment exécuter des scénarios courants utilisant le service de stockage de file d’attente d’Azure. Les exemples sont écrits en Java et utilisent le [Stockage Azure SDK Java][]. Les scénarios présentés comprennent **Insertion**, **lecture**, **mise en route**et **suppression de** file d’attente messages, ainsi que les files d’attente de **Création** et de **suppression** . Pour plus d’informations sur les files d’attente, consultez la section [étapes suivantes](#Next-Steps) .

Remarque : Un kit de développement est disponible pour les développeurs qui utilisent le stockage Azure sur des appareils Android. Pour plus d’informations, consultez le [Kit de développement logiciel pour Android stockage Azure][].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Créer une application Java

Dans ce guide, vous allez utiliser les fonctionnalités de stockage qui peuvent être exécutées dans une application Java, localement ou dans le code s’exécutant dans un rôle de web ou travailleur dans Azure.

Pour ce faire, vous devez installer le Kit de développement Java (JDK) et créer un compte de stockage Azure dans votre abonnement Azure. Une fois que vous l’avez fait, vous devez vérifier que votre système de développement répond à la configuration minimale requise et les dépendances qui sont répertoriés dans le référentiel de [Stockage Azure SDK Java][] sur GitHub. Si votre système est conforme à ces exigences, vous pouvez suivre les instructions pour télécharger et installer les bibliothèques de stockage Azure pour Java sur votre système à partir de ce référentiel. Une fois ces tâches effectuées, vous serez en mesure de créer une application Java qui utilise les exemples de cet article.

## <a name="configure-your-application-to-access-queue-storage"></a>Configurez votre application pour accéder au stockage de la file d’attente

Ajoutez les instructions d’importation suivantes en haut du fichier Java où vous souhaitez utiliser des API de stockage Azure pour accéder aux files d’attente :

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## <a name="setup-an-azure-storage-connection-string"></a>Programme d’installation une chaîne de connexion du stockage Azure

Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker les informations d’identification pour l’accès aux services de gestion de données et les points de terminaison. Lors de l’exécution dans une application cliente, vous devez fournir la chaîne de connexion de stockage dans le format suivant, en utilisant le nom de votre compte de stockage et de la clé d’accès principal pour le compte de stockage répertorié dans [Azure Portal](https://portal.azure.com) pour les valeurs de *nom de compte* et *AccountKey* . Cet exemple montre comment vous pouvez déclarer un champ statique pour contenir la chaîne de connexion :

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

Dans une application s’exécutant dans un rôle dans Microsoft Azure, cette chaîne peut être stockée dans le fichier de configuration de service, *ServiceConfiguration.cscfg*et sont accessibles par un appel à la méthode **RoleEnvironment.getConfigurationSettings** . Voici un exemple de mise en route de la chaîne de connexion à partir d’un élément de **paramètre** nommé *StorageConnectionString* dans le fichier de configuration de service :

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Les exemples suivants supposent que vous avez utilisé une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## <a name="how-to-create-a-queue"></a>Comment : créer une file d’attente

Un objet **CloudQueueClient** vous permet d’obtenir des objets de référence pour les files d’attente. Le code suivant crée un objet **CloudQueueClient** . (Remarque : il existe des méthodes supplémentaires pour créer des objets de **CloudStorageAccount** ; pour plus d’informations, consultez **CloudStorageAccount** dans la [Référence du Kit de développement logiciel Client Azure stockage].)

Utilisez l’objet **CloudQueueClient** pour obtenir une référence à la file d’attente que vous souhaitez utiliser. Vous pouvez créer la file d’attente si elle n’existe pas.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the queue client.
       CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

       // Retrieve a reference to a queue.
       CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Create the queue if it doesn't already exist.
       queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-a-message-to-a-queue"></a>Comment : ajouter un message à une file d’attente

Pour insérer un message dans une file d’attente existante, commencez par créer un nouveau **CloudQueueMessage**. Ensuite, appelez la méthode **addMessage** . Un **CloudQueueMessage** peut être créée à partir d’une chaîne (au format de format UTF-8) ou un tableau d’octets. Voici le code qui crée une file d’attente (si elle n’existe pas) et insère le message « Hello, World ».

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Create the queue if it doesn't already exist.
        queue.createIfNotExists();

        // Create a message and add it to the queue.
        CloudQueueMessage message = new CloudQueueMessage("Hello, World");
        queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-peek-at-the-next-message"></a>Comment : lire le message suivant

Vous pouvez lire le message avant une file d’attente sans le supprimer de la file d’attente en appelant **peekMessage**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Peek at the next message.
        CloudQueueMessage peekedMessage = queue.peekMessage();

        // Output the message value.
        if (peekedMessage != null)
        {
          System.out.println(peekedMessage.getMessageContentAsString());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Comment : modifier le contenu d’un message en file d’attente

Vous pouvez modifier le contenu d’un message sur place dans la file d’attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour de l’état de la tâche. Le code suivant met à jour le message de la file d’attente avec le nouveau contenu et définit le délai d’attente de visibilité pour étendre un autre 60 secondes. Cela enregistre l’état du travail associé au message et donne au client un autre minute pour continuer à travailler sur le message. Vous pouvez utiliser cette technique pour effectuer le suivi de plusieurs étapes de flux de travail dans la file d’attente messages, sans avoir à recommencer depuis le début en cas d’échec d’une étape de traitement en raison d’une défaillance matérielle ou logicielle. En général, vous conservez un nombre d’essais, et si le message est relancé plus de *n* fois, vous le supprimez. Cette option empêche un message qui déclenche une erreur d’application chaque fois qu’elle est traitée.

L’exemple de code suivant recherche par le biais de la file d’attente de messages, localise le premier message qui correspond à « Hello, World » pour le contenu, puis modifie le contenu du message et quitte.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32.
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields =
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Sinon, l’exemple de code suivant met à jour uniquement le premier message visible sur la file d’attente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage message = queue.retrieveMessage();

        if (message != null)
        {
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-get-the-queue-length"></a>Comment : obtenir la longueur de la file d’attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La méthode **downloadAttributes** demande le service de file d’attente pour plusieurs valeurs en cours, y compris un décompte du nombre de messages dans une file d’attente. Le nombre n’est approximatif, car les messages peuvent être ajoutés ou supprimés une fois que le service de file d’attente répond à votre demande. La méthode **getApproximateMessageCount** renvoie la dernière valeur récupérée par l’appel à **downloadAttributes**, sans appeler le service de file d’attente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Download the approximate message count from the server.
        queue.downloadAttributes();

        // Retrieve the newly cached approximate message count.
        long cachedMessageCount = queue.getApproximateMessageCount();

        // Display the queue length.
        System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-dequeue-the-next-message"></a>Comment : file d’attente du message suivant

Votre code retire un message à partir d’une file d’attente en deux étapes. Lorsque vous appelez **retrieveMessage**, vous obtenez le message suivant dans une file d’attente. Un message retourné par **retrieveMessage** devienne invisible pour un autre code de lecture des messages de cette file d’attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour terminer la suppression du message de la file d’attente, vous devez également appeler **deleteMessage**. Ce processus en deux étapes de suppression d’un message permet de garantir que si votre code ne parvient pas à traiter un message en raison d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et essayez à nouveau. Votre code appelle **deleteMessage** droite une fois que le message a été traité.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage retrievedMessage = queue.retrieveMessage();

        if (retrievedMessage != null)
        {
            // Process the message in less than 30 seconds, and then delete the message.
            queue.deleteMessage(retrievedMessage);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## <a name="additional-options-for-dequeuing-messages"></a>Options supplémentaires pour le retrait des messages

Il existe deux méthodes que vous pouvez personnaliser la récupération des messages à partir d’une file d’attente. Tout d’abord, vous pouvez obtenir un lot de messages (32 maximum). Ensuite, vous pouvez définir un délai d’expiration de l’invisibilité plus longues ou plus courtes, permettant à votre code plus ou moins de temps à traiter entièrement chaque message.

L’exemple de code suivant utilise la méthode **retrieveMessages** pour obtenir les 20 messages en un seul appel. Il traite chaque message à l’aide d’une boucle **for** . Il définit également le délai d’expiration de l’invisibilité à 5 minutes (300 secondes) pour chaque message. Notez que les cinq minutes démarre pour tous les messages en même temps, donc lorsque cinq minutes se sont écoulées depuis l’appel à **retrieveMessages**, tous les messages qui n’ont pas été supprimés va devenir visibles à nouveau.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes,
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-queues"></a>Comment : répertorier les files d’attente

Pour obtenir une liste de files d’attente en cours, appelez la méthode **CloudQueueClient.listQueues()** , qui retourne une collection d’objets de **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-queue"></a>Comment : supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’il contient, appelez la méthode **deleteIfExists** sur l’objet **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du stockage de la file d’attente, cliquez sur ces liens pour en savoir plus sur les tâches de stockage plus complexes.

- [Stockage Azure SDK pour Java][]
- [Référence du Kit de développement logiciel Client stockage Azure][]
- [Services de stockage Azure API REST][]
- [Blog de l’équipe stockage Azure][]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Stockage Azure SDK pour Java]: https://github.com/azure/azure-storage-java
[Stockage Azure SDK pour Android]: https://github.com/azure/azure-storage-android
[Référence du Kit de développement logiciel Client stockage Azure]: http://dl.windowsazure.com/storage/javadoc/
[Services de stockage Azure API REST]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog de l’équipe stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
