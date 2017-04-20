<properties
    pageTitle="Créer et utiliser un SAS avec stockage Blob | Microsoft Azure"
    description="Ce didacticiel vous montre comment créer des signatures d’accès partagé pour une utilisation avec le stockage Blob et comment les utiliser dans vos applications clientes."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Partagé des Signatures d’accès, partie 2 : Créer et utiliser un SAS avec stockage Blob

## <a name="overview"></a>Vue d’ensemble

La [partie 1](storage-dotnet-shared-access-signature-part-1.md) de ce didacticiel exploré partagés accès signatures (SAS) et décrit les méthodes conseillées pour les utiliser. Partie 2 explique comment générer et utiliser ensuite des signatures d’accès partagé avec le stockage des objets Blob. Les exemples sont écrits en C# et utilisent la bibliothèque cliente du stockage Azure pour .NET. Les scénarios présentés incluent ces aspects de l’utilisation de signatures d’accès partagé :

- Génération d’une signature d’accès partagé dans un conteneur
- Génération d’une signature d’accès partagé dans un blob
- Création d’une stratégie d’accès stockée pour gérer les signatures sur les ressources d’un conteneur
- Les signatures d’un accès partagé à partir d’une application cliente de test

## <a name="about-this-tutorial"></a>À propos de ce didacticiel

Dans ce didacticiel, nous allons nous concentrer sur la création d’un accès partagé des signatures pour les conteneurs et les objets BLOB en créant deux applications de console. La première application console génère des signatures d’un accès partagé à un conteneur et un blob. Cette application a connaissance des clés de compte de stockage. La deuxième application console qui servira d’une application cliente, accède au conteneur et les ressources de blob à l’aide de signatures accès partagé créés à l’aide de la première application. Cette application utilise les signatures de l’accès partagé uniquement et authentifier ses accès au conteneur de blob de ressources - il ne connaît pas les clés de compte.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Partie 1 : Créer une Application Console pour générer des Signatures de l’accès partagé

