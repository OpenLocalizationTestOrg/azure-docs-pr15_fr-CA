<properties
    pageTitle="Comment faire pour utiliser le stockage Blob de Node.js | Microsoft Azure"
    description="Stocker des données non structurées dans le nuage avec les Blob Azure le stockage (objet)."
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>



# <a name="how-to-use-blob-storage-from-nodejs"></a>Comment faire pour utiliser le stockage Blob de Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article vous montre comment réaliser des scénarios courants utilisant le stockage Blob. Ces exemples sont écrits via l’API Node.js. Les scénarios présentés comprennent comment répertorier, télécharger et supprimer des objets BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Créer une application Node.js

Pour obtenir des instructions sur la création d’une application Node.js, voir [créer une application web de Node.js dans le Service d’application Azure], [Générer et déployer une application Node.js à un Service de Cloud Azure] --à l’aide de Windows PowerShell, ou [Générer et déployer une application web Node.js Azure à l’aide de Web Matrix].

## <a name="configure-your-application-to-access-storage"></a>Configurez votre application pour accéder au stockage

Pour utiliser le stockage Azure, vous devez le Kit de développement de stockage Azure pour Node.js, qui inclut un ensemble de bibliothèques de commodité qui communiquent avec les services de reste de stockage.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilisez le nœud Gestionnaire de package (NPM) pour obtenir le package

1.  Une interface de ligne de commande **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix), permet de naviguer jusqu’au dossier où vous avez créé votre exemple d’application.

2.  Dans la fenêtre de commande, tapez **npm installer le stockage azure** . Sortie de la commande est semblable à l’exemple de code suivant.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier si un **nœud\_modules** dossier a été créé. À l’intérieur de ce dossier, recherchez le package de **stockage azure** , qui contient les bibliothèques dont vous avez besoin pour accéder au stockage.

### <a name="import-the-package"></a>Importer le package

À l’aide du bloc-notes ou un autre éditeur de texte, ajoutez le code suivant en haut du fichier **server.js** de l’application dans laquelle vous avez l’intention d’utiliser le stockage :

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurer une connexion de stockage Azure

Le module Azure lira les variables d’environnement `AZURE_STORAGE_ACCOUNT` et `AZURE_STORAGE_ACCESS_KEY`, ou `AZURE_STORAGE_CONNECTION_STRING`, pour les informations requises pour vous connecter à votre compte de stockage Azure. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l’appel de **createBlobService**.

