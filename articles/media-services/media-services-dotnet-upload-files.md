<properties 
    pageTitle="Télécharger des fichiers dans un compte de Services de support à l’aide de .NET | Microsoft Azure" 
    description="Découvrez comment obtenir du contenu multimédia dans les Services de support en créant et en chargeant les actifs." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="juliako"/>



# <a name="upload-files-into-a-media-services-account-using-net"></a>Télécharger des fichiers dans un compte de Services de support à l’aide de .NET

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [RESTE](media-services-rest-upload-files.md)
 - [Portail](media-services-portal-upload-files.md)

Dans les Services de support, vous charger (ou d’acquisition) vos fichiers numériques dans une immobilisation. L’entité **Asset** peut contenir, vidéo, audio, images, collections de miniatures, texte pistes et sous-titres fichiers (et les métadonnées relatives à ces fichiers.)  Une fois que les fichiers sont téléchargés, votre contenu est stocké en toute sécurité dans le nuage pour la poursuite du traitement et la diffusion en continu.

Les fichiers de l’actif sont appelés **Fichiers de composants**. L’instance de **AssetFile** et le fichier multimédia réelle sont deux objets distincts. L’instance de AssetFile contient des métadonnées sur le fichier multimédia, tandis que le fichier multimédia contient le contenu du média réel.

>[AZURE.NOTE]Les considérations suivantes s’appliquent lors du choix d’un nom de fichier de ressources :
>
>- Les Services de support utilise la valeur de la propriété IAssetFile.Name lors de la génération d’URL pour le contenu de transmission en continu (par exemple, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Pour cette raison, codage de pourcentage n’est pas autorisée. La valeur de la propriété **Name** ne peut comporter aucun des [caractères %-codage-réservés](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)suivants : !*'();:@&=+$,/?%#[]". En outre, il ne peut exister un '.' pour l’extension de nom de fichier.
>
>- La longueur du nom ne doit pas être supérieure à 260 caractères.

Lorsque vous créez des ressources, vous pouvez spécifier les options de cryptage suivantes. 

- **Aucun** - aucun chiffrement n’est utilisé. Il s’agit de la valeur par défaut. Notez que lors de l’utilisation de cette option votre contenu n’est pas protégé en transit ou au repos dans le stockage.
Si vous souhaitez fournir un MP4 utilisant le téléchargement progressif, utilisez cette option. 
- **CommonEncryption** - Utilisez cette option si vous téléchargez le contenu qui a été déjà chiffré et protégé par DRM PlayReady (par exemple, diffusion en continu lisse protégé avec PlayReady DRM) ou de cryptage commun.
- **EnvelopeEncrypted** – Utilisez cette option si vous téléchargez TLS cryptée avec AES. Notez que les fichiers doivent ont été codés et chiffrés par Gestionnaire de transformation.
- **StorageEncrypted** - chiffre votre contenu clair localement en utilisant le cryptage AES-256 bits, puis téléchargements pour stockage Azure où il est stocké crypté au repos. Les ressources protégées par un cryptage de stockage sont automatiquement et placés dans un système de fichiers cryptés avant le codage et non éventuellement cryptés avant de le télécharger comme une nouvelle immobilisation de sortie. Cas d’usage principal pour le cryptage du stockage est lorsque vous souhaitez sécuriser vos fichiers de média d’entrée de haute qualité avec le cryptage fort au repos sur le disque.

    Media Services assure le cryptage de stockage sur disque pour vos ressources, pas over-the-wire comme gestionnaire des droits numériques (DRM).

    Si votre actif est le stockage chiffré, vous devez configurer la stratégie de livraison d’actif. Pour plus d’informations, consultez [stratégie de livraison de configuration actif](media-services-dotnet-configure-asset-delivery-policy.md).

Si vous spécifiez pour votre actif à être chiffrés avec une option de **CommonEncrypted** , ou une option de **EnvelopeEncypted** , vous devez associer votre actif un **ContentKey**. Pour plus d’informations, consultez [comment créer un ContentKey](media-services-dotnet-create-contentkey.md). 

Si vous spécifiez pour votre actif à être chiffrés avec une option de **StorageEncrypted** , le Kit de développement de Services de support pour .NET créera une **StorateEncrypted** **ContentKey** pour vos ressources.


Cette rubrique indique comment utiliser Media Services SDK .NET ainsi que les extensions Media Services SDK .NET pour télécharger des fichiers en un atout de Media Services.

 
## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Télécharger un fichier unique avec Media Services .NET SDK 

L’exemple de code ci-dessous utilise le Kit de développement .NET pour effectuer les tâches suivantes : 

- Crée un élément vide.
- Crée une instance de AssetFile que nous souhaitons associer à l’actif.
- Crée une instance de AccessPolicy qui définit les autorisations et la durée d’accès à la ressource.
- Crée une instance de localisateur qui fournit l’accès à la ressource.
- Télécharge un seul fichier multimédia dans Media Services. 

        
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
        }

##<a name="upload-multiple-files-with-media-services-net-sdk"></a>Téléchargement de plusieurs fichiers avec Media Services .NET SDK 

Le code suivant montre comment créer un actif et de télécharger plusieurs fichiers.

Le code effectue les opérations suivantes :
    
-   Crée une ressource vide à l’aide de la méthode CreateEmptyAsset définie dans l’étape précédente.
    
-   Crée une instance de **AccessPolicy** qui définit les autorisations et la durée d’accès à la ressource.
    
