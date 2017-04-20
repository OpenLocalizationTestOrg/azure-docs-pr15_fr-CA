<properties
    pageTitle="Détecter les mouvements avec Azure Media Analytique | Microsoft Azure"
    description="Le processeur de média du détecteur de mouvement Azure Media (MP) vous permet d’identifier efficacement les sections d’intérêt dans une vidéo sinon long et se déroule normalement."
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
    ms.date="10/10/2016"  
    ms.author="milanga;juliako;"/>
 
# <a name="detect-motions-with-azure-media-analytics"></a>Détecter les mouvements avec Azure Media Analytique

##<a name="overview"></a>Vue d’ensemble

Le processeur de média du **Détecteur de mouvement Azure Media** (MP) vous permet d’identifier efficacement les sections d’intérêt dans une vidéo sinon long et se déroule normalement. Détection de mouvement peut servir de métrage de caméra statique pour identifier des sections de la vidéo où se produit le mouvement. Il génère un fichier JSON qui contient une entrée de métadonnées avec l’horodatage et la région de cadre englobant dans lequel l’événement s’est produit.

Ciblant les flux vidéo de sécurité, cette technologie est en mesure de catégoriser le mouvement dans les événements pertinents et les faux positifs, tels que des ombres et des changements d’éclairage. Cela vous permet de générer des alertes de sécurité à partir de flux vidéo sans recevoir des messages indésirables avec des événements non pertinents sans fin, tout en étant en mesure d’extraire les moments d’intérêt à partir des vidéos de surveillance extrêmement long.

Le **Détecteur de mouvement Azure Media** MP est actuellement en mode Aperçu.

Cette rubrique donne des détails sur le **Détecteur de mouvement Azure Media** et montre comment l’utiliser avec Media Services SDK pour .NET


##<a name="motion-detector-input-files"></a>Fichiers d’entrée de détecteur de mouvement

Fichiers vidéo. Actuellement, les formats suivants sont pris en charge : WMV, MOV et MP4.

##<a name="task-configuration-preset"></a>Configuration de la tâche (préconfiguration)

Lorsque vous créez une tâche avec le **Détecteur de mouvement Azure Media**, vous devez spécifier une préconfiguration de la configuration. 

###<a name="parameters"></a>Paramètres

Vous pouvez utiliser les paramètres suivants :

Nom|Options|Description|Par défaut
---|---|---|---
sensitivityLevel|Chaîne : « basse », « moyenne », « haute »|Définit le niveau de sensibilité à quels mouvements est signalé. Ajustement pour ajuster le montant de faux positifs.|« support »
frameSamplingValue|Entier positif|Définit la fréquence de l’algorithme qui s’exécute. 1 équivaut à chaque image, 2 signifie que chaque cadre 2 et ainsi de suite.|1
detectLightChange|Boolean : 'true', 'false'|Définit si des changements d’éclairage sont signalés dans les résultats|'False'
mergeTimeThreshold|Xs-heure : Hh : mm :<br/>Exemple : 00:00:03|Spécifie la fenêtre de temps entre les événements de mouvement où 2 événements seront combinées et signalés comme étant 1.|00:00:00
detectionZones|Tableau de zones de détection :<br/>-Zone de détection est un tableau de points de 3 ou plus<br/>-Est un x et y coordonnées allant de 0 à 1.|Décrit la liste des zones de détection polygonale à utiliser.<br/>Résultats seront signalées avec les zones en tant qu’ID, la première étant 'id' : 0|Zone unique qui couvre entièrement le cadre.

