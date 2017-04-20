<properties
    pageTitle="Comment faire pour utiliser le stockage blob (stockage de l’objet) à partir de PHP | Microsoft Azure"
    description="Stocker des données non structurées dans le nuage avec les Blob Azure le stockage (objet)."
    documentationCenter="php"
    services="storage"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="how-to-use-blob-storage-from-php"></a>Comment faire pour utiliser le stockage blob à partir de PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Stockage de Blob Azure est un service qui stocke les données non structurées dans le nuage en tant qu’objets/objets BLOB. Stockage des objets BLOB peut stocker n’importe quel type de données texte ou binaires, comme un document, un fichier multimédia ou un programme d’installation de l’application. Stockage des objets BLOB est également appelée stockage d’objets.

Ce guide vous explique comment exécuter des scénarios courants utilisant le service d’objet blob Azure. Les exemples sont écrits en PHP et utiliser le [Kit de développement logiciel Azure pour PHP] [download]. Les scénarios présentés incluent le **téléchargement**, **liste**, **téléchargement**et **Supprimer** les objets BLOB. Pour plus d’informations sur les objets BLOB, reportez-vous à la section [étapes suivantes](#next-steps) .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Création d’une application PHP

La seule condition requise pour la création d’une application PHP qui accède au service d’objet blob Azure est le faisant référence à des classes dans le Kit de développement Azure pour PHP à partir de votre code. Vous pouvez utiliser les outils de développement pour créer votre application, y compris le bloc-notes.

Dans ce guide, vous utilisez les fonctionnalités de service, qui peuvent être appelées au sein d’une application PHP localement ou dans le code s’exécutant dans un rôle web Azure, le rôle de travail ou le site Web.

## <a name="get-the-azure-client-libraries"></a>Obtenir les bibliothèques clientes Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-blob-service"></a>Configurez votre application pour accéder au service d’objet blob

Pour utiliser l’API de service blob Azure, vous devez :

1. Une référence au fichier de chargeur automatique à l’aide de l’instruction [require_once] , et
2. Référencer toutes les classes que vous pouvez utiliser.

L’exemple suivant montre comment inclure le fichier de chargeur automatique et faire référence à la classe **ServicesBuilder** .

> [AZURE.NOTE] Cet exemple montre comment (et autres exemples dans cet article) supposent que vous avez installé les bibliothèques clientes PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement, vous devez référencer la `WindowsAzure.php` fichier de chargeur automatique.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


Dans les exemples ci-dessous, les `require_once` instruction apparaît toujours, mais uniquement les classes nécessaires pour exécuter l’exemple sont référencés.

## <a name="set-up-an-azure-storage-connection"></a>Configurer une connexion de stockage Azure

Pour instancier un client de service d’objet blob Azure, vous devez avoir une chaîne de connexion valide. Le format de la chaîne de connexion de service blob est :

Pour accéder à un service direct :

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Pour accéder à l’émulateur de stockage :

    UseDevelopmentStorage=true


Pour créer un client de service d’Azure, vous devez utiliser la classe **ServicesBuilder** . Vous pouvez :

* Passez la chaîne de connexion directement ou
* Utiliser le **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
    * Par défaut, il est fourni avec prise en charge d’une source externe - variables d’environnement.
    * Vous pouvez ajouter de nouvelles sources en étendant la classe **ConnectionStringSource** .

Pour les exemples présentés ici, la chaîne de connexion sera passée directement.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## <a name="create-a-container"></a>Créer un conteneur

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Un objet **BlobRestProxy** vous permet de créer un conteneur d’objet blob avec la méthode **createContainer** . Lorsque vous créez un conteneur, vous pouvez définir des options sur le conteneur, mais cela n’est pas requis. (L’exemple ci-dessous montre comment définir la liste de contrôle d’accès (ACL) conteneur et conteneur métadonnées.)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
    use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    // OPTIONAL: Set public access policy and metadata.
    // Create container options object.
    $createContainerOptions = new CreateContainerOptions();

    // Set public access policy. Possible values are
    // PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
    // CONTAINER_AND_BLOBS:
    // Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous
    // request, but cannot enumerate containers within the storage account.
    //
    // BLOBS_ONLY:
    // Specifies public read access for blobs. Blob data within this
    // container can be read via anonymous request, but container data is not
    // available. proxys cannot enumerate blobs within the container via
    // anonymous request.
    // If this value is not specified in the request, container data is
    // private to the account owner.
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    try {
        // Create container.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

L’appel de **setPublicAccess (PublicAccessType::CONTAINER\_et\_objets BLOB)** rend les données blob et de conteneur accessible via les demandes anonymes. **SetPublicAccess(PublicAccessType::BLOBS_ONLY)** donc uniquement les données blob accessible via les demandes anonymes. Pour plus d’informations sur le conteneur de listes ACL, reportez-vous à la section [ensemble conteneur ACL (API REST)][container-acl].

Pour plus d’informations sur les codes d’erreur de service de Blob, consultez [Codes d’erreur de Service de Blob][error-codes].

## <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Pour télécharger un fichier comme un objet blob, utilisez la méthode **BlobRestProxy -> createBlockBlob** . Cette opération crée le blob s’il n’existe pas, ou s’il remplace dans ce cas. L’exemple de code ci-dessous suppose que le conteneur a déjà été créé et qu’il utilise [fopen] [ fopen] pour ouvrir le fichier en tant que flux.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Upload blob
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Notez que l’exemple précédent transfère un blob en tant que flux. Toutefois, un blob peut également être téléchargé sous la forme d’une chaîne à l’aide, par exemple, le [fichier\_obtenir\_contenu] [ file_get_contents] fonction. Pour ce faire à l’aide de l’exemple précédent, modifiez `$content = fopen("c:\myfile.txt", "r");` à `$content = file_get_contents("c:\myfile.txt");`.

## <a name="list-the-blobs-in-a-container"></a>Liste les objets BLOB dans un conteneur

Pour répertorier les objets BLOB dans un conteneur, utilisez la méthode **BlobRestProxy -> listBlobs** avec un **foreach** boucle par le biais du résultat. Le code suivant affiche le nom de chaque objet blob en tant que sortie dans un conteneur et affiche son URI dans le navigateur.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // List blobs.
        $blob_list = $blobRestProxy->listBlobs("mycontainer");
        $blobs = $blob_list->getBlobs();

        foreach($blobs as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="download-a-blob"></a>Télécharger un blob

Pour télécharger un blob, appelez la méthode **BlobRestProxy -> getBlob** , puis appelez la méthode **getContentStream** sur l’objet résultant de la **GetBlobResult** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Get blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Notez que l’exemple ci-dessus Obtient un blob sous la forme d’une ressource de flux de données (le comportement par défaut). Toutefois, vous pouvez utiliser la [flux\_obtenir\_contenu] [ stream-get-contents] fonction pour convertir le flux retourné en une chaîne.

## <a name="delete-a-blob"></a>Supprimer un objet blob

Pour supprimer un objet blob, passez le nom du conteneur et le nom de l’objet blob à **BlobRestProxy -> deleteBlob**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete blob.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-a-blob-container"></a>Supprimer un conteneur de blob

Enfin, pour supprimer un conteneur blob, passez le nom du conteneur pour **BlobRestProxy -> deleteContainer**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du service blob Azure, suivre ces liens pour en savoir plus sur les tâches de stockage plus complexes.

- Visitez le [blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Consultez l' [exemple de blob de bloc PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php).
- Consultez l' [exemple de blob de page PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php).
- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
 
Pour plus d’informations, consultez également le [Centre pour développeurs PHP](/develop/php/).


[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