-   Crée une instance de **localisateur** qui fournit l’accès à la ressource.
    
-   Crée une instance de **BlobTransferClient** . Ce type représente un client qui fonctionne sur les objets BLOB Azure. Dans cet exemple, nous utilisons le client pour surveiller la progression du téléchargement. 
    
-   Énumère les fichiers dans le répertoire spécifié et crée une instance de **AssetFile** pour chaque fichier.
    
-   Télécharge les fichiers dans les Services de support à l’aide de la méthode **UploadAsync** . 
    
>[AZURE.NOTE] Utilisez la méthode UploadAsync afin de garantir que les appels ne sont pas bloquée et que les fichiers sont téléchargés en parallèle.
    
    
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }
    
    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }



Lorsque vous téléchargez un grand nombre de ressources, considérez les points suivants.

- Créer un nouvel objet **CloudMediaContext** par thread. La classe **CloudMediaContext** n’est pas thread-safe.
 
- Augmenter les NumberOfConcurrentTransfers à partir de la valeur par défaut de 2 à une valeur supérieure à 5. La définition de cette propriété affecte toutes les instances de **CloudMediaContext**. 
 
- Conserver la valeur par défaut de 10 ParallelTransferThreadCount.
 
##<a id="ingest_in_bulk"></a>Traiter les actifs en vrac à l’aide de Media Services .NET SDK 

Téléchargement des fichiers de composants de grande taille, un goulot d’étranglement peut être lors de la création de l’immobilisation. Absorber actifs en bloc ou « Absorber en vrac », implique de découplage création d’immobilisations dans le processus de téléchargement. Pour utiliser un bloc d’absorber l’approche, créez un manifeste (IngestManifest) qui décrit l’actif et ses fichiers associés. Puis utilisez la méthode de téléchargement de votre choix pour télécharger les fichiers associés au conteneur du blob du manifeste. Microsoft Azure Media Services surveille le conteneur blob associé au manifeste. Une fois qu’un fichier est téléchargé vers le conteneur blob, Microsoft Azure Media Services a terminé la création de ressources en fonction de la configuration de l’immobilisation dans le manifeste (IngestManifestAsset).


Pour créer un nouvel appel de IngestManifest de la méthode Create exposée par la collection IngestManifests sur le CloudMediaContext. Cette méthode crée un nouveau IngestManifest avec le nom de manifeste que vous fournissez.

    IIngestManifest manifest = context.IngestManifests.Create(name);

Créer les ressources qui seront associés à l’IngestManifest en bloc. Configurer les options de chiffrement de votre choix sur l’actif en bloc cassettes.

    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

Un IngestManifestAsset associe un actif à un IngestManifest de bloc en bloc cassettes. Il associe également le AssetFiles qui constituent chaque élément d’actif. Pour créer un IngestManifestAsset, utilisez la méthode Create sur le contexte de serveur.

L’exemple suivant illustre l’ajout deux IngestManifestAssets nouvelles qui associent les deux actifs créés précédemment pour l’essentiel d’acquisition manifeste. Chaque IngestManifestAsset associe également un ensemble de fichiers qui seront téléchargés pour chaque actif au cours de l’ingestion de bloc.  

    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;
    
    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
    
Vous pouvez utiliser n’importe quelle application de client à grande vitesse capable de télécharger les fichiers de composants dans le conteneur de stockage blob URI fourni par la propriété **IIngestManifest.BlobStorageUriForUpload** de l’IngestManifest. Un service de téléchargement de notable à grande vitesse est [Aspera à la demande pour une Application Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). Vous pouvez également écrire du code pour charger les fichiers de ressources, comme indiqué dans l’exemple de code suivant.
    
    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
    
            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
    
            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);
    
            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });
    
        copytask.Start();
    }

Le code pour charger les fichiers de composants pour l’exemple utilisé dans cette rubrique est illustré dans l’exemple de code suivant.
    
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
    

Vous pouvez déterminer l’état d’avancement de la traiter en bloc pour tous les actifs associés à une **IngestManifest** en interrogeant la propriété statistiques de **IngestManifest**. Pour mettre à jour les informations de progression, vous devez utiliser un nouveau **CloudMediaContext** chaque fois que vous interrogez la propriété de statistiques.

L’exemple suivant illustre l’interrogation d’un IngestManifest par son **Id**.
    
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
    
          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
    
                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }
    
             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
    


##<a name="upload-files-using-net-sdk-extensions"></a>Télécharger des fichiers à l’aide des Extensions du Kit de développement .NET 

L’exemple ci-dessous montre comment charger un seul fichier à l’aide des Extensions du Kit de développement .NET. Dans ce cas la méthode **CreateFromFile** est utilisée, mais la version asynchrone est également disponible (**CreateFromFileAsync**). La méthode **CreateFromFile** permet de spécifier le nom de fichier, option de chiffrement et un rappel pour signaler la progression du téléchargement du fichier.


    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });
    
        Console.WriteLine("Asset {0} created.", inputAsset.Id);
    
        return inputAsset;
    }

L’exemple suivant appelle la fonction de UploadFile et spécifie le cryptage du stockage en tant que l’option de création d’immobilisations.  


    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-step"></a>Étape suivante

Maintenant que vous avez chargé une immobilisation à des Services multimédias, consultez la rubrique [comment obtenir un processeur média][] .

[Comment obtenir un processeur média]: media-services-get-media-processor.md
 
