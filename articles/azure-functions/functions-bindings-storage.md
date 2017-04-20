<properties
    pageTitle="Déclencheurs de fonctions et de liaisons pour le stockage Azure Azure | Microsoft Azure"
    description="Comprendre comment utiliser des déclencheurs de stockage Azure et les liaisons dans les fonctions d’Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="fonctions Azure, fonctions, traitement de l’événement, calcul dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-for-azure-storage"></a>Déclencheurs de fonctions et de liaisons pour le stockage Azure Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et les déclencheurs de stockage Azure de code et les liaisons dans les fonctions d’Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="storagequeuetrigger"></a>Déclencheur de file d’attente de stockage Azure

#### <a name="functionjson-for-storage-queue-trigger"></a>Function.JSON déclencheur de file d’attente de stockage

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de la fonction pour la file d’attente ou le message de la file d’attente. 
- `queueName`: Nom de la file d’attente à interroger. Pour les règles d’attribution de noms de la file d’attente, consultez [métadonnées et attribution de noms de files d’attente](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de stockage. Si vous laissez `connection` vide, le déclencheur fonctionnera avec la chaîne de connexion de stockage par défaut pour l’application de la fonction, qui est spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type`: Doit avoir la valeur *queueTrigger*.
- `direction`: Doit avoir la valeur *dans*. 

Exemple *function.json* d’un déclencheur de file d’attente de stockage :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### <a name="queue-trigger-supported-types"></a>Déclenchement de la file d’attente des types pris en charge

Le message de la file d’attente peut être désérialisé dans l’un des types suivants :

* Objet (à partir de JSON)
* Chaîne
* Tableau d’octets 
* `CloudQueueMessage`(C#) 

#### <a name="queue-trigger-metadata"></a>Métadonnées de déclencheur de file d’attente

Vous pouvez obtenir les métadonnées de la file d’attente dans votre fonction à l’aide de ces noms de variable :

* expirationTime
* insertionTime
* nextVisibleTime
* ID
* popReceipt
* dequeueCount
* queueTrigger (une autre manière pour récupérer le texte du message de file d’attente sous forme de chaîne)

Extrait de cet exemple de code C# et les journaux de file d’attente des métadonnées :

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### <a name="handling-poison-queue-messages"></a>Gestion des messages de la file d’attente de poison

Les messages dont le contenu provoque une fonction échec sont appelés des *messages incohérents*. Lorsque la fonction échoue, le message de la file d’attente n’est pas supprimé et finalement est récupérée encore une fois, entraînant le cycle doit être répétée. Le Kit de développement peut interrompre automatiquement le cycle après un nombre limité d’itérations, ou vous pouvez le faire manuellement.

Le Kit de développement appelle une fonction jusqu'à 5 fois pour traiter un message de la file d’attente. Si la cinquième tentative échoue, le message est déplacé vers une file d’attente poison.

La file d’attente poison est nommé *{originalqueuename}*-poison. Vous pouvez écrire une fonction pour traiter les messages de la file d’attente poison par leur enregistrement ou envoi d’une notification qu’attention manuelle est nécessaire. 

Si vous souhaitez gérer manuellement des messages incohérents, vous pouvez obtenir le nombre de fois qu’un message a été collecté pour le traitement en vérifiant `dequeueCount`.

## <a id="storagequeueoutput"></a>Liaison de sortie de file d’attente de stockage Azure

#### <a name="functionjson-for-storage-queue-output-binding"></a>liaison de sortie Function.JSON de file d’attente de stockage

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de la fonction pour la file d’attente ou le message de la file d’attente. 
- `queueName`: Nom de la file d’attente. Pour les règles d’attribution de noms de la file d’attente, consultez [métadonnées et attribution de noms de files d’attente](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de stockage. Si vous laissez `connection` vide, le déclencheur fonctionnera avec la chaîne de connexion de stockage par défaut pour l’application de la fonction, qui est spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type`: Doit être définie pour la *file d’attente*.
- `direction`: Doit avoir la valeur de *sortie*. 

Liaison qui utilise un déclencheur de la file d’attente et écrit un message de la file d’attente de sortie exemple *function.json* pour une file d’attente de stockage :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="queue-output-binding-supported-types"></a>Types de prise en charge de liaison de sortie de file d’attente

Le `queue` liaison peut sérialiser les types suivants d’un message de la file d’attente :

* Objet (`out T` en C#, crée un message avec un objet null si le paramètre est null lorsque la fonction se termine)
* Chaîne (`out string` dans C#, crée des messages de la file d’attente si la valeur du paramètre est non nulle lorsque la fonction se termine)
* Tableau d’octets (`out byte[]` dans C#, fonctionne comme chaîne) 
* `out CloudQueueMessage`(C#, fonctionne comme une chaîne) 

Dans C# vous pouvez également lier à `ICollector<T>` ou `IAsyncCollector<T>` où `T` est un des types pris en charge.

#### <a name="queue-output-binding-code-examples"></a>Exemples de code de liaison de sortie de file d’attente

Cet exemple de code C# écrit un message de file d’attente de sortie unique pour chaque message de la file d’attente d’entrée.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Cet exemple de code C# a écrit plusieurs messages à l’aide de `ICollector<T>` (utilisez `IAsyncCollector<T>` dans une fonction d’async) :

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a>Déclencheur de blob de stockage Azure

#### <a name="functionjson-for-storage-blob-trigger"></a>Function.JSON déclencheur de blob de stockage

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de la fonction de l’objet blob. 
- `path`: Un chemin d’accès qui spécifie le conteneur pour surveiller et éventuellement d’un modèle de dénomination des blob.
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de stockage. Si vous laissez `connection` vide, le déclencheur fonctionnera avec la chaîne de connexion de stockage par défaut pour l’application de la fonction, qui est spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type`: Doit avoir la valeur *blobTrigger*.
- `direction`: Doit avoir la valeur *dans*.

Exemple *function.json* pour un déclencheur de blob de stockage qui assure le suivi des objets BLOB qui est ajoutés au conteneur d’échantillons-éléments de travail :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### <a name="blob-trigger-supported-types"></a>Déclencheur de BLOB de types pris en charge

Le blob peut être désérialisé dans l’un des types suivants de fonctions de nœud ou C# :

* Objet (à partir de JSON)
* Chaîne

Dans les fonctions C#, vous pouvez également lier à un des types suivants :

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Autres types de désérialisation par [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

#### <a name="blob-trigger-c-code-example"></a>BLOB déclencheur C# exemple de code

Cet exemple de code C# enregistre le contenu de chaque objet blob qui est ajouté au conteneur surveillé.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### <a name="blob-trigger-name-patterns"></a>Modèles de nom de déclencheur de BLOB

Vous pouvez spécifier un modèle de dénomination des blob dans le `path` propriété. Par exemple :

```json
"path": "input/original-{name}",
```

Ce chemin d’accès permet de trouver un blob appelé *Blob1.txt de l’original* dans le conteneur *d’entrée* et la valeur de la `name` variable dans le code de la fonction serait `Blob1`.

Un autre exemple :

```json
"path": "input/{blobname}.{blobextension}",
```

Ce chemin d’accès de recherche également un blob appelé *Blob1.txt de l’original*et que la valeur de la `blobname` et `blobextension` les variables dans le code de la fonction serait *D’origine-Blob1* et *txt*.

Vous pouvez limiter les types d’objets BLOB qui déclenche la fonction en spécifiant un motif avec une valeur fixe pour l’extension de fichier. Si vous définissez la `path` *d'/échantillons / {nom} .png*, *.png* uniquement des objets BLOB dans le conteneur *d’échantillons* déclenche la fonction.

Si vous devez spécifier un modèle de nom pour les noms de blob qui contient des accolades dans le nom, double-cliquez sur les accolades. Par exemple, si vous souhaitez trouver des objets BLOB dans le conteneur *d’images* qui ont des noms comme suit :

        {20140101}-soundfile.mp3

Utilisez cette option pour le `path` propriété :

        images/{{20140101}}-{name}

Dans l’exemple, le `name` valeur de la variable serait *soundfile.mp3*. 

#### <a name="blob-receipts"></a>Réceptions de BLOB

L’exécution de fonctions d’Azure permet de s’assurer qu’aucune fonction de déclenchement blob n’est appelée plusieurs fois pour le même objet blob de nouveau ou mis à jour. Il procède en maintenant un *blob reçus* afin de déterminer si une version de blob donné a été traitée.

Réceptions de BLOB sont stockées dans un conteneur nommé *azure-webjobs-hôtes* dans le compte de stockage Azure spécifié par la chaîne de connexion AzureWebJobsStorage. Un reçu de blob contient les informations suivantes :

* La fonction a été appelée pour l’objet blob («*{nom de la fonction app}*. Fonctions. *{nom de la fonction}*», par exemple : « functionsf74b96f7. Functions.CopyBlob »)
* Le nom du conteneur
* Le type de blob (« BlockBlob » ou « PageBlob »)
* Le nom de l’objet blob
* L’ETag (un identificateur de version de blob, par exemple : « 0x8D1DC6E70A277EF »)

Si vous souhaitez forcer le retraitement d’un objet blob, vous pouvez supprimer manuellement la réception blob pour ce blob à partir du conteneur *d’azure-webjobs-hôtes* .

#### <a name="handling-poison-blobs"></a>Traitement des objets BLOB poison

En cas d’échec d’une fonction de déclenchement blob, le Kit de développement appelle à nouveau, dans le cas où l’erreur a été provoquée par une erreur temporaire. Si l’échec est causé par le contenu de l’objet blob, la fonction échoue à chaque fois qu’il essaie de traiter l’objet blob. Par défaut, le Kit de développement appelle une fonction jusqu'à 5 fois pour un blob donné. Si la cinquième tentative échoue, le Kit de développement logiciel ajoute un message à une file d’attente nommée *webjobs-blobtrigger-poison*.

Le message de la file d’attente pour les objets BLOB poison est un objet JSON qui contient les propriétés suivantes :

* FunctionId (dans le format *{nom app de fonction}*. Fonctions. *{nom de la fonction}*)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* ETag (un identificateur de version de blob, par exemple : « 0x8D1DC6E70A277EF »)

#### <a name="blob-polling-for-large-containers"></a>BLOB d’interrogation pour les grands conteneurs

Si le conteneur d’objet blob qui surveille le déclencheur contient plus de 10 000 objets de BLOB, les analyses de runtime de fonctions journaux pour surveiller les blobs nouvelles ou modifiées. Ce processus n’est pas en temps réel ; une fonction ne peut pas déclenchée tant que quelques minutes ou plus après la création de l’objet blob. En outre, la base du [stockage, les journaux sont créés sur un « les efforts déployés »](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; Il n’y a aucune garantie que tous les événements seront capturés. Sous certaines conditions, les journaux peuvent être manqués. Si les limitations de vitesse et la fiabilité des déclencheurs de blob pour les grands conteneurs ne sont pas acceptables pour votre application, la méthode recommandée est de créer un message de la file d’attente lorsque vous créez l’objet blob et que vous utilisez un déclencheur de la file d’attente au lieu d’un déclencheur de blob pour traiter l’objet blob.
 
## <a id="storageblobbindings"></a>Blob de stockage Azure d’entrée et de sortie des liaisons

#### <a name="functionjson-for-a-storage-blob-input-or-output-binding"></a>Function.JSON pour un objet blob de stockage d’entrée ou de sortie de liaison

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de la fonction de l’objet blob. 
- `path`: Un chemin d’accès qui spécifie le conteneur pour le blob à partir de lire ou d’écrire le blob dans et éventuellement d’un modèle de dénomination des blob.
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de stockage. Si vous laissez `connection` vide, la liaison fonctionnera avec la chaîne de connexion de stockage par défaut pour l’application de la fonction, qui est spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type`: Doit avoir la valeur *blob*.
- `direction`: La valeur *entrée ou de *sortie** . 

Exemple *function.json* pour un stockage blob d’entrée ou de sortie de liaison, à l’aide d’un déclencheur de la file d’attente pour copier un objet blob :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="blob-input-and-output-supported-types"></a>BLOB d’entrée et de sortie des types pris en charge

Le `blob` liaison peut sérialiser ou désérialiser des types suivants de fonctions Node.js ou C# :

* Objet (`out T` dans C# pour le blob de sortie : crée un blob en tant qu’objet null si la valeur du paramètre est null lorsque la fonction se termine)
* Chaîne (`out string` dans C# pour le blob de sortie : crée un objet blob que si le paramètre de chaîne n’est pas null lorsque la fonction retourne une valeur)

Dans les fonctions C#, vous pouvez également lier aux types suivants :

* `TextReader`(entrée uniquement)
* `TextWriter`(sortie uniquement)
* `Stream`
* `CloudBlobStream`(sortie uniquement)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### <a name="blob-output-c-code-example"></a>BLOB sortie C# exemple de code

Cet exemple de code C# copie un blob dont le nom est reçu dans un message de la file d’attente.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a>Tables de stockage Azure d’entrée et de sortie des liaisons

#### <a name="functionjson-for-storage-tables"></a>Function.JSON pour les tables de stockage

Le *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de la fonction pour la liaison de la table. 
- `tableName`: Le nom de la table.
- `partitionKey`et `rowKey` : conjointement en une entité unique dans une fonction C# ou un nœud ou d’écrire une entité unique dans une fonction de nœud.
- `take`: Le nombre maximal de lignes à lire pour la table d’entrée dans une fonction de nœud.
- `filter`: OData expression de filtre pour la table d’entrée dans une fonction de nœud.
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de stockage. Si vous laissez `connection` vide, la liaison fonctionnera avec la chaîne de connexion de stockage par défaut pour l’application de la fonction, qui est spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type`: Doit être définie à la *table*.
- `direction`: La valeur *entrée ou de *sortie** . 

L' exemple suivant *function.json* utilise un déclencheur de la file d’attente pour lire une seule ligne du tableau. Le JSON fournit une valeur de clé codée de manière irréversible la partition et spécifie que la clé de ligne provient du message de la file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="storage-tables-input-and-output-supported-types"></a>Tables de stockage d’entrée et de sortie des types pris en charge

Le `table` liaison peut sérialiser ou désérialiser des objets dans des fonctions Node.js ou C#. Les objets aura les propriétés RowKey et PartitionKey. 

Dans les fonctions C#, vous pouvez également lier aux types suivants :

* `T`où `T` implémente`ITableEntity`
* `IQueryable<T>`(entrée uniquement)
* `ICollector<T>`(sortie uniquement)
* `IAsyncCollector<T>`(sortie uniquement)

#### <a name="storage-tables-binding-scenarios"></a>Stockage des scénarios de liaison de tables

La liaison de table prend en charge les scénarios suivants :

* Lire une seule ligne dans une fonction C# ou de nœud.

    La valeur `partitionKey` et `rowKey`. La `filter` et `take` propriétés ne sont pas utilisées dans ce scénario.

* Lecture de plusieurs lignes dans une fonction C#.

    Le fonctions runtime fournit une `IQueryable<T>` objet lié à la table. Type de `T` doit dériver de `TableEntity` ou implémenter `ITableEntity`. Le `partitionKey`, `rowKey`, `filter`, et `take` propriétés ne sont pas utilisées dans ce scénario ; Vous pouvez utiliser la `IQueryable` objet d’effectuer un filtrage requis. 

* Lecture de plusieurs lignes dans une fonction de nœud.

    Définir la `filter` et `take` propriétés. Ne définissez pas de `partitionKey` ou `rowKey`.

* Écrivez une ou plusieurs lignes dans une fonction C#.

    Le fonctions runtime fournit une `ICollector<T>` ou `IAsyncCollector<T>` lié à la table, où `T` Spécifie le schéma des entités que vous souhaitez ajouter. En général, tapez `T` dérive de `TableEntity` ou implémente `ITableEntity`, mais il ne doit pas. Le `partitionKey`, `rowKey`, `filter`, et `take` propriétés ne sont pas utilisées dans ce scénario.

#### <a name="storage-tables-example-read-a-single-table-entity-in-c-or-node"></a>Exemple de tables de stockage : lecture d’une entité de table unique dans C# ou nœud

L’exemple de code C# suivant fonctionne avec le précédent fichier de *function.json* décrite précédemment pour lire une entité de table unique. Le message de la file d’attente a la valeur de clé de ligne et l’entité de la table est lues dans un type qui est défini dans le fichier *run.csx* . Le type inclut `PartitionKey` et `RowKey` propriétés et ne dérive pas de `TableEntity`. 

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

L’exemple de code suivant F# fonctionne également avec le fichier *function.json* précédent en une entité unique table.

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

L’exemple de code suivant nœud fonctionne également avec le fichier *function.json* précédent en une entité unique table.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### <a name="storage-tables-example-read-multiple-table-entities-in-c"></a>Exemple de tables de stockage : lire plusieurs entités de tableau en C# 

Les *function.json* et suivantes C# code exemple des entités de lectures pour une clé de partition qui est spécifié dans le message de la file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Le code C# ajoute une référence dans le Kit de développement de stockage Azure afin que le type d’entité peut dériver de `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### <a name="storage-tables-example-create-table-entities-in-c"></a>Exemple de tables de stockage : créer les entités de table en C# 

L’exemple suivant de *function.json* et de *run.csx* montre comment écrire les entités de table dans C#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### <a name="storage-tables-example-create-table-entities-in-f"></a>Exemple de tables de stockage : créer les entités de table f#

L’exemple suivant de *function.json* et de *run.fsx* montre comment écrire les entités de table dans F#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### <a name="storage-tables-example-create-a-table-entity-in-node"></a>Exemple de tables de stockage : créer une table d’entité dans le nœud

L’exemple suivant de *function.json* et de *run.csx* montre comment écrire une entité de la table dans le nœud.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
