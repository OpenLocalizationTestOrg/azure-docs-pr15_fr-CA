<properties
    pageTitle="Les liaisons de fonctions DocumentDB Azure | Microsoft Azure"
    description="Comprendre l’utilisation des liaisons d’Azure DocumentDB dans les fonctions d’Azure."
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
    ms.author="chrande; glenga"/>

# <a name="azure-functions-documentdb-bindings"></a>Liaisons de fonctions DocumentDB Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et liaisons d’Azure DocumentDB de code dans des fonctions d’Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="docdbinput"></a>Azure DocumentDB d’entrée de liaison

Les liaisons d’entrée peuvent charger un document à partir d’une collection de DocumentDB et de passer directement à votre liaison. L’id de document peut être déterminé selon le déclencheur qui a appelé la fonction. Dans une fonction C#, toutes les modifications apportées à l’enregistrement est envoyées automatiquement à la collection lorsque la fonction se termine avec succès.

#### <a name="functionjson-for-documentdb-input-binding"></a>Function.JSON pour la liaison d’entrée de DocumentDB

Le fichier *function.json* fournit les propriétés suivantes :

- `name`: Nom variable utilisée dans le code de la fonction dans le document.
- `type`: doit être défini sur « documentdb ».
- `databaseName`: La base de données contenant le document.
- `collectionName`: Collection contenant le document.
- `id`: L’Id du document à récupérer. Cette propriété prend en charge les liaisons semblables à « {queueTrigger} », qui utilise la valeur de la chaîne de message de la file d’attente que le document du code.
- `connection`: Cette chaîne doit être un jeu de paramètres d’Application au point de terminaison de votre compte de DocumentDB. Si vous choisissez votre compte à partir de l’onglet Intégration, un nouveau paramètre d’application est créé pour vous avec un nom qui prend la forme suivante, yourAccount_DOCUMENTDB. Si vous devez créer manuellement le paramètre d’application, la chaîne de connexion doit prendre la forme suivante, AccountEndpoint =<Endpoint for your account>; AccountKey =<Your primary access key>;.
- « direction : doit avoir la valeur *« in »*.

Exemple *function.json*:
 
    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "id" : "{queueTrigger}",
          "connection": "MyAccount_DOCUMENTDB",     
          "direction": "in"
        }
      ],
      "disabled": false
    }

#### <a name="azure-documentdb-input-code-example-for-a-c-queue-trigger"></a>Exemple de code d’entrée DocumentDB Azure d’un déclencheur de file d’attente de C#
 
À l’aide de la function.json d’exemple ci-dessus, la liaison d’entrée DocumentDB va récupérer le document avec l’id qui correspond à la chaîne de message de file d’attente et le passe au paramètre « document ». Si ce document n’est pas trouvé, le paramètre « document » sera null. Le document est ensuite mis à jour avec la nouvelle valeur de texte lorsque la fonction s’arrête.
 
    public static void Run(string myQueueItem, dynamic document)
    {   
        document.text = "This has changed.";
    }

#### <a name="azure-documentdb-input-code-example-for-an-f-queue-trigger"></a>Exemple de code d’entrée DocumentDB Azure pour un déclencheur de file d’attente F#

À l’aide de la function.json d’exemple ci-dessus, la liaison d’entrée DocumentDB va récupérer le document avec l’id qui correspond à la chaîne de message de file d’attente et le passe au paramètre « document ». Si ce document n’est pas trouvé, le paramètre « document » sera null. Le document est ensuite mis à jour avec la nouvelle valeur de texte lorsque la fonction s’arrête.

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- "This has changed."

Vous aurez besoin une `project.json` fichier qui utilise NuGet pour spécifier la `FSharp.Interop.Dynamic` et `Dynamitey` packages comme des dépendances du package, comme suit :

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

Cette option NuGet pour extraire les dépendances et les référencer dans votre script.

#### <a name="azure-documentdb-input-code-example-for-a-nodejs-queue-trigger"></a>Exemple de code d’entrée DocumentDB Azure d’un déclencheur de la file d’attente Node.js
 
À l’aide de la function.json d’exemple ci-dessus, la liaison d’entrée DocumentDB récupérer le document avec l’id qui correspond à la chaîne de message de file d’attente et passez-le à la `documentIn` liaison de la propriété. Dans les fonctions Node.js, les documents mis à jour ne sont pas envoyées à la collection. Toutefois, vous pouvez passer la liaison d’entrée directement à une sortie de DocumentDB liaison nommée `documentOut` pour prendre en charge les mises à jour. Cet exemple de code met à jour la propriété de texte du document d’entrée et le définit comme le document de sortie.
 
    module.exports = function (context, input) {   
        context.bindings.documentOut = context.bindings.documentIn;
        context.bindings.documentOut.text = "This was updated!";
        context.done();
    };

