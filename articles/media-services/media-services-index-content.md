<properties
    pageTitle="L’indexation des fichiers multimédias à Indexer de Media Azure"
    description="Azure Indexer de Media vous permet de rendre le contenu de vos fichiers multimédias avec possibilité de recherche et de générer une transcription du texte intégral pour les légendes et mots clés. Cette rubrique montre comment utiliser l’indexeur de Media."
    services="media-services"
    documentationCenter=""
    authors="Asolanki"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/12/2016"   
    ms.author="adsolank;juliako;johndeu"/>


# <a name="indexing-media-files-with-azure-media-indexer"></a>L’indexation des fichiers multimédias à Indexer de Media Azure


Azure Indexer de Media vous permet de rendre le contenu de vos fichiers multimédias avec possibilité de recherche et de générer une transcription du texte intégral pour les légendes et mots clés. Vous pouvez traiter un fichier ou plusieurs fichiers multimédias dans un lot.  

>[AZURE.IMPORTANT] Lors de l’indexation de contenu, vérifiez que vous utilisez des fichiers multimédias qui ont très clair vocale (sans la musique de fond, bruit, effets ou souffle de microphone). Voici quelques exemples de contenu approprié : enregistré des réunions, conférences ou présentations. Le contenu suivant peut ne pas être adapté à l’indexation : films, émissions TV, quoi que ce soit avec audio mixte et des effets sonores, du contenu avec le bruit de fond (souffle) mal enregistrement.


Une tâche d’indexation peut générer les sorties suivantes :

- Fermeture de fichiers de légendes dans les formats suivants : **SAMI**, **TTML**et **WebVTT**.

    Fichiers de sous-titrage incluent une balise appelée raisons de reconnaissance, les scores d’une tâche d’indexation en fonction de comment reconnaître la voix de la vidéo source.  Vous pouvez utiliser la valeur de raisons de reconnaissance pour les fichiers de sortie d’écran de facilité d’utilisation. Un score faible signifie mauvais résultats d’indexation en raison de la qualité audio.