Tout d’abord, assurez-vous que vous disposez de la bibliothèque cliente du stockage Azure pour .NET est installé. Vous pouvez installer le [package NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "package NuGet") contenant les assemblys plus récentes pour la bibliothèque cliente ; Il s’agit de la méthode recommandée pour s’assurer que vous disposez des derniers correctifs. Vous pouvez également télécharger la bibliothèque de client dans le cadre de la version la plus récente du [Kit de développement logiciel Azure pour .NET](https://azure.microsoft.com/downloads/).

Dans Visual Studio, créez une nouvelle application de console Windows et nommez-le **GenerateSharedAccessSignatures**. Ajoutez des références à **Microsoft.WindowsAzure.Configuration.dll** et à **Microsoft.WindowsAzure.Storage.dll**, à l’aide d’une des approches suivantes :

-   Si vous souhaitez installer le package NuGet, tout d’abord installer le [NuGet Client](https://docs.nuget.org/consume/installing-nuget). Dans Visual Studio, sélectionnez le projet de **| Gérer les Packages NuGet**, rechercher en ligne des **Stockage Azure**et suivez les instructions d’installation.
-   Localisez les assemblys dans votre installation du Kit de développement Azure et ajouter des références à ceux-ci.

En haut du fichier Program.cs, ajoutez les instructions **using** suivantes :

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Modifiez le fichier app.config de sorte qu’il contienne un paramètre de configuration avec une chaîne de connexion qui pointe vers votre compte de stockage. Votre fichier app.config doit ressembler à celle-ci :

    <configuration>
      <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings>
    </configuration>

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Générer une Signature d’accès partagé URI pour un conteneur

Dans un premier temps, nous allons ajouter une méthode pour générer une signature d’accès partagé dans un nouveau conteneur. Dans ce cas la signature n’est pas associée à une stratégie d’accès stockée, afin qu’elle exerce l’URI les informations indiquant sa date d’expiration et les autorisations qu’il accorde.

Tout d’abord, ajoutez le code dans la méthode **Main()** pour authentifier l’accès à votre compte de stockage et de créer un nouveau conteneur :

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Insert calls to the methods created below here...

        //Require user input before closing the console window.
        Console.ReadLine();
    }

Ensuite, ajoutez une nouvelle méthode qui génère la signature de l’accès partagé pour le conteneur et retourne l’URI de la signature :

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Au bas de la méthode **Main()** , avant l’appel à **console.ReadLine ()**, appelez **GetContainerSasUri()** et écrire la signature URI dans la fenêtre de console, ajoutez les lignes suivantes :

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Compilez et exécutez la signature d’accès partagé URI pour le nouveau conteneur de sortie. L’URI est identique à l’URI suivant :       

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Une fois que vous avez exécuté le code, la signature d’accès partagé que vous avez créé sur le conteneur sera valide pendant vingt-quatre heures. La signature autorise un client à blobs dans le conteneur et d’écrire un objet blob de nouveau vers le conteneur de la liste.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Générer une Signature d’accès partagé URI pour un objet Blob

Ensuite, nous écrirons un code similaire pour créer un blob de nouveau dans le conteneur et de générer une signature d’accès partagé pour elle. Cette signature accès partagé n’est pas associée à une stratégie d’accès stockée, afin qu’il comporte l’heure de début, date d’expiration, les informations d’autorisation sur l’URI.

Ajouter une nouvelle méthode qui crée un objet blob de nouveau et écrire du texte, génère une signature de l’accès partagé, puis retourne l’URI de la signature :

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

En bas de la méthode **Main()** , ajoutez les lignes suivantes pour appeler **GetBlobSasUri()**, avant l’appel à **console.ReadLine ()**et d’écrire la signature d’accès partagé URI dans la fenêtre de console :    

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();


Compilez et exécutez la signature d’accès partagé URI pour le blob de nouveau de sortie. L’URI est identique à l’URI suivant :

    https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### <a name="create-a-stored-access-policy-on-the-container"></a>Créer une stratégie d’accès stockée sur le conteneur

Maintenant nous allons créer une stratégie d’accès stockée sur le conteneur, qui définit les contraintes pour toutes les signatures accès partagé qui lui sont associés.

Dans les exemples précédents, nous avons spécifié l’heure de début (implicitement ou explicitement), le délai d’expiration, ainsi que les autorisations sur la signature d’accès partagé URI lui-même. Dans les exemples suivants, nous spécifie ces sur la stratégie d’accès stockées et non sur la signature d’accès partagé. Cela nous permet de modifier ces contraintes sans devoir réémettre la signature d’accès partagé.

Il est possible d’avoir une ou plusieurs des contraintes sur la signature d’un accès partagé et le reste sur la stratégie d’accès stockée. Toutefois, vous ne pouvez spécifier l’heure de début, date d’expiration et les autorisations dans un seul endroit ou de l’autre ; par exemple, vous ne peut pas spécifier des autorisations sur la signature d’un accès partagé et également de les spécifier sur la stratégie d’accès stockée.

Notez que lorsque vous ajoutez une stratégie d’accès à un conteneur, vous devez obtenir les autorisations existantes du conteneur, ajouter la nouvelle stratégie d’accès et puis définissez les autorisations du conteneur.

Ajoutez une nouvelle méthode qui crée une nouvelle stratégie d’accès stockées dans un conteneur et renvoie le nom de la stratégie :

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
        string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

Au bas de la méthode **Main()** , avant l’appel à **console.ReadLine ()**, ajoutez les lignes suivantes à la première clair des stratégies d’accès existantes et appelez ensuite la méthode **CreateSharedAccessPolicy()** :    

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Notez que lorsque vous désactivez les stratégies d’accès à un conteneur, vous devez tout d’abord obtenir les autorisations existantes du conteneur, puis désactivez les autorisations, puis définissez les autorisations à nouveau.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Générer une Signature d’accès partagé URI sur le conteneur qui utilise une stratégie d’accès

Ensuite, nous allons créer une autre signature accès partagé sur le conteneur que nous avons créée précédemment, mais cette fois-ci, que nous allons associer la signature de la stratégie d’accès que nous avons créée dans l’exemple précédent.

Ajoutez une nouvelle méthode pour générer une autre signature accès partagé sur le conteneur :

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generate the shared access signature on the container. In this case, all of the constraints for the
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Au bas de la méthode **Main()** , avant l’appel à **console.ReadLine ()**, ajoutez les lignes suivantes pour appeler la méthode **GetContainerSasUriWithPolicy** :

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Générer une Signature d’accès partagé l’URI de l’objet Blob qui utilise une stratégie d’accès

Enfin, nous ajouterons une méthode similaire pour créer un autre blob et générer une signature d’accès partagé qui est associée à une stratégie d’accès.

Ajoutez une nouvelle méthode pour créer un objet blob et de générer une signature d’accès partagé :

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " +
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Au bas de la méthode **Main()** , avant l’appel à **console.ReadLine ()**, ajoutez les lignes suivantes pour appeler la méthode **GetBlobSasUriWithPolicy** :    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

La méthode **Main()** doit maintenant ressembler à ceci dans son intégralité. Exécutez-le pour écrire la signature d’accès partagé URI dans la fenêtre de la console, puis la copier et la coller dans un fichier texte à utiliser dans la deuxième partie de ce didacticiel.

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        Console.ReadLine();
    }

