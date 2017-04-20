<properties
    pageTitle="Exécuter des applications de MPI dans Azure lot avec les tâches de plusieurs instances | Microsoft Azure"
    description="Apprenez à exécuter des applications d’Interface MPI (Message Passing) à l’aide du type de tâche contenant plusieurs instances dans Azure lot."
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
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-azure-batch"></a>Utilisez les tâches de plusieurs instances pour exécuter des applications d’Interface MPI (Message Passing) dans le traitement par lots d’Azure

Les tâches de plusieurs instances vous autorisent à exécuter une tâche de traitement par lots d’Azure sur plusieurs nœuds de calcul simultanément. Ces tâches permettent de scénarios tels que des applications d’Interface MPI (Message Passing) dans le traitement par lots à haute performance. Dans cet article, vous apprendrez comment exécuter les tâches de plusieurs instances utilisant le [Traitement par lots .NET] [ api_net] bibliothèque.

>[AZURE.NOTE] Bien que les exemples dans cet article reposent sur lot .NET, MS-MPI, et les nœuds de calcul Windows, les concepts de tâche de plusieurs instances présentées ici sont applicables à d’autres plates-formes et technologies (et relier les Python MPI Intel sur des nœuds de Linux, par exemple).

## <a name="multi-instance-task-overview"></a>Vue d’ensemble des tâches de plusieurs instances

Dans lot, chaque tâche est normalement exécuté sur un nœud de calcul unique--vous envoyez plusieurs tâches à un projet, et le service de lot planifie chaque tâche pour une exécution sur un nœud. Toutefois, en configurant les **paramètres de plusieurs instances**d’une tâche, vous indiquez le lot au lieu de cela créer une tâche principale et plusieurs tâches subordonnées qui sont ensuite exécutées sur plusieurs nœuds.

![Vue d’ensemble des tâches de plusieurs instances][1]

Lorsque vous envoyez une tâche avec les paramètres de plusieurs instances d’un travail, lot effectue plusieurs étapes uniques aux tâches d’instances multiples :

1. Le service de traitement par lots crée un **principal** et plusieurs **tâches subordonnées** basé sur les paramètres de plusieurs instances. Le nombre total de tâches (principales ainsi que toutes les tâches subordonnées) correspond au nombre d' **instances** (nœuds de calcul) vous spécifiez dans les paramètres de plusieurs instances.
1. Lot désigne l’un des nœuds de calcul en tant que **maître**et planifie la première tâche à exécuter sur le masque. Il planifie les tâches subordonnées à exécuter sur les autres nœuds de calcul affectée à la tâche de plusieurs instances, une sous-tâche par nœud.
1. Le serveur principal et toutes les tâches subordonnées téléchargement des **fichiers de ressources communs** que vous spécifiez dans les paramètres de plusieurs instances.
1. Une fois la ressource commune les fichiers ont été téléchargés, le principal et les tâches subordonnées d’exécuter la **commande de coordination** que vous spécifiez dans les paramètres de plusieurs instances. La commande de coordination est généralement utilisée pour préparer des nœuds pour l’exécution de la tâche. Cela peut inclure le démarrage des services d’arrière-plan (par exemple [Microsoft MPI][msmpi_msdn]de `smpd.exe`) et en vérifiant que les nœuds sont prêts à traiter les messages de nœuds entre.
1. La principale tâche exécute la **commande de l’application** sur le nœud maître *après* que la commande de coordination a été correctement effectuée par le serveur principal et de toutes les tâches subordonnées. La commande de l’application est la ligne de commande de la tâche de plusieurs instances et est exécutée uniquement par la tâche principale. Dans un [MS-MPI][msmpi_msdn]-en fonction de la solution, c’est lorsque vous exécutez votre application MPI activée à l’aide de `mpiexec.exe`.

> [AZURE.NOTE] Bien qu’il soit fonctionnellement distincte, la tâche « multi-instance » n’est pas un type de tâche unique comme la [tâche de début] [ net_starttask] ou [JobPreparationTask][net_jobprep]. La tâche de plusieurs instances est simplement une tâche de traitement par lots standard ([CloudTask] [ net_task] dans le traitement par lots .NET) dont les paramètres de plusieurs instances ont été configurés. Dans cet article, nous faisons référence à ce que la **tâche de plusieurs instances**.

