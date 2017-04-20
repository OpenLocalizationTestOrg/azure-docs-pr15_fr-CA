<properties
    pageTitle="L’indexation des fichiers multimédia avec Media Azure indexeur 2 Aperçu | Microsoft Azure"
    description="Azure Indexer de Media vous permet de rendre le contenu de vos fichiers multimédias avec possibilité de recherche et de générer une transcription du texte intégral pour les légendes et mots clés. Cette rubrique montre comment utiliser l’aperçu de Media indexeur 2."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016" 
    ms.author="adsolank;juliako;"/>


# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>L’indexation des fichiers multimédia avec Media Azure indexeur 2 Aperçu

##<a name="overview"></a>Vue d’ensemble

Le processeur de media **Azure Media indexeur 2 Preview** (MP) vous permet de vous rendre le contenu et des fichiers de support avec possibilité de recherche, ainsi que de générer des pistes de sous-titrage fermés. Par rapport à la version précédente de [Indexer de Media Azure](media-services-index-content.md), **Indexeur 2 Preview Azure Media** effectue une indexation plus rapides et offre la prise en charge plus large. Langues prises en charge incluent l’anglais, espagnol, Français, allemand, italien, chinois, portugaise et arabe.

L' **Indexeur 2 Preview Azure Media** MP est actuellement dans l’aperçu.

Cette rubrique indique comment créer des tâches d’indexation avec **Azure Media indexeur 2 Preview**.

>[AZURE.NOTE]Les considérations suivantes s’appliquent :
>
>Indexeur 2 n’est pas pris en charge dans la Chine d’Azure et d’Azure gouvernement.
>
>L’aperçu est limitée à ~ 10 minutes de traitement, mais il est libre de tous les clients.
>
>Lors de l’indexation de contenu, vérifiez que vous utilisez des fichiers multimédias qui ont très clair vocale (sans la musique de fond, bruit, effets ou souffle de microphone). Voici quelques exemples de contenu approprié : enregistré des réunions, conférences ou présentations. Le contenu suivant peut ne pas être adapté à l’indexation : films, émissions TV, quoi que ce soit avec audio mixte et des effets sonores, du contenu avec le bruit de fond (souffle) mal enregistrement.


Cette rubrique donne des détails sur **Azure Media indexeur 2 Preview** et montre comment l’utiliser avec Media Services SDK pour .NET

##<a name="input-and-output-files"></a>Fichiers d’entrée et de sortie

###<a name="input-files"></a>Fichiers d’entrée

Fichiers audio ou vidéo

###<a name="output-files"></a>Fichiers de sortie

Une tâche d’indexation peut générer des fichiers de sous-titres dans les formats suivants :  

- **Lapon**
- **TTML**
- **WebVTT**

Fermeture de légende (CC) et les fichiers dans ces formats peuvent être utilisés pour rendre des fichiers audio et vidéo accessibles aux personnes atteintes de handicap de l’audition.

##<a name="task-configuration-preset"></a>Configuration de la tâche (préconfiguration)

Lorsque vous créez une tâche d’indexation avec **Azure Media indexeur 2 Preview**, vous devez spécifier une préconfiguration de la configuration.

Le JSON suivant définit les paramètres disponibles.

    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }

##<a name="supported-languages"></a>Langues prises en charge  

Azure Media indexeur 2 Preview prend en charge la parole-texte pour les langues suivantes (lorsque vous spécifiez le nom du langage dans la configuration de la tâche, utiliser le code 4 caractères entre crochets comme indiqué ci-dessous) :

- Anglais [EnUs]
- Espagnol [EsEs]
- Chinois [ZhCn]
- Français [FrFr]
- Allemand [DeDe]
- Italien [ItIt]
- Portugais [PtBr]
- Arabe (égyptien) [ArEg]


## <a name="sample-code"></a>Exemples de code

Suivantes du programme montre comment :

1. Créer un actif et de télécharger un fichier multimédia dans des immobilisations.
1. Crée une tâche avec une tâche d’indexation basée sur un fichier de configuration qui contient la présélection json suivant.
            
        {
          "version":"1.0",
          "Features":
            [
               {
               "Options": {
                    "Formats":["WebVtt","ttml"],
                    "Language":"enUs",
                    "Type":"RecoOptions"
               },
               "Type":"SpReco"
            }]
        }

1. Télécharge les fichiers de sortie. 
    
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace IndexContent
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                static void Main(string[] args)
                {
        
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Run indexing job.
                    var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                                @"C:\supportFiles\Indexer\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
                }
        
                static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Indexing Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Indexing Job");
        
                    // Get a reference to Azure Media Indexer 2 Preview.
                    string MediaProcessorName = "Azure Media Indexer 2 Preview";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Indexing Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset to be indexed.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
        
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
        
                    // Launch the job.
                    job.Submit();
        
                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        
                    progressJobTask.Wait();
        
                    // If job state is Error, the event handling
                    // method for job progress should log errors.  Here we check
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                        Console.WriteLine(string.Format("Error: {0}. {1}",
                                                        error.Code,
                                                        error.Message));
                        return null;
                    }
        
                    return job.OutputMediaAssets[0];
                }
        
                static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
                {
                    IAsset asset = _context.Assets.Create(assetName, options);
        
                    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                    assetFile.Upload(filePath);
        
                    return asset;
                }
        
                static void DownloadAsset(IAsset asset, string outputDirectory)
                {
                    foreach (IAssetFile file in asset.AssetFiles)
                    {
                        file.Download(Path.Combine(outputDirectory, file.Name));
                    }
                }
        
                static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors
                        .Where(p => p.Name == mediaProcessorName)
                        .ToList()
                        .OrderBy(p => new Version(p.Version))
                        .LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor",
                                                                   mediaProcessorName));
        
                    return processor;
                }
        
                static private void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
        
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished.");
                            Console.WriteLine();
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
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
                    }
                }
            }
        }


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Liens connexes

[Vue d’ensemble de l’Analytique des Services multimédia pour Azure](media-services-analytics-overview.md)

[Démonstrations multimédias Analytique Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)