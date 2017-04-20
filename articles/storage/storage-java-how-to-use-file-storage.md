<properties
    pageTitle="Comment faire pour utiliser le stockage de fichiers à partir de Java | Microsoft Azure"
    description="Apprenez à utiliser le service de fichier Azure pour télécharger, liste et supprimer les fichiers. Exemples écrits en Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-file-storage-from-java"></a>Comment faire pour utiliser le stockage de fichiers à partir de Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Vue d’ensemble

Dans ce guide, vous allez apprendre à effectuer des opérations de base sur le service de stockage Microsoft Azure. Grâce à des exemples écrits en Java, vous allez apprendre à créer des répertoires et des partages, télécharger, liste et supprimer les fichiers. Si vous ne connaissez pas le service de stockage de fichiers de Microsoft Azure, parcourant les concepts décrits dans les sections qui suivent seront très utile pour comprendre les exemples.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Créer une application Java

Pour générer les exemples, vous devez le Kit de développement Java (JDK) et les crochets [] [SDK de stockage Azure pour Java]. Vous devez également avoir créé un compte de stockage Azure.

## <a name="setup-your-application-to-use-file-storage"></a>Le programme d’installation de votre application pour utiliser le stockage de fichiers

Pour utiliser les API de stockage Azure, ajoutez l’instruction suivante en haut du fichier Java dans laquelle vous souhaitez accéder au service de stockage à partir de.

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.file.*;

## <a name="setup-an-azure-storage-connection-string"></a>Programme d’installation une chaîne de connexion du stockage Azure

Pour utiliser le stockage de fichiers, vous devez vous connecter à votre compte de stockage Azure. La première étape serait de configurer une chaîne de connexion qui nous permettront de vous connecter à votre compte de stockage. Nous allons définir une variable statique pour cela.

    // Configure the connection-string with your values
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account_name;" +
        "AccountKey=your_storage_account_key";

> [AZURE.NOTE] Remplacer your_storage_account_name et your_storage_account_key avec les valeurs réelles de votre compte de stockage.

## <a name="connecting-to-an-azure-storage-account"></a>Connexion à un compte de stockage Azure

Pour vous connecter à votre compte de stockage, vous devez utiliser l’objet **CloudStorageAccount** , en passant une chaîne de connexion à la méthode **parse** .

    // Use the CloudStorageAccount object to connect to your storage account
    try {
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
    } catch (InvalidKeyException invalidKey) {
        // Handle the exception
    }

**CloudStorageAccount.parse** lève une InvalidKeyException, il vous faudra donc de le placer à l’intérieur d’un bloc try/catch.

## <a name="how-to-create-a-share"></a>Comment : créer un partage

Tous les fichiers et les répertoires dans le stockage de fichiers se trouvent dans un conteneur appelé un **partage**. Votre compte de stockage peut avoir autant de partages permet de la capacité de votre compte. Pour obtenir l’accès à un partage et son contenu, vous devez utiliser un client de stockage de fichier.

    // Create the file storage client.
    CloudFileClient fileClient = storageAccount.createCloudFileClient();

À l’aide du client de stockage du fichier, vous pouvez ensuite obtenir une référence à un partage.

    // Get a reference to the file share
    CloudFileShare share = fileClient.getShareReference("sampleshare");

Pour créer le partage, utilisez la méthode **createIfNotExists** de l’objet CloudFileShare.

    if (share.createIfNotExists()) {
        System.out.println("New share created");
    }

À ce stade, **partager** contient une référence à un partage nommé **sampleshare**.

## <a name="how-to-upload-a-file"></a>Comment : télécharger un fichier

Un partage de stockage Azure fichier contient au moins un répertoire racine où les fichiers peuvent résider. Dans cette section, vous allez apprendre comment télécharger un fichier à partir du stockage local vers le répertoire racine d’un partage.

