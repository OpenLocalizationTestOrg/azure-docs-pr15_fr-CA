<properties
    pageTitle="Mise en route de blob de Visual Studio et de stockage connecté services (services en nuage) | Microsoft Azure"
    description="La mise en route à l’aide du stockage des objets Blob Azure dans un projet de service cloud dans Visual Studio après la connexion à un compte de stockage à l’aide de Visual Studio de services connectés"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Mise en route de Visual Studio et de stockage des objets Blob Azure connecté services (services en nuage de projets)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment se familiariser avec le stockage Blob Azure après avoir créé ou référencé d’un compte de stockage Azure à l’aide de la boîte de dialogue de Visual Studio, **Ajouter des Services connectés** dans un projet de services cloud de Visual Studio. Nous allons vous montrer comment accéder à et de créer des conteneurs de blob et comment effectuer des tâches courantes telles que le téléchargement, mise en vente et le téléchargement des objets BLOB. Les exemples sont écrits en C\# et utilisation de la [Bibliothèque de Client Microsoft Azure stockage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Stockage de Blob Azure est un service permettant de stocker de grandes quantités de données non structurées qui sont accessibles à partir de n’importe où dans le monde via HTTP ou HTTPS. Un objet blob unique peut être n’importe quelle taille. BLOB peut être des éléments tels que des images, des fichiers audio et vidéo, des données brutes et des fichiers de document.

Tout comme les fichiers live dans des dossiers, des blobs de stockage live dans des conteneurs. Après avoir créé un stockage, vous créez un ou plusieurs conteneurs dans le stockage. Par exemple, dans un stockage appelée « Album », vous pouvez créer des conteneurs dans le stockage appelé « images » pour stocker des images et une autre appelée « audio » pour stocker les fichiers audio. Après avoir créé les conteneurs, vous pouvez télécharger les fichiers blob individuels pour les.