###<a name="json-example"></a>Exemple JSON

    
    {
      'version': '1.0',
      'options': {
        'sensitivityLevel': 'medium',
        'frameSamplingValue': 1,
        'detectLightChange': 'False',
        "mergeTimeThreshold":
        '00:00:02',
        'detectionZones': [
          [
            {'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}
           ],
          [
            {'x': 0.3, 'y': 0.3},
            {'x': 0.55, 'y': 0.3},
            {'x': 0.8, 'y': 0.3},
            {'x': 0.8, 'y': 0.55},
            {'x': 0.8, 'y': 0.8},
            {'x': 0.55, 'y': 0.8},
            {'x': 0.3, 'y': 0.8},
            {'x': 0.3, 'y': 0.55}
          ]
        ]
      }
    }


##<a name="motion-detector-output-files"></a>Fichiers de sortie détecteur de mouvement

Une tâche de détection de mouvement renvoie un fichier JSON dans le capital de sortie qui décrit les alertes de mouvement et leurs catégories, dans la vidéo. Le fichier contient des informations sur l’heure et la durée du mouvement détecté dans la vidéo.

L’API du détecteur de mouvement fournit des indicateurs une fois qu’il existe des objets en mouvement dans une vidéo d’arrière-plan fixe (par exemple, une surveillance vidéo). Le détecteur de mouvement est formé pour réduire les fausses alertes, telles que l’éclairage et les modifications de l’ombre. Les limites actuelles des algorithmes incluent des vidéos de vision de nuit, les objets semi-transparentes et petits objets.

###<a id="output_elements"></a>Éléments du fichier de sortie JSON

>[AZURE.NOTE]Dans la dernière version, le format JSON de sortie a été modifiée et peut représenter une modification avec rupture certains clients.

Le tableau suivant décrit les éléments de fichier de sortie JSON.

Élément|Description
---|---
Version|Il s’agit de la version de l’API de la vidéo. La version actuelle est 2.
Échelle de temps|« Fait tic-tac » par seconde de la vidéo.
Décalage|Le décalage de temps pour les estampilles dans « battements ». Dans la version 1.0 de l’API de la vidéo, ce sera toujours 0. À l’avenir les scénarios que pris en charge, cette valeur peut changer.
Fréquence d’images|Images par seconde de la vidéo.
Largeur, hauteur|Fait référence à la largeur et la hauteur de la vidéo en pixels.
Début|L’horodatage de début dans « battements ».
Durée|La longueur de l’événement, dans « battements ».
Intervalle|L’intervalle de chaque entrée de l’événement, dans « battements ».
Événements|Chaque fragment de l’événement contient le mouvement détecté dans cette durée.
Type de|Dans la version actuelle, c’est toujours « 2 » pour le mouvement de générique. Cette offre étiquette vidéo API la flexibilité nécessaire pour classer de mouvement dans les futures versions.
RegionID|Comme expliqué ci-dessus, ce sera toujours 0 dans cette version. Cette étiquette permet de Video API recherche le mouvement dans différentes régions dans les futures versions.
Régions|Fait référence à la zone où vous vous souciez de mouvement dans votre vidéo. <br/><br/>-« id » représente la zone de la région : dans cette version est seulement une ID 0. <br/>-« type » représente la forme de la zone que vous vous souciez de mouvement. Actuellement, « rectangle » et « polygone » sont pris en charge.<br/> Si vous avez spécifié « rectangle », la région a axes x, Y, largeur et hauteur. Les coordonnées X et Y représentent les coordonnées XY supérieur gauche de la région dans une échelle normalisée allant de 0.0 à 1.0. La largeur et la hauteur représentent la taille de la région dans une échelle normalisée allant de 0.0 à 1.0. Dans la version actuelle, X, Y, largeur et hauteur sont toujours fixe à 0, 0 et 1, 1. <br/>Si vous avez spécifié « polygone », la région a dimensions en points. <br/>
Fragments|Les métadonnées sont mémorisé en bloc dans différents segments appelés fragments. Chaque fragment contient un début, la durée, nombre d’intervalles et événements. Un fragment avec aucun événement signifie qu’aucun mouvement a été détectée au cours de cette heure de début et la durée.
Entre crochets]|Chaque support représente un intervalle dans l’événement. Crochets pour cet intervalle ne signifie qu’aucun mouvement a été détecté.
emplacements|Cette nouvelle entrée sous événements répertorie l’emplacement où le mouvement s’est produit. Cela est plus spécifique que les zones de détection.

Voici un exemple de sortie JSON

    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
    
    …
##<a name="limitations"></a>Limitations

- Les formats vidéo d’entrée pris en charge incluent WMV, MOV et MP4.
- Détection de mouvement est optimisée pour les vidéos en arrière-plan fixe. L’algorithme met l’accent sur la réduction des fausses alertes, telles que des changements d’éclairage et des tons foncés.
- Certains mouvement peut ne pas être détecté en raison de difficultés techniques ; par exemple, vidéos de vision de nuit, les objets semi-transparentes et petits objets.


## <a name="sample-code"></a>Exemples de code

Suivantes du programme montre comment :

1. Créer un actif et de télécharger un fichier multimédia dans des immobilisations.
1. Crée une tâche avec une tâche de détection de mouvement vidéo basée sur un fichier de configuration qui contient la présélection json suivant. 
                    
        {
          'Version': '1.0',
          'Options': {
            'SensitivityLevel': 'medium',
            'FrameSamplingValue': 1,
            'DetectLightChange': 'False',
            "MergeTimeThreshold":
            '00:00:02',
            'DetectionZones': [
              [
                {'x': 0, 'y': 0},
                {'x': 0.5, 'y': 0},
                {'x': 0, 'y': 1}
               ],
              [
                {'x': 0.3, 'y': 0.3},
                {'x': 0.55, 'y': 0.3},
                {'x': 0.8, 'y': 0.3},
                {'x': 0.8, 'y': 0.55},
                {'x': 0.8, 'y': 0.8},
                {'x': 0.55, 'y': 0.8},
                {'x': 0.3, 'y': 0.8},
                {'x': 0.3, 'y': 0.55}
              ]
            ]
          }
        }

1. Télécharge les fichiers de sortie de JSON. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace VideoMotionDetection
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
        
                    // Run the VideoMotionDetection job.
                    var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\VideoMotionDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
                }
        
                static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Video Motion Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Video Motion Detection Job");
        
                    // Get a reference to Azure Media Motion Detector.
                    string MediaProcessorName = "Azure Media Motion Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
        
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
[Blog de détecteur de mouvement de Media Services Azure](https://azure.microsoft.com/blog/motion-detector-update/)

[Vue d’ensemble de l’Analytique des Services multimédia pour Azure](media-services-analytics-overview.md)

[Démonstrations multimédias Analytique Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