Pour obtenir un exemple de définition des variables d’environnement dans [Azure Portal](https://portal.azure.com) pour une application web d’Azure, consultez [Node.js web app est l’utilisation du Service de la Table Azure].

## <a name="create-a-container"></a>Créer un conteneur

L’objet **BlobService** vous permet de travailler avec les conteneurs et les objets BLOB. Le code suivant crée un objet **BlobService** . Ajoutez le code suivant en haut de la **server.js**:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] Vous pouvez accéder un blob anonymement à l’aide de **createBlobServiceAnonymous** et de fournir l’adresse de l’hôte. Par exemple, utilisez `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Pour créer un nouveau conteneur, utilisez **createContainerIfNotExists**. L’exemple de code suivant crée un nouveau conteneur nommé « mycontainer » :

    blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
        if(!error){
          // Container exists and is private
        }
    });

Si le conteneur est créé, `result.created` a la valeur true. Si le conteneur existe déjà, `result.created` a la valeur false. `response`contient des informations sur l’opération, y compris les informations de l’ETag pour le conteneur.

### <a name="container-security"></a>Sécurité du conteneur

Par défaut, les nouveaux conteneurs sont privées et ne peut pas être accessibles de manière anonyme. Pour rendre le conteneur public et vous pouvez y accéder anonymement, vous pouvez définir le niveau d’accès du conteneur **blob** ou **conteneur**.

* **blob** - permet l’accès anonyme en lecture aux blob contenu et aux métadonnées dans ce conteneur, mais pas pour le conteneur des métadonnées telles que des listes de tous les objets BLOB dans un conteneur

* **conteneur** - autorise l’accès anonyme en lecture au contenu du blob et métadonnées ainsi que les métadonnées de conteneur

L’exemple de code suivant illustre la définissant le niveau d’accès **d’objet blob**:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
        if(!error){
          // Container exists and allows
          // anonymous read access to blob
          // content and metadata within this container
        }
    });

Sinon, vous pouvez modifier le niveau d’accès d’un conteneur à l’aide de **setContainerAcl** pour spécifier le niveau d’accès. L’exemple de code suivant modifie le niveau d’accès au conteneur :

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
      if(!error){
        // Container access level set to 'container'
      }
    });

Le résultat contient des informations sur l’opération, y compris l' actuel **ETag** pour le conteneur.

### <a name="filters"></a>Filtres

Vous pouvez appliquer des opérations de filtrage facultatives pour les opérations effectuées à l’aide de **BlobService**. Opérations de filtrage peuvent inclure la journalisation, l’automatiquement une nouvelle tentative, etc.. Les filtres sont des objets qui implémentent une méthode avec la signature :

    function handle (requestOptions, next)

Après avoir effectué le prétraitement sur les options de requête, la méthode doit appeler « suivant », en passant un rappel avec la signature suivante :

    function (returnObject, finalCallback, next)

Dans ce rappel et après le traitement de la returnObject (la réponse de la demande sur le serveur), le rappel doit appeler ensuite s’il existe pour poursuivre le traitement des autres filtres ou simplement appeler finalCallback pour mettre fin à l’appel de service.

Deux filtres qui implémentent la logique des nouvelles tentatives sont inclus dans le Kit de développement Azure Node.js, **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **BlobService** qui utilise **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Il existe trois types d’objets BLOB : bloquer les objets BLOB, BLOB de la page et ajouter des objets BLOB. BLOB de bloc permettre que plus téléchargement efficacement la grande quantité de données. Ajouter des objets BLOB est optimisés pour les opérations d’ajout. BLOB de page est optimisés pour les opérations de lecture/écriture. Pour plus d’informations, consultez [Blobs de bloc de compréhension, ajouter un BLOB et BLOB de la Page](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>BLOB de bloc

Pour télécharger des données à un objet blob de bloc, utilisez la commande suivante :

* **createBlockBlobFromLocalFile** - crée un nouveau blob de bloc et télécharge le contenu d’un fichier

* **createBlockBlobFromStream** - crée un nouveau blob de bloc et télécharge le contenu d’un flux de données

* **createBlockBlobFromText** - crée un nouveau blob de bloc et télécharge le contenu d’une chaîne

* **createWriteStreamToBlockBlob** - fournit un flux d’écriture à un objet blob de bloc

L’exemple de code suivant télécharge le contenu du fichier **test.txt** dans **myblob**.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

Le `result` retournés par ces méthodes contient des informations sur l’opération, comme l' **ETag** de l’objet blob.

### <a name="append-blobs"></a>Ajouter des objets BLOB

Pour télécharger des données à un objet blob de nouveau ajouter, procédez comme suit :

* **createAppendBlobFromLocalFile** - crée un blob Ajouter nouvel et transfère le contenu d’un fichier

* **createAppendBlobFromStream** - crée un blob Ajouter nouvel et transfère le contenu d’un flux de données

* **createAppendBlobFromText** - crée un blob Ajouter nouvel et transfère le contenu d’une chaîne

* **createWriteStreamToNewAppendBlob** - crée un blob Ajouter nouveau et fournit un flux en écriture

L’exemple de code suivant télécharge le contenu du fichier **test.txt** dans **myappendblob**.

    blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

Pour ajouter un bloc à un blob append existant, procédez comme suit :

* **appendFromLocalFile** - ajouter le contenu d’un fichier à un blob Ajouter existant

* **appendFromStream** - ajouter le contenu d’un flux à un blob Ajouter existant

* **appendFromText** - ajouter le contenu d’une chaîne à un blob Ajouter existant

* **appendBlockFromStream** - ajouter le contenu d’un flux à un blob Ajouter existant

* **appendBlockFromText** - ajouter le contenu d’une chaîne à un blob Ajouter existant

> [AZURE.NOTE] API d’appendFromXXX effectuera une validation côté client pour l’échec rapide éviter l’appel du serveur unncessary. appendBlockFromXXX n’est pas.

L’exemple de code suivant télécharge le contenu du fichier **test.txt** dans **myappendblob**.

    blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
      if(!error){
        // text appended
      }
    });


### <a name="page-blobs"></a>BLOB de page

Pour télécharger des données à un objet blob de page, procédez comme suit :

* **createPageBlob** - crée un nouveau blob de page d’une longueur spécifique

* **createPageBlobFromLocalFile** - crée un nouvel objet blob de page et télécharge le contenu d’un fichier

* **createPageBlobFromStream** - crée un nouvel objet blob de page et télécharge le contenu d’un flux de données

* **createWriteStreamToExistingPageBlob** - fournit un flux d’écriture pour un blob existant de la page

* **createWriteStreamToNewPageBlob** - crée un nouvel objet blob de page et fournit un flux en écriture

L’exemple de code suivant télécharge le contenu du fichier **test.txt** dans **mypageblob**.

    blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

> [AZURE.NOTE] BLOB de page sont constitués de 512 octets 'pages'. Vous recevrez une erreur lors du chargement des données avec une taille qui n’est pas un multiple de 512.

## <a name="list-the-blobs-in-a-container"></a>Liste les objets BLOB dans un conteneur

Pour répertorier les objets BLOB dans un conteneur, utilisez la méthode **listBlobsSegmented** . Si vous souhaitez retourner des objets BLOB avec un préfixe donné, utilisez **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
          // result.entries contains the entries
          // If not all blobs were returned, result.continuationToken has the continuation token.
      }
    });

Le `result` contient un `entries` collection, qui est un tableau d’objets qui décrivent chaque blob. Si tous les BLOB ne peut pas être retournés, le `result` fournit également une `continuationToken`, que vous pouvez utiliser comme second paramètre pour récupérer les entrées supplémentaires.

## <a name="download-blobs"></a>Télécharger des objets BLOB

Pour télécharger des données à partir d’un blob, vous devez utiliser la syntaxe suivante :

* **getBlobToLocalFile** - écrit le contenu de l’objet blob dans le fichier

* **getBlobToStream** - écrit le contenu de l’objet blob dans un flux

* **getBlobToText** - écrit le contenu de l’objet blob en chaîne

* **createReadStream** - fournit un flux pour lire à partir du blob

L’exemple de code suivant illustre l’utilisation de **getBlobToStream** pour télécharger le contenu de l’objet blob de **myblob** et le stocker dans le fichier **output.txt** à l’aide d’un flux de données :

    var fs = require('fs');
    blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
      if(!error){
        // blob retrieved
      }
    });

Le `result` contient des informations sur l’objet blob, y compris les informations de **l’ETag** .

## <a name="delete-a-blob"></a>Supprimer un objet blob

Enfin, pour supprimer un objet blob, appelez **deleteBlob**. L’exemple de code suivant supprime le blob nommé **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
      if(!error){
        // Blob has been deleted
      }
    });

## <a name="concurrent-access"></a>Accès simultané

Pour prendre en charge l’accès simultané à un objet blob à partir de plusieurs clients ou plusieurs instances de processus, vous pouvez utiliser des **ETags** ou des **baux**.

* **Etag** - fournit un moyen de détecter que le blob ou conteneur a été modifié par un autre processus

* **Bail** - permet d’obtenir exclusif, renouvelables, écrire ou supprimer l’accès à un objet blob pour une période donnée

### <a name="etag"></a>ETag

Utiliser des ETags si vous avez besoin permettre à plusieurs clients ou instances à écrire dans le bloc de Blob ou page Blob simultanément. L’ETag vous permet de déterminer si le conteneur ou le blob a été modifié depuis la lecture initiale ou créé, ce qui permet d’éviter d’écraser les modifications validées par un autre client ou processus.

Vous pouvez définir des conditions d’ETag à l’aide de l’option `options.accessConditions` paramètre. L’exemple de code suivant télécharge uniquement le fichier **test.txt** si le blob existe déjà et a la valeur ETag contenus dans `etagToMatch`.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
        if(!error){
        // file uploaded
      }
    });

Lorsque vous utilisez des ETags, le modèle général est :

1. Obtenir l’ETag à la suite d’une création, une liste ou une opération d’obtention.

2. Effectuer une action, en vérifiant que la valeur ETag n’a pas été modifiée.

Si la valeur a été modifiée, cela indique qu’un autre client ou une autre instance modifié le blob ou le conteneur depuis que vous avez obtenu la valeur ETag.

### <a name="lease"></a>Bail

Vous pouvez acquérir un nouveau bail par à l’aide de la méthode **acquireLease** , en spécifiant le blob ou le conteneur que vous souhaitez obtenir un bail. Par exemple, le code suivant obtient un bail sur **myblob**.

    blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
      if(!error) {
        console.log('leaseId: ' + result.id);
      }
    });

Les opérations ultérieures sur **myblob** doivent fournir le `options.leaseId` paramètre. Le bail ID est retourné en tant que `result.id` de **acquireLease**.

> [AZURE.NOTE] Par défaut, la durée du bail est infinie. Vous pouvez spécifier une durée non-infini (entre 15 et 60 des secondes) en fournissant la `options.leaseDuration` paramètre.

Pour supprimer un bail, utilisez **releaseLease**. Pour rompre un contrat de location, mais empêcher les autres utilisateurs d’obtenir un nouveau bail jusqu'à l’expiration de la durée d’origine, utilisez **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Avec des signatures d’accès partagé

Signatures d’accès partagé (SAS) sont un moyen sécurisé de fournir granulaire de l’accès à des conteneurs et des objets BLOB sans fournir votre nom de compte de stockage ou des clés. Accès partagé des signatures sont souvent utilisés pour restreindre l’accès à vos données, comme l’autorisation d’une application mobile accéder aux objets BLOB.

> [AZURE.NOTE] Alors que vous pouvez également autoriser l’accès anonyme aux objets BLOB, signatures d’accès partagé permettent de fournir plus un accès contrôlé, comme vous devez générer les associations de sécurité.

Une application de confiance comme un service en nuage génère des signatures d’un accès partagé à l’aide de la **generateSharedAccessSignature** de la **BlobService**et lui fournit à une application non fiable ou de niveau de confiance partiel, comme une application mobile. Partagé des accès signatures sont générés à l’aide d’une stratégie qui décrit le début et la fin au cours de laquelle les signatures d’accès partagé sont valides, ainsi que le niveau d’accès accordé à l’exploitant de signatures d’accès partagé.

L’exemple de code suivant génère une nouvelle stratégie d’accès partagé qui permet au détenteur de signatures d’accès partagé effectuer des opérations de lecture sur l’objet blob **myblob** et expiration de 100 minutes après l’heure de que sa création.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
    var host = blobSvc.host;

Notez que les informations de l’hôte doivent être également, comme il est requis lorsque le détenteur de signatures d’accès partagé tente d’accéder au conteneur.

L’application cliente utilise des signatures de l’accès partagé avec **BlobServiceWithSAS** pour effectuer des opérations sur l’objet blob. La commande suivante obtient des informations sur les **myblob**.

    var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
    sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
      if(!error) {
        // retrieved info
      }
    });

Dans la mesure où les signatures d’accès partagé a été générés avec accès en lecture seule, si une tentative est effectuée pour modifier l’objet blob, une erreur est renvoyée.

### <a name="access-control-lists"></a>Listes de contrôle d’accès

Vous pouvez également utiliser une liste de contrôle d’accès (ACL) pour définir la stratégie d’accès pour les associations de sécurité. Cela est utile si vous souhaitez permettre à plusieurs clients d’accéder à un conteneur, mais fournissent des stratégies d’accès différentes pour chaque client.

Une ACL est implémentée à l’aide d’un tableau des stratégies d’accès, dont l’ID est associé à chaque stratégie. L’exemple de code suivant définit deux stratégies, un pour « Utilisateur1 » et une pour 'utilisateur2' :

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
        Start: startDate,
        Expiry: expiryDate
      }
    };

L’exemple de code suivant obtient l’ACL en cours pour **mycontainer**et puis ajoute les nouvelles stratégies à l’aide de **setBlobAcl**. Cette approche permet de :

    var extend = require('extend');
    blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Une fois la liste ACL est définie, vous pouvez ensuite créer des signatures d’accès partagé basés sur l’ID d’une stratégie. L’exemple de code suivant crée les nouvelles signatures d’accès partagé pour 'utilisateur2' :

    blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes.

-   [Kit de développement logiciel de référence des API de nœud de stockage Azure][]
-   [Blog de l’équipe stockage Azure][]
-   Référentiel du [Kit de développement logiciel de nœud de stockage Azure][] sur GitHub
-   [Centre de développement Node.js](/develop/nodejs/)
-   [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)

[Kit de développement logiciel de nœud de stockage Azure]: https://github.com/Azure/azure-storage-node

[Créer une application web de Node.js dans le Service d’application Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[Application web de Node.js à l’aide du Service de Table Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[Générer et déployer une application web de Node.js vers Azure à l’aide de Web Matrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: https://portal.azure.com
[Générer et déployer une application Node.js à un Service de Cloud Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Blog de l’équipe stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Kit de développement logiciel de référence des API de nœud de stockage Azure]: http://dl.windowsazure.com/nodestoragedocs/index.html