## <a name="requirements-for-multi-instance-tasks"></a>Conditions requises pour les tâches de plusieurs instances

Tâches des instances multiples nécessitent un pool avec la **communication inter-nœud activée**et **désactivée l’exécution de tâches simultanées**. Si vous essayez d’exécuter une tâche plusieurs instances dans un pool avec la communication entre les nœuds désactivée, ou avec un *maxTasksPerNode* de valeur supérieure à 1, la tâche n’est pas planifiée, elle reste indéfiniment dans l’état « actif ». Cet extrait de code illustre la création d’un pool de ce type à l’aide de la bibliothèque .NET de traitement par lots.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicated: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

En outre, les tâches de plusieurs instances peuvent s’exécuter *uniquement* sur les nœuds dans **des pools créés après le 14 décembre 2015**.

### <a name="use-a-starttask-to-install-mpi"></a>Une tâche de début permet d’installer des MPI

Pour exécuter des applications MPI avec une tâche de plusieurs instances, vous devez d’abord installer une implémentation MPI (MS-MPI ou MPI Intel, par exemple) sur les nœuds de calcul dans le pool. Il est judicieux d’utiliser une [tâche de début][net_starttask], qui s’exécute chaque fois qu’un nœud joint à un pool, ou est redémarré. Cet extrait de code crée une tâche de début qui spécifie le package d’installation de MS-MPI en tant que [fichier de ressources][net_resourcefile]. Ligne de commande de cette tâche est exécutée après avoir téléchargé le fichier de ressources pour le nœud. Dans ce cas, la ligne de commande exécute une installation sans assistance de MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Accès direct à la mémoire à distance (RDMA)

Lorsque vous choisissez une [taille compatible RDMA](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) comme A9 pour les nœuds de calcul dans le pool de traitement par lots, votre application MPI peut profiter du réseau d’accès (RDMA) d’Azure hautes performances et à faible latence à distance direct à la mémoire.

Recherchez la taille spécifiée en tant que « RDMA capable » dans les articles suivants :

* Pools de **CloudServiceConfiguration**

  * [Tailles des Services en nuage](../cloud-services/cloud-services-sizes-specs.md) (Windows uniquement)

* Pools de **VirtualMachineConfiguration**

  * [Tailles des ordinateurs virtuels dans Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux)

  * [Tailles des ordinateurs virtuels dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows)

