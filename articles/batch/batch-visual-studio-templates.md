<properties
    pageTitle="Les modèles Visual Studio pour le lot d’Azure | Microsoft Azure"
    description="Découvrez comment ces modèles de projet Visual Studio peuvent vous aider à implémenter et exécuter vos charges de travail intensives sur Azure lot"
    services="batch"
    documentationCenter=".net"
    authors="fayora"
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

# <a name="visual-studio-project-templates-for-azure-batch"></a>Modèles de projet Visual Studio Azure lot

Le **Gestionnaire de projet** et **les modèles Visual Studio du processeur de tâches** de traitement par lots fournissent du code pour vous aider à mettre en œuvre et exécuter vos charges de travail intensives sur le traitement par lots avec le moins d’efforts. Ce document décrit ces modèles et fournit des conseils sur la manière de les utiliser.

>[AZURE.IMPORTANT] Cet article traite uniquement des informations applicables à ces deux modèles et suppose que vous êtes familiarisé avec le service de traitement par lots et les concepts clés qui lui sont associés : pools, calculer les nœuds, les travaux et tâches, le gestionnaire tâches, variables d’environnement et autres informations pertinentes. Vous trouverez plus d’informations dans le [Lot de bases d’Azure](batch-technical-overview.md), [Présentation des fonctionnalités de traitement par lots pour les développeurs](batch-api-basics.md)et [mise en route avec la bibliothèque de commandes d’Azure pour .NET](batch-dotnet-get-started.md).

## <a name="high-level-overview"></a>Vue d’ensemble

Les modèles de Job Manager et le processeur de tâches peuvent être utilisés pour créer deux composants utiles :

* Une tâche de gestionnaire qui implémente un séparateur de travail que vous pouvez scinder une tâche en plusieurs tâches peuvent s’exécuter indépendamment, en parallèle.

* Un processeur de tâches qui peut être utilisé pour effectuer le prétraitement et le post-traitement sur une ligne de commande d’application.

Par exemple, dans un scénario d’affichage vidéo, le séparateur de travail ferait une tâche unique de film des centaines ou des milliers de tâches distinctes qui seraient traiter séparément les images individuelles. En conséquence, le processeur de tâches appelait l’application de rendu et de tous les processus dépendants qui sont nécessaires pour restituer chaque cadre, ainsi que pour effectuent toutes les actions supplémentaires (par exemple, la copie de l’image restituée à un emplacement de stockage).

>[AZURE.NOTE] Les modèles Job Manager et le processeur de tâches sont indépendantes des autres, vous pouvez choisir d’utiliser les deux, ou uniquement à un d’eux, en fonction des exigences de votre travail de calcul et de vos préférences.

Comme indiqué dans le schéma ci-dessous, un travail de calcul qui utilise ces modèles passe par trois étapes :

1. Le code de client (par exemple, application, service web, etc.) soumet une tâche au service de traitement par lots sur Azure, spécifiant comme son gestionnaire de travail le programme Gestionnaire de travail de la tâche.

2. Le service de traitement par lots s’exécute le Gestionnaire de tâches sur un nœud de calcul, et le séparateur de travail lance le nombre spécifié de tâches du processeur de tâches, sur que plusieurs nœuds de calcul en tant que de besoin, basé sur les paramètres et les spécifications dans le code de fractionnement du travail.

3. Les tâches du processeur tâche exécutent indépendamment, en parallèle, afin de traiter les données d’entrée et de générer les données de sortie.

![Schéma montrant comment le code client interagit avec le service de traitement par lots][diagram01]

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser les modèles de lot, vous devrez les éléments suivants :

* Un ordinateur avec Visual Studio 2015, ou plus récent est installé.

* Les modèles de lot, qui sont disponibles à partir de la [Galerie de Visual Studio] [ vs_gallery] en tant qu’extensions de Visual Studio. Il existe deux façons d’obtenir les modèles :

  * Installer les modèles à l’aide de la boîte de dialogue **mises à jour et des Extensions** dans Visual Studio (pour plus d’informations, consultez [recherche et à l’aide des Extensions de Visual Studio][vs_find_use_ext]). Dans la boîte de dialogue **mises à jour et des Extensions** , rechercher et télécharger les deux extensions suivantes :

    * Gestionnaire de travaux par lots Azure avec séparateur de travaux
    * Processeur de tâches de traitement par lots Azure

  * Télécharger les modèles à partir de la galerie en ligne pour Visual Studio : [Modèles de projet de Microsoft Azure lot][vs_gallery_templates]

