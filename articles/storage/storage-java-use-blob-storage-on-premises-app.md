<properties
    pageTitle="Application avec le stockage des objets blob (Java) sur site | Microsoft Azure"
    description="Apprenez à créer une application console qui télécharge une image vers Azure, puis affiche l’image dans votre navigateur. Exemples de code en Java."
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="on-premises-application-with-blob-storage"></a>Application sur site avec le stockage des objets blob

## <a name="overview"></a>Vue d’ensemble

L’exemple suivant vous montre comment vous pouvez utiliser le stockage Azure pour stocker les images dans Azure. Le code de cet article est pour une application console qui télécharge une image vers Azure, puis crée un fichier HTML qui affiche l’image dans votre navigateur.

## <a name="prerequisites"></a>Conditions préalables

- Un Kit de développement Java (JDK), version 1.6 ou ultérieure, est installé.
- Le Kit de développement Azure est installé.
- Le fichier JAR pour les bibliothèques d’Azure pour Java et les fichiers JAR dépendance applicable, est installés et est dans le chemin d’accès de build utilisé par votre compilateur Java. Pour plus d’informations sur l’installation des bibliothèques Azure pour Java, reportez-vous à la section [Télécharger Azure SDK pour Java](java-download-azure-sdk.md).
- Un compte de stockage Azure a été défini. Le nom de compte et clé de compte pour le compte de stockage va être utilisés par le code dans cet article. Pour plus d’informations sur la création d’un compte de stockage et [Afficher et copier stockage touches d’accès rapide](storage-create-storage-account.md#view-and-copy-storage-access-keys) pour plus d’informations sur la récupération de la clé de compte, reportez-vous à la section [comment créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) .

- Vous avez créé un fichier d’image local nommé stockés sur le chemin c:\\myimages\\image1.jpg. Sinon, modifier le constructeur   **FileInputStream** dans l’exemple pour utiliser un nom de chemin d’accès et de fichier image différente.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="to-use-azure-blob-storage-to-upload-a-file"></a>Pour utiliser le stockage blob Azure pour télécharger un fichier

Une procédure pas à pas est présentée ici. Si vous souhaitez passer, l’ensemble du code est présenté plus loin dans cet article.

Commencer le code en incluant des importations pour les classes de stockage Azure de base, les classes clientes de blob Azure, les classes d’e/s de Java et la classe **URISyntaxException** .

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

Déclarez une classe nommée **StorageSample**et inclure le crochet ouvrant, **{**.

    public class StorageSample {

Dans la classe **StorageSample** , déclarez une variable chaîne qui contient le protocole de point de terminaison par défaut, le nom de votre compte de stockage et votre clé d’accès de stockage, comme indiqué dans votre compte de stockage Azure. Remplacer les valeurs d’espace réservé **votre\_compte\_nom de** et **votre\_compte\_clé** avec votre propre clé de compte nom et compte, respectivement.

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

Ajouter dans votre déclaration de **principal**, inclure un bloc **try** et inclure les parenthèses ouvertes nécessaires, **{**.

    public static void main(String[] args)
    {
        try
        {

Déclarer des variables de type suivant (les descriptions sont pour la façon dont ils sont utilisés dans cet exemple) :

-   **CloudStorageAccount**: utilisé pour initialiser l’objet de compte avec votre nom de compte de stockage Azure et d’une clé et pour créer l’objet client de blob.
-   **CloudBlobClient**: utilisé pour accéder au service d’objet blob.
-   **CloudBlobContainer**: permet de créer un conteneur blob, les BLOB dans le conteneur de liste et supprimer le conteneur.
-   **CloudBlockBlob**: permet de télécharger un fichier d’image local au conteneur.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Assignez une valeur à la variable de **compte** .

    account = CloudStorageAccount.parse(storageConnectionString);

Affecter une valeur à la variable **serviceClient** .

    serviceClient = account.createCloudBlobClient();

Assignez une valeur à la variable de **conteneur** . Nous allons obtenir une référence à un conteneur nommé **mise en route**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Créez le conteneur. Cette méthode crée le conteneur s’il n’existe (et retourner la **valeur true**). Si le conteneur existe, il retourne la **valeur false**. Une alternative à le **createIfNotExists** est la méthode de **Création** (qui renvoie une erreur si le conteneur existe déjà).

    container.createIfNotExists();

Définir l’accès anonyme pour le conteneur.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Obtenir une référence à l’objet blob bloc, qui représentera l’objet blob dans le stockage Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Utilisez le constructeur de **fichier** pour obtenir une référence au fichier créé en local que vous allez télécharger. Vérifiez que vous avez créé ce fichier avant d’exécuter le code.

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Téléchargez le fichier local via un appel à la méthode **CloudBlockBlob.upload** . Le premier paramètre à la méthode **CloudBlockBlob.upload** est un objet **FileInputStream** qui représente le fichier local qui sera téléchargé vers le stockage Azure. Le deuxième paramètre est la taille, en octets, du fichier.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Appel d’une fonction d’assistance nommée **MakeHTMLPage**, pour rendre une page HTML de base qui contient un ** &lt;image&gt; ** élément avec la source de la valeur qui se trouve maintenant dans votre compte de stockage Azure le blob. Le code de **MakeHTMLPage** sera abordé plus loin dans cet article.

    MakeHTMLPage(container);

Imprime un message d’état et les informations de la page HTML créée.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Fermez le bloc **try** en insérant un crochet fermant : **}**

Gérer les exceptions suivantes :

-   **FileNotFoundException**: peuvent être levées par les constructeurs **FileInputStream** ou **FileOutputStream** .
-   **StorageException**: peuvent être levées par la bibliothèque de stockage Azure client.
-   **URISyntaxException**: peut être levée par la méthode **ListBlobItem.getUri** .
-   **Exception**: gestion des exceptions générique.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Fermez **principal** en insérant un crochet fermant : **}**

Créez une méthode nommée **MakeHTMLPage** pour créer une page HTML de base. Cette méthode possède un paramètre de type **CloudBlobContainer**, qui sera utilisé pour parcourir la liste des objets BLOB téléchargés. Cette méthode lèvera des exceptions de type **FileNotFoundException**, qui peuvent être levées par le constructeur de **FileOutputStream** et **URISyntaxException**, qui peuvent être levées par la méthode **ListBlobItem.getUri** . Inclure le crochet ouvrant, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Créer un fichier local nommé **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Écrire dans le fichier local en ajoutant dans le ** &lt;html&gt;**, ** &lt;en-tête&gt;**, et ** &lt;body&gt; ** éléments.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Parcourez la liste des objets BLOB téléchargés. Pour chaque objet blob, dans la page HTML, créer un ** &lt;img&gt; ** élément dont l’attribut **src** est envoyée à l’URI de l’objet blob telle qu’elle existe dans votre compte de stockage Azure.
Bien que vous avez ajouté une seule image dans cet exemple, si vous avez ajouté plus, ce code serait parcourir toutes les.

Pour plus de simplicité, cet exemple suppose que chaque blob téléchargé est une image. Si vous avez mis à jour BLOB autres que des images ou des blobs de page plutôt que des objets BLOB de bloc, modifier le code si nécessaire.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Fermer la ** &lt;body&gt; ** élément et la ** &lt;html&gt; ** élément.

    stream.println("</body>");
    stream.println("</html>");

Fermez le fichier local.

    stream.close();

Fermer **MakeHTMLPage** en insérant un crochet fermant : **}**

Fermer **StorageSample** en insérant un crochet fermant : **}**

Voici le code complet de cet exemple. N’oubliez pas de modifier les valeurs d’espace réservé **votre\_compte\_nom de** et **votre\_compte\_clé** utilisation de votre nom de compte et votre clé de compte, respectivement.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

En plus du téléchargement de votre fichier d’image local vers le stockage Azure, l’exemple de code crée un fichier local namedindex.html, que vous pouvez ouvrir dans votre navigateur pour afficher votre image téléchargée.

Étant donné que le code contient votre nom de compte et votre clé de compte, vérifiez que votre code source est sécurisé.

## <a name="to-delete-a-container"></a>Pour supprimer un conteneur

Car vous sont facturés pour le stockage, vous souhaiterez peut-être supprimer le conteneur de **mise en route** , une fois que vous avez terminé l’expérimentation de cet exemple. Pour supprimer un conteneur, utilisez la méthode **CloudBlobContainer.delete** .

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Pour appeler la méthode **CloudBlobContainer.delete** , le processus d’initialisation des objets **CloudStorageAccount**, **ClodBlobClient**et **CloudBlobContainer** est le même que celui indiqué pour la méthode **createIfNotExist** . Voici un exemple complet qui supprime le conteneur nommé **mise en route**.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Pour une vue d’ensemble des autres classes de stockage blob et des méthodes, voir [comment utiliser le stockage Blob à partir de Java](storage-java-how-to-use-blob-storage.md).

## <a name="next-steps"></a>Étapes suivantes

Cliquez sur ces liens pour en savoir plus sur les tâches de stockage plus complexes.

- [Stockage Azure SDK pour Java](https://github.com/azure/azure-storage-java)
- [Référence du Kit de développement logiciel Client stockage Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
