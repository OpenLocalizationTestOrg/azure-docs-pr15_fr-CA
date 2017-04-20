<properties
    pageTitle="Référence du développeur de fonctions NodeJS Azure | Microsoft Azure"
    description="Comprendre comment développer des fonctions Azure à l’aide de NodeJS."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="nodejs"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-nodejs-developer-reference"></a>Référence du développeur de fonctions NodeJS Azure

> [AZURE.SELECTOR]
- [Script C#](../articles/azure-functions/functions-reference-csharp.md)
- [Script de F#](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)

L’expérience du nœud/JavaScript pour les fonctions d’Azure permet de facilement exporter une fonction qui est passée un `context` objet de communication avec le runtime et pour la réception et l’envoi de données via des liaisons.

Cet article suppose que vous avez déjà lu la [référence du développeur les fonctions Azure](functions-reference.md).

## <a name="exporting-a-function"></a>Exportation d’une fonction

Toutes les fonctions JavaScript doivent exporter un seul `function` via `module.exports` pour que le runtime peut trouver la fonction et l’exécuter. Cette fonction doit toujours contenir une `context` objet.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Liaisons de `direction === "in"` sont transmis en tant qu’arguments de la fonction, ce qui signifie que vous pouvez utiliser [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) gérer dynamiquement les nouvelles entrées (par exemple, à l’aide de `arguments.length` pour itérer sur toutes vos entrées). Cette fonctionnalité est très pratique si vous disposez d’un déclencheur avec aucune des entrées supplémentaires, comme vous pouvez prévisible accéder à vos données de déclencheur sans référencer votre `context` objet.

Les arguments sont toujours passés le long de la fonction dans l’ordre qu’ils se produisent dans *function.json*, même si vous ne les spécifiez dans votre instruction exports. Par exemple, si vous avez `function(context, a, b)` et le modifier à `function(context, a)`, vous pouvez obtenir toujours de la valeur de `b` dans le code de la fonction en vous reportant à `arguments[3]`.

Toutes les liaisons, quelle que soit la direction, sont également transmis le `context` objet (voir ci-dessous). 

## <a name="context-object"></a>objet de contexte

Le runtime utilise un `context` objet pour transmettre des données vers et à partir de votre fonction et pour vous permettre de communiquer avec le runtime.

L’objet de contexte est toujours le premier paramètre d’une fonction et doit toujours être inclus, car il possède des méthodes telles que `context.done` et `context.log` qui sont nécessaires pour utiliser correctement le runtime. Vous pouvez nommer l’objet ce que vous voulez (par exemple, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## <a name="contextbindings"></a>Context.Bindings

Le `context.bindings` objet de collecte toutes les données d’entrée et de sortie. Les données sont ajoutées à la `context.bindings` objet la `name` propriétés de la liaison. Par exemple, compte tenu de la définition de liaison suivant *function.json*, vous pouvez accéder au contenu de la file d’attente via `context.bindings.myInput`. 

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

Le `context.done` fonction indique au runtime que vous avez terminé en cours d’exécution. Il est important d’appeler lorsque vous avez terminé avec la fonction ; Si vous ne le faites pas, le runtime ne sauront toujours jamais que votre fonction terminée. 

Le `context.done` fonction permet de transmettre une erreur définie par l’utilisateur pour le runtime, ainsi que d’un sac de propriétés de propriétés qui remplace les propriétés sur le `context.bindings` objet.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## <a name="contextlogmessage"></a>Context.log(message)

Le `context.log` méthode vous permet d’instructions de journal qui sont corrélées ensemble pour l’enregistrement. Si vous utilisez `console.log`, vos messages affichera uniquement pour l’enregistrement au niveau du processus, qui n’est pas aussi utile.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

Le `context.log` méthode prend en charge le même format de paramètre qui la prend en charge la [méthode d’util.format](https://nodejs.org/api/util.html#util_util_format_format) du nœud. Ainsi, par exemple, le code comme suit :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

peut être écrite comme suit :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## <a name="http-triggers-contextreq-and-contextres"></a>Déclencheurs HTTP : context.req et context.res

Dans le cas de déclencheurs de HTTP, car il s’agit tel un modèle commun à utiliser `req` et `res` pour les objets de requête et de réponse HTTP, nous avons décidé faciliter l’accès à celles de l’objet de contexte, plutôt que de vous forcer à utiliser l’intégralité de la `context.bindings.name` modèle.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## <a name="node-version--package-management"></a>Nœud Version & gestion des packages

La version de nœud est actuellement verrouillée à `5.9.1`. Nous allons étudie d’ajouter la prise en charge pour les versions plus et rendant configurables.

Vous pouvez inclure des packages dans votre fonction en téléchargeant un fichier *package.json* dans le dossier de votre fonction dans le système de fichiers de l’application de la fonction. Fichier de téléchargement des instructions, reportez-vous à la section **comment mettre à jour les fichiers d’application de fonction** de la [rubrique de référence du développeur les fonctions Azure](functions-reference.md#fileupdate). 

Vous pouvez également utiliser `npm install` dans l’interface de ligne de commande SCM (Kudu) de l’application de la fonction :

1. Naviguez jusqu'à : `https://<function_app_name>.scm.azurewebsites.net`.

2. Cliquez sur **Console de débogage > CMD**.

3. Accédez à `D:\home\site\wwwroot\<function_name>`.

4. Exécutez `npm install`.

Une fois les packages dont vous avez besoin sont installés, vous les importez dans votre fonction de la façon habituelle (c'est-à-dire, via `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## <a name="environment-variables"></a>Variables d’environnement

Pour obtenir une variable d’environnement ou d’une application, la définition de valeur, utilisez `process.env`, comme illustré dans l’exemple de code suivant :

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    
    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="typescriptcoffeescript-support"></a>Prise en charge de la machine à écrire/CoffeeScript

Il n’existe pas encore, une prise en charge directe pour auto-compilation mécanographique/CoffeeScript via le runtime, afin qui seraient tous doivent être traitées en dehors du runtime, au moment du déploiement. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Référence du développeur de fonctions Azure](functions-reference.md)
* [Référence du développeur Azure fonctions C#](functions-reference-csharp.md)
* [Référence du développeur Azure fonctions F#](functions-reference-fsharp.md)
* [Les liaisons et les déclencheurs de fonctions azure](functions-triggers-bindings.md)
