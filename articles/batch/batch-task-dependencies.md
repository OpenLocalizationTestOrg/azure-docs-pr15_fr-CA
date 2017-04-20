<properties
    pageTitle="Interdépendances dans Azure lot | Microsoft Azure"
    description="Créer des tâches qui dépendent de l’achèvement réussi d’autres tâches de traitement MapReduce style et des données similaires dans Azure lot des charges de travail."
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
    ms.date="09/28/2016"
    ms.author="marsma" />

# <a name="task-dependencies-in-azure-batch"></a>Interdépendances des tâches dans un lot d’Azure

La fonction de dépendance de tâche de traitement par lots d’Azure convient parfaitement si vous souhaitez traiter :

- MapReduce charges de travail dans le cloud.
- Travaux de traitement de données dont les tâches peuvent être exprimées sous la forme d’un graphique acyclique dirigé (DAG).
- Tout travail dans lequel les tâches en aval dépendent de la sortie des tâches en amont.

Interdépendances des tâches par lots permettent de créer des tâches qui sont planifiées pour une exécution sur les nœuds de calcul uniquement après la réussite d’un ou plusieurs autres tâches. Par exemple, vous pouvez créer un travail qui rend chaque image d’un film 3D avec des tâches distinctes, en parallèles. La dernière tâche--la « tâche de fusion »--fusionne les images rendues dans la vidéo complète qu’après que toutes les images ont été correctement rendus.

Vous pouvez créer des tâches qui dépendent d’autres tâches dans une relation un à un ou un-à-plusieurs. Vous pouvez même créer une dépendance de la plage dans laquelle une tâche dépend de l’achèvement réussi d’un groupe de tâches au sein d’une plage d’ID de tâche spécifique. Vous pouvez combiner ces trois scénarios de base pour créer des relations plusieurs-à-plusieurs.

## <a name="task-dependencies-with-batch-net"></a>Interdépendances des tâches par lot .NET