## <a id="docdboutput"></a>Liaisons de sortie DocumentDB Azure

Vos fonctions peuvent écrire JSON liaison de documents à une base de données Azure DocumentDB à l’aide d' **Azure DocumentDB Document** de sortie. Pour plus d’informations sur les DocumentDB d’Azure, consultez l' [Introduction à DocumentDB](../documentdb/documentdb-introduction.md) et le [didacticiel de mise en route](../documentdb/documentdb-get-started.md).

#### <a name="functionjson-for-documentdb-output-binding"></a>liaison de sortie Function.JSON pour DocumentDB

Le fichier function.json fournit les propriétés suivantes :

- `name`: Nom variable utilisée dans le code de la fonction pour le nouveau document.
- `type`: doit être défini sur *« documentdb »*.
- `databaseName`: La base de données contenant la collection où le nouveau document sera créé.
- `collectionName`: La collection où le nouveau document sera créé.
- `createIfNotExists`: Il s’agit d’une valeur de type boolean pour indiquer si la collection doit être créée si elle n’existe pas. La valeur par défaut est *false*. La raison pour cela est nouveau collections sont créées avec un débit réservé, ce qui a des implications de la tarification. Pour plus d’informations, visitez la [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: Cette chaîne doit être qu'un **Paramètre d’Application** défini au point de terminaison de votre compte de DocumentDB. Si vous choisissez votre compte à partir de l’onglet **intégration** , un nouveau paramètre d’application sera créé pour vous avec un nom qui prend la forme suivante, `yourAccount_DOCUMENTDB`. Si vous devez créer manuellement le paramètre d’application, la chaîne de connexion doit prendre la forme suivante, `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction`: doit avoir la valeur *« out »*. 
 
Function.json de l’exemple :

    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "createIfNotExists": false,
          "connection": "MyAccount_DOCUMENTDB",
          "direction": "out"
        }
      ],
      "disabled": false
    }


#### <a name="azure-documentdb-output-code-example-for-a-nodejs-queue-trigger"></a>Exemple de code DocumentDB sortie Azure d’un déclencheur de la file d’attente Node.js

    module.exports = function (context, input) {
       
        context.bindings.document = {
            text : "I'm running in a Node function! Data: '" + input + "'"
        }   
     
        context.done();
    };

Le document de sortie :

    {
      "text": "I'm running in a Node function! Data: 'example queue data'",
      "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
    }
 

#### <a name="azure-documentdb-output-code-example-for-an-f-queue-trigger"></a>Exemple de code DocumentDB sortie Azure pour un déclencheur de file d’attente F#

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### <a name="azure-documentdb-output-code-example-for-a-c-queue-trigger"></a>Exemple de code DocumentDB sortie Azure d’un déclencheur de file d’attente de C#


    using System;

    public static void Run(string myQueueItem, out object document, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
       
        document = new {
            text = $"I'm running in a C# function! {myQueueItem}"
        };
    }


#### <a name="azure-documentdb-output-code-example-setting-file-name"></a>Nom de fichier DocumentDB sortie code exemple paramètre Azure

Si vous souhaitez définir le nom du document dans la fonction, vous venez de définir la `id` valeur.  Par exemple, si le contenu pour un employé JSON a été supprimée dans la file d’attente est semblable à la suivante :

    {
      "name" : "John Henry",
      "employeeId" : "123456",
      "address" : "A town nearby"
    }

Vous pouvez utiliser le code C# suivant dans une fonction de déclenchement de file d’attente : 
    
    #r "Newtonsoft.Json"
    
    using System;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        
        dynamic employee = JObject.Parse(myQueueItem);
        
        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }

Ou le code équivalent de F# :

    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
        id: string
        name: string
        employeeId: string
        address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
        log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
        let employee = JObject.Parse(myQueueItem)
        employeeDocument <-
            { id = sprintf "%s-%s" employee?name employee?employeeId
              name = employee?name
              employeeId = employee?id
              address = employee?address }

Exemple de sortie :

    {
      "id": "John Henry-123456",
      "name": "John Henry",
      "employeeId": "123456",
      "address": "A town nearby"
    }

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
