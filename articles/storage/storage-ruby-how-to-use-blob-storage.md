<properties
    pageTitle="Comment faire pour utiliser le stockage Blob (stockage de l’objet) à partir de Ruby | Microsoft Azure"
    description="Stocker des données non structurées dans le nuage avec les Blob Azure le stockage (objet)."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-blob-storage-from-ruby"></a>Comment faire pour utiliser le stockage Blob de Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Stockage de Blob Azure est un service qui stocke les données non structurées dans le nuage en tant qu’objets/objets BLOB. Stockage des objets BLOB peut stocker n’importe quel type de données texte ou binaires, comme un document, un fichier multimédia ou un programme d’installation de l’application. Stockage des objets BLOB est également appelée stockage d’objets.

Ce guide vous explique comment exécuter des scénarios courants utilisant le stockage Blob. Ces exemples sont écrits à l’aide de l’API Ruby. Les scénarios présentés incluent des blobs de **téléchargement, liste, téléchargement** et la **suppression** .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Créer une application Ruby

Créez une application Ruby. Pour obtenir des instructions, reportez-vous à la section [Ruby on Rails Web des applications sur un ordinateur virtuel d’Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)

## <a name="configure-your-application-to-access-storage"></a>Configurez votre application pour accéder au stockage

Pour utiliser le stockage Azure, vous devez télécharger et utiliser le package azure Ruby, qui inclut un ensemble de bibliothèques de commodité qui communiquent avec les services de reste de stockage.

### <a name="use-rubygems-to-obtain-the-package"></a>Utiliser RubyGems pour obtenir le package

1. Utilisez une interface de ligne de commande **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix).

2. Tapez « azure d’installation gem » dans la fenêtre de commande pour installer la marque et les dépendances.

### <a name="import-the-package"></a>Importer le package

À l’aide de votre éditeur de texte, ajoutez le code suivant en haut du fichier Ruby où vous avez l’intention d’utiliser le stockage :

    require "azure"

## <a name="setup-an-azure-storage-connection"></a>Programme d’installation une connexion de stockage Azure

Le module azure lira les variables d’environnement **AZURE\_stockage\_compte** et **AZURE\_stockage\_ACCESS_KEY** pour les informations requises pour vous connecter à votre compte de stockage Azure. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations de compte avant d’utiliser **Azure::Blob::BlobService** avec le code suivant :

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"


Pour obtenir ces valeurs à partir d’un classique ou compte de gestionnaire de ressources de stockage dans le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Atteindre le compte de stockage que vous souhaitez utiliser.
3. De la lame de paramètres sur la droite, cliquez sur **Les touches d’accès**.
4. De la lame de clés d’accès qui s’affiche, vous verrez la touche d’accès rapide 1 et la touche d’accès rapide 2. Vous pouvez utiliser une ou l’autre de ces. 
5. Cliquez sur l’icône Copier pour copier la clé dans le Presse-papiers. 

Pour obtenir ces valeurs à partir d’un compte de stockage classique dans le portail Azure classique :

1. Connectez-vous au [portail d’Azure classique](https://manage.windowsazure.com).
2. Atteindre le compte de stockage que vous souhaitez utiliser.
3. Cliquez sur **Gérer les touches d’accès rapide** au bas du volet de navigation.
4. Dans la boîte de dialogue contextuelle, vous verrez le nom de compte de stockage, la clé d’accès principal et la clé d’accès secondaire. Pour la touche d’accès rapide, vous pouvez utiliser soit principal, soit le secondaire. 
5. Cliquez sur l’icône Copier pour copier la clé dans le Presse-papiers.

## <a name="create-a-container"></a>Créer un conteneur

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

L’objet **Azure::Blob::BlobService** vous permet de travailler avec les conteneurs et les objets BLOB. Pour créer un conteneur, utilisez la **créer\_container()** méthode.

L’exemple de code suivant crée un conteneur ou une impression de l’erreur, s’il en existe une.

    azure_blob_service = Azure::Blob::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

Si vous souhaitez que les fichiers soient dans le conteneur public, vous pouvez définir les autorisations du conteneur.

Vous pouvez uniquement modifier le <strong>créer\_container()</strong> appel à passer le **: public\_accès\_niveau** option :

    container = azure_blob_service.create_container("test-container",
      :public_access_level => "<public access level>")


Les valeurs valides pour le **: public\_accès\_niveau** option sont :

* **blob :** Spécifie l’accès en lecture publique complète pour conteneur et objet blob de données. Les clients peuvent énumérer des objets BLOB dans le conteneur via une demande anonyme, mais ne peut pas énumérer les conteneurs à l’intérieur du compte de stockage.

* **conteneur :** Spécifie l’accès en lecture publique pour les objets BLOB. Les données BLOB dans ce conteneur peuvent être lus via une demande anonyme, mais les données de conteneur ne seront pas disponibles. Les clients ne peut pas énumérer les objets BLOB dans le conteneur via une demande anonyme.

Sinon, vous pouvez modifier le niveau d’accès public d’un conteneur à l’aide de **la valeur\_conteneur\_acl()** méthode pour spécifier le niveau d’accès public.

L’exemple de code suivant modifie le niveau d’accès public au **conteneur**:

    azure_blob_service.set_container_acl('test-container', "container")

## <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Pour télécharger un contenu dans un blob, vous devez utiliser le **créer\_bloc\_blob()** pour créer le blob, utilisez un fichier ou une chaîne en tant que le contenu de l’objet blob.

Le code suivant charge le fichier **test.png** sous la forme d’un blob de nouveau appelé « image-blob » dans le conteneur.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <a name="list-the-blobs-in-a-container"></a>Liste les objets BLOB dans un conteneur

Pour répertorier les conteneurs, utiliser la méthode de **list_containers()** .
Pour répertorier les objets BLOB dans un conteneur, utilisez **liste\_blobs()** méthode.

Cela génère les URL de tous les objets BLOB dans tous les conteneurs pour le compte.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <a name="download-blobs"></a>Télécharger des objets BLOB

Pour télécharger les objets BLOB, utilisez le **obtenir\_blob()** méthode pour récupérer le contenu.

L’exemple de code suivant illustre l’utilisation de **obtenir\_blob()** pour télécharger le contenu du blob « image » et l’écrire dans un fichier local.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <a name="delete-a-blob"></a>Supprimer un objet Blob
Enfin, pour supprimer un objet blob, utilisez le **Supprimer\_blob()** méthode. L’exemple de code suivant montre comment supprimer un objet blob.

    azure_blob_service.delete_blob(container.name, "image-blob")

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations sur les tâches de stockage plus complexes, cliquez sur ces liens :

- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Référentiel [d’Azure SDK pour Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub
- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
