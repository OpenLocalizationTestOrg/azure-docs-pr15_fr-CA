<properties
    pageTitle="Comment faire pour utiliser le stockage Azure Blob d’iOS | Microsoft Azure"
    description="Stocker des données non structurées dans le nuage avec les Blob Azure le stockage (objet)."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-ios"></a>Comment faire pour utiliser le stockage Blob d’iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article vous explique comment exécuter des scénarios courants utilisant le stockage Blob Azure de Microsoft. Les exemples sont écrits en Objective-C et utilisent la [Bibliothèque de Client de stockage Azure pour iOS](https://github.com/Azure/azure-storage-ios). Les scénarios présentés incluent le **téléchargement**, **liste**, **téléchargement**et **Supprimer** les objets BLOB. Pour plus d’informations sur les objets BLOB, reportez-vous à la section [Étapes suivantes](#next-steps) . Vous pouvez également télécharger l' [exemple d’application](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) pour voir rapidement l’utilisation du stockage Azure dans une application iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importer la bibliothèque d’e/s de stockage Azure dans votre application

Vous pouvez importer la bibliothèque e/s de stockage Azure dans votre application à l’aide de la [CocoaPod de stockage Azure](https://cocoapods.org/pods/AZSClient) ou en important le fichier du **Framework** .

## <a name="cocoapod"></a>CocoaPod

1. Si vous n’avez pas déjà fait, [Installez un CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) sur votre ordinateur en ouvrant une fenêtre de terminal et en exécutant la commande suivante

        sudo gem install cocoapods

2. Ensuite, dans le répertoire du projet (le répertoire contenant votre `.xcodeproj` fichier), créer un nouveau fichier appelé `Podfile`(aucune extension de fichier). Ajoutez le code suivant à `Podfile` et enregistrer

        pod 'AZSClient'

3. Dans la fenêtre du terminal, naviguez jusqu’au répertoire du projet et exécutez la commande suivante

        pod install

4. Si votre `.xcodeproj` est ouvert dans Xcode, de le fermer. Dans votre répertoire de projet, ouvrez le fichier de projet nouvellement créé qui affichera le `.xcworkspace` extension. C’est le fichier que vous allez utiliser à partir de maintenant.

## <a name="framework"></a>Framework
Pour utiliser la bibliothèque d’e/s de stockage Azure, vous devez d’abord créer le fichier de framework.

1. Tout d’abord, téléchargez ou cloner [mis en pension d’azure-stockage-ios](https://github.com/azure/azure-storage-ios).

2. Allez dans *azure-stockage-ios* -> *Lib* -> *Bibliothèque Client de stockage Azure*, puis ouvrez `AZSClient.xcodeproj` dans Xcode.

3. Dans le coin supérieur gauche de Xcode, modifier le schéma d’actif à partir de la « Bibliothèque de Client de stockage Azure » à « Infrastructure ».

4. Générez le projet (⌘ + B). Cette opération va créer un `AZSClient.framework` le fichier sur votre bureau.

Vous pouvez ensuite importer le fichier framework dans votre application en procédant comme suit :

1. Créez un nouveau projet ou ouvrez votre projet existant dans Xcode.

2. Cliquez sur votre projet dans le cadre de gauche et cliquez sur l’onglet *Général* en haut de l’Éditeur du projet.

3. Dans la section *bibliothèques et les infrastructures liées* , cliquez sur le bouton Ajouter (+).

4. Cliquez sur *ajouter d’autres...*. Accédez à et ajouter la `AZSClient.framework` fichier que vous venez de créer.

5. Dans la section *bibliothèques et les infrastructures liées* , cliquez de nouveau sur le bouton Ajouter (+).

6. Dans la liste des bibliothèques déjà fournies, recherchez `libxml2.2.dylib` et l’ajouter à votre projet.

7. Cliquez sur l’onglet *Paramètres de génération* du haut de l’Éditeur du projet.

8. Sous la section *Chemins d’accès de la recherche* , double-cliquez sur *Les chemins de recherche de Framework* et ajouter le chemin d’accès à votre `AZSClient.framework` fichier.

## <a name="import-statement"></a>Instruction d’importation
Vous devez inclure l’instruction import suivante dans le fichier où vous souhaitez appeler l’API de stockage Azure.

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Opérations asynchrones
> [AZURE.NOTE] Toutes les méthodes qui exécutent une requête auprès du service sont des opérations asynchrones. Dans les exemples de code, vous constaterez que ces méthodes ont un gestionnaire d’achèvement. Code dans le Gestionnaire de fin s’exécute **une fois** que la demande terminée. Code une fois que le Gestionnaire d’achèvement s’exécuteront **pendant que** la demande est effectuée.

## <a name="create-a-container"></a>Créer un conteneur
Chaque objet blob dans le stockage Azure doit résider dans un conteneur. L’exemple suivant montre comment créer un conteneur, appelé *newcontainer*, dans votre compte de stockage, si elle n’existe pas déjà. Lorsque vous choisissez un nom pour votre conteneur, être conscients des règles d’attribution de noms mentionnés ci-dessus.

    -(void)createContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

      // Create container in your Storage account if the container doesn't already exist
      [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
         if (error){
             NSLog(@"Error in creating container.");
         }
      }];
    }

