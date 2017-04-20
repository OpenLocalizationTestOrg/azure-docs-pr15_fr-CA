<properties
    pageTitle="Référence du développeur de fonctions Azure | Microsoft Azure"
    description="Comprendre comment développer des fonctions Azure à l’aide de C#."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="dotnet"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-c-developer-reference"></a>Référence du développeur Azure fonctions C#

> [AZURE.SELECTOR]
- [Script C#](../articles/azure-functions/functions-reference-csharp.md)
- [Script de F#](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)
 
L’expérience C# pour des fonctions d’Azure est basé sur le Kit de développement logiciel WebJobs Azure. Flux de données dans votre fonction C# via les arguments de la méthode. Noms d’arguments sont spécifiés dans `function.json`, et il existe des noms prédéfinis pour l’accès à des éléments tels que les jetons de journal et de l’annulation de fonction.

Cet article suppose que vous avez déjà lu la [référence du développeur les fonctions Azure](functions-reference.md).

## <a name="how-csx-works"></a>Fonctionnement de .csx

Le `.csx` format permet d’écrire moins « standard » et de se concentrer sur l’écriture de seulement une fonction C#. Pour inclure des fonctions d’Azure, que vous les références d’assembly et les espaces de noms vous devez haut haut, comme d’habitude, et au lieu d’encapsuler tous les éléments dans un espace de noms et une classe, vous pouvez simplement définir votre `Run` méthode. Si vous devez inclure toutes les classes, par exemple pour définir les objets POCO, vous pouvez inclure une classe à l’intérieur du même fichier.

## <a name="binding-to-arguments"></a>Liaison aux arguments

Les diverses liaisons sont liés à une fonction C# via le `name` propriété dans la configuration de *function.json* . Chaque liaison possède ses propres types pris en charge, qui est décrite par la liaison ; par exemple, un déclencheur de blob peut prendre en charge une chaîne, un POCO ou plusieurs autres types. Vous pouvez utiliser le type qui correspond le mieux à vos besoins. 

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

## <a name="logging"></a>Enregistrement dans le journal

Pour consigner les résultats dans vos journaux de transmission en continu dans C#, vous pouvez inclure un `TraceWriter` argument de type. Nous vous conseillons de nommer il `log`. Nous vous recommandons de vous évitez de `Console.Write` dans les fonctions d’Azure.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>Async

Pour rendre une fonction asynchrone, utilisez la `async` mot clé et retournent un `Task` objet.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>Jeton d’annulation

Dans certains cas, vous pouvez avoir des opérations sensibles à en cours d’arrêt. Alors qu’il est toujours préférable d’écrire du code qui peut gérer la défaillance, dans les cas où vous souhaitez gérer l’arrêt progressif des demandes, vous définissez un [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argument de type.  A `CancellationToken` seront fournies si un arrêt de l’hôte est déclenché. 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>Importez les espaces de noms

Si vous devez importer les espaces de noms, vous pouvez le faire donc comme d’habitude avec la `using` clause.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Espaces de noms suivants sont automatiquement importés et sont donc facultatifs :

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Référencement des assemblys externes

Pour les assemblys framework, ajoutez des références à l’aide de la `#r "AssemblyName"` la directive.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Les assemblys suivants sont ajoutés automatiquement par les fonctions d’Azure environnement d’hébergement :

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

En outre, les assemblys suivants sont spéciales la casse et peuvent être référencées par simplename (par exemple, `#r "AssemblyName"`) :

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

Si vous avez besoin de référencer un assembly privé, vous pouvez télécharger le fichier d’assembly dans une `bin` dossier par rapport à votre fonction de référence et en utilisant le fichier de nom (par exemple :  `#r "MyAssembly.dll"`). Pour plus d’informations sur la façon de télécharger des fichiers dans votre dossier de fonction, consultez la section suivante sur la gestion des packages.

## <a name="package-management"></a>Gestion des packages

Pour utiliser les packages NuGet dans une fonction C#, télécharger un fichier de *project.json* vers le dossier de la fonction dans le système de fichiers de l’application de la fonction. Voici un exemple de fichier *project.json* qui ajoute une référence à Microsoft.ProjectOxford.Face version 1.1.0 :

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Uniquement le 4.6 du.NET Framework est pris en charge, assurez-vous que votre fichier *project.json* spécifie `net46` comme illustré ici.

Lorsque vous téléchargez un fichier *project.json* , le runtime Obtient les packages et ajoute automatiquement des références aux assemblys de package. Vous n’avez pas besoin d’ajouter `#r "AssemblyName"` directives. Il suffit d’ajouter le `using` instructions dans votre fichier *run.csx* pour utiliser les types définis dans les packages NuGet.


### <a name="how-to-upload-a-projectjson-file"></a>Comment faire pour transférer un fichier de project.json

1. Début, en vous assurant que votre application de la fonction est en cours d’exécution, ce que vous pouvez faire en ouvrant votre fonction dans le portail Azure. 

    Ceci donne également accès aux journaux en continu où la sortie d’installation de package s’affichera. 

2. Pour télécharger un fichier project.json, utilisez une des méthodes décrites dans la section de **la mise à jour des fichiers d’application de fonction** de la [rubrique de référence du développeur les fonctions Azure](functions-reference.md#fileupdate). 

3. Une fois le fichier *project.json* est téléchargé, vous voyez la sortie comme dans l’exemple suivant dans votre fonction de diffusion en continu de journal :

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261 
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variables d’environnement

Pour obtenir une variable d’environnement ou d’une application, la définition de valeur, utilisez `System.Environment.GetEnvironmentVariable`, comme illustré dans l’exemple de code suivant :

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " + 
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

## <a name="reusing-csx-code"></a>Réutilisation du code .csx

Vous pouvez utiliser les classes et les méthodes définies dans d’autres fichiers *.csx* dans votre fichier *run.csx* . Pour ce faire, utilisez `#load` directives dans le fichier *run.csx* , comme illustré dans l’exemple suivant.

Exemple *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exemple *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

Vous pouvez utiliser un chemin d’accès relatif avec le `#load` la directive :

* `#load "mylogger.csx"`charge un fichier situé dans le dossier de la fonction.

* `#load "loadedfiles\mylogger.csx"`charge un fichier situé dans un dossier dans le dossier de la fonction.

* `#load "..\shared\mylogger.csx"`charge un fichier situé dans un dossier au même niveau que le dossier de fonction, c'est-à-dire, directement sous *wwwroot*.
 
Le `#load` directive fonctionne uniquement avec les fichiers *.csx* (script C#), et non avec les fichiers *.cs* . 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Référence du développeur de fonctions Azure](functions-reference.md)
* [Référence du développeur Azure fonctions F#](functions-reference-fsharp.md)
* [Référence du développeur de fonctions NodeJS Azure](functions-reference-node.md)
* [Les liaisons et les déclencheurs de fonctions azure](functions-triggers-bindings.md)

