<properties
    pageTitle="Permet de convertir le contenu de texte dans des fichiers vidéo en texte numérique Azure Media Analytique | Microsoft Azure"
    description="Azure Media d’Analytique OCR (reconnaissance optique de caractères) vous permet de vous permet de convertir le contenu de texte dans des fichiers vidéo en texte modifiable numérique avec possibilité de recherche.  Cela vous permet d’automatiser l’extraction de métadonnées explicites de signal vidéo de votre support."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"   
    ms.author="juliako"/>
 
#<a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Azure Media Analytique permet de convertir le contenu de texte dans des fichiers vidéo en texte numérique 

##<a name="overview"></a>Vue d’ensemble

Si vous avez besoin extraire le contenu de texte à partir de vos fichiers vidéo et de générer un texte numérique modifiable, avec possibilité de recherche, vous devez utiliser Azure Media Analytique OCR (reconnaissance optique de caractères). Ce processeur de média Azure détecte le contenu de texte dans des fichiers vidéo et génère des fichiers de texte pour votre usage. Reconnaissance optique de caractères vous permet de vous permettent d’automatiser l’extraction de métadonnées explicites de signal vidéo de votre support.

Lorsqu’il est utilisé conjointement avec un moteur de recherche, vous pouvez facilement vos médias d’index en texte et améliorer la détectabilité du contenu. Cela est particulièrement utile dans la vidéo hautement textuel, tel qu’un enregistrement vidéo ou de la capture d’écran d’un diaporama. Le processeur de média Azure OCR est optimisé pour texte numérique.

Le processeur de média **Azure Media OCR** est en cours d’aperçu.

Cette rubrique donne des détails sur **Azure Media OCR** et montre comment l’utiliser avec Media Services SDK pour .NET. Pour plus d’informations et d’exemples, consultez [le blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

##<a name="ocr-input-files"></a>Fichiers d’entrée de reconnaissance optique de caractères

Fichiers vidéo. Actuellement, les formats suivants sont pris en charge : WMV, MOV et MP4.

##<a name="task-configuration"></a>Configuration de la tâche 

Configuration d’une tâche (préconfiguration). Lorsque vous créez une tâche avec la **Reconnaissance optique de caractères Azure Media**, vous devez spécifier une configuration prédéfinie à l’aide de JSON ou XML. 

###<a name="attribute-descriptions"></a>Descriptions d’attribut

Nom de l’attribut|Description
---|---
Langue|(facultatif) décrit la langue du texte à rechercher. Une des valeurs suivantes : détection automatique (par défaut), arabe, ChineseSimplified, sait, tchèque danois, néerlandais, anglais, finnois, Français, allemand, grec, hongrois, italien, japonais, coréen, norvégien, polonais, portugais, roumain, russe, SerbianCyrillic, SerbianLatin, slovaque, espagnol, suédois, turc.
TextOrientation|(facultatif) décrit l’orientation du texte à rechercher.  « Gauche » signifie que la partie supérieure de toutes les lettres sont pointe vers la gauche.  Texte par défaut (comme celui qui se trouve dans un livre) peut être appelé « Up » orienté.  Une des valeurs suivantes : détection automatique (par défaut), haut, droite, bas, gauche.
TimeInterval|(facultatif) décrit le taux d’échantillonnage.  Valeur par défaut est toutes les 1/2 seconde.<br/>Format JSON : hh : mm :. SSS (00:00:00.500 par défaut)<br/>Format XML – primitive de durée de W3C XSD (par défaut, PT0.5)
DetectRegions|(facultatif) Tableau d’objets DetectRegion spécifiant des zones au sein de l’image vidéo dans laquelle détecter le texte.<br/>Un objet DetectRegion est constitué de valeurs quatre entières suivantes :<br/>Gauche : les pixels de la marge de gauche<br/>Haut – les pixels de la marge du haut<br/>Largeur : largeur de la zone de pixels<br/>Height : hauteur de la zone de pixels

####<a name="json-preset-example"></a>Exemple de JSON prédéfini
        
    {
        'Version':'1.0', 
        'Options': 
        {
        'Language':'English', 
            'TimeInterval':'00:00:01.5',
            'DetectRegions': 
             [
                {'Left':'1','Top':'1','Width':'1','Height':'1'},
                {'Left':'2','Top':'2','Width':'2','Height':'2'}
             ],
            'TextOrientation':'Up'
        }
    }

####<a name="xml-preset-example"></a>Exemple XML prédéfini

    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>1</Left>
                   <Top>1</Top>
                   <Width>1</Width>
                   <Height>1</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>

##<a name="ocr-output-files"></a>Fichiers de sortie de reconnaissance optique de caractères

La sortie du processeur média OCR est un fichier JSON.

###<a name="elements-of-the-output-json-file"></a>Éléments du fichier de sortie JSON

La sortie vidéo de reconnaissance optique de caractères fournit segmentés à la fois les données sur les caractères présents dans votre vidéo.  Vous pouvez utiliser des attributs tels que la langue ou l’orientation à e dans exactement les mots qui vous intéressez à l’analyse. 

La sortie contient les attributs suivants :

Élément|Description
---|---
Échelle de temps|« fait tic-tac » par seconde de la vidéo
Décalage|heure de décalage pour les estampilles. Dans la version 1.0 de l’API de la vidéo, ce sera toujours 0.
Fréquence d’images|Images par seconde de la vidéo
Largeur|largeur de la vidéo en pixels
hauteur|hauteur de la vidéo en pixels
Fragments|tableau de segments de basé sur le temps de la vidéo dans laquelle les métadonnées sont mémorisé en bloc
début|démarrage d’un fragment dans « fait tic-tac »
durée|longueur d’un fragment dans « fait tic-tac »
intervalle|intervalle de chaque événement dans le fragment donné
événements|tableau contenant des régions
région|objet représentant détecté des mots ou des expressions
langue|langue du texte détecté dans une région
orientation|orientation du texte détectée dans une région
lignes|tableau de lignes de texte détectées dans une région
texte|le texte réel

###<a name="json-output-example"></a>Exemple de sortie JSON

L’exemple de sortie suivant contient les informations générales relatives à la vidéo et plusieurs fragments de vidéo. Dans chaque fragment de vidéo, il contient toutes les régions qui sont détectée par reconnaissance optique de caractères MP avec le langage et l’orientation du texte. La région contient également toutes les lignes word dans cette zone de texte de la ligne, la position de la ligne et chaque mot d’informations (contenu word, position et confiance) dans cette ligne. Voici un exemple et placer des commentaires en ligne.

    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }

## <a name="sample-code"></a>Exemples de code

Suivantes du programme montre comment :

1. Créer un actif et de télécharger un fichier multimédia dans des immobilisations.
1. Crée un projet avec un fichier de configuration/programmé de reconnaissance optique de caractères.
1. Télécharge les fichiers de sortie de JSON. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace OCR
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
        
                    // Run the OCR job.
                    var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                                @"C:\supportFiles\OCR\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
                }
        
                static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My OCR Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My OCR Job");
        
                    // Get a reference to Azure Media OCR.
                    string MediaProcessorName = "Azure Media OCR";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My OCR Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);
        
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

##<a name="related-links"></a>Liens connexes

[Vue d’ensemble de l’Analytique des Services multimédia pour Azure](media-services-analytics-overview.md)
