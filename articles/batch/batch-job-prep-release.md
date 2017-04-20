<properties
    pageTitle="La tâche de préparation et nettoyage dans le traitement par lots | Microsoft Azure"
    description="Tâches de préparation de niveau tâche permet de réduire le transfert de données vers les nœuds de calcul Azure lot et libérer les tâches de nettoyage du nœud à la fin de la tâche."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="09/16/2016"
    ms.author="marsma" />

# <a name="run-job-preparation-and-completion-tasks-on-azure-batch-compute-nodes"></a>Exécuter les tâches de préparation et d’exécution sur les nœuds de calcul Azure lot

 Un traitement par lots Azure nécessite souvent une forme quelconque de l’installation avant que ses tâches sont exécutées et après la tâche maintenance lorsque ses tâches sont terminées. Vous devrez peut-être télécharger des données d’entrée des tâches communes sur vos nœuds de calcul, ou télécharger des données de sortie de tâche pour le stockage Azure une fois la tâche terminée. Vous pouvez utiliser les tâches de **Préparation du projet** et la **tâche de mise à jour** pour effectuer ces opérations.

## <a name="what-are-job-preparation-and-release-tasks"></a>Que sont la préparation du projet et les tâches ?

Avant l’exécution de tâches d’un projet, la tâche de préparation du projet s’exécute sur tous les nœuds de calcul planifiées pour s’exécuter au moins une tâche. Une fois la tâche terminée, la tâche de version s’exécute sur chaque nœud dans le pool qui a exécuté au moins une tâche. À l’instar des tâches normales de traitement par lots, vous pouvez spécifier une ligne de commande à appeler lors de l’exécution d’une tâche de préparation ou de la version du projet.

Tâches de préparation et de la version offrent des fonctions de tâche de lot courantes telles que le téléchargement de fichier ([fichiers de ressources][net_job_prep_resourcefiles]), avec des privilèges élevés à l’exécution, variables d’environnement personnalisées, durée d’exécution maximale, nombre de tentatives et la période de rétention.

Dans les sections suivantes, vous allez apprendre à utiliser le [JobPreparationTask] de[ net_job_prep] et [JobReleaseTask] [ net_job_release] classes se trouvent dans le [Traitement par lots .NET] [ api_net] bibliothèque.

> [AZURE.TIP] Tâches de préparation et de la version sont particulièrement utiles dans les environnements de « partagés pool », dans lequel un ensemble de nœuds de calcul persiste entre les exécutions de la tâche et est utilisé par un grand nombre de travaux.

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Quand utiliser la préparation du projet et de tâches

Préparation du travail et des tâches de publication sont une bonne solution pour les situations suivantes :

**Télécharger des données de tâches communes**

Traitements par lots nécessitent souvent un ensemble commun de données comme entrée pour les tâches de la tâche. Par exemple, dans les calculs de l’analyse des risques quotidiens, données du marché sont spécifiques à un projet, encore communes à toutes les tâches dans le projet. Ces données de marché, taille, souvent de plusieurs gigaoctets doivent être transférées qu’une seule fois pour chaque nœud de calcul afin que n’importe quelle tâche qui s’exécute sur le nœud puisse l’utiliser. Une **tâche de préparation** permet de télécharger ces données à chaque nœud avant l’exécution de la tâche 's des autres tâches.

**Supprimer la sortie du projet et la tâche**

Dans un environnement « pool de partagé », où les nœuds de calcul d’un pool ne sont pas mis hors service entre les tâches, vous devrez supprimer les données de projet entre les exécutions. Vous devrez peut-être économiser de l’espace disque sur les nœuds, ou répondre aux stratégies de sécurité de votre organisation. Une **tâche de publication** permet de supprimer des données qui a été téléchargées par une tâche de préparation ou générées lors de l’exécution de la tâche.

**Rétention de journal**

Vous pouvez souhaiter conserver une copie des fichiers journaux qui génèrent de vos tâches, ou peut-être bloquer les fichiers de vidage pouvant être générées par les applications a échoué. Utilisez une **tâche de publication** dans de tels cas pour compresser et télécharger des données vers un [Stockage Azure] [ azure_storage] compte.

>[AZURE.TIP] Une autre façon de conserver les journaux de projet et d’autres tâches de sortie données sont d’utiliser la bibliothèque de [Conventions des fichiers par lots Azure](batch-task-output.md) .

## <a name="job-preparation-task"></a>Tâche de préparation

Avant l’exécution de tâches d’un projet, lot exécute la tâche de préparation du travail sur chaque nœud de calcul qui est planifiée pour exécuter une tâche. Par défaut, le service de lot attend que la tâche Préparation du travail à effectuer avant d’exécuter les tâches planifiées pour s’exécuter sur le nœud. Toutefois, vous pouvez configurer le service ne pas en attente. Si le nœud a redémarré, la tâche de préparation du projet s’exécute à nouveau, mais vous pouvez également désactiver ce comportement.

La tâche de préparation du travail est exécutée uniquement sur des nœuds qui sont planifiées pour exécuter une tâche. Cela empêche l’exécution inutile d’une tâche de préparation dans le cas où un nœud n’est pas attribué une tâche. Cela peut se produire lorsque le nombre de tâches d’un projet est inférieur au nombre de nœuds dans un pool. Il s’applique également lors de [l’exécution des tâches simultanées](batch-parallel-node-tasks.md) est activée, ce qui laisse certains nœuds inactif si le nombre de tâches est inférieur au nombre total de tâches simultané possible. Par exécute ne pas la tâche de préparation du travail sur des nœuds de l’inactivité, vous pouvez dépenser moins d’argent sur les frais de transfert de données.

