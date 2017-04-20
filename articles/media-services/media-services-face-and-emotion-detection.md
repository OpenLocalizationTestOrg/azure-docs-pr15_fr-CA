<properties
    pageTitle="Détecter les émotions avec Azure Media Analytique et Face | Microsoft Azure"
    description="Cette rubrique montre comment détecter les faces et les émotions avec Azure Media Analytique."
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
    ms.author="milanga;juliako;"/>
 
#<a name="detect-face-and-emotion-with-azure-media-analytics"></a>Détecter les émotions avec Azure Media Analytique et Face

##<a name="overview"></a>Vue d’ensemble

Le processeur de média **Azure Media Face détecteur** (MP) vous permet de compter, effectuer le suivi des mouvements et évaluer la même réaction via des expressions du visage et la participation du public à. Ce service contient deux fonctionnalités : 

- **Détection de visage**

    La détection de visage détecte et suit les faces humains dans une vidéo. Plusieurs faces peuvent être détectés et par la suite être suivis qu’ils sont en déplacement, avec les métadonnées de l’heure et l’emplacement retournée dans un fichier JSON. Lors du suivi, il va tenter de donner un code cohérent sur la même face alors que la personne est déplacer sur l’écran, même s’ils sont empêchés ou laissez brièvement de l’image.

    >[AZURE.NOTE]Ce service n’effectue pas la reconnaissance faciale. Une personne qui quitte le cadre ou devient masquée pour trop long aura un nouvel ID lorsqu’ils renvoient.

- **Détection d’émotion**
    
    Détection d’émotion est un composant facultatif du processeur média détection Face qui renvoie les faces détectés, y compris le bonheur, tristesse, crainte, colère et plus analyse sur plusieurs attributs émotionnels. 

Le **Détecteur de Face Azure Media** MP est en cours d’aperçu.

Cette rubrique donne des détails sur **Azure Media Face détecteur** et montre comment l’utiliser avec Media Services SDK pour .NET.

##<a name="face-detector-input-files"></a>Fichiers d’entrée du détecteur de face

Fichiers vidéo. Actuellement, les formats suivants sont pris en charge : WMV, MOV et MP4.

##<a name="face-detector-output-files"></a>Les fichiers de sortie de détecteur de face

L’API de suivi et de détection de face fournit la détection de haute précision visage emplacement et de suivi permettant de détecter jusqu'à 64 faces humains dans une vidéo. Faces frontales fournissent les meilleurs résultats, lors des faces latérales et petites faces (inférieur ou égal à 24 x 24 pixels) peut ne pas être précis.

Les faces détectés et suivis sont renvoyés avec des coordonnées (gauche, haut, largeur et hauteur) indiquant l’emplacement des faces dans l’image en pixels, mais aussi un numéro d’identification face indiquant le suivi de cette personne. Numéros d’identification de face sont sujets à réinitialiser dans des circonstances, lorsque la face frontale est perdue ou qui se chevauchent dans le cadre, entraînant certaines personnes l’obtention assignées à plusieurs ID.

###<a id="output_elements"></a>Éléments du fichier de sortie JSON

Pour la détection de visage et opération de suivi, le résultat de sortie contient les métadonnées à partir des faces dans le fichier spécifié au format JSON.

La détection de visage et suivi JSON inclut les attributs suivants :

Élément|Description
---|---
Version|Il s’agit de la version de l’API de la vidéo.
Échelle de temps|« Fait tic-tac » par seconde de la vidéo.
Décalage|Il s’agit de l’offset de l’heure pour les estampilles. Dans la version 1.0 de l’API de la vidéo, ce sera toujours 0. À l’avenir les scénarios que pris en charge, cette valeur peut changer.
Fréquence d’images|Images par seconde de la vidéo.
Fragments|Les métadonnées sont mémorisé en bloc dans différents segments appelés fragments. Chaque fragment contient un début, la durée, nombre d’intervalles et événements.
Début|L’heure de début du premier événement dans 'ticks'.
Durée|La longueur du fragment, dans « battements ».
Intervalle|L’intervalle de chaque entrée d’événement dans le fragment, dans « battements ».
Événements|Chaque événement contient les faces détectés et gérés dans cette durée. Il s’agit d’un tableau d’un tableau d’événements. Le tableau extérieur représente un intervalle de temps. Le tableau interne se compose de 0 ou de plusieurs événements qui est arrivé à ce point dans le temps. Un [] de support vide signifie que pas de faces ont été détectés.
ID| L’ID de la face qui est suivie. Ce numéro peut modifier par inadvertance si une face devient non détectée. Un individu donné doit avoir le même ID tout au long de la vidéo globale, mais cela n’est pas garantie en raison de limitations dans l’algorithme de détection (occlusion, etc.).
X, Y|Le X en haut à gauche et les coordonnées Y de la face englobant dans une échelle normalisée de 0.0 à 1.0. <br/>-X et Y coordonnées sont relatives à l’affichage paysage toujours, donc si vous avez un portrait vidéo (ou envers, dans le cas des e/s), vous allez transposer les coordonnées en conséquence.
Largeur, hauteur|La largeur et la hauteur de la face englobant dans une échelle normalisée de 0.0 à 1.0.
facesDetected|Il se trouve à la fin des résultats JSON et résume le nombre de faces que l’algorithme est détectée au cours de la vidéo. Étant donné que les ID peuvent être réinitialisés par inadvertance si une face devient non détectée (par exemple, face est hors écran, recherche de rangement), ce nombre ne peut pas toujours égale le véritable nombre de faces dans la vidéo.

