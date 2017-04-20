<properties
    pageTitle="Gérer l’accès anonyme en lecture sur les conteneurs et les objets BLOB | Microsoft Azure"
    description="Découvrez comment rendre les conteneurs et les objets BLOB disponibles pour l’accès anonyme et comment y accéder par programmation."
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

# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gérer l’accès anonyme en lecture à des conteneurs et des objets BLOB

## <a name="overview"></a>Vue d’ensemble

Par défaut, seul le propriétaire du compte de stockage peut-être accéder à des ressources de stockage au sein de ce compte. Pour le stockage des objets Blob uniquement, vous pouvez définir des autorisations d’un conteneur pour autoriser l’accès anonyme en lecture au conteneur et à ses objets BLOB, afin que vous pouvez accorder l’accès à ces ressources sans partager votre clé de compte.

L’accès anonyme est préférable dans les cas où vous souhaitez que certains objets BLOB sont toujours disponibles pour l’accès anonyme en lecture. Pour le contrôle de plus grande ampleur, vous pouvez créer une signature d’accès partagé, ce qui permet à accès restreint de délégué à l’aide d’autorisations différentes et sur un intervalle de temps spécifié. Pour plus d’informations sur la création de signatures d’accès partagé, consultez [L’aide partagés accès Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Accorder des autorisations aux utilisateurs anonymes dans les conteneurs et les objets BLOB

Par défaut, un conteneur et les objets BLOB qu’il contient sont accessibles uniquement par le propriétaire du compte de stockage. Pour donner aux utilisateurs anonymes, autorisations de lecture sur le conteneur et ses objets BLOB, vous pouvez définir les autorisations du conteneur pour autoriser l’accès du public. Utilisateurs anonymes peuvent lire les objets BLOB dans un conteneur accessible au public sans s’authentifier la demande.

Les conteneurs fournissent les options suivantes pour la gestion de l’accès de conteneur :

- **Publics en lecture accès complet :** Conteneur et le blob de données peuvent être lues via une demande anonyme. Les clients peuvent énumérer des objets BLOB dans le conteneur via une demande anonyme, mais ne peut pas énumérer les conteneurs à l’intérieur du compte de stockage.

- **Public un accès pour les objets BLOB uniquement en lecture :** Les données BLOB dans ce conteneur peuvent être lus via une demande anonyme, mais les données de conteneur ne seront pas disponibles. Les clients ne peut pas énumérer les objets BLOB dans le conteneur via une demande anonyme.

- **Aucun public un accès en lecture :** Conteneur et le blob de données peuvent être lus par le propriétaire du compte uniquement.

Vous pouvez définir des autorisations du conteneur dans l’une des manières suivantes :

- À partir du [portail Azure](https://portal.azure.com).
- Par programme, en utilisant la bibliothèque de client de stockage ou de l’API REST.
- À l’aide de PowerShell. Pour en savoir plus sur la définition des autorisations du conteneur à partir de PowerShell d’Azure, consultez [L’aide de PowerShell Azure avec stockage Azure](storage-powershell-guide-full.md#how-to-manage-azure-blobs).

### <a name="setting-container-permissions-from-the-azure-portal"></a>Définition des autorisations du conteneur à partir du portail Azure

Pour définir les autorisations du conteneur à partir du [Portail Azure](https://portal.azure.com), procédez comme suit :

1. Accédez au tableau de bord pour votre compte de stockage.
2. Dans la liste, sélectionnez le nom du conteneur. Cliquez sur le nom expose les objets BLOB dans le conteneur sélectionné
3. Sélectionnez la **stratégie d’accès** à partir de la barre d’outils.
4. Dans le champ **type d’accès** , sélectionnez votre niveau d’autorisation souhaité, comme illustré dans la capture d’écran ci-dessous.

    ![Métadonnées du conteneur de boîte de dialogue Modifier](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="setting-container-permissions-programmatically-using-net"></a>Définition des autorisations du conteneur par programme à l’aide de .NET

Pour définir des autorisations pour un conteneur à l’aide de la bibliothèque cliente .NET, vous devez tout d’abord extraire les autorisations existantes du conteneur en appelant la méthode **GetPermissions** . Définissez ensuite la propriété **PublicAccess** pour l’objet **BlobContainerPermissions** qui est retourné par la méthode **GetPermissions** . Enfin, appelez la méthode **SetPermissions** avec les autorisations de mise à jour.

L’exemple suivant définit les autorisations du conteneur pour un accès en lecture publique complète. Pour définir les autorisations sur public un accès en lecture pour les objets BLOB uniquement, définissez la propriété **PublicAccess** à **BlobContainerPublicAccessType.Blob**. Pour supprimer toutes les autorisations pour les utilisateurs anonymes, affectez la propriété **BlobContainerPublicAccessType.Off**.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## <a name="access-containers-and-blobs-anonymously"></a>Accéder anonymement des conteneurs et des objets BLOB

Un client qui accède à des conteneurs et blobs de manière anonyme peut utiliser des constructeurs qui ne requièrent pas d’informations d’identification. Les exemples suivants montrent différentes manières pour référencer des ressources de service de Blob de manière anonyme.

### <a name="create-an-anonymous-client-object"></a>Créer un objet client anonyme

Vous pouvez créer un nouvel objet de service client pour l’accès anonyme en fournissant le point de terminaison de service Blob pour le compte. Toutefois, vous devez également connaître le nom d’un conteneur de ce compte est disponible pour l’accès anonyme.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### <a name="reference-a-container-anonymously"></a>Faire référence à un conteneur de manière anonyme

Si vous avez l’URL dans un conteneur qui est disponible de manière anonyme, vous pouvez l’utiliser pour référencer le conteneur directement.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### <a name="reference-a-blob-anonymously"></a>Faire référence à un objet blob anonyme

Si vous avez l’URL d’un blob qui est disponible pour l’accès anonyme, vous pouvez référencer l’objet blob directement à l’aide de cette URL :

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## <a name="features-available-to-anonymous-users"></a>Fonctionnalités disponibles pour les utilisateurs anonymes

Le tableau suivant montre les opérations qui peuvent être appelées par des utilisateurs anonymes lorsque les ACL d’un conteneur est définie pour permettre un accès public.

| Opération de reste                                         | Autorisation avec un accès en lecture publique complète | Autorisation public accès en lecture pour les objets BLOB uniquement |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| Conteneurs de la liste                                        | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Créez le conteneur                                       | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Obtenir les propriétés du conteneur                               | Tous les                                     | Propriétaire uniquement                                        |
| Obtenir les métadonnées du conteneur                                 | Tous les                                     | Propriétaire uniquement                                        |
| Métadonnées du jeu de conteneur                                 | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Obtenir le conteneur d’ACL                                      | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Définir des ACL de conteneur                                      | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Supprimer le conteneur                                       | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Liste des BLOB                                             | Tous les                                     | Propriétaire uniquement                                        |
| Placer les Blob                                               | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Obtenir des Blob                                               | Tous les                                     | Tous les                                               |
| Obtenir les propriétés de l’objet Blob                                    | Tous les                                     | Tous les                                               |
| Définir les propriétés de l’objet Blob                                    | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Obtenir les métadonnées de l’objet Blob                                      | Tous les                                     | Tous les                                               |
| Métadonnées du jeu de Blob                                      | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Placez le bloc                                              | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Obtenir la liste de blocage (blocs validées uniquement)                 | Tous les                                     | Tous les                                               |
| Obtenir la liste de blocage (blocs non validées uniquement ou tous les blocs) | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Placez la liste de blocage                                         | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Supprimer des Blob                                            | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Copier des Blob                                              | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Blob de capture instantanée                                          | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Blob de bail                                             | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Placez la Page                                               | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Les plages de pages Get                                        | Tous les                                     | Tous les                                                  |
| Ajouter des Blob                                            | Propriétaire uniquement                              | Propriétaire uniquement                                                  |


## <a name="see-also"></a>Voir aussi

- [Authentification pour les Services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [À l’aide de Signatures d’accès partagé (SAS)](storage-dotnet-shared-access-signature-part-1.md)
- [Délégation de l’accès avec une Signature d’accès partagé](https://msdn.microsoft.com/library/azure/ee395415.aspx)
