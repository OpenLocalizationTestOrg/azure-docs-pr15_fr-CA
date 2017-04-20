<properties 
    pageTitle="L’utilisation du stockage blob Azure avec le SDK WebJobs" 
    description="Apprenez à utiliser le stockage blob Azure avec le SDK WebJobs. Déclencher un processus, lorsqu’un objet blob de nouveau apparaît dans un conteneur et gérer « BLOB poison »." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-blob-storage-with-the-webjobs-sdk"></a>L’utilisation du stockage blob Azure avec le SDK WebJobs

## <a name="overview"></a>Vue d’ensemble

Ce guide fournit des exemples de code C# qui montrent comment déclencher un processus, lorsqu’un blob Azure est créé ou mis à jour. Les exemples de code utilisent le [Kit de développement logiciel WebJobs](websites-dotnet-webjobs-sdk.md) version 1.x.

Pour des exemples de code qui montrent comment créer des objets BLOB, voir [comment utiliser le stockage Azure de file d’attente avec le SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 
        
Le guide suppose que vous savez [comment créer un projet dans Visual Studio avec des chaînes de connexion qui pointent vers votre compte de stockage de WebJob](websites-dotnet-webjobs-sdk-get-started.md) ou de [plusieurs comptes de stockage](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

## <a id="trigger"></a>Comment déclencher une fonction lorsqu’un objet blob est créé ou mis à jour

Cette section indique comment utiliser le `BlobTrigger` attribut. 

> [AZURE.NOTE] Le SDK WebJobs analyse les fichiers journaux pour surveiller les blobs nouvelles ou modifiées. Ce processus n’est pas en temps réel ; une fonction ne peut pas déclenchée tant que quelques minutes ou plus après la création de l’objet blob. En outre, la base du [stockage, les journaux sont créés sur un « les efforts déployés »](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; Il n’y a aucune garantie que tous les événements seront capturés. Sous certaines conditions, les journaux peuvent être manqués. Si les limitations de vitesse et la fiabilité des déclencheurs de blob ne sont pas acceptables pour votre application, la méthode recommandée est de créer un message de la file d’attente lorsque vous créez l’objet blob et que vous utilisez l’attribut [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) à la place de la `BlobTrigger` attribut sur la fonction qui traite le blob.

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

## <a id="types"></a>Types que vous pouvez lier à des objets BLOB

Vous pouvez utiliser la `BlobTrigger` attribut sur les types suivants :

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Autres types de désérialisation par [ICloudBlobStreamBinder](#icbsb) 

Si vous souhaitez travailler directement avec le compte de stockage Azure, vous pouvez également ajouter un `CloudStorageAccount` paramètre de la signature de méthode.

Pour obtenir des exemples, consultez le [code de liaison de blob dans le référentiel d’azure-webjobs-sdk sur GitHub.com](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/BlobBindingEndToEndTests.cs).

## <a id="string"></a>Contenu de blob de texte en le liant à la chaîne de mise en route

Si vous prévoyez des blobs de texte, `BlobTrigger` peut être appliqué à un `string` paramètre. L’exemple de code suivant lie un objet blob de texte à un `string` paramètre nommé `logMessage`. La fonction utilise ce paramètre pour écrire le contenu de l’objet blob dans le tableau de bord WebJobs SDK. 
 
        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name, 
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a id="icbsb"></a>Mise en route de sérialisation blob contenu à l’aide de ICloudBlobStreamBinder

L’exemple de code suivant utilise une classe qui implémente `ICloudBlobStreamBinder` pour activer la `BlobTrigger` attribut pour lier un objet blob à la `WebImage` type.

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

Le `WebImage` code de liaison est fourni dans un `WebImageBinder` classe qui dérive de `ICloudBlobStreamBinder`.

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

## <a name="getting-the-blob-path-for-the-triggering-blob"></a>Obtenir le chemin d’accès de l’objet blob pour le blob de déclenchement

Pour obtenir le nom du conteneur et le nom de blob du blob qui a déclenché la fonction, incluez un `blobTrigger` chaîne de paramètre dans la signature de la fonction.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            string blobTrigger,
            TextWriter logger)
        {
             logger.WriteLine("Full blob path: {0}", blobTrigger);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }


## <a id="poison"></a>Comment gérer des objets BLOB poison

Lorsqu’un `BlobTrigger` Échec de la fonction, le Kit de développement appelle à nouveau, dans le cas où l’erreur a été provoquée par une erreur temporaire. Si l’échec est causé par le contenu de l’objet blob, la fonction échoue à chaque fois qu’il essaie de traiter l’objet blob. Par défaut, le Kit de développement appelle une fonction jusqu'à 5 fois pour un blob donné. Si la cinquième tentative échoue, le Kit de développement logiciel ajoute un message à une file d’attente nommée *webjobs-blobtrigger-poison*.

Le nombre maximal de tentatives est configurable. Le même paramètre [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) est utilisé pour la manipulation de poison blob et de la gestion des messages de file d’attente de poison. 

Le message de la file d’attente pour les objets BLOB poison est un objet JSON qui contient les propriétés suivantes :

* FunctionId (sous la forme *{nom WebJob}*. Fonctions. *{Nom de la fonction}*, par exemple : WebJob1.Functions.CopyBlob)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* ETag (un identificateur de version de blob, par exemple : « 0x8D1DC6E70A277EF »)

Dans l’exemple de code suivant, le `CopyBlob` fonction contient du code qui provoque l’échec de chaque fois qu’elle est appelée. Une fois le Kit de développement appelle pour le nombre maximal de nouvelles tentatives, un message est créé dans la file d’attente de poison blob et ce message est traité par le `LogPoisonBlob` fonction. 

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

Le Kit de développement logiciel désérialise automatiquement le message JSON. Voici le `PoisonBlobMessage` classe : 

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a id="polling"></a>Algorithme d’interrogation de BLOB

Le SDK WebJobs analyse tous les conteneurs indiqués par `BlobTrigger` attributs au démarrage de l’application. Dans un compte de stockage de grande capacité cette analyse peut prendre un certain temps, il peut être un certain temps avant que le nouveau BLOB est trouvés et `BlobTrigger` fonctions sont exécutées.

Pour détecter des objets BLOB de nouvelles ou modifiées après le démarrage de l’application, le Kit de développement lit périodiquement à partir des journaux de stockage de blob. Les journaux de blob sont et seulement sont écrites physiquement de 10 minutes ou, si bien qu’il peut donc retard important après un blob est créé ou mis à jour avant le correspondant `BlobTrigger` fonction s’exécute. 

Il existe une exception pour les objets BLOB que vous créez à l’aide de la `Blob` attribut. Lorsque le SDK WebJobs crée un objet blob de nouveau, il passe le blob de nouveau immédiatement à toute correspondance `BlobTrigger` fonctions. Par conséquent, si vous disposez d’une chaîne de blob entrées et les sorties, le Kit de développement peut les traiter efficacement. Mais si vous souhaitez que le blob en cours d’exécution des fonctions de traitement des objets BLOB qui est créés ou mis à jour par d’autres moyens de faible latence, nous vous recommandons d’utiliser `QueueTrigger` plutôt que `BlobTrigger`.

### <a id="receipts"></a>Réceptions de BLOB

Le SDK WebJobs permet de s’assurer qu’aucun `BlobTrigger` fonction est appelée plusieurs fois pour le même objet blob de nouveau ou mis à jour. Il procède en maintenant un *blob reçus* afin de déterminer si une version de blob donné a été traitée.

Réceptions de BLOB sont stockées dans un conteneur nommé *azure-webjobs-hôtes* dans le compte de stockage Azure spécifié par la chaîne de connexion AzureWebJobsStorage. Un reçu de blob contient les informations suivantes :

* La fonction a été appelée pour l’objet blob («*{nom WebJob}*. Fonctions. *{Nom de la fonction}*», par exemple : « WebJob1.Functions.CopyBlob »)
* Le nom du conteneur
* Le type de blob (« BlockBlob » ou « PageBlob »)
* Le nom de l’objet blob
* L’ETag (un identificateur de version de blob, par exemple : « 0x8D1DC6E70A277EF »)

Si vous souhaitez forcer le retraitement d’un objet blob, vous pouvez supprimer manuellement la réception blob pour ce blob à partir du conteneur *d’azure-webjobs-hôtes* .

## <a id="queues"></a>Rubriques connexes couvertes par l’article de files d’attente

Pour plus d’informations sur la façon de gérer le traitement des blob déclenchée par un message de la file d’attente, ou pour WebJobs SDK scénarios non spécifiques au blob de traitement, voir [comment utiliser le stockage Azure de file d’attente avec le SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Rubriques connexes décrites dans cet article sont les suivants :

* Fonctions d’async
* Plusieurs instances
* Arrêt progressif
* Utilisez les attributs WebJobs SDK dans le corps d’une fonction
* Définir les chaînes de connexion du Kit de développement logiciel dans le code.
* Définir les valeurs pour WebJobs SDK paramètres du constructeur dans le code
* Configurer `MaxDequeueCount` pour la gestion de poison blob.
* Déclencher une fonction manuellement
* Écrire des journaux

## <a id="nextsteps"></a>Étapes suivantes

Ce guide fournit des exemples de code qui montrent comment gérer des scénarios courants d’utilisation de BLOB Azure. Pour plus d’informations sur l’utilisation de WebJobs d’Azure et le SDK WebJobs, reportez-vous à la section [Ressources recommandé des WebJobs d’Azure](http://go.microsoft.com/fwlink/?linkid=390226).
 
