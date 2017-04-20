<properties
    pageTitle="Mise en route de blob de Visual Studio et de stockage connectés (projets WebJob) de services | Microsoft Azure"
    description="La mise en route à l’aide du stockage Blob dans un projet de WebJob après la connexion à un système de stockage Azure à l’aide de Visual Studio de services connectés."
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Mise en route avec les Blob Azure Visual Studio et stockage connecté services (projets WebJob)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article fournit des exemples de code C# qui montrent comment déclencher un processus, lorsqu’un blob Azure est créé ou mis à jour. Les exemples de code utilisent la [Kit de développement logiciel WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) version 1.x. Lorsque vous ajoutez un compte de stockage à un projet WebJob à l’aide de la boîte de dialogue de Visual Studio, **Ajouter des Services connectés** , le package NuGet de stockage Azure approprié est installé, les références de .NET appropriées sont ajoutées au projet, et les chaînes de connexion pour le compte de stockage sont mis à jour dans le fichier App.config.



## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Comment déclencher une fonction lorsqu’un objet blob est créé ou mis à jour

Cette section montre comment utiliser l’attribut **BlobTrigger** .

 **Remarque :** Le SDK WebJobs analyse les fichiers journaux pour surveiller les blobs nouvelles ou modifiées. Ce processus est lent ; une fonction ne peut pas déclenchée tant que quelques minutes ou plus après la création de l’objet blob.  Si votre application doit traiter les objets BLOB immédiatement, la méthode recommandée est de créer un message de la file d’attente lorsque vous créez le blob et que vous utilisez l’attribut [QueueTrigger](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) au lieu de l’attribut **BlobTrigger** sur la fonction qui traite le blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Seul espace réservé pour le nom d’objet blob avec extension  

L’exemple de code suivant copie les blobs de texte qui apparaissent dans le conteneur *d’entrée* pour le conteneur de *sortie* :

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Le constructeur d’attribut a un paramètre de chaîne qui spécifie le nom du conteneur et un espace réservé pour le nom de l’objet blob. Dans cet exemple, si un blob appelé *Blob1.txt* est créé dans le conteneur *d’entrée* , la fonction crée un blob appelé *Blob1.txt* dans le conteneur de *sortie* .

Vous pouvez spécifier un modèle de nom avec l’espace réservé du nom blob, comme indiqué dans l’exemple de code suivant :

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Ce code copie des seuls objets BLOB qui ont des noms commençant par « d’origine- ». Par exemple, *Blob1.txt d’origine* dans le conteneur *d’entrée* est copiée pour *Copie-Blob1.txt* dans le conteneur de *sortie* .

Si vous devez spécifier un modèle de nom pour les noms de blob qui contient des accolades dans le nom, double-cliquez sur les accolades. Par exemple, si vous souhaitez trouver des objets BLOB dans le conteneur *d’images* qui ont des noms comme suit :

        {20140101}-soundfile.mp3

Utilisez cette option pour votre modèle :

        images/{{20140101}}-{name}

Dans l’exemple, la valeur d’espace réservé *nom* serait *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Les espaces réservés au nom et extension du blob distinct

L’exemple de code suivant modifie l’extension de fichier pendant la copie des objets BLOB qui s’affichent dans le conteneur *d’entrée* pour le conteneur de *sortie* . Le code enregistre l’extension de l’objet blob *d’entrée* et définit l’extension de l’objet de *sortie* blob en *.txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Types que vous pouvez lier à des objets BLOB

Vous pouvez utiliser l’attribut **BlobTrigger** sur les types suivants :