La première étape lors du téléchargement d’un fichier consiste à obtenir une référence vers le répertoire où il doit résider. Pour cela, en appelant la méthode **getRootDirectoryReference** de l’objet partage.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

Maintenant que vous avez une référence à la racine du partage, vous pouvez télécharger un fichier à l’aide du code suivant.

    // Define the path to a local file.
    final String filePath = "C:\\temp\\Readme.txt";

    CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
    cloudFile.uploadFromFile(filePath);

## <a name="how-to-create-a-directory"></a>Comment : créer un répertoire

Vous pouvez également organiser le stockage en plaçant les fichiers dans les sous-répertoires au lieu de chacun d'entre eux dans le répertoire racine. Le service de stockage Azure vous permet de créer autant de répertoires que permettra de votre compte. Le code ci-dessous crée un sous-répertoire nommé **sampledir** sous le répertoire racine.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the sampledir directory
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    if (sampleDir.createIfNotExists()) {
        System.out.println("sampledir created");
    } else {
        System.out.println("sampledir already exists");
    }

## <a name="how-to-list-files-and-directories-in-a-share"></a>Comment : répertorier les fichiers et les répertoires dans un partage

Obtention d’une liste de fichiers et de répertoires à l’intérieur d’un partage s’effectue facilement en appelant **listFilesAndDirectories** sur une référence CloudFileDirectory. La méthode retourne une liste d’objets ListFileItem qui vous pouvez effectuer une itération sur. Par exemple, le code suivant répertorie les fichiers et les répertoires dans le répertoire racine.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
        System.out.println(fileItem.getUri());
    }


## <a name="how-to-download-a-file"></a>Comment : télécharger un fichier

Une des opérations plus fréquentes que vous allez effectuer contre le stockage de fichiers consiste à télécharger des fichiers. Dans l’exemple suivant, le code télécharge SampleFile.txt et affiche son contenu.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the directory that contains the file
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    //Get a reference to the file you want to download
    CloudFile file = sampleDir.getFileReference("SampleFile.txt");

    //Write the contents of the file to the console.
    System.out.println(file.downloadText());

## <a name="how-to-delete-a-file"></a>Comment : supprimer un fichier

Une autre opération de stockage de fichier courante est la suppression des fichiers. Le code suivant supprime un fichier nommé SampleFile.txt, stocké dans un répertoire nommé **sampledir**.


    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory where the file to be deleted is in
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    String filename = "SampleFile.txt"
    CloudFile file;

    file = containerDir.getFileReference(filename)
    if ( file.deleteIfExists() ) {
        System.out.println(filename + " was deleted");
    }


## <a name="how-to-delete-a-directory"></a>Comment : supprimer un répertoire

Suppression d’un répertoire d’est une tâche relativement simple, bien qu’il convient de noter que vous ne pouvez pas supprimer un répertoire contenant des fichiers ou autres répertoires.

    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory you want to delete
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    // Delete the directory
    if ( containerDir.deleteIfExists() ) {
        System.out.println("Directory deleted");
    }


## <a name="how-to-delete-a-share"></a>Comment : supprimer un partage

Suppression d’un partage s’effectue en appelant la méthode **deleteIfExists** sur un objet CloudFileShare. Voici un exemple de code qui effectue cette opération.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the file client.
       CloudFileClient fileClient = storageAccount.createCloudFileClient();

       // Get a reference to the file share
       CloudFileShare share = fileClient.getShareReference("sampleshare");

       if (share.deleteIfExists()) {
           System.out.println("sampleshare deleted");
       }
    } catch (Exception e) {
        e.printStackTrace();
    }

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez en savoir plus sur les autre API de stockage Azure, suivez ces liens.

- [Centre pour développeurs Java](http://azure.microsoft.com/develop/java/)
- [Stockage Azure SDK pour Java](https://github.com/azure/azure-storage-java)
- [Stockage Azure SDK pour Android](https://github.com/azure/azure-storage-android)
- [Référence du Kit de développement logiciel Client stockage Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
