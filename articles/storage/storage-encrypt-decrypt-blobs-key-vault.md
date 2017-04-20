<properties
    pageTitle="Didacticiel : Crypter et décrypter le BLOB dans le stockage Azure de Microsoft à l’aide de la chambre forte de clé Azure | Microsoft Azure"
    description="Ce didacticiel vous explique comment chiffrer et déchiffrer un blob en utilisant le cryptage côté client pour Microsoft Azure Storage avec Azure clé coffre-fort."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="required"
    ms.date="10/18/2016"
    ms.author="lakasa;robinsh"/>

# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Didacticiel : Crypter et décrypter le BLOB dans le stockage Azure de Microsoft à l’aide de la chambre forte de clé Azure

## <a name="introduction"></a>Introduction

Ce didacticiel décrit comment rendre l’utilisation de cryptage de stockage côté client avec Azure clé coffre-fort. Il vous explique comment faire pour chiffrer et déchiffrer un blob dans une application console à l’aide de ces technologies.

**Les temps estimé pour terminer :** 20 minutes

Pour plus d’informations sur le coffre-fort de clé d’Azure, consultez [Quel est le coffre-fort de clé Azure ?](../key-vault/key-vault-whatis.md).

Pour plus d’informations sur le chiffrement côté client pour le stockage Azure, consultez [cryptage côté Client et la chambre forte de clé Azure pour le stockage Azure Microsoft](storage-client-side-encryption.md).


## <a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez disposer des éléments suivants :

- Un compte de stockage Azure
- Visual Studio 2013 ou version ultérieure
- PowerShell Azure


## <a name="overview-of-client-side-encryption"></a>Vue d’ensemble du cryptage côté client

Pour une vue d’ensemble du chiffrement côté client pour le stockage Azure, consultez [cryptage côté Client et la chambre forte de clé Azure pour le stockage Azure Microsoft](storage-client-side-encryption.md)

Voici une brève description du fonctionne de chiffrement côté client :

1. Le client de stockage Azure SDK génère une clé de chiffrement de contenu (CEK), qui est une clé symétrique usage unique.
2. Les données de client sont cryptées à l’aide de cette CEK.
3. Le CEK est ensuite encapsulé (crypté) à l’aide de la clé de cryptage de clés (KEK). Le KEK est identifiée par un identificateur de clé et peut être une paire de clés asymétrique ou une clé symétrique et peut être gérée localement ou stockée dans Azure clé coffre-fort. Le client de stockage lui-même n’a jamais accès à la KEK. Il appelle simplement l’algorithme de clé d’habillage qui est fourni par le stockage en chambre forte de clé. Clients peuvent choisir d’utiliser des fournisseurs personnalisés pour clé habillage/dévoilement s’ils le souhaitent.
4. Les données cryptées sont ensuite téléchargées sur le service de stockage Azure.


## <a name="set-up-your-azure-key-vault"></a>Configurer votre coffre-fort de clé Azure
Pour continuer avec ce didacticiel, vous devez effectuer les étapes suivantes, qui sont décrites dans le didacticiel [mise en route de la chambre forte de clé Azure](../key-vault/key-vault-get-started.md):

- Créer un coffre-fort de clé.
- Ajouter une clé ou un secret dans la chambre forte de clé.
- Inscription d’une application dans Azure Active Directory.
- Autoriser l’application à utiliser la clé ou le secret.

Notez l’identifiant du client et ClientSecret qui ont été générés lors de l’enregistrement d’une application dans Azure Active Directory.

Créez les deux clés dans la chambre forte de clé. Pour le reste de ce didacticiel, nous supposons que vous avez utilisé les noms suivants : ContosoKeyVault et TestRSAKey1.


## <a name="create-a-console-application-with-packages-and-appsettings"></a>Créez une application console avec les packages et AppSettings

Dans Visual Studio, créez une nouvelle application de console.

Ajouter des packages nuget nécessaires dans la Console du Gestionnaire de package.

    Install-Package WindowsAzure.Storage

    // This is the latest stable release for ADAL.
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault
    Install-Package Microsoft.Azure.KeyVault.Extensions


Ajouter AppSettings dans le fichier App.Config.

    <appSettings>
        <add key="accountName" value="myaccount"/>
        <add key="accountKey" value="theaccountkey"/>
        <add key="clientId" value="theclientid"/>
        <add key="clientSecret" value="theclientsecret"/>
        <add key="container" value="stuff"/>
    </appSettings>

Ajoutez le code suivant `using` instructions et veillez à ajouter une référence à System.Configuration pour le projet.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.Azure.KeyVault;
    using System.Threading;     
    using System.IO;


## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Ajoutez une méthode pour obtenir un jeton à votre application de console