>[AZURE.NOTE] Pour tirer parti de RDMA sur les [nœuds de calcul Linux](batch-linux-nodes.md), vous devez utiliser **Intel MPI** sur les nœuds. Pour plus d’informations sur les pools de CloudServiceConfiguration et VirtualMachineConfiguration, consultez la section [Pool](batch-api-basics.md#Pool) de la vue d’ensemble de la fonctionnalité par lots.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Créer une tâche contenant plusieurs instances avec .NET de lot

Maintenant que nous avons abordé les exigences du pool et installation du package de MPI, nous allons créer la tâche de plusieurs instances. Dans cet extrait, nous allons créer un standard [CloudTask][net_task], puis configurer ses [MultiInstanceSettings] [ net_multiinstance_prop] propriété. Comme mentionné précédemment, la tâche d’instances multiples n’est pas un type de tâche distinctes, mais une tâche de traitement par lots standard configuré avec les paramètres de plusieurs instances.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Les tâches subordonnées et les tâches principales

Lorsque vous créez les paramètres de plusieurs instances d’une tâche, vous spécifiez le nombre de nœuds de calcul que vous souhaitez exécuter la tâche. Lorsque vous soumettez un travail de la tâche, le service de traitement par lots crée une tâche **principale** et suffisamment **tâches subordonnées** qui ensemble correspond au nombre de nœuds que vous avez spécifié.

Ces tâches sont affectées à un id d’entier dans la plage de 0 à *numberOfInstances* - 1. La tâche avec l’id 0 est la tâche principale, et tous les autres codes sont des tâches subordonnées. Par exemple, si vous créez les paramètres suivants de plusieurs instances d’une tâche, la tâche principale aurait un id 0, et les tâches subordonnées aurait ID 1 à 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nœud maître

Lorsque vous envoyez une tâche à plusieurs instances, le service de lot désigne l’un des nœuds de calcul en tant que le nœud « maître » et planifie la tâche principale à exécuter sur le nœud principal. Les tâches subordonnées sont planifiées pour s’exécuter sur le reste des nœuds affectée à la tâche de plusieurs instances.

## <a name="coordination-command"></a>Commande de coordination

La **commande de coordination** est exécutée par les deux principales et des tâches subordonnées.

L’appel de la commande de coordination bloque--lot n’exécute pas la commande de l’application jusqu'à ce que la commande de coordination a retourné avec succès pour toutes les tâches subordonnées. La commande de coordination doit donc démarrer tous les services requis, vérifiez qu’elles sont prêtes à l’emploi et quittez. Par exemple, cette commande de coordination pour une solution à l’aide de la version de MS-MPI 7 démarre le service SMPD sur le nœud, puis se ferme :

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Notez l’utilisation de `start` dans cette commande de coordination. Cela est nécessaire car le `smpd.exe` application ne retourne pas immédiatement après l’exécution. Sans l’utilisation de la [Démarrer] [ cmd_start] de commande, cette commande de coordination ne retourne pas et par conséquent risque de bloquer la commande de l’application de s’exécuter.

## <a name="application-command"></a>Commande de l’application

Une fois la tâche principale et toutes les tâches subordonnées en exécutant la commande de coordination, ligne de commande de la tâche de plusieurs instances est exécuté par la tâche principale *uniquement*. Ce que nous appelons la **commande de l’application** pour le distinguer de la commande de coordination.

Pour les applications MS-MPI, utilisez la commande de l’application pour exécuter votre application MPI compatible avec `mpiexec.exe`. Par exemple, voici une commande d’application pour une solution à l’aide de la version 7 de MS-MPI :

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

>[AZURE.NOTE] Dans la mesure où MS-MPI `mpiexec.exe` utilise le `CCP_NODES` variable par défaut (voir [variables d’environnement](#environment-variables)) l’exemple de ligne de commande d’application ci-dessus exclut.

## <a name="environment-variables"></a>Variables d’environnement

Traitement par lots crée plusieurs [variables d’environnement] [ msdn_env_var] spécifiques à des tâches de plusieurs instances sur les nœuds de calcul affectées à une tâche de plusieurs instances. Les lignes de commande de coordination et l’application peut référencer ces variables d’environnement, comme les scripts et les programmes qu’ils exécutent.

Les variables d’environnement suivantes sont créées par le service de traitement par lots pour une utilisation par les tâches de plusieurs instances :

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Pour plus de détails sur ces questions et les autres commandes compute nœud variables d’environnement, y compris leur contenu et leur visibilité, voir [variables d’environnement de nœud de calcul][msdn_env_var].

>[AZURE.TIP] L’exemple de code de lot Linux MPI contient un exemple de la façon dont plusieurs de ces variables d’environnement peuvent être utilisées. La [coordination-cmd] [ coord_cmd_example] Bash script télécharge l’application courante et entrée des fichiers à partir du stockage Azure, permet un partage de système de fichiers réseau (NFS) sur le nœud principal et configure les autres nœuds affectées à la tâche de plusieurs instances en tant que clients NFS.

## <a name="resource-files"></a>Fichiers de ressources

Il existe deux jeux de fichiers de ressources à prendre en compte pour les tâches de plusieurs instances : *toutes les* tâches de téléchargement **des fichiers de ressources communs** (principaux et les tâches subordonnées) et les **fichiers de ressources** spécifiés pour l’instance multiples lui-même, *seule la principale* tâche télécharge la tâche.

Vous pouvez spécifier un ou plusieurs **fichiers de ressources communs** dans les paramètres de plusieurs instances d’une tâche. Ces fichiers de ressources communs sont téléchargés à partir du [Stockage Azure](./../storage/storage-introduction.md) du **répertoire partagé de tâche** par le serveur principal de chaque nœud et de toutes les tâches subordonnées. Vous pouvez accéder au répertoire partagé de tâche à partir de l’application et la coordination des lignes de commande à l’aide de la `AZ_BATCH_TASK_SHARED_DIR` variable d’environnement. Le `AZ_BATCH_TASK_SHARED_DIR` chemin d’accès est identique sur chaque nœud affecté à la tâche d’instances multiples, donc vous pouvez partager une commande unique de coordination entre le serveur principal et de toutes les tâches subordonnées. Traitement par lots ne partage pas de « » le répertoire dans un sens d’accès à distance, mais vous pouvez l’utiliser comme un montage ou partager le point comme déjà mentionné dans le Conseil sur les variables d’environnement.

Les fichiers de ressources que vous spécifiez pour la tâche de plusieurs instances sont téléchargés vers le répertoire de travail de la tâche, `AZ_BATCH_TASK_WORKING_DIR`, par défaut. Comme mentionné, contrairement aux fichiers de ressources communs, seule la tâche principale télécharge les fichiers de ressource spécifiés pour la tâche de plusieurs instances.

> [AZURE.IMPORTANT] Toujours utiliser les variables d’environnement `AZ_BATCH_TASK_SHARED_DIR` et `AZ_BATCH_TASK_WORKING_DIR` pour faire référence à ces répertoires dans les lignes de commande. N’essayez pas de construire les chemins d’accès manuellement.

## <a name="task-lifetime"></a>Durée de vie de tâche

La durée de vie de la tâche principale contrôle la durée de vie de la tâche entière de plusieurs instances. Lorsque le serveur principal s’arrête, toutes les tâches subordonnées sont terminées. Le code de sortie du principal est le code de sortie de la tâche et est donc utilisé pour déterminer la réussite ou l’échec de la tâche à des fins de nouvelle tentative.

Si une des tâches subordonnées échoue, quitter avec un code de retour différent de zéro, par exemple, la tâche entière multi-instance échoue. La tâche de plusieurs instances est arrêtée, puis répétée, jusqu'à sa limite de nouvelle tentative.

Lorsque vous supprimez une tâche contenant plusieurs instances, le serveur principal et toutes les tâches subordonnées sont également supprimées par le service de traitement par lots. Toutes les sous-tâches des répertoires et leurs fichiers sont supprimés à partir des nœuds de calcul, comme pour une tâche standard.

[TaskConstraints] [ net_taskconstraints] pour une tâche de plusieurs instances, comme le [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]et [RetentionTime] [ net_taskconstraint_retention] propriétés, sont honorées, qu’il s’agit d’une tâche standard, s’appliquent à la principale et toutes les tâches subordonnées. Toutefois, si vous modifiez le [RetentionTime] [ net_taskconstraint_retention] propriété après l’ajout de la tâche d’instances multiples pour le travail, cette modification est appliquée uniquement à la tâche principale. Toutes les tâches subordonnées continuent à utiliser l' original [RetentionTime][net_taskconstraint_retention].

Liste des tâches du nœud de calcul récent reflète l’id d’une tâche subordonnée si la tâche récente faisait partie d’une tâche de plusieurs instances.

## <a name="obtain-information-about-subtasks"></a>Obtenir des informations sur les tâches subordonnées

Pour obtenir des informations sur les tâches subordonnées à l’aide de la bibliothèque .NET du lot, appelez le [CloudTask.ListSubtasks] de[ net_task_listsubtasks] méthode. Cette méthode retourne des informations sur toutes les tâches subordonnées et des informations sur le nœud de calcul exécutées les tâches. À partir de ces informations, vous pouvez déterminer le répertoire racine de la tâche subordonnée, l’id du pool, son état actuel, code de sortie et bien plus encore. Vous pouvez utiliser ces informations en combinaison avec le [PoolOperations.GetNodeFile] de[ poolops_getnodefile] méthode pour obtenir les fichiers de la tâche subordonnée. Notez que cette méthode ne retourne pas d’informations de la tâche principale (id 0).

> [AZURE.NOTE] Sauf indication contraire, les méthodes .NET de traitement par lots qui fonctionnent sur les instances multiples [CloudTask] [ net_task] lui-même s’appliquent *uniquement* à la tâche principale. Par exemple, lorsque vous appelez le [CloudTask.ListNodeFiles] de[ net_task_listnodefiles] méthode sur une tâche à plusieurs instances, seuls les fichiers de la tâche principale sont renvoyés.

L’extrait de code suivant montre comment obtenir les informations des tâches subordonnées, ainsi que demander le contenu du fichier à partir des nœuds sur lesquels ils exécutée.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Exemple de code

Le [MultiInstanceTasks] de[ github_mpi] sur GitHub exemple de code montre comment utiliser une tâche de plusieurs instances d’exécuter un [MS-MPI] [ msmpi_msdn] application sur lot des nœuds de calcul. Suivez les étapes de [préparation](#preparation) et [d’exécution](#execution) pour exécuter l’exemple.

### <a name="preparation"></a>Préparation

1. Suivez les deux premières étapes de la rubrique [comment compiler et exécuter un programme MS-MPI simple][msmpi_howto]. Cela répond à la prerequesites pour l’étape suivante.
1. Générer *une version de la [MPIHelloWorld] * [ helloworld_proj] exemple de programme MPI. C’est le programme qui sera exécuté par la tâche de plusieurs instances sur les nœuds de calcul.
1. Créer un fichier zip contenant `MPIHelloWorld.exe` (qui vous avez créé l’étape 2) et `MSMpiSetup.exe` (que vous avez téléchargé l’étape 1). Vous allez télécharger ce fichier zip sous la forme d’un package d’application à l’étape suivante.
1. Utiliser le [Azure portal] [ portal] pour créer un lot d' [application](batch-application-packages.md) appelé « MPIHelloWorld » et spécifiez le fichier zip vous avez créé à l’étape précédente en tant que version « 1.0 » du package d’application. Pour plus d’informations, consultez [télécharger et gérer des applications](batch-application-packages.md#upload-and-manage-applications) .

>[AZURE.TIP] Générer *une version de* `MPIHelloWorld.exe` afin que vous n’êtes pas obligé d’inclure les dépendances supplémentaires (par exemple, `msvcp140d.dll` ou `vcruntime140d.dll`) dans le package d’application.

### <a name="execution"></a>Exécution de

1. Téléchargez les [exemples de traitement par lots azure] [ github_samples_zip] de GitHub.
1. Ouvrez la MultiInstanceTasks **solution** dans Visual Studio 2015. Le `MultiInstanceTasks.sln` fichier de solution se trouve dans :

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`

1. Entrez vos informations d’identification compte de lot et de stockage de `AccountSettings.settings` dans le projet **Microsoft.Azure.Batch.Samples.Common** .
1. **Générer et exécuter** la solution de MultiInstanceTasks pour exécuter le MPI exemple d’application sur les nœuds de calcul dans un pool de traitement par lots.
1. *Facultatif*: utiliser le [portail Azure] [ portal] ou de l' [Explorateur de lot] [ batch_explorer] pour examiner le pool d’exemples, de travaux et tâche (« MultiInstanceSamplePool », « MultiInstanceSampleJob », « MultiInstanceSampleTask ») avant de suppriment les ressources.

>[AZURE.TIP] Vous pouvez télécharger [Visual Studio Communauté] [ visual_studio] gratuitement si vous ne disposez pas de Visual Studio.

Sortie de `MultiInstanceTasks.exe` est similaire à la suivante :

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Étapes suivantes

- Le blog Microsoft HPC & Azure lot équipe traite des [MPI prise en charge de Linux sur Azure lot][blog_mpi_linux]et inclut des informations sur l’utilisation de [OpenFOAM] [ openfoam] avec le traitement par lots. Vous trouverez des exemples de code Python pour obtenir un [exemple de OpenFOAM de GitHub][github_mpi].

- Découvrez comment [créer des pools de nœuds de calcul Linux](batch-linux-nodes.md) pour les utiliser dans vos solutions Azure lot MPI.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Vue d’ensemble de plusieurs instances"