- Fichier de mots-clés (XML).
- Audio de l’indexation de fichier de blob (AIB) à utiliser avec SQL server.

    Pour plus d’informations, consultez [Utilisation de fichiers de AIB Indexer de Media Azure et SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Cette rubrique montre comment créer des tâches d’indexation **Index un actif** et **plusieurs fichiers d’Index**.

Pour les dernières mises à jour de Indexer de Media Azure, consultez [blogs sur les Services de support](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>À l’aide des fichiers de configuration et de manifeste pour l’indexation des tâches

Vous pouvez spécifier plus de détails pour vos tâches d’indexation en utilisant une tâche configuration. Par exemple, vous pouvez spécifier les métadonnées à utiliser pour votre fichier média. Ces métadonnées sont utilisée par le moteur de langage pour développer son vocabulaire et améliore considérablement la précision de la reconnaissance vocale.  Vous pouvez également spécifier vos fichiers de sortie de votre choix.

Vous pouvez également traiter plusieurs fichiers de support à la fois à l’aide d’un fichier manifeste.

Pour plus d’informations, reportez-vous à [Tâche prédéfinie pour Indexer de Media Azure](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Index d’actif

La méthode suivante télécharge un fichier multimédia en tant qu’actif et crée un projet pour indexer la ressource.

Notez que si aucun fichier de configuration n’est spécifié, le fichier multimédia sera indexé avec tous les paramètres par défaut.

    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
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
<!-- __ -->
### <a id="output_files"></a>Fichiers de sortie

Par défaut, une tâche d’indexation génère les fichiers de sortie suivants. Les fichiers seront stockés dans la première immobilisation de sortie.

Lorsqu’il existe plus d’un fichier de média d’entrée, indexeur générera un fichier manifeste pour les sorties de projet, nommé « JobResult.txt ». Pour chaque entrée de fichier multimédia, AIB résultant, SAMI, TTML, WebVTT, les fichiers de mot clé, sont séquentiellement numérotées et nommés à l’aide de le « Alias ».

Nom de fichier | Description
----------|------------
__InputFileName.aib__ | Fichier d’objet blob d’indexation audio. <br /><br /> Fichier d’indexation Blob (AIB) audio est un fichier binaire qui peut être recherché dans un serveur Microsoft SQL à l’aide de la recherche de texte intégral.  Le fichier AIB est plus puissant que les fichiers de légende simple, car il contient des alternatives pour chaque mot, permettant une expérience de recherche plus riche. <br/> <br/>Il nécessite l’installation du module complémentaire d’indexeur SQL sur un ordinateur exécutant Microsoft SQL server 2008 ou version ultérieure. Recherche de texte intégral de serveur recherche la AIB à l’aide de Microsoft SQL fournit des résultats de recherche plus précises que la recherche les fichiers de sous-titres générés par WAMI. C’est pourquoi le AIB contient des synonymes le son similaire tandis que les fichiers de sous-titres contiennent le mot en toute confiance le plus élevé pour chaque segment de l’audio. Si vous recherchez des mots prononcés revêt une importance upmost, il est recommandé d’utiliser la conjonction AIB dans avec Microsoft SQL Server.<br/><br/> Pour télécharger le module complémentaire, cliquez sur <a href="http://aka.ms/indexersql">Complément de SQL Azure Media indexeur</a>. <br/><br/>Il est également possible d’utiliser d’autres moteurs de recherche tels que Apache Lucene/mode série sur LAN.r simplement indexer la vidéo selon les sous-titres et les fichiers XML de mot clé, mais cela entraîne des résultats de recherche moins précises.
__InputFileName.smi__<br />__InputFileName.ttml__<br />__InputFileName.vtt__ |Fichiers fermés de la légende (CC) dans les formats SAMI, TTML et WebVTT.<br/><br/>Ils permettent de rendre les fichiers audio et vidéo accessibles aux personnes atteintes de handicap de l’audition.<br/><br/>Les fichiers de légendes fermées incluent une balise appelée <b>raisons de reconnaissance</b> , les résultats d’une tâche d’indexation basée sur comment reconnaître la reconnaissance vocale de la vidéo source est.  Vous pouvez utiliser la valeur de <b>raisons de reconnaissance</b> pour les fichiers de sortie d’écran de facilité d’utilisation. Un score faible signifie mauvais résultats d’indexation en raison de la qualité audio.
__InputFileName.kw.xml<br />InputFileName.info__ |Mot clé et les informations sur les fichiers. <br/><br/>Fichier de mots-clés est un fichier XML qui contient des mots clés de l’extrait à partir du contenu de la parole, avec la fréquence et les informations d’offset. <br/><br/>Fichier Info est un fichier de texte brut qui contient des informations précises sur chacun des termes reconnus. La première ligne est spéciale et contient le score de raisons de reconnaissance. Chaque ligne suivante est une liste séparées par des tabulations, des données suivantes : Démarrer heure, heure de fin, mot/expression, en toute confiance. Les temps sont exprimés en secondes et la confiance est donnée sous la forme d’un nombre de 0 à 1. <br/><br/>Exemple de ligne : « 1,20 1,45 word 0.67 » <br/><br/>Ces fichiers peuvent être utilisés pour plusieurs raisons, telles que, pour effectuer l’analytique de reconnaissance vocale, ou exposées pour moteurs de recherche comme Bing, Google ou Microsoft SharePoint pour rendre les fichiers multimédias plus détectable, ou même utilisé pour fournir des annonces plus pertinentes.
__JobResult.txt__ |Manifeste de sortie présente uniquement lors de l’indexation de plusieurs fichiers, contenant les informations suivantes :<br/><br/><table border="1"><tr><th>Fichier d’entrée</th><th>Alias</th><th>MediaLength</th><th>Erreur</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/>



Si ce n’est pas le cas, tous les fichiers multimédias d’entrée sont indexés avec succès, le travail d’indexation échoue avec le code d’erreur 4000. Pour plus d’informations, consultez [codes d’erreur](#error_codes).

## <a name="index-multiple-files"></a>Plusieurs fichiers d’index

La méthode suivante télécharge plusieurs fichiers multimédias en tant qu’actif et crée un projet pour indexer tous ces fichiers dans un lot.

Un fichier manifeste avec l’extension .lst est créé et téléchargement dans l’immobilisation. Le fichier manifeste contient la liste de tous les fichiers actifs. Pour plus d’informations, reportez-vous à [Tâche prédéfinie pour Indexer de Media Azure](https://msdn.microsoft.com/library/dn783454.aspx).

    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }

### <a name="partially-succeeded-job"></a>Succès partiel de travail

Si ce n’est pas le cas, tous les fichiers multimédias d’entrée sont indexés avec succès, le travail d’indexation échoue avec le code d’erreur 4000. Pour plus d’informations, consultez [codes d’erreur](#error_codes).


Les mêmes sorties (en tant que travaux a réussi) sont générées. Vous pouvez faire référence au fichier manifeste de sortie pour savoir quels fichiers d’entrée sont a échoué, en fonction des valeurs de colonne d’erreur. Des fichiers d’entrée qui a échoué, le AIB résultant, SAMI, TTML, WebVTT et mot clé fichiers ne seront pas générés.

### <a id="preset"></a>Présélection de tâche pour Indexer de Media Azure

Le traitement de l’indexeur de Media Azure peut être personnalisé en fournissant une tâche facultative prédéfinie à côté de la tâche.  La liste suivante décrit le format de ce fichier xml de configuration.

Nom | Besoin | Description
----|----|---
__entrée__ | False | Actif ou les fichiers que vous souhaitez indexer.</p><p>Indexer de Media Azure prend en charge les formats de fichier multimédia suivants : MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Vous pouvez spécifier le nom de fichier (s) dans l’attribut de **nom** ou de la **liste** de l’élément **d’entrée** (comme indiqué ci-dessous). Si vous ne spécifiez pas de fichier actif à l’index, le fichier primaire est prélevé. Si aucun fichier principal n’est défini, le premier fichier de l’élément d’entrée est indexé.</p><p>Pour spécifier explicitement le nom du fichier actif, effectuez :<br />`<input name="TestFile.wmv">`<br /><br />Vous pouvez également effectuer l’indexation plusieurs ressources fichiers à la fois (jusqu'à 10). Pour ce faire :<br /><br /><ol class="ordered"><li><p>Créez un fichier texte (fichier manifest) et lui donner une extension .lst. </p></li><li><p>Ajouter une liste de tous les noms de fichier de ressources dans vos ressources d’entrée à ce fichier de manifeste. </p></li><li><p>Ajouter le fichier de thanifest (téléchargement) à l’actif.  </p></li><li><p>Spécifiez le nom du fichier manifeste dans l’attribut de liste de l’entrée.<br />`<input list="input.lst">`</li></ol><br /><br />Remarque : Si vous ajoutez plus de 10 fichiers au fichier manifeste, la tâche d’indexation échoue avec le code d’erreur 2006.
__métadonnées__ | False | Métadonnées pour l’ou les fichiers bien spécifié utilisé pour l’Adaptation du vocabulaire.  Utile pour préparer l’indexeur à reconnaître les mots de vocabulaire non standard tels que des noms propres.<br />`<metadata key="..." value="..."/>` <br /><br />Vous pouvez fournir des __valeurs__ prédéfinies de __clés__. Les clés suivantes sont actuellement prises en charge :<br /><br />« titre » et « description » - permet de modifier la langue pour l’adaptation du vocabulaire modèle pour votre travail et améliorent la précision de la reconnaissance vocale.  Les valeurs de semences des recherches sur Internet pour trouver des documents de texte approprié en fonction du contexte, à l’aide de la valeur pour compléter le dictionnaire interne pour la durée de la tâche d’indexation.<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__fonctionnalités__ <br /><br /> Ajouté dans la version 1.2. Actuellement, la seule fonctionnalité prise en charge est la reconnaissance vocale (« récupération automatique du système »).| False | La fonctionnalité de reconnaissance vocale a les clés des paramètres suivants :<table><tr><th><p>Clé</p></th>     <th><p>Description</p></th><th><p>Exemple de valeur</p></th></tr><tr><td><p>Langue</p></td><td><p>Le langage naturel à être reconnu dans le fichier multimédia.</p></td><td><p>Anglais, espagnol</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>met en forme une liste séparée par des points-virgules de la légende souhaitée (le cas échéant)</p></td><td><p>ttml ; Same ; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Un indicateur booléen indiquant si un fichier AIB est requis (pour une utilisation avec SQL Server et le client IFilter d’indexeur).  Pour plus d’informations, consultez <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Utilisation de fichiers de AIB Indexer de Media Azure et SQL Server</a>.</p></td><td><p>Valeur True ; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Un indicateur booléen indiquant si un fichier XML de mot clé est requis.</p></td><td><p>Valeur True ; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Un indicateur booléen indiquant ou non forcer les légendes complets (quel que soit le niveau de confiance).  </p><p>Valeur par défaut est false, auquel cas les mots et les phrases qui ont une niveau de confiance de 50 % de moins sont omis dans les sorties de légende final et remplacés par les points de suspension («... »).  Les points de suspension sont utiles pour l’audit et le contrôle de qualité de légende.</p></td><td><p>Valeur True ; False. </p></td></tr></table>

### <a id="error_codes"></a>Codes d’erreur

En cas d’erreur, signalez les Indexer de Media Azure sauvegarder un des codes d’erreur suivants :

Code | Nom | Raisons possibles
-----|------|------------------
2000 | Configuration non valide | Configuration non valide
2001 | Actifs d’entrée non valides | Pas d’entrée d’actifs ou un élément vide.
2002 | Manifeste non valide | Manifeste est vide ou manifeste contient des éléments non valides.
2003 | Échec du téléchargement du fichier multimédia | URL non valide dans le fichier de manifeste.
2004 | Protocole non pris en charge | Protocole d’URL media n’est pas pris en charge.
2005 | Type de fichier non pris en charge | Type de fichier multimédia d’entrée n’est pas pris en charge.
2006 | Trop de fichiers d’entrée | Il existe plus de 10 fichiers dans le manifeste d’entrée.
3000 | Impossible de décoder le fichier multimédia | Codec de média non pris en charge <br/>ou<br/> Fichier endommagé <br/>ou<br/> Aucun flux de données audio de média d’entrée.
4000 | Indexation partiellement réussie | Certains des fichiers média d’entrée n’a pas pu être indexé. Pour plus d’informations, consultez <a href="#output_files">fichiers de sortie</a>.
autres | Erreurs internes | Contactez l’équipe de support. indexer@microsoft.com


## <a id="supported_languages"></a>Langues prises en charge

Actuellement, les langues anglais et en espagnol sont pris en charge. Pour plus d’informations, consultez [le billet de blog version 1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Liens connexes

[Vue d’ensemble de l’Analytique des Services multimédia pour Azure](media-services-analytics-overview.md)

[À l’aide de fichiers AIB Indexer de Media Azure et SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[L’indexation des fichiers multimédia avec Media Azure indexeur 2 Aperçu](media-services-process-content-with-indexer2.md)