* **chaîne**
* **TextReader**
* **Flux de données**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Autres types de désérialisation par [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Si vous souhaitez travailler directement avec le compte de stockage Azure, vous pouvez également ajouter un paramètre **CloudStorageAccount** à la signature de méthode.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Contenu de blob de texte en le liant à la chaîne de mise en route

Si vous prévoyez des blobs de texte, **BlobTrigger** peut être appliqué à un paramètre de **chaîne** . L’exemple de code suivant lie un objet blob de texte à un paramètre de **chaîne** nommé **logMessage**. La fonction utilise ce paramètre pour écrire le contenu de l’objet blob dans le tableau de bord WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Mise en route de sérialisation blob contenu à l’aide de ICloudBlobStreamBinder

L’exemple de code suivant utilise une classe qui implémente **ICloudBlobStreamBinder** pour activer l’attribut **BlobTrigger** lier un blob de type **WebImage** .

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

Le code de liaison de **WebImage** est fourni dans une classe **WebImageBinder** qui dérive de **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Comment gérer des objets BLOB poison

En cas d’échec d’une fonction de **BlobTrigger** , le Kit de développement appelle à nouveau, dans le cas où l’erreur a été provoquée par une erreur temporaire. Si l’échec est causé par le contenu de l’objet blob, la fonction échoue à chaque fois qu’il essaie de traiter l’objet blob. Par défaut, le Kit de développement appelle une fonction jusqu'à 5 fois pour un blob donné. Si la cinquième tentative échoue, le Kit de développement logiciel ajoute un message à une file d’attente nommée *webjobs-blobtrigger-poison*.

Le nombre maximal de tentatives est configurable. Le même paramètre [MaxDequeueCount](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) est utilisé pour la manipulation de poison blob et de la gestion des messages de file d’attente de poison.

Le message de la file d’attente pour les objets BLOB poison est un objet JSON qui contient les propriétés suivantes :

* FunctionId (sous la forme *{nom WebJob}*. Fonctions. *{Nom de la fonction}*, par exemple : WebJob1.Functions.CopyBlob)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* ETag (un identificateur de version de blob, par exemple : « 0x8D1DC6E70A277EF »)

Dans l’exemple de code suivant, la fonction **CopyBlob** a le code qui provoque l’échec de chaque fois qu’elle est appelée. Une fois le Kit de développement appelle pour le nombre maximal de nouvelles tentatives, un message est créé dans la file d’attente de blob poison, et ce message est traité par la fonction **LogPoisonBlob** .

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

Le Kit de développement logiciel désérialise automatiquement le message JSON. Voici la classe **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algorithme d’interrogation de BLOB

Le SDK WebJobs analyse tous les conteneurs spécifiés par les attributs **BlobTrigger** au démarrage de l’application. Dans un compte de stockage de grande capacité cette analyse peut prendre un certain temps, il peut être un certain temps avant de nouveau BLOB est trouvés et **BlobTrigger** fonctions sont exécutées.

Pour détecter des objets BLOB de nouvelles ou modifiées après le démarrage de l’application, le Kit de développement lit périodiquement à partir des journaux de stockage de blob. Les journaux de blob sont et seulement sont écrites physiquement de 10 minutes ou ainsi, il peut être longue après qu’un blob est créé ou mis à jour avant le correspondant **BlobTrigger** fonction s’exécute.

Il existe une exception pour les objets BLOB que vous créez à l’aide de l’attribut de **l’objet Blob** . Lorsque le SDK WebJobs crée un objet blob de nouveau, il passe le blob de nouveau immédiatement à toutes les fonctions de **BlobTrigger** correspondantes. Par conséquent, si vous disposez d’une chaîne de blob entrées et les sorties, le Kit de développement peut les traiter efficacement. Mais si vous souhaitez une latence faible le blob de fonctions pour les objets BLOB qui est créés ou mis à jour par d’autres moyens de traitement en cours d’exécution, nous vous recommandons d’utiliser **QueueTrigger** plutôt que **BlobTrigger**.

### <a name="blob-receipts"></a>Réceptions de BLOB

Le SDK WebJobs permet de s’assurer qu’aucune fonction **BlobTrigger** n’est appelée plusieurs fois pour le même objet blob de nouveau ou mis à jour. Il procède en maintenant un *blob reçus* afin de déterminer si une version de blob donné a été traitée.

Réceptions de BLOB sont stockées dans un conteneur nommé *azure-webjobs-hôtes* dans le compte de stockage Azure spécifié par la chaîne de connexion AzureWebJobsStorage. Un reçu de blob contient les informations suivantes :

* La fonction a été appelée pour l’objet blob («*{nom WebJob}*. Fonctions. *{Nom de la fonction}*», par exemple : « WebJob1.Functions.CopyBlob »)
* Le nom du conteneur
* Le type de blob (« BlockBlob » ou « PageBlob »)
* Le nom de l’objet blob
* L’ETag (un identificateur de version de blob, par exemple : « 0x8D1DC6E70A277EF »)

Si vous souhaitez forcer le retraitement d’un objet blob, vous pouvez supprimer manuellement la réception blob pour ce blob à partir du conteneur *d’azure-webjobs-hôtes* .

## <a name="related-topics-covered-by-the-queues-article"></a>Rubriques connexes couvertes par l’article de files d’attente

Pour plus d’informations sur la façon de gérer le traitement des blob déclenchée par un message de la file d’attente, ou pour WebJobs SDK scénarios non spécifiques au blob de traitement, voir [comment utiliser le stockage Azure de file d’attente avec le SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Rubriques connexes décrites dans cet article sont les suivants :

* Fonctions d’async
* Plusieurs instances
* Arrêt progressif
* Utilisez les attributs WebJobs SDK dans le corps d’une fonction
* Définir les chaînes de connexion du Kit de développement logiciel dans le code.
* Définir les valeurs pour WebJobs SDK paramètres du constructeur dans le code
* Configurer **MaxDequeueCount** pour la gestion de poison blob.
* Déclencher une fonction manuellement
* Écrire des journaux

## <a name="next-steps"></a>Étapes suivantes

Cet article fournit des exemples de code qui montrent comment gérer des scénarios courants d’utilisation de BLOB Azure. Pour plus d’informations sur l’utilisation de WebJobs d’Azure et le SDK WebJobs, consultez [les ressources de documentation Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).