Lorsque vous exécutez l’application console GenerateSharedAccessSignatures, vous verrez une sortie similaire à ce qui suit dans la fenêtre de console. Ce sont les signatures d’accès partagé que vous allez utiliser dans la partie 2 de ce didacticiel.

![SAS-console-sortie-1][sas-console-output-1]

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Partie 2 : Créer une Application Console pour tester les Signatures d’accès partagé

Pour tester les signatures d’accès partagé créés dans les exemples précédents, nous allons créer une deuxième application console qui utilise les signatures pour effectuer des opérations sur le conteneur et sur un objet blob.

> [AZURE.NOTE] Si plus de 24 heures se sont écoulés depuis la fin de la première partie du didacticiel, les signatures que vous avez généré ne sera plus valides. Dans ce cas, vous devez exécuter le code dans la première application de console pour générer des signatures fraîches accès partagé pour une utilisation dans la deuxième partie du didacticiel.

Dans Visual Studio, créez une nouvelle application de console Windows et nommez-le **ConsumeSharedAccessSignatures**. Ajoutez des références à **Microsoft.WindowsAzure.Configuration.dll** et **Microsoft.WindowsAzure.Storage.dll**, comme vous l’avez fait précédemment.

En haut du fichier Program.cs, ajoutez les instructions **using** suivantes :

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Dans le corps de la méthode **Main()** , ajoutez les constantes suivantes et mettre à jour leurs valeurs pour les signatures d’accès partagé que vous avez généré dans la partie 1 de ce didacticiel.

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Ajoutez une méthode pour tester les opérations de conteneur à l’aide d’une Signature d’accès partagé

Ensuite, nous allons ajouter une méthode qui vérifie certaines opérations représentatif de conteneur à l’aide d’une signature d’accès partagé sur le conteneur. Notez que la signature de l’accès partagé est utilisée pour renvoyer une référence au conteneur, l’authentification de l’accès au conteneur en fonction de la signature uniquement.

Dans Program.cs, ajoutez la méthode suivante :

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Mettre à jour la méthode **Main()** pour appeler **UseContainerSAS()** avec deux signatures d’accès partagé que vous avez créé dans le conteneur :

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        Console.ReadLine();
    }


### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Ajoutez une méthode pour tester les opérations de Blob à l’aide d’une Signature d’accès partagé

Enfin, nous allons ajouter une méthode qui vérifie certaines opérations représentatif de blob à l’aide d’une signature d’accès partagé dans le blob. Dans ce cas, nous utilisons le constructeur **CloudBlockBlob(String)**, en passant dans la signature d’accès partagé, pour renvoyer une référence à l’objet blob. Aucune authentification n’est requise ; Il est basé sur la signature seule.

Dans Program.cs, ajoutez la méthode suivante :

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Mettre à jour la méthode **Main()** pour appeler **UseBlobSAS()** avec deux signatures d’accès partagé que vous avez créé sur le blob :

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Exécutez l’application console et observez la sortie pour voir quelles opérations sont autorisées pour les signatures. La sortie dans la fenêtre de la console sera semblable au suivant :

![SAS-console-sortie-2][sas-console-output-2]

## <a name="next-steps"></a>Étapes suivantes

[Signatures d’accès partagé, partie 1 : Présentation du modèle SAS](storage-dotnet-shared-access-signature-part-1.md)

[Gérer l’accès anonyme en lecture à des conteneurs et des objets BLOB](storage-manage-access-to-resources.md)

[Délégation de l’accès à l’aide d’une Signature d’accès partagé (API REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Présentation de Table et la file d’attente SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG
