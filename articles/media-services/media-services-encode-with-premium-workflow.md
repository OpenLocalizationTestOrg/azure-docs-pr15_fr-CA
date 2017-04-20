<properties 
    pageTitle="Avancée de codage avec Media Encoder Premium Workflow | Microsoft Azure" 
    description="Découvrez comment coder avec Media Encoder Premium Workflow. Exemples de code sont écrits en C# et utilisent le Kit de développement de Services de support pour .NET." 
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
    ms.date="09/26/2016" 
    ms.author="juliako"/>

#<a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Avancée de codage avec le Workflow de prime Media Encoder

>[AZURE.NOTE] Processeur de média Media Encoder Premium Workflow présenté dans cette rubrique n’est pas disponible en Chine.

Pour les questions de codeur premium, messagerie mepd sur le site Microsoft.com.

##<a name="overview"></a>Vue d’ensemble

Microsoft Azure Media Services introduit le processeur média **Media Encoder Premium Workflow** . Cette offre de processeur avance destinée à vos workflows à la demande de prime de codage. 

Les rubriques suivantes décrivent les détails relatifs au **Processus de Media Encoder Premium**: 

- [Formats pris en charge par le flux de travail Media Encoder prime](media-services-premium-workflow-encoder-formats.md) – présente les codecs et les formats de fichier pris en charge par **Media Encoder Premium Workflow**.

- La section [comparer les encodeurs](media-services-encode-asset.md#compare_encoders) compare les fonctions de codage du **Workflow de prime de codeur de Media** et **Media Encoder Standard**.

Cette rubrique montre comment coder avec le **Workflow de prime Media Encoder** à l’aide de .NET.

Tâches d’encodage pour le **Workflow de Media Encoder Premium** requièrent un fichier de configuration distinct, appelé fichier de flux de travail. Ces fichiers ont une extension .workflow et sont créés à l’aide de l’outil [Concepteur de flux de travail](media-services-workflow-designer.md) .

##<a name="encode"></a>Encoder

Tâches d’encodage pour le **Workflow de Media Encoder Premium** requièrent un fichier de configuration distinct, appelé fichier de flux de travail. Ces fichiers ont une extension .workflow et sont créés à l’aide de l’outil [Concepteur de flux de travail](media-services-workflow-designer.md) .


Vous pouvez également obtenir la valeur par défaut des fichiers de flux de travail [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Le dossier contient également la description de ces fichiers.

Les fichiers de flux de travail doivent être téléchargées à votre compte de Services de support en tant qu’actif, et cet actif doit être passé à la tâche de codage.

L’exemple suivant montre comment coder avec **Media Encoder Premium Workflow**. 

Les étapes suivantes sont effectuées : 
 
1. Créer un actif et de télécharger un fichier de flux de travail. 
2. Créer un actif et de télécharger un fichier de média source.
3. Obtenir le processeur média « Media Encoder Premium Workflow ».
4. Créer un projet et une tâche. 

    Dans la plupart des cas, la chaîne de configuration de la tâche est vide (comme dans l’exemple suivant). Il existe certains scénarios avancés (qui nécessitent de définir dynamiquement les propriétés d’exécution) dans ce cas, vous fourniriez une chaîne XML à la tâche de codage. Voici quelques exemples de ces scénarios : création d’une superposition, le media parallèle ou séquentielle de combinaison, le sous-titrage.
5. Ajoutez deux éléments d’entrée pour la tâche.
    
    une barre d’outils. 1 – l’actif de flux de travail.

    b. 2 – l’élément vidéo.
    
    **Remarque**: l’élément de flux de travail doit être ajouté à la tâche avant la ressource multimédia. La chaîne de configuration de cette tâche doit être vide. 

6. Soumettre la tâche d ' encodage.

Voici un exemple complet. Pour plus d’informations sur la configuration avec le développement de Media Services .NET, consultez [développement de Media Services avec .NET](media-services-dotnet-how-to-use.md).


    using System; 
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.MediaServices.Client;
    
    namespace MediaEncoderPremiumWorkflowSample
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
    
            private static readonly string _supportFiles =
                Path.GetFullPath(@"../..\Media");
    
            private static readonly string _workflowFilePath =
                Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");
            
            private static readonly string _singleMP4InputFilePath =
                Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");
    
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
    
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
                var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
                IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset); 
    
            }
    
            static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
            {
                var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
                var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
                var fileName = Path.GetFileName(singleFilePath);
    
                var assetFile = asset.AssetFiles.Create(fileName);
    
                Console.WriteLine("Created assetFile {0}", assetFile.Name);
    
                var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                    AccessPermissions.Write | AccessPermissions.List);
    
                var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
    
                Console.WriteLine("Upload {0}", assetFile.Name);
    
                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading {0}", assetFile.Name);
    
                locator.Delete();
                accessPolicy.Delete();
    
                return asset;
            }
    
            static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("Premium Workflow encoding job");
                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");
    
                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                    processor,
                    "",
                    TaskOptions.None);
    
                // Specify the input asset to be encoded.
                task.InputAssets.Add(workflow);
                task.InputAssets.Add(video); // we add one asset
                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is not encrypted. 
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);
    
                // Use the following event handler to check job progress.  
                job.StateChanged += new
                        EventHandler<JobStateChangedEventArgs>(StateChanged);
    
                // Launch the job.
                job.Submit();
    
                // Check job execution and wait for job to finish. 
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
                progressJobTask.Wait();
    
                // If job state is Error the event handling 
                // method for job progress should log errors.  Here we check 
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    throw new Exception("\nExiting method due to job error.");
                }
    
                return job.OutputMediaAssets[0];
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
                        LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }
    
            static private void LogJobStop(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = _context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();
    
                builder.AppendLine("\nThe job stopped due to cancellation or an error.");
                builder.AppendLine("***************************");
                builder.AppendLine("Job ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job State: " + job.State.ToString());
                builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
                builder.AppendLine("Media Services account name: " + _mediaServicesAccountName);
                // Log job errors if they exist.  
                if (job.State == JobState.Error)
                {
                    builder.Append("Error Details: \n");
                    foreach (ITask task in job.Tasks)
                    {
                        foreach (ErrorDetail detail in task.ErrorDetails)
                        {
                            builder.AppendLine("  Task Id: " + task.Id);
                            builder.AppendLine("    Error Code: " + detail.Code);
                            builder.AppendLine("    Error Message: " + detail.Message + "\n");
                        }
                    }
                }
                builder.AppendLine("***************************\n");
    
                Console.Write(builder.ToString());
            }
    
    
            static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
    
                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
    
                return processor;
            }
        }
    }


Pour les questions de codeur premium, messagerie mepd sur le site Microsoft.com.

##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