- Pour plus d’informations sur la manipulation par programmation des objets BLOB, reportez-vous à la section [mise en route de stockage Azure Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md).
- Pour obtenir des informations générales sur le stockage Azure, consultez la [documentation du stockage](https://azure.microsoft.com/documentation/services/storage/).
- Pour obtenir des informations générales sur les Services en nuage Azure, consultez la [documentation de Services en nuage](https://azure.microsoft.com/documentation/services/cloud-services/).
- Pour plus d’informations sur la programmation d’applications d’ASP.NET, consultez [ASP.NET](http://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Conteneurs de blob d’accès dans le code

Pour accéder par programme aux objets BLOB dans les projets de service cloud, vous devez ajouter les éléments suivants, si elles ne sont pas déjà présentes.

1. Ajoutez les déclarations d’espace de noms de code suivantes en haut de n’importe quel fichier C# dans lequel vous souhaitez accéder par programme à stockage Azure.

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Récupérez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d’obtenir le votre chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. Récupérez un objet **CloudBlobClient** pour faire référence à un conteneur existant dans votre compte de stockage.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

4. Récupérez un objet **CloudBlobContainer** pour faire référence à un conteneur spécifique blob.

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] Utilisez le code indiqué dans la procédure précédente devant le code indiqué dans les sections suivantes.

## <a name="create-a-container-in-code"></a>Créer un conteneur dans le code

> [AZURE.NOTE] Certaines API qui effectuent des appels en stockage Azure dans ASP.NET est asynchrones. Pour plus d’informations, consultez [la programmation asynchrone avec Async et Await](http://msdn.microsoft.com/library/hh191443.aspx) . Le code dans l’exemple suivant suppose que vous utilisez des méthodes de programmation asynchrone.

Pour créer un conteneur dans votre compte de stockage, tout ce que vous devez faire est ajouter un appel à **CreateIfNotExistsAsync** comme dans le code suivant :

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


Pour mettre les fichiers dans le conteneur à la disposition de tout le monde, vous pouvez définir le conteneur comme public en utilisant le code suivant.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Toute personne sur Internet peut voir les objets BLOB dans un conteneur public, mais vous pouvez les modifier ou les supprimer que si vous disposez de la clé d’accès approprié.

## <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Stockage Azure prend en charge les objets BLOB de page et les objets BLOB de bloc. Dans la plupart des cas, blob de bloc est le type recommandé à utiliser.

Pour télécharger un fichier à un objet blob de bloc, obtenir une référence de conteneur et de l’utiliser pour obtenir une référence d’objet blob de bloc. Une fois que vous avez une référence d’objet blob, vous pouvez télécharger n’importe quel flux de données lui en appelant la méthode **UploadFromStream** . Cette opération crée le blob s’il n’existait pas encore, ou s’il remplace si elle existe. L’exemple suivant montre comment charger un objet blob dans un conteneur et suppose que le conteneur a déjà été créé.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Liste les objets BLOB dans un conteneur

Pour répertorier les objets BLOB dans un conteneur, commencez par obtenir une référence de conteneur. Vous pouvez ensuite utiliser la méthode de **ListBlobs** du conteneur pour récupérer les objets BLOB et/ou les répertoires qu’il contient. Pour accéder à l’ensemble des propriétés et des méthodes pour un retour de **IListBlobItem**, vous devez effectuer un cast d’un objet **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** . Si le type est inconnu, vous pouvez utiliser un contrôle de type afin de déterminer lequel effectuer un cast en. Le code suivant montre comment récupérer et l’URI de chaque élément dans le conteneur de **photos** de sortie :

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

Comme indiqué dans l’exemple de code précédent, le service d’objet blob comprend le concept de répertoires à l’intérieur des conteneurs, ainsi. Il s’agit donc que vous pouvez organiser vos objets BLOB dans une structure de dossiers plus semblable. Par exemple, considérez l’ensemble des objets BLOB de bloc dans un conteneur nommé **photos**suivantes :

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Lorsque vous appelez **ListBlobs** sur le conteneur (comme dans l’exemple précédent), la collection retournée contient des objets **CloudBlobDirectory** et **CloudBlockBlob** , qui représente les répertoires et les objets BLOB contenues au niveau supérieur. Voici le résultat :

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Si vous le souhaitez, vous pouvez définir le paramètre **UseFlatBlobListing** de la méthode **ListBlobs** à **true**. Ainsi, chaque objet blob retourné sous la forme d’un **CloudBlockBlob**, quel que soit le répertoire. Voici l’appel à **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

et Voici les résultats :

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Pour plus d’informations, consultez [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Télécharger des objets BLOB

Pour télécharger les objets BLOB, tout d’abord extraire une référence d’objet blob et appelez ensuite la méthode **DownloadToStream** . L’exemple suivant utilise la méthode **DownloadToStream** pour transférer le contenu de l’objet blob dans un objet stream qui vous pouvez conserver ensuite dans un fichier local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Vous pouvez également utiliser la méthode **DownloadToStream** pour télécharger le contenu d’un objet blob sous la forme d’une chaîne de texte.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Supprimer des objets BLOB

Pour supprimer un objet blob, obtenez d’abord une référence d’objet blob, puis appelez la méthode **Delete** .

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Liste des objets BLOB dans les pages en mode asynchrone

Si vous mettez en vente un grand nombre d’objets BLOB, ou si vous voulez contrôler le nombre de résultats que vous renvoyez en une seule opération de liste, vous pouvez répertorier les objets BLOB dans les pages de résultats. Cet exemple montre comment renvoyer des résultats dans les pages de manière asynchrone, afin que l’exécution n’est pas bloquée en attendant de retourner un grand jeu de résultats.

Cet exemple montre un blob plat mise en vente, mais vous pouvez également effectuer une liste hiérarchique, en définissant le paramètre **useFlatBlobListing** de la méthode **ListBlobsSegmentedAsync** sur **false**.

Étant donné que l’exemple de méthode appelle une méthode asynchrone, il doit commencer avec le mot clé **async** , et elle doit retourner un objet **Task** . Le mot clé await spécifié pour la méthode **ListBlobsSegmentedAsync** suspend l’exécution de l’exemple de méthode jusqu'à la fin de la tâche de la liste.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
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

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