La méthode suivante est utilisée par les classes de stockage en chambre forte de clé qui doivent s’authentifier pour accéder à votre clé coffre-fort.

    private async static Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(
            ConfigurationManager.AppSettings["clientId"],
            ConfigurationManager.AppSettings["clientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

## <a name="access-storage-and-key-vault-in-your-program"></a>Stockage à accès et stockage en chambre forte de la clé dans votre programme

Dans la fonction Main, ajoutez le code suivant.

    // This is standard code to interact with Blob storage.
    StorageCredentials creds = new StorageCredentials(
        ConfigurationManager.AppSettings["accountName"],
        ConfigurationManager.AppSettings["accountKey"]);
    CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
    CloudBlobClient client = account.CreateCloudBlobClient();
    CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
    contain.CreateIfNotExists();

    // The Resolver object is used to interact with Key Vault for Azure Storage.
    // This is where the GetToken method from above is used.
    KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE] Modèles d’objet clé coffre-fort
>
>Il est important de comprendre qu’il n’y a en fait deux clé coffre-fort modèles d’objet à connaître : un est basé sur l’API REST (espace de noms KeyVault) et l’autre est une extension pour le chiffrement côté client.

> Le Client de chambre forte de clé interagit avec l’API REST et comprend des touches Web de JSON et les secrets pour les deux types d’éléments qui sont contenus dans le coffre-fort de la clé.

> Les Extensions de stockage en chambre forte de clé sont des classes qui semblent créés spécifiquement pour le chiffrement côté client dans le stockage Azure. Ils contiennent une interface pour les clés (IKey) et les classes basées sur le concept d’un résolveur de clé. Il existe deux implémentations de IKey dont vous avez besoin de savoir : RSAKey et SymmetricKey. Maintenant, elles se produisent pour coïncider avec les éléments contenus dans un coffre-fort de clé, mais à ce stade qu’ils sont des classes indépendantes (de sorte que la clé et le Secret, récupéré par le Client de chambre forte de clé n’implémentent pas IKey).


## <a name="encrypt-blob-and-upload"></a>Crypter les blob et télécharger
Ajoutez le code suivant pour crypter un blob et le télécharger vers votre compte de stockage Azure. La méthode **ResolveKeyAsync** est utilisée renvoie un IKey.


    // Retrieve the key that you created previously.
    // The IKey that is returned here is an RsaKey.
    // Remember that we used the names contosokeyvault and testrsakey1.
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


    // Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Reference a block blob.
    CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

    // Upload using the UploadFromStream method.
    using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
        blob.UploadFromStream(stream, stream.Length, null, options, null);


Voici une capture d’écran à partir du [Portail classique d’Azure](https://manage.windowsazure.com) pour un blob qui a été chiffré à l’aide du cryptage côté client avec une clé stockée dans la clé de stockage en chambre forte. La propriété **ID de la clé** est l’URI de la clé dans le coffre-fort de clé qui agit comme le KEK. La propriété **EncryptedKey** contient la version cryptée de la CEK.

![Capture d’écran présentant des métadonnées de Blob qui inclut des métadonnées de cryptage][1]

> [AZURE.NOTE] Si vous examinez le constructeur BlobEncryptionPolicy, vous verrez qu’il peut accepter une clé ou un programme de résolution. N’oubliez pas que pour le moment, vous ne pouvez pas utiliser un programme de résolution pour le cryptage, car il n’a pas actuellement prendre en charge une clé par défaut.



## <a name="decrypt-blob-and-download"></a>Décrypter le blob et télécharger
Le déchiffrement est vraiment lorsque l’utilisation des classes de programme de résolution de sens. L’ID de la clé utilisée pour le cryptage est associé à l’objet blob dans ses métadonnées, afin qu’il n’y a aucune raison de vous permet de récupérer la clé et n’oubliez pas de l’association entre la clé et blob. Il vous suffit de vous assurer que la clé reste dans le coffre-fort de la clé.   

La clé privée d’une clé RSA reste dans le coffre-fort de la clé, et pour le déchiffrement à se produire, la clé cryptée à partir des métadonnées blob qui contient le CEK sont envoyée à la chambre forte de clé pour le décryptage.

Ajoutez le code suivant pour déchiffrer le blob que vous venez de télécharger.

    // In this case, we will not pass a key and only pass the resolver because
    // this policy will only be used for downloading / decrypting.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
        blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE] Il existe deux autres types de résolveurs pour faciliter la gestion des clés, y compris : AggregateKeyResolver et CachingKeyResolver.


## <a name="use-key-vault-secrets"></a>Utilisez des secrets de la chambre forte de clé
La façon d’utiliser un secret avec cryptage côté client est via la classe SymmetricKey car un secret est essentiellement une clé symétrique. Mais, comme mentionné ci-dessus, un secret dans la chambre forte de clé ne correspond pas exactement à un SymmetricKey. Il y a quelques éléments à comprendre :


- La clé dans un SymmetricKey doit être d’une longueur fixe : 128, 192, 256, 384 ou 512 bits.
- La clé dans un SymmetricKey doit être encodé en base 64.
- Un secret en chambre forte de la clé qui sera utilisé comme un SymmetricKey doit avoir un Type de contenu de « application/octet-stream » dans une chambre forte de clé.

Voici un exemple dans PowerShell de créer un secret dans la chambre forte de clé qui peut être utilisé comme un SymmetricKey.
Remarque : La valeur codée en dur, $key, est uniquement à des fins de démonstration. Dans votre propre code, vous voudrez générer cette clé.

    // Here we are making a 128-bit key so we have 16 characters.
    //  The characters are in the ASCII range of UTF8 so they are
    //  each 1 byte. 16 x 8 = 128.
    $key = "qwertyuiopasdfgh"
    $b = [System.Text.Encoding]::UTF8.GetBytes($key)
    $enc = [System.Convert]::ToBase64String($b)
    $secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

    // Substitute the VaultName and Name in this command.
    $secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

Dans votre application console, vous pouvez utiliser le même appel en avant pour récupérer ce code secret comme un SymmetricKey.

    SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
        "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
        CancellationToken.None).GetAwaiter().GetResult();

Voilà. Profitez !

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Microsoft Azure Storage avec C#, consultez [Bibliothèque de Client Microsoft Azure stockage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Pour plus d’informations sur l’API REST de Blob, consultez [l’API REST de Service Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Pour obtenir les dernières informations sur Microsoft Azure Storage, allez sur le [Blog de l’équipe Microsoft Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png
