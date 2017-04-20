<properties
 pageTitle="Importer exporter des identités des appareils IoT Hub | Microsoft Azure"
 description="Concepts et .NET code extraits pour la gestion d’en bloc des identités des appareils IoT concentrateur"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

# <a name="bulk-management-of-iot-hub-device-identities"></a>Bloc de gestion des identités de périphérique IoT concentrateur

Chaque concentrateur IoT a un registre d’identité de périphérique vous permet de créer des ressources de périphérique dans le service, comme une file d’attente contient des messages en vol de nuage vers le périphérique. Le Registre d’identité de périphérique vous permet également de contrôler l’accès aux points de terminaison orientés vers le périphérique. Cet article décrit comment importer et exporter les identités des appareils en vrac vers et à partir d’un registre d’identité de périphérique.

Importer et exporter les opérations ont lieu dans le contexte des *tâches* qui vous permettent d’exécuter des opérations de service en bloc par rapport à un concentrateur IoT.

La classe **RegistryManager** inclut les **ExportDevicesAsync** et les **ImportDevicesAsync** les méthodes qui utilisent l’infrastructure de **travail** . Ces méthodes permettent d’exporter, importer et synchroniser l’intégralité d’un Registre de périphérique du concentrateur IoT.

## <a name="what-are-jobs"></a>Quelles sont les tâches ?

Opérations de périphérique identité du Registre utilisent le système de **projet** lors de l’opération :

*  A un temps d’exécution peut être long par rapport aux opérations de runtime standard, ou
*  Retourne une grande quantité de données à l’utilisateur.

Dans ces cas, au lieu d’un seul appel d’API en attente ou blocage sur le résultat de l’opération, l’opération asynchrone ne crée une **tâche** de ce concentrateur IoT. L’opération puis renvoie immédiatement un objet **JobProperties** .

L’extrait de code C# suivant montre comment créer une tâche d’exportation :

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

Puis, vous pouvez utiliser la classe **RegistryManager** pour demander l’état de la **tâche** à l’aide de la **JobProperties** de métadonnées retourné.

L’extrait de code C# suivant montre comment interroger toutes les cinq secondes pour voir si la tâche est terminée :

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Exporter des périphériques

