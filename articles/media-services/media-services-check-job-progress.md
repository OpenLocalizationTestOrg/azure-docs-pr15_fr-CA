<properties 
    pageTitle="Surveiller la progression du travail à l’aide de .NET" 
    description="Découvrez comment utiliser le code de gestionnaire d’événements pour suivre la progression de la tâche et envoyer des mises à jour de l’état. L’exemple de code est écrit en C# et utilise le Kit de développement de Services de support pour .NET." 
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

# <a name="monitor-job-progress-using-net"></a>Surveiller la progression du travail à l’aide de .NET

> [AZURE.SELECTOR]
- [Portail](media-services-portal-check-job-progress.md)
- [.NET](media-services-check-job-progress.md)
- [RESTE](media-services-rest-check-job-progress.md)

Lorsque vous exécutez des tâches, vous nécessitent souvent un moyen de suivre la progression de la tâche. Vous pouvez vérifier l’état d’avancement par la définition d’un gestionnaire d’événement StateChanged (comme décrit dans cette rubrique) ou de l’utilisation du stockage de la file d’attente d’Azure pour contrôler les notifications de travail des Services de support (comme décrit dans [cette](media-services-dotnet-check-job-progress-with-queues.md) rubrique).

## <a name="define-statechanged-event-handler-to-monitor-job-progress"></a>Définir le Gestionnaire d’événement StateChanged pour surveiller la progression de la tâche

L’exemple de code suivant définit le Gestionnaire d’événement StateChanged. Ce gestionnaire d’événements suit la progression du projet et fournit un état mis à jour, en fonction de l’état. Le code définit également la méthode LogJobStop. Cette méthode d’assistance enregistre les détails de l’erreur.

    private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
    
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("********************");
                Console.WriteLine("Job is finished.");
                Console.WriteLine("Please wait while local tasks or downloads complete...");
                Console.WriteLine("********************");
                Console.WriteLine();
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
    
    private static void LogJobStop(string jobId)
    {
        StringBuilder builder = new StringBuilder();
        IJob job = GetJob(jobId);
    
        builder.AppendLine("\nThe job stopped due to cancellation or an error.");
        builder.AppendLine("***************************");
        builder.AppendLine("Job ID: " + job.Id);
        builder.AppendLine("Job Name: " + job.Name);
        builder.AppendLine("Job State: " + job.State.ToString());
        builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
        builder.AppendLine("Media Services account name: " + _accountName);
        builder.AppendLine("Media Services account location: " + _accountLocation);
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
        // Write the output to a local file and to the console. The template 
        // for an error output file is:  JobStop-{JobId}.txt
        string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
        WriteToFile(outputFile, builder.ToString());
        Console.Write(builder.ToString());
    }
    
    private static string JobIdAsFileName(string jobID)
    {
        return jobID.Replace(":", "_");
    }



##<a name="next-step"></a>Étape suivante

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
