<properties
    pageTitle="Mise en route avec les Blob Azure le stockage (objet) à l’aide de .NET | Microsoft Azure"
    description="Stocker des données non structurées dans le nuage avec les Blob Azure le stockage (objet)."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-blob-storage-using-net"></a>Mise en route de stockage Azure Blob à l’aide de .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Stockage de Blob Azure est un service qui stocke les données non structurées dans le nuage en tant qu’objets/objets BLOB. Stockage des objets BLOB peut stocker n’importe quel type de données texte ou binaires, comme un document, un fichier multimédia ou un programme d’installation de l’application. Stockage des objets BLOB est également appelée stockage d’objets.

### <a name="about-this-tutorial"></a>À propos de ce didacticiel

Ce didacticiel explique comment écrire du code .NET pour les scénarios courants à l’aide du stockage des objets Blob Azure. Scénarios couverts comprennent le téléchargement de liste, téléchargement et suppression d’objets BLOB.

**Estimée durée :** 45 minutes

**Prerequisities :**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Bibliothèque Client de stockage Azure pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestionnaire de Configuration Azure pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Un [compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Plus d’échantillons

Pour des exemples supplémentaires à l’aide du stockage des objets Blob, reportez-vous à la section [Mise en route avec le stockage Blob Azure dans .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Vous pouvez télécharger l’exemple d’application et l’exécuter ou parcourir le code de GitHub.


[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Ajouter des déclarations d’espace de noms

Ajoutez le code suivant `using` instructions en haut de la `program.cs` fichier :

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### <a name="parse-the-connection-string"></a>Analyser la chaîne de connexion

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Créer le client de service d’objet Blob

La classe **CloudBlobClient** vous permet de récupérer les conteneurs et les objets BLOB stockés dans le stockage Blob. Voici une méthode pour créer le client du service :

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Vous êtes maintenant prêt à écrire du code qui lit et écrit des données dans le stockage Blob.

## <a name="create-a-container"></a>Créer un conteneur

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Cet exemple montre comment créer un conteneur, s’il n’existe pas déjà :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Par défaut, le nouveau conteneur est privé, c'est-à-dire que vous devez spécifier votre clé d’accès pour télécharger les objets BLOB de ce conteneur de stockage. Si vous souhaitez rendre disponibles les fichiers dans le conteneur à tout le monde, vous pouvez définir le conteneur public utilisant le code suivant :

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

Toute personne sur Internet peut voir les objets BLOB dans un conteneur public, mais vous pouvez les modifier ou les supprimer que si vous disposez de la clé d’accès de compte approprié ou une signature de l’accès partagé.

## <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Stockage de Blob Azure prend en charge les objets BLOB de page et les objets BLOB de bloc.  Dans la plupart des cas, blob de bloc est le type recommandé à utiliser.

Pour télécharger un fichier à un objet blob de bloc, obtenir une référence de conteneur et de l’utiliser pour obtenir une référence d’objet blob de bloc. Une fois que vous avez une référence d’objet blob, vous pouvez télécharger n’importe quel flux de données lui en appelant la méthode **UploadFromStream** . Cette opération crée le blob s’il n’a pas été précédemment existe ou le remplacer si elle existe.

L’exemple suivant montre comment charger un objet blob dans un conteneur et suppose que le conteneur a déjà été créé.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Liste les objets BLOB dans un conteneur

Pour répertorier les objets BLOB dans un conteneur, commencez par obtenir une référence de conteneur. Vous pouvez ensuite utiliser la méthode de **ListBlobs** du conteneur pour récupérer les objets BLOB et/ou les répertoires qu’il contient. Pour accéder à l’ensemble des propriétés et des méthodes pour un retour de **IListBlobItem**, vous devez effectuer un cast d’un objet **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** .  Si le type est inconnu, vous pouvez utiliser un contrôle de type afin de déterminer lequel effectuer un cast en.  Le code suivant montre comment récupérer et sortir de l’URI de chaque élément de la `photos` conteneur :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Comme indiqué ci-dessus, vous pouvez nommer les objets BLOB avec les informations de chemin d’accès dans leurs noms. Cela crée une structure de répertoires virtuels que vous pouvez organiser et parcourir comme vous le feriez pour un système de fichiers traditionnel. Notez que la structure de répertoire virtuelle n’est les seules ressources disponibles dans le stockage Blob sont des conteneurs et des objets BLOB. Cependant, la bibliothèque cliente de stockage offre un objet **CloudBlobDirectory** pour faire référence à un répertoire virtuel et de simplifier le processus de l’utilisation des objets BLOB qui est organisés de cette manière.

Par exemple, considérez l’ensemble suivant d’objets BLOB de bloc dans un conteneur nommé `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Lorsque vous appelez **ListBlobs** sur le conteneur « photos » (comme dans l’exemple ci-dessus), une liste hiérarchique est retournée. Il contient les objets à la fois **CloudBlobDirectory** et **CloudBlockBlob** , qui représente les répertoires et les objets BLOB dans le conteneur, respectivement. Le résultat ressemble à :

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Si vous le souhaitez, vous pouvez définir le paramètre **UseFlatBlobListing** de la méthode **ListBlobs** à **true**. Dans ce cas, chaque objet blob dans le conteneur est retournée sous la forme d’un objet **CloudBlockBlob** . L’appel à **ListBlobs** pour retourner une liste à deux dimensions ressemble à ceci :

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

et les résultats se présenter comme suit :

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## <a name="download-blobs"></a>Télécharger des objets BLOB

Pour télécharger les objets BLOB, tout d’abord extraire une référence d’objet blob et appelez ensuite la méthode **DownloadToStream** . L’exemple suivant utilise la méthode **DownloadToStream** pour transférer le contenu de l’objet blob dans un objet stream qui vous pouvez conserver ensuite dans un fichier local.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Vous pouvez également utiliser la méthode **DownloadToStream** pour télécharger le contenu d’un objet blob sous la forme d’une chaîne de texte.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Supprimer des objets BLOB

Pour supprimer un objet blob, obtenez d’abord une référence d’objet blob et ensuite appeler la méthode **Delete** sur celui-ci.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Liste des objets BLOB dans les pages en mode asynchrone

Si vous mettez en vente un grand nombre d’objets BLOB, ou si vous voulez contrôler le nombre de résultats que vous renvoyez en une seule opération de liste, vous pouvez répertorier les objets BLOB dans les pages de résultats. Cet exemple montre comment renvoyer des résultats dans les pages de manière asynchrone, afin que l’exécution n’est pas bloquée en attendant de retourner un grand jeu de résultats.

Cet exemple montre un blob plat mise en vente, mais vous pouvez également effectuer une liste hiérarchique, en définissant le `useFlatBlobListing` paramètre de la méthode de **ListBlobsSegmentedAsync** à `false`.

Étant donné que l’exemple de méthode appelle une méthode asynchrone, il doit être précédé du `async` mot clé et il doivent retourner un objet **Task** . Le mot clé await spécifié pour la méthode **ListBlobsSegmentedAsync** suspend l’exécution de l’exemple de méthode jusqu'à la fin de la tâche de la liste.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="writing-to-an-append-blob"></a>Écriture dans un blob append

Un blob append est un nouveau type d’objet blob introduite avec la version 5.x de la bibliothèque de client de stockage Azure pour .NET. Un blob append est optimisé pour les opérations d’ajout, telles que l’enregistrement. Comme un objet blob de bloc, un blob append est composé de blocs, mais lorsque vous ajoutez un nouveau bloc à un blob append, elle est toujours ajoutée à la fin de l’objet blob. Impossible de mettre à jour ou de supprimer un bloc existant dans un blob append. Le bloc ID pour un blob append ne sont pas exposés comme pour un blob de bloc.

Chaque bloc dans un blob append peut être d’une taille différente, avec un maximum de 4 Mo, et un blob append peut inclure un maximum de 50 000 blocs. La taille maximale d’un blob append est donc légèrement supérieure à 195 Go (4 Mo X 50000 blocs).

L’exemple ci-dessous crée un blob Ajouter nouveau et ajoute des données, simulation d’une opération d’enregistrement simple.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Pour plus d’informations sur les différences entre les trois types d’objets BLOB, reportez-vous à la section [présentation bloc BLOB, BLOB de la Page et ajouter les objets BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx) .

## <a name="managing-security-for-blobs"></a>Gestion de la sécurité pour les objets BLOB

Par défaut, le stockage Azure conserve vos données sécurisées en limitant l’accès au propriétaire du compte, qui détient les clés de l’accès du compte. Lorsque vous avez besoin de partager des données blob dans votre compte de stockage, il est important de le faire sans compromettre la sécurité de vos clés d’accès de compte. En outre, vous pouvez crypter les données blob pour garantir la sécurité de connexion sur le réseau et dans le stockage Azure.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Contrôler l’accès aux données blob

Par défaut, les données blob dans votre compte de stockage sont accessibles uniquement pour le compte du propriétaire du stockage. Authentifier les demandes sur le stockage des objets Blob de requiert la clé d’accès de compte par défaut. Toutefois, vous souhaiterez effectuer certaines données blob disponible à d’autres utilisateurs. Vous disposez de deux options :

- **L’accès anonyme :** Vous pouvez rendre un conteneur ou ses objets BLOB publiquement disponibles pour l’accès anonyme. Pour plus d’informations, reportez-vous à la section [accès anonyme en lecture de gestion pour les conteneurs et les objets BLOB](storage-manage-access-to-resources.md) .
- **Shared access signatures :** Vous pouvez fournir des clients avec une signature d’accès partagé (SAS), qui fournit le délégué l’accès à une ressource dans votre compte de stockage, avec les autorisations que vous spécifiez et sur un intervalle que vous spécifiez. Pour plus d’informations, consultez [L’aide partagés accès Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) .

### <a name="encrypting-blob-data"></a>Le cryptage des données blob

Stockage Azure prend en charge le cryptage des données blob à la fois sur le client et sur le serveur :

- **Cryptage côté client :** La bibliothèque cliente de stockage pour .NET prend en charge le chiffrement des données dans les applications clientes avant le téléchargement vers le stockage Azure et le décryptage des données pendant leur téléchargement vers le client. La bibliothèque prend également en charge l’intégration avec Azure clé coffre-fort stockage compte gestion des clés. Pour plus d’informations, consultez [Chiffrement côté Client avec .NET pour Microsoft Azure Storage](storage-client-side-encryption.md) . Voir aussi [didacticiel : crypter et décrypter le BLOB dans le stockage Azure Microsoft Azure clé coffre-fort à l’aide de](storage-encrypt-decrypt-blobs-key-vault.md).
- **Cryptage côté serveur**: stockage Azure prend désormais en charge le cryptage côté serveur. Consultez [cryptage de Service de stockage Azure pour données au repos (aperçu)](storage-service-encryption.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base de stockage des objets Blob, suivre ces liens pour en savoir plus.

### <a name="microsoft-azure-storage-explorer"></a>Explorateur de stockage Microsoft Azure
- [Microsoft Azure Storage Explorer (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome gratuit de Microsoft qui vous permet d’utiliser visuellement avec des données de stockage Azure sur Windows, OS X et Linux.

### <a name="blob-storage-samples"></a>Exemples de stockage BLOB

- [Mise en route avec le stockage Blob Azure dans .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Référence du stockage BLOB

- [Bibliothèque Client de stockage de référence .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [Référence de l’API REST](http://msdn.microsoft.com/library/azure/dd179355)

### <a name="conceptual-guides"></a>Guides de concepts

- [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
- [Mise en route avec le stockage de fichiers pour .NET](storage-dotnet-how-to-use-files.md)
- [L’utilisation du stockage blob Azure avec le SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