Utilisez la méthode **ExportDevicesAsync** pour exporter l’intégralité d’un Registre de périphérique du concentrateur IoT à un conteneur d’objet blob [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) à l’aide d’une [Signature de l’accès partagé](https://msdn.microsoft.com/library/ee395415.aspx).

Cette méthode vous permet de créer des sauvegardes fiables des informations de votre appareil dans un conteneur d’objet blob que vous contrôlez.

La méthode **ExportDevicesAsync** requiert deux paramètres :

*  *Chaîne* qui contient l’URI d’un conteneur d’objet blob. Cet URI doit contenir un jeton d’associations de sécurité qui accorde un accès en écriture au conteneur. Le traitement crée un blob de bloc dans ce conteneur pour stocker les données de périphérique exportation sérialisé. Le jeton SAS doit inclure ces autorisations :
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  *Boolean* qui indique si vous souhaitez exclure des clés d’authentification de vos données d’exportation. Si la **valeur false**, l’authentification de clés sont inclus dans l’exportation de sortie ; dans le cas contraire, les clés sont exportées comme **null**.

L’extrait de code C# suivant montre comment lancer une tâche d’exportation qui contient les clés d’authentification de dispositif dans les données d’exportation et puis interroger fin :

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

Le projet stocke sa sortie dans le conteneur de l’objet blob fourni sous la forme d’un blob de bloc avec le nom **devices.txt**. Les données de sortie se composent de données de périphérique JSON sérialisée, avec un seul périphérique par ligne.

L’exemple suivant montre les données de sortie :

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Si vous avez besoin d’accès à ces données dans le code, vous pouvez facilement désérialiser ces données à l’aide de la classe **ExportImportDevice** . L’extrait de code C# suivant montre comment lire des informations sur le périphérique qui a été précédemment exportées à un blob de bloc :

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  Vous pouvez également utiliser la méthode **GetDevicesAsync** de la classe **RegistryManager** pour extraire une liste de vos périphériques. Toutefois, cette approche a un couvercle dur à 1000 sur le nombre d’objets de périphérique qui sont retournées. Le cas de prévoir l’utilisation de la méthode **GetDevicesAsync** est pour les scénarios de développement faciliter le débogage et n’est pas recommandé pour les charges de travail.

## <a name="import-devices"></a>Périphériques d’importation

La méthode **ImportDevicesAsync** dans la classe **RegistryManager** vous permet d’effectuer les opérations de synchronisation et d’importation en bloc dans un Registre de périphérique du concentrateur IoT. Comme la méthode **ExportDevicesAsync** , la méthode **ImportDevicesAsync** utilise le cadre de la **tâche** .

Prendre en charge à l’aide de la méthode **ImportDevicesAsync** , car en plus de la mise en service de nouveaux périphériques dans votre Registre d’identité de périphérique, il peut également mettre à jour et supprimer des périphériques existants.

> [AZURE.WARNING]  Une opération d’importation ne peut pas être annulée. Toujours sauvegarder vos données existantes à l’aide de la méthode **ExportDevicesAsync** vers un autre conteneur blob avant d’apporter des modifications en bloc dans le Registre d’identité de périphérique.

La méthode **ImportDevicesAsync** prend deux paramètres :

*  *Chaîne* qui contient l’URI d’un conteneur d’objet blob [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) à utiliser comme *entrée* pour la tâche. Cet URI doit contenir un jeton d’associations de sécurité qui accorde l’accès en lecture au conteneur. Ce conteneur doit contenir un objet blob avec le nom **devices.txt** qui contient les données sérialisées de dispositif à importer dans votre Registre d’identité de périphérique. Les données d’importation doivent contenir des informations de périphérique dans le même format JSON qui le travail **ExportImportDevice** utilise lorsqu’il crée un objet blob de **devices.txt** . Le jeton SAS doit inclure ces autorisations :

    ```
    SharedAccessBlobPermissions.Read
    ```

*  *Chaîne* qui contient l’URI d’un conteneur d’objet blob [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) à utiliser comme *sortie* de la tâche. Le traitement crée un blob de bloc dans ce conteneur pour stocker des informations sur l’erreur dans la **travail**d’importation. Le jeton SAS doit inclure ces autorisations :
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  Les deux paramètres peuvent pointer vers le même conteneur d’objet blob. Les paramètres distincts permettent simplement plus de contrôle sur les données comme le conteneur de sortie nécessite d’autres autorisations.

L’extrait de code C# suivant montre comment lancer une tâche d’importation :

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## <a name="import-behavior"></a>Comportement d’importation

Vous pouvez utiliser la méthode **ImportDevicesAsync** pour effectuer des opérations en bloc suivantes dans le Registre d’identité de périphérique :

-   Inscription en bloc de nouveaux périphériques
-   Suppressions en masse des périphériques existants
-   En bloc les modifications d’état (activer ou désactiver des périphériques)
-   Affectation en vrac de nouvelles clés d’authentification de dispositif
-   En vrac auto-régénération de clés d’authentification de dispositif

Vous pouvez effectuer n’importe quelle combinaison des opérations précédentes au sein d’un seul appel de **ImportDevicesAsync** . Par exemple, vous pouvez enregistrer de nouveaux périphériques et supprimer ou mettre à jour des périphériques existants en même temps. Lorsqu’elle est utilisée avec la méthode **ExportDevicesAsync** , vous pouvez complètement migrer tous vos équipements à partir d’un concentrateur IoT à un autre.

Utilisez la propriété facultatif **importMode** dans les données de sérialisation d’importation pour chaque périphérique pour contrôler le processus par-périphérique d’importation. La propriété **importMode** possède les options suivantes :

| importMode |  Description |
| -------- | ----------- |
| **createOrUpdate** | Si un périphérique n’existe pas avec l' **id**spécifié, il est nouvellement enregistré. <br/>Si le périphérique existe déjà, les informations existantes sont remplacées par les données d’entrée fournies, quelle que soit la valeur **ETag** . |
| **créer** | Si un périphérique n’existe pas avec l' **id**spécifié, il est nouvellement enregistré. <br/>Si le périphérique existe déjà, une erreur est écrite dans le fichier journal. |
| **mise à jour** | Si un périphérique existe déjà avec l' **id**spécifié, les informations existantes sont remplacées par les données d’entrée fournies, quelle que soit la valeur **ETag** . <br/>Si le périphérique n’existe pas, une erreur est écrite dans le fichier journal. |
| **updateIfMatchETag** | Si un périphérique existe déjà avec l' **id**spécifié, les informations existantes sont remplacées par les données d’entrée fournies uniquement s’il existe une correspondance **ETag** . <br/>Si le périphérique n’existe pas, une erreur est écrite dans le fichier journal. <br/>S’il existe une incompatibilité de **ETag** , une erreur est écrite dans le fichier journal. |
| **createOrUpdateIfMatchETag** | Si un périphérique n’existe pas avec l' **id**spécifié, il est nouvellement enregistré. <br/>Si le périphérique existe déjà, les informations existantes sont remplacées par les données d’entrée fournies uniquement s’il existe une correspondance **ETag** . <br/>S’il existe une incompatibilité de **ETag** , une erreur est écrite dans le fichier journal. |
| **supprimer** | Si un périphérique existe déjà avec l' **id**spécifié, il est supprimé quelle que soit la valeur **ETag** . <br/>Si le périphérique n’existe pas, une erreur est écrite dans le fichier journal. |
| **deleteIfMatchETag** | Si un périphérique existe déjà avec l' **id**spécifié, il est supprimé uniquement s’il existe une correspondance **ETag** . Si le périphérique n’existe pas, une erreur est écrite dans le fichier journal. <br/>S’il existe une incompatibilité de ETag, une erreur est écrite dans le fichier journal. |

> [AZURE.NOTE] Si les données de sérialisation ne définissent pas explicitement un indicateur **importMode** pour un périphérique, il prend par défaut **createOrUpdate** pendant l’opération d’importation.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importer des exemple de périphériques – bloc de mise en service du dispositif 

L’exemple de code C# suivant illustre comment générer plusieurs identités de périphérique qui :

- Inclure des clés d’authentification.
- Écrire ces informations de périphérique dans un blob de bloc.
- Importez les périphériques dans le Registre d’identité de périphérique.

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Exemple de périphériques d’importation – suppression en bloc

L’exemple de code suivant vous montre comment supprimer les périphériques que vous avez ajouté à l’aide de l’exemple de code précédent :

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## <a name="getting-the-container-sas-uri"></a>Lors de l’obtention du conteneur SAS URI


L’exemple de code suivant vous montre comment générer un [URI d’associations de sécurité](../storage/storage-dotnet-shared-access-signature-part-2.md) avec les autorisations de lecture, écriture et supprimer pour un conteneur d’objet blob :

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer des opérations en bloc sur le Registre d’identité de périphérique dans un concentrateur IoT. Cliquez sur ces liens pour en savoir plus sur la gestion d’Azure IoT Hub :

- [Mesures de l’utilisation][lnk-metrics]
- [Surveillance des opérations][lnk-monitor]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md