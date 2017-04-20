<properties
    pageTitle="Les liaisons HTTP de fonctions et webhook Azure | Microsoft Azure"
    description="Comprendre comment utiliser des déclencheurs HTTP et de webhook et liaisons dans les fonctions d’Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande"/>

# <a name="azure-functions-http-and-webhook-bindings"></a>Liaisons HTTP de fonctions et webhook Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et code HTTP et webhook les déclencheurs et les liaisons dans les fonctions d’Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-http-and-webhook-bindings"></a>Function.JSON pour les liaisons HTTP et webhook

Le fichier *function.json* fournit des propriétés qui se rapportent à la demande et la réponse.

Propriétés de la requête HTTP :

- `name`: Nom variable utilisée dans le code de la fonction de l’objet de la demande (ou le corps de la demande pour les fonctions de Node.js).
- `type`: Doit avoir la valeur *httpTrigger*.
- `direction`: Doit avoir la valeur *dans*. 
- `webHookType`: Pour les triggers de la WebHook, les valeurs valides sont *github*, la *marge*et *genericJson*. Pour un déclencheur HTTP qui n’est pas d’un WebHook, définir cette propriété sur une chaîne vide. Pour plus d’informations sur WebHooks, consultez la section suivante de la [WebHook les déclencheurs](#webhook-triggers) .
- `authLevel`: Ne s’applique pas aux déclencheurs de WebHook. La valeur « function » pour demander la clé de l’API « anonyme » à l’exigence clé de l’API de dépôt, ou « admin » exiger la clé API principal. Pour plus d’informations, reportez-vous à la section sous les [clés de l’API](#apikeys) .

Propriétés de la réponse HTTP :

- `name`: Nom variable utilisée dans le code de la fonction de l’objet de réponse.
- `type`: Doit être définie à *http*.
- `direction`: Doit avoir la valeur de *sortie*. 
 
Exemple *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="webhook-triggers"></a>Déclencheurs de WebHook

Un déclencheur de WebHook est un HTTP qui présente les caractéristiques suivantes, conçues pour WebHooks :

* Pour les fournisseurs de WebHook spécifiques (actuellement GitHub et la marge sont pris en charge), le fonctions runtime valide les signatures de ce fournisseur.
* Pour les fonctions de Node.js, l’exécution de fonctions fournit le corps de la demande plutôt que l’objet de la demande. Il n’existe aucune gestion particulière pour les fonctions C#, car vous contrôler ce qui est fourni en spécifiant le type de paramètre. Si vous spécifiez `HttpRequestMessage` vous obtenez l’objet de la demande. Si vous spécifiez un type POCO, le fonctions runtime tente d’analyser un objet JSON dans le corps de la demande pour remplir les propriétés de l’objet.
* Pour déclencher un WebHook fonction de la demande HTTP doit inclure une clé API. Pour les triggers de non - WebHook HTTP, cette exigence est facultative.

Pour plus d’informations sur la façon de configurer un GitHub WebHook, reportez-vous à la section [GitHub Developer - création d’un WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409).

## <a name="url-to-trigger-the-function"></a>URL pour déclencher la fonction

Pour déclencher une fonction, vous envoyez une demande HTTP à une URL qui est une combinaison de l’URL d’application de fonction et le nom de la fonction :

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## <a name="api-keys"></a>Clés de l’API

Par défaut, une clé d’API doit être incluse dans une demande HTTP pour déclencher une fonction HTTP ou WebHook. La clé peut être incluse dans une variable de chaîne de requête nommée `code`, ou il peut être inclus dans une `x-functions-key` en-tête HTTP. Pour les fonctions non-WebHook, vous pouvez indiquer que la clé de l’API n’est pas requis en définissant le `authLevel` propriété « anonyme » dans le fichier *function.json* .

Vous pouvez trouver les valeurs de clé API dans le dossier *D:\home\data\Functions\secrets* du système de fichiers de l’application de la fonction.  La clé principale et la touche de fonction sont définies dans le fichier *host.json* , comme illustré dans cet exemple. 

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

La clé de fonction à partir de la *host.json* peut être utilisée pour déclencher une fonction, mais ne déclenche une fonction désactivée. La clé principale peut être utilisée pour déclencher une fonction et déclenchera une fonction, même s’il est désactivé. Vous pouvez configurer une fonction pour demander la clé principale en définissant le `authLevel` propriété sur « admin ». 

Si le dossier de *secrets* contient un fichier JSON avec le même nom qu’une fonction, le `key` propriété dans ce fichier peut également être utilisée pour déclencher la fonction, et cette clé fonctionne qu’avec la fonction qu’il désigne. Par exemple, la clé d’API pour une fonction nommée `HttpTrigger` est spécifié dans *HttpTrigger.json* dans le dossier des *secrets* . Voici un exemple :

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Lorsque vous définissez un déclencheur WebHook, avec le fournisseur WebHook ne partagent pas la clé principale. Utiliser une clé qui fonctionne qu’avec la fonction qui traite de la WebHook.  La clé principale peut être utilisée pour déclencher une fonction, même désactivé les fonctions.

## <a name="example-c-code-for-an-http-trigger-function"></a>Code exemple C# pour une fonction de déclenchement HTTP 

L’exemple de code recherche un `name` paramètre dans la chaîne de requête ou dans le corps de la demande HTTP.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

## <a name="example-f-code-for-an-http-trigger-function"></a>Code de l’exemple F# pour une fonction de déclenchement HTTP

L’exemple de code recherche un `name` paramètre dans la chaîne de requête ou dans le corps de la demande HTTP.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Vous aurez besoin une `project.json` fichier qui utilise NuGet pour faire référence à la `FSharp.Interop.Dynamic` et `Dynamitey` assemblys, comme suit :

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Cette option NuGet pour extraire les dépendances et les référencer dans votre script.

## <a name="example-nodejs-code-for-an-http-trigger-function"></a>Exemple de code d’une fonction de déclencheur HTTP Node.js 

Cet exemple de code recherche un `name` paramètre dans la chaîne de requête ou dans le corps de la demande HTTP.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

## <a name="example-c-code-for-a-github-webhook-function"></a>Code exemple C# pour une fonction de GitHub WebHook 

Cet exemple de code enregistre les commentaires de problème GitHub.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## <a name="example-f-code-for-a-github-webhook-function"></a>Code de l’exemple F# pour une fonction de GitHub WebHook

Cet exemple de code enregistre les commentaires de problème GitHub.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## <a name="example-nodejs-code-for-a-github-webhook-function"></a>Exemple de code Node.js pour une fonction de GitHub WebHook 

Cet exemple de code enregistre les commentaires de problème GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
