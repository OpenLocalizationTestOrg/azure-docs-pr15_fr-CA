<properties 
    pageTitle="Stockage de la file d’attente d’Azure permet de contrôler les notifications de travail des Services de support avec .NET | Microsoft Azure" 
    description="Apprenez à utiliser le stockage de la file d’attente d’Azure pour contrôler les notifications de travail des Services de support. L’exemple de code est écrit en C# et utilise le Kit de développement de Services de support pour .NET." 
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
    ms.date="08/19/2016"   
    ms.author="juliako"/>

# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Stockage de la file d’attente d’Azure permet de contrôler les notifications de travail des Services de support avec .NET

Lorsque vous exécutez des tâches, vous nécessitent souvent un moyen de suivre la progression de la tâche. Vous pouvez vérifier la progression à l’aide de stockage de la file d’attente d’Azure pour contrôler les notifications de travail des Services de support (comme décrit dans cette rubrique) ou de définition d’un gestionnaire d’événement StateChanged (comme décrit dans [cette](media-services-check-job-progress.md) rubrique.  

## <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications"></a>Stockage de la file d’attente d’Azure permet de contrôler les notifications de travail des Services de support

Microsoft Azure Media Services a la possibilité de remettre les messages de notification pour le [stockage de la file d’attente d’Azure](../storage-dotnet-how-to-use-queues.md#what-is) lors du traitement des tâches de support. Cette rubrique montre comment obtenir ces messages de notification à partir du stockage de la file d’attente.

Messages remis au stockage de la file d’attente est accessible à partir de n’importe où dans le monde. La file d’attente Azure architecture de messagerie est fiable et hautement évolutive. L’interrogation de stockage de la file d’attente est préférable d’utiliser d’autres méthodes. 

Un scénario courant pour écouter les notifications de Media Services est que si vous développez un système de gestion de contenu qui doit effectuer quelques tâches supplémentaires après une tâche de codage se termine (par exemple, le déclencheur de la prochaine étape dans un flux de travail, ou publier du contenu). 

###<a name="considerations"></a>Considérations relatives à la

Considérez les éléments suivants lors du développement d’applications des Services de médias qui utilisent la file d’attente de stockage Azure.

- Le service de files d’attente ne fournit pas une garantie de first-in-first-out (FIFO) commandé la livraison. Pour plus d’informations, reportez-vous à la section [files d’attente Azure et la comparaison avec les files d’attente de Bus de Service d’Azure et Contrasted](https://msdn.microsoft.com/library/azure/hh767287.aspx).
- Files d’attente de stockage Azure n’est pas un service de transmission ; Vous devez interroger la file d’attente. 
- Vous pouvez avoir n’importe quel nombre de files d’attente. Pour plus d’informations, voir [API de file d’attente Service REST](https://msdn.microsoft.com/library/azure/dd179363.aspx).
- Files d’attente de stockage Azure a certaines limitations et les caractéristiques qui sont décrites dans l’article suivant : [files d’attente Azure et la comparaison avec les files d’attente de Bus de Service d’Azure et Contrasted](https://msdn.microsoft.com/library/azure/hh767287.aspx).

###<a name="code-example"></a>Exemple de code

L’exemple de code dans cette section effectue les opérations suivantes :

1. Définit la classe **EncodingJobMessage** qui mappe vers le format de message de notification. Le code désérialise les messages reçus de la file d’attente en objets du type **EncodingJobMessage** .
1. Charge les informations de compte de Services de support et de stockage à partir du fichier app.config. Utilise ces informations pour créer les objets **CloudMediaContext** et **CloudQueue** .
1. Crée la file d’attente qui reçoit les messages de notification à propos de la tâche de codage.
1. Crée le point de fin de notification qui est mappé à la file d’attente.
1. Attache le point de fin de notification à la tâche et soumet le travail de codage. Vous pouvez avoir plusieurs points de terminaison de notification associés à une tâche.
1. Dans cet exemple, nous sommes uniquement intéressé dans les États finaux de la tâche de traitement, afin de nous transmettre **NotificationJobState.FinalStatesOnly** à la méthode **AddNew** . 
        
        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
1. Si vous passez à NotificationJobState.All, vous devriez obtenir toutes les notifications de changement d’état : en attente -> planifiée -> traitement -> terminé. Toutefois, comme indiqué précédemment, le service de files d’attente de stockage Azure ne garantit pas de livraison chronologique des messages. Vous pouvez utiliser la propriété Timestamp (définie sur le type de EncodingJobMessage dans l’exemple ci-dessous) pour les messages de la commande. Il est possible que vous obtenez des messages de notification en double. Utilisez la propriété ETag (définie sur le type EncodingJobMessage) pour vérifier les doublons. Il est également possible que certaines notifications de changement d’état va être ignorées. 
1. Attend que la tâche de vérification de la file d’attente toutes les 10 secondes pour accéder à l’état terminé. Supprime les messages après que qu’ils ont été traités.
1. Supprime de la file d’attente et le point de fin de notification.

>[AZURE.NOTE]Il est recommandé de surveiller l’état d’un projet en écoutant les messages de notification, comme illustré dans l’exemple suivant.
>
>Sinon, vous pourriez vérifier sur l’état d’un projet à l’aide de la propriété **IJob.State** .  Un message de notification à propos de l’achèvement d’un travail peut arriver avant que l’état de **IJob** est défini sur **terminé**. La propriété **IJob.State** reflète l’état précis avec un léger retard.

    
    using System;
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Web;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Runtime.Serialization.Json;
    
    namespace JobNotification
    {
        public class EncodingJobMessage
        {
            // MessageVersion is used for version control. 
            public String MessageVersion { get; set; }
        
            // Type of the event. Valid values are 
            // JobStateChange and NotificationEndpointRegistration.
            public String EventType { get; set; }
        
            // ETag is used to help the customer detect if 
            // the message is a duplicate of another message previously sent.
            public String ETag { get; set; }
        
            // Time of occurrence of the event.
            public String TimeStamp { get; set; }
    
            // Collection of values specific to the event.
    
            // For the JobStateChange event the values are:
            //     JobId - Id of the Job that triggered the notification.
            //     NewState- The new state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
            //     OldState- The old state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
    
            // For the NotificationEndpointRegistration event the values are:
            //     NotificationEndpointId- Id of the NotificationEndpoint 
            //          that triggered the notification.
            //     State- The state of the Endpoint. 
            //          Valid values are: Registered and Unregistered.
    
            public IDictionary<string, object> Properties { get; set; }
        }
    
        class Program
        {
            private static CloudMediaContext _context = null;
            private static CloudQueue _queue = null;
            private static INotificationEndPoint _notificationEndPoint = null;
    
            private static readonly string _singleInputMp4Path =
                Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");
    
            static void Main(string[] args)
            {
                // Get the values from app.config file.
                string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
                string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
                string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];
    
    
                string endPointAddress = Guid.NewGuid().ToString();
    
                // Create the context. 
                _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);
    
                // Create the queue that will be receiving the notification messages.
                _queue = CreateQueue(storageConnectionString, endPointAddress);
    
                // Create the notification point that is mapped to the queue.
                _notificationEndPoint = 
                        _context.NotificationEndPoints.Create(
                        Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);
    
    
                if (_notificationEndPoint != null)
                {
                    IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                    WaitForJobToReachedFinishedState(job.Id);
                }
    
                // Clean up.
                _queue.Delete();      
                _notificationEndPoint.Delete();
           }
    
    
            static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
            {
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);
    
                // Create the queue client
                CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
                // Retrieve a reference to a queue
                CloudQueue queue = queueClient.GetQueueReference(endPointAddress);
    
                // Create the queue if it doesn't already exist
                queue.CreateIfNotExists();
    
                return queue;
            }
     
    
            public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");
    
                //Create an encrypted asset and upload the mp4. 
                IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
                    inputMediaFilePath);
    
                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
    
                // Create a task with the conversion details, using a configuration file. 
                ITask task = job.Tasks.AddNew("My encoding Task",
                    processor,
                    "H264 Multiple Bitrate 720p",
                    Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
    
                // Specify the input asset to be encoded.
                task.InputAssets.Add(asset);
    
                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);
    
                // Add a notification point to the job. You can add multiple notification points.  
                job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, 
                    _notificationEndPoint);
    
                job.Submit();
    
                return job;
            }
    
            public static void WaitForJobToReachedFinishedState(string jobId)
            {
                int expectedState = (int)JobState.Finished;
                int timeOutInSeconds = 600;
    
                bool jobReachedExpectedState = false;
                DateTime startTime = DateTime.Now;
                int jobState = -1;
    
                while (!jobReachedExpectedState)
                {
                    // Specify how often you want to get messages from the queue.
                    Thread.Sleep(TimeSpan.FromSeconds(10));
    
                    foreach (var message in _queue.GetMessages(10))
                    {
                        using (Stream stream = new MemoryStream(message.AsBytes))
                        {
                            DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                            settings.UseSimpleDictionaryFormat = true;
                            DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                            EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);
    
                            Console.WriteLine();
    
                            // Display the message information.
                            Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                            Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                            Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                            Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                            foreach (var property in encodingJobMsg.Properties)
                            {
                                Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                            }
    
                            // We are only interested in messages 
                            // where EventType is "JobStateChange".
                            if (encodingJobMsg.EventType == "JobStateChange")
                            {
                                string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                                if (JobId == jobId)
                                {
                                    string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                    string newJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "NewState").FirstOrDefault().Value;
    
                                    JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                    JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);
    
                                    if (newJobState == (JobState)expectedState)
                                    {
                                        Console.WriteLine("job with Id: {0} reached expected state: {1}", 
                                            jobId, newJobState);
                                        jobReachedExpectedState = true;
                                        break;
                                    }
                                }
                            }
                        }
                        // Delete the message after we've read it.
                        _queue.DeleteMessage(message);
                    }
    
                    // Wait until timeout
                    TimeSpan timeDiff = DateTime.Now - startTime;
                    bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                    if (timedOut)
                    {
                        Console.WriteLine(@"Timeout for checking job notification messages, 
                                            latest found state ='{0}', wait time = {1} secs",
                            jobState,
                            timeDiff.TotalSeconds);
    
                        throw new TimeoutException();
                    }
                }
            }
       
            static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
            {
                var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(), 
                    assetCreationOptions);
    
                var fileName = Path.GetFileName(singleFilePath);
    
                var assetFile = asset.AssetFiles.Create(fileName);
    
                Console.WriteLine("Created assetFile {0}", assetFile.Name);
                Console.WriteLine("Upload {0}", assetFile.Name);
    
                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading of {0}", assetFile.Name);
    
                return asset;
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

L’exemple ci-dessus produit la sortie suivante. Vous, les valeurs peuvent varier.
    
    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4
    
    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35
    
    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected 
    State: Finished
    

## <a name="next-step"></a>Étape suivante

Révision Media Services, voies de formation

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
