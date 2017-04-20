<properties 
    pageTitle="Déplacer les données vers et depuis le stockage Blob Azure avec Explorateur de stockage Azure | Microsoft Azure" 
    description="Déplacement des données vers et depuis le stockage Blob Azure avec Explorateur de stockage Azure" 
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
    ms.date="08/31/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Déplacement des données vers et depuis le stockage Blob Azure avec Explorateur de stockage Azure

Explorateur de stockage Azure est un outil gratuit de Microsoft qui vous permet de travailler avec des données de stockage Azure sur Windows, macOS et Linux. Cette rubrique décrit comment l’utiliser pour transférer et télécharger des données depuis le stockage blob Azure. L’outil peut être téléchargé à partir de [l’Explorateur de stockage Microsoft Azure](http://storageexplorer.com/).

Conseils sur les technologies utilisées pour déplacer des données sur et/ou à partir de stockage des objets Blob Azure sont liés ici :
 
[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]   

 
> [AZURE.NOTE] Si vous utilisez un ordinateur virtuel qui a été configuré avec les scripts fournis par [des machines virtuelles de Science données dans Azure](machine-learning-data-science-virtual-machines.md), Explorateur de stockage Azure est déjà installé sur l’ordinateur virtuel.
 
> [AZURE.NOTE] Pour obtenir une présentation complète de stockage blob Azure, consultez [Notions fondamentales de Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et [Service d’objet Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).   

## <a name="prerequisites"></a>Conditions préalables

Ce document suppose que vous disposez d’un abonnement Azure, un compte de stockage et la clé de stockage correspondant pour ce compte. Avant le téléchargement/chargement des données, vous devez connaître votre clé de compte et de nom de compte stockage Azure. 

- Pour configurer un abonnement Azure, consultez [un mois gratuite](https://azure.microsoft.com/pricing/free-trial/).
- Pour obtenir des instructions sur la création d’un compte de stockage et d’obtenir des informations de compte et clées, voir [comptes de stockage sur Azure](../storage/storage-create-storage-account.md). Prenez note de la touche d’accès rapide de votre compte de stockage que vous avez besoin de cette clé pour se connecter sur le compte à l’aide de l’outil Explorateur de stockage Azure.
- L’outil Azure Storage Explorer peut être téléchargé à partir de [l’Explorateur de stockage Microsoft Azure](http://storageexplorer.com/). Acceptez les valeurs par défaut lors de l’installation.


<a id="explorer"></a>
## <a name="use-azure-storage-explorer"></a>Utilisez l’Explorateur de stockage Azure 

Les étapes suivantes du document comment télécharger des données à l’aide d’Explorateur de stockage Azure. 

1.  Lancer l’Explorateur de stockage Microsoft Azure.
2.  Pour afficher l’Assistant **se connecter à votre compte...** , sélectionnez l’icône **paramètres de compte Azure** , puis **Ajouter un compte** et vous entrez des informations d’identification. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3.  Pour afficher l’Assistant **se connecter au stockage Azure** , sélectionnez l’icône **se connecter au stockage Azure** . ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Entrez la clé d’accès de votre compte de stockage Azure sur l’Assistant de **connexion pour le stockage Azure** puis cliquez sur **suivant**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Entrez le nom du compte de stockage dans la zone **nom du compte** et puis cliquez sur **suivant**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Ajouté le compte de stockage doit maintenant être affiché. Pour créer un conteneur d’objet blob dans un compte de stockage, droit sur le nœud de **Conteneurs Blob** dans ce compte, sélectionnez **Créer un conteneur Blob**et entrez un nom.
7. Pour télécharger les données dans un conteneur, sélectionnez le conteneur cible, puis cliquez sur le bouton **Télécharger** .![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Cliquez sur le **...** à droite de la zone de **fichiers** , sélectionnez un ou plusieurs fichiers à télécharger à partir du système de fichier et cliquez sur **Télécharger** pour commencer à télécharger les fichiers.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
7. Pour télécharger les données, en sélectionnant l’objet blob dans le conteneur correspondant à télécharger et cliquez sur **Télécharger**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)


