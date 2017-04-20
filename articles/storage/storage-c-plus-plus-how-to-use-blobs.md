<properties
    pageTitle="Comment faire pour utiliser le stockage blob (stockage de l’objet) à partir de C++ | Microsoft Azure"
    description="Stocker des données non structurées dans le nuage avec les Blob Azure le stockage (objet)."
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

# <a name="how-to-use-blob-storage-from-c"></a>Comment faire pour utiliser le stockage Blob à partir de C++  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Stockage de Blob Azure est un service qui stocke les données non structurées dans le nuage en tant qu’objets/objets BLOB. Stockage des objets BLOB peut stocker n’importe quel type de données texte ou binaires, comme un document, un fichier multimédia ou un programme d’installation de l’application. Stockage des objets BLOB est également appelée stockage d’objets.

Ce guide va vous montrer comment réaliser des scénarios courants utilisant le service de stockage Azure Blob. Les exemples sont écrits en C++ et utilisent la [Bibliothèque de Client de stockage Azure pour C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Les scénarios présentés incluent le **téléchargement**, **liste**, **téléchargement**et **Supprimer** les objets BLOB.  

>[AZURE.NOTE] Ce guide vise la bibliothèque cliente du stockage Azure pour C++ version 1.0.0 et au-dessus. La version recommandée est bibliothèque de Client de stockage 2.2.0, qui est disponible via [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Créez une application C++
Dans ce guide, vous allez utiliser les fonctionnalités de stockage qui peuvent être exécutées dans une application C++.  

Pour ce faire, vous devez installer la bibliothèque cliente du stockage Azure pour C++ et créer un compte de stockage Azure dans votre abonnement Azure.   

Pour installer la bibliothèque cliente du stockage Azure pour C++, vous pouvez utiliser les méthodes suivantes :

-   **Linux :** Suivez les instructions fournies dans la page de la [Bibliothèque de Client de stockage Azure pour C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows :** Dans Visual Studio, cliquez sur **Outils > du Gestionnaire de package NuGet > Console de Gestionnaire de package**. Tapez la commande suivante dans la [console du Gestionnaire de package de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) et appuyez sur **entrée**.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Configurez votre application pour accéder au stockage de Blob  
Ajoutez que les instructions au début du fichier C++ où vous souhaitez utiliser les API de stockage Azure pour accéder aux objets BLOB include suivantes :  

    #include "was/storage_account.h"
    #include "was/blob.h"

## <a name="setup-an-azure-storage-connection-string"></a>Programme d’installation une chaîne de connexion du stockage Azure
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

Ensuite, obtenez une référence à une classe **cloud_blob_client** car elle permet de récupérer des objets qui représentent des conteneurs et des blobs stockées dans le Service de stockage Blob. Le code suivant crée un objet **cloud_blob_client** à l’aide de l’objet de compte de stockage que nous récupérés ci-dessus :  

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## <a name="how-to-create-a-container"></a>Comment : créer un conteneur

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Cet exemple montre comment créer un conteneur, s’il n’existe pas déjà :  

    try
    {
        // Retrieve storage account from connection string.
        azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

        // Create the blob client.
        azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

        // Retrieve a reference to a container.
        azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

        // Create the container if it doesn't already exist.
        container.create_if_not_exists();
    }
    catch (const std::exception& e)
    {
        std::wcout << U("Error: ") << e.what() << std::endl;
    }  

Par défaut, le nouveau conteneur est privé et vous devez spécifier votre clé d’accès pour télécharger les objets BLOB de ce conteneur de stockage. Si vous souhaitez rendre les fichiers (BLOB) dans le conteneur à tout le monde, vous pouvez définir le conteneur public utilisant le code suivant :  

    // Make the blob container publicly accessible.
    azure::storage::blob_container_permissions permissions;
    permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
    container.upload_permissions(permissions);  

Toute personne sur Internet peut voir les objets BLOB dans un conteneur public, mais vous pouvez les modifier ou les supprimer que si vous disposez de la clé d’accès approprié.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Comment : télécharger un blob dans un conteneur
Stockage de Blob Azure prend en charge les objets BLOB de page et les objets BLOB de bloc. Dans la plupart des cas, blob de bloc est le type recommandé à utiliser.  

Pour télécharger un fichier à un objet blob de bloc, obtenir une référence de conteneur et de l’utiliser pour obtenir une référence d’objet blob de bloc. Une fois que vous avez une référence d’objet blob, vous pouvez télécharger n’importe quel flux de données lui en appelant la méthode **upload_from_stream** . Cette opération crée le blob s’il n’a pas été précédemment existe ou le remplacer si elle existe. L’exemple suivant montre comment charger un objet blob dans un conteneur et suppose que le conteneur a déjà été créé.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Create or overwrite the "my-blob-1" blob with contents from a local file.
    concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
    blockBlob.upload_from_stream(input_stream);
    input_stream.close().wait();

    // Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
    // Retrieve a reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
    blob2.upload_text(U("more text"));

    // Retrieve a reference to a blob named "my-blob-3".
    azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
    blob3.upload_text(U("other text"));  

Sinon, vous pouvez utiliser la méthode **upload_from_file** pour télécharger un fichier à un objet blob de bloc.

## <a name="how-to-list-the-blobs-in-a-container"></a>Comment : répertorier les objets BLOB dans un conteneur
Pour répertorier les objets BLOB dans un conteneur, commencez par obtenir une référence de conteneur. Vous pouvez ensuite utiliser la méthode de **list_blobs** du conteneur pour récupérer les objets BLOB et/ou les répertoires qu’il contient. Pour accéder à l’ensemble complet de propriétés et de méthodes pour un retour de **list_blob_item**, vous devez appeler la méthode **list_blob_item.as_blob** pour obtenir un objet **cloud_blob** ou la méthode **list_blob.as_directory** pour obtenir un objet cloud_blob_directory. Le code suivant montre comment récupérer et l’URI de chaque élément dans le conteneur de **conteneur de mon exemple** de sortie :

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Output URI of each item.
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
        }
        else
        {
            std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
    }

Pour plus de détails sur les opérations de mise en vente, consultez la [Liste des ressources de stockage Azure dans C++](storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Comment : télécharger des objets BLOB
Pour télécharger les objets BLOB, tout d’abord extraire une référence d’objet blob et appelez ensuite la méthode **download_to_stream** . L’exemple suivant utilise la méthode **download_to_stream** pour transférer le contenu de l’objet blob dans un objet stream qui vous pouvez conserver ensuite dans un fichier local.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Save blob contents to a file.
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    blockBlob.download_to_stream(output_stream);

    std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();

    outfile.write((char *)&data[0], buffer.size());
    outfile.close();  

Sinon, vous pouvez utiliser la méthode **download_to_file** pour télécharger le contenu d’un objet blob dans un fichier.
En outre, vous pouvez également utiliser la méthode **download_text** pour télécharger le contenu d’un objet blob sous la forme d’une chaîne de texte.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

    // Download the contents of a blog as a text string.
    utility::string_t text = text_blob.download_text();

## <a name="how-to-delete-blobs"></a>Comment : supprimer des objets BLOB
Pour supprimer un objet blob, tout d’abord obtenir une référence d’objet blob, puis appelez la méthode **delete_blob** sur elle.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Delete the blob.
    blockBlob.delete_blob();

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris les notions de base de stockage des objets blob, suivre ces liens pour en savoir plus sur le stockage Azure.  

-   [Comment faire pour utiliser le stockage de file d’attente à partir de C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Comment faire pour utiliser le stockage de Table à partir de C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Liste des ressources de stockage Azure dans C++](storage-c-plus-plus-enumeration.md)
-   [Bibliothèque Client de stockage pour la référence C++](http://azure.github.io/azure-storage-cpp)
-   [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