* Si vous prévoyez d’utiliser la fonctionnalité [Packages d’Application](batch-application-packages.md) à déployer le Gestionnaire de tâches et les nœuds de calcul du processeur de tâches pour le traitement par lots, vous devez lier un compte de stockage pour votre compte de traitement par lots.

## <a name="preparation"></a>Préparation

Nous vous recommandons de créer une solution qui peut contenir votre responsable de projet, ainsi que votre processeur de tâches, car cela peut faciliter la partager du code entre votre gestionnaire de projet et les programmes de traitement de tâche. Pour créer cette solution, procédez comme suit :

1. Ouvrez Visual Studio 2015 et sélectionnez le **fichier** > **Nouveau** > **projet**.

2. Sous **modèles**, développer **d’Autres Types de projets**, cliquez sur **Solutions de Visual Studio**et puis sélectionnez **Blanc**.

3. Tapez un nom qui décrit l’objectif de cette solution (par exemple, « LitwareBatchTaskPrograms ») et de votre application.

4. Pour créer la nouvelle solution, cliquez sur **OK**.

## <a name="job-manager-template"></a>Modèle de gestionnaire de tâches

Le modèle de gestionnaire de tâches vous aide à implémenter une gestionnaire de tâche qui peut effectuer les actions suivantes :

* Fractionner une tâche en plusieurs tâches.
* Soumettre les tâches à exécuter sur le traitement par lots.

