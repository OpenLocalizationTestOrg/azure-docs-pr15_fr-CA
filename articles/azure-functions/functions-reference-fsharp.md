<properties
    pageTitle="Référence du développeur Azure fonctions F# | Microsoft Azure"
    description="Comprendre comment développer des fonctions Azure à l’aide de F#."
    services="functions"
    documentationCenter="fsharp"
    authors="sylvanc"
    manager="jbronsk"
    editor=""
    tags=""
    keywords="Azure fonctions, les fonctions, les événements de traitement, webhooks, calcul dynamique sans serveur architecture, F#"/>

<tags
    ms.service="functions"
    ms.devlang="fsharp"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/09/2016"
    ms.author="syclebsc"/>

# <a name="azure-functions-f-developer-reference"></a>Référence du développeur F# fonctions Azure

> [AZURE.SELECTOR]
- [Script C#](../articles/azure-functions/functions-reference-csharp.md)
- [Script de F#](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)

F# pour les fonctions d’Azure est une solution pour exécuter facilement des petits morceaux de code, ou « fonctions », dans le nuage. Flux de données dans votre fonction F# de via les arguments de la fonction. Noms d’arguments sont spécifiés dans `function.json`, et il existe des noms prédéfinis pour l’accès à des éléments tels que les jetons de journal et de l’annulation de fonction.

Cet article suppose que vous avez déjà lu la [référence du développeur les fonctions Azure](functions-reference.md).

## <a name="how-fsx-works"></a>Mode de fonctionnement de .fsx

Un `.fsx` fichier est un script F#. Il peut être considérée comme un projet de F# qui est contenu dans un seul fichier. Le fichier contient à la fois le code de votre programme (dans ce cas, votre fonction Azure) et des directives pour la gestion des dépendances.

Lorsque vous utilisez un `.fsx` pour une fonction d’Azure, généralement requis assemblys sont inclus automatiquement, vous permettant ainsi de vous concentrer sur le code de la fonction au lieu de « standard ».

## <a name="binding-to-arguments"></a>Liaison aux arguments

Chaque liaison prend en charge un ensemble d’arguments, comme détaillé dans les [fonctions d’Azure déclencheurs et la référence du développeur de liaisons](functions-triggers-bindings.md). Par exemple, un déclencheur de blob prend en charge les liaisons d’argument est un POCO, qui peut être exprimé à l’aide d’un enregistrement F#. Par exemple :

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Votre fonction d’Azure F# prendra un ou plusieurs arguments. Lorsque nous parlons d’arguments de fonctions d’Azure, nous faisons référence à des arguments _d’entrée_ et les arguments de _sortie_ . Un argument d’entrée est exactement ce qu’il ressemble : entrée à votre fonction d’Azure F#. Un argument de _sortie_ est données mutables ou un `byref<>` argument qui est utilisé comme un moyen de passer données retour _arrière_ de votre fonction.

Dans l’exemple ci-dessus, `blob` est un argument d’entrée, et `output` est un argument de sortie. Notez que nous avons utilisé `byref<>` de `output` (il n’y a aucun besoin d’ajouter le `[<Out>]` annotation). En utilisant un `byref<>` type permet de modifier les enregistrement ou un objet, l’argument fait référence à votre fonction.

Lorsqu’un enregistrement F# est utilisé comme un type d’entrée, la définition d’enregistrement doit être marquée avec `[<CLIMutable>]` afin de permettre à l’infrastructure Azure fonctions définir les champs de façon appropriée avant de transmettre l’enregistrement de votre fonction. Dans les coulisses, `[<CLIMutable>]` génère des accesseurs Set pour les propriétés de l’enregistrement. Par exemple :

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Une classe F# peut également être utilisée pour les arguments et. Pour une classe, les propriétés seront habituellement getters et setters. Par exemple :

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Enregistrement dans le journal

Pour consigner le résultat dans votre [diffusion en continu de journaux](../app-service-web/web-sites-streaming-logs-and-console.md) dans F#, votre fonction doit prendre un argument de type `TraceWriter`. Par souci de cohérence, nous vous recommandons de cet argument est nommé `log`. Par exemple :

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async

Le `async` workflow peut être utilisé, mais le résultat doit retourner un `Task`. Cela peut être fait avec `Async.StartAsTask`, par exemple :

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Jeton d’annulation

Si votre fonction doit gérer harmonieusement les arrêt, vous pouvez lui attribuer un [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argument. Cela peut être combiné avec `async`, par exemple :

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importez les espaces de noms

Espaces de noms peuvent être ouverts de la façon habituelle :

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Espaces de noms suivants sont automatiquement ouverts :

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Référencement des assemblys externes

De même, l’assembly framework références ajoutées avec la `#r "AssemblyName"` la directive.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
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
* `Microsoft.AspNEt.WebHooks.Common`.

Si vous avez besoin de référencer un assembly privé, vous pouvez télécharger le fichier d’assembly dans une `bin` dossier par rapport à votre fonction de référence et en utilisant le fichier de nom (par exemple :  `#r "MyAssembly.dll"`). Pour plus d’informations sur la façon de télécharger des fichiers dans votre dossier de fonction, consultez la section suivante sur la gestion des packages.

## <a name="editor-prelude"></a>Éditeur de prélude

Un éditeur qui prend en charge les Services de compilateur F# ne sera pas conscient des espaces de noms et assemblys incluant les fonctions Azure automatiquement. En tant que tel, il peut être utile d’inclure une étape préliminaire qui permet de rechercher les assemblys que vous utilisez l’éditeur et d’ouvrir explicitement les espaces de noms. Par exemple :

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open Sytem
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Lorsque les fonctions Azure exécute votre code, il traite la source avec `COMPILED` définis, l’éditeur prélude sera ignoré.

## <a name="package-management"></a>Gestion des packages

Pour utiliser les packages NuGet dans une fonction F#, ajouter un `project.json` de fichiers vers le dossier de la fonction dans le système de fichiers de l’application de la fonction. Voici un exemple `project.json` fichier qui ajoute une référence de package NuGet à `Microsoft.ProjectOxford.Face` version 1.1.0 :

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

Uniquement le 4.6 du.NET Framework est pris en charge, assurez-vous que votre `project.json` fichier spécifie `net46` comme illustré ici.

Lorsque vous téléchargez un `project.json` fichier, le runtime Obtient les packages et ajoute automatiquement des références aux assemblys de package. Vous n’avez pas besoin d’ajouter `#r "AssemblyName"` directives. Il suffit d’ajouter le `open` instructions à votre `.fsx` fichier.

Vous souhaiterez peut-être placer des assemblys de référence automatiquement dans votre éditeur prélude, afin d’améliorer l’interaction de votre éditeur avec des Services de compilation F#.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>L’ajout d’un `project.json` le fichier à votre fonction d’Azure

1. Début, en vous assurant que votre application de la fonction est en cours d’exécution, ce que vous pouvez faire en ouvrant votre fonction dans le portail Azure. Ceci donne également accès aux journaux en continu où la sortie d’installation de package s’affichera.

2. Pour télécharger un `project.json` de fichiers, utilisez une des méthodes décrites dans [la mise à jour des fichiers d’application de fonction](functions-reference.md#fileupdate). Si vous utilisez [Un déploiement continu pour les fonctions d’Azure](functions-continuous-deployment.md), vous pouvez ajouter un `project.json` le fichier à votre succursale intermédiaire pour tester avant de l’ajouter à la branche de votre déploiement.

3. Après le `project.json` fichier est ajouté, vous verrez une sortie similaire à l’exemple suivant dans votre fonction de diffusion en continu de journal :

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

Pour obtenir une variable d’environnement ou d’une application, la définition de valeur, utilisez `System.Environment.GetEnvironmentVariable`, par exemple :

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Réutilisation du code .fsx

Vous pouvez utiliser le code des autres `.fsx` fichiers à l’aide d’un `#load` la directive. Par exemple :

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Fournit des chemins d’accès à la `#load` sont la directive relative à l’emplacement de votre `.fsx` fichier.

* `#load "logger.fsx"`charge un fichier situé dans le dossier de la fonction.

* `#load "package\logger.fsx"`charge un fichier situé dans le `package` dossier dans le dossier de la fonction.

* `#load "..\shared\mylogger.fsx"`charge un fichier situé dans le `shared` dossier au même niveau que le dossier de fonction, c'est-à-dire, directement sous `wwwroot`.

Le `#load` la directive fonctionne uniquement avec les `.fsx` les fichiers (script F#) et non pas avec `.fs` fichiers.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Guide de F#](https://docs.microsoft.com/en-us/dotnet/articles/fsharp/index)
* [Référence du développeur de fonctions Azure](functions-reference.md)
* [Référence du développeur Azure fonctions C#](functions-reference-csharp.md)
* [Référence du développeur de fonctions NodeJS Azure](functions-reference-node.md)
* [Les liaisons et les déclencheurs de fonctions azure](functions-triggers-bindings.md)
* [Test de fonctions Azure](functions-test-a-function.md)
* [Fonctions Azure mise à l’échelle](functions-scale.md)
