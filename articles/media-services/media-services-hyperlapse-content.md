<properties
    pageTitle="Support de Hyperlapse de fichiers avec Azure Media Hyperlapse | Microsoft Azure"
    description="Azure Media Hyperlapse crée des vidéos bon temps écoulé à partir du contenu de première-personne ou l’appareil photo de l’action. Cette rubrique montre comment utiliser l’indexeur de Media."
    services="media-services"
    documentationCenter=""
    authors="asolanki"
    manager="johndeu"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/19/2016"  
    ms.author="adsolank"/>


# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Support de Hyperlapse de fichiers avec Azure Media Hyperlapse

Hyperlapse de médias Azure est un support processeur (MP) qui crée des vidéos bon temps écoulé à partir du contenu de première-personne ou l’appareil photo de l’action.  Le frère de nuage [de Microsoft Research bureau Hyperlapse Pro](http://aka.ms/hyperlapse)et Mobile de Hyperlapse téléphonique, Microsoft Hyperlapse pour Azure Media Services utilise l’échelle massive de la plate-forme de traitement de supports Azure Media Services à l’échelle horizontalement et de paralléliser en bloc Hyperlapse de traitement.

>[AZURE.IMPORTANT]Microsoft Hyperlapse est conçu pour un fonctionnement optimal sur le contenu de la première personne avec un appareil mobile.  Bien que le métrage de l’appareil-photo peut fonctionne toujours, les performances et la qualité du processeur Azure Media Hyperlapse Media ne sont pas garanties pour les autres types de contenu.  Pour plus d’informations sur Microsoft Hyperlapse pour Azure Media Services et consultez quelques vidéos de l’exemple, consultez l' [introduction de blog](http://aka.ms/azurehyperlapseblog) à partir de la version d’évaluation.

Un Hyperlapse de support Azure travail prend comme entrée un fichier actif MP4, MOV ou WMV ainsi qu’un fichier de configuration qui spécifie les images de la vidéo doivent être le temps écoulé et à quelle vitesse (par exemple, les 10 000 premières trames x 2).  La sortie est un format stabilisée et temps écoulé de l’entrée vidéo.

Pour les dernières mises à jour de Hyperlapse de support d’Azure, consultez [blogs sur les Services de support](https://azure.microsoft.com/blog/topics/media-services/).

## <a name="hyperlapse-an-asset"></a>Hyperlapse d’un actif

Vous devez tout d’abord télécharger le fichier d’entrée souhaité pour Azure Media Services.  Pour en savoir plus sur les concepts impliqués dans le chargement et la gestion de contenu, lisez l' [article de la gestion de contenu](media-services-portal-vod-get-started.md).

###  <a id="configuration"></a>Paramètre de configuration pour Hyperlapse

Une fois votre contenu dans votre compte de Services de support, vous devrez construire votre paramètre de configuration.  Le tableau suivant décrit les champs définis par l’utilisateur :

 Champ | Description
-------|-------------
StartFrame|Le cadre sur lequel le traitement de Microsoft Hyperlapse doit commencer.
NumFrames|Le nombre d’images à traiter
Vitesse|Le facteur permettant d’accélérer la vidéo d’entrée.

Voici un exemple de fichier de configuration de conforme dans XML et JSON :

**Préréglage XML :**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**Préréglage JSON :**

    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }

###  <a id="sample_code"></a>Hyperlapse de Microsoft avec le Kit de développement .NET AMS

La méthode suivante télécharge un fichier multimédia en tant qu’actif et crée une tâche avec le processeur de média Azure Media Hyperlapse.

> [AZURE.NOTE] Vous disposez déjà d’un CloudMediaContext dans la portée avec le nom « contexte » pour que ce code fonctionne.  Pour en savoir plus à ce sujet, lisez l' [article de la gestion de contenu](media-services-dotnet-get-started.md).

> [AZURE.NOTE] L’argument de chaîne « hyperConfig » est supposé être une configuration conforme prédéfinie dans JSON ou XML, tel que décrit ci-dessus.

static bool RunHyperlapseJob (entrée de chaîne, chaîne, chaîne hyperConfig) {/ / créer un actif avec capital IAsset de fichier d’entrée = context. Actifs. CreateAssetAndUploadSingleFile (entrée, "Mon entrée Hyperlapse", AssetCreationOptions.None) ;

récupérer les instances d’Azure Media Hyperlapse MP IMediaProcessor mp = context. MediaProcessors. GetLatestMediaProcessorByName ("Azure Media Hyperlapse") ;

créer un travail avec la tâche de IJob tâche Hyperlapse = context. Travaux. Créer (String.Format ("Hyperlapse {0}", entrée)) ;

Si (String.IsNullOrEmpty(hyperConfig)) {/ / config ne peut pas être vide retournera la valeur false ;}

hyperConfig = File.ReadAllText(hyperConfig) ;

ITask hyperlapseTask = travail. Tasks.AddNew (« Hyperlapse de tâche », mp, hyperConfig, TaskOptions.None) ; hyperlapseTask.InputAssets.Add(asset) ; hyperlapseTask.OutputAssets.AddNew ("Hyperlapse de sortie", AssetCreationOptions.None) ;


travail. Submit() ;

Créer une impression de l’état d’avancement et l’interrogation des tâches tâches progressPrintTask = Task(() Nouveau = > {

IJob jobQuery = null ; faire {var progressContext = contexte ; jobQuery = progressContext.Jobs. Où (j = > j.Id == travail. ID). First() ; Console.WriteLine (string.) Format ("{0} de \t \t {1} {2} », DateTime.Now, jobQuery.State, jobQuery.Tasks[0]. État d’avancement)) ; Thread.Sleep(10000) ; } tandis que (jobQuery.State ! = JobState.Finished & & jobQuery.State ! = JobState.Error & & jobQuery.State ! = JobState.Canceled) ; }); progressPrintTask.Start() ;

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }

### <a id="file_types"></a>Types de fichier pris en charge

- MP4
- MOV
- WMV



##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-links"></a>Liens connexes

[Vue d’ensemble de l’Analytique des Services multimédia pour Azure](media-services-analytics-overview.md)

[Démonstrations multimédias Analytique Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