> [AZURE.NOTE] [JobPreparationTask] [ net_job_prep_cloudjob] diffère de [CloudPool.StartTask] [ pool_starttask] dans la mesure où JobPreparationTask s’exécute au début de chaque tâche, alors que la tâche de début s’exécute uniquement lorsqu’un nœud de calcul tout d’abord rejoint un pool ou redémarre.

## <a name="job-release-task"></a>Tâche de publication

Une fois qu’une tâche est marquée comme étant terminée, la tâche de publication est exécutée sur chaque nœud dans le pool qui a exécuté au moins une tâche. Vous marquez une tâche comme étant achevée par l’émission d’une demande de terminaison. Le service de traitement par lots définit l’état de la tâche à la *fin*, met fin à des tâches actives ou en cours d’exécution, associés à la tâche, puis exécute la tâche de mise. La tâche déplace ensuite vers l’état *terminé* .

> [AZURE.NOTE] Suppression de la tâche exécute également la tâche de publication. Toutefois, si un travail a déjà été arrêté, la tâche lancement n'est pas exécuter une seconde fois si le travail est supprimé ultérieurement.

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Préparation du travail et de tâches avec .NET de lot

Pour utiliser une tâche de préparation du travail, affecter une [JobPreparationTask] [ net_job_prep] objet [CloudJob.JobPreparationTask de votre travail] [ net_job_prep_cloudjob] propriété. De même, initialiser un [JobReleaseTask] [ net_job_release] et l’affecter à [CloudJob.JobReleaseTask de votre travail] [ net_job_prep_cloudjob] propriété pour définir la version de la tâche.

Dans cet extrait de code, `myBatchClient` est une instance de [BatchClient][net_batch_client], et `myPool` est un pool existant dans le compte de traitement par lots.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobPreparationTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Comme mentionné précédemment, la tâche de publication est exécutée lorsqu’un travail est terminé ou supprimé. Terminer un travail avec [JobOperations.TerminateJobAsync][net_job_terminate]. Suppression d’un travail avec [JobOperations.DeleteJobAsync][net_job_delete]. En général, vous arrêter ou supprimez un travail lorsque ses tâches sont terminées, ou lorsque que vous avez défini un délai d’attente a été atteint.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsy("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Exemple de code de GitHub

Pour voir la préparation de la tâche et libérer des tâches en action, consultez le [JobPrepRelease] [ job_prep_release_sample] exemple de projet sur GitHub. Cette application console effectue les opérations suivantes :

1. Crée un pool avec deux nœuds « small ».
2. Crée un travail de préparation du travail, version et tâches standard.
3. Exécute la tâche Préparation du travail, qui écrit d’abord l’ID de nœud dans un fichier texte dans le répertoire de « partagé » d’un nœud.
4. Exécute une tâche sur chaque nœud qui écrit son ID de tâche dans le même fichier texte.
5. Une fois toutes les tâches sont terminées (ou que le délai d’expiration est atteint), affiche le contenu du fichier de texte de chaque nœud dans la console.
6. Lorsque la tâche est terminée, s’exécute la tâche de publication pour supprimer le fichier à partir du nœud.
6. Imprime les codes de sortie des tâches de préparation et de la version de projet pour chaque nœud sur lequel ils exécutée.
7. Suspend l’exécution à autoriser la confirmation de la suppression de travail et/ou de pool.

Sortie de l’exemple d’application est semblable à la suivante :

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] En raison du temps de création et de démarrage variable de nœuds dans un nouveau pool (certains nœuds sont prêts pour les tâches avant les autres), vous pouvez voir une sortie différente. En particulier, étant donné que les tâches s’achèvent rapidement, un des nœuds de la grappe peut exécuter toutes les tâches de la tâche. Si cela se produit, vous remarquerez que le travail de préparation et des tâches de publication n’existent pas pour le nœud qui n’exécute aucune tâche.

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Vérifier la préparation du projet et les tâches de libération dans le portail Azure

Lorsque vous exécutez l’exemple d’application, vous pouvez utiliser le [portail Azure] [ portal] pour afficher les propriétés de la tâche et ses tâches ou même télécharger le fichier texte partagé qui est modifié par les tâches de la tâche.

La capture d’écran ci-dessous montre la **lame de tâches de préparation** dans le portail Azure après une exécution de l’exemple d’application. Accédez aux propriétés du *JobPrepReleaseSampleJob* une fois les tâches terminées (mais avant de le supprimer de votre projet et le pool) et cliquez sur les **tâches de préparation** ou **version** pour afficher leurs propriétés.

![Propriétés de préparation du travail dans Azure portal][1]

## <a name="next-steps"></a>Étapes suivantes

### <a name="application-packages"></a>Packages d’applications

En plus de la tâche de préparation, vous pouvez également utiliser la fonctionnalité [packages d’applications](batch-application-packages.md) de traitement par lots pour préparer des nœuds de calcul pour l’exécution de la tâche. Cette fonctionnalité est particulièrement utile pour déployer des applications qui ne requièrent pas l’exécution d’un programme d’installation, les applications qui contiennent beaucoup de fichiers (100) ou applications qui nécessitent le contrôle de version strict.

### <a name="installing-applications-and-staging-data"></a>L’installation d’applications et de données de la zone de transit

Cette publication de forum MSDN fournit une vue d’ensemble de plusieurs méthodes de préparation de vos nœuds pour l’exécution des tâches :

[L’installation d’applications et de mise en attente de traitement par lots des nœuds de calcul][forum_post]

Écrit par un des membres de l’équipe du lot d’Azure, il traite de plusieurs techniques que vous pouvez utiliser pour déployer des applications et des données pour les nœuds de calcul.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
