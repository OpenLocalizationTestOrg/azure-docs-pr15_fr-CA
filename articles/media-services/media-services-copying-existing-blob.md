<properties 
    pageTitle="Copie d’un Blob existant dans un support de Services actifs | Microsoft Azure" 
    description="Cette rubrique indique comment copier un blob existant dans une ressource de Services multimédia." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/13/2016" 
    ms.author="juliako"/>

#<a name="copying-an-existing-blob-into-a-media-services-asset"></a>Copie d’un Blob existant dans une ressource de Services multimédia

Cette rubrique indique comment copier des objets BLOB à partir d’un compte de stockage dans une nouvelle immobilisation de Microsoft Azure Media Services.

Vos objets BLOB peut exister dans un compte de stockage qui est associé avec Media Services compte ou compte de stockage qui n’est pas associé au compte de Services de support. Cette rubrique montre comment copier des objets BLOB à partir d’un compte de stockage dans une ressource de Services multimédia. Notez que vous pouvez également copier dans des centres de données. Toutefois, il peuvent être frais supportés par cette opération. Pour plus d’informations sur la tarification, voir [Les transferts de données](https://azure.microsoft.com/pricing/#header-11).

>[AZURE.NOTE] Vous ne devriez pas modifier le contenu des conteneurs blob qui ont été générés par les Services de support sans utiliser les API de Service de support.

##<a name="download-sample"></a>Télécharger l’exemple

Obtenir et exécuter un exemple à partir [d’ici](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/).

##<a name="prerequisites"></a>Conditions préalables

- Deux comptes de Services de support dans un abonnement Azure nouvel ou existant. Reportez-vous à la rubrique [comment créer un compte de Services de support](media-services-portal-create-account.md).
- Systèmes d’exploitation : Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
- .NET Framework 4.5.
- Visual Studio 2010 SP1 (Professionnel, Premium, Ultimate ou Express) ou une version ultérieure.

##<a name="set-up-your-project"></a>Configurer votre projet

Dans cette section vous créer et configurer un projet d’Application de Console C#.

1. Utilisez Visual Studio pour créer une nouvelle solution qui contient le projet d’Application de Console C#. 
2. Entrez CopyExistingBlobsIntoAsset pour le nom et puis cliquez sur OK.
1. Utilisez Nuget pour ajouter des références aux DLL liées de Media Services. Dans le Menu principal de Visual Studio, sélectionnez Outils -> Gestionnaire de package de bibliothèque -> Console de Gestionnaire de package. Dans le type de fenêtre de console Install-Package windowsazure.mediaservices appuyez sur ENTRÉE.
1. Ajouter d’autres références qui sont nécessaires pour ce projet : System.Configuration.
1. Remplacer à l’aide des instructions qui ont été ajoutées dans le fichier Programs.cs par défaut avec les options suivantes :
        
        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure;
        using System.Web;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Auth;

1. Ajoutez la section appSettings du fichier .config et mettre à jour les valeurs en fonction de votre clé de stockage et les Services de support et les valeurs de nom. 

        <appSettings>
          <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
          <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
          <add key="MediaServicesStorageAccountName" value="Media-Services-Storage-Account-Name"/>
          <add key="MediaServicesStorageAccountKey" value="Media-Services-Storage-Account-Key"/>
          <add key="ExternalStorageAccountName" value="External-Storage-Account-Name"/>
          <add key="ExternalStorageAccountKey" value="External-Storage-Account-Key"/>
        </appSettings>


##<a name="copy-blobs-from-a-storage-account-into-a-media-services-asset"></a>Copier des objets BLOB à partir d’un compte de stockage dans une ressource de Services multimédia

L’exemple de code ci-dessous effectue les tâches suivantes :

1. Crée l’instance CloudMediaContext. 
1. Crée des instances de CloudStorageAccount : _sourceStorageAccount et _destinationStorageAccount.
1. Télécharge les fichiers de diffusion en continu lisse à partir d’un répertoire local dans un conteneur d’objet blob qui se trouve dans _sourceStorageAccount. 
1. Crée une nouvelle immobilisation. Le conteneur blob qui est créé pour cet actif se trouve dans _destinationStorageAccount. 
1. Utilise le SDK de stockage Azure pour copier les objets BLOB spécifiés dans le conteneur associé au capital.

    >[AZURE.NOTE]L’opération de copie ne lève pas d’exception si la recherche a expiré.

1. Étant donné que, dans cet exemple, nous copions les fichiers de transmission en continu lisses, l’exemple montre comment définir le fichier .ism le fichier primaire. Si, par exemple, nous avons copié un fichier .mp4, le fichier mp4 est défini comme le fichier primaire.
1. Crée l’URL de diffusion en continu lisse pour le localisateur OnDemandOrigin associés lié la ressource. 
            
        class Program
        {
            // Read values from the App.config file. 
            static string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
            static string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            static string _storageAccountName = ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
            static string _storageAccountKey = ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
            static string _externalStorageAccountName = ConfigurationManager.AppSettings["ExternalStorageAccountName"];
            static string _externalStorageAccountKey = ConfigurationManager.AppSettings["ExternalStorageAccountKey"];

            private static MediaServicesCredentials _cachedCredentials = null;
            private static CloudMediaContext _context = null;

            private static CloudStorageAccount _sourceStorageAccount = null;
            private static CloudStorageAccount _destinationStorageAccount = null;

            static void Main(string[] args)
            {
            _cachedCredentials = new MediaServicesCredentials(
                    _accountName,
                    _accountKey);
            // Use the cached credentials to create CloudMediaContext.
            _context = new CloudMediaContext(_cachedCredentials);

            // In this example the storage account from which we copy blobs is not 
            // associated with the Media Services account into which we copy blobs.
            // But the same code will work for coping blobs from a storage account that is 
            // associated with the Media Services account.
            //
            // Get a reference to a storage account that is not associated with a Media Services account
            // (an external account).  
            StorageCredentials externalStorageCredentials =
                new StorageCredentials(_externalStorageAccountName, _externalStorageAccountKey);
            _sourceStorageAccount = new CloudStorageAccount(externalStorageCredentials, true);

            //Get a reference to the storage account that is associated with a Media Services account. 
            StorageCredentials mediaServicesStorageCredentials =
                new StorageCredentials(_storageAccountName, _storageAccountKey);
            _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);

            // Upload Smooth Streaming files into a storage account.
            string localMediaDir = @"C:\supportFiles\streamingfiles";
            CloudBlobContainer blobContainer =
                UploadContentToStorageAccount(localMediaDir);

            // Create a new asset and copy the smooth streaming files into 
            // the container that is associated with the asset.
            IAsset asset = CreateAssetFromExistingBlobs(blobContainer);

            // Get the streaming URL for the smooth streaming files 
            // that were copied into the asset.   
            string urlForClientStreaming = CreateStreamingLocator(asset);
            Console.WriteLine("Smooth Streaming URL: " + urlForClientStreaming);

            Console.ReadLine();
            }

            /// <summary>
            /// Uploads content from a local directory into the specified storage account.
            /// In this example the storage account is not associated with the Media Services account.
            /// </summary>
            /// <param name="localPath">The path from which to upload the files.</param>
            /// <returns>The container that contains the uploaded files.</returns>
            static public CloudBlobContainer UploadContentToStorageAccount(string localPath)
            {
            CloudBlobClient externalCloudBlobClient = _sourceStorageAccount.CreateCloudBlobClient();

            CloudBlobContainer externalMediaBlobContainer = externalCloudBlobClient.GetContainerReference("streamingfiles");

            externalMediaBlobContainer.CreateIfNotExists();

            // Upload files to the blob container.  
            DirectoryInfo uploadDirectory = new DirectoryInfo(localPath);
            foreach (var file in uploadDirectory.EnumerateFiles())
            {
                CloudBlockBlob blob = externalMediaBlobContainer.GetBlockBlobReference(file.Name);

                blob.UploadFromFile(file.FullName, FileMode.Open);
            }

            return externalMediaBlobContainer;
            }

            /// <summary>
            /// Creates a new asset and copies blobs from the specifed storage account.
            /// </summary>
            /// <param name="mediaBlobContainer">The specified blob container.</param>
            /// <returns>The new asset.</returns>
            static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer mediaBlobContainer)
            {
            // Create a new asset. 
            IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);

            IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                TimeSpan.FromHours(24), AccessPermissions.Write);
            ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);

            CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

            // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
            string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];

            CloudBlobContainer assetContainer =
                destBlobStorage.GetContainerReference(destinationContainerName);

            if (assetContainer.CreateIfNotExists())
            {
                assetContainer.SetPermissions(new BlobContainerPermissions
                {
                PublicAccess = BlobContainerPublicAccessType.Blob
                });
            }

            var blobList = mediaBlobContainer.ListBlobs();
            foreach (var sourceBlob in blobList)
            {
                var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
                CopyBlob(sourceBlob as ICloudBlob, assetContainer);
                assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                assetFile.Update();
            }

            asset.Update();

            destinationLocator.Delete();
            writePolicy.Delete();

            // Since we copied a set of Smooth Streaming files, 
            // set the .ism file to be the primary file. 
            // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
            SetISMFileAsPrimary(asset);

            return asset;
            }

            /// <summary>
            /// Creates the OnDemandOrigin locator in order to get the streaming URL.
            /// </summary>
            /// <param name="asset">The asset that contains the smooth streaming files.</param>
            /// <returns>The streaming URL.</returns>
            static public string CreateStreamingLocator(IAsset asset)
            {
            var ismAssetFile = asset.AssetFiles.ToList().
                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).First();

            // Create a 30-day readonly access policy. 
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                TimeSpan.FromDays(30),
                AccessPermissions.Read);

            // Create a locator to the streaming content on an origin. 
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

            return originLocator.Path + ismAssetFile.Name + "/manifest";
            }

            /// <summary>
            /// Copies the specified blob into the specified container.
            /// </summary>
            /// <param name="sourceBlob">The source container.</param>
            /// <param name="destinationContainer">The destination container.</param>
            static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
            {
            var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
            {
                Permissions = SharedAccessBlobPermissions.Read,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
            });

            ICloudBlob destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);

            if (destinationBlob.Exists())
            {
                Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
            }
            else
            {

                // Display the size of the source blob.
                Console.WriteLine(sourceBlob.Properties.Length);

                Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
                destinationBlob.StartCopyFromBlob(new Uri(sourceBlob.Uri.AbsoluteUri + signature));

                while (true)
                {
                // The StartCopyFromBlob is an async operation, 
                // so we want to check if the copy operation is completed before proceeding. 
                // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
                destinationBlob.FetchAttributes();
                if (destinationBlob.CopyState.Status != CopyStatus.Pending)
                {
                    break;
                }
                //It's still not completed. So wait for some time.
                System.Threading.Thread.Sleep(1000);
                }


                // Display the size of the destination blob.
                Console.WriteLine(destinationBlob.Properties.Length);

            }
            }

            /// <summary>
            /// Sets a file with the .ism extension as a primary file.
            /// </summary>
            /// <param name="asset">The asset that contains the smooth streaming files.</param>
            static private void SetISMFileAsPrimary(IAsset asset)
            {

            //If you expect the asset to contain the .ism asset file, set the .ism file as the primary file.
            var ismAssetFiles = asset.AssetFiles.ToList().
                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();

            // The following code assigns the first .ism file as the primary file in the asset.
            // An asset should have one .ism file.  
            ismAssetFiles.First().IsPrimary = true;
            ismAssetFiles.First().Update();
            }
        }

 

##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
