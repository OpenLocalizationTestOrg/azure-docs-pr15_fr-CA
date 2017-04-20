<properties
    pageTitle="Définir et récupérer des propriétés et métadonnées des objets dans le stockage Azure | Microsoft Azure"
    description="Stocker des métadonnées personnalisées sur des objets dans le stockage Azure et de définir et de récupérer les propriétés système."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="set-and-retrieve-properties-and-metadata"></a>Définir et récupérer des propriétés et métadonnées #

## <a name="overview"></a>Vue d’ensemble

Objets dans les propriétés de prise en charge du système de stockage Azure et les métadonnées définies par l’utilisateur, en plus des données qu’ils contiennent :

*   **Propriétés du système.** Il existe des propriétés du système sur chaque ressource de stockage. Certains d'entre eux vous pouvez lire ou définir, tandis que d’autres sont en lecture seule. En coulisses, certaines propriétés système correspondent à certains en-têtes HTTP standard. La bibliothèque cliente de stockage Azure gère pour vous.  

*   **Métadonnées définies par l’utilisateur.** Métadonnées définies par l’utilisateur sont des métadonnées que vous spécifiez pour une ressource donnée, sous la forme d’une paire nom-valeur. Vous pouvez utiliser des métadonnées pour stocker des valeurs supplémentaires avec une ressource de stockage ; Ces valeurs sont pour vos propres besoins uniquement et n’affectent pas le comportement de la ressource.  

Extraction des valeurs de propriété et des métadonnées pour une ressource de stockage est un processus en deux étapes. Avant que vous puissiez lire ces valeurs, vous devez les extraire explicitement en appelant la méthode **FetchAttributes** .

> [AZURE.IMPORTANT] Les valeurs de propriété et des métadonnées pour une ressource de stockage ne sont pas remplies, sauf si vous appelez l’une des méthodes **FetchAttributes** . 

## <a name="setting-and-retrieving-properties"></a>Définition et récupération de propriétés

Pour récupérer les valeurs de propriété, appelez la méthode **FetchAttributes** sur votre blob ou le conteneur pour remplir les propriétés, puis lisez les valeurs.

Pour définir des propriétés sur un objet, spécifier la valeur de la propriété, puis appelez la méthode **SetProperties** .

L’exemple de code suivant crée un conteneur et écrit certains de ses valeurs de propriété dans une fenêtre de console :

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    
    //Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## <a name="setting-and-retrieving-metadata"></a>Définition et récupération de métadonnées

Vous pouvez spécifier des métadonnées sous la forme d’une ou plusieurs paires nom-valeur d’une ressource blob ou le conteneur. Pour définir les métadonnées, ajouter des paires nom-valeur à la collection de **métadonnées** sur la ressource, puis appelez la méthode **SetMetadata** pour enregistrer les valeurs dans le service.

> [AZURE.NOTE] Le nom de métadonnées doit respecter les conventions d’affectation de noms pour les identificateurs de C#.
 
L’exemple de code suivant définit les métadonnées sur un conteneur. Une valeur est définie à l’aide de la méthode **Add** de la collection. L’autre valeur est définie à l’aide de la syntaxe de clé/valeur implicite. Les deux sont valides.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Pour récupérer des métadonnées, appelez la méthode **FetchAttributes** sur votre blob ou un conteneur pour remplir la collection de **métadonnées** , puis lisez les valeurs, comme illustré dans l’exemple ci-dessous.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## <a name="see-also"></a>Voir aussi  

- [Bibliothèque Client de stockage Azure pour référence .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Bibliothèque de Client stockage Azure pour le package .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) 
