<properties
    pageTitle="À l’aide de l’interface CLI Azure avec stockage Azure | Microsoft Azure"
    description="Apprenez à utiliser l’Interface de ligne de commande (CLI Azure) Azure avec le stockage Azure pour créer et gérer des comptes de stockage et travailler avec des fichiers et BLOB Azure. La CLI Azure est un outil multiplate-forme "
    services="storage"
    documentationCenter="na"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="using-the-azure-cli-with-azure-storage"></a>À l’aide de l’interface CLI Azure avec stockage Azure

## <a name="overview"></a>Vue d’ensemble

La CLI Azure fournit un ensemble d’open source, multiplates-formes des commandes pour travailler avec la plateforme Azure. Il offre les mêmes fonctionnalités de la [Azure portal](https://portal.azure.com) , ainsi que les fonctionnalités d’accès aux données riches.

Dans ce guide, nous allons explorer l’utilisation de [Azure Interface de ligne de commande (CLI Azure)](../xplat-cli-install.md) pour effectuer de nombreuses tâches de développement et d’administration avec le stockage Azure. Nous vous recommandons de télécharger et installer ou mettre à niveau vers la dernière CLI Azure avant d’utiliser ce guide.

Ce guide suppose que vous comprenez les concepts de base du stockage Azure. Le guide fournit un certain nombre de scripts pour illustrer l’utilisation de l’infrastructure du langage commun Azure avec stockage Azure. Veillez à mettre à jour les variables de script en fonction de votre configuration avant d’exécuter chaque script.

> [AZURE.NOTE] Le guide fournit les exemples de commande et de script CLI d’Azure pour les comptes de stockage classique. Reportez-vous [à l’aide de la CLI Azure pour Mac, Linux et Windows Azure Gestion des ressources](../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) de l’infrastructure du langage commun Azure des commandes pour les comptes de stockage de gestionnaire de ressources.

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Mise en route avec le stockage Azure et d’Azure CLI en 5 minutes

Ce guide utilise Ubuntu pour obtenir des exemples, mais les autres plates-formes de système d’exploitation doivent effectuer de la même façon.

**Pour Azure :** Obtenir un abonnement Microsoft Azure et un compte Microsoft associé à cet abonnement. Pour plus d’informations sur les options d’achat d’Azure, consultez [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/), [Options d’achat](https://azure.microsoft.com/pricing/purchase-options/)et [Offre des membres](https://azure.microsoft.com/pricing/member-offers/) (pour les membres de MSDN, Microsoft Partner Network, BizSpark et des autres programmes de Microsoft).

Pour plus d’informations sur les abonnements d’Azure, consultez [affectation les rôles d’administrateur dans Azure Active Directory (AD Azure)](https://msdn.microsoft.com/library/azure/hh531793.aspx) .

**Après avoir créé un compte et un abonnement Microsoft Azure :**

1. Téléchargez et installez le CLI Azure suivant les instructions fournies dans [installer l’interface CLI d’Azure](../xplat-cli-install.md).
2. Une fois que l’infrastructure du langage commun Azure a été installé, vous serez en mesure d’utiliser la commande azure à partir de l’interface de ligne de commande (Bash, Terminal, invite de commandes) pour accéder aux commandes CLI d’Azure. Type de `azure` commande et que vous devez voir la sortie suivante.

    ![Sortie de la commande Azure][Image1]

3. Dans l’interface de ligne de commande, tapez `azure storage` à la liste de toutes les commandes de stockage azure et obtenir une première impression des fonctionnalités CLI Azure fournit. Vous pouvez taper le nom de la commande avec le paramètre **-h** (par exemple, `azure storage share create -h`) pour afficher les détails de la syntaxe de la commande.
4. Désormais, vous y trouverez un script simple qui affiche les commandes CLI d’Azure de base pour accéder au stockage d’Azure. Le script vous demande tout d’abord de définir deux variables pour votre compte de stockage et de la clé. Ensuite, le script crée un nouveau conteneur dans ce nouveau compte de stockage et télécharger un fichier image existant (blob) à ce conteneur. Une fois le script répertorie tous les objets BLOB dans ce conteneur, il télécharge le fichier image dans le répertoire de destination qui existe sur l’ordinateur local.

        #!/bin/bash
        # A simple Azure storage example

        export AZURE_STORAGE_ACCOUNT=<storage_account_name>
        export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

        export container_name=<container_name>
        export blob_name=<blob_name>
        export image_to_upload=<image_to_upload>
        export destination_folder=<destination_folder>

        echo "Creating the container..."
        azure storage container create $container_name

        echo "Uploading the image..."
        azure storage blob upload $image_to_upload $container_name $blob_name

        echo "Listing the blobs..."
        azure storage blob list $container_name

        echo "Downloading the image..."
        azure storage blob download $container_name $blob_name $destination_folder

        echo "Done"

5. Sur votre ordinateur local, ouvrez votre éditeur de texte préféré (vim par exemple). Dans votre éditeur de texte, tapez le script ci-dessus.

6. Maintenant, vous devez mettre à jour les variables de script en fonction de vos paramètres de configuration.

    - **< storage_account_name >** Utiliser le nom spécifié dans le script, ou entrez un nouveau nom pour votre compte de stockage. **Important :** Le nom du compte de stockage doit être unique dans Azure. Il doit être en minuscules, trop !

    - **< storage_account_key >** La touche d’accès rapide de votre compte de stockage.

    - **< container_name >** Utiliser le nom spécifié dans le script ou entrez un nouveau nom pour votre conteneur.

    - **< image_to_upload >** Entrez un chemin d’accès à une image sur votre ordinateur local, tel que : « ~ / images/HelloWorld.png ».

    - **< destination_folder >** Entrez un chemin d’accès vers un répertoire local pour stocker les fichiers téléchargés depuis le stockage Azure, par exemple : « ~/downloadImages ».

7. Une fois que vous avez mis à jour les variables nécessaires dans vim, appuyez sur les combinaisons de touches « ÉCHAP, :, wq ! » Pour enregistrer le script.

8. Pour exécuter ce script, tapez simplement le nom de fichier du script dans la console bash. Après l’exécution de ce script, vous devez disposer d’un dossier de destination local qui inclut le fichier image téléchargé. La capture d’écran suivante montre un exemple de résultat :

Une fois que le script s’exécute, vous devez disposer d’un dossier de destination local qui inclut le fichier image téléchargé.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Gérer les comptes de stockage avec la CLI d’Azure

### <a name="connect-to-your-azure-subscription"></a>Se connecter à votre abonnement Azure

Bien que la plupart des commandes de stockage fonctionnent sans un abonnement Azure, nous vous recommandons de vous connecter à votre abonnement à partir de la CLI d’Azure. Pour configurer l’interface CLI d’Azure pour travailler avec votre abonnement, suivez les étapes de [connexion à un abonnement Azure à partir de la CLI d’Azure](../xplat-cli-connect.md).

### <a name="create-a-new-storage-account"></a>Créer un nouveau compte de stockage

Pour utiliser le stockage Azure, vous aurez besoin d’un compte de stockage. Vous pouvez créer un nouveau compte de stockage Azure après avoir configuré votre ordinateur pour vous connecter à votre abonnement.

        azure storage account create <account_name>

Le nom de votre compte de stockage doit être comprise entre 3 et 24 caractères et utiliser des chiffres et des lettres minuscules uniquement.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Définir un compte de stockage Azure par défaut dans les variables d’environnement

Vous pouvez avoir plusieurs comptes de stockage de votre abonnement. Vous pouvez choisir un d'entre eux et définissez-le dans les variables d’environnement pour toutes les commandes de stockage dans la même session. Cela vous permet d’exécuter les commandes de stockage Azure CLI sans spécifier le compte de stockage et de clé explicitement.

        export AZURE_STORAGE_ACCOUNT=<account_name>
        export AZURE_STORAGE_ACCESS_KEY=<key>

Un autre moyen de définir un compte de stockage par défaut à l’aide de la chaîne de connexion. Tout d’abord obtenir la chaîne de connexion par commande :

        azure storage account connectionstring show <account_name>

Copier la chaîne de connexion de sortie, puis définissez-la sur la variable d’environnement :

        export AZURE_STORAGE_CONNECTION_STRING=<connection_string>

## <a name="create-and-manage-blobs"></a>Créer et gérer des objets BLOB

Stockage de Blob Azure est un service permettant de stocker de grandes quantités de données non structurées, telles que du texte ou des données binaires, qui sont accessibles à partir de n’importe où dans le monde via HTTP ou HTTPS. Cette section suppose que vous êtes déjà familiarisé avec les concepts de stockage Azure Blob. Pour plus d’informations, consultez [mise en route de stockage Azure Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md) et de [Concepts de Service d’objet Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Créer un conteneur

Chaque objet blob dans le stockage Azure doit être dans un conteneur. Vous pouvez créer un conteneur privé à l’aide de la `azure storage container create` commande :

        azure storage container create mycontainer

> [AZURE.NOTE] Il existe trois niveaux d’accès anonyme en lecture : **Off**, **Blob**et un **conteneur**. Pour empêcher l’accès anonyme aux objets BLOB, la valeur du paramètre d’autorisation **Off**. Par défaut, le nouveau conteneur est privé et est accessible uniquement par le propriétaire du compte. Pour autoriser l’accès anonyme en lecture publique à un blob de ressources, mais pas pour les métadonnées de conteneur ou la liste des objets BLOB dans le conteneur, la valeur du paramètre d’autorisation **Blob**. Pour autoriser les accès en lecture publique complète pour le blob de ressources, les métadonnées de conteneur et la liste des objets BLOB dans le conteneur, définissez le paramètre d’autorisation au **conteneur**. Pour plus d’informations, voir [Gérer les accès en lecture anonymes pour les conteneurs et les objets BLOB](storage-manage-access-to-resources.md).

### <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Stockage de Blob Azure prend en charge les objets BLOB de page et les objets BLOB de bloc. Pour plus d’informations, consultez [Blobs de bloc de compréhension, ajouter un BLOB et BLOB de la Page](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Pour charger des objets BLOB sur un conteneur, vous pouvez utiliser la `azure storage blob upload`. Par défaut, cette commande transfère les fichiers locaux dans un blob de bloc. Pour spécifier le type de l’objet blob, vous pouvez utiliser la `--blobtype` paramètre.

        azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob

### <a name="download-blobs-from-a-container"></a>Télécharger des objets BLOB à partir d’un conteneur

L’exemple suivant montre comment télécharger des objets BLOB à partir d’un conteneur.

        azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'

### <a name="copy-blobs"></a>Copier des objets BLOB

Vous pouvez copier des objets BLOB au sein ou entre les régions et les comptes de stockage en mode asynchrone.

L’exemple suivant montre comment copier des objets BLOB à partir d’un compte de stockage à un autre. Dans cet exemple, nous créons un conteneur dans lequel les objets BLOB est publiquement accessibles de manière anonyme.

    azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

    azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

    azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer

Cet exemple effectue une copie asynchrone. Vous pouvez surveiller l’état de chaque opération de copie en exécutant le `azure storage blob copy show` opération.

Notez que l’URL de la source fournie pour l’opération de copie doit être accessible au public, ou inclure un jeton SAS (signature de l’accès partagé).

### <a name="delete-a-blob"></a>Supprimer un objet blob

Pour supprimer un objet blob, utilisez le dessous de commande :

        azure storage blob delete mycontainer myBlockBlob2

## <a name="create-and-manage-file-shares"></a>Créer et gérer des partages de fichiers

Stockage de fichier Azure offre un stockage partagé pour les applications utilisant le protocole SMB standard. Ordinateurs virtuels Microsoft Azure et les services en nuage, ainsi que les applications sur site, peuvent partager les données de fichier via les partages montés. Vous pouvez gérer des partages de fichiers et de données de fichier via la CLI d’Azure. Pour plus d’informations sur le stockage de fichiers d’Azure, consultez [mise en route de Windows Azure fichier stockage](storage-dotnet-how-to-use-files.md) ou [l’utilisation du stockage Azure avec Linux](storage-how-to-use-files-linux.md).

### <a name="create-a-file-share"></a>Créer un partage de fichiers

Un partage de fichiers d’Azure est un partage de fichiers SMB dans Azure. Tous les fichiers et les répertoires doivent être créés dans un partage de fichiers. Un compte peut contenir un nombre illimité de parts et un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage. L’exemple suivant crée un partage de fichiers nommé **myshare**.

        azure storage share create myshare

### <a name="create-a-directory"></a>Créer un répertoire

Un répertoire fournit une structure hiérarchique facultative pour un partage de fichier Azure. L’exemple suivant crée un répertoire nommé **MonRép** dans le partage de fichiers.

        azure storage directory create myshare myDir

Remarque Ce chemin d’accès du répertoire peut inclure plusieurs niveaux, *par exemple*, **un / b**. Toutefois, vous devez vous assurer que tous les répertoires parent existe. Par exemple, pour un chemin d’accès **une / b**, vous devez créer répertoire **un** tout d’abord, puis créez le répertoire **b**.

### <a name="upload-a-local-file-to-directory"></a>Télécharger un fichier local dans répertoire

L’exemple suivant télécharge un fichier à partir de **~/temp/samplefile.txt** vers le répertoire **MonRép** . Modifier le chemin d’accès de fichier afin qu’il pointe vers un fichier valide sur votre ordinateur local :

        azure storage file upload '~/temp/samplefile.txt' myshare myDir

Notez qu’un fichier dans le partage peut être jusqu'à 1 To de taille.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Liste des fichiers dans le partage racine ou le répertoire

Vous pouvez lister les fichiers et sous-répertoires d’une racine de partage ou un répertoire à l’aide de la commande suivante :

        azure storage file list myshare myDir

Notez que le nom du répertoire est facultatif pour l’opération de la liste. En cas d’omission, la commande répertorie le contenu du répertoire racine du partage.

### <a name="copy-files"></a>Copier les fichiers

Depuis la version 0.9.8 de l’infrastructure du langage commun Azure, vous pouvez copier un fichier vers un autre fichier, un fichier à un objet blob, ou un objet blob à un fichier. Ci-dessous, nous montrent comment effectuer ces opérations de copie à l’aide de commandes CLI. Pour copier un fichier vers le nouveau répertoire :

    azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

Pour copier un objet blob vers un répertoire de fichiers :

    azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

## <a name="next-steps"></a>Étapes suivantes

Voici certains des articles associés et des ressources pour en savoir plus sur le stockage Azure.

- [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Référence des API de stockage Azure reste](https://msdn.microsoft.com/library/azure/dd179355.aspx)


[Image1]: ./media/storage-azure-cli/azure_command.png
