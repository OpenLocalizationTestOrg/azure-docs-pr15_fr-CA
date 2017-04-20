<properties
 pageTitle="Gérer l’expiration de contenu d’objet blob Azure Storage dans Azure CDN | Microsoft Azure"
 description="Obtenir des informations sur les options de contrôle de la durée de vie pour les objets BLOB dans la mise en cache d’Azure CDN."
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# <a name="manage-expiration-of-azure-storage-blob-content-in-azure-cdn"></a>Gérer l’expiration de contenu d’objet blob Azure Storage dans Azure CDN

> [AZURE.SELECTOR]
- [Services d’applications/Cloud Azure Web, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Service d’objet blob stockage Azure](cdn-manage-expiration-of-blob-content.md)

Le [service d’objet blob](../storage/storage-introduction.md#blob-storage) dans le [Stockage Azure](../storage/storage-introduction.md) est une de plusieurs origines basée sur Azure intégrées avec Azure CDN.  Tout contenu accessible publiquement blob peut être mis en cache dans Azure CDN jusqu'à expiration de sa durée de vie (TTL).  La durée de vie est déterminée par l' [en-tête *Cache-Control* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) dans la réponse HTTP de stockage Azure.

>[AZURE.TIP] Vous pouvez choisir de ne définir aucune durée de vie sur un objet blob.  Dans ce cas, Azure CDN applique automatiquement une durée de vie de sept jours par défaut.
>
>Pour plus d’informations sur le fonctionnement d’Azure CDN pour accélérer l’accès à des objets BLOB et d’autres fichiers, consultez la [Vue d’ensemble de Azure CDN](./cdn-overview.md).
>
>Pour plus d’informations sur le service d’objet blob Azure Storage, consultez [Concepts de Service d’objet Blob](https://msdn.microsoft.com/library/dd179376.aspx). 

Ce didacticiel présente plusieurs méthodes que vous pouvez définir la durée de vie sur un objet blob dans le stockage Azure.  

## <a name="azure-powershell"></a>PowerShell Azure

[Azure PowerShell](../powershell-install-configure.md) est une des méthodes plus rapide et la plus puissantes pour administrer vos services Azure.  Utilisez le `Get-AzureStorageBlob` applet de commande pour obtenir une référence à l’objet blob, puis définissez la `.ICloudBlob.Properties.CacheControl` propriété. 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] Vous pouvez également utiliser PowerShell pour [gérer vos profils CDN et les points de terminaison](./cdn-manage-powershell.md).

## <a name="azure-storage-client-library-for-net"></a>Bibliothèque Client de stockage Azure pour .NET

Pour définir la durée de vie d’un objet blob à l’aide de .NET, utilisez la [Bibliothèque de Client de stockage Azure pour .NET](../storage/storage-dotnet-how-to-use-blobs.md) pour définir la propriété [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) .

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        
        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

>[AZURE.TIP] Il existe de nombreux exemples de code .NET dans les [Échantillons de stockage Blob Azure pour .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).

## <a name="other-methods"></a>Autres méthodes

- [Interface de ligne de commande Azure](../xplat-cli-install.md)

    Lors du chargement de l’objet blob, affectez à la propriété *cacheControl* en utilisant la `-p` basculer.  Cet exemple définit la durée de vie d’une heure (3 600 secondes).

    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```

- [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)

    Définir explicitement la propriété de *contrôle du cache blob x ms* sur une demande de [Placer un Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Placez la liste de blocage](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)ou [Définir les propriétés de Blob](https://msdn.microsoft.com/library/azure/ee691966.aspx) .

- Outils de gestion de stockage tiers

    Certains outils de gestion du stockage Azure tiers vous permettent de vous permet de définir la propriété *CacheControl* sur les objets BLOB. 

## <a name="testing-the-cache-control-header"></a>Test de l’en-tête *Cache-Control.*

Vous pouvez facilement vérifier la durée de vie de vos objets BLOB.  À l’aide [d’Outils de développement](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test que le blob est, y compris l’en-tête de réponse *Cache-Control de votre navigateur* .  Vous pouvez également utiliser un outil tel que **wget**, [Postman](https://www.getpostman.com/)ou [Fiddler](http://www.telerik.com/fiddler) pour examiner les en-têtes de réponse.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur l’en-tête *Cache-Control.*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
- [Découvrez comment gérer l’expiration de contenu de Service Cloud dans Azure CDN](./cdn-manage-expiration-of-cloud-service-content.md)