Détecteur d’image utilise des techniques de fragmentation (où les métadonnées peuvent être subdivisée en segments de temps et vous pouvez télécharger uniquement ce dont vous avez besoin) et de segmentation (où les événements sont décomposées dans le cas où ils obtiennent trop volumineux). Des calculs simples peuvent vous aider à transformer les données. Par exemple, si un événement a démarré à 6300 (graduations), avec une échelle de temps de 2997 (graduations/s) et la fréquence d’images de 29,97 (images par seconde), puis :

- Démarrer/échelle = secondes 2.1
- Secondes x (échelle de fréquence/temps) = 63 blocs

Voici un exemple simple d’extraire le JSON dans une par format de trame pour la détection de visage et suivi :
    
    var faceDetectionResultJsonString = operationResult.ProcessingResult;
    var faceDetecionTracking = 
         JsonConvert.DeserializeObject<FaceDetectionResult>(faceDetectionResultJsonString, settings);


##<a name="face-detection-input-and-output-example"></a>Face d’entrée de la détection et l’exemple de sortie

###<a name="input-video"></a>Vidéo d’entrée

[Entrée vidéo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configuration de la tâche (préconfiguration)

Lorsque vous créez une tâche avec **Détecteur de Face Azure Media**, vous devez spécifier une préconfiguration de la configuration. Le préréglage de configuration suivant est pour la détection de visage.

    {"version":"1.0"}

###<a name="json-output"></a>Sortie JSON

L’exemple suivant de sortie JSON a été tronqué.

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

##<a name="emotion-detection-input-and-output-example"></a>Détection d’émotion d’entrée et de sortie exemple


###<a name="input-video"></a>Vidéo d’entrée

[Entrée vidéo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configuration de la tâche (préconfiguration)

Lorsque vous créez une tâche avec **Détecteur de Face Azure Media**, vous devez spécifier une préconfiguration de la configuration. Le préréglage de configuration suivant permet de créer des JSON en fonction de la détection d’émotion.
    
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


####<a name="attribute-descriptions"></a>Descriptions d’attribut

Nom de l’attribut|Description
---|---
Mode|Faces : Face uniquement de détection <br/>AggregateEmotion : Les valeurs de retour émotion moyenne de toutes les faces dans l’image.
AggregateEmotionWindowMs|Utilisez AggregateEmotion mode sélectionné. Spécifie la longueur de la vidéo permet de produire chaque résultat d’agrégation, en millisecondes.
AggregateEmotionIntervalMs|Utilisez AggregateEmotion mode sélectionné. Spécifie avec quelle fréquence pour produire des résultats de regroupement.

####<a name="aggregate-defaults"></a>Paramètres par défaut du regroupement

Ci-dessous sont recommandées des valeurs pour les paramètres de fenêtre et l’intervalle de regroupement. AggregateEmotionWindowMs doit être supérieure à AggregateEmotionIntervalMs.

   |Valeurs par défaut (s)|Max(s)|Min(s)
---|---|---|---
AggregateEmotionWindowMs|0,5|2|0,25
AggregateEmotionIntervalMs|0,5|1|0,25

###<a name="json-output"></a>Sortie JSON

JSON de sortie pour l’agrégation émotion (tronquée) :
 
    
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,


##<a name="limitations"></a>Limitations

- Les formats vidéo d’entrée pris en charge incluent WMV, MOV et MP4.
- La plage de taille face détectable est 24 x 24 à 2048 x 2048 pixels. Les faces de cette plage ne seront pas détectés.
- Pour chaque vidéo, le nombre maximal de faces retourné est 64.
- Certaines faces peut ne pas être détectés en raison de difficultés techniques ; par exemple très grands angles de face (tête-pose) et occlusion de grande taille. Faces frontales et près de frontal ont les meilleurs résultats.


## <a name="sample-code"></a>Exemples de code

Suivantes du programme montre comment :

1. Créer un actif et de télécharger un fichier multimédia dans des immobilisations.
1. Crée une tâche avec une tâche de détection de visage basée sur un fichier de configuration qui contient la présélection json suivant. 
                    
        {
            "version": "1.0"
        }

1. Télécharge les fichiers de sortie de JSON. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceDetection
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
        
                    // Run the FaceDetection job.
                    var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\FaceDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
                }
        
                static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Detection Job");
        
                    // Get a reference to Azure Media Face Detector.
                    string MediaProcessorName = "Azure Media Face Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
        
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

[Démonstrations multimédias Analytique Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)