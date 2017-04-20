<properties 
    pageTitle="Coder une immobilisation avec Media Encoder Standard à l’aide de .NET | Microsoft Azure" 
    description="Cette rubrique montre comment utiliser .NET à l’encodage d’un actif à l’aide de Media Encoder Strandard." 
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
    ms.author="juliako;anilmur"/>


# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Coder une immobilisation avec Media Encoder Standard à l’aide de .NET

Tâches d’encodage sont une des opérations de traitement plus courantes dans les Services de support. Vous créez les tâches d’encodage pour convertir des fichiers multimédias à partir d’un encodage à un autre. Lorsque vous codez, vous pouvez utiliser le codeur de Media Services Media intégré. Vous pouvez également utiliser un encodeur fourni par un partenaire de Services de support ; codeurs de tiers sont disponibles via l’Azure Marketplace. 

Cette rubrique montre comment utiliser .NET pour coder vos ressources avec Media Encoder Standard (MES). Media Encoder Standard est configuré à l’aide d’un de l’encodeur prédéfinis décrits [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Il est recommandé de toujours coder vos fichiers mezzanine à une vitesse de transmission adaptive MP4 la valeur et le format de votre choix à l’aide de l' [Emballage dynamique](media-services-dynamic-packaging-overview.md)puis convertir le jeu. Pour tirer parti de l’emballage dynamique, vous devez d’abord obtenir au moins une unité de transmission en continu à la demande pour le point de terminaison en continu à partir de laquelle vous envisagez de livraison votre contenu. Pour plus d’informations, consultez [comment les Services de support d’échelle](media-services-portal-manage-streaming-endpoints.md).

Si votre capital de sortie est stockage crypté, vous devez configurer la stratégie de livraison d’actif. Pour plus d’informations, consultez [stratégie de livraison de configuration actif](media-services-dotnet-configure-asset-delivery-policy.md).

>[AZURE.NOTE]MES génère un fichier de sortie dont le nom contient les 32 premiers caractères du nom du fichier d’entrée. Le nom est basé sur ce qui est spécifié dans le fichier de présélection. Par exemple, « FileName » : « _ {Basename} {Index} {Extension} ». {Basename} est remplacé par les 32 premiers caractères du nom du fichier d’entrée.

###<a name="mes-formats"></a>Formats d’économie de marché

[Les codecs et les formats](media-services-media-encoder-standard-formats.md)

###<a name="mes-presets"></a>MES préconfigurations

Media Encoder Standard est configuré à l’aide d’un de l’encodeur prédéfinis décrits [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Métadonnées d’entrée et de sortie

Lorsque vous codez un actif d’entrée (ou actifs) à l’aide d’économie de marché, vous obtenez un actif de sortie à la réussite coder les achèvement de cette tâche. L’élément de sortie comprend de vidéo, audio, miniatures, manifeste, etc. en fonction de la présélection de codage que vous utilisez.

L’actif de sortie contient également un fichier contenant des métadonnées sur l’élément d’entrée. Le nom du fichier de métadonnées XML possède le format suivant : < asset_id >, _metadata.xml (par exemple, 41114ad3-eb5e - 4c-57 8d 92-5354e2b7d4a4_metadata.xml), où < asset_id > est la valeur AssetId de l’actif d’entrée. Le schéma de cette entrée XML de métadonnées est décrit [ici](http://msdn.microsoft.com/library/azure/dn783120.aspx).

L’actif de sortie contient également un fichier contenant des métadonnées sur l’élément de sortie. Le nom du fichier de métadonnées XML possède le format suivant : < source_file_name > _manifest.xml (par exemple, BigBuckBunny_manifest.xml). Le schéma de ces métadonnées de sortie XML est décrite [ici](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Si vous souhaitez examiner une ou l’autre des deux fichiers de métadonnées, vous pouvez créer un localisateur d’associations de sécurité et télécharger le fichier sur votre ordinateur local. Vous pouvez trouver un exemple comment créer un localisateur SAS et télécharger un fichier à l’aide des Extensions Media Services .NET SDK.

##<a name="download-sample"></a>Télécharger l’exemple

Obtenir et exécuter un exemple à partir [d’ici](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##<a name="example"></a>Exemple

L’exemple de code suivant utilise Media Services .NET SDK pour effectuer les tâches suivantes :

- Créer une tâche de codage.
- Obtenir une référence à l’encodeur Media Encoder Standard.
- Spécifier qu’il faut utiliser le « H264 débit plusieurs 720p » prédéfini. Vous pouvez voir tous les paramètres prédéfinis [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). Vous pouvez également examiner le schéma auquel ces paramètres prédéfinis doivent satisfaire rubrique [ici](https://msdn.microsoft.com/library/mt269962.aspx) .
- Ajouter une tâche de codage unique pour la tâche. 
- Permet de spécifier la ressource d’entrée à coder.
- Créer un actif de sortie qui contiendra la ressource codée.
- Ajoutez un gestionnaire d’événements pour vérifier la progression de la tâche.
- Soumettre la tâche.
        
        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        

            // Create a task with the encoding details, using a string preset.
            // In this case "H264 Multiple Bitrate 720p" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Multiple Bitrate 720p",
                TaskOptions.None);
        
            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);
        
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
            return job.OutputMediaAssets[0];
        }
        
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
        
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
        
                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }
        
        
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
            return processor;
        }


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi 

[Comment générer miniature à l’aide de Media Encoder Standard avec .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Vue d’ensemble de Media Services de codage](media-services-encode-asset.md)