Vous pouvez confirmer que cela fonctionne en examinant l' [Explorateur de stockage Microsoft Azure](http://storageexplorer.com) et en vérifiant que *newcontainer* se trouve dans la liste des conteneurs pour votre compte de stockage.

## <a name="set-container-permissions"></a>Définir les autorisations du conteneur
Les autorisations d’un conteneur sont configurées pour l’accès **privé** par défaut. Toutefois, les conteneurs fournissent diverses options pour l’accès de conteneur :

- **Privé**: conteneur et le blob de données peuvent être lues par le propriétaire du compte uniquement.

- **BLOB**: données Blob dans ce conteneur peuvent être lus via une demande anonyme, mais les données de conteneur ne seront pas disponibles. Les clients ne peut pas énumérer les objets BLOB dans le conteneur via une demande anonyme.

- **Conteneur**: conteneur et le blob de données peuvent être lus via une demande anonyme. Les clients peuvent énumérer des objets BLOB dans le conteneur via une demande anonyme, mais ne peut pas énumérer les conteneurs à l’intérieur du compte de stockage.

L’exemple suivant vous montre comment créer un conteneur avec les autorisations d’accès de **conteneur** qui autorisent l’accès public en lecture seule pour tous les utilisateurs sur Internet :

    -(void)createContainerWithPublicAccess{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur
Comme mentionné dans la section [concepts de service d’objet Blob](#blob-service-concepts) , stockage Blob propose trois types différents d’objets BLOB : bloquer les objets BLOB, ajouter des objets BLOB et page BLOB. Pour l’instant, la bibliothèque e/s de stockage Azure prend uniquement en charge les objets BLOB de bloc. Dans la plupart des cas, blob de bloc est le type recommandé à utiliser.

L’exemple suivant montre comment charger un objet blob de bloc à partir d’un NSString. Si un objet blob avec le même nom existe déjà dans ce conteneur, le contenu de cet objet blob est remplacé.

    -(void)uploadBlobToContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
             else{
                 // Create a local blob object
                 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                 // Upload blob to Storage
                 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                     if (error){
                         NSLog(@"Error in creating blob.");
                     }
                 }];
             }
         }];
    }

Vous pouvez confirmer que cela fonctionne en examinant l' [Explorateur de stockage Microsoft Azure](http://storageexplorer.com) et en vérifiant que le conteneur, *containerpublic*, contienne le blob, *sampleblob*. Dans cet exemple, nous avons utilisé un conteneur public afin de pouvoir vérifier également que cela a fonctionné en accédant à l’URI du BLOB :

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

En plus du téléchargement d’un blob de bloc à partir d’un NSString, il existe des méthodes similaires pour NSData, NSInputStream ou un fichier local.

## <a name="list-the-blobs-in-a-container"></a>Liste les objets BLOB dans un conteneur
L’exemple suivant montre comment répertorier tous les BLOB dans un conteneur. Lorsque vous effectuez cette opération, être conscients des paramètres suivants :     