Dans cet article, nous verrons comment configurer des interdépendances des tâches en utilisant le [Traitement par lots .NET] [ net_msdn] bibliothèque. Nous avons tout d’abord de vous montrer comment [Activer l’interdépendance des tâches](#enable-task-dependencies) dans vos projets, puis montrez comment [configurer une tâche avec des dépendances](#create-dependent-tasks). Enfin, nous évoquerons les [scénarios de dépendance](#dependency-scenarios) qui prend en charge le traitement par lots.

## <a name="enable-task-dependencies"></a>Activer les interdépendances des tâches

Pour utiliser les interdépendances de tâches dans votre application de traitement par lots, vous devez tout d’abord indiquer au service de lot que la tâche utilise les interdépendances des tâches. Dans .NET de lot, l’activer sur vos [CloudJob] [ net_cloudjob] en définissant sa [UsesTaskDependencies] [ net_usestaskdependencies] propriété `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

L’extrait de code précédent, « batchClient » est une instance de la [BatchClient] [ net_batchclient] classe.

## <a name="create-dependent-tasks"></a>Créer des tâches dépendantes

Pour créer une tâche qui dépend de l’achèvement réussi d’une ou plusieurs tâches, vous indiquez lot que la tâche « dépend » les autres tâches. Dans .NET de lot, configurer le [CloudTask][net_cloudtask]. [DependsOn] [net_dependson] propriété avec une instance de l' [TaskDependencies] [ net_taskdependencies] classe :

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Cet extrait de code crée une tâche avec l’ID « Fleurs » et qui est programmé pour s’exécuter sur un nœud de calcul uniquement une fois que les tâches avec les ID des « Rain » et « Sun » s’est terminé avec succès.

 > [AZURE.NOTE] Une tâche est considéré comme étant terminé lorsqu’il est dans l’état **terminé** et son **code de sortie** est `0`. Dans le traitement par lots .NET, cela signifie une [CloudTask][net_cloudtask]. [État] [net_taskstate] valeur de la propriété `Completed` et de la CloudTask [TaskExecutionInformation][net_taskexecutioninformation]. [Code de sortie] [net_exitcode] est de valeur de la propriété `0`.

## <a name="dependency-scenarios"></a>Scénarios de dépendance

Il existe trois scénarios de dépendance de tâche de base que vous pouvez utiliser dans le traitement par lots d’Azure : un vers un, un-à-plusieurs et l’ID de tâche plage de dépendance. Elles peuvent être combinées pour fournir un quatrième scénario, plusieurs-à-plusieurs.

 Scénario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemple | |
 :-------------------: | ------------------- | -------------------
 [Un à un](#one-to-one) | *taskB* dépend de *taskA* <p/> *taskB* ne sera pas planifié pour l’exécution jusqu'à ce que *taskA* s’est correctement terminée | ![Diagramme : l’interdépendance des tâches un à un][1]
 [Un-à-plusieurs](#one-to-many) | *taskC* dépend de *taskA* et de *taskB* <p/> *taskC* ne sera pas planifié pour l’exécution jusqu'à ce que les *taskA* et *taskB* s’est terminé avec succès | ![Diagramme : dépendance de tâche de type un-à-plusieurs][2]
 [Plage d’ID de tâche](#task-id-range) | *taskD* dépend d’une gamme de tâches <p/> *taskD* ne sera pas planifié pour l’exécution jusqu'à ce que les tâches avec l’ID *1* à *10* s’est terminé avec succès | ![Diagramme : Dépendance de plage d’id de tâche][3]

>[AZURE.TIP] Vous pouvez créer des relations **plusieurs-à-plusieurs** , par exemple où les tâches C, D, E et F chaque dépendent des tâches A et B. Cela est utile, par exemple, dans les scénarios de prétraitement parallélisées où vos tâches en aval dépendent de la sortie de plusieurs tâches en amont.

### <a name="one-to-one"></a>Un à un

Pour créer une tâche qui a une dépendance sur l’achèvement d’une autre tâche, vous fournissez un ID de tâche unique à la [TaskDependencies][net_taskdependencies]. [OnId] [net_onid] une méthode statique lorsque vous remplissez la [DependsOn] [ net_dependson] propriété du [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Un-à-plusieurs

Pour créer une tâche qui a une dépendance sur la réalisation de plusieurs tâches, vous fournissez une collection d’ID pour le [TaskDependencies]de tâche[net_taskdependencies]. [OnIds] [net_onids] une méthode statique lorsque vous remplissez la [DependsOn] [ net_dependson] propriété du [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Plage d’ID de tâche

Pour créer une tâche qui a une dépendance sur la réussite d’un groupe de tâches dont lie ID dans une plage, vous fournissez le premier et dernier ID de la tâche dans la plage à la [TaskDependencies][net_taskdependencies]. [OnIdRange] [net_onidrange] une méthode statique lorsque vous remplissez la [DependsOn] [ net_dependson] propriété du [CloudTask][net_cloudtask].

>[AZURE.IMPORTANT] Lorsque vous utilisez des plages d’ID de tâche pour vos dépendances, l’ID de tâche dans la plage *doivent* être des représentations de chaîne des valeurs entières. En outre, chaque tâche dans la plage doit se termine correctement pour être planifié pour l’exécution de la tâche dépendante.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="code-sample"></a>Exemple de code

Le [TaskDependencies] [ github_taskdependencies] exemple de projet est un des [exemples de code de lot d’Azure] [ github_samples] sur GitHub. Cette solution Visual Studio 2015 montre comment activer la dépendance de tâche d’un projet, créer des tâches qui dépendent d’autres tâches et exécuter ces tâches sur un ensemble de nœuds de calcul.

## <a name="next-steps"></a>Étapes suivantes

### <a name="application-deployment"></a>Déploiement d’applications

La fonctionnalité [packages d’applications](batch-application-packages.md) de traitement par lots offre un moyen facile pour les déployer et les applications que vos tâches s’exécutent sur les nœuds de calcul.

### <a name="installing-applications-and-staging-data"></a>L’installation d’applications et de données de la zone de transit

Découvrez les [applications d’installation et de mise en attente des données sur le traitement par lots des nœuds de calcul] [ forum_post] publier sur le forum du lot d’Azure pour une vue d’ensemble des différentes méthodes pour préparer vos nœuds pour exécuter des tâches. Écrit par un des membres de l’équipe du lot d’Azure, cette annonce est une bonne introduction sur les différentes façons d’obtenir des fichiers (y compris les applications et les données d’entrée des tâches) sur vos nœuds de calcul.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagramme : dépendance d’un à un"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagramme : dépendance de type un-à-plusieurs"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagramme : dépendance de plage d’id de tâche"