>[AZURE.NOTE] Pour plus d’informations sur le Gestionnaire de tâches, voir [Présentation des fonctionnalités de traitement par lots pour les développeurs](batch-api-basics.md#job-manager-task).

### <a name="create-a-job-manager-using-the-template"></a>Création d’un gestionnaire de projet à l’aide du modèle

Pour ajouter un gestionnaire de projet à la solution que vous avez créé précédemment, procédez comme suit :

1. Ouvrez votre solution existante dans Visual Studio 2015.

2. Dans l’Explorateur de solutions, cliquez sur la solution, cliquez sur **Ajouter** > **Nouveau projet**.

3. Sous **Visual C#**, cliquez sur le **nuage**, puis cliquez sur **Gestionnaire de travaux par lots Azure avec fractionnement de la tâche**.

4. Tapez un nom qui décrit votre application et identifie ce projet en tant que le Gestionnaire de tâches (par exemple, « LitwareJobManager »).

5. Pour créer le projet, cliquez sur **OK**.

6. Enfin, générez le projet pour forcer Visual Studio de charger tous les packages NuGet référencés et de vérifier que le projet est valide avant de commencer à modifier.

### <a name="job-manager-template-files-and-their-purpose"></a>Les fichiers de modèle de gestionnaire de travaux et de leur objectif

Lorsque vous créez un projet à l’aide du modèle de gestionnaire de tâches, il génère trois groupes de fichiers de code :

* Le fichier de programme principal (Program.cs). Cet onglet contient le point d’entrée du programme et de la gestion des exceptions de niveau supérieur. Vous devez ne devrait pas normalement modifier cette option.

* Le répertoire du Framework. Cet onglet contient les fichiers de travail 'standard' effectué par le programme de gestion de projet – décompression des paramètres, d’ajouter des tâches au traitement par lots, etc.. Vous ne devez normalement modifier ces fichiers.

* Le fichier séparateur de travaux (JobSplitter.cs). Il s’agit d’où vous allez placer la logique spécifique à l’application pour le fractionnement d’une tâche en tâches.

Bien sûr, vous pouvez ajouter des fichiers supplémentaires requis pour prendre en charge de votre code de séparateur de travail, en fonction de la complexité de la logique de fractionnement de tâche.

Le modèle génère également des fichiers de projet .NET standard fichier .csproj, app.config, packages.config, etc..

Le reste de cette section décrit les différents fichiers et leur structure de code et explique la signification de chaque classe.

![Explorateur de solutions Visual Studio affichant le Gestionnaire de projet modèle de solution][solution_explorer01]

**Fichiers de Framework**

* `Configuration.cs`: Encapsule le chargement des données de configuration de projet telles que les détails du lot de compte, informations d’identification du compte de stockage, tâche et informations sur les tâches et paramètres de la tâche. Il fournit également l’accès aux variables d’environnement de définis par lot (voir les paramètres d’environnement pour les tâches, dans la documentation de lot) via la classe Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Résume l’implémentation de la classe de Configuration, afin que vous puissiez les tests d’unité votre fractionnement de la tâche à l’aide d’un objet de configuration de substitut ou de simulacre.

* `JobManager.cs`: Orchestre les composants du programme Gestionnaire des tâches. Il est responsable de l’initialisation du séparateur de travail, en appelant le fractionnement de la tâche et distribuer les tâches retournés par le fractionnement de la tâche à l’émetteur de la tâche.

* `JobManagerException.cs`: Représente une erreur qui requiert le Gestionnaire de tâches terminer. JobManagerException est utilisée pour encapsuler des erreurs 'attendus' où les informations de diagnostics spécifiques peuvent être fournies dans le cadre de la résiliation.

* `TaskSubmitter.cs`: Cette classe est responsable de l’ajout de tâches retournés par le fractionnement de la tâche pour le traitement par lots. Les agrégats de classe JobManager la séquence de tâches en lots plus efficace mais en temps opportun à la tâche, puis appelle TaskSubmitter.SubmitTasks sur un thread d’arrière-plan pour chaque lot.

**Fractionnement de la tâche**

`JobSplitter.cs`: Cette classe contient la logique spécifique à l’application pour le fractionnement de la tâche en tâches. L’infrastructure appelle la méthode JobSplitter.Split pour obtenir une séquence de tâches, il ajoute au travail que la méthode les retourne. C’est la classe où vous injectera la logique de votre travail. Implémentez la méthode Split pour retourner une séquence d’instances de CloudTask qui représente les tâches dans lesquelles vous souhaitez partitionner le travail.

**Fichiers de projet de ligne de commande .NET standard**

* `App.config`: Fichier de configuration application .NET standard.

* `Packages.config`: Fichier de dépendance package NuGet standard.

* `Program.cs`: Contient le point d’entrée du programme et de la gestion des exceptions de niveau supérieur.

### <a name="implementing-the-job-splitter"></a>Le fractionnement de la tâche de mise en œuvre

Lorsque vous ouvrez le Gestionnaire de projet de modèle de projet, vous devez le projet le fichier JobSplitter.cs pour ouvrir par défaut. Vous pouvez implémenter la logique de fractionnement des tâches dans votre charge de travail à l’aide de l’émission de la méthode Split() ci-dessous :

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

>[AZURE.NOTE] La section annotée dans les `Split()` méthode est la seule partie du code du modèle de gestionnaire de tâche qui vous est destiné à modifier par l’ajout de la logique de fractionner vos travaux en différentes tâches. Si vous souhaitez modifier une section différente du modèle, vérifiez que vous sont être familiarisé avec le fonctionne du traitement par lots et essayer de quelques-uns des [exemples de code de lot][github_samples].

Votre implémentation de Split() a accès à :

* Les paramètres de la tâche, via le `_parameters` champ.
* L’objet CloudJob qui représente le travail, via le `_job` champ.
* L’objet CloudTask qui représente la tâche Gestionnaire de projet, via le `_jobManagerTask` champ.

Votre `Split()` implémentation n’a pas besoin d’ajouter directement des tâches à la tâche. Au lieu de cela, votre code doit retourner une séquence d’objets de CloudTask, et ils seront ajoutés au projet automatiquement par les classes du framework qui appellent le fractionnement de la tâche. Il est courant d’itérateur d’utiliser C# (`yield return`) fonction pour implémenter les séparateurs de travail comme cela permet les tâches démarrer l’exécution dès que possible plutôt que d’attendre pour toutes les tâches de calcul.

**Échec de tâche fractionnée**

Si le séparateur de travail rencontre une erreur, il doit :

* Terminer la séquence à l’aide de C# `yield break` instruction, dans lequel cas le Gestionnaire de travaux est considéré comme réussi ; ou

* Une exception, auquel cas que le Gestionnaire de travaux sera traité comme a échoué et peut être répétée en fonction de la façon dont le client a configuré).

Dans les deux cas, toutes les tâches déjà renvoyé par le fractionnement de la tâche et ajoutées à la tâche par lot seront autorisés à exécuter. Si vous ne souhaitez pas que cela se produise, vous pouvez :

* Arrêter la tâche avant le retour de fractionnement de la tâche

