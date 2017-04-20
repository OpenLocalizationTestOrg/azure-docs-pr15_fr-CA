<properties
    pageTitle="Persistance dans Azure lot de sortie de projet et la tâche | Microsoft Azure"
    description="Apprenez à utiliser le stockage Azure comme un magasin durable pour votre tâche de traitement par lots et le travail de sortie et activer l’affichage de cette sortie persistante dans le portail Azure."
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
    ms.date="09/07/2016"
    ms.author="marsma" />

# <a name="persist-azure-batch-job-and-task-output"></a>Conserver la sortie de projet et la tâche de traitement par lots d’Azure

Les tâches que vous exécutez généralement dans le traitement par lots génèrent une sortie qui doit être stocké et récupéré ultérieurement par d’autres tâches dans la tâche, l’application cliente qui a exécuté la tâche, ou les deux. Cette sortie peut être créés par traitement d’entrée de données de fichiers ou fichiers journaux associés à l’exécution de la tâche. Cet article présente une bibliothèque de classes .NET qui utilise une technique basée sur les conventions pour conserver cette production de tâche vers le stockage Azure Blob, rendant disponible même après avoir supprimer vos pools, tâches et les nœuds de calcul.

À l’aide de la technique dans cet article, vous pouvez également afficher la sortie de la tâche dans **les fichiers de sortie enregistré** et **les journaux enregistrés** dans [Azure portal][portal].

![Les fichiers de sortie enregistré et sélecteurs de journaux enregistrées dans le portail][1]

>[AZURE.NOTE] Les [Conventions des fichiers par lots Azure] [ nuget_package] bibliothèque de classes .NET abordée dans cet article est en cours d’aperçu. Certaines des fonctionnalités décrites ici peuvent changer avant la disponibilité générale.

## <a name="task-output-considerations"></a>Considérations relatives à la tâche sortie

Lorsque vous concevez votre solution de traitement par lots, vous devez considérer plusieurs facteurs liées à des sorties de projet et la tâche.

* **Calculer la durée de vie de nœud**: calculer les nœuds sont souvent transitoires, en particulier dans des pools d’échelle automatique activée. Les sorties des tâches qui s’exécutent sur un nœud ne sont disponibles que lorsque le nœud existe, et uniquement dans le temps de rétention du fichier que vous avez définie pour la tâche. Pour assurer la préservation de la sortie de la tâche, les vos tâches doivent donc télécharger leurs fichiers de sortie pour un magasin durable, par exemple, le stockage Azure.

* **Stockage de la sortie**: pour conserver les données de sortie de tâche dans le stockage durable, vous pouvez utiliser le [Kit de développement de stockage Azure](../storage/storage-dotnet-how-to-use-blobs.md) dans votre code de tâche pour télécharger de la sortie de la tâche à un conteneur de stockage Blob. Si vous implémentez un conteneur et une convention de dénomination de fichier, votre application cliente ou autres tâches dans le projet peuvent ensuite, recherchez et téléchargez cette sortie en fonction de la convention.

* **Récupération de la sortie**: vous pouvez récupérer sortie des tâches directement à partir de nœuds de calcul dans le pool ou stockage Azure si vos tâches persistent leur sortie. Pour récupérer la sortie d’une tâche directement à partir d’un nœud de calcul, vous devez le nom de fichier et son emplacement de sortie sur le nœud. Si vous conserver la sortie vers le stockage Azure, tâches en aval ou votre application cliente doit avoir le chemin d’accès complet au fichier dans le stockage Azure pour le télécharger à l’aide du Kit de développement de stockage Azure.

* **Affichage de sortie**: lorsque vous accédez à une tâche de traitement par lots dans le portail Azure et sélectionnez **les fichiers dans le nœud**, vous sont présentées avec tous les fichiers associés à la tâche, pas uniquement les fichiers de sortie qui vous intéresse. Encore une fois, les fichiers sur les nœuds de calcul sont disponibles uniquement lorsque le nœud existe, et uniquement dans le temps de rétention du fichier que vous avez définie pour la tâche. Pour afficher la sortie de la tâche que vous avez rendues persistantes dans le stockage Azure dans le portail ou d’une application telle que l' [Explorateur de stockage Azure][storage_explorer], vous devez connaître son emplacement et accédez directement au fichier.

## <a name="help-for-persisted-output"></a>Aide pour la sortie persistant

Pour vous aider plus facilement conserver de projet et la tâche de sortie, l’équipe du lot a défini et mis en œuvre un jeu de conventions d’affectation de noms ainsi qu’une bibliothèque de classes .NET, les [Conventions des fichiers par lots Azure] [ nuget_package] bibliothèque, que vous pouvez utiliser dans vos applications de traitement par lots. En outre, le portail Azure est conscient de ces conventions d’affectation de noms afin que vous pouvez trouver facilement les fichiers que vous avez enregistrées à l’aide de la bibliothèque.

