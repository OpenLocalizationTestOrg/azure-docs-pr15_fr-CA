<properties
    pageTitle="Mise en route de blob de Visual Studio et de stockage connecté services (ASP.NET 5) | Microsoft Azure"
    description="La mise en route à l’aide de stockage des objets Blob Azure dans un projet Visual Studio 5 d’ASP.NET une fois que vous avez créé un compte de stockage à l’aide de Visual Studio de services connectés"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-5"></a>Mise en route avec les Blob Azure Visual Studio et stockage connecté services (5 ASP.NET)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

##<a name="overview"></a>Vue d’ensemble

Cet article décrit comment démarrer à l’aide de stockage des objets Blob Azure dans Visual Studio après avoir créé ou référencé d’un compte de stockage Azure dans un projet ASP.NET 5 à l’aide de la boîte de dialogue Visual Studio ajouter Services connectés.

Stockage de Blob Azure est un service permettant de stocker de grandes quantités de données non structurées qui sont accessibles à partir de n’importe où dans le monde via HTTP ou HTTPS. Un objet blob unique peut être n’importe quelle taille. BLOB peut être des éléments tels que des images, des fichiers audio et vidéo, des données brutes et des fichiers de document. Cet article décrit comment se familiariser avec le stockage blob, après avoir créé un compte de stockage Azure à l’aide de la boîte de dialogue de Visual Studio, **Ajouter des Services connectés** dans un projet ASP.NET 5.

Tout comme les fichiers live dans des dossiers, des blobs de stockage live dans des conteneurs. Après avoir créé un stockage, vous créez un ou plusieurs conteneurs dans le stockage. Par exemple, dans un stockage appelée « Album », vous pouvez créer des conteneurs dans le stockage appelé « images » pour stocker des images et une autre appelée « audio » pour stocker les fichiers audio. Après avoir créé les conteneurs, vous pouvez télécharger les fichiers blob individuels pour les. Pour plus d’informations sur la manipulation par programmation des objets BLOB, reportez-vous à la section [mise en route de stockage Azure Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md) .

##<a name="access-blob-containers-in-code"></a>Conteneurs de blob d’accès dans le code

Pour accéder par programme aux objets BLOB dans les projets ASP.NET 5, vous devez ajouter les éléments suivants, si elles ne sont pas déjà présentes.

1. Ajoutez les déclarations d’espace de noms de code suivantes en haut de n’importe quel fichier C# dans lequel vous souhaitez accéder par programme à stockage Azure.

        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;

2. Récupérez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d’obtenir le votre chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **Remarque :** Utiliser le code ci-dessus gauche du code dans les sections suivantes.


3. Utilisez un objet **CloudBlobClient** pour obtenir une référence de **CloudBlobContainer** à un conteneur existant dans votre compte de stockage.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##<a name="create-a-container-in-code"></a>Créer un conteneur dans le code

Vous pouvez également utiliser le **CloudBlobClient** pour créer un conteneur dans votre compte de stockage. Tout ce que vous devez faire est d’ajouter un appel à **CreateIfNotExistsAsync** comme dans le code suivant :

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**Remarque :** Les API qui effectuent des appels vers le stockage Azure dans ASP.NET 5 sont asynchrones. Pour plus d’informations, consultez [la programmation asynchrone avec Async et Await](http://msdn.microsoft.com/library/hh191443.aspx) . Le code ci-dessous suppose que les méthodes de programmation asynchrone sont utilisés.

Pour mettre les fichiers dans le conteneur à la disposition de tout le monde, vous pouvez définir le conteneur comme public en utilisant le code suivant.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##<a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Pour télécharger un fichier blob dans un conteneur, obtenir une référence de conteneur et de l’utiliser pour obtenir une référence d’objet blob. Une fois que vous avez une référence d’objet blob, vous pouvez télécharger n’importe quel flux de données lui en appelant la méthode **UploadFromStreamAsync** . Cette opération crée le blob si ce n’est déjà fait, ou le remplace si elle n’existe pas. L’exemple suivant montre comment charger un objet blob dans un conteneur et suppose que le conteneur a déjà été créé.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##<a name="list-the-blobs-in-a-container"></a>Liste les objets BLOB dans un conteneur
Pour répertorier les objets BLOB dans un conteneur, commencez par obtenir une référence de conteneur. Vous pouvez ensuite appeler la méthode de **ListBlobsSegmentedAsync** du conteneur pour récupérer le BLOB et/ou les répertoires qu’il contient. Pour accéder à l’ensemble des propriétés et des méthodes pour un retour de **IListBlobItem**, vous devez effectuer un cast d’un objet **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** . Si vous ne connaissez pas le type de blob, vous pouvez utiliser un contrôle de type afin de déterminer lequel effectuer un cast en. Le code suivant montre comment récupérer et l’URI de chaque élément dans un conteneur de sortie.

    BlobContinuationToken token = null;
    do
    {
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
        token = resultSegment.ContinuationToken;

        foreach (IListBlobItem item in resultSegment.Results)
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
    } while (token != null);

Il existe d’autres moyens pour répertorier le contenu d’un conteneur d’objet blob. Pour plus d’informations, consultez [mise en route de stockage Azure Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) .

##<a name="download-a-blob"></a>Télécharger un blob
Pour télécharger un blob, tout d’abord obtenir une référence à l’objet blob et appelez ensuite la méthode **DownloadToStreamAsync** . L’exemple suivant utilise la méthode **DownloadToStreamAsync** pour transférer le contenu de l’objet blob dans un objet stream que vous pouvez ensuite enregistrer comme un fichier local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

Il existe autres façons d’enregistrer des objets BLOB en tant que fichiers. Pour plus d’informations, consultez [mise en route de stockage Azure Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md#download-blobs) .

##<a name="delete-a-blob"></a>Supprimer un objet blob
Pour supprimer un objet blob, tout d’abord obtenir une référence à l’objet blob, puis appelez la méthode **DeleteAsync** sur elle.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
