<properties
    pageTitle="Créer un instantané en lecture seule d’un objet blob | Microsoft Azure"
    description="Apprenez à créer un instantané d’un objet blob à sauvegarder des données blob à un moment donné dans le temps. Comprendre comment les captures instantanées sont facturées et comment les utiliser pour réduire les frais de la capacité."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="create-a-blob-snapshot"></a>Créer une capture instantanée de blob

## <a name="overview"></a>Vue d’ensemble

Un instantané est une version en lecture seule d’un objet blob qui est effectuée à un point dans le temps. Les captures instantanées sont utiles pour la sauvegarde des objets BLOB. Après avoir créé une capture instantanée, vous pouvez lire, copier ou supprimer, mais vous ne pouvez pas le modifier.

Un instantané d’un objet blob est identique à sa base blob, à ceci près que le blob URI a une valeur **DateTime** ajoutée à l’objet blob URI pour indiquer l’heure à laquelle l’instantané a été pris. Par exemple, si une page blob URI est `http://storagesample.core.blob.windows.net/mydrives/myvhd`, la capture instantanée URI est semblable à `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. 

> [AZURE.NOTE] Tous les instantanés partagent URI du blob base. La seule distinction entre le blob de base et la capture instantanée est la valeur de **DateTime** ajoutée.

Un blob peut avoir n’importe quel nombre de captures instantanées. Les snapshots sont conservées jusqu'à ce qu’elles sont explicitement supprimées. Une capture instantanée ne survivent sa base blob. Vous pouvez énumérer les snapshots associés avec le blob de base pour effectuer le suivi de vos snapshots en cours.

Lorsque vous créez un instantané d’un objet blob, propriétés de l’objet blob du système sont copiées dans la capture instantanée avec les mêmes valeurs. Les métadonnées du blob de la base sont également copiée dans la capture instantanée, sauf si vous spécifiez des métadonnées séparé de la capture instantanée, lors de sa création.

Des baux associés à l’objet blob de base n’affectent pas la capture instantanée. Impossible d’obtenir un bail sur une capture instantanée.

## <a name="create-a-snapshot"></a>Créer une capture instantanée

L’exemple de code suivant montre comment créer un instantané dans .NET. Cet exemple spécifie les métadonnées séparé de la capture instantanée lors de sa création.

    private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
    {
        // Create a new block blob in the container.
        CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

        // Add blob metadata.
        baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

        try
        {
            // Upload the blob to create it, with its metadata.
            await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

            // Sleep 5 seconds.
            System.Threading.Thread.Sleep(5000);

            // Create a snapshot of the base blob.
            // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
            // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
            Dictionary<string, string> metadata = new Dictionary<string, string>();
            metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
            await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        }
        catch (StorageException e)
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
 

## <a name="copy-snapshots"></a>Instantanés de copie

Les opérations de copie impliquant des objets BLOB et les instantanés suivent ces règles :

- Vous pouvez copier une capture instantanée sur sa base blob. En promouvant un instantané à la position de l’objet blob de base, vous pouvez restaurer une version antérieure d’un objet blob. Le reste de la capture instantanée, mais la base de blob est remplacé par une copie accessible en écriture de la capture instantanée.

- Vous pouvez copier un instantané à un objet blob de destination avec un nom différent. Le blob de destination qui en résulte est un blob accessible en écriture et pas d’un instantané.

- Lorsque vous copiez un blob source, des snapshots de l’objet blob de source ne sont pas copiés vers la destination. Lorsqu’un objet blob de destination est remplacé par une copie, des snapshots associés à l’objet blob de destination d’origine restent intacts.

- Lorsque vous créez un instantané d’un objet blob de bloc, liste de bloc validées de l’objet blob est également copié à la capture instantanée. Tous les blocs non validées ne sont pas copiés.

## <a name="specify-an-access-condition"></a>Spécification d’une condition d’accès

Vous pouvez spécifier une condition d’accès afin que la capture instantanée est créée uniquement si une condition est remplie. Pour spécifier une condition d’accès, utilisez la propriété **AccessCondition** . Si la condition spécifiée n’est pas remplie, la capture instantanée n’est pas créée, et le service d’objet Blob renvoie le code d’état HTTPStatusCode.PreconditionFailed.

## <a name="delete-snapshots"></a>Supprimer des captures instantanées

Impossible de supprimer un objet blob avec les snapshots, sauf si les captures instantanées sont également supprimées. Vous pouvez supprimer une capture instantanée individuellement, ou spécifier que tous les snapshots supprimée lorsque le blob source est supprimé. Si vous essayez de supprimer un objet blob qui a encore des snapshots, une erreur se produit.

L’exemple de code suivant montre comment supprimer un objet blob et ses instantanés dans .NET, où `blockBlob` est une variable de type **CloudBlockBlob**:

    await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);

## <a name="snapshots-with-azure-premium-storage"></a>Snapshots avec le stockage Azure Premium

Utiliser des snapshots avec suivi de stockage de la prime de ces règles :

- Le nombre maximal de snapshots par blob de page dans un compte de stockage premium est de 100. Si cette limite est dépassée, l’opération de capture instantanée Blob retourne 409 (**SnapshotCountExceeded**) code d’erreur.

- Pour prendre un instantané d’un objet blob de page dans un compte de stockage premium une fois toutes les 10 minutes. Si ce taux est dépassé, l’opération de capture instantanée Blob retourne 409 (**SnaphotOperationRateExceeded**) code d’erreur.

- Vous ne pouvez pas appeler obtenir un Blob pour lire un instantané d’un objet blob de page dans un compte de stockage premium. L’appel à obtenir un Blob sur une capture instantanée dans un compte de stockage premium de renvoie le code d’erreur 400 (**InvalidOperation**). Toutefois, vous pouvez appeler la méthode obtenir les métadonnées de Blob et obtenir les propriétés de Blob par rapport à un instantané dans un compte de stockage premium.

- Pour lire une capture instantanée, vous pouvez utiliser l’opération de copie Blob à copier un instantané à un autre objet blob de page dans le compte. Le blob de destination pour l’opération de copie ne doit pas avoir des snapshots existants. Si le blob de destination ne dispose pas des snapshots, l’opération de copie Blob renvoie 409 (**SnapshotsPresent**) code d’erreur.

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Retourne l’URI absolu à un snapshot

Cet exemple de code C# crée un instantané et écrit l’URI absolu de l’emplacement principal.

    //Create the blob service client object.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## <a name="understand-how-snapshots-accrue-charges"></a>Comprendre le mode d’allocation des frais des snapshots

Création d’une capture instantanée, qui est une copie en lecture seule d’un objet blob, peut entraîner des frais de stockage des données supplémentaires à votre compte. Lorsque vous concevez votre application, il est important de savoir comment ces frais peuvent accumuler afin que vous pouvez réduire les coûts inutiles.

### <a name="important-billing-considerations"></a>Considérations importantes relatives à la facturation

La liste suivante comprend les points clés à prendre en compte lors de la création d’un instantané.

- Compte de stockage vous entraîne des frais pour les blocs uniques ou pages, qu’ils soient dans le blob ou de l’instantané. Votre compte n’entraîne pas de frais supplémentaires pour les snapshots associés à un objet blob jusqu'à ce que vous mettez à jour le blob sur lesquelles ils sont basés. Une fois que vous mettez à jour le blob de base, elle diffère de ses instantanés. Lorsque cela se produit, vous sont facturés pour les blocs uniques ou des pages dans chaque objet blob ou une capture instantanée.

- Lorsque vous remplacez un bloc au sein d’un objet blob de bloc, ce bloc est facturé par la suite comme un bloc unique. Cela est vrai même si le bloc de porte le même ID de bloc et les mêmes données que dans l’instantané. Une fois que le bloc est validé, elle diffère de son équivalent dans n’importe quel snapshot et vous seront facturés pour ses données. La même chose pour une page dans un blob de page qui est mis à jour avec des données identiques.

- Remplacement d’un objet blob de bloc en appelant la méthode **UploadFile**, **UploadText**, **UploadStream**ou **UploadByteArray** remplace tous les blocs dans le blob. Si vous avez un instantané associé à ce blob, tous les blocs dans le blob de base et l’instantané maintenant divergent, et vous seront facturés pour tous les blocs dans les deux objets BLOB. Cela est vrai même si les données dans le blob de base et la capture instantanée restent identiques.

- Le service Azure Blob n’a pas un moyen pour déterminer si deux blocs contiennent des données identiques. Chaque bloc qui est chargé et validé est considéré comme unique, même si elle a les mêmes données et le même ID de bloc. Étant donné que le cumul des frais de blocs uniques, il est important de considérer que la mise à jour d’un objet blob qui comporte une capture instantanée produit dans les blocs uniques supplémentaires et les frais supplémentaires.

> [AZURE.NOTE] Les meilleures pratiques préconisent que vous gérez les snapshots avec soin pour éviter des frais supplémentaires. Il est recommandé de gérer les instantanés de la manière suivante :

> - Supprimer et recréer les instantanés associés à un objet blob lorsque vous mettez à jour le blob, même si vous mettez à jour avec des données identiques, à moins que la conception de votre application exige que vous maintenez des captures instantanées. En supprimant et en recréant les snapshots de l’objet blob, vous pouvez vous assurer que les blob et les instantanés ne diffèrent pas.

> - Si vous mettez à jour des captures instantanées pour un blob, évitez d’appeler **UploadFile**, **UploadText**, **UploadStream**ou **UploadByteArray** pour mettre à jour le blob. Ces méthodes remplacent tous les blocs dans le blob, afin que votre base blob et les instantanés de déclarer divergent. Mise à jour au lieu de cela, le plus petit nombre possible de blocs en utilisant les méthodes **PutBlock** et **PutBlockList** .


### <a name="snapshot-billing-scenarios"></a>Scénarios de capture instantanée


Les scénarios suivants illustrent le mode d’allocation des frais pour un blob de bloc et de ses instantanés.

Dans le scénario 1, le blob de base n'a pas été mis à jour après la prise du cliché, afin que les frais encourus uniquement pour les blocs uniques de 1, 2 et 3.

![Ressources de stockage Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

Dans le scénario 2, le blob de base a été mise à jour, mais n’a pas la capture instantanée. Bloc 3 a été mis à jour, et même si elle contient les mêmes données et le même ID, il n’est pas identique à celle de la bloquer 3 dans l’instantané. Par conséquent, le compte est facturé pour quatre blocs.

![Ressources de stockage Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

Dans le scénario 3, le blob de base a été mise à jour, mais n’a pas la capture instantanée. Bloc 3 a été remplacé par bloc 4 dans le blob de base, mais l’instantané reflète toujours un bloc 3. Par conséquent, le compte est facturé pour quatre blocs.

![Ressources de stockage Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

Dans le scénario 4, le blob de base a été complètement mis à jour et ne contient aucun de ses blocs d’origine. Par conséquent, le compte est facturé pour tous les blocs uniques huit. Ce scénario peut se produire si vous utilisez une méthode de mise à jour comme **UploadFile**, **UploadText**, **UploadFromStream**ou **UploadByteArray**, car ces méthodes remplacent tout le contenu d’un objet blob.

![Ressources de stockage Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples d’utilisation du stockage Blob, consultez [Exemples de Code d’Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Vous pouvez télécharger un exemple d’application et l’exécuter ou parcourir le code de GitHub. 