## <a name="using-the-file-conventions-library"></a>À l’aide de la bibliothèque de fichiers de conventions

[Conventions des fichiers par lots Azure] [ nuget_package] est une bibliothèque de classes .NET que vos applications .NET de traitement par lots peuvent utiliser pour stocker et récupérer des sorties de tâche vers et depuis le stockage Azure. Elle est conçue dans le code de tâche et client--dans le code de tâche pour les fichiers de persistance et dans le code client à la liste et de les récupérer. Vos tâches peuvent également utiliser la bibliothèque pour la récupération des sorties des tâches en amont, comme dans un scénario [d’interdépendances de tâches](batch-task-dependencies.md) .

Prend en charge la bibliothèque de conventions permettant de garantir que les tâches et conteneurs de stockage sortie fichiers sont nommées selon la convention et sont téléchargés vers le bon endroit lorsque rendues persistantes dans le stockage Azure. Lorsque vous récupérez des sorties, vous retrouver facilement les sorties pour une tâche donnée ou d’une tâche en liste ou en récupérant les sorties par ID et leur objectif, sans avoir à connaître les noms de fichier ou lorsqu’il existe dans le stockage.

Par exemple, vous pouvez utiliser la bibliothèque « liste tous les fichiers intermédiaires pour la tâche 7 » ou « me l’aperçu miniature pour travail *mymovie*, » sans avoir à connaître les noms de fichier ou un emplacement dans votre compte de stockage.

### <a name="get-the-library"></a>Obtenir la bibliothèque

Vous pouvez obtenir la bibliothèque qui contient de nouvelles classes et étend la [CloudJob] [ net_cloudjob] et [CloudTask] [ net_cloudtask] classes avec de nouvelles méthodes, à partir de [NuGet][nuget_package]. Vous pouvez l’ajouter à votre projet Visual Studio à l’aide de la [De Gestionnaire de package NuGet bibliothèque][nuget_manager].

>[AZURE.TIP] Vous pouvez trouver le [code source] de[ github_file_conventions] de la bibliothèque de Conventions des fichiers par lots Azure sur GitHub dans le Kit de développement Microsoft Azure pour le référentiel de .NET.

## <a name="requirement-linked-storage-account"></a>Requise : compte de stockage

