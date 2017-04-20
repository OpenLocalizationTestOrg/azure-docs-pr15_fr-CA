<properties
    pageTitle="Mise en route avec le Kit de développement Azure CDN pour Node.js | Microsoft Azure"
    description="Apprenez à écrire des applications Node.js à gérer Azure CDN."
    services="cdn"
    documentationCenter="nodejs"
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="casoper"/>

# <a name="get-started-with-azure-cdn-development"></a>Mise en route avec le développement d’Azure CDN

> [AZURE.SELECTOR]
- [Node.js](cdn-app-dev-node.md)
- [.NET](cdn-app-dev-net.md)

Vous pouvez utiliser le [Kit de développement de logiciel de Azure CDN pour Node.js](https://www.npmjs.com/package/azure-arm-cdn) pour automatiser la création et la gestion des profils du Canada et des points de terminaison.  Ce didacticiel vous guide par le biais de la création d’une application de console Node.js simple qui illustre plusieurs les opérations disponibles.  Ce didacticiel n’est pas destiné pour décrire tous les aspects du SDK Azure CDN pour Node.js en détail.

Pour terminer ce didacticiel, vous disposez déjà [Node.js](http://www.nodejs.org) **versions4.x.x** ou supérieur installé et configuré.  Vous pouvez utiliser n’importe quel éditeur de texte que vous voulez créer votre application Node.js.  Pour écrire ce didacticiel, j’ai utilisé [Visual Studio Code](https://code.visualstudio.com).  

> [AZURE.TIP] La [fin d’un projet à partir de ce didacticiel](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) est disponible en téléchargement sur le site MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Créez votre projet et ajouter des dépendances NPM

Maintenant que nous avons créé un groupe de ressources pour les profils de nos CDN et compte tenu de notre annonce Azure application l’autorisation de gérer des profils CDN et points de terminaison dans ce groupe, nous pouvons commencer à créer notre application.

Créez un dossier pour stocker votre application.  À partir d’une console avec les outils Node.js dans votre chemin d’accès actuel, la valeur de votre emplacement actuel dans ce nouveau dossier et initialiser votre projet en exécutant :
    
    npm init
    
Vous obtiendrez une série de questions pour initialiser votre projet.  Ce didacticiel utilise le **point d’entrée**, *app.js*.  Vous pouvez voir mes autres choix dans l’exemple suivant.

![Sortie d’init NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Notre projet est maintenant initialisé avec un fichier *packages.json* .  Notre projet va utiliser certaines bibliothèques Azure contenues dans des packages NPM.  Nous allons utiliser l’exécution du Client Azure pour Node.js (ms-reste-azure) et la bibliothèque de Client Azure CDN pour Node.js (azure-bras-cd).  Ajoutons à celles pour le projet en tant que dépendances.
 
    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Les packages sont ensuite l’installation, de l' *package.json* fichier doit ressembler à cet exemple (version numéros peuvent être différentes) :

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Enfin, à l’aide de votre éditeur de texte, créez un fichier texte vide et enregistrez-le dans la racine du dossier de notre projet en tant que *app.js*.  Nous sommes maintenant prêts à commencer à écrire du code.

## <a name="requires-constants-authentication-and-structure"></a>Nécessite, de constantes, de l’authentification et de structure

Avec *app.js* ouvert dans notre éditeur, passons à la structure de base de notre programme écrit.

1. Ajoutez le « demande » pour nos packages NPM en haut avec le texte suivant :

    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```

2. Nous devons définir certaines constantes que nos méthodes utilisera.  Ajoutez le code suivant.  Veillez à remplacer les espaces réservés, y compris la ** &lt;crochets&gt;**, avec vos propres valeurs le cas échéant.

    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";

    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```

3. Ensuite, nous instancier le client de gestion CDN et lui attribuer ses informations d’identification.

    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
    
    Si vous utilisez l’authentification de l’utilisateur individuel, ces deux lignes auront l’aspect légèrement différents.

    >[AZURE.IMPORTANT] Utiliser uniquement le code si vous préférez que l’authentification d’utilisateur individuel au lieu d’un service principal.  Veillez à protéger vos informations d’identification de l’utilisateur individuel et de toujours les garder secrets.

    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```

    Veillez à remplacer les éléments de ** &lt;crochets&gt; ** avec les informations correctes.  Pour `<redirect URI>`, utilisez l’URI que vous avez entré lorsque vous avez enregistré l’application dans Azure AD de redirection.
    

4.  Notre application de console Node.js va prendre certains paramètres de ligne de commande.  Nous allons valider qu’au moins un paramètre a été passé.

    ```javascript
    //Collect command-line parameters
    var parms = process.argv.slice(2);

    //Do we have parameters?
    if(parms == null || parms.length == 0)
    {
        console.log("Not enough parameters!");
        console.log("Valid commands are list, delete, create, and purge.");
        process.exit(1);
    }
    ```

5. Ce qui nous amène à la partie principale de notre programme, où nous créer des branches à d’autres fonctions selon les paramètres passés.

    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;

        case "create":
            cdnCreate();
            break;
        
        case "delete":
            cdnDelete();
            break;

        case "purge":
            cdnPurge();
            break;

        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```

6.  En plusieurs endroits dans notre programme, nous avons besoin pour vous assurer que le nombre correct de paramètres ont été passé et affiche une aide si elles ne semblent pas correctes.  Nous allons créer des fonctions pour cela.

    ```javascript
    function requireParms(parmCount) {
        if(parms.length < parmCount) {
            usageHelp(parms[0].toLowerCase());
            process.exit(1);
        }
    }

    function usageHelp(cmd) {
        console.log("Usage for " + cmd + ":");
        switch(cmd)
        {
            case "list":
                console.log("list profiles");
                console.log("list endpoints <profile name>");
                break;

            case "create":
                console.log("create profile <profile name>");
                console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
                break;
            
            case "delete":
                console.log("delete profile <profile name>");
                console.log("delete endpoint <profile name> <endpoint name>");
                break;

            case "purge":
                console.log("purge <profile name> <endpoint name> <path>");
                break;

            default:
                console.log("Invalid command.");
        }
    }
    ```

7. Enfin, les fonctions que nous utiliserons sur le client de gestion CDN étant asynchrones, ils ont besoin d’une méthode à appeler lorsqu’ils ont terminé.  Nous allons en créer un qui peut afficher la sortie à partir du client de gestion CDN (le cas échéant) et quitter le programme normalement.

    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Maintenant que la structure de base de notre programme est écrit, nous devons créer les fonctions appelées en fonction de nos paramètres.

## <a name="list-cdn-profiles-and-endpoints"></a>Liste des profils CDN et points de terminaison

Commençons par notre profils existants et les points de terminaison de la liste de code.  Mes commentaires de code fournissent la syntaxe attendue pour savoir où va de chaque paramètre.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Créer des points de terminaison et les profils CDN

Ensuite, nous allons écrire les fonctions pour créer des profils et des points de terminaison.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Purge d’un point de terminaison

En supposant que le point de terminaison a été créé, une tâche courante que nous pouvons effectuer dans notre programme de purge le contenu de notre point de terminaison.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Supprimer les points de terminaison et les profils CDN

La dernière fonction que nous nous occupons de supprime les points de terminaison et les profils.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Le programme en cours d’exécution

Nous pouvons maintenant exécuter notre programme Node.js à l’aide de notre débogueur favori ou sur la console.

> [AZURE.TIP] Si vous utilisez Visual Studio Code sous le débogueur, vous devez configurer votre environnement pour passer les paramètres de ligne de commande.  Code de Visual Studio pour cela, dans le fichier **lanuch.json** .  Recherchez une propriété nommée **args** et ajouter un tableau de valeurs de chaîne pour vos paramètres, afin qu’il ressemble à ceci : `"args": ["list", "profiles"]`.

Commençons par nos profils de la liste.

![Liste des profils](./media/cdn-app-dev-node/cdn-list-profiles.png)

Nous avons obtenu un tableau vide.  Dans la mesure où nous n’avons pas tous les profils dans notre groupe de ressources, qui est attendu.  Créons maintenant un profil.

![Créer profil](./media/cdn-app-dev-node/cdn-create-profile.png)

Maintenant, nous allons ajouter un point de terminaison.

![Créer le point de terminaison](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Enfin, nous allons supprimer de notre profil.

![Supprimer le profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Étapes suivantes

Pour voir le projet réalisé à partir de cette procédure pas à pas, [Téléchargez l’exemple](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Pour voir la référence pour le Kit de développement Azure CDN pour Node.js, consultez la [référence](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Pour rechercher de la documentation supplémentaire sur le SDK Azure Node.js, permet d’afficher la [référence complète](http://azure.github.io/azure-sdk-for-node/).

Gérer vos ressources CDN avec [PowerShell](./cdn-manage-powershell.md).

