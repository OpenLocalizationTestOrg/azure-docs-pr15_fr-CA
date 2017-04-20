<properties
    pageTitle="Déplacer les données vers et depuis le stockage Blob Azure utilisant les Python | Microsoft Azure"
    description="Déplacement des données vers et depuis le stockage Blob Azure utilisant les Python"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Déplacement des données vers et depuis le stockage Blob Azure utilisant les Python

Cette rubrique décrit comment répertorier, télécharger et télécharger des objets BLOB à l’aide de l’API de Python. Avec l’API de Python fournis dans Azure SDK, vous pouvez :

- Créer un conteneur
- Télécharger un blob dans un conteneur
- Télécharger des objets BLOB
- Liste les objets BLOB dans un conteneur
- Supprimer un objet blob

Pour plus d’informations sur l’utilisation de l’API de Python, voir [comment utiliser le Service de stockage de Blob à partir de Python](../storage/storage-python-how-to-use-blob-storage.md).

Conseils sur les technologies utilisées pour déplacer des données sur et/ou à partir de stockage des objets Blob Azure sont liés ici :

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Si vous utilisez un ordinateur virtuel qui a été configuré avec les scripts fournis par [des machines virtuelles de Science données dans Azure](machine-learning-data-science-virtual-machines.md), AzCopy est déjà installé sur l’ordinateur virtuel.

> [AZURE.NOTE] Pour une introduction complète à stockage blob Azure, consultez [Notions fondamentales de Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et [Service d’objet Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## <a name="prerequisites"></a>Conditions préalables

Ce document suppose que vous disposez d’un abonnement Azure, un compte de stockage et la clé de stockage correspondant pour ce compte. Avant le téléchargement/chargement des données, vous devez connaître votre clé de compte et de nom de compte stockage Azure.

- Pour configurer un abonnement Azure, consultez [un mois gratuite](https://azure.microsoft.com/pricing/free-trial/).

- Pour obtenir des instructions sur la création d’un compte de stockage et d’obtenir des informations de compte et clées, voir [comptes de stockage sur Azure](../storage/storage-create-storage-account.md).


## <a name="upload-data-to-blob"></a>Télécharger les données d’objet Blob

Ajouter l’extrait de code suivant en haut de n’importe quel code Python dans laquelle vous souhaitez accéder par programme à stockage Azure :

    from azure.storage.blob import BlobService

L’objet **BlobService** vous permet de travailler avec les conteneurs et les objets BLOB. Le code suivant crée un objet BlobService à l’aide de la clé de nom de compte et le compte de stockage. Remplacez le nom de compte et clé de compte avec votre compte réel et la clé.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Pour charger des données dans un blob, appliquez les méthodes suivantes :

1. Put\_bloc\_blob\_de\_chemin d’accès (transfère le contenu d’un fichier à partir du chemin spécifié)
2. Put\_block_blob\_de\_fichier (télécharge le contenu à partir d’un fichier déjà ouvert/flux)
3. Put\_bloc\_blob\_de\_octets (téléchargements un tableau d’octets)
4. Put\_bloc\_blob\_de\_texte (transfère la valeur de texte spécifié à l’aide de l’encodage spécifié)

L’exemple de code suivant télécharge un fichier local à un conteneur :

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

L’exemple de code suivant télécharge tous les fichiers (à l’exclusion de répertoires) dans un répertoire local pour le stockage des objets blob :

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>Télécharger des données à partir de Blob

Pour télécharger des données à partir d’un objet blob, appliquez les méthodes suivantes :
1. obtenir\_blob\_à\_chemin d’accès
2. obtenir\_blob\_à\_fichier
3. obtenir\_blob\_à\_octets
4. obtenir\_blob\_à\_texte

Ces méthodes qui effectuent la segmentation nécessaire lorsque la taille des données dépasse 64 Mo.

L’exemple de code suivant télécharge le contenu d’un objet blob dans un conteneur à un fichier local :

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

L’exemple de code suivant télécharge tous les BLOB à partir d’un conteneur. Il utilise la liste\_objets BLOB pour obtenir la liste des objets BLOB disponibles dans le conteneur et les télécharge dans un répertoire local.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