* Élaboration de la collection de l’ensemble de la tâche avant de la retourner (c'est-à-dire retournent un `ICollection<CloudTask>` ou `IList<CloudTask>` au lieu d’implémenter votre fractionnement de la tâche à l’aide d’un itérateur C#)

* Interdépendances des tâches permet d’effectuer toutes les tâches dépendent de l’achèvement réussi du Gestionnaire de tâches

**Tentatives de gestionnaire de projet**

Si le Gestionnaire de tâches échoue, il peut être relancée par le service de traitement par lots selon les paramètres de nouvelles tentatives du client. En général, c’est sûr, car lorsque le framework ajoute des tâches à la tâche, il ignore toutes les tâches qui existent déjà. Toutefois, si les tâches de calcul est coûteux, vous pouvez choisir pas de charge le coût du recalcul des tâches qui ont déjà été ajoutées à la tâche ; à l’inverse, si vous l’exécutez de nouveau n’est pas garanti pour générer la même ID de tâche puis le comportement « ignorer les doublons » pas démarre. Dans ces cas, vous devez concevoir votre fractionnement de la tâche pour détecter le travail déjà effectué et pas répéter, par exemple en effectuant une CloudJob.ListTasks avant de commencer à produire des tâches.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Codes de sortie et les exceptions dans le modèle de gestionnaire de tâches

Codes de sortie et les exceptions fournissent un mécanisme pour déterminer le résultat de l’exécution d’un programme, et ils peuvent aider à identifier les problèmes liés à l’exécution du programme. Le modèle de gestionnaire de travaux implémente les codes de sortie et les exceptions décrites dans cette section.

Une gestionnaire de tâche qui est implémentée avec le modèle de gestionnaire de travaux peut retourner trois codes de sortie possibles :

| Code | Description |
|------|-------------|
| 0    | Le Gestionnaire de tâches s’est terminé correctement. Votre code de fractionnement de tâche est terminée et toutes les tâches ont été ajoutées à la tâche. |
| 1    | La tâche de gestionnaire du projet a échoué avec une exception dans une partie « attendue » du programme. L’exception a été traduite dans une JobManagerException avec les informations de diagnostic et, si possible, suggestions pour résoudre le problème. |
| 2    | La tâche Gestionnaire de projet a échoué avec une exception « inattendue ». L’exception qui a été enregistrée sur la sortie standard, mais le Gestionnaire de tâches n’a pas pu ajouter les informations de conversion ou de diagnostic supplémentaires. |

En cas de défaillance de tâche du Gestionnaire de tâches, certaines tâches peuvent toujours ont été ajoutés au service avant que l’erreur s’est produite. Ces tâches s’exécutent normales. Pour une discussion sur ce chemin d’accès du code, voir « Échec de fractionnement de travail » ci-dessus.

Toutes les informations renvoyées par des exceptions sont écrit dans les fichiers stdout.txt et stderr.txt. Pour plus d’informations, consultez [Gestion des erreurs](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Considérations sur les clients

Cette section décrit certaines exigences de mise en œuvre du client lors de l’appel d’un gestionnaire de projet basé sur ce modèle. Pour plus d’informations sur le passage de paramètres et des paramètres d’environnement, reportez-vous à la section [comment passer des paramètres et des variables d’environnement à partir du code client](#pass-environment-settings) .

**Informations d’identification obligatoires**

Pour ajouter des tâches à la tâche par lot d’Azure, la Gestionnaire de tâche requiert votre URL de compte Azure lot et la clé. Vous devez transmettre ces variables d’environnement nommées YOUR_BATCH_URL et YOUR_BATCH_KEY. Vous pouvez définir ces dans le Gestionnaire de travaux paramètres d’environnement de tâche. Par exemple, dans un client C# :

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Informations d’identification de stockage**

En général, le client n’a pas besoin fournir les informations d’identification du compte de stockage lié à la tâche Gestionnaire de projet, car les responsables de la tâche (a) les plus n’avez pas besoin d’accéder explicitement le compte de stockage et (b) le compte de stockage est souvent fourni sur toutes les tâches sous la forme d’un paramètre d’environnement courant pour le travail. Si vous ne fournissez pas le compte de stockage via les paramètres d’environnement commun et le Gestionnaire de travaux requiert l’accès au stockage lié, vous devez fournir les informations d’identification de stockage comme suit :

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Paramètres de tâche du Gestionnaire de projet**

Le client doit définir l’indicateur de *killJobOnCompletion* de gestionnaire de travail **false**.

Il est généralement sécurisé pour le client de définir *runExclusive* sur **false**.

Le client doit utiliser la collection *resourceFiles* ou *applicationPackageReferences* pour que la tâche exécutable du gestionnaire (et ses DLL requis) déployée sur le nœud de calcul.

Par défaut, le Gestionnaire de travaux va être retentée pas en cas d’échec. En fonction de votre logique de gestionnaire de projet, le client peut souhaitez activer tentatives via les *contraintes*/*maxTaskRetryCount*.

**Paramètres de travail**

Si le séparateur de travail émet des tâches avec des dépendances, le client doit définir les usesTaskDependencies du travail sur true.

Dans le modèle de séparateur de travail, il est inhabituel pour les clients à ajouter des tâches à des projets en plus de ce qui crée le fractionnement de la tâche. Le client doit donc normalement la valeur du travail *onAllTasksComplete* **terminatejob**.

## <a name="task-processor-template"></a>Modèle de tâche

Un modèle de tâche vous aide à implémenter un processeur de tâches que peut effectuer les actions suivantes :

* Permet de paramétrer les informations requises par chaque tâche à exécuter.
* Exécuter toutes les actions requises par chaque tâche.
* Enregistrer les sorties de tâche dans un stockage persistant.

Bien qu’un processeur de tâches n’est pas obligatoire pour exécuter des tâches sur le lot, l’avantage de l’utilisation d’un processeur de tâches est qu’elle fournit un wrapper pour mettre en œuvre toutes les actions de l’exécution de tâches dans un seul emplacement. Par exemple, si vous avez besoin d’exécuter plusieurs applications dans le contexte de chaque tâche, ou si vous devez copier les données dans un stockage persistant après la fin de chaque tâche.

Les actions exécutées par le processeur de tâches peuvent être simple ou complexe et autant ou moins, en fonction de votre charge de travail. En outre, en mettant en œuvre toutes les actions de tâche dans un processeur de tâches, vous pouvez facilement mettre à jour ou ajouter des actions basées sur les modifications apportées aux applications ou charges de travail. Toutefois, dans certains cas un processeur de tâches peut-être pas la solution optimale pour votre mise en œuvre comme il peut ajouter une complexité inutile, par exemple lors de l’exécution de travaux qui peuvent être démarrés rapidement à partir d’une simple ligne de commande.

### <a name="create-a-task-processor-using-the-template"></a>Créer un processeur de tâches à l’aide du modèle

Pour ajouter un processeur de tâches à la solution que vous avez créé précédemment, procédez comme suit :

1. Ouvrez votre solution existante dans Visual Studio 2015.

2. Dans l’Explorateur de solutions, avec le bouton droit de la solution, cliquez sur **Ajouter**, puis cliquez sur **Nouveau projet**.

3. Dans **Visual C#**, cliquez sur le **nuage**, puis cliquez sur **Processeur de tâches de traitement par lots Azure**.

4. Tapez un nom qui décrit votre application et identifie ce projet en tant que processeur de tâches (par exemple, « LitwareTaskProcessor »).

5. Pour créer le projet, cliquez sur **OK**.

6. Enfin, générez le projet pour forcer Visual Studio de charger tous les packages NuGet référencés et de vérifier que le projet est valide avant de commencer à modifier.

### <a name="task-processor-template-files-and-their-purpose"></a>Fichiers de modèle de processeur de tâches et de leur fonction

Lorsque vous créez un projet en utilisant le modèle de processeur de tâches, il génère trois groupes de fichiers de code :

* Le fichier de programme principal (Program.cs). Cet onglet contient le point d’entrée du programme et de la gestion des exceptions de niveau supérieur. Vous devez ne devrait pas normalement modifier cette option.

* Le répertoire du Framework. Cet onglet contient les fichiers de travail 'standard' effectué par le programme de gestion de projet – décompression des paramètres, d’ajouter des tâches au traitement par lots, etc.. Vous ne devez normalement modifier ces fichiers.

* Le fichier du processeur de tâches (TaskProcessor.cs). Il s’agit d’où vous allez placer la logique spécifique à l’application pour l’exécution d’une tâche (généralement en appelant un exécutable existant). Code de pré et post-traitement, telles que le téléchargement des données supplémentaires ou le téléchargement de fichiers de résultats, également ici.

Bien sûr, vous pouvez ajouter des fichiers supplémentaires requis pour prendre en charge de votre code de processeur de tâches, en fonction de la complexité de la logique de fractionnement de tâche.

Le modèle génère également des fichiers de projet .NET standard fichier .csproj, app.config, packages.config, etc..

Le reste de cette section décrit les différents fichiers et leur structure de code et explique la signification de chaque classe.

![Explorateur de solutions Visual Studio affiche la solution de modèle du processeur de tâches][solution_explorer02]

**Fichiers de Framework**

* `Configuration.cs`: Encapsule le chargement des données de configuration de projet telles que les détails du lot de compte, informations d’identification du compte de stockage, tâche et informations sur les tâches et paramètres de la tâche. Il fournit également l’accès aux variables d’environnement de définis par lot (voir les paramètres d’environnement pour les tâches, dans la documentation de lot) via la classe Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Résume l’implémentation de la classe de Configuration, afin que vous puissiez les tests d’unité votre fractionnement de la tâche à l’aide d’un objet de configuration de substitut ou de simulacre.

* `TaskProcessorException.cs`: Représente une erreur qui requiert le Gestionnaire de tâches terminer. TaskProcessorException est utilisée pour encapsuler des erreurs 'attendus' où les informations de diagnostics spécifiques peuvent être fournies dans le cadre de la résiliation.

**Processeur de tâches**

* `TaskProcessor.cs`: Exécute la tâche. L’infrastructure appelle la méthode TaskProcessor.Run. C’est la classe où vous injectera la logique spécifique à l’application de votre tâche. Implémentez la méthode Run :
  * Analyser et valider les paramètres de la tâche
  * Composer de la ligne de commande pour n’importe quel programme externe que vous souhaitez appeler
  * Enregistrer les informations de diagnostic que vous pouvez avoir besoin pour le débogage
  * Démarrer un processus à l’aide de la ligne de commande
  * Attendez que le processus s’arrête
  * Capturer le code de sortie du processus pour déterminer si elle a réussi ou a échoué
  * Enregistrez les fichiers de sortie que vous souhaitez conserver de façon permanente

**Fichiers de projet de ligne de commande .NET standard**

* `App.config`: Fichier de configuration application .NET standard.
* `Packages.config`: Fichier de dépendance package NuGet standard.
* `Program.cs`: Contient le point d’entrée du programme et de la gestion des exceptions de niveau supérieur.

## <a name="implementing-the-task-processor"></a>Le processeur de tâches de mise en œuvre

Lorsque vous ouvrez le projet de modèle de processeur de tâches, vous devez le projet le fichier TaskProcessor.cs pour ouvrir par défaut. Vous pouvez implémenter la logique d’exécution des tâches dans votre charge de travail à l’aide de la méthode Run() indiquée ci-dessous :

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
>[AZURE.NOTE] La section annotée dans la méthode Run() est la seule partie du code du modèle de processeur de la tâche qui vous est destiné à modifier en ajoutant de la logique d’exécution des tâches dans votre charge de travail. Si vous souhaitez modifier une section différente du modèle, Veuillez tout d’abord vous familiariser avec le fonctionnement des commandes par la révision de la documentation de traitement par lots et en essayant de quelques-uns des exemples de code de lot.

La méthode Run() est responsable du lancement de la ligne de commande, démarrage d’un ou plusieurs processus, en attente de tous les processus, en enregistrant les résultats et enfin retourne avec un code de sortie. La méthode Run() est où vous implémentez la logique de traitement pour vos tâches. L’infrastructure de processeur de tâche appelle la méthode Run() vous n’avez pas besoin d’appeler vous-même.

Votre implémentation Run() a accès à :

* Les paramètres de la tâche, via le `_parameters` champ.
* L’ID projet et la tâche, via le `_jobId` et `_taskId` champs.
* La configuration de la tâche, via le `_configuration` champ.

**Échec de la tâche**

En cas d’échec, vous pouvez quitter la méthode Run() en levant une exception, mais présente le Gestionnaire d’exceptions de niveau supérieur dans le contrôle de code de sortie de la tâche. Si vous devez contrôler le code de sortie de sorte que vous pouvez distinguer les différents types d’échec, par exemple à des fins de diagnostics ou parce que certains modes de défaillance doivent se terminer la tâche et d’autres ne devraient pas, vous devez quitter la méthode Run() en renvoyant un code de sortie différent de zéro. Cela devient le code de sortie de la tâche.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Codes de sortie et les exceptions dans le modèle de processeur de tâches

Codes de sortie et les exceptions fournissent un mécanisme pour déterminer le résultat de l’exécution d’un programme, et elles peuvent aider à identifier les problèmes liés à l’exécution du programme. Le modèle de traitement de tâche implémente les codes de sortie et les exceptions décrites dans cette section.

Une tâche de processeur qui est implémentée avec le modèle de processeur de tâches peut retourner trois codes de sortie possibles :

| Code | Description |
|------|-------------|
|  [Process.ExitCode][process_exitcode] | Le processeur de tâches s’est terminé. Notez que cela n’implique pas que le programme que vous avez appelé a réussi – uniquement que le processeur de tâches appelé avec succès et effectuer tout post-traitement sans exceptions. La signification du code sortie varie selon le programme appelé – généralement le code de sortie 0 signifie que le programme a réussi et tout autre code de sortie signifie que le programme a échoué. |
| 1    | Le processeur de tâches a échoué avec une exception dans une partie « attendue » du programme. L’exception a été traduite dans une `TaskProcessorException` avec les informations de diagnostic et, si possible, suggestions pour résoudre le problème. |
| 2    | Le processeur de tâches a échoué avec une exception « inattendue ». L’exception qui a été enregistrée sur la sortie standard, mais le processeur de tâches n’a pas pu ajouter les informations de conversion ou de diagnostic supplémentaires. |

>[AZURE.NOTE] Si le programme que vous appelez utilise des codes de sortie 1 et 2 pour indiquer les modes d’échec spécifique, puis à l’aide de codes de sortie 1 et 2 pour les erreurs de processeur de tâches est ambigu. Vous pouvez modifier ces codes d’erreur de processeur tâche pour les codes de sortie unique en modifiant les cas d’exception dans le fichier Program.cs.

Toutes les informations renvoyées par des exceptions sont écrit dans les fichiers stdout.txt et stderr.txt. Pour plus d’informations, voir gestion d’erreur, dans la documentation de traitement par lots.

### <a name="client-considerations"></a>Considérations sur les clients

**Informations d’identification de stockage**

Si votre processeur de tâches utilise le stockage blob Azure pour rendre persistantes les sorties, par exemple à l’aide de la bibliothèque d’assistance de conventions de fichier, puis il doit accéder au *soit* le nuage stockage compte d’informations d’identification *ou* une URL de conteneur blob qui contient une signature d’accès partagé (SAS). Le modèle inclut la prise en charge de fournir les informations d’identification via des variables d’environnement courantes. Votre client peut passer les informations d’identification de stockage comme suit :

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Le compte de stockage est ensuite disponible dans la classe TaskProcessor via la `_configuration.StorageAccount` propriété.

Si vous préférez utiliser une URL de conteneur avec des associations de sécurité, vous pouvez également le transmettre via un paramètre d’environnement commun de travail, mais le modèle de processeur de tâches n’inclut pas actuellement prise en charge intégrée pour cela.

**Programme d’installation de stockage**

Il est recommandé que la tâche Gestionnaire de client ou de la tâche de créer des conteneurs requis par les tâches avant d’ajouter des tâches à la tâche. Ce champ est obligatoire que si vous utilisez une URL de conteneur avec des associations de sécurité, comme par exemple une URL n’inclut pas l’autorisation pour créer le conteneur. Il est recommandé de même si vous transmettez des informations d’identification du compte de stockage, lorsqu’il enregistre chaque tâche devoir appeler CloudBlobContainer.CreateIfNotExistsAsync sur le conteneur.

## <a name="pass-parameters-and-environment-variables"></a>Passer des paramètres et des variables d’environnement

### <a name="pass-environment-settings"></a>Paramètres de l’environnement

Un client peut passer des informations à la tâche Gestionnaire de projet dans l’écran des paramètres d’environnement. Ces informations puis utilisable par la Gestionnaire de tâche lors de la génération de la tâche de tâches de processeur qui s’exécute dans le cadre de la tâche de calcul. Exemples d’informations que vous pouvez passer comme paramètres d’environnement sont :

* Clés de compte et de nom de compte de stockage
* Lot compte URL
* Clé de compte de traitement par lots

Le service de traitement par lots dispose d’un mécanisme simple pour transmettre des paramètres d’environnement pour une gestionnaire de tâche à l’aide de la `EnvironmentSettings` propriété dans [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Par exemple, pour obtenir le `BatchClient` instance pour un compte de traitement par lots, vous pouvez transmettre des variables d’environnement à partir du client de code l’URL et les informations d’identification de la clé partagées pour le compte de traitement par lots. De même, pour accéder au compte de stockage qui est lié au compte de traitement par lots, vous pouvez passer le nom de compte de stockage et de la clé de compte de stockage en tant que variables d’environnement.

### <a name="pass-parameters-to-the-job-manager-template"></a>Passer des paramètres au modèle de gestionnaire de tâches

Dans de nombreux cas, il est utile passer des paramètres de projet par projet à la tâche Gestionnaire de projet, pour contrôler le processus de fractionnement de travail ou pour configurer les tâches du projet. Pour cela, en téléchargeant un fichier JSON nommé parameters.json sous la forme d’un fichier de ressources pour la tâche Gestionnaire de projet. Les paramètres sont ensuite accessibles dans le `JobSplitter._parameters` champ dans le modèle de gestionnaire de tâches.

>[AZURE.NOTE] Le Gestionnaire de paramètre intégré prend en charge uniquement les dictionnaires de chaîne en chaîne. Si vous souhaitez passer des valeurs JSON complexes comme valeurs de paramètre, vous devrez passer sous forme de chaînes et de les analyser dans le fractionnement de la tâche ou de modifier l’infrastructure `Configuration.GetJobParameters` méthode.

### <a name="pass-parameters-to-the-task-processor-template"></a>Passer des paramètres au modèle de processeur de tâches

Vous pouvez également transmettre des paramètres à des tâches individuelles implémentés en utilisant le modèle de processeur de tâches. Tout comme avec le modèle de gestionnaire de projet, le modèle de processeur de tâches recherche un fichier de ressources nommé

Parameters.JSON et le cas échéant il charge que le dictionnaire de paramètres. Il existe deux options pour le passage de paramètres aux tâches du processeur de tâches :

* Réutiliser les paramètres de la tâche JSON. Cela fonctionne bien si les seuls paramètres sont ceux qui sont à l’échelle de la tâche (par exemple, un rendu hauteur et largeur). Pour ce faire, lors de la création d’un CloudTask dans le fractionnement de la tâche, ajoutez une référence à l’objet de fichier de ressource de parameters.json de ResourceFiles de la tâche responsable de projet (`JobSplitter._jobManagerTask.ResourceFiles`) à la collection de ResourceFiles de la CloudTask.

* Générer et télécharger un document parameters.json de tâches spécifiques dans le cadre de l’exécution de la tâche fractionnée et référencer ce blob dans la collection de fichiers de ressources de la tâche. Ceci est nécessaire si des tâches différentes ont des paramètres différents. Un exemple peut être un scénario de rendu 3D où l’index de l’image est passé à la tâche en tant que paramètre.

>[AZURE.NOTE] Le Gestionnaire de paramètre intégré prend en charge uniquement les dictionnaires de chaîne en chaîne. Si vous souhaitez passer des valeurs JSON complexes comme valeurs de paramètre, vous devrez passer sous forme de chaînes et de les analyser dans le processeur de tâches ou de modifier l’infrastructure `Configuration.GetTaskParameters` méthode.

## <a name="next-steps"></a>Étapes suivantes

### <a name="persist-job-and-task-output-to-azure-storage"></a>Conserver la sortie de projet et la tâche dans le stockage Azure

Un autre outil très utile dans le développement de solutions de traitement par lots est [Conventions des fichiers par lots Azure][nuget_package]. Cette bibliothèque de classes .NET (en aperçu) dans vos applications .NET de traitement par lots permet de stocker et récupérer des sorties de tâche vers et depuis le stockage Azure. [Persistent Azure traitement par lots et tâches de sortie](batch-task-output.md) contient une description complète de la bibliothèque et de son utilisation.

### <a name="batch-forum"></a>Forum du lot

Le [Forum du lot Azure] [ forum] sur le site MSDN est l’endroit idéal pour discuter des lots et de poser des questions sur le service. Accédez pour les publications de « rémanentes » utiles et publiez vos questions, qu’ils surviennent alors que vous générez vos solutions de traitement par lots.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
