<properties
    pageTitle="Déplacer les données vers et depuis le stockage Blob Azure à l’aide de AzCopy | Microsoft Azure"
    description="Déplacement des données vers et depuis le stockage Blob Azure à l’aide de AzCopy"
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

# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Déplacement des données vers et depuis le stockage Blob Azure à l’aide de AzCopy

AzCopy est un utilitaire de ligne de commande conçu pour le téléchargement, le téléchargement et la copie des données vers et à partir de blob Microsoft Azure, de fichiers et de stockage de la table.

Pour obtenir des instructions sur l’installation des AzCopy et des informations supplémentaires sur son utilisation avec la plateforme Azure, consultez [Mise en route avec l’utilitaire de ligne de commande de AzCopy](../storage/storage-use-azcopy.md).

Conseils sur les technologies utilisées pour déplacer des données sur et/ou à partir de stockage des objets Blob Azure sont liés ici :

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Si vous utilisez un ordinateur virtuel qui a été configuré avec les scripts fournis par [des machines virtuelles de Science données dans Azure](machine-learning-data-science-virtual-machines.md), AzCopy est déjà installé sur l’ordinateur virtuel.

> [AZURE.NOTE] Pour une introduction complète à stockage blob Azure, consultez [Notions fondamentales de Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et [Service d’objet Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## <a name="prerequisites"></a>Conditions préalables

Ce document suppose que vous disposez d’un abonnement Azure, un compte de stockage et la clé de stockage correspondant pour ce compte. Avant le téléchargement/chargement des données, vous devez connaître votre clé de compte et de nom de compte stockage Azure.

- Pour configurer un abonnement Azure, consultez [un mois gratuite](https://azure.microsoft.com/pricing/free-trial/).

- Pour obtenir des instructions sur la création d’un compte de stockage et d’obtenir des informations de compte et clées, voir [comptes de stockage sur Azure](../storage/storage-create-storage-account.md).


## <a name="run-azcopy-commands"></a>Exécuter des commandes AzCopy

Pour exécuter les commandes AzCopy, ouvrez une fenêtre de commande et accédez au répertoire d’installation sur votre ordinateur, où se trouve l’exécutable de AzCopy.exe AzCopy. 

La syntaxe de base pour les commandes de AzCopy est :

    AzCopy /Source:<source> /Dest:<destination> [Options]

>[AZURE.NOTE] Vous pouvez ajouter l’emplacement d’installation de AzCopy à votre chemin d’accès système et puis exécutez les commandes à partir de n’importe quel répertoire. Par défaut, AzCopy est installé *% ProgramFiles(x86) %\Microsoft SDKs\Azure\AzCopy* ou *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.

## <a name="upload-files-to-an-azure-blob"></a>Télécharger des fichiers vers un blob Azure

Pour télécharger un fichier, utilisez la commande suivante :

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Télécharger des fichiers à partir d’un blob Azure

Pour télécharger un fichier à partir d’un blob Azure, utilisez la commande suivante :

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Transférer des objets BLOB entre conteneurs Azure

Pour transférer des objets BLOB entre conteneurs d’Azure, utilisez la commande suivante :

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Conseils d’utilisation de AzCopy

> [AZURE.TIP]   
> 1. Lorsque **téléchargement** de fichiers, */S* télécharge les fichiers de manière récursive. Sans ce paramètre, les fichiers dans les sous-répertoires ne sont pas téléchargés.  
> 2. Lorsque le fichier de **téléchargement** , */S* recherche le conteneur de manière récursive jusqu'à ce que tous les fichiers dans le répertoire spécifié et ses sous-répertoires, ou tous les fichiers qui correspondent au modèle spécifié dans le répertoire donné et ses sous-répertoires, sont téléchargées.  
> 3.  Vous ne pouvez pas spécifier un **fichier blob spécifique** à télécharger à l’aide du paramètre */Source* . Pour télécharger un fichier spécifique, spécifiez le nom du fichier à télécharger à l’aide du paramètre */Pattern* blob. Paramètre **/S** permet d’avoir AzCopy recherchez un modèle fichier nom de manière récursive. Sans le paramètre de modèle, AzCopy télécharge tous les fichiers dans ce répertoire.
