<properties
    pageTitle="Comment faire pour utiliser le stockage de file d’attente (C++) | Microsoft Azure"
    description="Apprenez à utiliser le service de stockage de file d’attente dans Azure. Exemples sont écrits en C++."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-queue-storage-from-c"></a>Comment faire pour utiliser le stockage de file d’attente à partir de C++  

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble
Ce guide vous explique comment exécuter des scénarios courants utilisant le service de stockage de file d’attente d’Azure. Les exemples sont écrits en C++ et utilisent la [Bibliothèque de Client de stockage Azure pour C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Les scénarios présentés comprennent **Insertion**, **lecture**, **mise en route**et **suppression de** file d’attente messages, ainsi que **Création et suppression de files d’attente**.

>[AZURE.NOTE] Ce guide vise la bibliothèque cliente du stockage Azure pour C++ version 1.0.0 et au-dessus. La version recommandée est bibliothèque de Client de stockage 2.2.0, qui est disponible via [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](http://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Créez une application C++  
Dans ce guide, vous allez utiliser les fonctionnalités de stockage qui peuvent être exécutées dans une application C++.

Pour ce faire, vous devez installer la bibliothèque cliente du stockage Azure pour C++ et créer un compte de stockage Azure dans votre abonnement Azure.

Pour installer la bibliothèque cliente du stockage Azure pour C++, vous pouvez utiliser les méthodes suivantes :

-   **Linux :** Suivez les instructions fournies dans la page de la [Bibliothèque de Client de stockage Azure pour C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
-   **Windows :** Dans Visual Studio, cliquez sur **Outils > du Gestionnaire de package NuGet > Console de Gestionnaire de package**. Tapez la commande suivante dans la [console du Gestionnaire de package de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) et appuyez sur **entrée**.

        Install-Package wastorage

## <a name="configure-your-application-to-access-queue-storage"></a>Configurez votre application pour accéder au stockage de la file d’attente
Ajoutez que les éléments suivants incluent des instructions au début du fichier C++ où vous souhaitez utiliser les API de stockage Azure pour accéder aux files d’attente :  

    #include "was/storage_account.h"
    #include "was/queue.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurer une chaîne de connexion du stockage Azure

Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker les informations d’identification pour l’accès aux services de gestion de données et les points de terminaison. Lors de l’exécution dans une application cliente, vous devez fournir la chaîne de connexion de stockage dans le format suivant, en utilisant le nom de votre compte de stockage et de la clé d’accès de stockage pour le compte de stockage répertorié dans [Azure Portal](https://portal.azure.com) pour les valeurs de *nom de compte* et *AccountKey* . Pour plus d’informations sur les comptes de stockage et les touches d’accès rapide, reportez-vous à la section [Sur les comptes de stockage Azure](storage-create-storage-account.md). Cet exemple montre comment vous pouvez déclarer un champ statique pour contenir la chaîne de connexion :  

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Pour tester votre application sur votre ordinateur Windows local, vous pouvez utiliser Microsoft Azure [émulateur de stockage](storage-use-emulator.md) qui est installé avec le [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/). L’émulateur de stockage est un utilitaire qui simule les Blob, file d’attente et Table des services disponibles dans Azure sur votre ordinateur de développement local. L’exemple suivant montre comment déclarer un champ statique pour contenir la chaîne de connexion pour votre émulateur de stockage local :  

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Pour démarrer l’émulateur de stockage Azure, cliquez sur le bouton **Démarrer** ou appuyez sur la touche **Windows** . Commencez à taper **l’Émulateur de stockage Azure**et sélectionnez **l’Émulateur de stockage Microsoft Azure** à partir de la liste des applications.

Les exemples suivants supposent que vous avez utilisé une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## <a name="retrieve-your-connection-string"></a>Récupérer la chaîne de connexion
Vous pouvez utiliser la classe **cloud_storage_account** pour représenter les informations de votre compte de stockage. Pour récupérer les informations de votre compte de stockage à partir de la chaîne de connexion de stockage, vous pouvez utiliser la méthode **parse** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-queue"></a>Comment : créer une file d’attente
Un objet **cloud_queue_client** vous permet d’obtenir des objets de référence pour les files d’attente. Le code suivant crée un objet **cloud_queue_client** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create a queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

Utilisez l’objet **cloud_queue_client** pour obtenir une référence à la file d’attente que vous souhaitez utiliser. Vous pouvez créer la file d’attente si elle n’existe pas.

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();  

## <a name="how-to-insert-a-message-into-a-queue"></a>Comment : insérer un message dans une file d’attente
Pour insérer un message dans une file d’attente existante, commencez par créer un nouveau **cloud_queue_message**. Ensuite, appelez la méthode **add_message** . Un **cloud_queue_message** peut être créée à partir d’une chaîne ou un tableau **d’octets** . Voici le code qui crée une file d’attente (si elle n’existe pas) et insère le message « Hello, World » :

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();

    // Create a message and add it to the queue.
    azure::storage::cloud_queue_message message1(U("Hello, World"));
    queue.add_message(message1);  

## <a name="how-to-peek-at-the-next-message"></a>Comment : lire le message suivant
Vous pouvez lire le message avant une file d’attente sans le supprimer de la file d’attente en appelant la méthode **peek_message** .

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Peek at the next message.
    azure::storage::cloud_queue_message peeked_message = queue.peek_message();

    // Output the message content.
    std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Comment : modifier le contenu d’un message en file d’attente
Vous pouvez modifier le contenu d’un message sur place dans la file d’attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour de l’état de la tâche. Le code suivant met à jour le message de la file d’attente avec le nouveau contenu et définit le délai d’attente de visibilité pour étendre un autre 60 secondes. Cela enregistre l’état du travail associé au message et donne au client un autre minute pour continuer à travailler sur le message. Vous pouvez utiliser cette technique pour effectuer le suivi de plusieurs étapes de flux de travail dans la file d’attente messages, sans avoir à recommencer depuis le début en cas d’échec d’une étape de traitement en raison d’une défaillance matérielle ou logicielle. En général, vous conservez un nombre d’essais, et si le message est relancé plus de n fois, vous le supprimez. Cette option empêche un message qui déclenche une erreur d’application chaque fois qu’elle est traitée.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the message from the queue and update the message contents.
    // The visibility timeout "0" means make it visible immediately.
    // The visibility timeout "60" means the client can get another minute to continue
    // working on the message.
    azure::storage::cloud_queue_message changed_message = queue.get_message();

    changed_message.set_content(U("Changed message"));
    queue.update_message(changed_message, std::chrono::seconds(60), true);

    // Output the message content.
    std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## <a name="how-to-de-queue-the-next-message"></a>Comment : désactiver le prochain message de la file d’attente
Votre code des files d’attente de message d’une file d’attente en deux étapes. Lorsque vous appelez **get_message**, vous obtenez le message suivant dans une file d’attente. Un message retourné par **get_message** devienne invisible pour un autre code de lecture des messages de cette file d’attente. Pour terminer la suppression du message de la file d’attente, vous devez également appeler **delete_message**. Ce processus en deux étapes de suppression d’un message permet de garantir que si votre code ne parvient pas à traiter un message en raison d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et essayez à nouveau. Votre code appelle **delete_message** droite une fois que le message a été traité.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the next message.
    azure::storage::cloud_queue_message dequeued_message = queue.get_message();
    std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

    // Delete the message.
    queue.delete_message(dequeued_message);

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Comment : exploiter des options supplémentaires pour les files d’attente de messages
Il existe deux méthodes que vous pouvez personnaliser la récupération des messages à partir d’une file d’attente. Tout d’abord, vous pouvez obtenir un lot de messages (32 maximum). Ensuite, vous pouvez définir un délai d’expiration de l’invisibilité plus longues ou plus courtes, permettant à votre code plus ou moins de temps à traiter entièrement chaque message. L’exemple de code suivant utilise la méthode **get_messages** pour obtenir les 20 messages en un seul appel. Il traite chaque message à l’aide d’une boucle **for** . Il définit également le délai d’expiration de l’invisibilité à cinq minutes pour chaque message. Notez que le démarrage 5 minutes pour tous les messages en même temps, après 5 minutes ont passé que depuis l’appel à **get_messages**, tous les messages qui n’ont pas été supprimés va devenir visible à nouveau.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
    // 5 minutes (300 seconds).
    azure::storage::queue_request_options options;
    azure::storage::operation_context context;

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

    for (auto it = messages.cbegin(); it != messages.cend(); ++it)
    {
        // Display the contents of the message.
        std::wcout << U("Get: ") << it->content_as_string() << std::endl;
    }

## <a name="how-to-get-the-queue-length"></a>Comment : obtenir la longueur de la file d’attente
Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La méthode **download_attributes** demande le service de file d’attente pour récupérer les attributs de la file d’attente, y compris le nombre de messages. La méthode **approximate_message_count** Obtient le nombre approximatif de messages dans la file d’attente.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Fetch the queue attributes.
    queue.download_attributes();

    // Retrieve the cached approximate message count.
    int cachedMessageCount = queue.approximate_message_count();

    // Display number of messages.
    std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## <a name="how-to-delete-a-queue"></a>Comment : supprimer une file d’attente
Pour supprimer une file d’attente et tous les messages qu’il contient, appelez la méthode **delete_queue_if_exists** sur l’objet de file d’attente.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // If the queue exists and delete it.
    queue.delete_queue_if_exists();  

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du stockage de la file d’attente, cliquez sur ces liens pour en savoir plus sur le stockage Azure.

-   [Comment faire pour utiliser le stockage Blob à partir de C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Comment faire pour utiliser le stockage de Table à partir de C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Liste des ressources de stockage Azure dans C++](storage-c-plus-plus-enumeration.md)
-   [Bibliothèque Client de stockage pour la référence C++](http://azure.github.io/azure-storage-cpp)
-   [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)

