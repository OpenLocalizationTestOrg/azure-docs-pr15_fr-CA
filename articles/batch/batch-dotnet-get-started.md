<properties
    pageTitle="Didacticiel - mise en route de la bibliothèque .NET de lot Azure | Microsoft Azure"
    description="Découvrez les concepts de base du lot d’Azure et le développement du service de traitement par lots avec un exemple de scénario."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="08/15/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-library-for-net"></a>Mise en route avec la bibliothèque de commandes d’Azure pour .NET

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Apprenez les bases [d’Azure lot] [ azure_batch] et le [Traitement par lots .NET] [ net_api] bibliothèque dans cet article que nous abordons une application exemple C# étape par étape. Nous allons voir comment cet exemple d’application utilise le service de traitement par lots pour traiter une charge de travail parallèle dans le nuage, ainsi que la manière dont il interagit avec [Le stockage Azure](../storage/storage-introduction.md) pour transfert de fichier et l’extraction. Vous apprendrez des techniques courantes lot application du flux de travail. Vous allez également acquérir une compréhension de base des principaux composants de traitement par lots, tels que les tâches, les tâches, les pools et les nœuds de calcul.

![Workflow de la solution par lots (basic)][11]<br/>

## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous avez une connaissance pratique de C# et de Visual Studio. Il suppose également que vous êtes en mesure de satisfaire les exigences de création de compte sont précisées ci-après Azure et les services de stockage et de traitement par lots.

### <a name="accounts"></a>Comptes

- **Compte Azure**: Si vous ne disposez pas d’un abonnement Azure, [créer un compte Azure gratuit][azure_free_account].
- **Compte du lot**: une fois que vous avez un abonnement Azure, [créez un compte Azure lot](batch-account-create-portal.md).
- **Compte de stockage**: voir la section [Création d’un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md).

> [AZURE.IMPORTANT] Traitement par lots prend actuellement en charge *uniquement* le type de compte de stockage **polyvalente** , comme décrit à l’étape #5 [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md).

### <a name="visual-studio"></a>Visual Studio

Vous devez disposer de **Visual Studio 2015** pour générer l’exemple de projet. Vous trouverez des versions gratuites et d’évaluation de Visual Studio dans la [vue d’ensemble des produits de Visual Studio 2015][visual_studio].

### <a name="dotnettutorial-code-sample"></a>Exemple de code *DotNetTutorial*

La [DotNetTutorial] [ github_dotnettutorial] exemple est un des nombreux exemples de code dans les [exemples de traitement par lots azure] [ github_samples] référentiel sur GitHub. Vous pouvez télécharger l’exemple en cliquant sur le bouton **ZIP de téléchargement** sur la page d’accueil du référentiel ou en cliquant sur l' [azure-lot-exemples-master.zip] [ github_samples_zip] lien de téléchargement direct. Une fois que vous avez extrait le contenu du fichier ZIP, vous trouverez la solution dans le dossier suivant :

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>Azure Explorateur de lot (facultatif)

L' [Explorateur de lot Azure] [ github_batchexplorer] est un utilitaire gratuit qui est inclus dans les [exemples de traitement par lots azure] [ github_samples] référentiel sur GitHub. Bien que non obligatoire pour terminer ce didacticiel, il peut être utile lors du développement et de débogage de vos solutions de traitement par lots.

## <a name="dotnettutorial-sample-project-overview"></a>Vue d’ensemble du projet DotNetTutorial exemple

L’exemple de code *DotNetTutorial* est une solution Visual Studio 2015 qui se compose de deux projets : **DotNetTutorial** et **TaskApplication**.

- **DotNetTutorial** est l’application cliente qui interagit avec les services de stockage et de traitement par lots à exécuter une charge de travail parallèle sur les nœuds (machines virtuelles). DotNetTutorial s’exécute sur votre station de travail locale.