- **continuationToken** - le jeton de continuation représente où l’opération de la liste doit commencer. Si aucun jeton n’est fourni, il répertorie les objets BLOB à partir du début. Un nombre quelconque d’objets BLOB peut être répertorié, à partir de zéro jusqu'à un maximum spécifié. Même si cette méthode retourne zéro résultats, si `results.continuationToken` n’est pas nulle, il peut y avoir plusieurs objets BLOB sur le service qui n’ont pas été répertoriés.
- **préfixe** - vous pouvez spécifier le préfixe à utiliser pour la liste de blob. Uniquement les objets BLOB qui commencent par ce préfixe est répertoriés.
- **useFlatBlobListing** - comme mentionné dans la section [Naming et référencement des conteneurs et des objets BLOB](#naming-and-referencing-containers-and-blobs) , bien que le service d’objet Blob est un jeu à deux dimensions de stockage, vous pouvez créer une hiérarchie virtuelle en nommant les objets BLOB avec les informations de chemin d’accès. Toutefois, la liste non plate est actuellement pas pris en charge ; Cela sera bientôt disponible. Pour l’instant, cette valeur doit être`YES`
- **blobListingDetails** - vous pouvez spécifier les éléments à inclure lors de l’affichage des objets BLOB
    - `AZSBlobListingDetailsNone`: Répertorie uniquement les objets BLOB validée et ne retournent pas de métadonnées de l’objet blob.
    - `AZSBlobListingDetailsSnapshots`: Liste engage les BLOB et les instantanés de blob.
    - `AZSBlobListingDetailsMetadata`: Métadonnées d’objet blob récupérer pour chaque objet blob retourné dans la liste.
    - `AZSBlobListingDetailsUncommittedBlobs`: Liste des blobs validées et non validées.
    - `AZSBlobListingDetailsCopy`: Inclure les propriétés dans la liste.
    - `AZSBlobListingDetailsAll`: Liste tous les blobs de validation disponibles, BLOB non validées et des captures instantanées et renvoyer tous les statuts des métadonnées et de copie pour les objets BLOB.
- **maxResults** - le nombre maximal de résultats à retourner pour cette opération. Ne pas définir une limite, utilisez -1.
- **completionHandler** - le bloc de code à exécuter avec les résultats de l’opération de la liste.

Dans cet exemple, une méthode d’assistance est utilisée pour appeler de manière récursive la liste objets BLOB méthode chaque fois qu’un jeton de continuation est retourné.

    -(void)listBlobsInContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        //List all blobs in container
        [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
            if (error != nil){
                NSLog(@"Error in creating container.");
            }
        }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
        [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
            if (error)
            {
                completionHandler(error);
            }
            else
            {
                for (int i = 0; i < results.blobs.count; i++) {
                    NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
                }
                if (results.continuationToken)
                {
                    [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
                }
                else
                {
                    completionHandler(nil);
                }
            }
        }];
    }


## <a name="download-a-blob"></a>Télécharger un blob

L’exemple suivant montre comment télécharger un blob à un objet NSString.

    -(void)downloadBlobToString{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
            else{
                NSLog(@"%@",text);
            }
        }];
    }

## <a name="delete-a-blob"></a>Supprimer un objet blob

L’exemple suivant montre comment supprimer un objet blob.

    -(void)deleteBlob{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
            if (error) {
                NSLog(@"Error in deleting blob.");
            }
        }];
    }

## <a name="delete-a-blob-container"></a>Supprimer un conteneur de blob

L’exemple suivant montre comment supprimer un conteneur.

    -(void)deleteContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      // Delete container
      [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
         if(error){
             NSLog(@"Error in deleting container");
         }
      }];
    }

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser le stockage Blob d’iOS, cliquez sur ces liens pour en savoir plus sur le service de stockage et de la bibliothèque d’e/s.

- [Bibliothèque de Client stockage Azure pour iOS](https://github.com/azure/azure-storage-ios)
- [IOS de stockage Azure Documentation de référence](http://azure.github.io/azure-storage-ios/)
- [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage)

Si vous avez des questions concernant cette bibliothèque n’hésitez pas à publier sur notre [forum de MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) ou un [Débordement de pile](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Si vous avez des suggestions de fonctionnalités de stockage Azure, veuillez l’envoyer à une [Évaluation du stockage Azure](https://feedback.azure.com/forums/217298-storage/).