Pour stocker les sorties dans le stockage durable à l’aide de la bibliothèque de fichiers de conventions et de les afficher dans le portail Azure, vous devez [lier un compte de stockage Azure](batch-application-packages.md#link-a-storage-account) à votre compte de traitement par lots. Si vous ne l’avez pas déjà, vous pouvez lier un compte de stockage à votre compte de lot à l’aide du portail Azure :

Lame de **compte de traitement par lots** > **paramètres** > **Compte de stockage** > **Compte de stockage** (aucune) > sélectionnez un compte de stockage de votre abonnement

Pour une vue d’ensemble plus détaillée sur la liaison d’un compte de stockage, consultez [déploiement d’applications avec des packages d’application Azure lot](batch-application-packages.md).

## <a name="persist-output"></a>Conserver la sortie

Il existe deux actions principales à effectuer lors de l’enregistrement de la sortie de projet et la tâche avec la bibliothèque de fichiers de conventions : créer le conteneur de stockage et d’enregistrer le résultat dans le conteneur.

>[AZURE.WARNING] Toutes les sorties de projet et la tâche sont stockées dans le même conteneur, [limites de limitation du stockage](../storage/storage-performance-checklist.md#blobs) peut être appliquée si vous essayez de conserver des fichiers en même temps un grand nombre de tâches.

### <a name="create-storage-container"></a>Créer le conteneur de stockage

Avant de commencent à vos tâches de sortie persistante dans le stockage, vous devez créer un conteneur de stockage blob auquel ils télécharge leur sortie. Pour ce faire, l’appel de [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync] [net_prepareoutputasync]. Cette méthode d’extension est un [CloudStorageAccount] de[ net_cloudstorageaccount] de l’objet en tant que paramètre et crée un conteneur nommé de manière à ce que son contenu est détectable par le portail Azure et les méthodes de récupération décrites plus loin dans l’article.

En général, vous placez ce code dans votre application cliente--l’application qui crée des pools, des projets et des tâches.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Stocker les sorties de tâche

Maintenant que vous avez préparé un conteneur dans le stockage blob, tâches peuvent enregistrer la sortie vers le conteneur à l’aide de la [TaskOutputStorage] [ net_taskoutputstorage] classe trouvée dans la bibliothèque de fichiers de conventions.

Dans le code de la tâche, commencez par créer un [TaskOutputStorage] [ net_taskoutputstorage] objet, puis lorsque la tâche a terminé son travail, appelez le [TaskOutputStorage][net_taskoutputstorage]. [SaveAsync] [net_saveasync] méthode enregistrer sa sortie vers un stockage Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

Le paramètre « type de sortie » pour classer les fichiers persistants. Il existe quatre prédéfinies [TaskOutputKind] [ net_taskoutputkind] les types : « TaskOutput », « TaskPreview », « TaskLog » et « TaskIntermediate ». Vous pouvez également définir des types personnalisés si ils seraient utiles dans votre flux de travail.

Ces types de sortie permettent de spécifier le type de sortie à la liste lorsque vous interrogez par la suite par lots pour les sorties persistants d’une tâche donnée. En d’autres termes, lorsque vous mettez les sorties d’une tâche, vous pouvez filtrer la liste sur l’un des types de sortie. Par exemple, « me donner la sortie de *l’Aperçu* de la tâche, *109*. » Plus sur la liste et la récupération des sorties apparaît dans [sortie de récupérer](#retrieve-output) ultérieurement dans cet article.

>[AZURE.TIP] Le type de sortie désigne également l’où un fichier particulier s’affiche dans le portail Azure : *TaskOutput*-fichiers classés apparaissent dans « Fichiers de sortie de tâche » et les fichiers *TaskLog* dans « Journaux des tâches ».

### <a name="store-job-outputs"></a>Stocker les sorties de tâche

Outre le stockage des sorties de tâche, vous pouvez stocker les sorties associées à l’intégralité du travail. Par exemple, dans la tâche de fusion d’un travail de rendu vidéo, vous susceptible de se maintenir le film rendu complet sous la forme d’une sortie de projet. Lorsque votre tâche est terminée, votre application cliente peut simplement répertorier et extraire les sorties du projet et n’a pas besoin d’interroger les tâches individuelles.

Stocker la sortie de la tâche en appelant le [JobOutputStorage][net_joboutputstorage]. [SaveAsync] [net_joboutputstorage_saveasync] méthode, et spécifiez le [JobOutputKind] [ net_joboutputkind] et le nom de fichier :

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Comme avec TaskOutputKind pour les sorties de tâche, vous utilisez la [JobOutputKind] [ net_joboutputkind] paramètre pour classer un travail de persistant des fichiers. Ce paramètre permet de requête ultérieur pour (liste) un type spécifique de sortie. Le JobOutputKind comprend les types de sortie et de visualisation et prend en charge la création de types personnalisés.

### <a name="store-task-logs"></a>Stocker les journaux des tâches

En plus de la conservation d’un fichier dans le stockage durable issue d’une tâche ou un projet, vous pourriez trouver nécessaire pour conserver les fichiers qui sont mis à jour pendant l’exécution d’une tâche : les fichiers journaux ou de `stdout.txt` et `stderr.txt`, par exemple. À cet effet, la bibliothèque de Conventions des fichiers par lots Azure fournit la [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync] [net_savetrackedasync] méthode. Avec [SaveTrackedAsync][net_savetrackedasync], vous pouvez effectuer le suivi des mises à jour dans un fichier sur le nœud (à un intervalle que vous spécifiez) et rendre persistantes les mises à jour pour le stockage Azure.

Dans l’extrait de code suivant, nous utilisons [SaveTrackedAsync] [ net_savetrackedasync] mise à jour `stdout.txt` dans le stockage Azure toutes les 15 secondes lors de l’exécution de la tâche :

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
    await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)`est simplement un espace réservé pour le code qui effectue normalement votre tâche. Par exemple, vous pouvez avoir code qui télécharge des données de stockage Azure et effectue une transformation ou un calcul sur elle. La partie importante de cet extrait de code est montrant comment vous pouvez placer ce code dans un `using` bloc de mettre régulièrement à jour un fichier avec [SaveTrackedAsync][net_savetrackedasync].

Le `Task.Delay` est nécessaire à la fin de ce `using` bloc pour garantir que l’agent de nœud ait le temps de vider le contenu de la sortie standard vers le fichier stdout.txt sur le nœud (l’agent de nœud est un programme qui s’exécute sur chaque nœud dans le pool et fournit l’interface de commande et de contrôle entre le nœud et le service de traitement par lots). Sans ce délai, il est possible le risque de perdre les dernières secondes de la sortie. Ce délai n’est peut-être pas nécessaire pour tous les fichiers.

>[AZURE.NOTE] Lorsque vous activez le suivi avec SaveTrackedAsync de fichier, uniquement *ajoute* au fichier de suivi sont rendues persistantes dans le stockage Azure. Utilisez cette méthode uniquement pour les non-rotation des fichiers journaux ou autres fichiers qui sont ajoutés, autrement dit, les données de suivi est ajoutée uniquement à la fin du fichier lorsqu’il est mis à jour.

## <a name="retrieve-output"></a>Récupérer la sortie

Lorsque vous récupérez votre sortie persistant à l’aide de la bibliothèque de Conventions des fichiers par lots Azure, vous le faire de façon centrée sur la tâche et à la tâche. Vous pouvez demander la sortie pour tâche ou le travail sans avoir à connaître son chemin d’accès de stockage de blob, ou même son nom de fichier. Vous pouvez simplement dire, « Obtenir des fichiers de sortie de la tâche, *109*. »

L’extrait de code suivant itère au sein de toutes les tâches d’un travail, imprime certaines informations sur les fichiers de sortie de la tâche et puis télécharge les fichiers à partir du stockage.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>Sorties de tâche et le portail Azure

Le portail Azure affiche les sorties de tâche et les journaux qui sont conservées dans un compte de stockage Azure lié en utilisant les conventions d’attribution de noms trouvées dans le [README de Conventions de fichier de lot Azure][github_file_conventions_readme]. Vous pouvez implémenter vous-même ces conventions dans une langue de votre choix, ou vous pouvez utiliser la bibliothèque de conventions des fichiers dans vos applications .NET.

### <a name="enable-portal-display"></a>Activer l’affichage du portail

Pour activer l’affichage de vos sorties dans le portail, vous devez remplir les conditions suivantes :

 1. [Lier un compte de stockage Azure](#requirement-linked-storage-account) à votre compte de traitement par lots.
 2. Respecter les conventions de dénomination prédéfinies pour les conteneurs de stockage et de fichiers lors de la persistance des sorties. Vous trouverez la définition de ces conventions dans la bibliothèque de conventions des fichiers [Lisez-moi][github_file_conventions_readme]. Si vous utilisez les [Conventions des fichiers par lots Azure] [ nuget_package] bibliothèque pour conserver votre sortie, cette exigence est satisfaite.

### <a name="view-outputs-in-the-portal"></a>Affichage des sorties dans le portail

Pour afficher les journaux et les sorties de tâche dans le portail Azure, accédez à la tâche dont la sortie vous intéressez, puis cliquez sur **fichiers de sortie enregistré** ou **des journaux enregistrés**. Cette image montre les **fichiers de sortie enregistrée** pour la tâche avec l’ID « 007 » :

![Lame de sorties de tâche dans le portail Azure][2]

## <a name="code-sample"></a>Exemple de code

La [PersistOutputs] [ github_persistoutputs] exemple de projet est un des [exemples de code de lot d’Azure] [ github_samples] sur GitHub. Cette solution Visual Studio 2015 montre comment utiliser la bibliothèque de Conventions des fichiers par lots Azure pour faire persister la sortie de tâche dans le stockage durable. Pour exécuter l’exemple, procédez comme suit :

1. Ouvrez le projet dans **Visual Studio 2015**.
2. Ajouter vos **informations d’identification de compte** de lot et le stockage de **AccountSettings.settings** dans le projet Microsoft.Azure.Batch.Samples.Common.
3. **Générer** (mais ne s’exécutent pas) la solution. Restaurer les packages NuGet si vous y êtes invité.
4. Le portail Azure permet de télécharger un [package d’application](batch-application-packages.md) pour **PersistOutputsTask**. Inclure la `PersistOutputsTask.exe` et ses assemblys dépendants dans le package .zip, définir l’ID de l’application à « PersistOutputsTask » et la version de package d’application « 1.0 ».
5. **Démarrer** projet de (exécuter) le **PersistOutputs** .

## <a name="next-steps"></a>Étapes suivantes

### <a name="application-deployment"></a>Déploiement d’applications

La fonctionnalité [packages d’applications](batch-application-packages.md) de traitement par lots offre un moyen facile pour les déployer et les applications que vos tâches s’exécutent sur les nœuds de calcul.

### <a name="installing-applications-and-staging-data"></a>L’installation d’applications et de données de la zone de transit

Découvrez les [applications d’installation et de mise en attente des données sur le traitement par lots des nœuds de calcul] [ forum_post] publier sur le forum du lot d’Azure pour une vue d’ensemble des différentes méthodes de préparation de vos nœuds pour l’exécution des tâches. Écrit par un des membres de l’équipe du lot d’Azure, cette annonce est une bonne introduction sur les différentes façons d’obtenir des fichiers (y compris les applications et les données d’entrée de tâche) sur vos nœuds de calcul et des considérations particulières pour chaque méthode.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Les fichiers de sortie enregistré et sélecteurs de journaux enregistrées dans le portail"
[2]: ./media/batch-task-output/task-output-02.png "Lame de sorties de tâche dans le portail Azure"