- **TaskApplication** est un programme qui s’exécute sur les nœuds de calcul dans Azure pour effectuer le travail réel. Dans l’exemple, `TaskApplication.exe` analyse le texte dans un fichier téléchargé depuis le stockage Azure (le fichier d’entrée). Ensuite, il génère un fichier de texte (le fichier de sortie) qui contient une liste des trois mots principaux qui s’affichent dans le fichier d’entrée. Après avoir créé le fichier de sortie, TaskApplication télécharge le fichier vers le stockage Azure. Cela permet à l’application cliente pour le téléchargement. TaskApplication s’exécute en parallèle sur plusieurs nœuds de calcul dans le service de traitement par lots.

Le diagramme suivant illustre les opérations principales qui sont effectuées par l’application qui est exécutée par les tâches, *TaskApplication*, *DotNetTutorial*et l’application cliente. Ce flux de travail est généralement de nombreuses solutions de calcul qui sont créées avec le traitement par lots. Il ne présente pas toutes les fonctionnalités disponibles dans le service de traitement par lots, presque tous les scénarios de traitement par lots inclut des processus similaires.

![Exemple de processus de traitement par lots][8]<br/>

[**Étape 1.**](#step-1-create-storage-containers) Permet de créer des **conteneurs** dans le stockage Blob Azure.<br/>
[**Étape 2.**](#step-2-upload-task-application-and-data-files) Télécharger les fichiers de l’application Office et des fichiers d’entrée aux conteneurs.<br/>
[**Étape 3.**](#step-3-create-batch-pool) Créer un **pool**de traitement par lots.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3 a.** La **tâche de début** du pool télécharge les fichiers binaires de tâche (TaskApplication) aux nœuds dès qu’ils rejoignent le pool.<br/>
[**Étape 4.**](#step-4-create-batch-job) Créer une **tâche**.<br/>
[**Étape 5.**](#step-5-add-tasks-to-job) Ajouter des **tâches** à la tâche.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5 a.** Les tâches sont planifiées pour s’exécuter sur les nœuds.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5 b.** Chaque tâche télécharge ses données d’entrée à partir du stockage Azure, puis commence l’exécution.<br/>
[**Étape 6.**](#step-6-monitor-tasks) Contrôler des tâches.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6 a.** Que les tâches sont terminées, ils téléchargent leurs données de sortie vers le stockage Azure.<br/>
[**Étape 7.**](#step-7-download-task-output) Télécharger la sortie de la tâche à partir du stockage.

Comme mentionné, pas chaque solution de traitement par lots effectue ces étapes exactes et peut-être inclure d’autres, mais *DotNetTutorial* l’exemple d’application montre les processus courants dans une solution de traitement par lots.

## <a name="build-the-dotnettutorial-sample-project"></a>Créer l’exemple de projet *DotNetTutorial*

Avant de pouvoir exécuter l’exemple avec succès, vous devez spécifier des informations d’identification de compte à la fois de lot et de stockage dans le fichier *DotNetTutorial* `Program.cs` fichier. Si vous ne le n'avez pas déjà fait, ouvrez la solution dans Visual Studio en double-cliquant sur le `DotNetTutorial.sln` fichier de solution. Ou ouvrez-le à partir de Visual Studio à l’aide de le **fichier > Ouvrir > Projet/Solution** menu.

Ouvrir `Program.cs` dans le projet *DotNetTutorial* . Ajoutez ensuite vos informations d’identification comme indiqué en haut du fichier :

```
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [AZURE.IMPORTANT] Comme mentionné ci-dessus, vous devez en spécifier les informations d’identification pour un compte de stockage **polyvalente** dans le stockage Azure. Vos applications de lot utiliser stockage blob dans le compte de stockage **polyvalente** . Ne spécifiez pas les informations d’identification pour un compte de stockage qui a été créé en sélectionnant le type de compte de *stockage des objets Blob* .

Vous trouverez vos informations d’identification compte de lot et de stockage au sein de la lame de compte de chaque service dans [Azure portal][azure_portal]:

![Lot d’informations d’identification dans le portail de][9]
![informations d’identification de stockage dans le portail][10]<br/>

Maintenant que vous avez mis à jour le projet avec vos informations d’identification, cliquez sur la solution dans l’Explorateur de solutions et cliquez sur **Générer la Solution**. Confirmer la restauration de tous les packages NuGet, si vous y êtes invité.

> [AZURE.TIP] Si les packages NuGet ne sont pas restaurées automatiquement ou si vous constatez des erreurs sur un échec pour restaurer les packages, assurez-vous d’avoir le [Du Gestionnaire de package NuGet] [ nuget_packagemgr] installé. Activer le téléchargement des packages manquants. Reportez-vous à la section [Activation Package restaurer lors de la génération] [ nuget_restore] pour permettre le téléchargement du package.

Dans les sections suivantes, nous décomposer l’exemple d’application dans les étapes qu’il accomplit pour traiter une charge de travail dans le service de traitement par lots et de décrire ces étapes en détail. Nous vous encourageons à faire référence à la solution ouverte dans Visual Studio, tandis que vous progressez dans le reste de cet article, dans la mesure où vous trouverez pas chaque ligne de code dans l’exemple.

Naviguer vers le haut de la `MainAsync` méthode dans le fichier *DotNetTutorial* `Program.cs` fichier de démarrage à l’étape 1. Chaque étape ci-dessous puis à peu près suit la progression d’appels de méthode dans `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Étape 1 : Créer des conteneurs de stockage

![Créer des conteneurs dans le stockage Azure][1]
<br/>

Lot prend en charge l’interaction avec le stockage Azure. Conteneurs dans votre compte de stockage fournira les fichiers requis par les tâches qui s’exécutent dans votre compte de traitement par lots. Les conteneurs fournissent également un emplacement pour stocker les données de sortie qui produisent des tâches. La première chose que fait l’application client de *DotNetTutorial* est créer trois conteneurs dans [Le stockage Blob Azure](../storage/storage-introduction.md):

- **application**: ce conteneur de stockage de l’application exécutée par les tâches, ainsi qu’une de ses dépendances, telles que les DLL.
- **entrée**: tâches de télécharger les fichiers de données à traiter dans le conteneur *d’entrée* .
- **sortie**: tâches issue de traitement du fichier d’entrée, ils télécharger les résultats vers le conteneur de *sortie* .

Pour interagir avec un compte de stockage et de créer des conteneurs, nous utilisons la [Bibliothèque de Client de stockage Azure pour .NET][net_api_storage]. Nous créons une référence au compte avec [CloudStorageAccount][net_cloudstorageaccount]et de celle créer un [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Nous utilisons le `blobClient` de référence tout au long de l’application et la passer comme paramètre à plusieurs méthodes. Un exemple de cela est dans le bloc de code qui suit immédiatement ce qui précède, où nous appeler `CreateContainerIfNotExistAsync` pour créer les conteneurs.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Une fois que les récipients ont été créés, l’application peut à présent télécharger les fichiers qui seront utilisées par les tâches.

> [AZURE.TIP] [Comment faire pour utiliser le stockage Blob à partir de .NET](../storage/storage-dotnet-how-to-use-blobs.md) fournit une bonne vue d’ensemble de l’utilisation des objets BLOB et les conteneurs de stockage Azure. Comme vous commencez à travailler avec le traitement par lots, il doit être en haut de la liste de lecture.

## <a name="step-2-upload-task-application-and-data-files"></a>Étape 2 : Télécharger des fichiers d’application et les données de tâche

![Télécharger l’application des tâches et des fichiers d’entrée (données) aux conteneurs][2]
<br/>

Dans l’opération de téléchargement de fichier, *DotNetTutorial* définit tout d’abord des collections des chemins d’accès de fichier **d’entrée** et les **application** tels qu’ils existent sur l’ordinateur local. Puis il télécharge ces fichiers vers les conteneurs que vous avez créé à l’étape précédente.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Il existe deux méthodes de `Program.cs` qui sont impliqués dans le processus de téléchargement :

- `UploadFilesToContainerAsync`: Cette méthode retourne une collection de [ResourceFile] [ net_resourcefile] objets (voir ci-dessous) et en interne les appels `UploadFileToContainerAsync` pour charger chaque fichier qui est passé dans le paramètre *filePaths* .
- `UploadFileToContainerAsync`: Il s’agit de la méthode qui effectue le téléchargement de fichiers et crée le [ResourceFile] [ net_resourcefile] objets. Après avoir téléchargé le fichier, il obtient une signature d’accès partagé (SAS) pour le fichier et renvoie un objet ResourceFile qui le représente. L’accès au partagé signatures sont également décrites ci-dessous.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath, FileMode.Open);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles

Un [ResourceFile] [ net_resourcefile] fournit des tâches dans un lot avec l’URL d’un fichier dans le stockage Azure qui est téléchargé sur un nœud de calcul avant l’exécution de cette tâche. La [ResourceFile.BlobSource] [ net_resourcefile_blobsource] propriété spécifie l’URL complète du fichier telle qu’elle existe dans le stockage Azure. L’URL peut également inclure une signature d’accès partagé (SAS) qui offre un accès sécurisé au fichier. La plupart des types de tâches dans un lot .NET inclut une propriété *ResourceFiles* , y compris :

- [CloudTask][net_task]
- [Tâche de début][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

L’exemple d’application DotNetTutorial n’utilise pas les types de tâches JobPreparationTask ou JobReleaseTask, mais vous pouvez en savoir plus sur les [nœuds de calcul exécuter préparation achèvement tâches et sur le traitement par lots d’Azure](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Signature de l’accès partagé (SAS)

Accès partagé des signatures sont des chaînes qui — lorsque inclus dans le cadre d’une URL, fournir un accès sécurisé à des conteneurs et des objets BLOB dans le stockage Azure. L’application utilise à la fois les blob et conteneur partagé de DotNetTutorial accéder aux URL de signature et montre comment obtenir ces chaînes de signature d’un accès partagé à partir du service de stockage.

- **Objet BLOB partagé signatures d’accès**: tâche de début du pool dans DotNetTutorial utilise les signatures d’accès partagé de blob lorsqu’il télécharge les fichiers binaires d’application et les fichiers de données d’entrée à partir du stockage (voir l’étape 3 ci-dessous). Le `UploadFileToContainerAsync` méthode de DotNetTutorial `Program.cs` contient le code qui obtient la signature d’accès partagé de chaque objet blob. Il le fait en appelant [CloudBlob.GetSharedAccessSignature][net_sas_blob].

- **Conteneur partagés accès signatures**: que chaque tâche termine son travail sur le nœud de calcul, il télécharge son fichier de sortie pour le conteneur de *sortie* dans le stockage Azure. Pour ce faire, TaskApplication utilise une signature d’accès partagé de conteneur qui fournit un accès en écriture au conteneur dans le chemin d’accès lorsqu’il télécharge le fichier. Obtention de la signature d’accès partagé de conteneur s’effectue de la même manière que lorsque l’obtention de l’objet blob partagé signature d’accès. Dans DotNetTutorial, vous constaterez que la `GetContainerSasUrl` méthode d’assistance appelle [CloudBlobContainer.GetSharedAccessSignature] [ net_sas_container] à le faire. Vous obtiendrez plus d’informations sur la manière dont TaskApplication utilise le conteneur partagé signature accès dans « étape 6 : tâches du moniteur. »

> [AZURE.TIP] Consultez la série en deux parties sur les signatures d’accès partagé, [partie 1 : présentation du modèle de signature (SAS) d’un accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md) et [partie 2 : créer et utiliser une signature d’accès partagé (SAS) avec stockage Blob](../storage/storage-dotnet-shared-access-signature-part-2.md), pour en savoir plus sur l’accès sécurisé aux données de votre compte de stockage.

## <a name="step-3-create-batch-pool"></a>Étape 3 : Créer le pool de traitement par lots

![Créer un pool de traitement par lots][3]
<br/>

Un **pool** de traitement par lots est une collection de nœuds de calcul (machines virtuelles) sur laquelle le lot s’exécute les tâches d’un travail.

Une fois qu’il télécharge les fichiers d’application et des données sur le compte de stockage *DotNetTutorial* démarre son interaction avec le service de traitement par lots à l’aide de la bibliothèque .NET de traitement par lots. Pour ce faire, un [BatchClient] de[ net_batchclient] est créé :

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Ensuite, un ensemble de nœuds de calcul est créé dans le compte de traitement par lots avec un appel à `CreatePoolAsync`. `CreatePoolAsync`utilise le [BatchClient.PoolOperations.CreatePool] de[ net_pool_create] méthode pour créer un pool dans le service de traitement par lots.

```csharp
private static async Task CreatePoolAsync(
    BatchClient batchClient,
    string poolId,
    IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(),
    // no pool is actually created in the Batch service. This CloudPool instance is
    // therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,           // 3 compute nodes
            virtualMachineSize: "small",  // single-core, 1.75 GB memory, 224 GB disk
            cloudServiceConfiguration:
                new CloudServiceConfiguration(osFamily: "4")); // Win Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join
    // the pool. In this case, we copy the StartTask's resource files (that will be
    // automatically downloaded to the node by the StartTask) into the shared
    // directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application
        // files to the node's shared directory. Every compute node in a Batch pool
        // is configured with several pre-defined environment variables that you can
        // reference by using commands or applications run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code
        // (e.g. 1 when one or more files were successfully copied) we need to
        // manually exit with a 0 for Batch to recognize StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Lorsque vous créez un pool avec [CreatePool][net_pool_create], vous spécifiez plusieurs paramètres tels que le nombre de nœuds de calcul, la [taille des nœuds](../cloud-services/cloud-services-sizes-specs.md)et du système des nœuds. Dans *DotNetTutorial*, nous utilisons [CloudServiceConfiguration] [ net_cloudserviceconfiguration] pour spécifier Windows Server 2012 R2 à partir des [Services en nuage](../cloud-services/cloud-services-guestos-update-matrix.md). Toutefois, en spécifiant un [VirtualMachineConfiguration] [ net_virtualmachineconfiguration] au lieu de cela, vous pouvez créer des pools de nœuds créés à partir des images du marché, qui inclut des images à la fois Windows et Linux, consultez [fourniture Linux nœuds dans des pools d’Azure lot](batch-linux-nodes.md) pour plus d’informations.

> [AZURE.IMPORTANT] Vous sont facturés pour les ressources de calcul de lot. Pour réduire les coûts, vous pouvez réduire le `targetDedicated` à 1 avant d’exécuter l’exemple.

Ces propriétés de nœud physique, vous pouvez également spécifier une [tâche de début] [ net_pool_starttask] pour le pool. La tâche de début s’exécute sur chaque nœud, ce nœud rejoint le pool, et chaque fois qu’un nœud a redémarré. La tâche de début est particulièrement utile pour installer des applications sur des nœuds de calcul avant l’exécution des tâches. Par exemple, si vos tâches de traitent des données à l’aide de scripts de Python, vous pouvez utiliser une tâche de début pour installer les Python sur les nœuds de calcul.

Dans cet exemple d’application, la tâche de début copie les fichiers qu’il télécharge à partir du stockage (qui sont spécifiés à l’aide de la [tâche de début][net_starttask].[ ResourceFiles] [ net_starttask_resourcefiles] propriété) à partir du répertoire de travail de tâche de début pour le répertoire partagé que vous peuvent accéder à *toutes les* tâches en cours d’exécution sur le nœud. Pour l’essentiel, cette copie `TaskApplication.exe` et ses dépendances dans le répertoire partagé sur chaque nœud que le nœud rattache le pool, afin que toutes les tâches qui s’exécutent sur le nœud peuvent y accéder.

> [AZURE.TIP] La fonctionnalité **packages d’applications** de traitement par lots d’Azure fournit un autre moyen d’obtenir votre application sur les nœuds de calcul dans un pool. Pour plus d’informations, consultez [déploiement d’applications avec des packages d’application Azure lot](batch-application-packages.md) .

Aussi intéressantes dans l’extrait de code ci-dessus est l’utilisation de deux variables d’environnement dans la propriété de *ligne de commande* de la tâche de début : `%AZ_BATCH_TASK_WORKING_DIR%` et `%AZ_BATCH_NODE_SHARED_DIR%`. Chaque nœud de calcul au sein d’un pool de traitement par lots est automatiquement configuré avec plusieurs variables d’environnement qui sont spécifiques à un traitement par lots. Tout processus qui est exécuté par une tâche a accès à ces variables d’environnement.

> [AZURE.TIP] Pour en savoir plus sur l’environnement de variables qui sont disponibles sur les nœuds de calcul dans un pool de traitement par lots et des informations sur les répertoires de travail tâche, consultez les sections de [paramètres d’environnement pour les tâches](batch-api-basics.md#environment-settings-for-tasks) et les [fichiers et les répertoires](batch-api-basics.md#files-and-directories) dans la [Présentation des fonctionnalités de traitement par lots pour les développeurs](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Étape 4 : Créer le traitement par lots

![Créer des lots][4]<br/>

Une **tâche** est un ensemble de tâches et est associé à un ensemble de nœuds de calcul. D’exécuter les tâches d’un projet sur les nœuds de calcul du pool associé.

Vous pouvez utiliser une tâche non seulement pour l’organisation et le suivi des tâches dans les charges de travail connexes, mais également pour l’imposition de certaines contraintes, telles que le runtime maximal pour la tâche (et par extension, ses tâches), ainsi que la priorité de la tâche par rapport à d’autres tâches dans le compte de traitement par lots. Toutefois, dans cet exemple, la tâche est uniquement associée au pool qui a été créé à l’étape #3. Aucune des propriétés supplémentaires ne sont configurées.

Tous les traitements par lots sont associés à un pool spécifique. Cette association indique les nœuds de la tâche va s’exécuter sur. Vous le spécifier à l’aide de la [CloudJob.PoolInformation] [ net_job_poolinfo] la propriété, comme indiqué dans l’extrait de code ci-dessous.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Maintenant qu’un travail a été créé, les tâches sont ajoutées pour effectuer le travail.

## <a name="step-5-add-tasks-to-job"></a>Étape 5 : Ajouter des tâches à la tâche

![Ajouter des tâches à une tâche][5]<br/>
*(1) les tâches sont ajoutées à la tâche et (2), les tâches sont planifiées pour s’exécuter sur les nœuds (3) les tâches de téléchargement les fichiers de données à traiter*

Traitement par lots, **les tâches** sont les unités de travail individuelles qui s’exécutent sur les nœuds de calcul. Une tâche a une ligne de commande et exécute les scripts ou les exécutables que vous spécifiez dans la ligne de commande.

Pour réaliser le travail, les tâches doivent être ajoutés à un projet. Chaque [CloudTask] [ net_task] est configuré à l’aide d’une propriété de ligne de commande et la [ResourceFiles] [ net_task_resourcefiles] (comme avec la tâche de début du pool) que la tâche télécharge sur le nœud avant sa ligne de commande est exécutée automatiquement. Dans l’exemple de projet *DotNetTutorial* , chaque tâche ne traite qu’un seul fichier. Par conséquent, sa collection ResourceFiles contient un seul élément.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] Lorsqu’ils accéder aux variables d’environnement tels que `%AZ_BATCH_NODE_SHARED_DIR%` ou exécuter une application non trouvée dans le nœud `PATH`, lignes de commande de tâche doit être précédés de `cmd /c`. Cela sera explicitement exécuter l’interpréteur de commandes et lui indique d’arrêter après avoir effectué votre commande. Cette exigence n’est pas nécessaire si une application d’exécuter vos tâches dans le nœud `PATH` (comme *robocopy.exe* ou *powershell.exe*) et les variables d’environnement sont utilisées.

Dans le `foreach` boucle dans l’extrait de code ci-dessus, vous pouvez voir que la ligne de commande de la tâche est construite telle que trois arguments de ligne de commande sont passés à *TaskApplication.exe*:

1. **premier argument** est le chemin d’accès du fichier à traiter. Il s’agit du chemin d’accès local au fichier telle qu’elle existe sur le nœud. Lorsque le ResourceFile objet dans `UploadFileToContainerAsync` a été créé tout d’abord, le nom de fichier a été utilisé pour cette propriété (en tant que paramètre au constructeur ResourceFile). Cela indique que le fichier se trouve dans le même répertoire que *TaskApplication.exe*.

2. **deuxième argument** Spécifie que les mots top *N* doivent être écrite dans le fichier de sortie. Dans l’exemple, il est codé en dur afin que les trois premiers mots sont écrits dans le fichier de sortie.

3. **troisième argument** est la signature d’accès partagé (SAS) qui fournit un accès en écriture au conteneur de **sortie** dans le stockage Azure. *TaskApplication.exe* utilise cette URL de signature accès partagé lorsqu’il télécharge le fichier de sortie vers le stockage Azure. Vous trouverez le code pour cela dans le `UploadFileToContainer` méthode dans le fichier TaskApplication `Program.cs` fichier :

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath, FileMode.Open);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Étape 6 : Tâches du moniteur

![Tâches du moniteur][6]<br/>
*L’application cliente (1) surveille les tâches pour la réalisation et le statut de réussite, et (2) les tâches téléchargement des données de résultat pour le stockage Azure*

Lorsque les tâches sont ajoutées à une tâche, ils sont automatiquement mis en attente et planifiées pour une exécution sur les nœuds de calcul dans le pool associé à la tâche. Selon les paramètres que vous spécifiez, lot gère queuing à toutes les tâches, la planification, une nouvelle tentative et autres tâches d’administration de tâche pour vous. Il existe de nombreuses approches à la surveillance de l’exécution de la tâche. DotNetTutorial affiche un exemple simple qui ne signale que réussite ou échec d’achèvement et de la tâche les États.

Dans le `MonitorTasks` méthode de DotNetTutorial `Program.cs`, il existe trois concepts .NET de traitement par lots qui nécessitent une discussion. Ils sont répertoriés ci-dessous dans leur ordre d’apparition :

1. **ODATADetailLevel**: spécification de [ODATADetailLevel] [ net_odatadetaillevel] dans la liste des opérations (telles que l’obtention d’une liste de tâches d’un projet) est essentiel pour garantir les performances des applications de traitement par lots. Ajouter [efficace d’interroger le service de traitement par lots d’Azure](batch-efficient-list-queries.md) à votre liste de lecture si vous prévoyez d’effectuer un type quelconque de surveillance dans vos applications de traitement par lots de l’état.

2. **TaskStateMonitor**: [TaskStateMonitor] [ net_taskstatemonitor] fournit aux applications .NET de lot utilitaires d’assistance pour l’analyse des États de la tâche. Dans `MonitorTasks`, *DotNetTutorial* attend que toutes les tâches atteindre le [TaskState.Completed] [ net_taskstate] dans un délai déterminé. Puis il met fin à la tâche.

3. **TerminateJobAsync**: fin d’un travail avec [JobOperations.TerminateJobAsync] [ net_joboperations_terminatejob] (ou le blocage JobOperations.TerminateJob) marque cette tâche comme terminée. Il est essentiel de le faire si votre solution de traitement par lots utilise un [JobReleaseTask][net_jobreltask]. Il s’agit d’un type spécial de tâche, qui est décrite dans les [tâches de préparation et de réalisation du projet](batch-job-prep-release.md).

Le `MonitorTasks` méthode à partir de *DotNetTutorial* `Program.cs` apparaît ci-dessous :

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a scheduling error
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the
        // Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.SchedulingError != null)
        {
            // A scheduling error indicates a problem starting the task on the node.
            // It is important to note that the task's state can be "Completed," yet
            // still have encountered a scheduling error.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}",
                task.Id,
                task.ExecutionInformation.SchedulingError.Message);
        }
        else if (task.ExecutionInformation.ExitCode != 0)
        {
            // A non-zero exit code may indicate that the application executed by
            // the task encountered an error during execution. As not every
            // application returns non-zero on failure by default (e.g. robocopy),
            // your implementation of error checking may differ from this example.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Étape 7 : Téléchargez la sortie de la tâche

![Télécharger la sortie de la tâche à partir du stockage][7]<br/>

Maintenant que le travail est terminé, la sortie des tâches peut être téléchargée à partir du stockage Azure. Cela est effectué avec un appel à `DownloadBlobsFromContainerAsync` de *DotNetTutorial* `Program.cs`:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] L’appel à `DownloadBlobsFromContainerAsync` dans le *DotNetTutorial* de l' application spécifie que les fichiers doivent être téléchargés sur votre `%TEMP%` dossier. N’hésitez pas à modifier cet emplacement de sortie.

## <a name="step-8-delete-containers"></a>Étape 8 : Conteneurs de supprimer

Dans la mesure où vous sont facturés pour les données se trouvant dans le stockage Azure, il est toujours judicieux de supprimer les objets BLOB qui n’est plus nécessaires pour vos traitements par lots. Dans la du DotNetTutorial `Program.cs`, cela est effectué avec trois appels à la méthode d’assistance `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

La méthode elle-même simplement Obtient une référence au conteneur et appelle ensuite [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Étape 9 : Supprimer le travail et le pool

Dans la dernière étape, l’utilisateur est invité à supprimer le travail et le pool qui ont été créés par l’application DotNetTutorial. Bien que vous ne sont pas facturés pour des projets et des tâches elles-mêmes, et qui vous *sont* facturés pour les nœuds de calcul. Par conséquent, nous vous recommandons d’allouer les nœuds uniquement en fonction des besoins. Suppression des pools non utilisés peut être la partie de votre processus de gestion.

De la BatchClient [JobOperations] [ net_joboperations] et [PoolOperations] [ net_pooloperations] ont des méthodes de suppression correspondante, qui sont appelées si l’utilisateur confirme la suppression :

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] N’oubliez pas que vous sont facturés pour les ressources de calcul : suppression de pools inutilisés minimiser les coûts. En outre, sachez que la suppression d’un pool supprime tous les nœuds de calcul dans ce pool, et que toutes les données sur les nœuds irrécupérables après que le pool est supprimé.

## <a name="run-the-dotnettutorial-sample"></a>Exécutez l’exemple *DotNetTutorial*

Lorsque vous exécutez l’exemple d’application, la sortie de la console sera semblable à la suivante. Lors de l’exécution, vous obtiendrez une pause au `Awaiting task completion, timeout in 00:30:00...` tandis que les nœuds de calcul du pool sont démarrés. Utiliser le [Azure portal] [ azure_portal] pour analyser votre pool, calculer les nœuds, les travaux et les tâches pendant et après l’exécution. Utiliser le [portail Azure] [ azure_portal] ou de l' [Explorateur de stockage Azure] [ storage_explorers] pour afficher les ressources de stockage (conteneurs et objets BLOB) qui sont créés par l’application.

Les temps d’exécution par défaut est **5 minutes** lorsque vous exécutez l’application dans sa configuration par défaut.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Étapes suivantes

N’hésitez pas à apporter des modifications à la *DotNetTutorial* et de *TaskApplication* d’expérimenter les scénarios de calcul différentes. Par exemple, essayez d’ajouter un délai d’exécution, à *TaskApplication*, comme avec [Thread.Sleep][net_thread_sleep], pour simuler des tâches à exécution longue et les contrôler dans le portail. Essayez d’ajouter davantage de tâches ou en modifiant le nombre de nœuds de calcul. Ajouter une logique pour vérifier et autoriser l’utilisation d’un pool existant au moment de l’exécution de vitesse (*Conseil*: extraire `ArticleHelpers.cs` dans les [Microsoft.Azure.Batch.Samples.Common] [ github_samples_common] projet dans des [échantillons de lot azure][github_samples]).

Maintenant que vous êtes familiarisé avec le flux de travail d’une solution de traitement par lots, il est temps pour Explorer les fonctionnalités supplémentaires du service de traitement par lots.

- Lire la [Présentation des fonctionnalités de traitement par lots pour les développeurs](batch-api-basics.md), ce que nous conseillons pour tous les nouveaux utilisateurs de traitement par lots.
- Démarrer sur les autres articles de développement du lot en cours de **développement détaillée** dans [lot cursus][batch_learning_path].
- Extraire une autre implémentation de traitement de la charge de travail « mots top N » à l’aide du lot dans le [TopNWords] [ github_topnwords] exemple.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Créer des conteneurs dans le stockage Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Télécharger l’application des tâches et des fichiers d’entrée (données) aux conteneurs"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Créer le pool de traitement par lots"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Créer des lots"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Ajouter des tâches à une tâche"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Tâches du moniteur"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Télécharger la sortie de la tâche à partir du stockage"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Workflow de la solution par lots (diagramme complet)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Informations d’identification du lot de portail"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Informations d’identification de stockage dans le portail"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Workflow de la solution par lots (un minimum de tâches)"
